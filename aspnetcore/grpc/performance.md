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
# <a name="performance-best-practices-in-grpc-for-aspnet-core"></a><span data-ttu-id="9a49a-103">Bewährte Methoden für die Leistung in gRPC für ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9a49a-103">Performance best practices in gRPC for ASP.NET Core</span></span>

<span data-ttu-id="9a49a-104">Von [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="9a49a-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="9a49a-105">gRPC ist für leistungsstarke Dienste konzipiert.</span><span class="sxs-lookup"><span data-stu-id="9a49a-105">gRPC is designed for high-performance services.</span></span> <span data-ttu-id="9a49a-106">In dieser Dokumentation wird erläutert, wie Sie die bestmögliche Leistung von gRPC erzielen.</span><span class="sxs-lookup"><span data-stu-id="9a49a-106">This document explains how to get the best performance possible from gRPC.</span></span>

## <a name="reuse-channel"></a><span data-ttu-id="9a49a-107">Wiederverwenden von Kanälen</span><span class="sxs-lookup"><span data-stu-id="9a49a-107">Reuse channel</span></span>

<span data-ttu-id="9a49a-108">Ein gRPC-Kanal sollte für gRPC-Aufrufe wiederverwendet werden.</span><span class="sxs-lookup"><span data-stu-id="9a49a-108">A gRPC channel should be reused when making gRPC calls.</span></span> <span data-ttu-id="9a49a-109">Durch die Wiederverwendung eines Kanals können Aufrufe für Multiplexing über eine vorhandene HTTP/2-Verbindung verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="9a49a-109">Reusing a channel allows calls to be multiplexed through an existing HTTP/2 connection.</span></span>

<span data-ttu-id="9a49a-110">Wenn ein neuer Kanal für jeden gRPC-Aufruf erstellt wird, kann dies deutlich mehr Zeit beanspruchen.</span><span class="sxs-lookup"><span data-stu-id="9a49a-110">If a new channel is created for each gRPC call then the amount of time it takes to complete can increase significantly.</span></span> <span data-ttu-id="9a49a-111">Jeder Aufruf erfordert mehrere Netzwerkroundtrips zwischen dem Client und dem Server, um eine HTTP/2-Verbindung herzustellen:</span><span class="sxs-lookup"><span data-stu-id="9a49a-111">Each call will require multiple network round-trips between the client and the server to create an HTTP/2 connection:</span></span>

1. <span data-ttu-id="9a49a-112">Öffnen eines Sockets</span><span class="sxs-lookup"><span data-stu-id="9a49a-112">Opening a socket</span></span>
2. <span data-ttu-id="9a49a-113">Herstellen der TCP-Verbindung</span><span class="sxs-lookup"><span data-stu-id="9a49a-113">Establishing TCP connection</span></span>
3. <span data-ttu-id="9a49a-114">TLS-Aushandlung</span><span class="sxs-lookup"><span data-stu-id="9a49a-114">Negotiating TLS</span></span>
4. <span data-ttu-id="9a49a-115">Starten der HTTP/2-Verbindung</span><span class="sxs-lookup"><span data-stu-id="9a49a-115">Starting HTTP/2 connection</span></span>
5. <span data-ttu-id="9a49a-116">Ausführen des gRPC-Aufrufs</span><span class="sxs-lookup"><span data-stu-id="9a49a-116">Making the gRPC call</span></span>

<span data-ttu-id="9a49a-117">Kanäle können problemlos für mehrere gRPC-Aufrufe gemeinsam genutzt und wiederverwendet werden:</span><span class="sxs-lookup"><span data-stu-id="9a49a-117">Channels are safe to share and reuse between gRPC calls:</span></span>

* <span data-ttu-id="9a49a-118">gRPC-Clients werden mit Kanälen erstellt.</span><span class="sxs-lookup"><span data-stu-id="9a49a-118">gRPC clients are created with channels.</span></span> <span data-ttu-id="9a49a-119">Bei gRPC-Clients handelt es sich um Lightweightobjekte. Sie müssen nicht zwischengespeichert oder wiederverwendet werden.</span><span class="sxs-lookup"><span data-stu-id="9a49a-119">gRPC clients are lightweight objects and don't need to be cached or reused.</span></span>
* <span data-ttu-id="9a49a-120">Aus einem Kanal können mehrere gRPC-Clients erstellt werden, einschließlich verschiedener Clienttypen.</span><span class="sxs-lookup"><span data-stu-id="9a49a-120">Multiple gRPC clients can be created from a channel, including different types of clients.</span></span>
* <span data-ttu-id="9a49a-121">Ein Kanal und Clients, die aus dem Kanal erstellt wurden, können sicher von mehreren Threads verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="9a49a-121">A channel and clients created from the channel can safely be used by multiple threads.</span></span>
* <span data-ttu-id="9a49a-122">Clients, die aus dem Kanal erstellt wurden, können mehrere gleichzeitige Aufrufe durchführen.</span><span class="sxs-lookup"><span data-stu-id="9a49a-122">Clients created from the channel can make multiple simultaneous calls.</span></span>

## <a name="connection-concurrency"></a><span data-ttu-id="9a49a-123">Verbindungsparallelität</span><span class="sxs-lookup"><span data-stu-id="9a49a-123">Connection concurrency</span></span>

<span data-ttu-id="9a49a-124">HTTP/2-Verbindungen weisen in der Regel eine Einschränkung für die [maximale Anzahl gleichzeitiger Streams (aktive HTTP-Anforderungen)](https://http2.github.io/http2-spec/#rfc.section.5.1.2) für eine Verbindung auf.</span><span class="sxs-lookup"><span data-stu-id="9a49a-124">HTTP/2 connections typically have a limit on the number of [maximum concurrent streams (active HTTP requests)](https://http2.github.io/http2-spec/#rfc.section.5.1.2) on a connection at one time.</span></span> <span data-ttu-id="9a49a-125">Für die meisten Server liegt eine Standardbeschränkung auf 100 gleichzeitige Streams vor.</span><span class="sxs-lookup"><span data-stu-id="9a49a-125">By default, most servers set this limit to 100 concurrent streams.</span></span>

<span data-ttu-id="9a49a-126">Ein gRPC-Kanal verwendet eine einzige HTTP/2-Verbindung, und parallele Aufrufe werden per Multiplexing für diese Verbindung ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="9a49a-126">A gRPC channel uses a single HTTP/2 connection, and concurrent calls are multiplexed on that connection.</span></span> <span data-ttu-id="9a49a-127">Wenn die Anzahl aktiver Aufrufe die Grenze für den Verbindungsstream erreicht, werden zusätzliche Aufrufe im Client in eine Warteschlange eingereiht.</span><span class="sxs-lookup"><span data-stu-id="9a49a-127">When the number of active calls reaches the connection stream limit, additional calls are queued in the client.</span></span> <span data-ttu-id="9a49a-128">Aufrufe in der Warteschlange warten auf den Abschluss aktiver Aufrufe, bevor sie übermittelt werden.</span><span class="sxs-lookup"><span data-stu-id="9a49a-128">Queued calls wait for active calls to complete before they are sent.</span></span> <span data-ttu-id="9a49a-129">Bei Anwendungen mit hoher Auslastung oder zeitintensiven gRPC-Streamingaufrufen können Leistungsprobleme auftreten, die von Aufrufen ausgelöst werden, die aufgrund dieser Einschränkungen in die Warteschlange eingereiht werden.</span><span class="sxs-lookup"><span data-stu-id="9a49a-129">Applications with high load, or long running streaming gRPC calls, could see performance issues caused by calls queuing because of this limit.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="9a49a-130">Mit .NET 5 wurde die `SocketsHttpHandler.EnableMultipleHttp2Connections`-Eigenschaft eingeführt.</span><span class="sxs-lookup"><span data-stu-id="9a49a-130">.NET 5 introduces the `SocketsHttpHandler.EnableMultipleHttp2Connections` property.</span></span> <span data-ttu-id="9a49a-131">Wenn `true` festgelegt wird, werden zusätzliche HTTP/2-Verbindungen von einem Kanal erstellt, wenn die maximale Anzahl gleichzeitiger Streams erreicht wird.</span><span class="sxs-lookup"><span data-stu-id="9a49a-131">When set to `true`, additional HTTP/2 connections are created by a channel when the concurrent stream limit is reached.</span></span> <span data-ttu-id="9a49a-132">Wenn ein `GrpcChannel` erstellt wird, wird der interne `SocketsHttpHandler` automatisch konfiguriert, um zusätzliche HTTP/2-Verbindungen herzustellen.</span><span class="sxs-lookup"><span data-stu-id="9a49a-132">When a `GrpcChannel` is created its internal `SocketsHttpHandler` is automatically configured to create additional HTTP/2 connections.</span></span> <span data-ttu-id="9a49a-133">Wenn eine App seinen eigenen Handler konfiguriert, sollten Sie `EnableMultipleHttp2Connections` auf `true` festlegen:</span><span class="sxs-lookup"><span data-stu-id="9a49a-133">If an app configures its own handler, consider setting `EnableMultipleHttp2Connections` to `true`:</span></span>

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

<span data-ttu-id="9a49a-134">Es gibt einige Problemumgehungen für .NET Core 3.1-Apps:</span><span class="sxs-lookup"><span data-stu-id="9a49a-134">There are a couple of workarounds for .NET Core 3.1 apps:</span></span>

* <span data-ttu-id="9a49a-135">Erstellen Sie separate gRPC-Kanäle für Bereiche der App, die eine hohe Auslastung aufweisen.</span><span class="sxs-lookup"><span data-stu-id="9a49a-135">Create separate gRPC channels for areas of the app with high load.</span></span> <span data-ttu-id="9a49a-136">Beispielsweise weist der gRPC-Dienst `Logger` möglicherweise eine hohe Auslastung auf.</span><span class="sxs-lookup"><span data-stu-id="9a49a-136">For example, the `Logger` gRPC service might have a high load.</span></span> <span data-ttu-id="9a49a-137">Verwenden Sie einen separaten Kanal, um `LoggerClient` in der App zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="9a49a-137">Use a separate channel to create the `LoggerClient` in the app.</span></span>
* <span data-ttu-id="9a49a-138">Verwenden Sie einen Pool von gRPC-Kanälen, um beispielsweise eine Liste von gRPC-Kanälen zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="9a49a-138">Use a pool of gRPC channels, for example,  create a list of gRPC channels.</span></span> <span data-ttu-id="9a49a-139">`Random` wird jedes Mal für die Auswahl eines Kanals aus der Liste verwendet, wenn ein gRPC-Kanal benötigt wird.</span><span class="sxs-lookup"><span data-stu-id="9a49a-139">`Random` is used to pick a channel from the list each time a gRPC channel is needed.</span></span> <span data-ttu-id="9a49a-140">Bei Verwendung von `Random` werden Aufrufe zufällig auf mehrere Verbindungen verteilt.</span><span class="sxs-lookup"><span data-stu-id="9a49a-140">Using `Random` randomly distributes calls over multiple connections.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="9a49a-141">Das Erhöhen der Grenze für die maximale Anzahl gleichzeitiger Streams für den Server ist eine weitere Möglichkeit, das Problem zu lösen.</span><span class="sxs-lookup"><span data-stu-id="9a49a-141">Increasing the maximum concurrent stream limit on the server is another way to solve this problem.</span></span> <span data-ttu-id="9a49a-142">Dies wird in Kestrel mit <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Http2Limits.MaxStreamsPerConnection> konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="9a49a-142">In Kestrel this is configured with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Http2Limits.MaxStreamsPerConnection>.</span></span>
>
> <span data-ttu-id="9a49a-143">Von einer Erhöhung der maximalen Anzahl gleichzeitiger Streams wird abgeraten.</span><span class="sxs-lookup"><span data-stu-id="9a49a-143">Increasing the maximum concurrent stream limit is not recommended.</span></span> <span data-ttu-id="9a49a-144">Bei zu vielen Streams über eine einzelne HTTP/2-Verbindung treten neue Leistungsprobleme auf:</span><span class="sxs-lookup"><span data-stu-id="9a49a-144">Too many streams on a single HTTP/2 connection introduces new performance issues:</span></span>
>
> * <span data-ttu-id="9a49a-145">Threadkonflikte zwischen Streams, die versuchen in die Verbindung zu schreiben</span><span class="sxs-lookup"><span data-stu-id="9a49a-145">Thread contention between streams trying to write to the connection.</span></span>
> * <span data-ttu-id="9a49a-146">Verlust von Verbindungspaketen, der dazu führt, dass alle Aufrufe auf der TCP-Ebene blockiert werden</span><span class="sxs-lookup"><span data-stu-id="9a49a-146">Connection packet loss causes all calls to be blocked at the TCP layer.</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="keep-alive-pings"></a><span data-ttu-id="9a49a-147">Keep-Alive-Pings</span><span class="sxs-lookup"><span data-stu-id="9a49a-147">Keep alive pings</span></span>

<span data-ttu-id="9a49a-148">Keep-Alive-Pings können dazu verwendet werden, HTTP/2-Verbindungen während Inaktivität aufrechtzuerhalten.</span><span class="sxs-lookup"><span data-stu-id="9a49a-148">Keep alive pings can be used to keep HTTP/2 connections alive during periods of inactivity.</span></span> <span data-ttu-id="9a49a-149">Wenn Sie über eine bereite HTTP/2-Verbindung verfügen, wenn eine App ihre Aktivitäten fortsetzt, werden schnelle anfängliche gRPC-Aufrufe ermöglicht, ohne dass Verzögerungen aufgrund der Wiederherstellung der Verbindung auftreten.</span><span class="sxs-lookup"><span data-stu-id="9a49a-149">Having an existing HTTP/2 connection ready when an app resumes activity allows for the initial gRPC calls to be made quickly, without a delay caused by the connection being reestablished.</span></span>

<span data-ttu-id="9a49a-150">Keep-Alive-Pings werden mit <xref:System.Net.Http.SocketsHttpHandler> konfiguriert:</span><span class="sxs-lookup"><span data-stu-id="9a49a-150">Keep alive pings are configured on <xref:System.Net.Http.SocketsHttpHandler>:</span></span>

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

<span data-ttu-id="9a49a-151">Im obigen Code wird ein Kanal konfiguriert, der alle 60 Sekunden während Inaktivität einen Keep-Alive-Ping an den Server sendet.</span><span class="sxs-lookup"><span data-stu-id="9a49a-151">The preceding code configures a channel that sends a keep alive ping to the server every 60 seconds during periods of inactivity.</span></span> <span data-ttu-id="9a49a-152">Durch diesen Ping wird sichergestellt, dass der Server und jegliche verwendeten Proxys die Verbindung nicht aufgrund der Inaktivität beenden.</span><span class="sxs-lookup"><span data-stu-id="9a49a-152">The ping ensures the server and any proxies in use won't close the connection because of inactivity.</span></span>

::: moniker-end

## <a name="streaming"></a><span data-ttu-id="9a49a-153">Streaming</span><span class="sxs-lookup"><span data-stu-id="9a49a-153">Streaming</span></span>

<span data-ttu-id="9a49a-154">Das bidirektionale gRPC-Streaming kann dazu verwendet werden, unäre gRPC-Aufrufe in Hochleistungsszenarios zu ersetzen.</span><span class="sxs-lookup"><span data-stu-id="9a49a-154">gRPC bidirectional streaming can be used to replace unary gRPC calls in high-performance scenarios.</span></span> <span data-ttu-id="9a49a-155">Sobald ein bidirektionaler Stream gestartet wurde, ist das Streaming von Nachrichten hin und zurück schneller als das Senden von Nachrichten mit mehreren unären gRPC-Aufrufen.</span><span class="sxs-lookup"><span data-stu-id="9a49a-155">Once a bidirectional stream has started, streaming messages back and forth is faster than sending messages with multiple unary gRPC calls.</span></span> <span data-ttu-id="9a49a-156">Streamnachrichten werden als Daten einer vorhandenen HTTP/2-Anforderung übermittelt und umgehen den Mehraufwand, der durch das Erstellen einer neuen HTTP/2-Anforderung für jeden unären Aufruf entsteht.</span><span class="sxs-lookup"><span data-stu-id="9a49a-156">Streamed messages are sent as data on an existing HTTP/2 request and eliminates the overhead of creating a new HTTP/2 request for each unary call.</span></span>

<span data-ttu-id="9a49a-157">Beispieldienst:</span><span class="sxs-lookup"><span data-stu-id="9a49a-157">Example service:</span></span>

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

<span data-ttu-id="9a49a-158">Beispielclient:</span><span class="sxs-lookup"><span data-stu-id="9a49a-158">Example client:</span></span>

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

<span data-ttu-id="9a49a-159">Das Ersetzen unärer Aufrufe durch bidirektionales Streaming aus Leistungsgründen ist eine fortgeschrittene Vorgehensweise und eignet sich in vielen Situationen nicht.</span><span class="sxs-lookup"><span data-stu-id="9a49a-159">Replacing unary calls with bidirectional streaming for performance reasons is an advanced technique and is not appropriate in many situations.</span></span>

<span data-ttu-id="9a49a-160">In folgenden Fällen eignet sich die Verwendung von Streamingaufrufen:</span><span class="sxs-lookup"><span data-stu-id="9a49a-160">Using streaming calls is a good choice when:</span></span>

1. <span data-ttu-id="9a49a-161">Es ist hoher Durchsatz oder niedrige Latenz erforderlich.</span><span class="sxs-lookup"><span data-stu-id="9a49a-161">High throughput or low latency is required.</span></span>
2. <span data-ttu-id="9a49a-162">gRPC und HTTP/2 wurden als Leistungsengpass identifiziert.</span><span class="sxs-lookup"><span data-stu-id="9a49a-162">gRPC and HTTP/2 are identified as a performance bottleneck.</span></span>
3. <span data-ttu-id="9a49a-163">Ein Worker im Client sendet oder empfängt regelmäßig Nachrichten mit einem gRPC-Dienst.</span><span class="sxs-lookup"><span data-stu-id="9a49a-163">A worker in the client is sending or receiving regular messages with a gRPC service.</span></span>

<span data-ttu-id="9a49a-164">Achten Sie auf die zusätzliche Komplexität und Einschränkungen der Verwendung von Streamingaufrufen anstelle von unären Aufrufen:</span><span class="sxs-lookup"><span data-stu-id="9a49a-164">Be aware of the additional complexity and limitations of using streaming calls instead of unary:</span></span>

1. <span data-ttu-id="9a49a-165">Ein Stream kann von einem Dienst oder Verbindungsfehler unterbrochen werden.</span><span class="sxs-lookup"><span data-stu-id="9a49a-165">A stream can be interrupted by a service or connection error.</span></span> <span data-ttu-id="9a49a-166">Es ist Logik zum Neustarten des Streams erforderlich, wenn ein Fehler auftritt.</span><span class="sxs-lookup"><span data-stu-id="9a49a-166">Logic is required to restart stream if there is an error.</span></span>
2. <span data-ttu-id="9a49a-167">`RequestStream.WriteAsync` ist für Multithreading nicht sicher.</span><span class="sxs-lookup"><span data-stu-id="9a49a-167">`RequestStream.WriteAsync` is not safe for multi-threading.</span></span> <span data-ttu-id="9a49a-168">Es kann immer nur eine Nachricht in einem Stream geschrieben werden.</span><span class="sxs-lookup"><span data-stu-id="9a49a-168">Only one message can be written to a stream at a time.</span></span> <span data-ttu-id="9a49a-169">Das Senden von Nachrichten aus mehreren Threads über einen einzelnen Stream erfordert eine Ersteller-/Verbraucherwarteschlange wie <xref:System.Threading.Channels.Channel%601> zum Marshallen von Nachrichten.</span><span class="sxs-lookup"><span data-stu-id="9a49a-169">Sending messages from multiple threads over a single stream requires a producer/consumer queue like <xref:System.Threading.Channels.Channel%601> to marshall messages.</span></span>
3. <span data-ttu-id="9a49a-170">Eine gRPC-Streamingmethode ist darauf beschränkt, eine Art von Nachricht zu empfangen und eine Art von Nachricht zu senden.</span><span class="sxs-lookup"><span data-stu-id="9a49a-170">A gRPC streaming method is limited to receiving one type of message and sending one type of message.</span></span> <span data-ttu-id="9a49a-171">`rpc StreamingCall(stream RequestMessage) returns (stream ResponseMessage)` empfängt beispielsweise `RequestMessage` und sendet `ResponseMessage`.</span><span class="sxs-lookup"><span data-stu-id="9a49a-171">For example, `rpc StreamingCall(stream RequestMessage) returns (stream ResponseMessage)` receives `RequestMessage` and sends `ResponseMessage`.</span></span> <span data-ttu-id="9a49a-172">Mithilfe der Protobuf-Unterstützung unbekannter oder bedingter Nachrichten mit `Any` und `oneof` kann diese Einschränkung umgangen werden.</span><span class="sxs-lookup"><span data-stu-id="9a49a-172">Protobuf's support for unknown or conditional messages using `Any` and `oneof` can work around this limitation.</span></span>
