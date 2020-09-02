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
# <a name="create-grpc-services-and-methods"></a><span data-ttu-id="da39b-103">Erstellen von gRPC-Diensten und -Methoden</span><span class="sxs-lookup"><span data-stu-id="da39b-103">Create gRPC services and methods</span></span>

<span data-ttu-id="da39b-104">In diesem Artikel wird erläutert, wie Sie gRPC-Dienste und -Methoden in C# erstellen.</span><span class="sxs-lookup"><span data-stu-id="da39b-104">This document explains how to create gRPC services and methods in C#.</span></span> <span data-ttu-id="da39b-105">Dabei werden folgende Themen behandelt:</span><span class="sxs-lookup"><span data-stu-id="da39b-105">Topics include:</span></span>

* <span data-ttu-id="da39b-106">Definieren von Diensten und Methoden in *.proto*-Dateien</span><span class="sxs-lookup"><span data-stu-id="da39b-106">How to define services and methods in *.proto* files.</span></span>
* <span data-ttu-id="da39b-107">Generieren von Code mithilfe von gRPC-C#-Tools</span><span class="sxs-lookup"><span data-stu-id="da39b-107">Generated code using gRPC C# tooling.</span></span>
* <span data-ttu-id="da39b-108">Implementieren von gRPC-Diensten und -Methoden</span><span class="sxs-lookup"><span data-stu-id="da39b-108">Implementing gRPC services and methods.</span></span>

## <a name="create-new-grpc-services"></a><span data-ttu-id="da39b-109">Erstellen neuer gRPC-Dienste</span><span class="sxs-lookup"><span data-stu-id="da39b-109">Create new gRPC services</span></span>

<span data-ttu-id="da39b-110">[gRPC-Dienste mit C#](xref:grpc/basics) haben den Contract-First-Ansatz von gRPC an die API-Entwicklung eingeführt.</span><span class="sxs-lookup"><span data-stu-id="da39b-110">[gRPC services with C#](xref:grpc/basics) introduced gRPC's contract-first approach to API development.</span></span> <span data-ttu-id="da39b-111">Dienste und Nachrichten werden in *.proto*-Dateien definiert.</span><span class="sxs-lookup"><span data-stu-id="da39b-111">Services and messages are defined in *.proto* files.</span></span> <span data-ttu-id="da39b-112">C#-Tools generieren dann Code aus den *.proto*-Dateien.</span><span class="sxs-lookup"><span data-stu-id="da39b-112">C# tooling then generates code from *.proto* files.</span></span> <span data-ttu-id="da39b-113">Bei serverseitigen Ressourcen wird pro Dienst ein abstrakter Basistyp generiert, zusammen mit Klassen für die einzelnen Nachrichten.</span><span class="sxs-lookup"><span data-stu-id="da39b-113">For server-side assets, an abstract base type is generated for each service, along with classes for any messages.</span></span>

<span data-ttu-id="da39b-114">Folgendes trifft für diese *.proto*-Datei zu:</span><span class="sxs-lookup"><span data-stu-id="da39b-114">The following *.proto* file:</span></span>

* <span data-ttu-id="da39b-115">Die Datei definiert einen `Greeter`-Dienst.</span><span class="sxs-lookup"><span data-stu-id="da39b-115">Defines a `Greeter` service.</span></span>
* <span data-ttu-id="da39b-116">Der `Greeter`-Dienst definiert einen `SayHello`-Aufruf.</span><span class="sxs-lookup"><span data-stu-id="da39b-116">The `Greeter` service defines a `SayHello` call.</span></span>
* <span data-ttu-id="da39b-117">`SayHello` sendet eine `HelloRequest`-Nachricht und empfängt eine `HelloReply`-Nachricht.</span><span class="sxs-lookup"><span data-stu-id="da39b-117">`SayHello` sends a `HelloRequest` message and receives a `HelloReply` message</span></span>

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

<span data-ttu-id="da39b-118">C#-Tools generieren den `GreeterBase`-C#-Basistyp:</span><span class="sxs-lookup"><span data-stu-id="da39b-118">C# tooling generates the C# `GreeterBase` base type:</span></span>

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

<span data-ttu-id="da39b-119">Standardmäßig hat der generierte `GreeterBase`-Basistyp keine Aufgabe.</span><span class="sxs-lookup"><span data-stu-id="da39b-119">By default the generated `GreeterBase` doesn't do anything.</span></span> <span data-ttu-id="da39b-120">Die dazugehörige virtuelle `SayHello`-Methode gibt einen `UNIMPLEMENTED`-Fehler für Clients zurück, die den Typ aufrufen.</span><span class="sxs-lookup"><span data-stu-id="da39b-120">Its virtual `SayHello` method will return an `UNIMPLEMENTED` error to any clients that call it.</span></span> <span data-ttu-id="da39b-121">Damit der Dienst hilfreich ist, muss eine App eine konkrete Implementierung von `GreeterBase` erstellen:</span><span class="sxs-lookup"><span data-stu-id="da39b-121">For the service to be useful an app must create a concrete implementation of `GreeterBase`:</span></span>

```csharp
public class GreeterService : GreeterBase
{
    public override Task<HelloReply> UnaryCall(HelloRequest request, ServerCallContext context)
    {
        return Task.FromResult(new HelloRequest { Message = $"Hello {request.Name}" });
    }
}
```

<span data-ttu-id="da39b-122">Die Dienstimplementierung wird für die App registriert.</span><span class="sxs-lookup"><span data-stu-id="da39b-122">The service implementation is registered with the app.</span></span> <span data-ttu-id="da39b-123">Wenn der Dienst vom ASP.NET Core-gRPC gehostet wird, sollte er mit der `MapGrpcService`-Methode der Routingpipeline hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="da39b-123">If the service is hosted by ASP.NET Core gRPC, it should be added to the routing pipeline with the `MapGrpcService` method.</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapGrpcService<GreeterService>();
});
```

<span data-ttu-id="da39b-124">Weitere Informationen finden Sie unter <xref:grpc/aspnetcore>.</span><span class="sxs-lookup"><span data-stu-id="da39b-124">See <xref:grpc/aspnetcore> for more information.</span></span>

## <a name="implement-grpc-methods"></a><span data-ttu-id="da39b-125">Implementieren von gRPC-Methoden</span><span class="sxs-lookup"><span data-stu-id="da39b-125">Implement gRPC methods</span></span>

<span data-ttu-id="da39b-126">Ein gRPC-Dienst kann verschiedene Methodentypen aufweisen.</span><span class="sxs-lookup"><span data-stu-id="da39b-126">A gRPC service can have different types of methods.</span></span> <span data-ttu-id="da39b-127">Das Senden und Empfangen von Nachrichten durch einen Dienst hängt vom Typ der definierten Methode ab.</span><span class="sxs-lookup"><span data-stu-id="da39b-127">How messages are sent and received by a service depends on the type of method defined.</span></span> <span data-ttu-id="da39b-128">Es gibt die folgenden gRPC-Methodentypen:</span><span class="sxs-lookup"><span data-stu-id="da39b-128">The gRPC method types are:</span></span>

* <span data-ttu-id="da39b-129">Unär</span><span class="sxs-lookup"><span data-stu-id="da39b-129">Unary</span></span>
* <span data-ttu-id="da39b-130">Serverstreaming</span><span class="sxs-lookup"><span data-stu-id="da39b-130">Server streaming</span></span>
* <span data-ttu-id="da39b-131">Clientstreaming</span><span class="sxs-lookup"><span data-stu-id="da39b-131">Client streaming</span></span>
* <span data-ttu-id="da39b-132">Bidirektionales Streaming</span><span class="sxs-lookup"><span data-stu-id="da39b-132">Bi-directional streaming</span></span>

<span data-ttu-id="da39b-133">Streamingaufrufe werden mit dem `stream`-Schlüsselwort in der *.proto*-Datei angegeben.</span><span class="sxs-lookup"><span data-stu-id="da39b-133">Streaming calls are specified with the `stream` keyword in the *.proto* file.</span></span> <span data-ttu-id="da39b-134">`stream` kann für die Anforderungsnachricht, Antwortnachricht oder beides eines Aufrufs verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="da39b-134">`stream` can be placed on a call's request message, response message, or both.</span></span>

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

<span data-ttu-id="da39b-135">Jeder Aufruftyp weist eine andere Methodensignatur auf.</span><span class="sxs-lookup"><span data-stu-id="da39b-135">Each call type has a different method signature.</span></span> <span data-ttu-id="da39b-136">Das Überschreiben generierter Methoden aus dem Diensttyp mit abstrakter Basis in einer konkreten Implementierung sorgt dafür, dass die richtigen Argumente und Rückgabetypen verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="da39b-136">Overriding generated methods from the abstract base service type in a concrete implementation ensures the correct arguments and return type are used.</span></span>

### <a name="unary-method"></a><span data-ttu-id="da39b-137">Unäre Methode</span><span class="sxs-lookup"><span data-stu-id="da39b-137">Unary method</span></span>

<span data-ttu-id="da39b-138">Eine unäre Methode ruft die Anforderungsnachricht als Parameter ab und gibt die Antwort zurück.</span><span class="sxs-lookup"><span data-stu-id="da39b-138">A unary method gets the request message as a parameter, and returns the response.</span></span> <span data-ttu-id="da39b-139">Ein unärer Aufruf ist abgeschlossen, wenn die Antwort zurückgegeben wurde.</span><span class="sxs-lookup"><span data-stu-id="da39b-139">A unary call is complete when the response is returned.</span></span>

```csharp
public override Task<ExampleResponse> UnaryCall(ExampleRequest request,
    ServerCallContext context)
{
    var response = new ExampleResponse();
    return Task.FromResult(response);
}
```

<span data-ttu-id="da39b-140">Unäre Aufrufe ähneln [Aktionen für Web-API-Controller](xref:web-api/index) am stärksten.</span><span class="sxs-lookup"><span data-stu-id="da39b-140">Unary calls are the most similar to [actions on web API controllers](xref:web-api/index).</span></span> <span data-ttu-id="da39b-141">Ein wichtiger Unterschied zwischen gRPC-Methoden und Aktionen ist der, dass gRPC-Methoden keine Teile einer Anforderung an andere Methodenargumente binden können.</span><span class="sxs-lookup"><span data-stu-id="da39b-141">One important difference gRPC methods have from actions is gRPC methods are not able to bind parts of a request to different method arguments.</span></span> <span data-ttu-id="da39b-142">gRPC-Methoden weisen immer ein Nachrichtenargument für die eingehenden Anforderungsdaten auf.</span><span class="sxs-lookup"><span data-stu-id="da39b-142">gRPC methods always have one message argument for the incoming request data.</span></span> <span data-ttu-id="da39b-143">Weiterhin können jedoch mehrere Werte an einen gRPC-Dienst gesendet werden, indem sie zu Feldern in der Anforderungsnachricht gemacht werden:</span><span class="sxs-lookup"><span data-stu-id="da39b-143">Multiple values can still be sent to a gRPC service by making them fields on the request message:</span></span>

```protobuf
message ExampleRequest {
    int pageIndex = 1;
    int pageSize = 2;
    bool isDescending = 3;
}
```

### <a name="server-streaming-method"></a><span data-ttu-id="da39b-144">Serverstreamingmethode</span><span class="sxs-lookup"><span data-stu-id="da39b-144">Server streaming method</span></span>

<span data-ttu-id="da39b-145">Eine Serverstreamingmethode ruft die Anforderungsnachricht als Parameter ab.</span><span class="sxs-lookup"><span data-stu-id="da39b-145">A server streaming method gets the request message as a parameter.</span></span> <span data-ttu-id="da39b-146">Da mehrere Nachrichten zurück zum Aufrufer gestreamt werden können, wird `responseStream.WriteAsync` verwendet, um Antwortnachrichten zu senden.</span><span class="sxs-lookup"><span data-stu-id="da39b-146">Because multiple messages can be streamed back to the caller, `responseStream.WriteAsync` is used to send response messages.</span></span> <span data-ttu-id="da39b-147">Der Serverstreamingaufruf ist abgeschlossen, wenn die Methode etwas zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="da39b-147">A server streaming call is complete when the method returns.</span></span>

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

<span data-ttu-id="da39b-148">Der Client hat keine Möglichkeit, weitere Nachrichten oder Daten zu senden, sobald die Serverstreamingmethode gestartet wurde.</span><span class="sxs-lookup"><span data-stu-id="da39b-148">The client has no way to send additional messages or data once the server streaming method has started.</span></span> <span data-ttu-id="da39b-149">Einige Streamingmethoden sind so entworfen, dass sie fortlaufend ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="da39b-149">Some streaming methods are designed to run forever.</span></span> <span data-ttu-id="da39b-150">Bei fortlaufenden Streamingmethoden kann ein Client den Aufruf abbrechen, wenn dieser länger dauert als nötig.</span><span class="sxs-lookup"><span data-stu-id="da39b-150">For continuous streaming methods, a client can cancel the call when it's no longer needed.</span></span> <span data-ttu-id="da39b-151">Bei einem Abbruch sendet der Client ein Signal an den Server, und [ServerCallContext.CancellationToken](xref:System.Threading.CancellationToken) wird ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="da39b-151">When cancellation happens the client sends a signal to the server and the [ServerCallContext.CancellationToken](xref:System.Threading.CancellationToken) is raised.</span></span> <span data-ttu-id="da39b-152">Das `CancellationToken`-Token sollte für den Server mit asynchronen Methoden verwendet werden, sodass Folgendes möglich ist:</span><span class="sxs-lookup"><span data-stu-id="da39b-152">The `CancellationToken` token should be used on the server with async methods so that:</span></span>

* <span data-ttu-id="da39b-153">Asynchrone Aufgaben werden zusammen mit dem Streamingaufruf abgebrochen.</span><span class="sxs-lookup"><span data-stu-id="da39b-153">Any asynchronous work is canceled together with the streaming call.</span></span>
* <span data-ttu-id="da39b-154">Die Methode kann schnell beendet werden.</span><span class="sxs-lookup"><span data-stu-id="da39b-154">The method exits quickly.</span></span>

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

### <a name="client-streaming-method"></a><span data-ttu-id="da39b-155">Clientstreamingmethode</span><span class="sxs-lookup"><span data-stu-id="da39b-155">Client streaming method</span></span>

<span data-ttu-id="da39b-156">Eine Clientstreamingmethode beginnt, *ohne* dass die Methode eine Nachricht empfängt.</span><span class="sxs-lookup"><span data-stu-id="da39b-156">A client streaming method starts *without* the method receiving a message.</span></span> <span data-ttu-id="da39b-157">Der `requestStream`-Parameter wird verwendet, um Nachrichten des Clients zu lesen.</span><span class="sxs-lookup"><span data-stu-id="da39b-157">The `requestStream` parameter is used to read messages from the client.</span></span> <span data-ttu-id="da39b-158">Ein Clientstreamingaufruf ist abgeschlossen, wenn eine Antwortnachricht zurückgegeben wird:</span><span class="sxs-lookup"><span data-stu-id="da39b-158">A client streaming call is complete when a response message is returned:</span></span>

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

<span data-ttu-id="da39b-159">Wenn Sie C# 8 oder höher verwenden, kann die `await foreach`-Syntax verwendet werden, um Nachrichten zu lesen.</span><span class="sxs-lookup"><span data-stu-id="da39b-159">When using C# 8 or later, the `await foreach` syntax can be used to read messages.</span></span> <span data-ttu-id="da39b-160">Die `IAsyncStreamReader<T>.ReadAllAsync()`-Erweiterungsmethode liest alle Nachrichten aus dem Anforderungsstream:</span><span class="sxs-lookup"><span data-stu-id="da39b-160">The `IAsyncStreamReader<T>.ReadAllAsync()` extension method reads all messages from the request stream:</span></span>

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

### <a name="bi-directional-streaming-method"></a><span data-ttu-id="da39b-161">Bidirektionale Streamingmethode</span><span class="sxs-lookup"><span data-stu-id="da39b-161">Bi-directional streaming method</span></span>

<span data-ttu-id="da39b-162">Eine bidirektionale Streamingmethode beginnt, *ohne* dass die Methode eine Nachricht empfängt.</span><span class="sxs-lookup"><span data-stu-id="da39b-162">A bi-directional streaming method starts *without* the method receiving a message.</span></span> <span data-ttu-id="da39b-163">Der `requestStream`-Parameter wird verwendet, um Nachrichten des Clients zu lesen.</span><span class="sxs-lookup"><span data-stu-id="da39b-163">The `requestStream` parameter is used to read messages from the client.</span></span> <span data-ttu-id="da39b-164">Die Methode kann Nachrichten mit `responseStream.WriteAsync` senden.</span><span class="sxs-lookup"><span data-stu-id="da39b-164">The method can choose to send messages with `responseStream.WriteAsync`.</span></span> <span data-ttu-id="da39b-165">Ein bidirektionaler Streamingaufruf ist abgeschlossen, wenn die Methode etwas zurückgibt:</span><span class="sxs-lookup"><span data-stu-id="da39b-165">A bi-directional streaming call is complete when the the method returns:</span></span>

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

<span data-ttu-id="da39b-166">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="da39b-166">The preceding code:</span></span>

* <span data-ttu-id="da39b-167">Sendet eine Antwort pro Anforderung.</span><span class="sxs-lookup"><span data-stu-id="da39b-167">Sends a response for each request.</span></span>
* <span data-ttu-id="da39b-168">Ist eine einfache Verwendung des bidirektionalen Streamings.</span><span class="sxs-lookup"><span data-stu-id="da39b-168">Is a basic usage of bi-directional streaming.</span></span>

<span data-ttu-id="da39b-169">Es ist möglich, auch komplexere Szenarios zu unterstützen, z. B. das gleichzeitige Lesen von Anforderungen und Senden von Antworten.</span><span class="sxs-lookup"><span data-stu-id="da39b-169">It is possible to support more complex scenarios, such as reading requests and sending responses simultaneously:</span></span>

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

<span data-ttu-id="da39b-170">Bei einer bidirektionalen Streamingmethode können sich der Client und der Dienst jederzeit gegenseitig Nachrichten senden.</span><span class="sxs-lookup"><span data-stu-id="da39b-170">In a bi-directional streaming method, the client and service can send messages to each other at any time.</span></span> <span data-ttu-id="da39b-171">Die beste Implementierung einer bidirektionalen Methode variiert je nach Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="da39b-171">The best implementation of a bi-directional method varies depending upon requirements.</span></span>

## <a name="access-grpc-request-headers"></a><span data-ttu-id="da39b-172">Zugriff auf gRPC-Anforderungsheader</span><span class="sxs-lookup"><span data-stu-id="da39b-172">Access gRPC request headers</span></span>

<span data-ttu-id="da39b-173">Eine Anforderungsnachricht ist nicht die einzige Möglichkeit für einen Client, Daten an einen gRPC-Dienst zu senden.</span><span class="sxs-lookup"><span data-stu-id="da39b-173">A request message is not the only way for a client to send data to a gRPC service.</span></span> <span data-ttu-id="da39b-174">Headerwerte stehen in einem Dienst mithilfe von `ServerCallContext.RequestHeaders` zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="da39b-174">Header values are available in a service using `ServerCallContext.RequestHeaders`.</span></span>

```csharp
public override Task<ExampleResponse> UnaryCall(ExampleRequest request, ServerCallContext context)
{
    var userAgent = context.RequestHeaders.GetValue("user-agent");
    // ...

    return Task.FromResult(new ExampleResponse());
}
```

## <a name="additional-resources"></a><span data-ttu-id="da39b-175">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="da39b-175">Additional resources</span></span>

* <xref:grpc/basics>
* <xref:grpc/client>
