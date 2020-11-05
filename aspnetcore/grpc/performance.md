---
title: Bewährte Methoden für Leistung mit gRPC
author: jamesnk
description: In diesem Artikel werden bewährte Methoden zum Erstellen leistungsstarker gRPC-Dienste vorgestellt.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/23/2020
no-loc:
- appsettings.json
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
ms.openlocfilehash: 622c6ba042c5832f99bba379fadd9aba7d7163f2
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060403"
---
# <a name="performance-best-practices-with-grpc"></a>Bewährte Methoden für Leistung mit gRPC

Von [James Newton-King](https://twitter.com/jamesnk)

gRPC ist für leistungsstarke Dienste konzipiert. In dieser Dokumentation wird erläutert, wie Sie die bestmögliche Leistung von gRPC erzielen.

## <a name="reuse-grpc-channels"></a>Wiederverwenden von gRPC-Kanälen

Ein gRPC-Kanal sollte für gRPC-Aufrufe wiederverwendet werden. Durch die Wiederverwendung eines Kanals können Aufrufe für Multiplexing über eine vorhandene HTTP/2-Verbindung verwendet werden.

Wenn ein neuer Kanal für jeden gRPC-Aufruf erstellt wird, kann dies deutlich mehr Zeit beanspruchen. Jeder Aufruf erfordert mehrere Netzwerkroundtrips zwischen dem Client und dem Server, um eine neue HTTP/2-Verbindung herzustellen:

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

Die gRPC-Clientfactory bietet eine zentralisierte Möglichkeit zum Konfigurieren von Kanälen. Zugrunde liegende Kanäle werden automatisch wiederverwendet. Weitere Informationen finden Sie unter <xref:grpc/clientfactory>.

## <a name="connection-concurrency"></a>Verbindungsparallelität

HTTP/2-Verbindungen weisen in der Regel eine Einschränkung für die [maximale Anzahl gleichzeitiger Streams (aktive HTTP-Anforderungen)](https://httpwg.github.io/specs/rfc7540.html#rfc.section.5.1.2) für eine Verbindung auf. Für die meisten Server liegt eine Standardbeschränkung auf 100 gleichzeitige Streams vor.

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

## <a name="load-balancing"></a>Lastenausgleich

Einige Lastenausgleiche funktionieren mit gRPC nicht effektiv. L4-Lastenausgleiche (Transport) agieren auf Verbindungsebene, indem sie TCP-Verbindungen endpunktübergreifend verteilen. Dieser Ansatz eignet sich für den Lastenausgleich von API-Aufrufen mit HTTP/1.1. Gleichzeitige HTTP/1.1-Aufrufe werden über verschiedene Verbindungen gesendet, sodass der Lastenausgleich für diese endpunktübergreifend erfolgen kann.

Da L4-Lastenausgleiche auf Verbindungsebene agieren, sind sie für gRPC nicht besonders gut geeignet. gRPC verwendet HTTP/2, sodass ein Multiplexing für mehrere Aufrufe über eine einzige TCP-Verbindung erfolgt. Alle gRPC-Aufrufe über diese Verbindung werden an einen Endpunkt gesendet.

Es gibt zwei Optionen für den effektiven Lastenausgleich von gRPC:

* Clientseitiger Lastenausgleich
* L7-Proxylastenausgleich (Anwendung)

> [!NOTE]
> Nur für gRPC-Aufrufe kann ein Lastenausgleich zwischen Endpunkten ausgeführt werden. Sobald ein gRPC-Streamingaufruf erstellt wurde, werden alle über den Stream gesendeten Nachrichten an einen Endpunkt weitergeleitet.

### <a name="client-side-load-balancing"></a>Clientseitiger Lastenausgleich

Beim clientseitigen Lastenausgleich kennt der Client die Endpunkte. Für jeden gRPC-Rückruf wählt er einen anderen Endpunkt aus, an den der Aufruf gesendet werden soll. Der clientseitige Lastenausgleich ist eine gute Wahl, wenn die Latenz entscheidend ist. Zwischen dem Client und dem Dienst ist kein Proxy vorhanden, sodass der Aufruf direkt an den Dienst gesendet wird. Der Nachteil beim clientseitigen Lastenausgleich besteht darin, dass jeder Client die verfügbaren Endpunkte nachverfolgen muss, die er verwenden soll.

Der clientseitige Lookaside-Lastenausgleich ist ein Verfahren, bei dem der Lastenausgleichzustand an einem zentralen Ort gespeichert wird. Clients fragen diesen zentralen Ort regelmäßig auf Informationen bezüglich Lastenausgleichsentscheidungen ab.

`Grpc.Net.Client` unterstützt derzeit keinen clientseitigen Lastenausgleich. [Grpc.Core](https://www.nuget.org/packages/Grpc.Core) ist eine gute Wahl, wenn der clientseitige Lastenausgleich in .NET erforderlich ist.

### <a name="proxy-load-balancing"></a>Proxylastenausgleich

Ein L7-Proxy (Anwendung) agiert auf einer höheren Ebene als ein L4-Proxy (Transport). L7-Proxys sind mit HTTP/2 kompatibel und können gRPC-Aufrufe per Multiplexing über eine HTTP/2-Verbindung und mehrere Endpunkte an den Proxy verteilen. Die Verwendung eines Proxys ist einfacher als der clientseitige Lastenausgleich, kann jedoch die Latenz für gRPC-Aufrufe erhöhen.

Es gibt viele verfügbare L7-Proxys. Unter anderem gibt es folgende Optionen:

* [Envoy](https://www.envoyproxy.io/), ein beliebter Open-Source-Proxy
* [Linkerd](https://linkerd.io/), ein Dienstnetz für Kubernetes
* [YARP: A Reverse Proxy](https://microsoft.github.io/reverse-proxy/), ein als Vorschauversion verfügbarer Open-Source-Proxy, der in .NET geschrieben wurde

::: moniker range=">= aspnetcore-5.0"

## <a name="inter-process-communication"></a>Prozessübergreifende Kommunikation

gRPC-Aufrufe zwischen einem Client und einem Dienst werden in der Regel über TCP-Sockets gesendet. TCP eignet sich ideal für die Kommunikation über ein Netzwerk, [Inter-Process Communication (IPC)](https://wikipedia.org/wiki/Inter-process_communication) ist jedoch noch effizienter, wenn sich Client und Dienst auf demselben Computer befinden.

Erwägen Sie die Verwendung eines Transports wie Unix-Domänensockets oder Named Pipes für gRPC-Aufrufe zwischen Prozessen auf derselben Computer. Weitere Informationen finden Sie unter <xref:grpc/interprocess>.

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
