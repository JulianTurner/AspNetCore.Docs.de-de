---
title: Erstellen von gRPC-Diensten und -Methoden
author: jamesnk
description: In diesem Artikel erfahren Sie, wie Sie gRPC-Dienste und -Methoden erstellen.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/25/2020
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
uid: grpc/services
ms.openlocfilehash: fde589b2de9d908db26a2557c5f57c715625aadc
ms.sourcegitcommit: 47c9a59ff8a359baa6bca2637d3af87ddca1245b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/27/2020
ms.locfileid: "88945482"
---
# <a name="create-grpc-services-and-methods"></a>Erstellen von gRPC-Diensten und -Methoden

In diesem Artikel wird erläutert, wie Sie gRPC-Dienste und -Methoden in C# erstellen. Dabei werden folgende Themen behandelt:

* Definieren von Diensten und Methoden in *.proto*-Dateien
* Generieren von Code mithilfe von gRPC-C#-Tools
* Implementieren von gRPC-Diensten und -Methoden

## <a name="create-new-grpc-services"></a>Erstellen neuer gRPC-Dienste

[gRPC-Dienste mit C#](xref:grpc/basics) haben den Contract-First-Ansatz von gRPC an die API-Entwicklung eingeführt. Dienste und Nachrichten werden in *.proto*-Dateien definiert. C#-Tools generieren dann Code aus den *.proto*-Dateien. Bei serverseitigen Ressourcen wird pro Dienst ein abstrakter Basistyp generiert, zusammen mit Klassen für die einzelnen Nachrichten.

Folgendes trifft für diese *.proto*-Datei zu:

* Die Datei definiert einen `Greeter`-Dienst.
* Der `Greeter`-Dienst definiert einen `SayHello`-Aufruf.
* `SayHello` sendet eine `HelloRequest`-Nachricht und empfängt eine `HelloReply`-Nachricht.

```protobuf
syntax = "proto3";

service Greeter {
  rpc SayHello (HelloRequest) returns (HelloReply);
}

message HelloRequest {
  string name = 1;
}

message HelloReply {
  string message = 1;
}
```

C#-Tools generieren den `GreeterBase`-C#-Basistyp:

```csharp
public abstract partial class GreeterBase
{
    public virtual Task<HelloReply> SayHello(HelloRequest request, ServerCallContext context)
    {
        throw new RpcException(new Status(StatusCode.Unimplemented, ""));
    }
}

public class HelloRequest
{
    public string Name { get; set; }
}

public class HelloReply
{
    public string Message { get; set; }
}
```

Standardmäßig hat der generierte `GreeterBase`-Basistyp keine Aufgabe. Die dazugehörige virtuelle `SayHello`-Methode gibt einen `UNIMPLEMENTED`-Fehler für Clients zurück, die den Typ aufrufen. Damit der Dienst hilfreich ist, muss eine App eine konkrete Implementierung von `GreeterBase` erstellen:

```csharp
public class GreeterService : GreeterBase
{
    public override Task<HelloReply> UnaryCall(HelloRequest request, ServerCallContext context)
    {
        return Task.FromResult(new HelloRequest { Message = $"Hello {request.Name}" });
    }
}
```

Die Dienstimplementierung wird für die App registriert. Wenn der Dienst vom ASP.NET Core-gRPC gehostet wird, sollte er mit der `MapGrpcService`-Methode der Routingpipeline hinzugefügt werden.

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapGrpcService<GreeterService>();
});
```

Weitere Informationen finden Sie unter <xref:grpc/aspnetcore>.

## <a name="implement-grpc-methods"></a>Implementieren von gRPC-Methoden

Ein gRPC-Dienst kann verschiedene Methodentypen aufweisen. Das Senden und Empfangen von Nachrichten durch einen Dienst hängt vom Typ der definierten Methode ab. Es gibt die folgenden gRPC-Methodentypen:

* Unär
* Serverstreaming
* Clientstreaming
* Bidirektionales Streaming

Streamingaufrufe werden mit dem `stream`-Schlüsselwort in der *.proto*-Datei angegeben. `stream` kann für die Anforderungsnachricht, Antwortnachricht oder beides eines Aufrufs verwendet werden.

```protobuf
syntax = "proto3";

service ExampleService {
  // Unary
  rpc UnaryCall (ExampleRequest) returns (ExampleResponse);

  // Server streaming
  rpc StreamingFromServer (ExampleRequest) returns (stream ExampleResponse);

  // Client streaming
  rpc StreamingFromClient (stream ExampleRequest) returns (ExampleResponse);

  // Bi-directional streaming
  rpc StreamingBothWays (stream ExampleRequest) returns (stream ExampleResponse);
}
```

Jeder Aufruftyp weist eine andere Methodensignatur auf. Das Überschreiben generierter Methoden aus dem Diensttyp mit abstrakter Basis in einer konkreten Implementierung sorgt dafür, dass die richtigen Argumente und Rückgabetypen verwendet werden.

### <a name="unary-method"></a>Unäre Methode

Eine unäre Methode ruft die Anforderungsnachricht als Parameter ab und gibt die Antwort zurück. Ein unärer Aufruf ist abgeschlossen, wenn die Antwort zurückgegeben wurde.

```csharp
public override Task<ExampleResponse> UnaryCall(ExampleRequest request,
    ServerCallContext context)
{
    var response = new ExampleResponse();
    return Task.FromResult(response);
}
```

Unäre Aufrufe ähneln [Aktionen für Web-API-Controller](xref:web-api/index) am stärksten. Ein wichtiger Unterschied zwischen gRPC-Methoden und Aktionen ist der, dass gRPC-Methoden keine Teile einer Anforderung an andere Methodenargumente binden können. gRPC-Methoden weisen immer ein Nachrichtenargument für die eingehenden Anforderungsdaten auf. Weiterhin können jedoch mehrere Werte an einen gRPC-Dienst gesendet werden, indem sie zu Feldern in der Anforderungsnachricht gemacht werden:

```protobuf
message ExampleRequest {
    int pageIndex = 1;
    int pageSize = 2;
    bool isDescending = 3;
}
```

### <a name="server-streaming-method"></a>Serverstreamingmethode

Eine Serverstreamingmethode ruft die Anforderungsnachricht als Parameter ab. Da mehrere Nachrichten zurück zum Aufrufer gestreamt werden können, wird `responseStream.WriteAsync` verwendet, um Antwortnachrichten zu senden. Der Serverstreamingaufruf ist abgeschlossen, wenn die Methode etwas zurückgibt.

```csharp
public override async Task StreamingFromServer(ExampleRequest request,
    IServerStreamWriter<ExampleResponse> responseStream, ServerCallContext context)
{
    for (var i = 0; i < 5; i++)
    {
        await responseStream.WriteAsync(new ExampleResponse());
        await Task.Delay(TimeSpan.FromSeconds(1));
    }
}
```

Der Client hat keine Möglichkeit, weitere Nachrichten oder Daten zu senden, sobald die Serverstreamingmethode gestartet wurde. Einige Streamingmethoden sind so entworfen, dass sie fortlaufend ausgeführt werden. Bei fortlaufenden Streamingmethoden kann ein Client den Aufruf abbrechen, wenn dieser länger dauert als nötig. Bei einem Abbruch sendet der Client ein Signal an den Server, und [ServerCallContext.CancellationToken](xref:System.Threading.CancellationToken) wird ausgelöst. Das `CancellationToken`-Token sollte für den Server mit asynchronen Methoden verwendet werden, sodass Folgendes möglich ist:

* Asynchrone Aufgaben werden zusammen mit dem Streamingaufruf abgebrochen.
* Die Methode kann schnell beendet werden.

```csharp
public override async Task StreamingFromServer(ExampleRequest request,
    IServerStreamWriter<ExampleResponse> responseStream, ServerCallContext context)
{
    while (!context.CancellationToken.IsCancellationRequested)
    {
        await responseStream.WriteAsync(new ExampleResponse());
        await Task.Delay(TimeSpan.FromSeconds(1), context.CancellationToken);
    }
}
```

### <a name="client-streaming-method"></a>Clientstreamingmethode

Eine Clientstreamingmethode beginnt, *ohne* dass die Methode eine Nachricht empfängt. Der `requestStream`-Parameter wird verwendet, um Nachrichten des Clients zu lesen. Ein Clientstreamingaufruf ist abgeschlossen, wenn eine Antwortnachricht zurückgegeben wird:

```csharp
public override async Task<ExampleResponse> StreamingFromClient(
    IAsyncStreamReader<ExampleRequest> requestStream, ServerCallContext context)
{
    while (await requestStream.MoveNext())
    {
        var message = requestStream.Current;
        // ...
    }
    return new ExampleResponse();
}
```

Wenn Sie C# 8 oder höher verwenden, kann die `await foreach`-Syntax verwendet werden, um Nachrichten zu lesen. Die `IAsyncStreamReader<T>.ReadAllAsync()`-Erweiterungsmethode liest alle Nachrichten aus dem Anforderungsstream:

```csharp
public override async Task<ExampleResponse> StreamingFromClient(
    IAsyncStreamReader<ExampleRequest> requestStream, ServerCallContext context)
{
    await foreach (var message in requestStream.ReadAllAsync())
    {
        // ...
    }
    return new ExampleResponse();
}
```

### <a name="bi-directional-streaming-method"></a>Bidirektionale Streamingmethode

Eine bidirektionale Streamingmethode beginnt, *ohne* dass die Methode eine Nachricht empfängt. Der `requestStream`-Parameter wird verwendet, um Nachrichten des Clients zu lesen. Die Methode kann Nachrichten mit `responseStream.WriteAsync` senden. Ein bidirektionaler Streamingaufruf ist abgeschlossen, wenn die Methode etwas zurückgibt:

```csharp
public override async Task StreamingBothWays(IAsyncStreamReader<ExampleRequest> requestStream,
    IServerStreamWriter<ExampleResponse> responseStream, ServerCallContext context)
{
    await foreach (var message in requestStream.ReadAllAsync())
    {
        await responseStream.WriteAsync(new ExampleResponse());
    }
}
```

Der vorangehende Code:

* Sendet eine Antwort pro Anforderung.
* Ist eine einfache Verwendung des bidirektionalen Streamings.

Es ist möglich, auch komplexere Szenarios zu unterstützen, z. B. das gleichzeitige Lesen von Anforderungen und Senden von Antworten.

```csharp
public override async Task StreamingBothWays(IAsyncStreamReader<ExampleRequest> requestStream,
    IServerStreamWriter<ExampleResponse> responseStream, ServerCallContext context)
{
    // Read requests in a background task.
    var readTask = Task.Run(async () =>
    {
        await foreach (var message in requestStream.ReadAllAsync())
        {
            // Process request.
        }
    });
    
    // Send responses until the client signals that it is complete.
    while (!readTask.IsCompleted)
    {
        await responseStream.WriteAsync(new ExampleResponse());
        await Task.Delay(TimeSpan.FromSeconds(1), context.CancellationToken);
    }
}
```

Bei einer bidirektionalen Streamingmethode können sich der Client und der Dienst jederzeit gegenseitig Nachrichten senden. Die beste Implementierung einer bidirektionalen Methode variiert je nach Anforderungen.

## <a name="access-grpc-request-headers"></a>Zugriff auf gRPC-Anforderungsheader

Eine Anforderungsnachricht ist nicht die einzige Möglichkeit für einen Client, Daten an einen gRPC-Dienst zu senden. Headerwerte stehen in einem Dienst mithilfe von `ServerCallContext.RequestHeaders` zur Verfügung.

```csharp
public override Task<ExampleResponse> UnaryCall(ExampleRequest request, ServerCallContext context)
{
    var userAgent = context.RequestHeaders.GetValue("user-agent");
    // ...

    return Task.FromResult(new ExampleResponse());
}
```

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:grpc/basics>
* <xref:grpc/client>
