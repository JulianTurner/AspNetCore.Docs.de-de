---
title: Bewährte Methoden für Leistung mit gRPC
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
ms.openlocfilehash: a0a1a6901e07fb0074ca403870378f267d3d4403
ms.sourcegitcommit: c9b03d8a6a4dcc59e4aacb30a691f349235a74c8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/02/2020
ms.locfileid: "89379444"
---
# <a name="performance-best-practices-with-grpc"></a><span data-ttu-id="648fc-103">Bewährte Methoden für Leistung mit gRPC</span><span class="sxs-lookup"><span data-stu-id="648fc-103">Performance best practices with gRPC</span></span>

<span data-ttu-id="648fc-104">Von [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="648fc-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="648fc-105">gRPC ist für leistungsstarke Dienste konzipiert.</span><span class="sxs-lookup"><span data-stu-id="648fc-105">gRPC is designed for high-performance services.</span></span> <span data-ttu-id="648fc-106">In dieser Dokumentation wird erläutert, wie Sie die bestmögliche Leistung von gRPC erzielen.</span><span class="sxs-lookup"><span data-stu-id="648fc-106">This document explains how to get the best performance possible from gRPC.</span></span>

## <a name="reuse-grpc-channels"></a><span data-ttu-id="648fc-107">Wiederverwenden von gRPC-Kanälen</span><span class="sxs-lookup"><span data-stu-id="648fc-107">Reuse gRPC channels</span></span>

<span data-ttu-id="648fc-108">Ein gRPC-Kanal sollte für gRPC-Aufrufe wiederverwendet werden.</span><span class="sxs-lookup"><span data-stu-id="648fc-108">A gRPC channel should be reused when making gRPC calls.</span></span> <span data-ttu-id="648fc-109">Durch die Wiederverwendung eines Kanals können Aufrufe für Multiplexing über eine vorhandene HTTP/2-Verbindung verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="648fc-109">Reusing a channel allows calls to be multiplexed through an existing HTTP/2 connection.</span></span>

<span data-ttu-id="648fc-110">Wenn ein neuer Kanal für jeden gRPC-Aufruf erstellt wird, kann dies deutlich mehr Zeit beanspruchen.</span><span class="sxs-lookup"><span data-stu-id="648fc-110">If a new channel is created for each gRPC call then the amount of time it takes to complete can increase significantly.</span></span> <span data-ttu-id="648fc-111">Jeder Aufruf erfordert mehrere Netzwerkroundtrips zwischen dem Client und dem Server, um eine neue HTTP/2-Verbindung herzustellen:</span><span class="sxs-lookup"><span data-stu-id="648fc-111">Each call will require multiple network round-trips between the client and the server to create a new HTTP/2 connection:</span></span>

1. <span data-ttu-id="648fc-112">Öffnen eines Sockets</span><span class="sxs-lookup"><span data-stu-id="648fc-112">Opening a socket</span></span>
2. <span data-ttu-id="648fc-113">Herstellen der TCP-Verbindung</span><span class="sxs-lookup"><span data-stu-id="648fc-113">Establishing TCP connection</span></span>
3. <span data-ttu-id="648fc-114">TLS-Aushandlung</span><span class="sxs-lookup"><span data-stu-id="648fc-114">Negotiating TLS</span></span>
4. <span data-ttu-id="648fc-115">Starten der HTTP/2-Verbindung</span><span class="sxs-lookup"><span data-stu-id="648fc-115">Starting HTTP/2 connection</span></span>
5. <span data-ttu-id="648fc-116">Ausführen des gRPC-Aufrufs</span><span class="sxs-lookup"><span data-stu-id="648fc-116">Making the gRPC call</span></span>

<span data-ttu-id="648fc-117">Kanäle können problemlos für mehrere gRPC-Aufrufe gemeinsam genutzt und wiederverwendet werden:</span><span class="sxs-lookup"><span data-stu-id="648fc-117">Channels are safe to share and reuse between gRPC calls:</span></span>

* <span data-ttu-id="648fc-118">gRPC-Clients werden mit Kanälen erstellt.</span><span class="sxs-lookup"><span data-stu-id="648fc-118">gRPC clients are created with channels.</span></span> <span data-ttu-id="648fc-119">Bei gRPC-Clients handelt es sich um Lightweightobjekte. Sie müssen nicht zwischengespeichert oder wiederverwendet werden.</span><span class="sxs-lookup"><span data-stu-id="648fc-119">gRPC clients are lightweight objects and don't need to be cached or reused.</span></span>
* <span data-ttu-id="648fc-120">Aus einem Kanal können mehrere gRPC-Clients erstellt werden, einschließlich verschiedener Clienttypen.</span><span class="sxs-lookup"><span data-stu-id="648fc-120">Multiple gRPC clients can be created from a channel, including different types of clients.</span></span>
* <span data-ttu-id="648fc-121">Ein Kanal und Clients, die aus dem Kanal erstellt wurden, können sicher von mehreren Threads verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="648fc-121">A channel and clients created from the channel can safely be used by multiple threads.</span></span>
* <span data-ttu-id="648fc-122">Clients, die aus dem Kanal erstellt wurden, können mehrere gleichzeitige Aufrufe durchführen.</span><span class="sxs-lookup"><span data-stu-id="648fc-122">Clients created from the channel can make multiple simultaneous calls.</span></span>

<span data-ttu-id="648fc-123">Die gRPC-Clientfactory bietet eine zentralisierte Möglichkeit zum Konfigurieren von Kanälen.</span><span class="sxs-lookup"><span data-stu-id="648fc-123">gRPC client factory offers a centralized way to configure channels.</span></span> <span data-ttu-id="648fc-124">Zugrunde liegende Kanäle werden automatisch wiederverwendet.</span><span class="sxs-lookup"><span data-stu-id="648fc-124">It automatically reuses underlying channels.</span></span> <span data-ttu-id="648fc-125">Weitere Informationen finden Sie unter <xref:grpc/clientfactory>.</span><span class="sxs-lookup"><span data-stu-id="648fc-125">For more information, see <xref:grpc/clientfactory>.</span></span>

## <a name="connection-concurrency"></a><span data-ttu-id="648fc-126">Verbindungsparallelität</span><span class="sxs-lookup"><span data-stu-id="648fc-126">Connection concurrency</span></span>

<span data-ttu-id="648fc-127">HTTP/2-Verbindungen weisen in der Regel eine Einschränkung für die [maximale Anzahl gleichzeitiger Streams (aktive HTTP-Anforderungen)](https://http2.github.io/http2-spec/#rfc.section.5.1.2) für eine Verbindung auf.</span><span class="sxs-lookup"><span data-stu-id="648fc-127">HTTP/2 connections typically have a limit on the number of [maximum concurrent streams (active HTTP requests)](https://http2.github.io/http2-spec/#rfc.section.5.1.2) on a connection at one time.</span></span> <span data-ttu-id="648fc-128">Für die meisten Server liegt eine Standardbeschränkung auf 100 gleichzeitige Streams vor.</span><span class="sxs-lookup"><span data-stu-id="648fc-128">By default, most servers set this limit to 100 concurrent streams.</span></span>

<span data-ttu-id="648fc-129">Ein gRPC-Kanal verwendet eine einzige HTTP/2-Verbindung, und parallele Aufrufe werden per Multiplexing für diese Verbindung ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="648fc-129">A gRPC channel uses a single HTTP/2 connection, and concurrent calls are multiplexed on that connection.</span></span> <span data-ttu-id="648fc-130">Wenn die Anzahl aktiver Aufrufe die Grenze für den Verbindungsstream erreicht, werden zusätzliche Aufrufe im Client in eine Warteschlange eingereiht.</span><span class="sxs-lookup"><span data-stu-id="648fc-130">When the number of active calls reaches the connection stream limit, additional calls are queued in the client.</span></span> <span data-ttu-id="648fc-131">Aufrufe in der Warteschlange warten auf den Abschluss aktiver Aufrufe, bevor sie übermittelt werden.</span><span class="sxs-lookup"><span data-stu-id="648fc-131">Queued calls wait for active calls to complete before they are sent.</span></span> <span data-ttu-id="648fc-132">Bei Anwendungen mit hoher Auslastung oder zeitintensiven gRPC-Streamingaufrufen können Leistungsprobleme auftreten, die von Aufrufen ausgelöst werden, die aufgrund dieser Einschränkungen in die Warteschlange eingereiht werden.</span><span class="sxs-lookup"><span data-stu-id="648fc-132">Applications with high load, or long running streaming gRPC calls, could see performance issues caused by calls queuing because of this limit.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="648fc-133">Mit .NET 5 wurde die `SocketsHttpHandler.EnableMultipleHttp2Connections`-Eigenschaft eingeführt.</span><span class="sxs-lookup"><span data-stu-id="648fc-133">.NET 5 introduces the `SocketsHttpHandler.EnableMultipleHttp2Connections` property.</span></span> <span data-ttu-id="648fc-134">Wenn `true` festgelegt wird, werden zusätzliche HTTP/2-Verbindungen von einem Kanal erstellt, wenn die maximale Anzahl gleichzeitiger Streams erreicht wird.</span><span class="sxs-lookup"><span data-stu-id="648fc-134">When set to `true`, additional HTTP/2 connections are created by a channel when the concurrent stream limit is reached.</span></span> <span data-ttu-id="648fc-135">Wenn ein `GrpcChannel` erstellt wird, wird der interne `SocketsHttpHandler` automatisch konfiguriert, um zusätzliche HTTP/2-Verbindungen herzustellen.</span><span class="sxs-lookup"><span data-stu-id="648fc-135">When a `GrpcChannel` is created its internal `SocketsHttpHandler` is automatically configured to create additional HTTP/2 connections.</span></span> <span data-ttu-id="648fc-136">Wenn eine App seinen eigenen Handler konfiguriert, sollten Sie `EnableMultipleHttp2Connections` auf `true` festlegen:</span><span class="sxs-lookup"><span data-stu-id="648fc-136">If an app configures its own handler, consider setting `EnableMultipleHttp2Connections` to `true`:</span></span>

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

<span data-ttu-id="648fc-137">Es gibt einige Problemumgehungen für .NET Core 3.1-Apps:</span><span class="sxs-lookup"><span data-stu-id="648fc-137">There are a couple of workarounds for .NET Core 3.1 apps:</span></span>

* <span data-ttu-id="648fc-138">Erstellen Sie separate gRPC-Kanäle für Bereiche der App, die eine hohe Auslastung aufweisen.</span><span class="sxs-lookup"><span data-stu-id="648fc-138">Create separate gRPC channels for areas of the app with high load.</span></span> <span data-ttu-id="648fc-139">Beispielsweise weist der gRPC-Dienst `Logger` möglicherweise eine hohe Auslastung auf.</span><span class="sxs-lookup"><span data-stu-id="648fc-139">For example, the `Logger` gRPC service might have a high load.</span></span> <span data-ttu-id="648fc-140">Verwenden Sie einen separaten Kanal, um `LoggerClient` in der App zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="648fc-140">Use a separate channel to create the `LoggerClient` in the app.</span></span>
* <span data-ttu-id="648fc-141">Verwenden Sie einen Pool von gRPC-Kanälen, um beispielsweise eine Liste von gRPC-Kanälen zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="648fc-141">Use a pool of gRPC channels, for example,  create a list of gRPC channels.</span></span> <span data-ttu-id="648fc-142">`Random` wird jedes Mal für die Auswahl eines Kanals aus der Liste verwendet, wenn ein gRPC-Kanal benötigt wird.</span><span class="sxs-lookup"><span data-stu-id="648fc-142">`Random` is used to pick a channel from the list each time a gRPC channel is needed.</span></span> <span data-ttu-id="648fc-143">Bei Verwendung von `Random` werden Aufrufe zufällig auf mehrere Verbindungen verteilt.</span><span class="sxs-lookup"><span data-stu-id="648fc-143">Using `Random` randomly distributes calls over multiple connections.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="648fc-144">Das Erhöhen der Grenze für die maximale Anzahl gleichzeitiger Streams für den Server ist eine weitere Möglichkeit, das Problem zu lösen.</span><span class="sxs-lookup"><span data-stu-id="648fc-144">Increasing the maximum concurrent stream limit on the server is another way to solve this problem.</span></span> <span data-ttu-id="648fc-145">Dies wird in Kestrel mit <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Http2Limits.MaxStreamsPerConnection> konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="648fc-145">In Kestrel this is configured with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Http2Limits.MaxStreamsPerConnection>.</span></span>
>
> <span data-ttu-id="648fc-146">Von einer Erhöhung der maximalen Anzahl gleichzeitiger Streams wird abgeraten.</span><span class="sxs-lookup"><span data-stu-id="648fc-146">Increasing the maximum concurrent stream limit is not recommended.</span></span> <span data-ttu-id="648fc-147">Bei zu vielen Streams über eine einzelne HTTP/2-Verbindung treten neue Leistungsprobleme auf:</span><span class="sxs-lookup"><span data-stu-id="648fc-147">Too many streams on a single HTTP/2 connection introduces new performance issues:</span></span>
>
> * <span data-ttu-id="648fc-148">Threadkonflikte zwischen Streams, die versuchen in die Verbindung zu schreiben</span><span class="sxs-lookup"><span data-stu-id="648fc-148">Thread contention between streams trying to write to the connection.</span></span>
> * <span data-ttu-id="648fc-149">Verlust von Verbindungspaketen, der dazu führt, dass alle Aufrufe auf der TCP-Ebene blockiert werden</span><span class="sxs-lookup"><span data-stu-id="648fc-149">Connection packet loss causes all calls to be blocked at the TCP layer.</span></span>

## <a name="load-balancing"></a><span data-ttu-id="648fc-150">Lastenausgleich</span><span class="sxs-lookup"><span data-stu-id="648fc-150">Load balancing</span></span>

<span data-ttu-id="648fc-151">Einige Lastenausgleiche funktionieren mit gRPC nicht effektiv.</span><span class="sxs-lookup"><span data-stu-id="648fc-151">Some load balancers don't work effectively with gRPC.</span></span> <span data-ttu-id="648fc-152">L4-Lastenausgleiche (Transport) agieren auf Verbindungsebene, indem sie TCP-Verbindungen endpunktübergreifend verteilen.</span><span class="sxs-lookup"><span data-stu-id="648fc-152">L4 (transport) load balancers operate at a connection level, by distributing TCP connections across endpoints.</span></span> <span data-ttu-id="648fc-153">Dieser Ansatz eignet sich für den Lastenausgleich von API-Aufrufen mit HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="648fc-153">This approach works well for loading balancing API calls made with HTTP/1.1.</span></span> <span data-ttu-id="648fc-154">Gleichzeitige HTTP/1.1-Aufrufe werden über verschiedene Verbindungen gesendet, sodass der Lastenausgleich für diese endpunktübergreifend erfolgen kann.</span><span class="sxs-lookup"><span data-stu-id="648fc-154">Concurrent calls made with HTTP/1.1 are sent on different connections, allowing calls to be load balanced across endpoints.</span></span>

<span data-ttu-id="648fc-155">Da L4-Lastenausgleiche auf Verbindungsebene agieren, sind sie für gRPC nicht besonders gut geeignet.</span><span class="sxs-lookup"><span data-stu-id="648fc-155">Because L4 load balancers operate at a connection level, they don't work well with gRPC.</span></span> <span data-ttu-id="648fc-156">gRPC verwendet HTTP/2, sodass ein Multiplexing für mehrere Aufrufe über eine einzige TCP-Verbindung erfolgt.</span><span class="sxs-lookup"><span data-stu-id="648fc-156">gRPC uses HTTP/2, which multiplexes multiple calls on a single TCP connection.</span></span> <span data-ttu-id="648fc-157">Alle gRPC-Aufrufe über diese Verbindung werden an einen Endpunkt gesendet.</span><span class="sxs-lookup"><span data-stu-id="648fc-157">All gRPC calls over that connection go to one endpoint.</span></span>

<span data-ttu-id="648fc-158">Es gibt zwei Optionen für den effektiven Lastenausgleich von gRPC:</span><span class="sxs-lookup"><span data-stu-id="648fc-158">There are two options to effectively load balance gRPC:</span></span>

* <span data-ttu-id="648fc-159">Clientseitiger Lastenausgleich</span><span class="sxs-lookup"><span data-stu-id="648fc-159">Client-side load balancing</span></span>
* <span data-ttu-id="648fc-160">L7-Proxylastenausgleich (Anwendung)</span><span class="sxs-lookup"><span data-stu-id="648fc-160">L7 (application) proxy load balancing</span></span>

> [!NOTE]
> <span data-ttu-id="648fc-161">Nur für gRPC-Aufrufe kann ein Lastenausgleich zwischen Endpunkten ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="648fc-161">Only gRPC calls can be load balanced between endpoints.</span></span> <span data-ttu-id="648fc-162">Sobald ein gRPC-Streamingaufruf erstellt wurde, werden alle über den Stream gesendeten Nachrichten an einen Endpunkt weitergeleitet.</span><span class="sxs-lookup"><span data-stu-id="648fc-162">Once a streaming gRPC call is established, all messages sent over the stream go to one endpoint.</span></span>

### <a name="client-side-load-balancing"></a><span data-ttu-id="648fc-163">Clientseitiger Lastenausgleich</span><span class="sxs-lookup"><span data-stu-id="648fc-163">Client-side load balancing</span></span>

<span data-ttu-id="648fc-164">Beim clientseitigen Lastenausgleich kennt der Client die Endpunkte.</span><span class="sxs-lookup"><span data-stu-id="648fc-164">With client-side load balancing, the client knows about endpoints.</span></span> <span data-ttu-id="648fc-165">Für jeden gRPC-Rückruf wählt er einen anderen Endpunkt aus, an den der Aufruf gesendet werden soll.</span><span class="sxs-lookup"><span data-stu-id="648fc-165">For each gRPC call it selects a different endpoint to send the call to.</span></span> <span data-ttu-id="648fc-166">Der clientseitige Lastenausgleich ist eine gute Wahl, wenn die Latenz entscheidend ist.</span><span class="sxs-lookup"><span data-stu-id="648fc-166">Client-side load balancing is a good choice when latency is important.</span></span> <span data-ttu-id="648fc-167">Zwischen dem Client und dem Dienst ist kein Proxy vorhanden, sodass der Aufruf direkt an den Dienst gesendet wird.</span><span class="sxs-lookup"><span data-stu-id="648fc-167">There is no proxy between the client and the service so the call is sent to the service directly.</span></span> <span data-ttu-id="648fc-168">Der Nachteil beim clientseitigen Lastenausgleich besteht darin, dass jeder Client die verfügbaren Endpunkte nachverfolgen muss, die er verwenden soll.</span><span class="sxs-lookup"><span data-stu-id="648fc-168">The downside to client-side load balancing is that each client must keep track of available endpoints it should use.</span></span>

<span data-ttu-id="648fc-169">Der clientseitige Lookaside-Lastenausgleich ist ein Verfahren, bei dem der Lastenausgleichzustand an einem zentralen Ort gespeichert wird.</span><span class="sxs-lookup"><span data-stu-id="648fc-169">Lookaside client load balancing is a technique where load balancing state is stored in a central location.</span></span> <span data-ttu-id="648fc-170">Clients fragen diesen zentralen Ort regelmäßig auf Informationen bezüglich Lastenausgleichsentscheidungen ab.</span><span class="sxs-lookup"><span data-stu-id="648fc-170">Clients periodically query the central location for information to use when making load balancing decisions.</span></span>

<span data-ttu-id="648fc-171">`Grpc.Net.Client` unterstützt derzeit keinen clientseitigen Lastenausgleich.</span><span class="sxs-lookup"><span data-stu-id="648fc-171">`Grpc.Net.Client` currently doesn't support client-side load balancing.</span></span> <span data-ttu-id="648fc-172">[Grpc.Core](https://www.nuget.org/packages/Grpc.Core) ist eine gute Wahl, wenn der clientseitige Lastenausgleich in .NET erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="648fc-172">[Grpc.Core](https://www.nuget.org/packages/Grpc.Core) is a good choice if client-side load balancing is required in .NET.</span></span>

### <a name="proxy-load-balancing"></a><span data-ttu-id="648fc-173">Proxylastenausgleich</span><span class="sxs-lookup"><span data-stu-id="648fc-173">Proxy load balancing</span></span>

<span data-ttu-id="648fc-174">Ein L7-Proxy (Anwendung) agiert auf einer höheren Ebene als ein L4-Proxy (Transport).</span><span class="sxs-lookup"><span data-stu-id="648fc-174">An L7 (application) proxy works at a higher level than an L4 (transport) proxy.</span></span> <span data-ttu-id="648fc-175">L7-Proxys sind mit HTTP/2 kompatibel und können gRPC-Aufrufe per Multiplexing über eine HTTP/2-Verbindung und mehrere Endpunkte an den Proxy verteilen.</span><span class="sxs-lookup"><span data-stu-id="648fc-175">L7 proxies understand HTTP/2, and are able to distribute gRPC calls multiplexed to the proxy on one HTTP/2 connection across multiple endpoints.</span></span> <span data-ttu-id="648fc-176">Die Verwendung eines Proxys ist einfacher als der clientseitige Lastenausgleich, kann jedoch die Latenz für gRPC-Aufrufe erhöhen.</span><span class="sxs-lookup"><span data-stu-id="648fc-176">Using a proxy is simpler than client-side load balancing, but can add extra latency to gRPC calls.</span></span>

<span data-ttu-id="648fc-177">Es gibt viele verfügbare L7-Proxys.</span><span class="sxs-lookup"><span data-stu-id="648fc-177">There are many L7 proxies available.</span></span> <span data-ttu-id="648fc-178">Unter anderem gibt es folgende Optionen:</span><span class="sxs-lookup"><span data-stu-id="648fc-178">Some options are:</span></span>

* <span data-ttu-id="648fc-179">[Envoy](https://www.envoyproxy.io/), ein beliebter Open-Source-Proxy</span><span class="sxs-lookup"><span data-stu-id="648fc-179">[Envoy](https://www.envoyproxy.io/) - A popular open source proxy.</span></span>
* <span data-ttu-id="648fc-180">[Linkerd](https://linkerd.io/), ein Dienstnetz für Kubernetes</span><span class="sxs-lookup"><span data-stu-id="648fc-180">[Linkerd](https://linkerd.io/) - Service mesh for Kubernetes.</span></span>
* <span data-ttu-id="648fc-181">[YARP: A Reverse Proxy](https://microsoft.github.io/reverse-proxy/), ein als Vorschauversion verfügbarer Open-Source-Proxy, der in .NET geschrieben wurde</span><span class="sxs-lookup"><span data-stu-id="648fc-181">[YARP: A Reverse Proxy](https://microsoft.github.io/reverse-proxy/) - A preview open source proxy written in .NET.</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="keep-alive-pings"></a><span data-ttu-id="648fc-182">Keep-Alive-Pings</span><span class="sxs-lookup"><span data-stu-id="648fc-182">Keep alive pings</span></span>

<span data-ttu-id="648fc-183">Keep-Alive-Pings können dazu verwendet werden, HTTP/2-Verbindungen während Inaktivität aufrechtzuerhalten.</span><span class="sxs-lookup"><span data-stu-id="648fc-183">Keep alive pings can be used to keep HTTP/2 connections alive during periods of inactivity.</span></span> <span data-ttu-id="648fc-184">Wenn Sie über eine bereite HTTP/2-Verbindung verfügen, wenn eine App ihre Aktivitäten fortsetzt, werden schnelle anfängliche gRPC-Aufrufe ermöglicht, ohne dass Verzögerungen aufgrund der Wiederherstellung der Verbindung auftreten.</span><span class="sxs-lookup"><span data-stu-id="648fc-184">Having an existing HTTP/2 connection ready when an app resumes activity allows for the initial gRPC calls to be made quickly, without a delay caused by the connection being reestablished.</span></span>

<span data-ttu-id="648fc-185">Keep-Alive-Pings werden mit <xref:System.Net.Http.SocketsHttpHandler> konfiguriert:</span><span class="sxs-lookup"><span data-stu-id="648fc-185">Keep alive pings are configured on <xref:System.Net.Http.SocketsHttpHandler>:</span></span>

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

<span data-ttu-id="648fc-186">Im obigen Code wird ein Kanal konfiguriert, der alle 60 Sekunden während Inaktivität einen Keep-Alive-Ping an den Server sendet.</span><span class="sxs-lookup"><span data-stu-id="648fc-186">The preceding code configures a channel that sends a keep alive ping to the server every 60 seconds during periods of inactivity.</span></span> <span data-ttu-id="648fc-187">Durch diesen Ping wird sichergestellt, dass der Server und jegliche verwendeten Proxys die Verbindung nicht aufgrund der Inaktivität beenden.</span><span class="sxs-lookup"><span data-stu-id="648fc-187">The ping ensures the server and any proxies in use won't close the connection because of inactivity.</span></span>

::: moniker-end

## <a name="streaming"></a><span data-ttu-id="648fc-188">Streaming</span><span class="sxs-lookup"><span data-stu-id="648fc-188">Streaming</span></span>

<span data-ttu-id="648fc-189">Das bidirektionale gRPC-Streaming kann dazu verwendet werden, unäre gRPC-Aufrufe in Hochleistungsszenarios zu ersetzen.</span><span class="sxs-lookup"><span data-stu-id="648fc-189">gRPC bidirectional streaming can be used to replace unary gRPC calls in high-performance scenarios.</span></span> <span data-ttu-id="648fc-190">Sobald ein bidirektionaler Stream gestartet wurde, ist das Streaming von Nachrichten hin und zurück schneller als das Senden von Nachrichten mit mehreren unären gRPC-Aufrufen.</span><span class="sxs-lookup"><span data-stu-id="648fc-190">Once a bidirectional stream has started, streaming messages back and forth is faster than sending messages with multiple unary gRPC calls.</span></span> <span data-ttu-id="648fc-191">Streamnachrichten werden als Daten einer vorhandenen HTTP/2-Anforderung übermittelt und umgehen den Mehraufwand, der durch das Erstellen einer neuen HTTP/2-Anforderung für jeden unären Aufruf entsteht.</span><span class="sxs-lookup"><span data-stu-id="648fc-191">Streamed messages are sent as data on an existing HTTP/2 request and eliminates the overhead of creating a new HTTP/2 request for each unary call.</span></span>

<span data-ttu-id="648fc-192">Beispieldienst:</span><span class="sxs-lookup"><span data-stu-id="648fc-192">Example service:</span></span>

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

<span data-ttu-id="648fc-193">Beispielclient:</span><span class="sxs-lookup"><span data-stu-id="648fc-193">Example client:</span></span>

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

<span data-ttu-id="648fc-194">Das Ersetzen unärer Aufrufe durch bidirektionales Streaming aus Leistungsgründen ist eine fortgeschrittene Vorgehensweise und eignet sich in vielen Situationen nicht.</span><span class="sxs-lookup"><span data-stu-id="648fc-194">Replacing unary calls with bidirectional streaming for performance reasons is an advanced technique and is not appropriate in many situations.</span></span>

<span data-ttu-id="648fc-195">In folgenden Fällen eignet sich die Verwendung von Streamingaufrufen:</span><span class="sxs-lookup"><span data-stu-id="648fc-195">Using streaming calls is a good choice when:</span></span>

1. <span data-ttu-id="648fc-196">Es ist hoher Durchsatz oder niedrige Latenz erforderlich.</span><span class="sxs-lookup"><span data-stu-id="648fc-196">High throughput or low latency is required.</span></span>
2. <span data-ttu-id="648fc-197">gRPC und HTTP/2 wurden als Leistungsengpass identifiziert.</span><span class="sxs-lookup"><span data-stu-id="648fc-197">gRPC and HTTP/2 are identified as a performance bottleneck.</span></span>
3. <span data-ttu-id="648fc-198">Ein Worker im Client sendet oder empfängt regelmäßig Nachrichten mit einem gRPC-Dienst.</span><span class="sxs-lookup"><span data-stu-id="648fc-198">A worker in the client is sending or receiving regular messages with a gRPC service.</span></span>

<span data-ttu-id="648fc-199">Achten Sie auf die zusätzliche Komplexität und Einschränkungen der Verwendung von Streamingaufrufen anstelle von unären Aufrufen:</span><span class="sxs-lookup"><span data-stu-id="648fc-199">Be aware of the additional complexity and limitations of using streaming calls instead of unary:</span></span>

1. <span data-ttu-id="648fc-200">Ein Stream kann von einem Dienst oder Verbindungsfehler unterbrochen werden.</span><span class="sxs-lookup"><span data-stu-id="648fc-200">A stream can be interrupted by a service or connection error.</span></span> <span data-ttu-id="648fc-201">Es ist Logik zum Neustarten des Streams erforderlich, wenn ein Fehler auftritt.</span><span class="sxs-lookup"><span data-stu-id="648fc-201">Logic is required to restart stream if there is an error.</span></span>
2. <span data-ttu-id="648fc-202">`RequestStream.WriteAsync` ist für Multithreading nicht sicher.</span><span class="sxs-lookup"><span data-stu-id="648fc-202">`RequestStream.WriteAsync` is not safe for multi-threading.</span></span> <span data-ttu-id="648fc-203">Es kann immer nur eine Nachricht in einem Stream geschrieben werden.</span><span class="sxs-lookup"><span data-stu-id="648fc-203">Only one message can be written to a stream at a time.</span></span> <span data-ttu-id="648fc-204">Das Senden von Nachrichten aus mehreren Threads über einen einzelnen Stream erfordert eine Ersteller-/Verbraucherwarteschlange wie <xref:System.Threading.Channels.Channel%601> zum Marshallen von Nachrichten.</span><span class="sxs-lookup"><span data-stu-id="648fc-204">Sending messages from multiple threads over a single stream requires a producer/consumer queue like <xref:System.Threading.Channels.Channel%601> to marshall messages.</span></span>
3. <span data-ttu-id="648fc-205">Eine gRPC-Streamingmethode ist darauf beschränkt, eine Art von Nachricht zu empfangen und eine Art von Nachricht zu senden.</span><span class="sxs-lookup"><span data-stu-id="648fc-205">A gRPC streaming method is limited to receiving one type of message and sending one type of message.</span></span> <span data-ttu-id="648fc-206">`rpc StreamingCall(stream RequestMessage) returns (stream ResponseMessage)` empfängt beispielsweise `RequestMessage` und sendet `ResponseMessage`.</span><span class="sxs-lookup"><span data-stu-id="648fc-206">For example, `rpc StreamingCall(stream RequestMessage) returns (stream ResponseMessage)` receives `RequestMessage` and sends `ResponseMessage`.</span></span> <span data-ttu-id="648fc-207">Mithilfe der Protobuf-Unterstützung unbekannter oder bedingter Nachrichten mit `Any` und `oneof` kann diese Einschränkung umgangen werden.</span><span class="sxs-lookup"><span data-stu-id="648fc-207">Protobuf's support for unknown or conditional messages using `Any` and `oneof` can work around this limitation.</span></span>
