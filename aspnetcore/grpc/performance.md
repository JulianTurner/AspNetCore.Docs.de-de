---
title: Bewährte Methoden für die Leistung in gRPC für ASP.NET Core
author: jamesnk
description: In diesem Artikel werden bewährte Methoden zum Erstellen leistungsstarker gRPC-Dienste vorgestellt.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/23/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: grpc/performance
ms.openlocfilehash: f9cefa89ec6e533920b33223b34333f6ebe38428
ms.sourcegitcommit: 4df148cbbfae9ec8d377283ee71394944a284051
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/26/2020
ms.locfileid: "88876723"
---
# <a name="performance-best-practices-in-grpc-for-aspnet-core"></a>Bewährte Methoden für die Leistung in gRPC für ASP.NET Core

Von [James Newton-King](https://twitter.com/jamesnk)

gRPC ist für leistungsstarke Dienste konzipiert. In dieser Dokumentation wird erläutert, wie Sie die bestmögliche Leistung von gRPC erzielen.

## <a name="reuse-channel"></a>Wiederverwenden von Kanälen

Ein gRPC-Kanal sollte für gRPC-Aufrufe wiederverwendet werden. Durch die Wiederverwendung eines Kanals können Aufrufe für Multiplexing über eine vorhandene HTTP/2-Verbindung verwendet werden.

Wenn ein neuer Kanal für jeden gRPC-Aufruf erstellt wird, kann dies deutlich mehr Zeit beanspruchen. Jeder Aufruf erfordert mehrere Netzwerkroundtrips zwischen dem Client und dem Server, um eine HTTP/2-Verbindung herzustellen:

1. Öffnen eines Sockets
2. Herstellen der TCP-Verbindung
3. TLS-Aushandlung
4. Starten der HTTP/2-Verbindung
5. Ausführen des gRPC-Aufrufs

Kanäle können problemlos für mehrere gRPC-Aufrufe gemeinsam genutzt und wiederverwendet werden:

* gRPC-Clients werden mit Kanälen erstellt. Bei gRPC-Clients handelt es sich um Lightweightobjekte. Sie müssen nicht zwischengespeichert oder wiederverwendet werden.
* Aus einem Kanal können mehrere gRPC-Clients erstellt werden, einschließlich verschiedener Clienttypen.
* Ein Kanal und Clients, die aus dem Kanal erstellt wurden, können sicher von mehreren Threads verwendet werden.
* Clients, die aus dem Kanal erstellt wurden, können mehrere gleichzeitige Aufrufe durchführen.

## <a name="connection-concurrency"></a>Verbindungsparallelität

HTTP/2-Verbindungen weisen in der Regel eine Einschränkung für die [maximale Anzahl gleichzeitiger Streams (aktive HTTP-Anforderungen)](https://http2.github.io/http2-spec/#rfc.section.5.1.2) für eine Verbindung auf. Für die meisten Server liegt eine Standardbeschränkung auf 100 gleichzeitige Streams vor.

Ein gRPC-Kanal verwendet eine einzige HTTP/2-Verbindung, und parallele Aufrufe werden per Multiplexing für diese Verbindung ausgeführt. Wenn die Anzahl aktiver Aufrufe die Grenze für den Verbindungsstream erreicht, werden zusätzliche Aufrufe im Client in eine Warteschlange eingereiht. Aufrufe in der Warteschlange warten auf den Abschluss aktiver Aufrufe, bevor sie übermittelt werden. Bei Anwendungen mit hoher Auslastung oder zeitintensiven gRPC-Streamingaufrufen können Leistungsprobleme auftreten, die von Aufrufen ausgelöst werden, die aufgrund dieser Einschränkungen in die Warteschlange eingereiht werden.

::: moniker range=">= aspnetcore-5.0"

Mit .NET 5 wurde die `SocketsHttpHandler.EnableMultipleHttp2Connections`-Eigenschaft eingeführt. Wenn `true` festgelegt wird, werden zusätzliche HTTP/2-Verbindungen von einem Kanal erstellt, wenn die maximale Anzahl gleichzeitiger Streams erreicht wird. Wenn ein `GrpcChannel` erstellt wird, wird der interne `SocketsHttpHandler` automatisch konfiguriert, um zusätzliche HTTP/2-Verbindungen herzustellen. Wenn eine App seinen eigenen Handler konfiguriert, sollten Sie `EnableMultipleHttp2Connections` auf `true` festlegen:

```csharp
var channel = GrpcChannel.ForAddress("https://localhost", new GrpcChannelOptions
{
    HttpHandler = new SocketsHttpHandler
    {
        EnableMultipleHttp2Connections = true,

        // ...configure other handler settings
    }
});
```

::: moniker-end

Es gibt einige Problemumgehungen für .NET Core 3.1-Apps:

* Erstellen Sie separate gRPC-Kanäle für Bereiche der App, die eine hohe Auslastung aufweisen. Beispielsweise weist der gRPC-Dienst `Logger` möglicherweise eine hohe Auslastung auf. Verwenden Sie einen separaten Kanal, um `LoggerClient` in der App zu erstellen.
* Verwenden Sie einen Pool von gRPC-Kanälen, um beispielsweise eine Liste von gRPC-Kanälen zu erstellen. `Random` wird jedes Mal für die Auswahl eines Kanals aus der Liste verwendet, wenn ein gRPC-Kanal benötigt wird. Bei Verwendung von `Random` werden Aufrufe zufällig auf mehrere Verbindungen verteilt.

> [!IMPORTANT]
> Das Erhöhen der Grenze für die maximale Anzahl gleichzeitiger Streams für den Server ist eine weitere Möglichkeit, das Problem zu lösen. Dies wird in Kestrel mit <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Http2Limits.MaxStreamsPerConnection> konfiguriert.
>
> Von einer Erhöhung der maximalen Anzahl gleichzeitiger Streams wird abgeraten. Bei zu vielen Streams über eine einzelne HTTP/2-Verbindung treten neue Leistungsprobleme auf:
>
> * Threadkonflikte zwischen Streams, die versuchen in die Verbindung zu schreiben
> * Verlust von Verbindungspaketen, der dazu führt, dass alle Aufrufe auf der TCP-Ebene blockiert werden

::: moniker range=">= aspnetcore-5.0"

## <a name="keep-alive-pings"></a>Keep-Alive-Pings

Keep-Alive-Pings können dazu verwendet werden, HTTP/2-Verbindungen während Inaktivität aufrechtzuerhalten. Wenn Sie über eine bereite HTTP/2-Verbindung verfügen, wenn eine App ihre Aktivitäten fortsetzt, werden schnelle anfängliche gRPC-Aufrufe ermöglicht, ohne dass Verzögerungen aufgrund der Wiederherstellung der Verbindung auftreten.

Keep-Alive-Pings werden mit <xref:System.Net.Http.SocketsHttpHandler> konfiguriert:

```csharp
var handler = new SocketsHttpHandler
{
    PooledConnectionIdleTimeout = Timeout.InfiniteTimeSpan,
    KeepAlivePingDelay = TimeSpan.FromSeconds(60),
    KeepAlivePingTimeout = TimeSpan.FromSeconds(30),
    EnableMultipleHttp2Connections = true
};

var channel = GrpcChannel.ForAddress("https://localhost:5001", new GrpcChannelOptions
{
    HttpHandler = handler
});
```

Im obigen Code wird ein Kanal konfiguriert, der alle 60 Sekunden während Inaktivität einen Keep-Alive-Ping an den Server sendet. Durch diesen Ping wird sichergestellt, dass der Server und jegliche verwendeten Proxys die Verbindung nicht aufgrund der Inaktivität beenden.

::: moniker-end

## <a name="streaming"></a>Streaming

Das bidirektionale gRPC-Streaming kann dazu verwendet werden, unäre gRPC-Aufrufe in Hochleistungsszenarios zu ersetzen. Sobald ein bidirektionaler Stream gestartet wurde, ist das Streaming von Nachrichten hin und zurück schneller als das Senden von Nachrichten mit mehreren unären gRPC-Aufrufen. Streamnachrichten werden als Daten einer vorhandenen HTTP/2-Anforderung übermittelt und umgehen den Mehraufwand, der durch das Erstellen einer neuen HTTP/2-Anforderung für jeden unären Aufruf entsteht.

Beispieldienst:

```csharp
public override async Task SayHello(IAsyncStreamReader<HelloRequest> requestStream,
    IServerStreamWriter<HelloReply> responseStream, ServerCallContext context)
{
    await foreach (var request in requestStream.ReadAllAsync())
    {
        var helloReply = new HelloReply { Message = "Hello " + request.Name };

        await responseStream.WriteAsync(helloReply);
    }
}
```

Beispielclient:

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHello();

Console.WriteLine("Type a name then press enter.");
while (true)
{
    var text = Console.ReadLine();

    // Send and receive messages over the stream
    await call.RequestStream.WriteAsync(new HelloRequest { Name = text });
    await call.ResponseStream.MoveNext();

    Console.WriteLine($"Greeting: {call.ResponseStream.Current.Message}");
}
```

Das Ersetzen unärer Aufrufe durch bidirektionales Streaming aus Leistungsgründen ist eine fortgeschrittene Vorgehensweise und eignet sich in vielen Situationen nicht.

In folgenden Fällen eignet sich die Verwendung von Streamingaufrufen:

1. Es ist hoher Durchsatz oder niedrige Latenz erforderlich.
2. gRPC und HTTP/2 wurden als Leistungsengpass identifiziert.
3. Ein Worker im Client sendet oder empfängt regelmäßig Nachrichten mit einem gRPC-Dienst.

Achten Sie auf die zusätzliche Komplexität und Einschränkungen der Verwendung von Streamingaufrufen anstelle von unären Aufrufen:

1. Ein Stream kann von einem Dienst oder Verbindungsfehler unterbrochen werden. Es ist Logik zum Neustarten des Streams erforderlich, wenn ein Fehler auftritt.
2. `RequestStream.WriteAsync` ist für Multithreading nicht sicher. Es kann immer nur eine Nachricht in einem Stream geschrieben werden. Das Senden von Nachrichten aus mehreren Threads über einen einzelnen Stream erfordert eine Ersteller-/Verbraucherwarteschlange wie <xref:System.Threading.Channels.Channel%601> zum Marshallen von Nachrichten.
3. Eine gRPC-Streamingmethode ist darauf beschränkt, eine Art von Nachricht zu empfangen und eine Art von Nachricht zu senden. `rpc StreamingCall(stream RequestMessage) returns (stream ResponseMessage)` empfängt beispielsweise `RequestMessage` und sendet `ResponseMessage`. Mithilfe der Protobuf-Unterstützung unbekannter oder bedingter Nachrichten mit `Any` und `oneof` kann diese Einschränkung umgangen werden.
