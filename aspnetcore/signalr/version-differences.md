---
title: 'Unterschiede zwischen SignalR und ASP.net Core SignalR'
author: bradygaster
description: 'Unterschiede zwischen SignalR und ASP.net Core SignalR'
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.date: 11/21/2019
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: signalr/version-differences
ms.openlocfilehash: c4c0ff83cb789e9aa35085496daa461404615726
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061209"
---
# <a name="differences-between-aspnet-no-locsignalr-and-aspnet-core-no-locsignalr"></a><span data-ttu-id="59913-103">Unterschiede zwischen ASP.net SignalR und ASP.net Core SignalR</span><span class="sxs-lookup"><span data-stu-id="59913-103">Differences between ASP.NET SignalR and ASP.NET Core SignalR</span></span>

<span data-ttu-id="59913-104">ASP.net Core SignalR ist nicht mit Clients oder Servern für ASP.NET kompatibel SignalR .</span><span class="sxs-lookup"><span data-stu-id="59913-104">ASP.NET Core SignalR isn't compatible with clients or servers for ASP.NET SignalR.</span></span> <span data-ttu-id="59913-105">In diesem Artikel werden die Features erläutert, die in ASP.net Core entfernt oder geändert wurden SignalR .</span><span class="sxs-lookup"><span data-stu-id="59913-105">This article details features which have been removed or changed in ASP.NET Core SignalR.</span></span>

## <a name="how-to-identify-the-no-locsignalr-version"></a><span data-ttu-id="59913-106">Identifizieren der SignalR Version</span><span class="sxs-lookup"><span data-stu-id="59913-106">How to identify the SignalR version</span></span>

::: moniker range=">= aspnetcore-3.0"

|                      | <span data-ttu-id="59913-107">ASP.NET SignalR</span><span class="sxs-lookup"><span data-stu-id="59913-107">ASP.NET SignalR</span></span> | <span data-ttu-id="59913-108">ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="59913-108">ASP.NET Core SignalR</span></span> |
| -------------------- | --------------- | -------------------- |
| <span data-ttu-id="59913-109">**Server-nuget-Paket**</span><span class="sxs-lookup"><span data-stu-id="59913-109">**Server NuGet package**</span></span> | <span data-ttu-id="59913-110">[Microsoft. Aspnet.SignalR](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/)</span><span class="sxs-lookup"><span data-stu-id="59913-110">[Microsoft.AspNet.SignalR](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/)</span></span> | <span data-ttu-id="59913-111">Keine</span><span class="sxs-lookup"><span data-stu-id="59913-111">None.</span></span> <span data-ttu-id="59913-112">Im gemeinsamen [Microsoft. aspnetcore. app](xref:fundamentals/metapackage-app) -Framework enthalten.</span><span class="sxs-lookup"><span data-stu-id="59913-112">Included in the [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) shared framework.</span></span> |
| <span data-ttu-id="59913-113">**Client-nuget-Pakete**</span><span class="sxs-lookup"><span data-stu-id="59913-113">**Client NuGet packages**</span></span> | <span data-ttu-id="59913-114">[Microsoft. Aspnet. SignalR . Ent](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.Client/)</span><span class="sxs-lookup"><span data-stu-id="59913-114">[Microsoft.AspNet.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.Client/)</span></span><br><span data-ttu-id="59913-115">[Microsoft. Aspnet. SignalR . JS](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.JS/)</span><span class="sxs-lookup"><span data-stu-id="59913-115">[Microsoft.AspNet.SignalR.JS](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.JS/)</span></span> | <span data-ttu-id="59913-116">[Microsoft. aspnetcore. SignalR . Ent](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/)</span><span class="sxs-lookup"><span data-stu-id="59913-116">[Microsoft.AspNetCore.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/)</span></span> |
| <span data-ttu-id="59913-117">**NPM-Paket für JavaScript-Client**</span><span class="sxs-lookup"><span data-stu-id="59913-117">**JavaScript client npm package**</span></span> | [<span data-ttu-id="59913-118">signalr</span><span class="sxs-lookup"><span data-stu-id="59913-118">signalr</span></span>](https://www.npmjs.com/package/signalr) | [`@microsoft/signalr`](https://www.npmjs.com/package/@microsoft/signalr) |
| <span data-ttu-id="59913-119">**Java-Client**</span><span class="sxs-lookup"><span data-stu-id="59913-119">**Java client**</span></span> | <span data-ttu-id="59913-120">[GitHub-Repository](https://github.com/SignalR/java-client) (veraltet)</span><span class="sxs-lookup"><span data-stu-id="59913-120">[GitHub Repository](https://github.com/SignalR/java-client) (deprecated)</span></span>  | <span data-ttu-id="59913-121">Maven-Paket [com. Microsoft. signalr](https://search.maven.org/artifact/com.microsoft.signalr/signalr)</span><span class="sxs-lookup"><span data-stu-id="59913-121">Maven package [com.microsoft.signalr](https://search.maven.org/artifact/com.microsoft.signalr/signalr)</span></span> |
| <span data-ttu-id="59913-122">**Server-App-Typ**</span><span class="sxs-lookup"><span data-stu-id="59913-122">**Server app type**</span></span> | <span data-ttu-id="59913-123">ASP.net (System. Web) oder owin-Self-Host</span><span class="sxs-lookup"><span data-stu-id="59913-123">ASP.NET (System.Web) or OWIN Self-Host</span></span> | <span data-ttu-id="59913-124">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="59913-124">ASP.NET Core</span></span> |
| <span data-ttu-id="59913-125">**Unterstützte Serverplattformen**</span><span class="sxs-lookup"><span data-stu-id="59913-125">**Supported server platforms**</span></span> | <span data-ttu-id="59913-126">.NET Framework 4,5 oder höher</span><span class="sxs-lookup"><span data-stu-id="59913-126">.NET Framework 4.5 or later</span></span> | <span data-ttu-id="59913-127">.Net Core 3,0 oder höher</span><span class="sxs-lookup"><span data-stu-id="59913-127">.NET Core 3.0 or later</span></span> |

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

|                      | <span data-ttu-id="59913-128">ASP.NET SignalR</span><span class="sxs-lookup"><span data-stu-id="59913-128">ASP.NET SignalR</span></span> | <span data-ttu-id="59913-129">ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="59913-129">ASP.NET Core SignalR</span></span> |
| -------------------- | --------------- | -------------------- |
| <span data-ttu-id="59913-130">**Server-nuget-Paket**</span><span class="sxs-lookup"><span data-stu-id="59913-130">**Server NuGet package**</span></span> | <span data-ttu-id="59913-131">[Microsoft. Aspnet.SignalR](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/)</span><span class="sxs-lookup"><span data-stu-id="59913-131">[Microsoft.AspNet.SignalR](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/)</span></span> | <span data-ttu-id="59913-132">[Microsoft. aspnetcore. app](https://www.nuget.org/packages/Microsoft.AspNetCore.App/) (.net Core)</span><span class="sxs-lookup"><span data-stu-id="59913-132">[Microsoft.AspNetCore.App](https://www.nuget.org/packages/Microsoft.AspNetCore.App/) (.NET Core)</span></span><br><span data-ttu-id="59913-133">[Microsoft. aspnetcore.SignalR](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR/)</span><span class="sxs-lookup"><span data-stu-id="59913-133">[Microsoft.AspNetCore.SignalR](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR/)</span></span> <span data-ttu-id="59913-134">(.NET Framework)</span><span class="sxs-lookup"><span data-stu-id="59913-134">(.NET Framework)</span></span> |
| <span data-ttu-id="59913-135">**Client-nuget-Pakete**</span><span class="sxs-lookup"><span data-stu-id="59913-135">**Client NuGet packages**</span></span> | <span data-ttu-id="59913-136">[Microsoft. Aspnet. SignalR . Ent](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.Client/)</span><span class="sxs-lookup"><span data-stu-id="59913-136">[Microsoft.AspNet.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.Client/)</span></span><br><span data-ttu-id="59913-137">[Microsoft. Aspnet. SignalR . JS](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.JS/)</span><span class="sxs-lookup"><span data-stu-id="59913-137">[Microsoft.AspNet.SignalR.JS](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.JS/)</span></span> | <span data-ttu-id="59913-138">[Microsoft. aspnetcore. SignalR . Ent](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/)</span><span class="sxs-lookup"><span data-stu-id="59913-138">[Microsoft.AspNetCore.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/)</span></span> |
| <span data-ttu-id="59913-139">**NPM-Paket für JavaScript-Client**</span><span class="sxs-lookup"><span data-stu-id="59913-139">**JavaScript client npm package**</span></span> | [<span data-ttu-id="59913-140">signalr</span><span class="sxs-lookup"><span data-stu-id="59913-140">signalr</span></span>](https://www.npmjs.com/package/signalr) | [`@aspnet/signalr`](https://www.npmjs.com/package/@aspnet/signalr) |
| <span data-ttu-id="59913-141">**Java-Client**</span><span class="sxs-lookup"><span data-stu-id="59913-141">**Java client**</span></span> | <span data-ttu-id="59913-142">[GitHub-Repository](https://github.com/SignalR/java-client) (veraltet)</span><span class="sxs-lookup"><span data-stu-id="59913-142">[GitHub Repository](https://github.com/SignalR/java-client) (deprecated)</span></span>  | <span data-ttu-id="59913-143">Maven-Paket [com. Microsoft. signalr](https://search.maven.org/artifact/com.microsoft.signalr/signalr)</span><span class="sxs-lookup"><span data-stu-id="59913-143">Maven package [com.microsoft.signalr](https://search.maven.org/artifact/com.microsoft.signalr/signalr)</span></span> |
| <span data-ttu-id="59913-144">**Server-App-Typ**</span><span class="sxs-lookup"><span data-stu-id="59913-144">**Server app type**</span></span> | <span data-ttu-id="59913-145">ASP.net (System. Web) oder owin-Self-Host</span><span class="sxs-lookup"><span data-stu-id="59913-145">ASP.NET (System.Web) or OWIN Self-Host</span></span> | <span data-ttu-id="59913-146">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="59913-146">ASP.NET Core</span></span> |
| <span data-ttu-id="59913-147">**Unterstützte Serverplattformen**</span><span class="sxs-lookup"><span data-stu-id="59913-147">**Supported server platforms**</span></span> | <span data-ttu-id="59913-148">.NET Framework 4,5 oder höher</span><span class="sxs-lookup"><span data-stu-id="59913-148">.NET Framework 4.5 or later</span></span> | <span data-ttu-id="59913-149">.NET Framework 4.6.1 oder höher</span><span class="sxs-lookup"><span data-stu-id="59913-149">.NET Framework 4.6.1 or later</span></span><br><span data-ttu-id="59913-150">.Net Core 2,1 oder höher</span><span class="sxs-lookup"><span data-stu-id="59913-150">.NET Core 2.1 or later</span></span> |

::: moniker-end

## <a name="feature-differences"></a><span data-ttu-id="59913-151">Featureunterschiede</span><span class="sxs-lookup"><span data-stu-id="59913-151">Feature differences</span></span>

### <a name="automatic-reconnects"></a><span data-ttu-id="59913-152">Automatische erneute Verbindungen</span><span class="sxs-lookup"><span data-stu-id="59913-152">Automatic reconnects</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="59913-153">In ASP.net SignalR :</span><span class="sxs-lookup"><span data-stu-id="59913-153">In ASP.NET SignalR:</span></span>

* <span data-ttu-id="59913-154">Standardmäßig versucht, erneut eine Verbindung SignalR mit dem Server herzustellen, wenn die Verbindung getrennt wird.</span><span class="sxs-lookup"><span data-stu-id="59913-154">By default, SignalR attempts to reconnect to the server if the connection is dropped.</span></span> 

<span data-ttu-id="59913-155">In ASP.net Core SignalR :</span><span class="sxs-lookup"><span data-stu-id="59913-155">In ASP.NET Core SignalR:</span></span>

* <span data-ttu-id="59913-156">Automatische erneute Verbindungen sind sowohl für den [.NET-Client](xref:signalr/dotnet-client#automatically-reconnect) als auch den [JavaScript-Client](xref:signalr/javascript-client#automatically-reconnect)aktiviert:</span><span class="sxs-lookup"><span data-stu-id="59913-156">Automatic reconnects are opt-in with both the [.NET client](xref:signalr/dotnet-client#automatically-reconnect) and the [JavaScript client](xref:signalr/javascript-client#automatically-reconnect):</span></span>

```csharp
HubConnection connection = new HubConnectionBuilder()
    .WithUrl(new Uri("http://127.0.0.1:5000/chathub"))
    .WithAutomaticReconnect()
    .Build();
```

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withAutomaticReconnect()
    .build();
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="59913-157">Vor ASP.net Core 3,0 SignalR unterstützt keine automatischen erneuten Verbindungen.</span><span class="sxs-lookup"><span data-stu-id="59913-157">Prior to ASP.NET Core 3.0, SignalR doesn't support automatic reconnects.</span></span> <span data-ttu-id="59913-158">Wenn der Client getrennt wird, muss der Benutzer explizit eine neue Verbindung starten, um die Verbindung wiederherzustellen.</span><span class="sxs-lookup"><span data-stu-id="59913-158">If the client is disconnected, the user must explicitly start a new connection to reconnect.</span></span> <span data-ttu-id="59913-159">In ASP.net SignalR versucht, erneut eine Verbindung SignalR mit dem Server herzustellen, wenn die Verbindung getrennt wird.</span><span class="sxs-lookup"><span data-stu-id="59913-159">In ASP.NET SignalR, SignalR attempts to reconnect to the server if the connection is dropped.</span></span>

::: moniker-end

### <a name="protocol-support"></a><span data-ttu-id="59913-160">Protokollunterstützung</span><span class="sxs-lookup"><span data-stu-id="59913-160">Protocol support</span></span>

<span data-ttu-id="59913-161">ASP.net Core SignalR unterstützt JSON und ein neues binäres Protokoll, das auf [messagepack](xref:signalr/messagepackhubprotocol)basiert.</span><span class="sxs-lookup"><span data-stu-id="59913-161">ASP.NET Core SignalR supports JSON, as well as a new binary protocol based on [MessagePack](xref:signalr/messagepackhubprotocol).</span></span> <span data-ttu-id="59913-162">Außerdem können benutzerdefinierte Protokolle erstellt werden.</span><span class="sxs-lookup"><span data-stu-id="59913-162">Additionally, custom protocols can be created.</span></span>

### <a name="transports"></a><span data-ttu-id="59913-163">Transportprotokolle</span><span class="sxs-lookup"><span data-stu-id="59913-163">Transports</span></span>

<span data-ttu-id="59913-164">Der unveränderte Frame Transport wird in ASP.net Core nicht unterstützt SignalR .</span><span class="sxs-lookup"><span data-stu-id="59913-164">The Forever Frame transport isn't supported in ASP.NET Core SignalR.</span></span>

## <a name="differences-on-the-server"></a><span data-ttu-id="59913-165">Unterschiede auf dem Server</span><span class="sxs-lookup"><span data-stu-id="59913-165">Differences on the server</span></span>

<span data-ttu-id="59913-166">Die ASP.net Core SignalR serverseitigen Bibliotheken sind in [Microsoft. aspnetcore. app](xref:fundamentals/metapackage-app)enthalten, das in der **ASP.net Core-Webanwendungs** Vorlage für Razor und MVC-Projekte verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="59913-166">The ASP.NET Core SignalR server-side libraries are included in [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app), which is used in the **ASP.NET Core Web Application** template for both Razor and MVC projects.</span></span>

<span data-ttu-id="59913-167">ASP.net Core SignalR ist eine ASP.net Core Middleware.</span><span class="sxs-lookup"><span data-stu-id="59913-167">ASP.NET Core SignalR is an ASP.NET Core middleware.</span></span> <span data-ttu-id="59913-168">Sie muss konfiguriert werden, indem Sie <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddSignalR%2A> in Aufrufen `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="59913-168">It must be configured by calling <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddSignalR%2A> in `Startup.ConfigureServices`.</span></span>

```csharp
services.AddSignalR()
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="59913-169">Zum Konfigurieren des Routings ordnen Sie Routen zu Hubs innerhalb des <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> Methoden Aufrufes in der- `Startup.Configure` Methode zu.</span><span class="sxs-lookup"><span data-stu-id="59913-169">To configure routing, map routes to hubs inside the <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> method call in the `Startup.Configure` method.</span></span>

```csharp
app.UseRouting();

app.UseEndpoints(endpoints =>
{
    endpoints.MapHub<ChatHub>("/hub");
});
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="59913-170">Zum Konfigurieren des Routings ordnen Sie Routen zu Hubs innerhalb des <xref:Microsoft.AspNetCore.Builder.SignalRAppBuilderExtensions.UseSignalR%2A> Methoden Aufrufes in der- `Startup.Configure` Methode zu.</span><span class="sxs-lookup"><span data-stu-id="59913-170">To configure routing, map routes to hubs inside the <xref:Microsoft.AspNetCore.Builder.SignalRAppBuilderExtensions.UseSignalR%2A> method call in the `Startup.Configure` method.</span></span>

```csharp
app.UseSignalR(routes =>
{
    routes.MapHub<ChatHub>("/hub");
});
```

::: moniker-end

### <a name="sticky-sessions"></a><span data-ttu-id="59913-171">Persistente Sitzungen</span><span class="sxs-lookup"><span data-stu-id="59913-171">Sticky sessions</span></span>

<span data-ttu-id="59913-172">Das horizontale Skalierungs Modell für ASP.net SignalR ermöglicht Clients das erneute Verbinden und Senden von Nachrichten an einen beliebigen Server in der Farm.</span><span class="sxs-lookup"><span data-stu-id="59913-172">The scaleout model for ASP.NET SignalR allows clients to reconnect and send messages to any server in the farm.</span></span> <span data-ttu-id="59913-173">In ASP.net Core SignalR muss der Client für die Dauer der Verbindung mit dem gleichen Server interagieren.</span><span class="sxs-lookup"><span data-stu-id="59913-173">In ASP.NET Core SignalR, the client must interact with the same server for the duration of the connection.</span></span> <span data-ttu-id="59913-174">Für horizontales Skalieren mit redis bedeutet das, dass persistente Sitzungen erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="59913-174">For scaleout using Redis, that means sticky sessions are required.</span></span> <span data-ttu-id="59913-175">Für horizontales Skalieren mit dem [Azure- SignalR Dienst](/azure/azure-signalr/)sind keine persistenten Sitzungen erforderlich, da der Dienst Verbindungen mit Clients verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="59913-175">For scaleout using [Azure SignalR Service](/azure/azure-signalr/), sticky sessions aren't required because the service handles connections to clients.</span></span>

### <a name="single-hub-per-connection"></a><span data-ttu-id="59913-176">Einzelner Hub pro Verbindung</span><span class="sxs-lookup"><span data-stu-id="59913-176">Single hub per connection</span></span>

<span data-ttu-id="59913-177">In ASP.net Core wurde SignalR das Verbindungs Modell vereinfacht.</span><span class="sxs-lookup"><span data-stu-id="59913-177">In ASP.NET Core SignalR, the connection model has been simplified.</span></span> <span data-ttu-id="59913-178">Verbindungen werden direkt mit einem einzelnen Hub hergestellt, anstatt mit einer einzelnen Verbindung, die für die gemeinsame Nutzung des Zugriffs auf mehrere Hubs verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="59913-178">Connections are made directly to a single hub, rather than a single connection being used to share access to multiple hubs.</span></span>

### <a name="streaming"></a><span data-ttu-id="59913-179">Streaming</span><span class="sxs-lookup"><span data-stu-id="59913-179">Streaming</span></span>

<span data-ttu-id="59913-180">ASP.net Core SignalR unterstützt jetzt das [Streamen von Daten](xref:signalr/streaming) vom Hub an den Client.</span><span class="sxs-lookup"><span data-stu-id="59913-180">ASP.NET Core SignalR now supports [streaming data](xref:signalr/streaming) from the hub to the client.</span></span>

### <a name="state"></a><span data-ttu-id="59913-181">State</span><span class="sxs-lookup"><span data-stu-id="59913-181">State</span></span>

<span data-ttu-id="59913-182">Die Möglichkeit, den beliebigen Zustand zwischen Clients und dem Hub (häufig genannt `HubState` ) zu übergeben, wurde entfernt sowie Unterstützung für Fortschrittsmeldungen.</span><span class="sxs-lookup"><span data-stu-id="59913-182">The ability to pass arbitrary state between clients and the hub (often called `HubState`) has been removed, as well as support for progress messages.</span></span> <span data-ttu-id="59913-183">Zurzeit gibt es keine Entsprechung von Hub-Proxys.</span><span class="sxs-lookup"><span data-stu-id="59913-183">There is no counterpart of hub proxies at the moment.</span></span>

### <a name="persistentconnection-removal"></a><span data-ttu-id="59913-184">Entfernen von persistentconnection</span><span class="sxs-lookup"><span data-stu-id="59913-184">PersistentConnection removal</span></span>

<span data-ttu-id="59913-185">In ASP.net Core wurde SignalR die [persistentconnection](/previous-versions/aspnet/jj919047(v=vs.118)) -Klasse entfernt.</span><span class="sxs-lookup"><span data-stu-id="59913-185">In ASP.NET Core SignalR, the [PersistentConnection](/previous-versions/aspnet/jj919047(v=vs.118)) class has been removed.</span></span>

### <a name="globalhost"></a><span data-ttu-id="59913-186">Global Host</span><span class="sxs-lookup"><span data-stu-id="59913-186">GlobalHost</span></span>

<span data-ttu-id="59913-187">ASP.net Core ist eine Abhängigkeitsinjektion (di) in das Framework integriert.</span><span class="sxs-lookup"><span data-stu-id="59913-187">ASP.NET Core has dependency injection (DI) built into the framework.</span></span> <span data-ttu-id="59913-188">Dienste können mit di auf den [hubcontext](xref:signalr/hubcontext)zugreifen.</span><span class="sxs-lookup"><span data-stu-id="59913-188">Services can use DI to access the [HubContext](xref:signalr/hubcontext).</span></span> <span data-ttu-id="59913-189">Das `GlobalHost` Objekt, das in ASP.NET verwendet wird SignalR , um ein-Objekt zu erhalten, ist `HubContext` nicht in ASP.net Core vorhanden SignalR .</span><span class="sxs-lookup"><span data-stu-id="59913-189">The `GlobalHost` object that is used in ASP.NET SignalR to get a `HubContext` doesn't exist in ASP.NET Core SignalR.</span></span>

### <a name="hubpipeline"></a><span data-ttu-id="59913-190">Hubpipeline</span><span class="sxs-lookup"><span data-stu-id="59913-190">HubPipeline</span></span>

<span data-ttu-id="59913-191">ASP.net Core bietet SignalR keine Unterstützung für `HubPipeline` Module.</span><span class="sxs-lookup"><span data-stu-id="59913-191">ASP.NET Core SignalR doesn't have support for `HubPipeline` modules.</span></span>

## <a name="differences-on-the-client"></a><span data-ttu-id="59913-192">Unterschiede auf dem Client</span><span class="sxs-lookup"><span data-stu-id="59913-192">Differences on the client</span></span>

### <a name="typescript"></a><span data-ttu-id="59913-193">TypeScript</span><span class="sxs-lookup"><span data-stu-id="59913-193">TypeScript</span></span>

<span data-ttu-id="59913-194">Der ASP.net Core- SignalR Client wird in [typescript](https://www.typescriptlang.org/)geschrieben.</span><span class="sxs-lookup"><span data-stu-id="59913-194">The ASP.NET Core SignalR client is written in [TypeScript](https://www.typescriptlang.org/).</span></span> <span data-ttu-id="59913-195">Sie können in JavaScript oder typescript schreiben, wenn Sie den [JavaScript-Client](xref:signalr/javascript-client)verwenden.</span><span class="sxs-lookup"><span data-stu-id="59913-195">You can write in JavaScript or TypeScript when using the [JavaScript client](xref:signalr/javascript-client).</span></span>

### <a name="the-javascript-client-is-hosted-at-npm"></a><span data-ttu-id="59913-196">Der JavaScript-Client wird auf NPM gehostet.</span><span class="sxs-lookup"><span data-stu-id="59913-196">The JavaScript client is hosted at npm</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="59913-197">In ASP.NET-Versionen wurde der JavaScript-Client über ein nuget-Paket in Visual Studio abgerufen.</span><span class="sxs-lookup"><span data-stu-id="59913-197">In ASP.NET versions, the JavaScript client was obtained through a NuGet package in Visual Studio.</span></span> <span data-ttu-id="59913-198">In den ASP.net Core Versionen enthält das [`@microsoft/signalr`](https://www.npmjs.com/package/@microsoft/signalr) NPM-Paket die JavaScript-Bibliotheken.</span><span class="sxs-lookup"><span data-stu-id="59913-198">In the ASP.NET Core versions, the [`@microsoft/signalr`](https://www.npmjs.com/package/@microsoft/signalr) npm package contains the JavaScript libraries.</span></span> <span data-ttu-id="59913-199">Dieses Paket ist nicht in der **ASP.net Core-Webanwendungs** Vorlage enthalten.</span><span class="sxs-lookup"><span data-stu-id="59913-199">This package isn't included in the **ASP.NET Core Web Application** template.</span></span> <span data-ttu-id="59913-200">Verwenden Sie NPM, um das NPM-Paket abzurufen und zu installieren `@microsoft/signalr` .</span><span class="sxs-lookup"><span data-stu-id="59913-200">Use npm to obtain and install the `@microsoft/signalr` npm package.</span></span>

```console
npm init -y
npm install @microsoft/signalr
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="59913-201">In ASP.NET-Versionen wurde der JavaScript-Client über ein nuget-Paket in Visual Studio abgerufen.</span><span class="sxs-lookup"><span data-stu-id="59913-201">In ASP.NET versions, the JavaScript client was obtained through a NuGet package in Visual Studio.</span></span> <span data-ttu-id="59913-202">In den ASP.net Core Versionen enthält das [`@aspnet/signalr`](https://www.npmjs.com/package/@aspnet/signalr) NPM-Paket die JavaScript-Bibliotheken.</span><span class="sxs-lookup"><span data-stu-id="59913-202">In the ASP.NET Core versions, the [`@aspnet/signalr`](https://www.npmjs.com/package/@aspnet/signalr) npm package contains the JavaScript libraries.</span></span> <span data-ttu-id="59913-203">Dieses Paket ist nicht in der **ASP.net Core-Webanwendungs** Vorlage enthalten.</span><span class="sxs-lookup"><span data-stu-id="59913-203">This package isn't included in the **ASP.NET Core Web Application** template.</span></span> <span data-ttu-id="59913-204">Verwenden Sie NPM, um das NPM-Paket abzurufen und zu installieren `@aspnet/signalr` .</span><span class="sxs-lookup"><span data-stu-id="59913-204">Use npm to obtain and install the `@aspnet/signalr` npm package.</span></span>

```console
npm init -y
npm install @aspnet/signalr
```

::: moniker-end

### <a name="jquery"></a><span data-ttu-id="59913-205">jQuery</span><span class="sxs-lookup"><span data-stu-id="59913-205">jQuery</span></span>

<span data-ttu-id="59913-206">Die Abhängigkeit von jQuery wurde entfernt, aber Projekte können weiterhin jQuery verwenden.</span><span class="sxs-lookup"><span data-stu-id="59913-206">The dependency on jQuery has been removed, however projects can still use jQuery.</span></span>

### <a name="internet-explorer-support"></a><span data-ttu-id="59913-207">Internet Explorer-Unterstützung</span><span class="sxs-lookup"><span data-stu-id="59913-207">Internet Explorer support</span></span>

<span data-ttu-id="59913-208">ASP.net Core SignalR unterstützt Microsoft Internet Explorer 11 oder höher, wohingegen ASP.net SignalR Microsoft Internet Explorer 8 oder höher unterstützt.</span><span class="sxs-lookup"><span data-stu-id="59913-208">ASP.NET Core SignalR supports Microsoft Internet Explorer 11 or later, whereas ASP.NET SignalR supports Microsoft Internet Explorer 8 or later.</span></span>
<span data-ttu-id="59913-209">Weitere Informationen zur Browserunterstützung finden Sie unter [Unterstützte Plattformen](xref:signalr/supported-platforms#javascript-client).</span><span class="sxs-lookup"><span data-stu-id="59913-209">More info on browser support can be found at [supported platforms](xref:signalr/supported-platforms#javascript-client).</span></span>

### <a name="javascript-client-method-syntax"></a><span data-ttu-id="59913-210">Syntax der JavaScript-Client Methode</span><span class="sxs-lookup"><span data-stu-id="59913-210">JavaScript client method syntax</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="59913-211">Die JavaScript-Syntax wurde von der ASP.NET-Version von geändert SignalR .</span><span class="sxs-lookup"><span data-stu-id="59913-211">The JavaScript syntax has changed from the ASP.NET version of SignalR.</span></span> <span data-ttu-id="59913-212">Anstatt das-Objekt zu verwenden `$connection` , erstellen Sie mithilfe der [hubconnectionbuilder](/javascript/api/@aspnet/signalr/hubconnectionbuilder) -API eine Verbindung.</span><span class="sxs-lookup"><span data-stu-id="59913-212">Rather than using the `$connection` object, create a connection using the [HubConnectionBuilder](/javascript/api/@aspnet/signalr/hubconnectionbuilder) API.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/hub")
    .build();
```

<span data-ttu-id="59913-213">Verwenden Sie die [on](/javascript/api/@microsoft/signalr/HubConnection#on) -Methode, um Client Methoden anzugeben, die der Hub abrufen kann.</span><span class="sxs-lookup"><span data-stu-id="59913-213">Use the [on](/javascript/api/@microsoft/signalr/HubConnection#on) method to specify client methods that the hub can call.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="59913-214">Die JavaScript-Syntax wurde von der ASP.NET-Version von geändert SignalR .</span><span class="sxs-lookup"><span data-stu-id="59913-214">The JavaScript syntax has changed from the ASP.NET version of SignalR.</span></span> <span data-ttu-id="59913-215">Anstatt das-Objekt zu verwenden `$connection` , erstellen Sie mithilfe der [hubconnectionbuilder](/javascript/api/@microsoft/signalr/hubconnectionbuilder) -API eine Verbindung.</span><span class="sxs-lookup"><span data-stu-id="59913-215">Rather than using the `$connection` object, create a connection using the [HubConnectionBuilder](/javascript/api/@microsoft/signalr/hubconnectionbuilder) API.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/hub")
    .build();
```

<span data-ttu-id="59913-216">Verwenden Sie die [on](/javascript/api/@aspnet/signalr/HubConnection#on) -Methode, um Client Methoden anzugeben, die der Hub abrufen kann.</span><span class="sxs-lookup"><span data-stu-id="59913-216">Use the [on](/javascript/api/@aspnet/signalr/HubConnection#on) method to specify client methods that the hub can call.</span></span>

::: moniker-end

```javascript
connection.on("ReceiveMessage", (user, message) => {
    const msg = message.replace(/&/g, "&amp;").replace(/</g, "&lt;").replace(/>/g, "&gt;");
    const encodedMsg = `${user} says ${msg}`;
    console.log(encodedMsg);
});
```

<span data-ttu-id="59913-217">Nachdem Sie die Client Methode erstellt haben, starten Sie die Hub-Verbindung.</span><span class="sxs-lookup"><span data-stu-id="59913-217">After creating the client method, start the hub connection.</span></span> <span data-ttu-id="59913-218">Verketten einer [catch](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise/catch) -Methode zum Protokollieren oder behandeln von Fehlern.</span><span class="sxs-lookup"><span data-stu-id="59913-218">Chain a [catch](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise/catch) method to log or handle errors.</span></span>

```javascript
connection.start().catch(err => console.error(err));
```

### <a name="hub-proxies"></a><span data-ttu-id="59913-219">Knotenproxys</span><span class="sxs-lookup"><span data-stu-id="59913-219">Hub proxies</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="59913-220">Hub-Proxys werden nicht mehr automatisch generiert.</span><span class="sxs-lookup"><span data-stu-id="59913-220">Hub proxies are no longer automatically generated.</span></span> <span data-ttu-id="59913-221">Stattdessen wird der Methodenname als Zeichenfolge an die [Aufruf](/javascript/api/@microsoft/signalr/hubconnection#invoke) -API übermittelt.</span><span class="sxs-lookup"><span data-stu-id="59913-221">Instead, the method name is passed into the [invoke](/javascript/api/@microsoft/signalr/hubconnection#invoke) API as a string.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="59913-222">Hub-Proxys werden nicht mehr automatisch generiert.</span><span class="sxs-lookup"><span data-stu-id="59913-222">Hub proxies are no longer automatically generated.</span></span> <span data-ttu-id="59913-223">Stattdessen wird der Methodenname als Zeichenfolge an die [Aufruf](/javascript/api/@aspnet/signalr/hubconnection#invoke) -API übermittelt.</span><span class="sxs-lookup"><span data-stu-id="59913-223">Instead, the method name is passed into the [invoke](/javascript/api/@aspnet/signalr/hubconnection#invoke) API as a string.</span></span>

::: moniker-end

### <a name="net-and-other-clients"></a><span data-ttu-id="59913-224">.Net und andere Clients</span><span class="sxs-lookup"><span data-stu-id="59913-224">.NET and other clients</span></span>

<span data-ttu-id="59913-225">[Microsoft. aspnetcore. SignalR . ](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client)Das nuget-Paket des Clients enthält die .NET-Client Bibliotheken für ASP.net Core SignalR .</span><span class="sxs-lookup"><span data-stu-id="59913-225">The [Microsoft.AspNetCore.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) NuGet package contains the .NET client libraries for ASP.NET Core SignalR.</span></span>

<span data-ttu-id="59913-226">Verwenden <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder> Sie, um eine Instanz einer Verbindung mit einem Hub zu erstellen und zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="59913-226">Use the <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder> to create and build an instance of a connection to a hub.</span></span>

```csharp
connection = new HubConnectionBuilder()
    .WithUrl("url")
    .Build();
```

## <a name="scaleout-differences"></a><span data-ttu-id="59913-227">Unterschiede bei horizontaler Skalierung</span><span class="sxs-lookup"><span data-stu-id="59913-227">Scaleout differences</span></span>

<span data-ttu-id="59913-228">ASP.net SignalR unterstützt SQL Server und redis.</span><span class="sxs-lookup"><span data-stu-id="59913-228">ASP.NET SignalR supports SQL Server and Redis.</span></span> <span data-ttu-id="59913-229">ASP.net Core SignalR unterstützt den Azure SignalR -Dienst und redis.</span><span class="sxs-lookup"><span data-stu-id="59913-229">ASP.NET Core SignalR supports Azure SignalR Service and Redis.</span></span>

### <a name="aspnet"></a><span data-ttu-id="59913-230">ASP.NET</span><span class="sxs-lookup"><span data-stu-id="59913-230">ASP.NET</span></span>

* [<span data-ttu-id="59913-231">SignalR horizontales hochskalieren mit Azure Service Bus</span><span class="sxs-lookup"><span data-stu-id="59913-231">SignalR scaleout with Azure Service Bus</span></span>](/aspnet/signalr/overview/performance/scaleout-with-windows-azure-service-bus)
* [<span data-ttu-id="59913-232">SignalR horizontales hochskalieren mit redis</span><span class="sxs-lookup"><span data-stu-id="59913-232">SignalR scaleout with Redis</span></span>](/aspnet/signalr/overview/performance/scaleout-with-redis)
* [<span data-ttu-id="59913-233">SignalR horizontales hochskalieren mit SQL Server</span><span class="sxs-lookup"><span data-stu-id="59913-233">SignalR scaleout with SQL Server</span></span>](/aspnet/signalr/overview/performance/scaleout-with-sql-server)

### <a name="aspnet-core"></a><span data-ttu-id="59913-234">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="59913-234">ASP.NET Core</span></span>

* [<span data-ttu-id="59913-235">Azure- SignalR Dienst</span><span class="sxs-lookup"><span data-stu-id="59913-235">Azure SignalR Service</span></span>](/azure/azure-signalr/)
* [<span data-ttu-id="59913-236">Redis-Backplane</span><span class="sxs-lookup"><span data-stu-id="59913-236">Redis Backplane</span></span>](xref:signalr/redis-backplane)

## <a name="additional-resources"></a><span data-ttu-id="59913-237">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="59913-237">Additional resources</span></span>

* [<span data-ttu-id="59913-238">Hubs</span><span class="sxs-lookup"><span data-stu-id="59913-238">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="59913-239">JavaScript-Client</span><span class="sxs-lookup"><span data-stu-id="59913-239">JavaScript client</span></span>](xref:signalr/javascript-client)
* [<span data-ttu-id="59913-240">.NET-Client</span><span class="sxs-lookup"><span data-stu-id="59913-240">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="59913-241">Unterstützte Plattformen</span><span class="sxs-lookup"><span data-stu-id="59913-241">Supported platforms</span></span>](xref:signalr/supported-platforms)
