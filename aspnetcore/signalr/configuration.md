---
title: SignalR-Konfiguration in ASP.NET Core
author: bradygaster
description: Erfahren Sie, wie Sie ASP.net Core- SignalR apps konfigurieren.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/12/2020
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
uid: signalr/configuration
ms.openlocfilehash: cef05b32731e0930d7a3cfc6fe4502236b07a236
ms.sourcegitcommit: b64c44ba5e3abb4ad4d50de93b7e282bf0f251e4
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/07/2021
ms.locfileid: "97972066"
---
# <a name="aspnet-core-no-locsignalr-configuration"></a><span data-ttu-id="a6907-103">SignalR-Konfiguration in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a6907-103">ASP.NET Core SignalR configuration</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="a6907-104">JSON/messagepack-Serialisierungsoptionen</span><span class="sxs-lookup"><span data-stu-id="a6907-104">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="a6907-105">ASP.net Core SignalR unterstützt zwei Protokolle zum Codieren von Nachrichten: [JSON](https://www.json.org/) und [messagepack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="a6907-105">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="a6907-106">Jedes Protokoll verfügt über Konfigurationsoptionen für die Serialisierung.</span><span class="sxs-lookup"><span data-stu-id="a6907-106">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="a6907-107">Die JSON-Serialisierung kann mithilfe der [addjsonprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) -Erweiterungsmethode auf dem Server konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="a6907-107">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="a6907-108">`AddJsonProtocol`kann nach [Add SignalR ](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in hinzugefügt werden `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="a6907-108">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="a6907-109">Die- `AddJsonProtocol` Methode nimmt einen Delegaten an, der ein- `options` Objekt empfängt.</span><span class="sxs-lookup"><span data-stu-id="a6907-109">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="a6907-110">Die [payloadserializeroptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) -Eigenschaft für dieses Objekt ist ein- `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> Objekt, das zum Konfigurieren der Serialisierung von Argumenten und Rückgabe Werten verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="a6907-110">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="a6907-111">Weitere Informationen finden Sie in den [System.Text.Jszur Dokumentation](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="a6907-111">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="a6907-112">Verwenden Sie beispielsweise den folgenden Code in, um das Serialisierungsprogramm so zu konfigurieren, dass die Groß-/Kleinschreibung von Eigenschaftsnamen nicht geändert wird. verwenden Sie dazu den folgenden Code in `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="a6907-112">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="a6907-113">Im .NET-Client ist dieselbe `AddJsonProtocol` Erweiterungsmethode auf [hubconnectionbuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)vorhanden.</span><span class="sxs-lookup"><span data-stu-id="a6907-113">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="a6907-114">Der `Microsoft.Extensions.DependencyInjection` Namespace muss importiert werden, um die Erweiterungsmethode aufzulösen:</span><span class="sxs-lookup"><span data-stu-id="a6907-114">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;

// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    })
    .Build();
```

> [!NOTE]
> <span data-ttu-id="a6907-115">Zurzeit ist es nicht möglich, JSON-Serialisierung im JavaScript-Client zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="a6907-115">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="a6907-116">Zum Newtonsoft.Jswechseln</span><span class="sxs-lookup"><span data-stu-id="a6907-116">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="a6907-117">Wenn Sie Features von benötigen `Newtonsoft.Json` , die in nicht unterstützt `System.Text.Json` werden, finden Sie weitere Informationen unter [Wechseln zu Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="a6907-117">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="a6907-118">Messagepack-Serialisierungsoptionen</span><span class="sxs-lookup"><span data-stu-id="a6907-118">MessagePack serialization options</span></span>

<span data-ttu-id="a6907-119">Die messagepack-Serialisierung kann konfiguriert werden, indem ein Delegat für den [addmessagepackprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) -Aufruf bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="a6907-119">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="a6907-120">Weitere Informationen finden Sie [unter SignalR messagepack in](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="a6907-120">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="a6907-121">Zurzeit ist es nicht möglich, die messagepack-Serialisierung im JavaScript-Client zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="a6907-121">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="a6907-122">Konfigurieren von Serveroptionen</span><span class="sxs-lookup"><span data-stu-id="a6907-122">Configure server options</span></span>

<span data-ttu-id="a6907-123">In der folgenden Tabelle werden die Optionen zum Konfigurieren von SignalR Hubs beschrieben:</span><span class="sxs-lookup"><span data-stu-id="a6907-123">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="a6907-124">Option</span><span class="sxs-lookup"><span data-stu-id="a6907-124">Option</span></span> | <span data-ttu-id="a6907-125">Standardwert</span><span class="sxs-lookup"><span data-stu-id="a6907-125">Default Value</span></span> | <span data-ttu-id="a6907-126">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="a6907-126">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="a6907-127">30 Sekunden</span><span class="sxs-lookup"><span data-stu-id="a6907-127">30 seconds</span></span> | <span data-ttu-id="a6907-128">Der Server wird vom-Client als getrennt betrachtet, wenn in diesem Intervall keine Nachricht (einschließlich Keep-Alive) empfangen wurde.</span><span class="sxs-lookup"><span data-stu-id="a6907-128">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="a6907-129">Es kann länger dauern, bis der Client als getrennt gekennzeichnet ist, weil dies implementiert ist.</span><span class="sxs-lookup"><span data-stu-id="a6907-129">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="a6907-130">Der empfohlene Wert ist Double- `KeepAliveInterval` Wert.</span><span class="sxs-lookup"><span data-stu-id="a6907-130">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="a6907-131">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="a6907-131">15 seconds</span></span> | <span data-ttu-id="a6907-132">Wenn der Client innerhalb dieses Zeitraums keine anfängliche Hand Shake Nachricht sendet, wird die Verbindung geschlossen.</span><span class="sxs-lookup"><span data-stu-id="a6907-132">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="a6907-133">Dies ist eine erweiterte Einstellung, die nur geändert werden sollte, wenn aufgrund schwerer Netzwerk Latenz Fehler aufgrund von Hand Shake Timeout auftreten.</span><span class="sxs-lookup"><span data-stu-id="a6907-133">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="a6907-134">Weitere Details zum Hand Shake Prozess finden Sie in der [ SignalR Hub-Protokollspezifikation](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="a6907-134">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="a6907-135">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="a6907-135">15 seconds</span></span> | <span data-ttu-id="a6907-136">Wenn der Server innerhalb dieses Intervalls keine Nachricht gesendet hat, wird automatisch eine Ping-Nachricht gesendet, um die Verbindung offen zu halten.</span><span class="sxs-lookup"><span data-stu-id="a6907-136">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="a6907-137">Ändern Sie `KeepAliveInterval` die `ServerTimeout` / `serverTimeoutInMilliseconds` Einstellung auf dem Client, wenn Sie sich ändern.</span><span class="sxs-lookup"><span data-stu-id="a6907-137">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="a6907-138">Der empfohlene `ServerTimeout` / `serverTimeoutInMilliseconds` Wert ist Double- `KeepAliveInterval` Wert.</span><span class="sxs-lookup"><span data-stu-id="a6907-138">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="a6907-139">Alle installierten Protokolle</span><span class="sxs-lookup"><span data-stu-id="a6907-139">All installed protocols</span></span> | <span data-ttu-id="a6907-140">Protokolle, die von diesem Hub unterstützt werden.</span><span class="sxs-lookup"><span data-stu-id="a6907-140">Protocols supported by this hub.</span></span> <span data-ttu-id="a6907-141">Standardmäßig sind alle auf dem Server registrierten Protokolle zulässig, aber Protokolle können aus dieser Liste entfernt werden, um bestimmte Protokolle für einzelne Hubs zu deaktivieren.</span><span class="sxs-lookup"><span data-stu-id="a6907-141">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="a6907-142">Wenn der Wert `true` ist, werden ausführliche Ausnahme Meldungen an Clients zurückgegeben, wenn eine Ausnahme in einer Hub-Methode ausgelöst wird.</span><span class="sxs-lookup"><span data-stu-id="a6907-142">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="a6907-143">Der Standardwert ist `false` , da diese Ausnahme Meldungen vertrauliche Informationen enthalten können.</span><span class="sxs-lookup"><span data-stu-id="a6907-143">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="a6907-144">Die maximale Anzahl von Elementen, die für Client-uploadstreams gepuffert werden können.</span><span class="sxs-lookup"><span data-stu-id="a6907-144">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="a6907-145">Wenn dieser Grenzwert erreicht wird, wird die Verarbeitung von Aufrufen blockiert, bis der Server streamingelemente verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="a6907-145">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="a6907-146">32 KB</span><span class="sxs-lookup"><span data-stu-id="a6907-146">32 KB</span></span> | <span data-ttu-id="a6907-147">Maximale Größe einer einzelnen eingehenden Hub-Nachricht.</span><span class="sxs-lookup"><span data-stu-id="a6907-147">Maximum size of a single incoming hub message.</span></span> |
| `MaximumParallelInvocationsPerClient` | <span data-ttu-id="a6907-148">1</span><span class="sxs-lookup"><span data-stu-id="a6907-148">1</span></span> | <span data-ttu-id="a6907-149">Die maximale Anzahl von hubmethoden, die jeder Client parallel vor der Warteschlange abrufen kann.</span><span class="sxs-lookup"><span data-stu-id="a6907-149">The maximum number of hub methods that each client can call in parallel before queueing.</span></span> |

<span data-ttu-id="a6907-150">Optionen können für alle Hubs konfiguriert werden, indem ein Options Delegat für den Aufruf in bereitgestellt wird `AddSignalR` `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="a6907-150">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

<span data-ttu-id="a6907-151">Optionen für einen einzelnen Hub überschreiben die in bereitgestellten globalen Optionen `AddSignalR` und können mithilfe von konfiguriert werden <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="a6907-151">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="a6907-152">Erweiterte http-Konfigurationsoptionen</span><span class="sxs-lookup"><span data-stu-id="a6907-152">Advanced HTTP configuration options</span></span>

<span data-ttu-id="a6907-153">Verwenden `HttpConnectionDispatcherOptions` Sie, um erweiterte Einstellungen im Zusammenhang mit Transporte und der Speicherpuffer Verwaltung zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="a6907-153">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="a6907-154">Diese Optionen werden konfiguriert, indem ein [Delegat an \<T> maphub](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in übergeben wird `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="a6907-154">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<ChatHub>("/chathub", options =>
        {
            options.Transports =
                HttpTransportType.WebSockets |
                HttpTransportType.LongPolling;
        });
    });
}
```

<span data-ttu-id="a6907-155">In der folgenden Tabelle werden die Optionen zum Konfigurieren der SignalR erweiterten http-Optionen von ASP.net Core beschrieben:</span><span class="sxs-lookup"><span data-stu-id="a6907-155">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="a6907-156">Option</span><span class="sxs-lookup"><span data-stu-id="a6907-156">Option</span></span> | <span data-ttu-id="a6907-157">Standardwert</span><span class="sxs-lookup"><span data-stu-id="a6907-157">Default Value</span></span> | <span data-ttu-id="a6907-158">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="a6907-158">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="a6907-159">32 KB</span><span class="sxs-lookup"><span data-stu-id="a6907-159">32 KB</span></span> | <span data-ttu-id="a6907-160">Die maximale Anzahl von Bytes, die vom Client vor dem Anwenden des backdrucks vom Server gepuffert werden.</span><span class="sxs-lookup"><span data-stu-id="a6907-160">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="a6907-161">Wenn Sie diesen Wert erhöhen, kann der Server größere Nachrichten schneller empfangen, ohne dass ein Rückdruck angewendet wird, der Arbeitsspeicher Verbrauch kann jedoch gesteigert werden.</span><span class="sxs-lookup"><span data-stu-id="a6907-161">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="a6907-162">Daten, die automatisch von den `Authorize` auf die Hub-Klasse angewendeten Attributen gesammelt werden.</span><span class="sxs-lookup"><span data-stu-id="a6907-162">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="a6907-163">Eine Liste von [iautorizedata](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) -Objekten, die verwendet werden, um zu bestimmen, ob ein Client für die Verbindung mit dem Hub autorisiert ist.</span><span class="sxs-lookup"><span data-stu-id="a6907-163">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="a6907-164">32 KB</span><span class="sxs-lookup"><span data-stu-id="a6907-164">32 KB</span></span> | <span data-ttu-id="a6907-165">Die maximale Anzahl von Bytes, die von der APP gesendet werden, die der Server puffert, bevor der Rückdruck beobachtet wird</span><span class="sxs-lookup"><span data-stu-id="a6907-165">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="a6907-166">Wenn Sie diesen Wert erhöhen, kann der Server größere Nachrichten schneller Puffern, ohne dass auf den Rückstand gewartet wird, aber die Arbeitsspeicher Nutzung erhöhen kann.</span><span class="sxs-lookup"><span data-stu-id="a6907-166">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="a6907-167">Alle Transporte sind aktiviert.</span><span class="sxs-lookup"><span data-stu-id="a6907-167">All Transports are enabled.</span></span> | <span data-ttu-id="a6907-168">Ein Bitflags-Enumeration von `HttpTransportType` Werten, die die Transporte einschränken können, die ein Client für die Verbindungs Herstellung verwenden kann.</span><span class="sxs-lookup"><span data-stu-id="a6907-168">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="a6907-169">Siehe unten.</span><span class="sxs-lookup"><span data-stu-id="a6907-169">See below.</span></span> | <span data-ttu-id="a6907-170">Zusätzliche Optionen, die für den langen Abruf Transport spezifisch sind.</span><span class="sxs-lookup"><span data-stu-id="a6907-170">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="a6907-171">Siehe unten.</span><span class="sxs-lookup"><span data-stu-id="a6907-171">See below.</span></span> | <span data-ttu-id="a6907-172">Zusätzliche Optionen für den websockets-Transport.</span><span class="sxs-lookup"><span data-stu-id="a6907-172">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="a6907-173">0</span><span class="sxs-lookup"><span data-stu-id="a6907-173">0</span></span> | <span data-ttu-id="a6907-174">Geben Sie die Mindestversion des Aushandlungs Protokolls an.</span><span class="sxs-lookup"><span data-stu-id="a6907-174">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="a6907-175">Hiermit werden Clients auf neuere Versionen beschränkt.</span><span class="sxs-lookup"><span data-stu-id="a6907-175">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="a6907-176">Der lange Abruf Transport bietet zusätzliche Optionen, die mit der-Eigenschaft konfiguriert werden können `LongPolling` :</span><span class="sxs-lookup"><span data-stu-id="a6907-176">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="a6907-177">Option</span><span class="sxs-lookup"><span data-stu-id="a6907-177">Option</span></span> | <span data-ttu-id="a6907-178">Standardwert</span><span class="sxs-lookup"><span data-stu-id="a6907-178">Default Value</span></span> | <span data-ttu-id="a6907-179">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="a6907-179">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="a6907-180">90 Sekunden</span><span class="sxs-lookup"><span data-stu-id="a6907-180">90 seconds</span></span> | <span data-ttu-id="a6907-181">Die maximale Zeitspanne, die der Server wartet, bis eine Nachricht an den Client gesendet wird, bevor eine einzelne Abruf Anforderung beendet wird.</span><span class="sxs-lookup"><span data-stu-id="a6907-181">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="a6907-182">Das verringern dieses Werts bewirkt, dass der Client neue Abruf Anforderungen häufiger ausgibt.</span><span class="sxs-lookup"><span data-stu-id="a6907-182">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="a6907-183">Der WebSocket-Transport verfügt über zusätzliche Optionen, die mit der-Eigenschaft konfiguriert werden können `WebSockets` :</span><span class="sxs-lookup"><span data-stu-id="a6907-183">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="a6907-184">Option</span><span class="sxs-lookup"><span data-stu-id="a6907-184">Option</span></span> | <span data-ttu-id="a6907-185">Standardwert</span><span class="sxs-lookup"><span data-stu-id="a6907-185">Default Value</span></span> | <span data-ttu-id="a6907-186">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="a6907-186">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="a6907-187">5 Sekunden</span><span class="sxs-lookup"><span data-stu-id="a6907-187">5 seconds</span></span> | <span data-ttu-id="a6907-188">Wenn der Client nach dem Schließen des Servers nicht innerhalb dieses Zeitraums geschlossen werden kann, wird die Verbindung beendet.</span><span class="sxs-lookup"><span data-stu-id="a6907-188">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="a6907-189">Ein Delegat, der verwendet werden kann, um den- `Sec-WebSocket-Protocol` Header auf einen benutzerdefinierten Wert festzulegen.</span><span class="sxs-lookup"><span data-stu-id="a6907-189">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="a6907-190">Der Delegat empfängt die vom Client angeforderten Werte als Eingabe und erwartet, dass der gewünschte Wert zurückgegeben wird.</span><span class="sxs-lookup"><span data-stu-id="a6907-190">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="a6907-191">Konfigurieren von Clientoptionen</span><span class="sxs-lookup"><span data-stu-id="a6907-191">Configure client options</span></span>

<span data-ttu-id="a6907-192">Client Optionen können für den Typ konfiguriert werden `HubConnectionBuilder` (verfügbar in den .net-und JavaScript-Clients).</span><span class="sxs-lookup"><span data-stu-id="a6907-192">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="a6907-193">Sie steht auch im Java-Client zur Verfügung, aber die `HttpHubConnectionBuilder` Unterklasse enthält die Generator-Konfigurationsoptionen sowie die `HubConnection` selbst.</span><span class="sxs-lookup"><span data-stu-id="a6907-193">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="a6907-194">Konfigurieren der Protokollierung</span><span class="sxs-lookup"><span data-stu-id="a6907-194">Configure logging</span></span>

<span data-ttu-id="a6907-195">Die Protokollierung wird mithilfe der-Methode im .NET-Client konfiguriert `ConfigureLogging` .</span><span class="sxs-lookup"><span data-stu-id="a6907-195">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="a6907-196">Protokollierungs Anbieter und Filter können auf die gleiche Weise wie auf dem Server registriert werden.</span><span class="sxs-lookup"><span data-stu-id="a6907-196">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="a6907-197">Weitere Informationen finden Sie in der Dokumentation zur [Protokollierung ASP.net Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="a6907-197">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="a6907-198">Um Protokollierungs Anbieter zu registrieren, müssen Sie die erforderlichen Pakete installieren.</span><span class="sxs-lookup"><span data-stu-id="a6907-198">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="a6907-199">Eine vollständige Liste finden Sie im Abschnitt [integrierte Protokollierungs Anbieter](xref:fundamentals/logging/index#built-in-logging-providers) der Dokumentation.</span><span class="sxs-lookup"><span data-stu-id="a6907-199">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="a6907-200">Um z. b. die Konsolen Protokollierung zu aktivieren, installieren Sie das `Microsoft.Extensions.Logging.Console` nuget-Paket.</span><span class="sxs-lookup"><span data-stu-id="a6907-200">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="a6907-201">Die `AddConsole` Erweiterungsmethode aufzurufen:</span><span class="sxs-lookup"><span data-stu-id="a6907-201">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="a6907-202">Im JavaScript-Client ist eine ähnliche `configureLogging` Methode vorhanden.</span><span class="sxs-lookup"><span data-stu-id="a6907-202">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="a6907-203">Geben `LogLevel` Sie einen Wert an, der die minimale Ebene der zu erstellenden Protokollmeldungen angibt.</span><span class="sxs-lookup"><span data-stu-id="a6907-203">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="a6907-204">Protokolle werden in das Browser Konsolenfenster geschrieben.</span><span class="sxs-lookup"><span data-stu-id="a6907-204">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="a6907-205">Anstelle eines `LogLevel` Werts können Sie auch einen Wert angeben, der den `string` Namen einer Protokollebene darstellt.</span><span class="sxs-lookup"><span data-stu-id="a6907-205">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="a6907-206">Dies ist nützlich, wenn SignalR Sie die Protokollierung in Umgebungen konfigurieren, in denen Sie keinen Zugriff auf die `LogLevel` Konstanten haben.</span><span class="sxs-lookup"><span data-stu-id="a6907-206">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="a6907-207">In der folgenden Tabelle sind die verfügbaren Protokoll Ebenen aufgeführt.</span><span class="sxs-lookup"><span data-stu-id="a6907-207">The following table lists the available log levels.</span></span> <span data-ttu-id="a6907-208">Der von Ihnen bereitgestellte Wert, `configureLogging` der die **minimale** Protokollierung festlegt, die protokolliert werden soll.</span><span class="sxs-lookup"><span data-stu-id="a6907-208">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="a6907-209">Nachrichten, die auf dieser Ebene protokolliert werden, **oder die nach der Tabelle aufgeführten Ebenen** werden protokolliert.</span><span class="sxs-lookup"><span data-stu-id="a6907-209">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="a6907-210">String</span><span class="sxs-lookup"><span data-stu-id="a6907-210">String</span></span>                      | <span data-ttu-id="a6907-211">LogLevel</span><span class="sxs-lookup"><span data-stu-id="a6907-211">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="a6907-212">`info`**oder**`information`</span><span class="sxs-lookup"><span data-stu-id="a6907-212">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="a6907-213">`warn`**oder**`warning`</span><span class="sxs-lookup"><span data-stu-id="a6907-213">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="a6907-214">Um die Protokollierung vollständig zu deaktivieren, geben Sie `signalR.LogLevel.None` in der `configureLogging` Methode an</span><span class="sxs-lookup"><span data-stu-id="a6907-214">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="a6907-215">Weitere Informationen zur Protokollierung finden Sie in der [ SignalR Diagnose Dokumentation](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="a6907-215">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="a6907-216">Der SignalR Java-Client verwendet die [SLF4J](https://www.slf4j.org/) -Bibliothek für die Protokollierung.</span><span class="sxs-lookup"><span data-stu-id="a6907-216">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="a6907-217">Dabei handelt es sich um eine allgemeine Protokollierungs-API, die es Benutzern der Bibliothek ermöglicht, ihre eigene spezifische Protokollierungs Implementierung zu wählen, indem Sie eine bestimmte Protokollierungs Abhängigkeit einbinden.</span><span class="sxs-lookup"><span data-stu-id="a6907-217">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="a6907-218">Der folgende Code Ausschnitt zeigt, wie `java.util.logging` mit dem Java- SignalR Client verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="a6907-218">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="a6907-219">Wenn Sie die Protokollierung in ihren Abhängigkeiten nicht konfigurieren, lädt SLF4J eine Standard Protokollierung ohne Vorgang mit folgender Warnmeldung:</span><span class="sxs-lookup"><span data-stu-id="a6907-219">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="a6907-220">Dies kann sicher ignoriert werden.</span><span class="sxs-lookup"><span data-stu-id="a6907-220">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="a6907-221">Zulässige Transporte konfigurieren</span><span class="sxs-lookup"><span data-stu-id="a6907-221">Configure allowed transports</span></span>

<span data-ttu-id="a6907-222">Die von verwendeten Transporte SignalR können im-Befehl `WithUrl` ( `withUrl` in JavaScript) konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="a6907-222">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="a6907-223">Ein bitweises OR der Werte von `HttpTransportType` kann verwendet werden, um den Client so einzuschränken, dass nur die angegebenen Transporte verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="a6907-223">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="a6907-224">Alle Transporte sind standardmäßig aktiviert.</span><span class="sxs-lookup"><span data-stu-id="a6907-224">All transports are enabled by default.</span></span>

<span data-ttu-id="a6907-225">Um z. b. den Server-Sent Ereignis Transport zu deaktivieren, aber websockets und lange Abruf Verbindungen zuzulassen:</span><span class="sxs-lookup"><span data-stu-id="a6907-225">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="a6907-226">Im JavaScript-Client werden Transporte durch Festlegen des- `transport` Felds für das Options-Objekt konfiguriert, das für bereitgestellt wird `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="a6907-226">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="a6907-227">In dieser Version des Java-Clients ist websockets der einzige verfügbare Transport.</span><span class="sxs-lookup"><span data-stu-id="a6907-227">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="a6907-228">Im Java-Client wird der Transport mit der-Methode im ausgewählt `withTransport` `HttpHubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="a6907-228">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="a6907-229">Der Java-Client verwendet standardmäßig den websockets-Transport.</span><span class="sxs-lookup"><span data-stu-id="a6907-229">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="a6907-230">Der SignalR Java-Client unterstützt noch keinen Transport Fall Back.</span><span class="sxs-lookup"><span data-stu-id="a6907-230">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="a6907-231">Konfigurieren der bearerauthentifizierung</span><span class="sxs-lookup"><span data-stu-id="a6907-231">Configure bearer authentication</span></span>

<span data-ttu-id="a6907-232">Um Authentifizierungsdaten zusammen mit Anforderungen bereitzustellen SignalR , verwenden `AccessTokenProvider` Sie die-Option ( `accessTokenFactory` in JavaScript), um eine Funktion anzugeben, die das gewünschte Zugriffs Token zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="a6907-232">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="a6907-233">Im .NET-Client wird dieses Zugriffs Token als http-tokenauthentifizierungstoken (mit dem- `Authorization` Header mit dem Typ) an das Token "bearerauthentifizierung" geleitet `Bearer` .</span><span class="sxs-lookup"><span data-stu-id="a6907-233">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="a6907-234">Im JavaScript-Client wird das Zugriffs Token als bearertoken verwendet, **außer** in einigen Fällen, in denen Browser-APIs die Fähigkeit zum Anwenden von Headern einschränken (insbesondere in Server-Sent Ereignissen und websockets-Anforderungen).</span><span class="sxs-lookup"><span data-stu-id="a6907-234">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="a6907-235">In diesen Fällen wird das Zugriffs Token als Abfrage Zeichenfolgen-Wert bereitgestellt `access_token` .</span><span class="sxs-lookup"><span data-stu-id="a6907-235">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="a6907-236">Im .NET-Client kann die `AccessTokenProvider` Option mithilfe des Options Delegaten in angegeben werden `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="a6907-236">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="a6907-237">Im JavaScript-Client wird das Zugriffs Token konfiguriert, indem das- `accessTokenFactory` Feld für das Options-Objekt in festgelegt wird `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="a6907-237">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

<span data-ttu-id="a6907-238">Im SignalR Java-Client können Sie ein bearertoken für die Authentifizierung konfigurieren, indem Sie eine zugriffstokenfactory für " [httphubconnectionbuilder](/java/api/com.microsoft.signalr.httphubconnectionbuilder?view=aspnet-signalr-java)" bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="a6907-238">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr.httphubconnectionbuilder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="a6907-239">Verwenden Sie [withaccesstokenfactory](/java/api/com.microsoft.signalr.httphubconnectionbuilder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) , um eine [einzelne \<String> ](https://reactivex.io/documentation/single.html) [rxjava](https://github.com/ReactiveX/RxJava) -Instanz bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="a6907-239">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr.httphubconnectionbuilder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="a6907-240">Beim Aufrufen von " [Single.](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)Write" können Sie Logik schreiben, um Zugriffs Token für den Client zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="a6907-240">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="a6907-241">Konfigurieren von Timeout-und Keep-Alive-Optionen</span><span class="sxs-lookup"><span data-stu-id="a6907-241">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="a6907-242">Zusätzliche Optionen zum Konfigurieren von Timeout und Keep-Alive-Verhalten sind für das `HubConnection` Objekt selbst verfügbar:</span><span class="sxs-lookup"><span data-stu-id="a6907-242">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="a6907-243">.NET</span><span class="sxs-lookup"><span data-stu-id="a6907-243">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="a6907-244">Option</span><span class="sxs-lookup"><span data-stu-id="a6907-244">Option</span></span> | <span data-ttu-id="a6907-245">Standardwert</span><span class="sxs-lookup"><span data-stu-id="a6907-245">Default value</span></span> | <span data-ttu-id="a6907-246">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="a6907-246">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="a6907-247">30 Sekunden (30.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="a6907-247">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="a6907-248">Timeout für die Serveraktivität.</span><span class="sxs-lookup"><span data-stu-id="a6907-248">Timeout for server activity.</span></span> <span data-ttu-id="a6907-249">Wenn der Server in diesem Intervall keine Nachricht gesendet hat, betrachtet der Client den Server als getrennt und löst das `Closed` Ereignis `onclose` aus (in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="a6907-249">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="a6907-250">Dieser Wert muss groß genug sein, damit eine Ping-Nachricht vom Server gesendet **und** innerhalb des Timeout Intervalls vom Client empfangen wird.</span><span class="sxs-lookup"><span data-stu-id="a6907-250">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="a6907-251">Der empfohlene Wert ist eine Zahl, die mindestens dem Wert des Servers `KeepAliveInterval` entspricht, um Zeit für das Eintreffen von Pings zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="a6907-251">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="a6907-252">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="a6907-252">15 seconds</span></span> | <span data-ttu-id="a6907-253">Timeout für den anfänglichen Server Hand Shake.</span><span class="sxs-lookup"><span data-stu-id="a6907-253">Timeout for initial server handshake.</span></span> <span data-ttu-id="a6907-254">Wenn der Server in diesem Intervall keine Hand Shake Antwort sendet, bricht der Client den Handshake ab und löst das `Closed` Ereignis `onclose` aus (in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="a6907-254">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="a6907-255">Dies ist eine erweiterte Einstellung, die nur geändert werden sollte, wenn aufgrund schwerer Netzwerk Latenz Fehler aufgrund von Hand Shake Timeout auftreten.</span><span class="sxs-lookup"><span data-stu-id="a6907-255">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="a6907-256">Weitere Details zum Hand Shake Prozess finden Sie in der [ SignalR Hub-Protokollspezifikation](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="a6907-256">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="a6907-257">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="a6907-257">15 seconds</span></span> | <span data-ttu-id="a6907-258">Bestimmt das Intervall, in dem der Client Ping-Nachrichten sendet.</span><span class="sxs-lookup"><span data-stu-id="a6907-258">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="a6907-259">Beim Senden von Nachrichten vom Client wird der Timer auf den Anfang des Intervalls zurückgesetzt.</span><span class="sxs-lookup"><span data-stu-id="a6907-259">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="a6907-260">Wenn der Client keine Nachricht in der `ClientTimeoutInterval` Gruppe auf dem Server gesendet hat, wird der Client vom Server als getrennt betrachtet.</span><span class="sxs-lookup"><span data-stu-id="a6907-260">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="a6907-261">Im .NET-Client werden Timeout Werte als Werte angegeben `TimeSpan` .</span><span class="sxs-lookup"><span data-stu-id="a6907-261">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="a6907-262">JavaScript</span><span class="sxs-lookup"><span data-stu-id="a6907-262">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="a6907-263">Option</span><span class="sxs-lookup"><span data-stu-id="a6907-263">Option</span></span> | <span data-ttu-id="a6907-264">Standardwert</span><span class="sxs-lookup"><span data-stu-id="a6907-264">Default value</span></span> | <span data-ttu-id="a6907-265">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="a6907-265">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="a6907-266">30 Sekunden (30.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="a6907-266">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="a6907-267">Timeout für die Serveraktivität.</span><span class="sxs-lookup"><span data-stu-id="a6907-267">Timeout for server activity.</span></span> <span data-ttu-id="a6907-268">Wenn der Server in diesem Intervall keine Nachricht gesendet hat, betrachtet der Client den Server als getrennt und löst das `onclose` Ereignis aus.</span><span class="sxs-lookup"><span data-stu-id="a6907-268">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="a6907-269">Dieser Wert muss groß genug sein, damit eine Ping-Nachricht vom Server gesendet **und** innerhalb des Timeout Intervalls vom Client empfangen wird.</span><span class="sxs-lookup"><span data-stu-id="a6907-269">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="a6907-270">Der empfohlene Wert ist eine Zahl, die mindestens dem Wert des Servers `KeepAliveInterval` entspricht, um Zeit für das Eintreffen von Pings zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="a6907-270">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="a6907-271">15 Sekunden (15.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="a6907-271">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="a6907-272">Bestimmt das Intervall, in dem der Client Ping-Nachrichten sendet.</span><span class="sxs-lookup"><span data-stu-id="a6907-272">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="a6907-273">Beim Senden von Nachrichten vom Client wird der Timer auf den Anfang des Intervalls zurückgesetzt.</span><span class="sxs-lookup"><span data-stu-id="a6907-273">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="a6907-274">Wenn der Client keine Nachricht in der `ClientTimeoutInterval` Gruppe auf dem Server gesendet hat, wird der Client vom Server als getrennt betrachtet.</span><span class="sxs-lookup"><span data-stu-id="a6907-274">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="a6907-275">Java</span><span class="sxs-lookup"><span data-stu-id="a6907-275">Java</span></span>](#tab/java)

| <span data-ttu-id="a6907-276">Option</span><span class="sxs-lookup"><span data-stu-id="a6907-276">Option</span></span> | <span data-ttu-id="a6907-277">Standardwert</span><span class="sxs-lookup"><span data-stu-id="a6907-277">Default value</span></span> | <span data-ttu-id="a6907-278">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="a6907-278">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="a6907-279">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="a6907-279">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="a6907-280">30 Sekunden (30.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="a6907-280">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="a6907-281">Timeout für die Serveraktivität.</span><span class="sxs-lookup"><span data-stu-id="a6907-281">Timeout for server activity.</span></span> <span data-ttu-id="a6907-282">Wenn der Server in diesem Intervall keine Nachricht gesendet hat, betrachtet der Client den Server als getrennt und löst das `onClose` Ereignis aus.</span><span class="sxs-lookup"><span data-stu-id="a6907-282">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="a6907-283">Dieser Wert muss groß genug sein, damit eine Ping-Nachricht vom Server gesendet **und** innerhalb des Timeout Intervalls vom Client empfangen wird.</span><span class="sxs-lookup"><span data-stu-id="a6907-283">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="a6907-284">Der empfohlene Wert ist eine Zahl, die mindestens dem Wert des Servers `KeepAliveInterval` entspricht, um Zeit für das Eintreffen von Pings zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="a6907-284">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="a6907-285">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="a6907-285">15 seconds</span></span> | <span data-ttu-id="a6907-286">Timeout für den anfänglichen Server Hand Shake.</span><span class="sxs-lookup"><span data-stu-id="a6907-286">Timeout for initial server handshake.</span></span> <span data-ttu-id="a6907-287">Wenn der Server in diesem Intervall keine Hand Shake Antwort sendet, bricht der Client den Handshake ab und löst das- `onClose` Ereignis aus.</span><span class="sxs-lookup"><span data-stu-id="a6907-287">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="a6907-288">Dies ist eine erweiterte Einstellung, die nur geändert werden sollte, wenn aufgrund schwerer Netzwerk Latenz Fehler aufgrund von Hand Shake Timeout auftreten.</span><span class="sxs-lookup"><span data-stu-id="a6907-288">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="a6907-289">Weitere Details zum Hand Shake Prozess finden Sie in der [ SignalR Hub-Protokollspezifikation](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="a6907-289">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="a6907-290">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="a6907-290">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="a6907-291">15 Sekunden (15.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="a6907-291">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="a6907-292">Bestimmt das Intervall, in dem der Client Ping-Nachrichten sendet.</span><span class="sxs-lookup"><span data-stu-id="a6907-292">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="a6907-293">Beim Senden von Nachrichten vom Client wird der Timer auf den Anfang des Intervalls zurückgesetzt.</span><span class="sxs-lookup"><span data-stu-id="a6907-293">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="a6907-294">Wenn der Client keine Nachricht in der `ClientTimeoutInterval` Gruppe auf dem Server gesendet hat, wird der Client vom Server als getrennt betrachtet.</span><span class="sxs-lookup"><span data-stu-id="a6907-294">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="a6907-295">Konfigurieren zusätzlicher Optionen</span><span class="sxs-lookup"><span data-stu-id="a6907-295">Configure additional options</span></span>

<span data-ttu-id="a6907-296">Zusätzliche Optionen können in der- `WithUrl` Methode ( `withUrl` in JavaScript) für `HubConnectionBuilder` oder für die verschiedenen Konfigurations-APIs auf dem `HttpHubConnectionBuilder` im Java-Client konfiguriert werden:</span><span class="sxs-lookup"><span data-stu-id="a6907-296">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="a6907-297">.NET</span><span class="sxs-lookup"><span data-stu-id="a6907-297">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="a6907-298">.Net-Option</span><span class="sxs-lookup"><span data-stu-id="a6907-298">.NET Option</span></span> |  <span data-ttu-id="a6907-299">Standardwert</span><span class="sxs-lookup"><span data-stu-id="a6907-299">Default value</span></span> | <span data-ttu-id="a6907-300">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="a6907-300">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="a6907-301">Eine Funktion, die eine Zeichenfolge zurückgibt, die als bearerauthentifizierungstoken in HTTP-Anforderungen bereitgestellt</span><span class="sxs-lookup"><span data-stu-id="a6907-301">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="a6907-302">Legen Sie dies auf fest, `true` um den Aushandlungs Schritt zu überspringen</span><span class="sxs-lookup"><span data-stu-id="a6907-302">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="a6907-303">Wird **nur unterstützt, wenn der websockets-Transport der einzige aktivierte Transport ist**.</span><span class="sxs-lookup"><span data-stu-id="a6907-303">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="a6907-304">Diese Einstellung kann nicht aktiviert werden, wenn der Azure-Dienst verwendet wird SignalR .</span><span class="sxs-lookup"><span data-stu-id="a6907-304">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="a6907-305">Leer</span><span class="sxs-lookup"><span data-stu-id="a6907-305">Empty</span></span> | <span data-ttu-id="a6907-306">Eine Auflistung von TLS-Zertifikaten, die zum Authentifizieren von Anforderungen gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="a6907-306">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="a6907-307">Leer</span><span class="sxs-lookup"><span data-stu-id="a6907-307">Empty</span></span> | <span data-ttu-id="a6907-308">Eine Auflistung von http- cookie s, die mit jeder HTTP-Anforderung gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="a6907-308">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="a6907-309">Leer</span><span class="sxs-lookup"><span data-stu-id="a6907-309">Empty</span></span> | <span data-ttu-id="a6907-310">Anmelde Informationen, die mit jeder HTTP-Anforderung gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="a6907-310">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="a6907-311">5 Sekunden</span><span class="sxs-lookup"><span data-stu-id="a6907-311">5 seconds</span></span> | <span data-ttu-id="a6907-312">Nur websockets.</span><span class="sxs-lookup"><span data-stu-id="a6907-312">WebSockets only.</span></span> <span data-ttu-id="a6907-313">Die maximale Zeitspanne, die der Client nach dem Schließen des Servers wartet, um die Anforderung zum Schließen zu bestätigen.</span><span class="sxs-lookup"><span data-stu-id="a6907-313">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="a6907-314">Wenn der Server die Schließung innerhalb dieses Zeitraums nicht anerkennt, trennt der Client die Verbindung.</span><span class="sxs-lookup"><span data-stu-id="a6907-314">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="a6907-315">Leer</span><span class="sxs-lookup"><span data-stu-id="a6907-315">Empty</span></span> | <span data-ttu-id="a6907-316">Eine Karte mit zusätzlichen HTTP-Headern, die mit jeder HTTP-Anforderung gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="a6907-316">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="a6907-317">Ein Delegat, der verwendet werden kann, um den `HttpMessageHandler` zum Senden von HTTP-Anforderungen verwendeten zu konfigurieren oder zu ersetzen.</span><span class="sxs-lookup"><span data-stu-id="a6907-317">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="a6907-318">Wird nicht für WebSocket-Verbindungen verwendet.</span><span class="sxs-lookup"><span data-stu-id="a6907-318">Not used for WebSocket connections.</span></span> <span data-ttu-id="a6907-319">Dieser Delegat muss einen Wert zurückgeben, der nicht NULL ist, und erhält den Standardwert als Parameter.</span><span class="sxs-lookup"><span data-stu-id="a6907-319">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="a6907-320">Ändern Sie die Einstellungen für diesen Standardwert, und geben Sie ihn zurück, oder geben Sie eine neue `HttpMessageHandler` Instanz zurück.</span><span class="sxs-lookup"><span data-stu-id="a6907-320">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="a6907-321">**Wenn Sie den Handler ersetzen, stellen Sie sicher, dass die Einstellungen, die Sie beibehalten möchten, vom bereitgestellten Handler kopiert werden. Andernfalls gelten die konfigurierten Optionen (z. b. Cookie s und Header) nicht für den neuen Handler.**</span><span class="sxs-lookup"><span data-stu-id="a6907-321">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="a6907-322">Ein HTTP-Proxy, der beim Senden von HTTP-Anforderungen verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="a6907-322">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="a6907-323">Legen Sie diesen booleschen Wert fest, um die Standard Anmelde Informationen für http-und websockets-Anforderungen zu senden</span><span class="sxs-lookup"><span data-stu-id="a6907-323">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="a6907-324">Dies ermöglicht die Verwendung der Windows-Authentifizierung.</span><span class="sxs-lookup"><span data-stu-id="a6907-324">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="a6907-325">Ein Delegat, der zum Konfigurieren zusätzlicher WebSocket-Optionen verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="a6907-325">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="a6907-326">Empfängt eine Instanz von [clientwebsocketoptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) , die zum Konfigurieren der Optionen verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="a6907-326">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="a6907-327">JavaScript</span><span class="sxs-lookup"><span data-stu-id="a6907-327">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="a6907-328">JavaScript-Option</span><span class="sxs-lookup"><span data-stu-id="a6907-328">JavaScript Option</span></span> | <span data-ttu-id="a6907-329">Standardwert</span><span class="sxs-lookup"><span data-stu-id="a6907-329">Default Value</span></span> | <span data-ttu-id="a6907-330">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="a6907-330">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="a6907-331">Eine Funktion, die eine Zeichenfolge zurückgibt, die als bearerauthentifizierungstoken in HTTP-Anforderungen bereitgestellt</span><span class="sxs-lookup"><span data-stu-id="a6907-331">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `transport` | `null` | <span data-ttu-id="a6907-332">Ein- <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType> Wert, der den für die Verbindung zu verwendenden Transport angibt.</span><span class="sxs-lookup"><span data-stu-id="a6907-332">An <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType> value specifying the transport to use for the connection.</span></span> |
| `headers` | `null` | <span data-ttu-id="a6907-333">Das Wörterbuch der mit jeder HTTP-Anforderung gesendeten Header.</span><span class="sxs-lookup"><span data-stu-id="a6907-333">Dictionary of headers sent with every HTTP request.</span></span> <span data-ttu-id="a6907-334">Das Senden von Headern im Browser funktioniert nicht für websockets oder den <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType.ServerSentEvents> Stream.</span><span class="sxs-lookup"><span data-stu-id="a6907-334">Sending headers in the browser doesn't work for WebSockets or the <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType.ServerSentEvents> stream.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="a6907-335">Legen Sie auf fest, `true` um die Bytes/Zeichen der vom Client gesendeten und empfangenen Nachrichten zu protokollieren.</span><span class="sxs-lookup"><span data-stu-id="a6907-335">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="a6907-336">Legen Sie dies auf fest, `true` um den Aushandlungs Schritt zu überspringen</span><span class="sxs-lookup"><span data-stu-id="a6907-336">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="a6907-337">Wird **nur unterstützt, wenn der websockets-Transport der einzige aktivierte Transport ist**.</span><span class="sxs-lookup"><span data-stu-id="a6907-337">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="a6907-338">Diese Einstellung kann nicht aktiviert werden, wenn der Azure-Dienst verwendet wird SignalR .</span><span class="sxs-lookup"><span data-stu-id="a6907-338">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `withCredentials` | `true` | <span data-ttu-id="a6907-339">Gibt an, ob Anmelde Informationen mit der cors-Anforderung gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="a6907-339">Specifies whether credentials will be sent with the CORS request.</span></span> <span data-ttu-id="a6907-340">Azure App Service verwendet cookie s für persistente Sitzungen und erfordert, dass diese Option ordnungsgemäß funktioniert.</span><span class="sxs-lookup"><span data-stu-id="a6907-340">Azure App Service uses cookies for sticky sessions and needs this option enabled to work correctly.</span></span> <span data-ttu-id="a6907-341">Weitere Informationen zu cors mit SignalR finden Sie unter <xref:signalr/security#cross-origin-resource-sharing> .</span><span class="sxs-lookup"><span data-stu-id="a6907-341">For more info on CORS with SignalR, see <xref:signalr/security#cross-origin-resource-sharing>.</span></span> |

# <a name="java"></a>[<span data-ttu-id="a6907-342">Java</span><span class="sxs-lookup"><span data-stu-id="a6907-342">Java</span></span>](#tab/java)

| <span data-ttu-id="a6907-343">Java-Option</span><span class="sxs-lookup"><span data-stu-id="a6907-343">Java Option</span></span> | <span data-ttu-id="a6907-344">Standardwert</span><span class="sxs-lookup"><span data-stu-id="a6907-344">Default Value</span></span> | <span data-ttu-id="a6907-345">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="a6907-345">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="a6907-346">Eine Funktion, die eine Zeichenfolge zurückgibt, die als bearerauthentifizierungstoken in HTTP-Anforderungen bereitgestellt</span><span class="sxs-lookup"><span data-stu-id="a6907-346">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="a6907-347">Legen Sie dies auf fest, `true` um den Aushandlungs Schritt zu überspringen</span><span class="sxs-lookup"><span data-stu-id="a6907-347">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="a6907-348">Wird **nur unterstützt, wenn der websockets-Transport der einzige aktivierte Transport ist**.</span><span class="sxs-lookup"><span data-stu-id="a6907-348">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="a6907-349">Diese Einstellung kann nicht aktiviert werden, wenn der Azure-Dienst verwendet wird SignalR .</span><span class="sxs-lookup"><span data-stu-id="a6907-349">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="a6907-350">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="a6907-350">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="a6907-351">Leer</span><span class="sxs-lookup"><span data-stu-id="a6907-351">Empty</span></span> | <span data-ttu-id="a6907-352">Eine Karte mit zusätzlichen HTTP-Headern, die mit jeder HTTP-Anforderung gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="a6907-352">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="a6907-353">Im .NET-Client können diese Optionen durch den für bereitgestellten Options Delegaten geändert werden `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="a6907-353">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.SkipNegotiation = true;
        options.Transports = HttpTransportType.WebSockets;
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="a6907-354">Im JavaScript-Client können diese Optionen in einem JavaScript-Objekt bereitgestellt werden, das für Folgendes bereitgestellt wird `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="a6907-354">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        // "Foo: Bar" will not be sent with WebSockets or Server-Sent Events requests
        headers: { "Foo": "Bar" },
        transport: signalR.HttpTransportType.LongPolling 
    })
    .build();
```

<span data-ttu-id="a6907-355">Im Java-Client können diese Optionen mit den Methoden auf der konfiguriert werden, die `HttpHubConnectionBuilder` von der `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="a6907-355">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="a6907-356">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="a6907-356">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.1"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="a6907-357">JSON/messagepack-Serialisierungsoptionen</span><span class="sxs-lookup"><span data-stu-id="a6907-357">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="a6907-358">ASP.net Core SignalR unterstützt zwei Protokolle zum Codieren von Nachrichten: [JSON](https://www.json.org/) und [messagepack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="a6907-358">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="a6907-359">Jedes Protokoll verfügt über Konfigurationsoptionen für die Serialisierung.</span><span class="sxs-lookup"><span data-stu-id="a6907-359">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="a6907-360">Die JSON-Serialisierung kann mithilfe der [addjsonprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) -Erweiterungsmethode auf dem Server konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="a6907-360">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="a6907-361">`AddJsonProtocol`kann nach [Add SignalR ](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in hinzugefügt werden `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="a6907-361">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="a6907-362">Die- `AddJsonProtocol` Methode nimmt einen Delegaten an, der ein- `options` Objekt empfängt.</span><span class="sxs-lookup"><span data-stu-id="a6907-362">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="a6907-363">Die [payloadserializeroptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) -Eigenschaft für dieses Objekt ist ein- `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> Objekt, das zum Konfigurieren der Serialisierung von Argumenten und Rückgabe Werten verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="a6907-363">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="a6907-364">Weitere Informationen finden Sie in den [System.Text.Jszur Dokumentation](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="a6907-364">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="a6907-365">Verwenden Sie beispielsweise den folgenden Code in, um das Serialisierungsprogramm so zu konfigurieren, dass die Groß-/Kleinschreibung von Eigenschaftsnamen nicht geändert wird. verwenden Sie dazu den folgenden Code in `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="a6907-365">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="a6907-366">Im .NET-Client ist dieselbe `AddJsonProtocol` Erweiterungsmethode auf [hubconnectionbuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)vorhanden.</span><span class="sxs-lookup"><span data-stu-id="a6907-366">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="a6907-367">Der `Microsoft.Extensions.DependencyInjection` Namespace muss importiert werden, um die Erweiterungsmethode aufzulösen:</span><span class="sxs-lookup"><span data-stu-id="a6907-367">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;

// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    })
    .Build();
```

> [!NOTE]
> <span data-ttu-id="a6907-368">Zurzeit ist es nicht möglich, JSON-Serialisierung im JavaScript-Client zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="a6907-368">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="a6907-369">Zum Newtonsoft.Jswechseln</span><span class="sxs-lookup"><span data-stu-id="a6907-369">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="a6907-370">Wenn Sie Features von benötigen `Newtonsoft.Json` , die in nicht unterstützt `System.Text.Json` werden, finden Sie weitere Informationen unter [Wechseln zu Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="a6907-370">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="a6907-371">Messagepack-Serialisierungsoptionen</span><span class="sxs-lookup"><span data-stu-id="a6907-371">MessagePack serialization options</span></span>

<span data-ttu-id="a6907-372">Die messagepack-Serialisierung kann konfiguriert werden, indem ein Delegat für den [addmessagepackprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) -Aufruf bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="a6907-372">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="a6907-373">Weitere Informationen finden Sie [unter SignalR messagepack in](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="a6907-373">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="a6907-374">Zurzeit ist es nicht möglich, die messagepack-Serialisierung im JavaScript-Client zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="a6907-374">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="a6907-375">Konfigurieren von Serveroptionen</span><span class="sxs-lookup"><span data-stu-id="a6907-375">Configure server options</span></span>

<span data-ttu-id="a6907-376">In der folgenden Tabelle werden die Optionen zum Konfigurieren von SignalR Hubs beschrieben:</span><span class="sxs-lookup"><span data-stu-id="a6907-376">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="a6907-377">Option</span><span class="sxs-lookup"><span data-stu-id="a6907-377">Option</span></span> | <span data-ttu-id="a6907-378">Standardwert</span><span class="sxs-lookup"><span data-stu-id="a6907-378">Default Value</span></span> | <span data-ttu-id="a6907-379">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="a6907-379">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="a6907-380">30 Sekunden</span><span class="sxs-lookup"><span data-stu-id="a6907-380">30 seconds</span></span> | <span data-ttu-id="a6907-381">Der Server wird vom-Client als getrennt betrachtet, wenn in diesem Intervall keine Nachricht (einschließlich Keep-Alive) empfangen wurde.</span><span class="sxs-lookup"><span data-stu-id="a6907-381">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="a6907-382">Es kann länger dauern, bis der Client als getrennt gekennzeichnet ist, weil dies implementiert ist.</span><span class="sxs-lookup"><span data-stu-id="a6907-382">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="a6907-383">Der empfohlene Wert ist Double- `KeepAliveInterval` Wert.</span><span class="sxs-lookup"><span data-stu-id="a6907-383">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="a6907-384">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="a6907-384">15 seconds</span></span> | <span data-ttu-id="a6907-385">Wenn der Client innerhalb dieses Zeitraums keine anfängliche Hand Shake Nachricht sendet, wird die Verbindung geschlossen.</span><span class="sxs-lookup"><span data-stu-id="a6907-385">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="a6907-386">Dies ist eine erweiterte Einstellung, die nur geändert werden sollte, wenn aufgrund schwerer Netzwerk Latenz Fehler aufgrund von Hand Shake Timeout auftreten.</span><span class="sxs-lookup"><span data-stu-id="a6907-386">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="a6907-387">Weitere Details zum Hand Shake Prozess finden Sie in der [ SignalR Hub-Protokollspezifikation](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="a6907-387">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="a6907-388">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="a6907-388">15 seconds</span></span> | <span data-ttu-id="a6907-389">Wenn der Server innerhalb dieses Intervalls keine Nachricht gesendet hat, wird automatisch eine Ping-Nachricht gesendet, um die Verbindung offen zu halten.</span><span class="sxs-lookup"><span data-stu-id="a6907-389">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="a6907-390">Ändern Sie `KeepAliveInterval` die `ServerTimeout` / `serverTimeoutInMilliseconds` Einstellung auf dem Client, wenn Sie sich ändern.</span><span class="sxs-lookup"><span data-stu-id="a6907-390">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="a6907-391">Der empfohlene `ServerTimeout` / `serverTimeoutInMilliseconds` Wert ist Double- `KeepAliveInterval` Wert.</span><span class="sxs-lookup"><span data-stu-id="a6907-391">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="a6907-392">Alle installierten Protokolle</span><span class="sxs-lookup"><span data-stu-id="a6907-392">All installed protocols</span></span> | <span data-ttu-id="a6907-393">Protokolle, die von diesem Hub unterstützt werden.</span><span class="sxs-lookup"><span data-stu-id="a6907-393">Protocols supported by this hub.</span></span> <span data-ttu-id="a6907-394">Standardmäßig sind alle auf dem Server registrierten Protokolle zulässig, aber Protokolle können aus dieser Liste entfernt werden, um bestimmte Protokolle für einzelne Hubs zu deaktivieren.</span><span class="sxs-lookup"><span data-stu-id="a6907-394">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="a6907-395">Wenn der Wert `true` ist, werden ausführliche Ausnahme Meldungen an Clients zurückgegeben, wenn eine Ausnahme in einer Hub-Methode ausgelöst wird.</span><span class="sxs-lookup"><span data-stu-id="a6907-395">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="a6907-396">Der Standardwert ist `false` , da diese Ausnahme Meldungen vertrauliche Informationen enthalten können.</span><span class="sxs-lookup"><span data-stu-id="a6907-396">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="a6907-397">Die maximale Anzahl von Elementen, die für Client-uploadstreams gepuffert werden können.</span><span class="sxs-lookup"><span data-stu-id="a6907-397">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="a6907-398">Wenn dieser Grenzwert erreicht wird, wird die Verarbeitung von Aufrufen blockiert, bis der Server streamingelemente verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="a6907-398">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="a6907-399">32 KB</span><span class="sxs-lookup"><span data-stu-id="a6907-399">32 KB</span></span> | <span data-ttu-id="a6907-400">Maximale Größe einer einzelnen eingehenden Hub-Nachricht.</span><span class="sxs-lookup"><span data-stu-id="a6907-400">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="a6907-401">Optionen können für alle Hubs konfiguriert werden, indem ein Options Delegat für den Aufruf in bereitgestellt wird `AddSignalR` `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="a6907-401">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

<span data-ttu-id="a6907-402">Optionen für einen einzelnen Hub überschreiben die in bereitgestellten globalen Optionen `AddSignalR` und können mithilfe von konfiguriert werden <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="a6907-402">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="a6907-403">Erweiterte http-Konfigurationsoptionen</span><span class="sxs-lookup"><span data-stu-id="a6907-403">Advanced HTTP configuration options</span></span>

<span data-ttu-id="a6907-404">Verwenden `HttpConnectionDispatcherOptions` Sie, um erweiterte Einstellungen im Zusammenhang mit Transporte und der Speicherpuffer Verwaltung zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="a6907-404">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="a6907-405">Diese Optionen werden konfiguriert, indem ein [Delegat an \<T> maphub](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in übergeben wird `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="a6907-405">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<ChatHub>("/chathub", options =>
        {
            options.Transports =
                HttpTransportType.WebSockets |
                HttpTransportType.LongPolling;
        });
    });
}
```

<span data-ttu-id="a6907-406">In der folgenden Tabelle werden die Optionen zum Konfigurieren der SignalR erweiterten http-Optionen von ASP.net Core beschrieben:</span><span class="sxs-lookup"><span data-stu-id="a6907-406">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="a6907-407">Option</span><span class="sxs-lookup"><span data-stu-id="a6907-407">Option</span></span> | <span data-ttu-id="a6907-408">Standardwert</span><span class="sxs-lookup"><span data-stu-id="a6907-408">Default Value</span></span> | <span data-ttu-id="a6907-409">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="a6907-409">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="a6907-410">32 KB</span><span class="sxs-lookup"><span data-stu-id="a6907-410">32 KB</span></span> | <span data-ttu-id="a6907-411">Die maximale Anzahl von Bytes, die vom Client vor dem Anwenden des backdrucks vom Server gepuffert werden.</span><span class="sxs-lookup"><span data-stu-id="a6907-411">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="a6907-412">Wenn Sie diesen Wert erhöhen, kann der Server größere Nachrichten schneller empfangen, ohne dass ein Rückdruck angewendet wird, der Arbeitsspeicher Verbrauch kann jedoch gesteigert werden.</span><span class="sxs-lookup"><span data-stu-id="a6907-412">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="a6907-413">Daten, die automatisch von den `Authorize` auf die Hub-Klasse angewendeten Attributen gesammelt werden.</span><span class="sxs-lookup"><span data-stu-id="a6907-413">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="a6907-414">Eine Liste von [iautorizedata](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) -Objekten, die verwendet werden, um zu bestimmen, ob ein Client für die Verbindung mit dem Hub autorisiert ist.</span><span class="sxs-lookup"><span data-stu-id="a6907-414">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="a6907-415">32 KB</span><span class="sxs-lookup"><span data-stu-id="a6907-415">32 KB</span></span> | <span data-ttu-id="a6907-416">Die maximale Anzahl von Bytes, die von der APP gesendet werden, die der Server puffert, bevor der Rückdruck beobachtet wird</span><span class="sxs-lookup"><span data-stu-id="a6907-416">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="a6907-417">Wenn Sie diesen Wert erhöhen, kann der Server größere Nachrichten schneller Puffern, ohne dass auf den Rückstand gewartet wird, aber die Arbeitsspeicher Nutzung erhöhen kann.</span><span class="sxs-lookup"><span data-stu-id="a6907-417">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="a6907-418">Alle Transporte sind aktiviert.</span><span class="sxs-lookup"><span data-stu-id="a6907-418">All Transports are enabled.</span></span> | <span data-ttu-id="a6907-419">Ein Bitflags-Enumeration von `HttpTransportType` Werten, die die Transporte einschränken können, die ein Client für die Verbindungs Herstellung verwenden kann.</span><span class="sxs-lookup"><span data-stu-id="a6907-419">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="a6907-420">Siehe unten.</span><span class="sxs-lookup"><span data-stu-id="a6907-420">See below.</span></span> | <span data-ttu-id="a6907-421">Zusätzliche Optionen, die für den langen Abruf Transport spezifisch sind.</span><span class="sxs-lookup"><span data-stu-id="a6907-421">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="a6907-422">Siehe unten.</span><span class="sxs-lookup"><span data-stu-id="a6907-422">See below.</span></span> | <span data-ttu-id="a6907-423">Zusätzliche Optionen für den websockets-Transport.</span><span class="sxs-lookup"><span data-stu-id="a6907-423">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="a6907-424">0</span><span class="sxs-lookup"><span data-stu-id="a6907-424">0</span></span> | <span data-ttu-id="a6907-425">Geben Sie die Mindestversion des Aushandlungs Protokolls an.</span><span class="sxs-lookup"><span data-stu-id="a6907-425">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="a6907-426">Hiermit werden Clients auf neuere Versionen beschränkt.</span><span class="sxs-lookup"><span data-stu-id="a6907-426">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="a6907-427">Der lange Abruf Transport bietet zusätzliche Optionen, die mit der-Eigenschaft konfiguriert werden können `LongPolling` :</span><span class="sxs-lookup"><span data-stu-id="a6907-427">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="a6907-428">Option</span><span class="sxs-lookup"><span data-stu-id="a6907-428">Option</span></span> | <span data-ttu-id="a6907-429">Standardwert</span><span class="sxs-lookup"><span data-stu-id="a6907-429">Default Value</span></span> | <span data-ttu-id="a6907-430">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="a6907-430">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="a6907-431">90 Sekunden</span><span class="sxs-lookup"><span data-stu-id="a6907-431">90 seconds</span></span> | <span data-ttu-id="a6907-432">Die maximale Zeitspanne, die der Server wartet, bis eine Nachricht an den Client gesendet wird, bevor eine einzelne Abruf Anforderung beendet wird.</span><span class="sxs-lookup"><span data-stu-id="a6907-432">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="a6907-433">Das verringern dieses Werts bewirkt, dass der Client neue Abruf Anforderungen häufiger ausgibt.</span><span class="sxs-lookup"><span data-stu-id="a6907-433">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="a6907-434">Der WebSocket-Transport verfügt über zusätzliche Optionen, die mit der-Eigenschaft konfiguriert werden können `WebSockets` :</span><span class="sxs-lookup"><span data-stu-id="a6907-434">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="a6907-435">Option</span><span class="sxs-lookup"><span data-stu-id="a6907-435">Option</span></span> | <span data-ttu-id="a6907-436">Standardwert</span><span class="sxs-lookup"><span data-stu-id="a6907-436">Default Value</span></span> | <span data-ttu-id="a6907-437">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="a6907-437">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="a6907-438">5 Sekunden</span><span class="sxs-lookup"><span data-stu-id="a6907-438">5 seconds</span></span> | <span data-ttu-id="a6907-439">Wenn der Client nach dem Schließen des Servers nicht innerhalb dieses Zeitraums geschlossen werden kann, wird die Verbindung beendet.</span><span class="sxs-lookup"><span data-stu-id="a6907-439">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="a6907-440">Ein Delegat, der verwendet werden kann, um den- `Sec-WebSocket-Protocol` Header auf einen benutzerdefinierten Wert festzulegen.</span><span class="sxs-lookup"><span data-stu-id="a6907-440">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="a6907-441">Der Delegat empfängt die vom Client angeforderten Werte als Eingabe und erwartet, dass der gewünschte Wert zurückgegeben wird.</span><span class="sxs-lookup"><span data-stu-id="a6907-441">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="a6907-442">Konfigurieren von Clientoptionen</span><span class="sxs-lookup"><span data-stu-id="a6907-442">Configure client options</span></span>

<span data-ttu-id="a6907-443">Client Optionen können für den Typ konfiguriert werden `HubConnectionBuilder` (verfügbar in den .net-und JavaScript-Clients).</span><span class="sxs-lookup"><span data-stu-id="a6907-443">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="a6907-444">Sie steht auch im Java-Client zur Verfügung, aber die `HttpHubConnectionBuilder` Unterklasse enthält die Generator-Konfigurationsoptionen sowie die `HubConnection` selbst.</span><span class="sxs-lookup"><span data-stu-id="a6907-444">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="a6907-445">Konfigurieren der Protokollierung</span><span class="sxs-lookup"><span data-stu-id="a6907-445">Configure logging</span></span>

<span data-ttu-id="a6907-446">Die Protokollierung wird mithilfe der-Methode im .NET-Client konfiguriert `ConfigureLogging` .</span><span class="sxs-lookup"><span data-stu-id="a6907-446">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="a6907-447">Protokollierungs Anbieter und Filter können auf die gleiche Weise wie auf dem Server registriert werden.</span><span class="sxs-lookup"><span data-stu-id="a6907-447">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="a6907-448">Weitere Informationen finden Sie in der Dokumentation zur [Protokollierung ASP.net Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="a6907-448">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="a6907-449">Um Protokollierungs Anbieter zu registrieren, müssen Sie die erforderlichen Pakete installieren.</span><span class="sxs-lookup"><span data-stu-id="a6907-449">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="a6907-450">Eine vollständige Liste finden Sie im Abschnitt [integrierte Protokollierungs Anbieter](xref:fundamentals/logging/index#built-in-logging-providers) der Dokumentation.</span><span class="sxs-lookup"><span data-stu-id="a6907-450">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="a6907-451">Um z. b. die Konsolen Protokollierung zu aktivieren, installieren Sie das `Microsoft.Extensions.Logging.Console` nuget-Paket.</span><span class="sxs-lookup"><span data-stu-id="a6907-451">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="a6907-452">Die `AddConsole` Erweiterungsmethode aufzurufen:</span><span class="sxs-lookup"><span data-stu-id="a6907-452">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="a6907-453">Im JavaScript-Client ist eine ähnliche `configureLogging` Methode vorhanden.</span><span class="sxs-lookup"><span data-stu-id="a6907-453">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="a6907-454">Geben `LogLevel` Sie einen Wert an, der die minimale Ebene der zu erstellenden Protokollmeldungen angibt.</span><span class="sxs-lookup"><span data-stu-id="a6907-454">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="a6907-455">Protokolle werden in das Browser Konsolenfenster geschrieben.</span><span class="sxs-lookup"><span data-stu-id="a6907-455">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="a6907-456">Anstelle eines `LogLevel` Werts können Sie auch einen Wert angeben, der den `string` Namen einer Protokollebene darstellt.</span><span class="sxs-lookup"><span data-stu-id="a6907-456">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="a6907-457">Dies ist nützlich, wenn SignalR Sie die Protokollierung in Umgebungen konfigurieren, in denen Sie keinen Zugriff auf die `LogLevel` Konstanten haben.</span><span class="sxs-lookup"><span data-stu-id="a6907-457">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="a6907-458">In der folgenden Tabelle sind die verfügbaren Protokoll Ebenen aufgeführt.</span><span class="sxs-lookup"><span data-stu-id="a6907-458">The following table lists the available log levels.</span></span> <span data-ttu-id="a6907-459">Der von Ihnen bereitgestellte Wert, `configureLogging` der die **minimale** Protokollierung festlegt, die protokolliert werden soll.</span><span class="sxs-lookup"><span data-stu-id="a6907-459">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="a6907-460">Nachrichten, die auf dieser Ebene protokolliert werden, **oder die nach der Tabelle aufgeführten Ebenen** werden protokolliert.</span><span class="sxs-lookup"><span data-stu-id="a6907-460">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="a6907-461">String</span><span class="sxs-lookup"><span data-stu-id="a6907-461">String</span></span>                      | <span data-ttu-id="a6907-462">LogLevel</span><span class="sxs-lookup"><span data-stu-id="a6907-462">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="a6907-463">`info`**oder**`information`</span><span class="sxs-lookup"><span data-stu-id="a6907-463">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="a6907-464">`warn`**oder**`warning`</span><span class="sxs-lookup"><span data-stu-id="a6907-464">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="a6907-465">Um die Protokollierung vollständig zu deaktivieren, geben Sie `signalR.LogLevel.None` in der `configureLogging` Methode an</span><span class="sxs-lookup"><span data-stu-id="a6907-465">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="a6907-466">Weitere Informationen zur Protokollierung finden Sie in der [ SignalR Diagnose Dokumentation](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="a6907-466">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="a6907-467">Der SignalR Java-Client verwendet die [SLF4J](https://www.slf4j.org/) -Bibliothek für die Protokollierung.</span><span class="sxs-lookup"><span data-stu-id="a6907-467">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="a6907-468">Dabei handelt es sich um eine allgemeine Protokollierungs-API, die es Benutzern der Bibliothek ermöglicht, ihre eigene spezifische Protokollierungs Implementierung zu wählen, indem Sie eine bestimmte Protokollierungs Abhängigkeit einbinden.</span><span class="sxs-lookup"><span data-stu-id="a6907-468">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="a6907-469">Der folgende Code Ausschnitt zeigt, wie `java.util.logging` mit dem Java- SignalR Client verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="a6907-469">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="a6907-470">Wenn Sie die Protokollierung in ihren Abhängigkeiten nicht konfigurieren, lädt SLF4J eine Standard Protokollierung ohne Vorgang mit folgender Warnmeldung:</span><span class="sxs-lookup"><span data-stu-id="a6907-470">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="a6907-471">Dies kann sicher ignoriert werden.</span><span class="sxs-lookup"><span data-stu-id="a6907-471">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="a6907-472">Zulässige Transporte konfigurieren</span><span class="sxs-lookup"><span data-stu-id="a6907-472">Configure allowed transports</span></span>

<span data-ttu-id="a6907-473">Die von verwendeten Transporte SignalR können im-Befehl `WithUrl` ( `withUrl` in JavaScript) konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="a6907-473">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="a6907-474">Ein bitweises OR der Werte von `HttpTransportType` kann verwendet werden, um den Client so einzuschränken, dass nur die angegebenen Transporte verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="a6907-474">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="a6907-475">Alle Transporte sind standardmäßig aktiviert.</span><span class="sxs-lookup"><span data-stu-id="a6907-475">All transports are enabled by default.</span></span>

<span data-ttu-id="a6907-476">Um z. b. den Server-Sent Ereignis Transport zu deaktivieren, aber websockets und lange Abruf Verbindungen zuzulassen:</span><span class="sxs-lookup"><span data-stu-id="a6907-476">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="a6907-477">Im JavaScript-Client werden Transporte durch Festlegen des- `transport` Felds für das Options-Objekt konfiguriert, das für bereitgestellt wird `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="a6907-477">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="a6907-478">In dieser Version des Java-Clients ist websockets der einzige verfügbare Transport.</span><span class="sxs-lookup"><span data-stu-id="a6907-478">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="a6907-479">Im Java-Client wird der Transport mit der-Methode im ausgewählt `withTransport` `HttpHubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="a6907-479">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="a6907-480">Der Java-Client verwendet standardmäßig den websockets-Transport.</span><span class="sxs-lookup"><span data-stu-id="a6907-480">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="a6907-481">Der SignalR Java-Client unterstützt noch keinen Transport Fall Back.</span><span class="sxs-lookup"><span data-stu-id="a6907-481">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="a6907-482">Konfigurieren der bearerauthentifizierung</span><span class="sxs-lookup"><span data-stu-id="a6907-482">Configure bearer authentication</span></span>

<span data-ttu-id="a6907-483">Um Authentifizierungsdaten zusammen mit Anforderungen bereitzustellen SignalR , verwenden `AccessTokenProvider` Sie die-Option ( `accessTokenFactory` in JavaScript), um eine Funktion anzugeben, die das gewünschte Zugriffs Token zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="a6907-483">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="a6907-484">Im .NET-Client wird dieses Zugriffs Token als http-tokenauthentifizierungstoken (mit dem- `Authorization` Header mit dem Typ) an das Token "bearerauthentifizierung" geleitet `Bearer` .</span><span class="sxs-lookup"><span data-stu-id="a6907-484">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="a6907-485">Im JavaScript-Client wird das Zugriffs Token als bearertoken verwendet, **außer** in einigen Fällen, in denen Browser-APIs die Fähigkeit zum Anwenden von Headern einschränken (insbesondere in Server-Sent Ereignissen und websockets-Anforderungen).</span><span class="sxs-lookup"><span data-stu-id="a6907-485">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="a6907-486">In diesen Fällen wird das Zugriffs Token als Abfrage Zeichenfolgen-Wert bereitgestellt `access_token` .</span><span class="sxs-lookup"><span data-stu-id="a6907-486">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="a6907-487">Im .NET-Client kann die `AccessTokenProvider` Option mithilfe des Options Delegaten in angegeben werden `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="a6907-487">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="a6907-488">Im JavaScript-Client wird das Zugriffs Token konfiguriert, indem das- `accessTokenFactory` Feld für das Options-Objekt in festgelegt wird `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="a6907-488">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

<span data-ttu-id="a6907-489">Im SignalR Java-Client können Sie ein bearertoken für die Authentifizierung konfigurieren, indem Sie eine zugriffstokenfactory für " [httphubconnectionbuilder](/java/api/com.microsoft.signalr.httphubconnectionbuilder?view=aspnet-signalr-java)" bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="a6907-489">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr.httphubconnectionbuilder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="a6907-490">Verwenden Sie [withaccesstokenfactory](/java/api/com.microsoft.signalr.httphubconnectionbuilder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) , um eine [einzelne \<String> ](https://reactivex.io/documentation/single.html) [rxjava](https://github.com/ReactiveX/RxJava) -Instanz bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="a6907-490">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr.httphubconnectionbuilder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="a6907-491">Beim Aufrufen von " [Single.](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)Write" können Sie Logik schreiben, um Zugriffs Token für den Client zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="a6907-491">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="a6907-492">Konfigurieren von Timeout-und Keep-Alive-Optionen</span><span class="sxs-lookup"><span data-stu-id="a6907-492">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="a6907-493">Zusätzliche Optionen zum Konfigurieren von Timeout und Keep-Alive-Verhalten sind für das `HubConnection` Objekt selbst verfügbar:</span><span class="sxs-lookup"><span data-stu-id="a6907-493">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="a6907-494">.NET</span><span class="sxs-lookup"><span data-stu-id="a6907-494">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="a6907-495">Option</span><span class="sxs-lookup"><span data-stu-id="a6907-495">Option</span></span> | <span data-ttu-id="a6907-496">Standardwert</span><span class="sxs-lookup"><span data-stu-id="a6907-496">Default value</span></span> | <span data-ttu-id="a6907-497">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="a6907-497">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="a6907-498">30 Sekunden (30.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="a6907-498">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="a6907-499">Timeout für die Serveraktivität.</span><span class="sxs-lookup"><span data-stu-id="a6907-499">Timeout for server activity.</span></span> <span data-ttu-id="a6907-500">Wenn der Server in diesem Intervall keine Nachricht gesendet hat, betrachtet der Client den Server als getrennt und löst das `Closed` Ereignis `onclose` aus (in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="a6907-500">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="a6907-501">Dieser Wert muss groß genug sein, damit eine Ping-Nachricht vom Server gesendet **und** innerhalb des Timeout Intervalls vom Client empfangen wird.</span><span class="sxs-lookup"><span data-stu-id="a6907-501">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="a6907-502">Der empfohlene Wert ist eine Zahl, die mindestens dem Wert des Servers `KeepAliveInterval` entspricht, um Zeit für das Eintreffen von Pings zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="a6907-502">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="a6907-503">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="a6907-503">15 seconds</span></span> | <span data-ttu-id="a6907-504">Timeout für den anfänglichen Server Hand Shake.</span><span class="sxs-lookup"><span data-stu-id="a6907-504">Timeout for initial server handshake.</span></span> <span data-ttu-id="a6907-505">Wenn der Server in diesem Intervall keine Hand Shake Antwort sendet, bricht der Client den Handshake ab und löst das `Closed` Ereignis `onclose` aus (in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="a6907-505">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="a6907-506">Dies ist eine erweiterte Einstellung, die nur geändert werden sollte, wenn aufgrund schwerer Netzwerk Latenz Fehler aufgrund von Hand Shake Timeout auftreten.</span><span class="sxs-lookup"><span data-stu-id="a6907-506">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="a6907-507">Weitere Details zum Hand Shake Prozess finden Sie in der [ SignalR Hub-Protokollspezifikation](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="a6907-507">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="a6907-508">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="a6907-508">15 seconds</span></span> | <span data-ttu-id="a6907-509">Bestimmt das Intervall, in dem der Client Ping-Nachrichten sendet.</span><span class="sxs-lookup"><span data-stu-id="a6907-509">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="a6907-510">Beim Senden von Nachrichten vom Client wird der Timer auf den Anfang des Intervalls zurückgesetzt.</span><span class="sxs-lookup"><span data-stu-id="a6907-510">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="a6907-511">Wenn der Client keine Nachricht in der `ClientTimeoutInterval` Gruppe auf dem Server gesendet hat, wird der Client vom Server als getrennt betrachtet.</span><span class="sxs-lookup"><span data-stu-id="a6907-511">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="a6907-512">Im .NET-Client werden Timeout Werte als Werte angegeben `TimeSpan` .</span><span class="sxs-lookup"><span data-stu-id="a6907-512">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="a6907-513">JavaScript</span><span class="sxs-lookup"><span data-stu-id="a6907-513">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="a6907-514">Option</span><span class="sxs-lookup"><span data-stu-id="a6907-514">Option</span></span> | <span data-ttu-id="a6907-515">Standardwert</span><span class="sxs-lookup"><span data-stu-id="a6907-515">Default value</span></span> | <span data-ttu-id="a6907-516">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="a6907-516">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="a6907-517">30 Sekunden (30.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="a6907-517">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="a6907-518">Timeout für die Serveraktivität.</span><span class="sxs-lookup"><span data-stu-id="a6907-518">Timeout for server activity.</span></span> <span data-ttu-id="a6907-519">Wenn der Server in diesem Intervall keine Nachricht gesendet hat, betrachtet der Client den Server als getrennt und löst das `onclose` Ereignis aus.</span><span class="sxs-lookup"><span data-stu-id="a6907-519">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="a6907-520">Dieser Wert muss groß genug sein, damit eine Ping-Nachricht vom Server gesendet **und** innerhalb des Timeout Intervalls vom Client empfangen wird.</span><span class="sxs-lookup"><span data-stu-id="a6907-520">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="a6907-521">Der empfohlene Wert ist eine Zahl, die mindestens dem Wert des Servers `KeepAliveInterval` entspricht, um Zeit für das Eintreffen von Pings zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="a6907-521">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="a6907-522">15 Sekunden (15.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="a6907-522">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="a6907-523">Bestimmt das Intervall, in dem der Client Ping-Nachrichten sendet.</span><span class="sxs-lookup"><span data-stu-id="a6907-523">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="a6907-524">Beim Senden von Nachrichten vom Client wird der Timer auf den Anfang des Intervalls zurückgesetzt.</span><span class="sxs-lookup"><span data-stu-id="a6907-524">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="a6907-525">Wenn der Client keine Nachricht in der `ClientTimeoutInterval` Gruppe auf dem Server gesendet hat, wird der Client vom Server als getrennt betrachtet.</span><span class="sxs-lookup"><span data-stu-id="a6907-525">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="a6907-526">Java</span><span class="sxs-lookup"><span data-stu-id="a6907-526">Java</span></span>](#tab/java)

| <span data-ttu-id="a6907-527">Option</span><span class="sxs-lookup"><span data-stu-id="a6907-527">Option</span></span> | <span data-ttu-id="a6907-528">Standardwert</span><span class="sxs-lookup"><span data-stu-id="a6907-528">Default value</span></span> | <span data-ttu-id="a6907-529">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="a6907-529">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="a6907-530">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="a6907-530">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="a6907-531">30 Sekunden (30.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="a6907-531">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="a6907-532">Timeout für die Serveraktivität.</span><span class="sxs-lookup"><span data-stu-id="a6907-532">Timeout for server activity.</span></span> <span data-ttu-id="a6907-533">Wenn der Server in diesem Intervall keine Nachricht gesendet hat, betrachtet der Client den Server als getrennt und löst das `onClose` Ereignis aus.</span><span class="sxs-lookup"><span data-stu-id="a6907-533">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="a6907-534">Dieser Wert muss groß genug sein, damit eine Ping-Nachricht vom Server gesendet **und** innerhalb des Timeout Intervalls vom Client empfangen wird.</span><span class="sxs-lookup"><span data-stu-id="a6907-534">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="a6907-535">Der empfohlene Wert ist eine Zahl, die mindestens dem Wert des Servers `KeepAliveInterval` entspricht, um Zeit für das Eintreffen von Pings zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="a6907-535">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="a6907-536">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="a6907-536">15 seconds</span></span> | <span data-ttu-id="a6907-537">Timeout für den anfänglichen Server Hand Shake.</span><span class="sxs-lookup"><span data-stu-id="a6907-537">Timeout for initial server handshake.</span></span> <span data-ttu-id="a6907-538">Wenn der Server in diesem Intervall keine Hand Shake Antwort sendet, bricht der Client den Handshake ab und löst das- `onClose` Ereignis aus.</span><span class="sxs-lookup"><span data-stu-id="a6907-538">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="a6907-539">Dies ist eine erweiterte Einstellung, die nur geändert werden sollte, wenn aufgrund schwerer Netzwerk Latenz Fehler aufgrund von Hand Shake Timeout auftreten.</span><span class="sxs-lookup"><span data-stu-id="a6907-539">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="a6907-540">Weitere Details zum Hand Shake Prozess finden Sie in der [ SignalR Hub-Protokollspezifikation](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="a6907-540">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="a6907-541">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="a6907-541">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="a6907-542">15 Sekunden (15.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="a6907-542">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="a6907-543">Bestimmt das Intervall, in dem der Client Ping-Nachrichten sendet.</span><span class="sxs-lookup"><span data-stu-id="a6907-543">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="a6907-544">Beim Senden von Nachrichten vom Client wird der Timer auf den Anfang des Intervalls zurückgesetzt.</span><span class="sxs-lookup"><span data-stu-id="a6907-544">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="a6907-545">Wenn der Client keine Nachricht in der `ClientTimeoutInterval` Gruppe auf dem Server gesendet hat, wird der Client vom Server als getrennt betrachtet.</span><span class="sxs-lookup"><span data-stu-id="a6907-545">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="a6907-546">Konfigurieren zusätzlicher Optionen</span><span class="sxs-lookup"><span data-stu-id="a6907-546">Configure additional options</span></span>

<span data-ttu-id="a6907-547">Zusätzliche Optionen können in der- `WithUrl` Methode ( `withUrl` in JavaScript) für `HubConnectionBuilder` oder für die verschiedenen Konfigurations-APIs auf dem `HttpHubConnectionBuilder` im Java-Client konfiguriert werden:</span><span class="sxs-lookup"><span data-stu-id="a6907-547">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="a6907-548">.NET</span><span class="sxs-lookup"><span data-stu-id="a6907-548">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="a6907-549">.Net-Option</span><span class="sxs-lookup"><span data-stu-id="a6907-549">.NET Option</span></span> |  <span data-ttu-id="a6907-550">Standardwert</span><span class="sxs-lookup"><span data-stu-id="a6907-550">Default value</span></span> | <span data-ttu-id="a6907-551">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="a6907-551">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="a6907-552">Eine Funktion, die eine Zeichenfolge zurückgibt, die als bearerauthentifizierungstoken in HTTP-Anforderungen bereitgestellt</span><span class="sxs-lookup"><span data-stu-id="a6907-552">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="a6907-553">Legen Sie dies auf fest, `true` um den Aushandlungs Schritt zu überspringen</span><span class="sxs-lookup"><span data-stu-id="a6907-553">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="a6907-554">Wird **nur unterstützt, wenn der websockets-Transport der einzige aktivierte Transport ist**.</span><span class="sxs-lookup"><span data-stu-id="a6907-554">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="a6907-555">Diese Einstellung kann nicht aktiviert werden, wenn der Azure-Dienst verwendet wird SignalR .</span><span class="sxs-lookup"><span data-stu-id="a6907-555">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="a6907-556">Leer</span><span class="sxs-lookup"><span data-stu-id="a6907-556">Empty</span></span> | <span data-ttu-id="a6907-557">Eine Auflistung von TLS-Zertifikaten, die zum Authentifizieren von Anforderungen gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="a6907-557">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="a6907-558">Leer</span><span class="sxs-lookup"><span data-stu-id="a6907-558">Empty</span></span> | <span data-ttu-id="a6907-559">Eine Auflistung von http- cookie s, die mit jeder HTTP-Anforderung gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="a6907-559">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="a6907-560">Leer</span><span class="sxs-lookup"><span data-stu-id="a6907-560">Empty</span></span> | <span data-ttu-id="a6907-561">Anmelde Informationen, die mit jeder HTTP-Anforderung gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="a6907-561">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="a6907-562">5 Sekunden</span><span class="sxs-lookup"><span data-stu-id="a6907-562">5 seconds</span></span> | <span data-ttu-id="a6907-563">Nur websockets.</span><span class="sxs-lookup"><span data-stu-id="a6907-563">WebSockets only.</span></span> <span data-ttu-id="a6907-564">Die maximale Zeitspanne, die der Client nach dem Schließen des Servers wartet, um die Anforderung zum Schließen zu bestätigen.</span><span class="sxs-lookup"><span data-stu-id="a6907-564">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="a6907-565">Wenn der Server die Schließung innerhalb dieses Zeitraums nicht anerkennt, trennt der Client die Verbindung.</span><span class="sxs-lookup"><span data-stu-id="a6907-565">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="a6907-566">Leer</span><span class="sxs-lookup"><span data-stu-id="a6907-566">Empty</span></span> | <span data-ttu-id="a6907-567">Eine Karte mit zusätzlichen HTTP-Headern, die mit jeder HTTP-Anforderung gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="a6907-567">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="a6907-568">Ein Delegat, der verwendet werden kann, um den `HttpMessageHandler` zum Senden von HTTP-Anforderungen verwendeten zu konfigurieren oder zu ersetzen.</span><span class="sxs-lookup"><span data-stu-id="a6907-568">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="a6907-569">Wird nicht für WebSocket-Verbindungen verwendet.</span><span class="sxs-lookup"><span data-stu-id="a6907-569">Not used for WebSocket connections.</span></span> <span data-ttu-id="a6907-570">Dieser Delegat muss einen Wert zurückgeben, der nicht NULL ist, und erhält den Standardwert als Parameter.</span><span class="sxs-lookup"><span data-stu-id="a6907-570">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="a6907-571">Ändern Sie die Einstellungen für diesen Standardwert, und geben Sie ihn zurück, oder geben Sie eine neue `HttpMessageHandler` Instanz zurück.</span><span class="sxs-lookup"><span data-stu-id="a6907-571">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="a6907-572">**Wenn Sie den Handler ersetzen, stellen Sie sicher, dass die Einstellungen, die Sie beibehalten möchten, vom bereitgestellten Handler kopiert werden. Andernfalls gelten die konfigurierten Optionen (z. b. Cookie s und Header) nicht für den neuen Handler.**</span><span class="sxs-lookup"><span data-stu-id="a6907-572">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="a6907-573">Ein HTTP-Proxy, der beim Senden von HTTP-Anforderungen verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="a6907-573">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="a6907-574">Legen Sie diesen booleschen Wert fest, um die Standard Anmelde Informationen für http-und websockets-Anforderungen zu senden</span><span class="sxs-lookup"><span data-stu-id="a6907-574">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="a6907-575">Dies ermöglicht die Verwendung der Windows-Authentifizierung.</span><span class="sxs-lookup"><span data-stu-id="a6907-575">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="a6907-576">Ein Delegat, der zum Konfigurieren zusätzlicher WebSocket-Optionen verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="a6907-576">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="a6907-577">Empfängt eine Instanz von [clientwebsocketoptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) , die zum Konfigurieren der Optionen verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="a6907-577">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="a6907-578">JavaScript</span><span class="sxs-lookup"><span data-stu-id="a6907-578">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="a6907-579">JavaScript-Option</span><span class="sxs-lookup"><span data-stu-id="a6907-579">JavaScript Option</span></span> | <span data-ttu-id="a6907-580">Standardwert</span><span class="sxs-lookup"><span data-stu-id="a6907-580">Default Value</span></span> | <span data-ttu-id="a6907-581">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="a6907-581">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="a6907-582">Eine Funktion, die eine Zeichenfolge zurückgibt, die als bearerauthentifizierungstoken in HTTP-Anforderungen bereitgestellt</span><span class="sxs-lookup"><span data-stu-id="a6907-582">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `transport` | `null` | <span data-ttu-id="a6907-583">Ein- <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType> Wert, der den für die Verbindung zu verwendenden Transport angibt.</span><span class="sxs-lookup"><span data-stu-id="a6907-583">An <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType> value specifying the transport to use for the connection.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="a6907-584">Legen Sie auf fest, `true` um die Bytes/Zeichen der vom Client gesendeten und empfangenen Nachrichten zu protokollieren.</span><span class="sxs-lookup"><span data-stu-id="a6907-584">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="a6907-585">Legen Sie dies auf fest, `true` um den Aushandlungs Schritt zu überspringen</span><span class="sxs-lookup"><span data-stu-id="a6907-585">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="a6907-586">Wird **nur unterstützt, wenn der websockets-Transport der einzige aktivierte Transport ist**.</span><span class="sxs-lookup"><span data-stu-id="a6907-586">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="a6907-587">Diese Einstellung kann nicht aktiviert werden, wenn der Azure-Dienst verwendet wird SignalR .</span><span class="sxs-lookup"><span data-stu-id="a6907-587">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="a6907-588">Java</span><span class="sxs-lookup"><span data-stu-id="a6907-588">Java</span></span>](#tab/java)

| <span data-ttu-id="a6907-589">Java-Option</span><span class="sxs-lookup"><span data-stu-id="a6907-589">Java Option</span></span> | <span data-ttu-id="a6907-590">Standardwert</span><span class="sxs-lookup"><span data-stu-id="a6907-590">Default Value</span></span> | <span data-ttu-id="a6907-591">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="a6907-591">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="a6907-592">Eine Funktion, die eine Zeichenfolge zurückgibt, die als bearerauthentifizierungstoken in HTTP-Anforderungen bereitgestellt</span><span class="sxs-lookup"><span data-stu-id="a6907-592">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="a6907-593">Legen Sie dies auf fest, `true` um den Aushandlungs Schritt zu überspringen</span><span class="sxs-lookup"><span data-stu-id="a6907-593">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="a6907-594">Wird **nur unterstützt, wenn der websockets-Transport der einzige aktivierte Transport ist**.</span><span class="sxs-lookup"><span data-stu-id="a6907-594">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="a6907-595">Diese Einstellung kann nicht aktiviert werden, wenn der Azure-Dienst verwendet wird SignalR .</span><span class="sxs-lookup"><span data-stu-id="a6907-595">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="a6907-596">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="a6907-596">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="a6907-597">Leer</span><span class="sxs-lookup"><span data-stu-id="a6907-597">Empty</span></span> | <span data-ttu-id="a6907-598">Eine Karte mit zusätzlichen HTTP-Headern, die mit jeder HTTP-Anforderung gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="a6907-598">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="a6907-599">Im .NET-Client können diese Optionen durch den für bereitgestellten Options Delegaten geändert werden `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="a6907-599">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="a6907-600">Im JavaScript-Client können diese Optionen in einem JavaScript-Objekt bereitgestellt werden, das für Folgendes bereitgestellt wird `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="a6907-600">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="a6907-601">Im Java-Client können diese Optionen mit den Methoden auf der konfiguriert werden, die `HttpHubConnectionBuilder` von der `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="a6907-601">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="a6907-602">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="a6907-602">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="a6907-603">JSON/messagepack-Serialisierungsoptionen</span><span class="sxs-lookup"><span data-stu-id="a6907-603">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="a6907-604">ASP.net Core SignalR unterstützt zwei Protokolle zum Codieren von Nachrichten: [JSON](https://www.json.org/) und [messagepack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="a6907-604">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="a6907-605">Jedes Protokoll verfügt über Konfigurationsoptionen für die Serialisierung.</span><span class="sxs-lookup"><span data-stu-id="a6907-605">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="a6907-606">Die JSON-Serialisierung kann mithilfe der [addjsonprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) -Erweiterungsmethode auf dem Server konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="a6907-606">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="a6907-607">`AddJsonProtocol`kann nach [Add SignalR ](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in hinzugefügt werden `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="a6907-607">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="a6907-608">Die- `AddJsonProtocol` Methode nimmt einen Delegaten an, der ein- `options` Objekt empfängt.</span><span class="sxs-lookup"><span data-stu-id="a6907-608">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="a6907-609">Die [payloadserializeroptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) -Eigenschaft für dieses Objekt ist ein- `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> Objekt, das zum Konfigurieren der Serialisierung von Argumenten und Rückgabe Werten verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="a6907-609">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="a6907-610">Weitere Informationen finden Sie in den [System.Text.Jszur Dokumentation](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="a6907-610">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="a6907-611">Verwenden Sie beispielsweise den folgenden Code in, um das Serialisierungsprogramm so zu konfigurieren, dass die Groß-/Kleinschreibung von Eigenschaftsnamen nicht geändert wird. verwenden Sie dazu den folgenden Code in `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="a6907-611">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    });
```

<span data-ttu-id="a6907-612">Im .NET-Client ist dieselbe `AddJsonProtocol` Erweiterungsmethode auf [hubconnectionbuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)vorhanden.</span><span class="sxs-lookup"><span data-stu-id="a6907-612">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="a6907-613">Der `Microsoft.Extensions.DependencyInjection` Namespace muss importiert werden, um die Erweiterungsmethode aufzulösen:</span><span class="sxs-lookup"><span data-stu-id="a6907-613">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;

// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    })
    .Build();
```

> [!NOTE]
> <span data-ttu-id="a6907-614">Zurzeit ist es nicht möglich, JSON-Serialisierung im JavaScript-Client zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="a6907-614">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="a6907-615">Zum Newtonsoft.Jswechseln</span><span class="sxs-lookup"><span data-stu-id="a6907-615">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="a6907-616">Wenn Sie Features von benötigen `Newtonsoft.Json` , die in nicht unterstützt `System.Text.Json` werden, finden Sie weitere Informationen unter [Wechseln zu Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="a6907-616">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="a6907-617">Messagepack-Serialisierungsoptionen</span><span class="sxs-lookup"><span data-stu-id="a6907-617">MessagePack serialization options</span></span>

<span data-ttu-id="a6907-618">Die messagepack-Serialisierung kann konfiguriert werden, indem ein Delegat für den [addmessagepackprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) -Aufruf bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="a6907-618">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="a6907-619">Weitere Informationen finden Sie [unter SignalR messagepack in](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="a6907-619">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="a6907-620">Zurzeit ist es nicht möglich, die messagepack-Serialisierung im JavaScript-Client zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="a6907-620">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="a6907-621">Konfigurieren von Serveroptionen</span><span class="sxs-lookup"><span data-stu-id="a6907-621">Configure server options</span></span>

<span data-ttu-id="a6907-622">In der folgenden Tabelle werden die Optionen zum Konfigurieren von SignalR Hubs beschrieben:</span><span class="sxs-lookup"><span data-stu-id="a6907-622">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="a6907-623">Option</span><span class="sxs-lookup"><span data-stu-id="a6907-623">Option</span></span> | <span data-ttu-id="a6907-624">Standardwert</span><span class="sxs-lookup"><span data-stu-id="a6907-624">Default Value</span></span> | <span data-ttu-id="a6907-625">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="a6907-625">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="a6907-626">30 Sekunden</span><span class="sxs-lookup"><span data-stu-id="a6907-626">30 seconds</span></span> | <span data-ttu-id="a6907-627">Der Server wird vom-Client als getrennt betrachtet, wenn in diesem Intervall keine Nachricht (einschließlich Keep-Alive) empfangen wurde.</span><span class="sxs-lookup"><span data-stu-id="a6907-627">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="a6907-628">Es kann länger dauern, bis der Client als getrennt gekennzeichnet ist, weil dies implementiert ist.</span><span class="sxs-lookup"><span data-stu-id="a6907-628">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="a6907-629">Der empfohlene Wert ist Double- `KeepAliveInterval` Wert.</span><span class="sxs-lookup"><span data-stu-id="a6907-629">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="a6907-630">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="a6907-630">15 seconds</span></span> | <span data-ttu-id="a6907-631">Wenn der Client innerhalb dieses Zeitraums keine anfängliche Hand Shake Nachricht sendet, wird die Verbindung geschlossen.</span><span class="sxs-lookup"><span data-stu-id="a6907-631">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="a6907-632">Dies ist eine erweiterte Einstellung, die nur geändert werden sollte, wenn aufgrund schwerer Netzwerk Latenz Fehler aufgrund von Hand Shake Timeout auftreten.</span><span class="sxs-lookup"><span data-stu-id="a6907-632">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="a6907-633">Weitere Details zum Hand Shake Prozess finden Sie in der [ SignalR Hub-Protokollspezifikation](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="a6907-633">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="a6907-634">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="a6907-634">15 seconds</span></span> | <span data-ttu-id="a6907-635">Wenn der Server innerhalb dieses Intervalls keine Nachricht gesendet hat, wird automatisch eine Ping-Nachricht gesendet, um die Verbindung offen zu halten.</span><span class="sxs-lookup"><span data-stu-id="a6907-635">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="a6907-636">Ändern Sie `KeepAliveInterval` die `ServerTimeout` / `serverTimeoutInMilliseconds` Einstellung auf dem Client, wenn Sie sich ändern.</span><span class="sxs-lookup"><span data-stu-id="a6907-636">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="a6907-637">Der empfohlene `ServerTimeout` / `serverTimeoutInMilliseconds` Wert ist Double- `KeepAliveInterval` Wert.</span><span class="sxs-lookup"><span data-stu-id="a6907-637">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="a6907-638">Alle installierten Protokolle</span><span class="sxs-lookup"><span data-stu-id="a6907-638">All installed protocols</span></span> | <span data-ttu-id="a6907-639">Protokolle, die von diesem Hub unterstützt werden.</span><span class="sxs-lookup"><span data-stu-id="a6907-639">Protocols supported by this hub.</span></span> <span data-ttu-id="a6907-640">Standardmäßig sind alle auf dem Server registrierten Protokolle zulässig, aber Protokolle können aus dieser Liste entfernt werden, um bestimmte Protokolle für einzelne Hubs zu deaktivieren.</span><span class="sxs-lookup"><span data-stu-id="a6907-640">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="a6907-641">Wenn der Wert `true` ist, werden ausführliche Ausnahme Meldungen an Clients zurückgegeben, wenn eine Ausnahme in einer Hub-Methode ausgelöst wird.</span><span class="sxs-lookup"><span data-stu-id="a6907-641">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="a6907-642">Der Standardwert ist `false` , da diese Ausnahme Meldungen vertrauliche Informationen enthalten können.</span><span class="sxs-lookup"><span data-stu-id="a6907-642">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="a6907-643">Die maximale Anzahl von Elementen, die für Client-uploadstreams gepuffert werden können.</span><span class="sxs-lookup"><span data-stu-id="a6907-643">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="a6907-644">Wenn dieser Grenzwert erreicht wird, wird die Verarbeitung von Aufrufen blockiert, bis der Server streamingelemente verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="a6907-644">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="a6907-645">32 KB</span><span class="sxs-lookup"><span data-stu-id="a6907-645">32 KB</span></span> | <span data-ttu-id="a6907-646">Maximale Größe einer einzelnen eingehenden Hub-Nachricht.</span><span class="sxs-lookup"><span data-stu-id="a6907-646">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="a6907-647">Optionen können für alle Hubs konfiguriert werden, indem ein Options Delegat für den Aufruf in bereitgestellt wird `AddSignalR` `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="a6907-647">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

<span data-ttu-id="a6907-648">Optionen für einen einzelnen Hub überschreiben die in bereitgestellten globalen Optionen `AddSignalR` und können mithilfe von konfiguriert werden <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="a6907-648">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="a6907-649">Erweiterte http-Konfigurationsoptionen</span><span class="sxs-lookup"><span data-stu-id="a6907-649">Advanced HTTP configuration options</span></span>

<span data-ttu-id="a6907-650">Verwenden `HttpConnectionDispatcherOptions` Sie, um erweiterte Einstellungen im Zusammenhang mit Transporte und der Speicherpuffer Verwaltung zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="a6907-650">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="a6907-651">Diese Optionen werden konfiguriert, indem ein [Delegat an \<T> maphub](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in übergeben wird `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="a6907-651">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<ChatHub>("/chathub", options =>
        {
            options.Transports =
                HttpTransportType.WebSockets |
                HttpTransportType.LongPolling;
        });
    });
}
```

<span data-ttu-id="a6907-652">In der folgenden Tabelle werden die Optionen zum Konfigurieren der SignalR erweiterten http-Optionen von ASP.net Core beschrieben:</span><span class="sxs-lookup"><span data-stu-id="a6907-652">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="a6907-653">Option</span><span class="sxs-lookup"><span data-stu-id="a6907-653">Option</span></span> | <span data-ttu-id="a6907-654">Standardwert</span><span class="sxs-lookup"><span data-stu-id="a6907-654">Default Value</span></span> | <span data-ttu-id="a6907-655">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="a6907-655">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="a6907-656">32 KB</span><span class="sxs-lookup"><span data-stu-id="a6907-656">32 KB</span></span> | <span data-ttu-id="a6907-657">Die maximale Anzahl von Bytes, die vom Client vor dem Anwenden des backdrucks vom Server gepuffert werden.</span><span class="sxs-lookup"><span data-stu-id="a6907-657">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="a6907-658">Wenn Sie diesen Wert erhöhen, kann der Server größere Nachrichten schneller empfangen, ohne dass ein Rückdruck angewendet wird, der Arbeitsspeicher Verbrauch kann jedoch gesteigert werden.</span><span class="sxs-lookup"><span data-stu-id="a6907-658">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="a6907-659">Daten, die automatisch von den `Authorize` auf die Hub-Klasse angewendeten Attributen gesammelt werden.</span><span class="sxs-lookup"><span data-stu-id="a6907-659">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="a6907-660">Eine Liste von [iautorizedata](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) -Objekten, die verwendet werden, um zu bestimmen, ob ein Client für die Verbindung mit dem Hub autorisiert ist.</span><span class="sxs-lookup"><span data-stu-id="a6907-660">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="a6907-661">32 KB</span><span class="sxs-lookup"><span data-stu-id="a6907-661">32 KB</span></span> | <span data-ttu-id="a6907-662">Die maximale Anzahl von Bytes, die von der APP gesendet werden, die der Server puffert, bevor der Rückdruck beobachtet wird</span><span class="sxs-lookup"><span data-stu-id="a6907-662">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="a6907-663">Wenn Sie diesen Wert erhöhen, kann der Server größere Nachrichten schneller Puffern, ohne dass auf den Rückstand gewartet wird, aber die Arbeitsspeicher Nutzung erhöhen kann.</span><span class="sxs-lookup"><span data-stu-id="a6907-663">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="a6907-664">Alle Transporte sind aktiviert.</span><span class="sxs-lookup"><span data-stu-id="a6907-664">All Transports are enabled.</span></span> | <span data-ttu-id="a6907-665">Ein Bitflags-Enumeration von `HttpTransportType` Werten, die die Transporte einschränken können, die ein Client für die Verbindungs Herstellung verwenden kann.</span><span class="sxs-lookup"><span data-stu-id="a6907-665">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="a6907-666">Siehe unten.</span><span class="sxs-lookup"><span data-stu-id="a6907-666">See below.</span></span> | <span data-ttu-id="a6907-667">Zusätzliche Optionen, die für den langen Abruf Transport spezifisch sind.</span><span class="sxs-lookup"><span data-stu-id="a6907-667">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="a6907-668">Siehe unten.</span><span class="sxs-lookup"><span data-stu-id="a6907-668">See below.</span></span> | <span data-ttu-id="a6907-669">Zusätzliche Optionen für den websockets-Transport.</span><span class="sxs-lookup"><span data-stu-id="a6907-669">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="a6907-670">Der lange Abruf Transport bietet zusätzliche Optionen, die mit der-Eigenschaft konfiguriert werden können `LongPolling` :</span><span class="sxs-lookup"><span data-stu-id="a6907-670">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="a6907-671">Option</span><span class="sxs-lookup"><span data-stu-id="a6907-671">Option</span></span> | <span data-ttu-id="a6907-672">Standardwert</span><span class="sxs-lookup"><span data-stu-id="a6907-672">Default Value</span></span> | <span data-ttu-id="a6907-673">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="a6907-673">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="a6907-674">90 Sekunden</span><span class="sxs-lookup"><span data-stu-id="a6907-674">90 seconds</span></span> | <span data-ttu-id="a6907-675">Die maximale Zeitspanne, die der Server wartet, bis eine Nachricht an den Client gesendet wird, bevor eine einzelne Abruf Anforderung beendet wird.</span><span class="sxs-lookup"><span data-stu-id="a6907-675">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="a6907-676">Das verringern dieses Werts bewirkt, dass der Client neue Abruf Anforderungen häufiger ausgibt.</span><span class="sxs-lookup"><span data-stu-id="a6907-676">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="a6907-677">Der WebSocket-Transport verfügt über zusätzliche Optionen, die mit der-Eigenschaft konfiguriert werden können `WebSockets` :</span><span class="sxs-lookup"><span data-stu-id="a6907-677">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="a6907-678">Option</span><span class="sxs-lookup"><span data-stu-id="a6907-678">Option</span></span> | <span data-ttu-id="a6907-679">Standardwert</span><span class="sxs-lookup"><span data-stu-id="a6907-679">Default Value</span></span> | <span data-ttu-id="a6907-680">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="a6907-680">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="a6907-681">5 Sekunden</span><span class="sxs-lookup"><span data-stu-id="a6907-681">5 seconds</span></span> | <span data-ttu-id="a6907-682">Wenn der Client nach dem Schließen des Servers nicht innerhalb dieses Zeitraums geschlossen werden kann, wird die Verbindung beendet.</span><span class="sxs-lookup"><span data-stu-id="a6907-682">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="a6907-683">Ein Delegat, der verwendet werden kann, um den- `Sec-WebSocket-Protocol` Header auf einen benutzerdefinierten Wert festzulegen.</span><span class="sxs-lookup"><span data-stu-id="a6907-683">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="a6907-684">Der Delegat empfängt die vom Client angeforderten Werte als Eingabe und erwartet, dass der gewünschte Wert zurückgegeben wird.</span><span class="sxs-lookup"><span data-stu-id="a6907-684">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="a6907-685">Konfigurieren von Clientoptionen</span><span class="sxs-lookup"><span data-stu-id="a6907-685">Configure client options</span></span>

<span data-ttu-id="a6907-686">Client Optionen können für den Typ konfiguriert werden `HubConnectionBuilder` (verfügbar in den .net-und JavaScript-Clients).</span><span class="sxs-lookup"><span data-stu-id="a6907-686">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="a6907-687">Sie steht auch im Java-Client zur Verfügung, aber die `HttpHubConnectionBuilder` Unterklasse enthält die Generator-Konfigurationsoptionen sowie die `HubConnection` selbst.</span><span class="sxs-lookup"><span data-stu-id="a6907-687">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="a6907-688">Konfigurieren der Protokollierung</span><span class="sxs-lookup"><span data-stu-id="a6907-688">Configure logging</span></span>

<span data-ttu-id="a6907-689">Die Protokollierung wird mithilfe der-Methode im .NET-Client konfiguriert `ConfigureLogging` .</span><span class="sxs-lookup"><span data-stu-id="a6907-689">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="a6907-690">Protokollierungs Anbieter und Filter können auf die gleiche Weise wie auf dem Server registriert werden.</span><span class="sxs-lookup"><span data-stu-id="a6907-690">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="a6907-691">Weitere Informationen finden Sie in der Dokumentation zur [Protokollierung ASP.net Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="a6907-691">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="a6907-692">Um Protokollierungs Anbieter zu registrieren, müssen Sie die erforderlichen Pakete installieren.</span><span class="sxs-lookup"><span data-stu-id="a6907-692">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="a6907-693">Eine vollständige Liste finden Sie im Abschnitt [integrierte Protokollierungs Anbieter](xref:fundamentals/logging/index#built-in-logging-providers) der Dokumentation.</span><span class="sxs-lookup"><span data-stu-id="a6907-693">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="a6907-694">Um z. b. die Konsolen Protokollierung zu aktivieren, installieren Sie das `Microsoft.Extensions.Logging.Console` nuget-Paket.</span><span class="sxs-lookup"><span data-stu-id="a6907-694">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="a6907-695">Die `AddConsole` Erweiterungsmethode aufzurufen:</span><span class="sxs-lookup"><span data-stu-id="a6907-695">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="a6907-696">Im JavaScript-Client ist eine ähnliche `configureLogging` Methode vorhanden.</span><span class="sxs-lookup"><span data-stu-id="a6907-696">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="a6907-697">Geben `LogLevel` Sie einen Wert an, der die minimale Ebene der zu erstellenden Protokollmeldungen angibt.</span><span class="sxs-lookup"><span data-stu-id="a6907-697">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="a6907-698">Protokolle werden in das Browser Konsolenfenster geschrieben.</span><span class="sxs-lookup"><span data-stu-id="a6907-698">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="a6907-699">Anstelle eines `LogLevel` Werts können Sie auch einen Wert angeben, der den `string` Namen einer Protokollebene darstellt.</span><span class="sxs-lookup"><span data-stu-id="a6907-699">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="a6907-700">Dies ist nützlich, wenn SignalR Sie die Protokollierung in Umgebungen konfigurieren, in denen Sie keinen Zugriff auf die `LogLevel` Konstanten haben.</span><span class="sxs-lookup"><span data-stu-id="a6907-700">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="a6907-701">In der folgenden Tabelle sind die verfügbaren Protokoll Ebenen aufgeführt.</span><span class="sxs-lookup"><span data-stu-id="a6907-701">The following table lists the available log levels.</span></span> <span data-ttu-id="a6907-702">Der von Ihnen bereitgestellte Wert, `configureLogging` der die **minimale** Protokollierung festlegt, die protokolliert werden soll.</span><span class="sxs-lookup"><span data-stu-id="a6907-702">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="a6907-703">Nachrichten, die auf dieser Ebene protokolliert werden, **oder die nach der Tabelle aufgeführten Ebenen** werden protokolliert.</span><span class="sxs-lookup"><span data-stu-id="a6907-703">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="a6907-704">String</span><span class="sxs-lookup"><span data-stu-id="a6907-704">String</span></span>                      | <span data-ttu-id="a6907-705">LogLevel</span><span class="sxs-lookup"><span data-stu-id="a6907-705">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="a6907-706">`info`**oder**`information`</span><span class="sxs-lookup"><span data-stu-id="a6907-706">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="a6907-707">`warn`**oder**`warning`</span><span class="sxs-lookup"><span data-stu-id="a6907-707">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="a6907-708">Um die Protokollierung vollständig zu deaktivieren, geben Sie `signalR.LogLevel.None` in der `configureLogging` Methode an</span><span class="sxs-lookup"><span data-stu-id="a6907-708">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="a6907-709">Weitere Informationen zur Protokollierung finden Sie in der [ SignalR Diagnose Dokumentation](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="a6907-709">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="a6907-710">Der SignalR Java-Client verwendet die [SLF4J](https://www.slf4j.org/) -Bibliothek für die Protokollierung.</span><span class="sxs-lookup"><span data-stu-id="a6907-710">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="a6907-711">Dabei handelt es sich um eine allgemeine Protokollierungs-API, die es Benutzern der Bibliothek ermöglicht, ihre eigene spezifische Protokollierungs Implementierung zu wählen, indem Sie eine bestimmte Protokollierungs Abhängigkeit einbinden.</span><span class="sxs-lookup"><span data-stu-id="a6907-711">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="a6907-712">Der folgende Code Ausschnitt zeigt, wie `java.util.logging` mit dem Java- SignalR Client verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="a6907-712">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="a6907-713">Wenn Sie die Protokollierung in ihren Abhängigkeiten nicht konfigurieren, lädt SLF4J eine Standard Protokollierung ohne Vorgang mit folgender Warnmeldung:</span><span class="sxs-lookup"><span data-stu-id="a6907-713">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="a6907-714">Dies kann sicher ignoriert werden.</span><span class="sxs-lookup"><span data-stu-id="a6907-714">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="a6907-715">Zulässige Transporte konfigurieren</span><span class="sxs-lookup"><span data-stu-id="a6907-715">Configure allowed transports</span></span>

<span data-ttu-id="a6907-716">Die von verwendeten Transporte SignalR können im-Befehl `WithUrl` ( `withUrl` in JavaScript) konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="a6907-716">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="a6907-717">Ein bitweises OR der Werte von `HttpTransportType` kann verwendet werden, um den Client so einzuschränken, dass nur die angegebenen Transporte verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="a6907-717">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="a6907-718">Alle Transporte sind standardmäßig aktiviert.</span><span class="sxs-lookup"><span data-stu-id="a6907-718">All transports are enabled by default.</span></span>

<span data-ttu-id="a6907-719">Um z. b. den Server-Sent Ereignis Transport zu deaktivieren, aber websockets und lange Abruf Verbindungen zuzulassen:</span><span class="sxs-lookup"><span data-stu-id="a6907-719">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="a6907-720">Im JavaScript-Client werden Transporte durch Festlegen des- `transport` Felds für das Options-Objekt konfiguriert, das für bereitgestellt wird `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="a6907-720">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="a6907-721">In dieser Version des Java-Clients ist websockets der einzige verfügbare Transport.</span><span class="sxs-lookup"><span data-stu-id="a6907-721">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="a6907-722">Im Java-Client wird der Transport mit der-Methode im ausgewählt `withTransport` `HttpHubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="a6907-722">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="a6907-723">Der Java-Client verwendet standardmäßig den websockets-Transport.</span><span class="sxs-lookup"><span data-stu-id="a6907-723">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="a6907-724">Der SignalR Java-Client unterstützt noch keinen Transport Fall Back.</span><span class="sxs-lookup"><span data-stu-id="a6907-724">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="a6907-725">Konfigurieren der bearerauthentifizierung</span><span class="sxs-lookup"><span data-stu-id="a6907-725">Configure bearer authentication</span></span>

<span data-ttu-id="a6907-726">Um Authentifizierungsdaten zusammen mit Anforderungen bereitzustellen SignalR , verwenden `AccessTokenProvider` Sie die-Option ( `accessTokenFactory` in JavaScript), um eine Funktion anzugeben, die das gewünschte Zugriffs Token zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="a6907-726">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="a6907-727">Im .NET-Client wird dieses Zugriffs Token als http-tokenauthentifizierungstoken (mit dem- `Authorization` Header mit dem Typ) an das Token "bearerauthentifizierung" geleitet `Bearer` .</span><span class="sxs-lookup"><span data-stu-id="a6907-727">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="a6907-728">Im JavaScript-Client wird das Zugriffs Token als bearertoken verwendet, **außer** in einigen Fällen, in denen Browser-APIs die Fähigkeit zum Anwenden von Headern einschränken (insbesondere in Server-Sent Ereignissen und websockets-Anforderungen).</span><span class="sxs-lookup"><span data-stu-id="a6907-728">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="a6907-729">In diesen Fällen wird das Zugriffs Token als Abfrage Zeichenfolgen-Wert bereitgestellt `access_token` .</span><span class="sxs-lookup"><span data-stu-id="a6907-729">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="a6907-730">Im .NET-Client kann die `AccessTokenProvider` Option mithilfe des Options Delegaten in angegeben werden `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="a6907-730">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="a6907-731">Im JavaScript-Client wird das Zugriffs Token konfiguriert, indem das- `accessTokenFactory` Feld für das Options-Objekt in festgelegt wird `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="a6907-731">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

<span data-ttu-id="a6907-732">Im SignalR Java-Client können Sie ein bearertoken für die Authentifizierung konfigurieren, indem Sie eine zugriffstokenfactory für " [httphubconnectionbuilder](/java/api/com.microsoft.signalr.httphubconnectionbuilder?view=aspnet-signalr-java)" bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="a6907-732">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr.httphubconnectionbuilder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="a6907-733">Verwenden Sie [withaccesstokenfactory](/java/api/com.microsoft.signalr.httphubconnectionbuilder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) , um eine [einzelne \<String> ](https://reactivex.io/documentation/single.html) [rxjava](https://github.com/ReactiveX/RxJava) -Instanz bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="a6907-733">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr.httphubconnectionbuilder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="a6907-734">Beim Aufrufen von " [Single.](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)Write" können Sie Logik schreiben, um Zugriffs Token für den Client zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="a6907-734">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="a6907-735">Konfigurieren von Timeout-und Keep-Alive-Optionen</span><span class="sxs-lookup"><span data-stu-id="a6907-735">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="a6907-736">Zusätzliche Optionen zum Konfigurieren von Timeout und Keep-Alive-Verhalten sind für das `HubConnection` Objekt selbst verfügbar:</span><span class="sxs-lookup"><span data-stu-id="a6907-736">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="a6907-737">.NET</span><span class="sxs-lookup"><span data-stu-id="a6907-737">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="a6907-738">Option</span><span class="sxs-lookup"><span data-stu-id="a6907-738">Option</span></span> | <span data-ttu-id="a6907-739">Standardwert</span><span class="sxs-lookup"><span data-stu-id="a6907-739">Default value</span></span> | <span data-ttu-id="a6907-740">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="a6907-740">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="a6907-741">30 Sekunden (30.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="a6907-741">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="a6907-742">Timeout für die Serveraktivität.</span><span class="sxs-lookup"><span data-stu-id="a6907-742">Timeout for server activity.</span></span> <span data-ttu-id="a6907-743">Wenn der Server in diesem Intervall keine Nachricht gesendet hat, betrachtet der Client den Server als getrennt und löst das `Closed` Ereignis `onclose` aus (in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="a6907-743">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="a6907-744">Dieser Wert muss groß genug sein, damit eine Ping-Nachricht vom Server gesendet **und** innerhalb des Timeout Intervalls vom Client empfangen wird.</span><span class="sxs-lookup"><span data-stu-id="a6907-744">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="a6907-745">Der empfohlene Wert ist eine Zahl, die mindestens dem Wert des Servers `KeepAliveInterval` entspricht, um Zeit für das Eintreffen von Pings zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="a6907-745">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="a6907-746">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="a6907-746">15 seconds</span></span> | <span data-ttu-id="a6907-747">Timeout für den anfänglichen Server Hand Shake.</span><span class="sxs-lookup"><span data-stu-id="a6907-747">Timeout for initial server handshake.</span></span> <span data-ttu-id="a6907-748">Wenn der Server in diesem Intervall keine Hand Shake Antwort sendet, bricht der Client den Handshake ab und löst das `Closed` Ereignis `onclose` aus (in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="a6907-748">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="a6907-749">Dies ist eine erweiterte Einstellung, die nur geändert werden sollte, wenn aufgrund schwerer Netzwerk Latenz Fehler aufgrund von Hand Shake Timeout auftreten.</span><span class="sxs-lookup"><span data-stu-id="a6907-749">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="a6907-750">Weitere Details zum Hand Shake Prozess finden Sie in der [ SignalR Hub-Protokollspezifikation](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="a6907-750">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="a6907-751">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="a6907-751">15 seconds</span></span> | <span data-ttu-id="a6907-752">Bestimmt das Intervall, in dem der Client Ping-Nachrichten sendet.</span><span class="sxs-lookup"><span data-stu-id="a6907-752">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="a6907-753">Beim Senden von Nachrichten vom Client wird der Timer auf den Anfang des Intervalls zurückgesetzt.</span><span class="sxs-lookup"><span data-stu-id="a6907-753">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="a6907-754">Wenn der Client keine Nachricht in der `ClientTimeoutInterval` Gruppe auf dem Server gesendet hat, wird der Client vom Server als getrennt betrachtet.</span><span class="sxs-lookup"><span data-stu-id="a6907-754">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="a6907-755">Im .NET-Client werden Timeout Werte als Werte angegeben `TimeSpan` .</span><span class="sxs-lookup"><span data-stu-id="a6907-755">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="a6907-756">JavaScript</span><span class="sxs-lookup"><span data-stu-id="a6907-756">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="a6907-757">Option</span><span class="sxs-lookup"><span data-stu-id="a6907-757">Option</span></span> | <span data-ttu-id="a6907-758">Standardwert</span><span class="sxs-lookup"><span data-stu-id="a6907-758">Default value</span></span> | <span data-ttu-id="a6907-759">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="a6907-759">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="a6907-760">30 Sekunden (30.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="a6907-760">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="a6907-761">Timeout für die Serveraktivität.</span><span class="sxs-lookup"><span data-stu-id="a6907-761">Timeout for server activity.</span></span> <span data-ttu-id="a6907-762">Wenn der Server in diesem Intervall keine Nachricht gesendet hat, betrachtet der Client den Server als getrennt und löst das `onclose` Ereignis aus.</span><span class="sxs-lookup"><span data-stu-id="a6907-762">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="a6907-763">Dieser Wert muss groß genug sein, damit eine Ping-Nachricht vom Server gesendet **und** innerhalb des Timeout Intervalls vom Client empfangen wird.</span><span class="sxs-lookup"><span data-stu-id="a6907-763">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="a6907-764">Der empfohlene Wert ist eine Zahl, die mindestens dem Wert des Servers `KeepAliveInterval` entspricht, um Zeit für das Eintreffen von Pings zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="a6907-764">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="a6907-765">15 Sekunden (15.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="a6907-765">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="a6907-766">Bestimmt das Intervall, in dem der Client Ping-Nachrichten sendet.</span><span class="sxs-lookup"><span data-stu-id="a6907-766">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="a6907-767">Beim Senden von Nachrichten vom Client wird der Timer auf den Anfang des Intervalls zurückgesetzt.</span><span class="sxs-lookup"><span data-stu-id="a6907-767">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="a6907-768">Wenn der Client keine Nachricht in der `ClientTimeoutInterval` Gruppe auf dem Server gesendet hat, wird der Client vom Server als getrennt betrachtet.</span><span class="sxs-lookup"><span data-stu-id="a6907-768">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="a6907-769">Java</span><span class="sxs-lookup"><span data-stu-id="a6907-769">Java</span></span>](#tab/java)

| <span data-ttu-id="a6907-770">Option</span><span class="sxs-lookup"><span data-stu-id="a6907-770">Option</span></span> | <span data-ttu-id="a6907-771">Standardwert</span><span class="sxs-lookup"><span data-stu-id="a6907-771">Default value</span></span> | <span data-ttu-id="a6907-772">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="a6907-772">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="a6907-773">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="a6907-773">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="a6907-774">30 Sekunden (30.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="a6907-774">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="a6907-775">Timeout für die Serveraktivität.</span><span class="sxs-lookup"><span data-stu-id="a6907-775">Timeout for server activity.</span></span> <span data-ttu-id="a6907-776">Wenn der Server in diesem Intervall keine Nachricht gesendet hat, betrachtet der Client den Server als getrennt und löst das `onClose` Ereignis aus.</span><span class="sxs-lookup"><span data-stu-id="a6907-776">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="a6907-777">Dieser Wert muss groß genug sein, damit eine Ping-Nachricht vom Server gesendet **und** innerhalb des Timeout Intervalls vom Client empfangen wird.</span><span class="sxs-lookup"><span data-stu-id="a6907-777">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="a6907-778">Der empfohlene Wert ist eine Zahl, die mindestens dem Wert des Servers `KeepAliveInterval` entspricht, um Zeit für das Eintreffen von Pings zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="a6907-778">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="a6907-779">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="a6907-779">15 seconds</span></span> | <span data-ttu-id="a6907-780">Timeout für den anfänglichen Server Hand Shake.</span><span class="sxs-lookup"><span data-stu-id="a6907-780">Timeout for initial server handshake.</span></span> <span data-ttu-id="a6907-781">Wenn der Server in diesem Intervall keine Hand Shake Antwort sendet, bricht der Client den Handshake ab und löst das- `onClose` Ereignis aus.</span><span class="sxs-lookup"><span data-stu-id="a6907-781">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="a6907-782">Dies ist eine erweiterte Einstellung, die nur geändert werden sollte, wenn aufgrund schwerer Netzwerk Latenz Fehler aufgrund von Hand Shake Timeout auftreten.</span><span class="sxs-lookup"><span data-stu-id="a6907-782">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="a6907-783">Weitere Details zum Hand Shake Prozess finden Sie in der [ SignalR Hub-Protokollspezifikation](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="a6907-783">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="a6907-784">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="a6907-784">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="a6907-785">15 Sekunden (15.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="a6907-785">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="a6907-786">Bestimmt das Intervall, in dem der Client Ping-Nachrichten sendet.</span><span class="sxs-lookup"><span data-stu-id="a6907-786">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="a6907-787">Beim Senden von Nachrichten vom Client wird der Timer auf den Anfang des Intervalls zurückgesetzt.</span><span class="sxs-lookup"><span data-stu-id="a6907-787">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="a6907-788">Wenn der Client keine Nachricht in der `ClientTimeoutInterval` Gruppe auf dem Server gesendet hat, wird der Client vom Server als getrennt betrachtet.</span><span class="sxs-lookup"><span data-stu-id="a6907-788">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="a6907-789">Konfigurieren zusätzlicher Optionen</span><span class="sxs-lookup"><span data-stu-id="a6907-789">Configure additional options</span></span>

<span data-ttu-id="a6907-790">Zusätzliche Optionen können in der- `WithUrl` Methode ( `withUrl` in JavaScript) für `HubConnectionBuilder` oder für die verschiedenen Konfigurations-APIs auf dem `HttpHubConnectionBuilder` im Java-Client konfiguriert werden:</span><span class="sxs-lookup"><span data-stu-id="a6907-790">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="a6907-791">.NET</span><span class="sxs-lookup"><span data-stu-id="a6907-791">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="a6907-792">.Net-Option</span><span class="sxs-lookup"><span data-stu-id="a6907-792">.NET Option</span></span> |  <span data-ttu-id="a6907-793">Standardwert</span><span class="sxs-lookup"><span data-stu-id="a6907-793">Default value</span></span> | <span data-ttu-id="a6907-794">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="a6907-794">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="a6907-795">Eine Funktion, die eine Zeichenfolge zurückgibt, die als bearerauthentifizierungstoken in HTTP-Anforderungen bereitgestellt</span><span class="sxs-lookup"><span data-stu-id="a6907-795">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="a6907-796">Legen Sie dies auf fest, `true` um den Aushandlungs Schritt zu überspringen</span><span class="sxs-lookup"><span data-stu-id="a6907-796">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="a6907-797">Wird **nur unterstützt, wenn der websockets-Transport der einzige aktivierte Transport ist**.</span><span class="sxs-lookup"><span data-stu-id="a6907-797">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="a6907-798">Diese Einstellung kann nicht aktiviert werden, wenn der Azure-Dienst verwendet wird SignalR .</span><span class="sxs-lookup"><span data-stu-id="a6907-798">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="a6907-799">Leer</span><span class="sxs-lookup"><span data-stu-id="a6907-799">Empty</span></span> | <span data-ttu-id="a6907-800">Eine Auflistung von TLS-Zertifikaten, die zum Authentifizieren von Anforderungen gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="a6907-800">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="a6907-801">Leer</span><span class="sxs-lookup"><span data-stu-id="a6907-801">Empty</span></span> | <span data-ttu-id="a6907-802">Eine Auflistung von http- cookie s, die mit jeder HTTP-Anforderung gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="a6907-802">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="a6907-803">Leer</span><span class="sxs-lookup"><span data-stu-id="a6907-803">Empty</span></span> | <span data-ttu-id="a6907-804">Anmelde Informationen, die mit jeder HTTP-Anforderung gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="a6907-804">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="a6907-805">5 Sekunden</span><span class="sxs-lookup"><span data-stu-id="a6907-805">5 seconds</span></span> | <span data-ttu-id="a6907-806">Nur websockets.</span><span class="sxs-lookup"><span data-stu-id="a6907-806">WebSockets only.</span></span> <span data-ttu-id="a6907-807">Die maximale Zeitspanne, die der Client nach dem Schließen des Servers wartet, um die Anforderung zum Schließen zu bestätigen.</span><span class="sxs-lookup"><span data-stu-id="a6907-807">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="a6907-808">Wenn der Server die Schließung innerhalb dieses Zeitraums nicht anerkennt, trennt der Client die Verbindung.</span><span class="sxs-lookup"><span data-stu-id="a6907-808">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="a6907-809">Leer</span><span class="sxs-lookup"><span data-stu-id="a6907-809">Empty</span></span> | <span data-ttu-id="a6907-810">Eine Karte mit zusätzlichen HTTP-Headern, die mit jeder HTTP-Anforderung gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="a6907-810">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="a6907-811">Ein Delegat, der verwendet werden kann, um den `HttpMessageHandler` zum Senden von HTTP-Anforderungen verwendeten zu konfigurieren oder zu ersetzen.</span><span class="sxs-lookup"><span data-stu-id="a6907-811">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="a6907-812">Wird nicht für WebSocket-Verbindungen verwendet.</span><span class="sxs-lookup"><span data-stu-id="a6907-812">Not used for WebSocket connections.</span></span> <span data-ttu-id="a6907-813">Dieser Delegat muss einen Wert zurückgeben, der nicht NULL ist, und erhält den Standardwert als Parameter.</span><span class="sxs-lookup"><span data-stu-id="a6907-813">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="a6907-814">Ändern Sie die Einstellungen für diesen Standardwert, und geben Sie ihn zurück, oder geben Sie eine neue `HttpMessageHandler` Instanz zurück.</span><span class="sxs-lookup"><span data-stu-id="a6907-814">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="a6907-815">**Wenn Sie den Handler ersetzen, stellen Sie sicher, dass die Einstellungen, die Sie beibehalten möchten, vom bereitgestellten Handler kopiert werden. Andernfalls gelten die konfigurierten Optionen (z. b. Cookie s und Header) nicht für den neuen Handler.**</span><span class="sxs-lookup"><span data-stu-id="a6907-815">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="a6907-816">Ein HTTP-Proxy, der beim Senden von HTTP-Anforderungen verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="a6907-816">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="a6907-817">Legen Sie diesen booleschen Wert fest, um die Standard Anmelde Informationen für http-und websockets-Anforderungen zu senden</span><span class="sxs-lookup"><span data-stu-id="a6907-817">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="a6907-818">Dies ermöglicht die Verwendung der Windows-Authentifizierung.</span><span class="sxs-lookup"><span data-stu-id="a6907-818">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="a6907-819">Ein Delegat, der zum Konfigurieren zusätzlicher WebSocket-Optionen verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="a6907-819">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="a6907-820">Empfängt eine Instanz von [clientwebsocketoptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) , die zum Konfigurieren der Optionen verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="a6907-820">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="a6907-821">JavaScript</span><span class="sxs-lookup"><span data-stu-id="a6907-821">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="a6907-822">JavaScript-Option</span><span class="sxs-lookup"><span data-stu-id="a6907-822">JavaScript Option</span></span> | <span data-ttu-id="a6907-823">Standardwert</span><span class="sxs-lookup"><span data-stu-id="a6907-823">Default Value</span></span> | <span data-ttu-id="a6907-824">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="a6907-824">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="a6907-825">Eine Funktion, die eine Zeichenfolge zurückgibt, die als bearerauthentifizierungstoken in HTTP-Anforderungen bereitgestellt</span><span class="sxs-lookup"><span data-stu-id="a6907-825">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `transport` | `null` | <span data-ttu-id="a6907-826">Ein- <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType> Wert, der den für die Verbindung zu verwendenden Transport angibt.</span><span class="sxs-lookup"><span data-stu-id="a6907-826">An <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType> value specifying the transport to use for the connection.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="a6907-827">Legen Sie auf fest, `true` um die Bytes/Zeichen der vom Client gesendeten und empfangenen Nachrichten zu protokollieren.</span><span class="sxs-lookup"><span data-stu-id="a6907-827">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="a6907-828">Legen Sie dies auf fest, `true` um den Aushandlungs Schritt zu überspringen</span><span class="sxs-lookup"><span data-stu-id="a6907-828">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="a6907-829">Wird **nur unterstützt, wenn der websockets-Transport der einzige aktivierte Transport ist**.</span><span class="sxs-lookup"><span data-stu-id="a6907-829">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="a6907-830">Diese Einstellung kann nicht aktiviert werden, wenn der Azure-Dienst verwendet wird SignalR .</span><span class="sxs-lookup"><span data-stu-id="a6907-830">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="a6907-831">Java</span><span class="sxs-lookup"><span data-stu-id="a6907-831">Java</span></span>](#tab/java)

| <span data-ttu-id="a6907-832">Java-Option</span><span class="sxs-lookup"><span data-stu-id="a6907-832">Java Option</span></span> | <span data-ttu-id="a6907-833">Standardwert</span><span class="sxs-lookup"><span data-stu-id="a6907-833">Default Value</span></span> | <span data-ttu-id="a6907-834">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="a6907-834">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="a6907-835">Eine Funktion, die eine Zeichenfolge zurückgibt, die als bearerauthentifizierungstoken in HTTP-Anforderungen bereitgestellt</span><span class="sxs-lookup"><span data-stu-id="a6907-835">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="a6907-836">Legen Sie dies auf fest, `true` um den Aushandlungs Schritt zu überspringen</span><span class="sxs-lookup"><span data-stu-id="a6907-836">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="a6907-837">Wird **nur unterstützt, wenn der websockets-Transport der einzige aktivierte Transport ist**.</span><span class="sxs-lookup"><span data-stu-id="a6907-837">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="a6907-838">Diese Einstellung kann nicht aktiviert werden, wenn der Azure-Dienst verwendet wird SignalR .</span><span class="sxs-lookup"><span data-stu-id="a6907-838">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="a6907-839">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="a6907-839">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="a6907-840">Leer</span><span class="sxs-lookup"><span data-stu-id="a6907-840">Empty</span></span> | <span data-ttu-id="a6907-841">Eine Karte mit zusätzlichen HTTP-Headern, die mit jeder HTTP-Anforderung gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="a6907-841">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="a6907-842">Im .NET-Client können diese Optionen durch den für bereitgestellten Options Delegaten geändert werden `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="a6907-842">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="a6907-843">Im JavaScript-Client können diese Optionen in einem JavaScript-Objekt bereitgestellt werden, das für Folgendes bereitgestellt wird `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="a6907-843">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="a6907-844">Im Java-Client können diese Optionen mit den Methoden auf der konfiguriert werden, die `HttpHubConnectionBuilder` von der `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="a6907-844">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="a6907-845">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="a6907-845">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="a6907-846">JSON/messagepack-Serialisierungsoptionen</span><span class="sxs-lookup"><span data-stu-id="a6907-846">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="a6907-847">ASP.net Core SignalR unterstützt zwei Protokolle zum Codieren von Nachrichten: [JSON](https://www.json.org/) und [messagepack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="a6907-847">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="a6907-848">Jedes Protokoll verfügt über Konfigurationsoptionen für die Serialisierung.</span><span class="sxs-lookup"><span data-stu-id="a6907-848">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="a6907-849">Die JSON-Serialisierung kann mithilfe der [addjsonprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) -Erweiterungsmethode auf dem Server konfiguriert werden, die nach [dem SignalR Hinzufügen](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in der Methode hinzugefügt werden kann `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="a6907-849">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="a6907-850">Die- `AddJsonProtocol` Methode nimmt einen Delegaten an, der ein- `options` Objekt empfängt.</span><span class="sxs-lookup"><span data-stu-id="a6907-850">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="a6907-851">Die [payloadserializersettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) -Eigenschaft für dieses Objekt ist ein JSON.net- `JsonSerializerSettings` Objekt, das zum Konfigurieren der Serialisierung von Argumenten und Rückgabe Werten verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="a6907-851">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="a6907-852">Weitere Informationen finden Sie in der [JSON.NET-Dokumentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span><span class="sxs-lookup"><span data-stu-id="a6907-852">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="a6907-853">Verwenden Sie beispielsweise den folgenden Code in, um das Serialisierungsprogramm so zu konfigurieren, dass "PascalCase"-Eigenschaftsnamen anstelle der Standardnamen "CamelCase" verwendet werden `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="a6907-853">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="a6907-854">Im .NET-Client ist dieselbe `AddJsonProtocol` Erweiterungsmethode auf [hubconnectionbuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)vorhanden.</span><span class="sxs-lookup"><span data-stu-id="a6907-854">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="a6907-855">Der `Microsoft.Extensions.DependencyInjection` Namespace muss importiert werden, um die Erweiterungsmethode aufzulösen:</span><span class="sxs-lookup"><span data-stu-id="a6907-855">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;
 
// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    })
    .Build();
```

> [!NOTE]
> <span data-ttu-id="a6907-856">Zurzeit ist es nicht möglich, JSON-Serialisierung im JavaScript-Client zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="a6907-856">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="a6907-857">Messagepack-Serialisierungsoptionen</span><span class="sxs-lookup"><span data-stu-id="a6907-857">MessagePack serialization options</span></span>

<span data-ttu-id="a6907-858">Die messagepack-Serialisierung kann konfiguriert werden, indem ein Delegat für den [addmessagepackprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) -Aufruf bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="a6907-858">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="a6907-859">Weitere Informationen finden Sie [unter SignalR messagepack in](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="a6907-859">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="a6907-860">Zurzeit ist es nicht möglich, die messagepack-Serialisierung im JavaScript-Client zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="a6907-860">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="a6907-861">Konfigurieren von Serveroptionen</span><span class="sxs-lookup"><span data-stu-id="a6907-861">Configure server options</span></span>

<span data-ttu-id="a6907-862">In der folgenden Tabelle werden die Optionen zum Konfigurieren von SignalR Hubs beschrieben:</span><span class="sxs-lookup"><span data-stu-id="a6907-862">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="a6907-863">Option</span><span class="sxs-lookup"><span data-stu-id="a6907-863">Option</span></span> | <span data-ttu-id="a6907-864">Standardwert</span><span class="sxs-lookup"><span data-stu-id="a6907-864">Default Value</span></span> | <span data-ttu-id="a6907-865">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="a6907-865">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="a6907-866">30 Sekunden</span><span class="sxs-lookup"><span data-stu-id="a6907-866">30 seconds</span></span> | <span data-ttu-id="a6907-867">Der Server wird vom-Client als getrennt betrachtet, wenn in diesem Intervall keine Nachricht (einschließlich Keep-Alive) empfangen wurde.</span><span class="sxs-lookup"><span data-stu-id="a6907-867">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="a6907-868">Es kann länger dauern, bis der Client als getrennt gekennzeichnet ist, weil dies implementiert ist.</span><span class="sxs-lookup"><span data-stu-id="a6907-868">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="a6907-869">Der empfohlene Wert ist Double- `KeepAliveInterval` Wert.</span><span class="sxs-lookup"><span data-stu-id="a6907-869">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="a6907-870">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="a6907-870">15 seconds</span></span> | <span data-ttu-id="a6907-871">Wenn der Client innerhalb dieses Zeitraums keine anfängliche Hand Shake Nachricht sendet, wird die Verbindung geschlossen.</span><span class="sxs-lookup"><span data-stu-id="a6907-871">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="a6907-872">Dies ist eine erweiterte Einstellung, die nur geändert werden sollte, wenn aufgrund schwerer Netzwerk Latenz Fehler aufgrund von Hand Shake Timeout auftreten.</span><span class="sxs-lookup"><span data-stu-id="a6907-872">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="a6907-873">Weitere Details zum Hand Shake Prozess finden Sie in der [ SignalR Hub-Protokollspezifikation](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="a6907-873">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="a6907-874">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="a6907-874">15 seconds</span></span> | <span data-ttu-id="a6907-875">Wenn der Server innerhalb dieses Intervalls keine Nachricht gesendet hat, wird automatisch eine Ping-Nachricht gesendet, um die Verbindung offen zu halten.</span><span class="sxs-lookup"><span data-stu-id="a6907-875">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="a6907-876">Ändern Sie `KeepAliveInterval` die `ServerTimeout` / `serverTimeoutInMilliseconds` Einstellung auf dem Client, wenn Sie sich ändern.</span><span class="sxs-lookup"><span data-stu-id="a6907-876">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="a6907-877">Der empfohlene `ServerTimeout` / `serverTimeoutInMilliseconds` Wert ist Double- `KeepAliveInterval` Wert.</span><span class="sxs-lookup"><span data-stu-id="a6907-877">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="a6907-878">Alle installierten Protokolle</span><span class="sxs-lookup"><span data-stu-id="a6907-878">All installed protocols</span></span> | <span data-ttu-id="a6907-879">Protokolle, die von diesem Hub unterstützt werden.</span><span class="sxs-lookup"><span data-stu-id="a6907-879">Protocols supported by this hub.</span></span> <span data-ttu-id="a6907-880">Standardmäßig sind alle auf dem Server registrierten Protokolle zulässig, aber Protokolle können aus dieser Liste entfernt werden, um bestimmte Protokolle für einzelne Hubs zu deaktivieren.</span><span class="sxs-lookup"><span data-stu-id="a6907-880">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="a6907-881">Wenn der Wert `true` ist, werden ausführliche Ausnahme Meldungen an Clients zurückgegeben, wenn eine Ausnahme in einer Hub-Methode ausgelöst wird.</span><span class="sxs-lookup"><span data-stu-id="a6907-881">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="a6907-882">Der Standardwert ist `false` , da diese Ausnahme Meldungen vertrauliche Informationen enthalten können.</span><span class="sxs-lookup"><span data-stu-id="a6907-882">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="a6907-883">Optionen können für alle Hubs konfiguriert werden, indem ein Options Delegat für den Aufruf in bereitgestellt wird `AddSignalR` `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="a6907-883">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

<span data-ttu-id="a6907-884">Optionen für einen einzelnen Hub überschreiben die in bereitgestellten globalen Optionen `AddSignalR` und können mithilfe von konfiguriert werden <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="a6907-884">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="a6907-885">Erweiterte http-Konfigurationsoptionen</span><span class="sxs-lookup"><span data-stu-id="a6907-885">Advanced HTTP configuration options</span></span>

<span data-ttu-id="a6907-886">Verwenden `HttpConnectionDispatcherOptions` Sie, um erweiterte Einstellungen im Zusammenhang mit Transporte und der Speicherpuffer Verwaltung zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="a6907-886">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="a6907-887">Diese Optionen werden konfiguriert, indem ein [Delegat an \<T> maphub](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in übergeben wird `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="a6907-887">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseSignalR((configure) =>
    {
        var desiredTransports =
            HttpTransportType.WebSockets |
            HttpTransportType.LongPolling;

        configure.MapHub<ChatHub>("/chathub", (options) =>
        {
            options.Transports = desiredTransports;
        });
    });
}
```

<span data-ttu-id="a6907-888">In der folgenden Tabelle werden die Optionen zum Konfigurieren der SignalR erweiterten http-Optionen von ASP.net Core beschrieben:</span><span class="sxs-lookup"><span data-stu-id="a6907-888">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="a6907-889">Option</span><span class="sxs-lookup"><span data-stu-id="a6907-889">Option</span></span> | <span data-ttu-id="a6907-890">Standardwert</span><span class="sxs-lookup"><span data-stu-id="a6907-890">Default Value</span></span> | <span data-ttu-id="a6907-891">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="a6907-891">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="a6907-892">32 KB</span><span class="sxs-lookup"><span data-stu-id="a6907-892">32 KB</span></span> | <span data-ttu-id="a6907-893">Die maximale Anzahl von Bytes, die vom Client empfangen werden, der vom Server gepuffert wird.</span><span class="sxs-lookup"><span data-stu-id="a6907-893">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="a6907-894">Eine Erhöhung dieses Werts ermöglicht es dem Server, größere Nachrichten zu empfangen, kann sich jedoch negativ auf den Arbeitsspeicherverbrauch auswirken.</span><span class="sxs-lookup"><span data-stu-id="a6907-894">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="a6907-895">Daten, die automatisch von den `Authorize` auf die Hub-Klasse angewendeten Attributen gesammelt werden.</span><span class="sxs-lookup"><span data-stu-id="a6907-895">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="a6907-896">Eine Liste von [iautorizedata](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) -Objekten, die verwendet werden, um zu bestimmen, ob ein Client für die Verbindung mit dem Hub autorisiert ist.</span><span class="sxs-lookup"><span data-stu-id="a6907-896">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="a6907-897">32 KB</span><span class="sxs-lookup"><span data-stu-id="a6907-897">32 KB</span></span> | <span data-ttu-id="a6907-898">Die maximale Anzahl von Bytes, die von der APP gesendet werden, die der Server puffert.</span><span class="sxs-lookup"><span data-stu-id="a6907-898">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="a6907-899">Wenn Sie diesen Wert erhöhen, kann der Server größere Nachrichten senden, kann sich jedoch negativ auf die Arbeitsspeicher Auslastung auswirken.</span><span class="sxs-lookup"><span data-stu-id="a6907-899">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="a6907-900">Alle Transporte sind aktiviert.</span><span class="sxs-lookup"><span data-stu-id="a6907-900">All Transports are enabled.</span></span> | <span data-ttu-id="a6907-901">Ein Bitflags-Enumeration von `HttpTransportType` Werten, die die Transporte einschränken können, die ein Client für die Verbindungs Herstellung verwenden kann.</span><span class="sxs-lookup"><span data-stu-id="a6907-901">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="a6907-902">Siehe unten.</span><span class="sxs-lookup"><span data-stu-id="a6907-902">See below.</span></span> | <span data-ttu-id="a6907-903">Zusätzliche Optionen, die für den langen Abruf Transport spezifisch sind.</span><span class="sxs-lookup"><span data-stu-id="a6907-903">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="a6907-904">Siehe unten.</span><span class="sxs-lookup"><span data-stu-id="a6907-904">See below.</span></span> | <span data-ttu-id="a6907-905">Zusätzliche Optionen für den websockets-Transport.</span><span class="sxs-lookup"><span data-stu-id="a6907-905">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="a6907-906">Der lange Abruf Transport bietet zusätzliche Optionen, die mit der-Eigenschaft konfiguriert werden können `LongPolling` :</span><span class="sxs-lookup"><span data-stu-id="a6907-906">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="a6907-907">Option</span><span class="sxs-lookup"><span data-stu-id="a6907-907">Option</span></span> | <span data-ttu-id="a6907-908">Standardwert</span><span class="sxs-lookup"><span data-stu-id="a6907-908">Default Value</span></span> | <span data-ttu-id="a6907-909">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="a6907-909">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="a6907-910">90 Sekunden</span><span class="sxs-lookup"><span data-stu-id="a6907-910">90 seconds</span></span> | <span data-ttu-id="a6907-911">Die maximale Zeitspanne, die der Server wartet, bis eine Nachricht an den Client gesendet wird, bevor eine einzelne Abruf Anforderung beendet wird.</span><span class="sxs-lookup"><span data-stu-id="a6907-911">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="a6907-912">Das verringern dieses Werts bewirkt, dass der Client neue Abruf Anforderungen häufiger ausgibt.</span><span class="sxs-lookup"><span data-stu-id="a6907-912">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="a6907-913">Der WebSocket-Transport verfügt über zusätzliche Optionen, die mit der-Eigenschaft konfiguriert werden können `WebSockets` :</span><span class="sxs-lookup"><span data-stu-id="a6907-913">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="a6907-914">Option</span><span class="sxs-lookup"><span data-stu-id="a6907-914">Option</span></span> | <span data-ttu-id="a6907-915">Standardwert</span><span class="sxs-lookup"><span data-stu-id="a6907-915">Default Value</span></span> | <span data-ttu-id="a6907-916">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="a6907-916">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="a6907-917">5 Sekunden</span><span class="sxs-lookup"><span data-stu-id="a6907-917">5 seconds</span></span> | <span data-ttu-id="a6907-918">Wenn der Client nach dem Schließen des Servers nicht innerhalb dieses Zeitraums geschlossen werden kann, wird die Verbindung beendet.</span><span class="sxs-lookup"><span data-stu-id="a6907-918">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="a6907-919">Ein Delegat, der verwendet werden kann, um den- `Sec-WebSocket-Protocol` Header auf einen benutzerdefinierten Wert festzulegen.</span><span class="sxs-lookup"><span data-stu-id="a6907-919">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="a6907-920">Der Delegat empfängt die vom Client angeforderten Werte als Eingabe und erwartet, dass der gewünschte Wert zurückgegeben wird.</span><span class="sxs-lookup"><span data-stu-id="a6907-920">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="a6907-921">Konfigurieren von Clientoptionen</span><span class="sxs-lookup"><span data-stu-id="a6907-921">Configure client options</span></span>

<span data-ttu-id="a6907-922">Client Optionen können für den Typ konfiguriert werden `HubConnectionBuilder` (verfügbar in den .net-und JavaScript-Clients).</span><span class="sxs-lookup"><span data-stu-id="a6907-922">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="a6907-923">Sie steht auch im Java-Client zur Verfügung, aber die `HttpHubConnectionBuilder` Unterklasse enthält die Generator-Konfigurationsoptionen sowie die `HubConnection` selbst.</span><span class="sxs-lookup"><span data-stu-id="a6907-923">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="a6907-924">Konfigurieren der Protokollierung</span><span class="sxs-lookup"><span data-stu-id="a6907-924">Configure logging</span></span>

<span data-ttu-id="a6907-925">Die Protokollierung wird mithilfe der-Methode im .NET-Client konfiguriert `ConfigureLogging` .</span><span class="sxs-lookup"><span data-stu-id="a6907-925">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="a6907-926">Protokollierungs Anbieter und Filter können auf die gleiche Weise wie auf dem Server registriert werden.</span><span class="sxs-lookup"><span data-stu-id="a6907-926">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="a6907-927">Weitere Informationen finden Sie in der Dokumentation zur [Protokollierung ASP.net Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="a6907-927">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="a6907-928">Um Protokollierungs Anbieter zu registrieren, müssen Sie die erforderlichen Pakete installieren.</span><span class="sxs-lookup"><span data-stu-id="a6907-928">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="a6907-929">Eine vollständige Liste finden Sie im Abschnitt [integrierte Protokollierungs Anbieter](xref:fundamentals/logging/index#built-in-logging-providers) der Dokumentation.</span><span class="sxs-lookup"><span data-stu-id="a6907-929">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="a6907-930">Um z. b. die Konsolen Protokollierung zu aktivieren, installieren Sie das `Microsoft.Extensions.Logging.Console` nuget-Paket.</span><span class="sxs-lookup"><span data-stu-id="a6907-930">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="a6907-931">Die `AddConsole` Erweiterungsmethode aufzurufen:</span><span class="sxs-lookup"><span data-stu-id="a6907-931">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="a6907-932">Im JavaScript-Client ist eine ähnliche `configureLogging` Methode vorhanden.</span><span class="sxs-lookup"><span data-stu-id="a6907-932">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="a6907-933">Geben `LogLevel` Sie einen Wert an, der die minimale Ebene der zu erstellenden Protokollmeldungen angibt.</span><span class="sxs-lookup"><span data-stu-id="a6907-933">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="a6907-934">Protokolle werden in das Browser Konsolenfenster geschrieben.</span><span class="sxs-lookup"><span data-stu-id="a6907-934">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="a6907-935">Um die Protokollierung vollständig zu deaktivieren, geben Sie `signalR.LogLevel.None` in der `configureLogging` Methode an</span><span class="sxs-lookup"><span data-stu-id="a6907-935">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="a6907-936">Weitere Informationen zur Protokollierung finden Sie in der [ SignalR Diagnose Dokumentation](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="a6907-936">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="a6907-937">Der SignalR Java-Client verwendet die [SLF4J](https://www.slf4j.org/) -Bibliothek für die Protokollierung.</span><span class="sxs-lookup"><span data-stu-id="a6907-937">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="a6907-938">Dabei handelt es sich um eine allgemeine Protokollierungs-API, die es Benutzern der Bibliothek ermöglicht, ihre eigene spezifische Protokollierungs Implementierung zu wählen, indem Sie eine bestimmte Protokollierungs Abhängigkeit einbinden.</span><span class="sxs-lookup"><span data-stu-id="a6907-938">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="a6907-939">Der folgende Code Ausschnitt zeigt, wie `java.util.logging` mit dem Java- SignalR Client verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="a6907-939">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="a6907-940">Wenn Sie die Protokollierung in ihren Abhängigkeiten nicht konfigurieren, lädt SLF4J eine Standard Protokollierung ohne Vorgang mit folgender Warnmeldung:</span><span class="sxs-lookup"><span data-stu-id="a6907-940">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="a6907-941">Dies kann sicher ignoriert werden.</span><span class="sxs-lookup"><span data-stu-id="a6907-941">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="a6907-942">Zulässige Transporte konfigurieren</span><span class="sxs-lookup"><span data-stu-id="a6907-942">Configure allowed transports</span></span>

<span data-ttu-id="a6907-943">Die von verwendeten Transporte SignalR können im-Befehl `WithUrl` ( `withUrl` in JavaScript) konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="a6907-943">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="a6907-944">Ein bitweises OR der Werte von `HttpTransportType` kann verwendet werden, um den Client so einzuschränken, dass nur die angegebenen Transporte verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="a6907-944">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="a6907-945">Alle Transporte sind standardmäßig aktiviert.</span><span class="sxs-lookup"><span data-stu-id="a6907-945">All transports are enabled by default.</span></span>

<span data-ttu-id="a6907-946">Um z. b. den Server-Sent Ereignis Transport zu deaktivieren, aber websockets und lange Abruf Verbindungen zuzulassen:</span><span class="sxs-lookup"><span data-stu-id="a6907-946">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="a6907-947">Im JavaScript-Client werden Transporte durch Festlegen des- `transport` Felds für das Options-Objekt konfiguriert, das für bereitgestellt wird `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="a6907-947">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="a6907-948">In dieser Version des Java-Clients ist websockets der einzige verfügbare Transport.</span><span class="sxs-lookup"><span data-stu-id="a6907-948">In this version of the Java client websockets is the only available transport.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="a6907-949">Konfigurieren der bearerauthentifizierung</span><span class="sxs-lookup"><span data-stu-id="a6907-949">Configure bearer authentication</span></span>

<span data-ttu-id="a6907-950">Um Authentifizierungsdaten zusammen mit Anforderungen bereitzustellen SignalR , verwenden `AccessTokenProvider` Sie die-Option ( `accessTokenFactory` in JavaScript), um eine Funktion anzugeben, die das gewünschte Zugriffs Token zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="a6907-950">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="a6907-951">Im .NET-Client wird dieses Zugriffs Token als http-tokenauthentifizierungstoken (mit dem- `Authorization` Header mit dem Typ) an das Token "bearerauthentifizierung" geleitet `Bearer` .</span><span class="sxs-lookup"><span data-stu-id="a6907-951">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="a6907-952">Im JavaScript-Client wird das Zugriffs Token als bearertoken verwendet, **außer** in einigen Fällen, in denen Browser-APIs die Fähigkeit zum Anwenden von Headern einschränken (insbesondere in Server-Sent Ereignissen und websockets-Anforderungen).</span><span class="sxs-lookup"><span data-stu-id="a6907-952">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="a6907-953">In diesen Fällen wird das Zugriffs Token als Abfrage Zeichenfolgen-Wert bereitgestellt `access_token` .</span><span class="sxs-lookup"><span data-stu-id="a6907-953">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="a6907-954">Im .NET-Client kann die `AccessTokenProvider` Option mithilfe des Options Delegaten in angegeben werden `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="a6907-954">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="a6907-955">Im JavaScript-Client wird das Zugriffs Token konfiguriert, indem das- `accessTokenFactory` Feld für das Options-Objekt in festgelegt wird `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="a6907-955">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

<span data-ttu-id="a6907-956">Im SignalR Java-Client können Sie ein bearertoken für die Authentifizierung konfigurieren, indem Sie eine zugriffstokenfactory für " [httphubconnectionbuilder](/java/api/com.microsoft.signalr.httphubconnectionbuilder?view=aspnet-signalr-java)" bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="a6907-956">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr.httphubconnectionbuilder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="a6907-957">Verwenden Sie [withaccesstokenfactory](/java/api/com.microsoft.signalr.httphubconnectionbuilder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) , um eine [einzelne \<String> ](https://reactivex.io/documentation/single.html) [rxjava](https://github.com/ReactiveX/RxJava) -Instanz bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="a6907-957">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr.httphubconnectionbuilder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="a6907-958">Beim Aufrufen von " [Single.](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)Write" können Sie Logik schreiben, um Zugriffs Token für den Client zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="a6907-958">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="a6907-959">Konfigurieren von Timeout-und Keep-Alive-Optionen</span><span class="sxs-lookup"><span data-stu-id="a6907-959">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="a6907-960">Zusätzliche Optionen zum Konfigurieren von Timeout und Keep-Alive-Verhalten sind für das `HubConnection` Objekt selbst verfügbar:</span><span class="sxs-lookup"><span data-stu-id="a6907-960">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="a6907-961">.NET</span><span class="sxs-lookup"><span data-stu-id="a6907-961">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="a6907-962">Option</span><span class="sxs-lookup"><span data-stu-id="a6907-962">Option</span></span> | <span data-ttu-id="a6907-963">Standardwert</span><span class="sxs-lookup"><span data-stu-id="a6907-963">Default value</span></span> | <span data-ttu-id="a6907-964">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="a6907-964">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="a6907-965">30 Sekunden (30.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="a6907-965">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="a6907-966">Timeout für die Serveraktivität.</span><span class="sxs-lookup"><span data-stu-id="a6907-966">Timeout for server activity.</span></span> <span data-ttu-id="a6907-967">Wenn der Server in diesem Intervall keine Nachricht gesendet hat, betrachtet der Client den Server als getrennt und löst das `Closed` Ereignis `onclose` aus (in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="a6907-967">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="a6907-968">Dieser Wert muss groß genug sein, damit eine Ping-Nachricht vom Server gesendet **und** innerhalb des Timeout Intervalls vom Client empfangen wird.</span><span class="sxs-lookup"><span data-stu-id="a6907-968">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="a6907-969">Der empfohlene Wert ist eine Zahl, die mindestens dem Wert des Servers `KeepAliveInterval` entspricht, um Zeit für das Eintreffen von Pings zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="a6907-969">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="a6907-970">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="a6907-970">15 seconds</span></span> | <span data-ttu-id="a6907-971">Timeout für den anfänglichen Server Hand Shake.</span><span class="sxs-lookup"><span data-stu-id="a6907-971">Timeout for initial server handshake.</span></span> <span data-ttu-id="a6907-972">Wenn der Server in diesem Intervall keine Hand Shake Antwort sendet, bricht der Client den Handshake ab und löst das `Closed` Ereignis `onclose` aus (in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="a6907-972">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="a6907-973">Dies ist eine erweiterte Einstellung, die nur geändert werden sollte, wenn aufgrund schwerer Netzwerk Latenz Fehler aufgrund von Hand Shake Timeout auftreten.</span><span class="sxs-lookup"><span data-stu-id="a6907-973">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="a6907-974">Weitere Details zum Hand Shake Prozess finden Sie in der [ SignalR Hub-Protokollspezifikation](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="a6907-974">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="a6907-975">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="a6907-975">15 seconds</span></span> | <span data-ttu-id="a6907-976">Bestimmt das Intervall, in dem der Client Ping-Nachrichten sendet.</span><span class="sxs-lookup"><span data-stu-id="a6907-976">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="a6907-977">Beim Senden von Nachrichten vom Client wird der Timer auf den Anfang des Intervalls zurückgesetzt.</span><span class="sxs-lookup"><span data-stu-id="a6907-977">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="a6907-978">Wenn der Client keine Nachricht in der `ClientTimeoutInterval` Gruppe auf dem Server gesendet hat, wird der Client vom Server als getrennt betrachtet.</span><span class="sxs-lookup"><span data-stu-id="a6907-978">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="a6907-979">Im .NET-Client werden Timeout Werte als Werte angegeben `TimeSpan` .</span><span class="sxs-lookup"><span data-stu-id="a6907-979">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="a6907-980">JavaScript</span><span class="sxs-lookup"><span data-stu-id="a6907-980">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="a6907-981">Option</span><span class="sxs-lookup"><span data-stu-id="a6907-981">Option</span></span> | <span data-ttu-id="a6907-982">Standardwert</span><span class="sxs-lookup"><span data-stu-id="a6907-982">Default value</span></span> | <span data-ttu-id="a6907-983">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="a6907-983">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="a6907-984">30 Sekunden (30.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="a6907-984">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="a6907-985">Timeout für die Serveraktivität.</span><span class="sxs-lookup"><span data-stu-id="a6907-985">Timeout for server activity.</span></span> <span data-ttu-id="a6907-986">Wenn der Server in diesem Intervall keine Nachricht gesendet hat, betrachtet der Client den Server als getrennt und löst das `onclose` Ereignis aus.</span><span class="sxs-lookup"><span data-stu-id="a6907-986">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="a6907-987">Dieser Wert muss groß genug sein, damit eine Ping-Nachricht vom Server gesendet **und** innerhalb des Timeout Intervalls vom Client empfangen wird.</span><span class="sxs-lookup"><span data-stu-id="a6907-987">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="a6907-988">Der empfohlene Wert ist eine Zahl, die mindestens dem Wert des Servers `KeepAliveInterval` entspricht, um Zeit für das Eintreffen von Pings zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="a6907-988">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="a6907-989">15 Sekunden (15.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="a6907-989">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="a6907-990">Bestimmt das Intervall, in dem der Client Ping-Nachrichten sendet.</span><span class="sxs-lookup"><span data-stu-id="a6907-990">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="a6907-991">Beim Senden von Nachrichten vom Client wird der Timer auf den Anfang des Intervalls zurückgesetzt.</span><span class="sxs-lookup"><span data-stu-id="a6907-991">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="a6907-992">Wenn der Client keine Nachricht in der `ClientTimeoutInterval` Gruppe auf dem Server gesendet hat, wird der Client vom Server als getrennt betrachtet.</span><span class="sxs-lookup"><span data-stu-id="a6907-992">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="a6907-993">Java</span><span class="sxs-lookup"><span data-stu-id="a6907-993">Java</span></span>](#tab/java)

| <span data-ttu-id="a6907-994">Option</span><span class="sxs-lookup"><span data-stu-id="a6907-994">Option</span></span> | <span data-ttu-id="a6907-995">Standardwert</span><span class="sxs-lookup"><span data-stu-id="a6907-995">Default value</span></span> | <span data-ttu-id="a6907-996">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="a6907-996">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="a6907-997">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="a6907-997">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="a6907-998">30 Sekunden (30.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="a6907-998">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="a6907-999">Timeout für die Serveraktivität.</span><span class="sxs-lookup"><span data-stu-id="a6907-999">Timeout for server activity.</span></span> <span data-ttu-id="a6907-1000">Wenn der Server in diesem Intervall keine Nachricht gesendet hat, betrachtet der Client den Server als getrennt und löst das `onClose` Ereignis aus.</span><span class="sxs-lookup"><span data-stu-id="a6907-1000">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="a6907-1001">Dieser Wert muss groß genug sein, damit eine Ping-Nachricht vom Server gesendet **und** innerhalb des Timeout Intervalls vom Client empfangen wird.</span><span class="sxs-lookup"><span data-stu-id="a6907-1001">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="a6907-1002">Der empfohlene Wert ist eine Zahl, die mindestens dem Wert des Servers `KeepAliveInterval` entspricht, um Zeit für das Eintreffen von Pings zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="a6907-1002">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="a6907-1003">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="a6907-1003">15 seconds</span></span> | <span data-ttu-id="a6907-1004">Timeout für den anfänglichen Server Hand Shake.</span><span class="sxs-lookup"><span data-stu-id="a6907-1004">Timeout for initial server handshake.</span></span> <span data-ttu-id="a6907-1005">Wenn der Server in diesem Intervall keine Hand Shake Antwort sendet, bricht der Client den Handshake ab und löst das- `onClose` Ereignis aus.</span><span class="sxs-lookup"><span data-stu-id="a6907-1005">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="a6907-1006">Dies ist eine erweiterte Einstellung, die nur geändert werden sollte, wenn aufgrund schwerer Netzwerk Latenz Fehler aufgrund von Hand Shake Timeout auftreten.</span><span class="sxs-lookup"><span data-stu-id="a6907-1006">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="a6907-1007">Weitere Details zum Hand Shake Prozess finden Sie in der [ SignalR Hub-Protokollspezifikation](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="a6907-1007">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="a6907-1008">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="a6907-1008">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="a6907-1009">15 Sekunden (15.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="a6907-1009">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="a6907-1010">Bestimmt das Intervall, in dem der Client Ping-Nachrichten sendet.</span><span class="sxs-lookup"><span data-stu-id="a6907-1010">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="a6907-1011">Beim Senden von Nachrichten vom Client wird der Timer auf den Anfang des Intervalls zurückgesetzt.</span><span class="sxs-lookup"><span data-stu-id="a6907-1011">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="a6907-1012">Wenn der Client keine Nachricht in der `ClientTimeoutInterval` Gruppe auf dem Server gesendet hat, wird der Client vom Server als getrennt betrachtet.</span><span class="sxs-lookup"><span data-stu-id="a6907-1012">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="a6907-1013">Konfigurieren zusätzlicher Optionen</span><span class="sxs-lookup"><span data-stu-id="a6907-1013">Configure additional options</span></span>

<span data-ttu-id="a6907-1014">Zusätzliche Optionen können in der- `WithUrl` Methode ( `withUrl` in JavaScript) für `HubConnectionBuilder` oder für die verschiedenen Konfigurations-APIs auf dem `HttpHubConnectionBuilder` im Java-Client konfiguriert werden:</span><span class="sxs-lookup"><span data-stu-id="a6907-1014">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="a6907-1015">.NET</span><span class="sxs-lookup"><span data-stu-id="a6907-1015">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="a6907-1016">.Net-Option</span><span class="sxs-lookup"><span data-stu-id="a6907-1016">.NET Option</span></span> |  <span data-ttu-id="a6907-1017">Standardwert</span><span class="sxs-lookup"><span data-stu-id="a6907-1017">Default value</span></span> | <span data-ttu-id="a6907-1018">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="a6907-1018">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="a6907-1019">Eine Funktion, die eine Zeichenfolge zurückgibt, die als bearerauthentifizierungstoken in HTTP-Anforderungen bereitgestellt</span><span class="sxs-lookup"><span data-stu-id="a6907-1019">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="a6907-1020">Legen Sie dies auf fest, `true` um den Aushandlungs Schritt zu überspringen</span><span class="sxs-lookup"><span data-stu-id="a6907-1020">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="a6907-1021">Wird **nur unterstützt, wenn der websockets-Transport der einzige aktivierte Transport ist**.</span><span class="sxs-lookup"><span data-stu-id="a6907-1021">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="a6907-1022">Diese Einstellung kann nicht aktiviert werden, wenn der Azure-Dienst verwendet wird SignalR .</span><span class="sxs-lookup"><span data-stu-id="a6907-1022">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="a6907-1023">Leer</span><span class="sxs-lookup"><span data-stu-id="a6907-1023">Empty</span></span> | <span data-ttu-id="a6907-1024">Eine Auflistung von TLS-Zertifikaten, die zum Authentifizieren von Anforderungen gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="a6907-1024">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="a6907-1025">Leer</span><span class="sxs-lookup"><span data-stu-id="a6907-1025">Empty</span></span> | <span data-ttu-id="a6907-1026">Eine Auflistung von http- cookie s, die mit jeder HTTP-Anforderung gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="a6907-1026">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="a6907-1027">Leer</span><span class="sxs-lookup"><span data-stu-id="a6907-1027">Empty</span></span> | <span data-ttu-id="a6907-1028">Anmelde Informationen, die mit jeder HTTP-Anforderung gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="a6907-1028">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="a6907-1029">5 Sekunden</span><span class="sxs-lookup"><span data-stu-id="a6907-1029">5 seconds</span></span> | <span data-ttu-id="a6907-1030">Nur websockets.</span><span class="sxs-lookup"><span data-stu-id="a6907-1030">WebSockets only.</span></span> <span data-ttu-id="a6907-1031">Die maximale Zeitspanne, die der Client nach dem Schließen des Servers wartet, um die Anforderung zum Schließen zu bestätigen.</span><span class="sxs-lookup"><span data-stu-id="a6907-1031">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="a6907-1032">Wenn der Server die Schließung innerhalb dieses Zeitraums nicht anerkennt, trennt der Client die Verbindung.</span><span class="sxs-lookup"><span data-stu-id="a6907-1032">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="a6907-1033">Leer</span><span class="sxs-lookup"><span data-stu-id="a6907-1033">Empty</span></span> | <span data-ttu-id="a6907-1034">Eine Karte mit zusätzlichen HTTP-Headern, die mit jeder HTTP-Anforderung gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="a6907-1034">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="a6907-1035">Ein Delegat, der verwendet werden kann, um den `HttpMessageHandler` zum Senden von HTTP-Anforderungen verwendeten zu konfigurieren oder zu ersetzen.</span><span class="sxs-lookup"><span data-stu-id="a6907-1035">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="a6907-1036">Wird nicht für WebSocket-Verbindungen verwendet.</span><span class="sxs-lookup"><span data-stu-id="a6907-1036">Not used for WebSocket connections.</span></span> <span data-ttu-id="a6907-1037">Dieser Delegat muss einen Wert zurückgeben, der nicht NULL ist, und erhält den Standardwert als Parameter.</span><span class="sxs-lookup"><span data-stu-id="a6907-1037">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="a6907-1038">Ändern Sie die Einstellungen für diesen Standardwert, und geben Sie ihn zurück, oder geben Sie eine neue `HttpMessageHandler` Instanz zurück.</span><span class="sxs-lookup"><span data-stu-id="a6907-1038">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="a6907-1039">**Wenn Sie den Handler ersetzen, stellen Sie sicher, dass die Einstellungen, die Sie beibehalten möchten, vom bereitgestellten Handler kopiert werden. Andernfalls gelten die konfigurierten Optionen (z. b. Cookie s und Header) nicht für den neuen Handler.**</span><span class="sxs-lookup"><span data-stu-id="a6907-1039">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="a6907-1040">Ein HTTP-Proxy, der beim Senden von HTTP-Anforderungen verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="a6907-1040">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="a6907-1041">Legen Sie diesen booleschen Wert fest, um die Standard Anmelde Informationen für http-und websockets-Anforderungen zu senden</span><span class="sxs-lookup"><span data-stu-id="a6907-1041">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="a6907-1042">Dies ermöglicht die Verwendung der Windows-Authentifizierung.</span><span class="sxs-lookup"><span data-stu-id="a6907-1042">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="a6907-1043">Ein Delegat, der zum Konfigurieren zusätzlicher WebSocket-Optionen verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="a6907-1043">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="a6907-1044">Empfängt eine Instanz von [clientwebsocketoptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) , die zum Konfigurieren der Optionen verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="a6907-1044">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="a6907-1045">JavaScript</span><span class="sxs-lookup"><span data-stu-id="a6907-1045">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="a6907-1046">JavaScript-Option</span><span class="sxs-lookup"><span data-stu-id="a6907-1046">JavaScript Option</span></span> | <span data-ttu-id="a6907-1047">Standardwert</span><span class="sxs-lookup"><span data-stu-id="a6907-1047">Default Value</span></span> | <span data-ttu-id="a6907-1048">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="a6907-1048">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="a6907-1049">Eine Funktion, die eine Zeichenfolge zurückgibt, die als bearerauthentifizierungstoken in HTTP-Anforderungen bereitgestellt</span><span class="sxs-lookup"><span data-stu-id="a6907-1049">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `transport` | `null` | <span data-ttu-id="a6907-1050">Ein- <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType> Wert, der den für die Verbindung zu verwendenden Transport angibt.</span><span class="sxs-lookup"><span data-stu-id="a6907-1050">An <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType> value specifying the transport to use for the connection.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="a6907-1051">Legen Sie auf fest, `true` um die Bytes/Zeichen der vom Client gesendeten und empfangenen Nachrichten zu protokollieren.</span><span class="sxs-lookup"><span data-stu-id="a6907-1051">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="a6907-1052">Legen Sie dies auf fest, `true` um den Aushandlungs Schritt zu überspringen</span><span class="sxs-lookup"><span data-stu-id="a6907-1052">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="a6907-1053">Wird **nur unterstützt, wenn der websockets-Transport der einzige aktivierte Transport ist**.</span><span class="sxs-lookup"><span data-stu-id="a6907-1053">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="a6907-1054">Diese Einstellung kann nicht aktiviert werden, wenn der Azure-Dienst verwendet wird SignalR .</span><span class="sxs-lookup"><span data-stu-id="a6907-1054">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="a6907-1055">Java</span><span class="sxs-lookup"><span data-stu-id="a6907-1055">Java</span></span>](#tab/java)

| <span data-ttu-id="a6907-1056">Java-Option</span><span class="sxs-lookup"><span data-stu-id="a6907-1056">Java Option</span></span> | <span data-ttu-id="a6907-1057">Standardwert</span><span class="sxs-lookup"><span data-stu-id="a6907-1057">Default Value</span></span> | <span data-ttu-id="a6907-1058">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="a6907-1058">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="a6907-1059">Eine Funktion, die eine Zeichenfolge zurückgibt, die als bearerauthentifizierungstoken in HTTP-Anforderungen bereitgestellt</span><span class="sxs-lookup"><span data-stu-id="a6907-1059">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="a6907-1060">Legen Sie dies auf fest, `true` um den Aushandlungs Schritt zu überspringen</span><span class="sxs-lookup"><span data-stu-id="a6907-1060">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="a6907-1061">Wird **nur unterstützt, wenn der websockets-Transport der einzige aktivierte Transport ist**.</span><span class="sxs-lookup"><span data-stu-id="a6907-1061">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="a6907-1062">Diese Einstellung kann nicht aktiviert werden, wenn der Azure-Dienst verwendet wird SignalR .</span><span class="sxs-lookup"><span data-stu-id="a6907-1062">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="a6907-1063">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="a6907-1063">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="a6907-1064">Leer</span><span class="sxs-lookup"><span data-stu-id="a6907-1064">Empty</span></span> | <span data-ttu-id="a6907-1065">Eine Karte mit zusätzlichen HTTP-Headern, die mit jeder HTTP-Anforderung gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="a6907-1065">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="a6907-1066">Im .NET-Client können diese Optionen durch den für bereitgestellten Options Delegaten geändert werden `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="a6907-1066">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="a6907-1067">Im JavaScript-Client können diese Optionen in einem JavaScript-Objekt bereitgestellt werden, das für Folgendes bereitgestellt wird `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="a6907-1067">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="a6907-1068">Im Java-Client können diese Optionen mit den Methoden auf der konfiguriert werden, die `HttpHubConnectionBuilder` von der `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="a6907-1068">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="a6907-1069">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="a6907-1069">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="< aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="a6907-1070">JSON/messagepack-Serialisierungsoptionen</span><span class="sxs-lookup"><span data-stu-id="a6907-1070">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="a6907-1071">ASP.net Core SignalR unterstützt zwei Protokolle zum Codieren von Nachrichten: [JSON](https://www.json.org/) und [messagepack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="a6907-1071">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="a6907-1072">Jedes Protokoll verfügt über Konfigurationsoptionen für die Serialisierung.</span><span class="sxs-lookup"><span data-stu-id="a6907-1072">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="a6907-1073">Die JSON-Serialisierung kann mithilfe der [addjsonprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) -Erweiterungsmethode auf dem Server konfiguriert werden, die nach [dem SignalR Hinzufügen](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in der Methode hinzugefügt werden kann `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="a6907-1073">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="a6907-1074">Die- `AddJsonProtocol` Methode nimmt einen Delegaten an, der ein- `options` Objekt empfängt.</span><span class="sxs-lookup"><span data-stu-id="a6907-1074">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="a6907-1075">Die [payloadserializersettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) -Eigenschaft für dieses Objekt ist ein JSON.net- `JsonSerializerSettings` Objekt, das zum Konfigurieren der Serialisierung von Argumenten und Rückgabe Werten verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="a6907-1075">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="a6907-1076">Weitere Informationen finden Sie in der [JSON.NET-Dokumentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span><span class="sxs-lookup"><span data-stu-id="a6907-1076">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="a6907-1077">Verwenden Sie beispielsweise den folgenden Code in, um das Serialisierungsprogramm so zu konfigurieren, dass "PascalCase"-Eigenschaftsnamen anstelle der Standardnamen "CamelCase" verwendet werden `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="a6907-1077">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="a6907-1078">Im .NET-Client ist dieselbe `AddJsonProtocol` Erweiterungsmethode auf [hubconnectionbuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)vorhanden.</span><span class="sxs-lookup"><span data-stu-id="a6907-1078">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="a6907-1079">Der `Microsoft.Extensions.DependencyInjection` Namespace muss importiert werden, um die Erweiterungsmethode aufzulösen:</span><span class="sxs-lookup"><span data-stu-id="a6907-1079">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;
 
// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    })
    .Build();
```

> [!NOTE]
> <span data-ttu-id="a6907-1080">Zurzeit ist es nicht möglich, JSON-Serialisierung im JavaScript-Client zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="a6907-1080">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="a6907-1081">Messagepack-Serialisierungsoptionen</span><span class="sxs-lookup"><span data-stu-id="a6907-1081">MessagePack serialization options</span></span>

<span data-ttu-id="a6907-1082">Die messagepack-Serialisierung kann konfiguriert werden, indem ein Delegat für den [addmessagepackprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) -Aufruf bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="a6907-1082">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="a6907-1083">Weitere Informationen finden Sie [unter SignalR messagepack in](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="a6907-1083">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="a6907-1084">Zurzeit ist es nicht möglich, die messagepack-Serialisierung im JavaScript-Client zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="a6907-1084">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="a6907-1085">Konfigurieren von Serveroptionen</span><span class="sxs-lookup"><span data-stu-id="a6907-1085">Configure server options</span></span>

<span data-ttu-id="a6907-1086">In der folgenden Tabelle werden die Optionen zum Konfigurieren von SignalR Hubs beschrieben:</span><span class="sxs-lookup"><span data-stu-id="a6907-1086">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="a6907-1087">Option</span><span class="sxs-lookup"><span data-stu-id="a6907-1087">Option</span></span> | <span data-ttu-id="a6907-1088">Standardwert</span><span class="sxs-lookup"><span data-stu-id="a6907-1088">Default Value</span></span> | <span data-ttu-id="a6907-1089">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="a6907-1089">Description</span></span> |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | <span data-ttu-id="a6907-1090">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="a6907-1090">15 seconds</span></span> | <span data-ttu-id="a6907-1091">Wenn der Client innerhalb dieses Zeitraums keine anfängliche Hand Shake Nachricht sendet, wird die Verbindung geschlossen.</span><span class="sxs-lookup"><span data-stu-id="a6907-1091">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="a6907-1092">Dies ist eine erweiterte Einstellung, die nur geändert werden sollte, wenn aufgrund schwerer Netzwerk Latenz Fehler aufgrund von Hand Shake Timeout auftreten.</span><span class="sxs-lookup"><span data-stu-id="a6907-1092">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="a6907-1093">Weitere Details zum Hand Shake Prozess finden Sie in der [ SignalR Hub-Protokollspezifikation](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="a6907-1093">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="a6907-1094">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="a6907-1094">15 seconds</span></span> | <span data-ttu-id="a6907-1095">Wenn der Server innerhalb dieses Intervalls keine Nachricht gesendet hat, wird automatisch eine Ping-Nachricht gesendet, um die Verbindung offen zu halten.</span><span class="sxs-lookup"><span data-stu-id="a6907-1095">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="a6907-1096">Ändern Sie `KeepAliveInterval` die `ServerTimeout` / `serverTimeoutInMilliseconds` Einstellung auf dem Client, wenn Sie sich ändern.</span><span class="sxs-lookup"><span data-stu-id="a6907-1096">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="a6907-1097">Der empfohlene `ServerTimeout` / `serverTimeoutInMilliseconds` Wert ist Double- `KeepAliveInterval` Wert.</span><span class="sxs-lookup"><span data-stu-id="a6907-1097">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="a6907-1098">Alle installierten Protokolle</span><span class="sxs-lookup"><span data-stu-id="a6907-1098">All installed protocols</span></span> | <span data-ttu-id="a6907-1099">Protokolle, die von diesem Hub unterstützt werden.</span><span class="sxs-lookup"><span data-stu-id="a6907-1099">Protocols supported by this hub.</span></span> <span data-ttu-id="a6907-1100">Standardmäßig sind alle auf dem Server registrierten Protokolle zulässig, aber Protokolle können aus dieser Liste entfernt werden, um bestimmte Protokolle für einzelne Hubs zu deaktivieren.</span><span class="sxs-lookup"><span data-stu-id="a6907-1100">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="a6907-1101">Wenn der Wert `true` ist, werden ausführliche Ausnahme Meldungen an Clients zurückgegeben, wenn eine Ausnahme in einer Hub-Methode ausgelöst wird.</span><span class="sxs-lookup"><span data-stu-id="a6907-1101">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="a6907-1102">Der Standardwert ist `false` , da diese Ausnahme Meldungen vertrauliche Informationen enthalten können.</span><span class="sxs-lookup"><span data-stu-id="a6907-1102">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="a6907-1103">Optionen können für alle Hubs konfiguriert werden, indem ein Options Delegat für den Aufruf in bereitgestellt wird `AddSignalR` `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="a6907-1103">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

<span data-ttu-id="a6907-1104">Optionen für einen einzelnen Hub überschreiben die in bereitgestellten globalen Optionen `AddSignalR` und können mithilfe von konfiguriert werden <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="a6907-1104">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="a6907-1105">Erweiterte http-Konfigurationsoptionen</span><span class="sxs-lookup"><span data-stu-id="a6907-1105">Advanced HTTP configuration options</span></span>

<span data-ttu-id="a6907-1106">Verwenden `HttpConnectionDispatcherOptions` Sie, um erweiterte Einstellungen im Zusammenhang mit Transporte und der Speicherpuffer Verwaltung zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="a6907-1106">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="a6907-1107">Diese Optionen werden konfiguriert, indem ein [Delegat an \<T> maphub](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in übergeben wird `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="a6907-1107">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseSignalR((configure) =>
    {
        var desiredTransports =
            HttpTransportType.WebSockets |
            HttpTransportType.LongPolling;

        configure.MapHub<ChatHub>("/chathub", (options) =>
        {
            options.Transports = desiredTransports;
        });
    });
}
```

<span data-ttu-id="a6907-1108">In der folgenden Tabelle werden die Optionen zum Konfigurieren der SignalR erweiterten http-Optionen von ASP.net Core beschrieben:</span><span class="sxs-lookup"><span data-stu-id="a6907-1108">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="a6907-1109">Option</span><span class="sxs-lookup"><span data-stu-id="a6907-1109">Option</span></span> | <span data-ttu-id="a6907-1110">Standardwert</span><span class="sxs-lookup"><span data-stu-id="a6907-1110">Default Value</span></span> | <span data-ttu-id="a6907-1111">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="a6907-1111">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="a6907-1112">32 KB</span><span class="sxs-lookup"><span data-stu-id="a6907-1112">32 KB</span></span> | <span data-ttu-id="a6907-1113">Die maximale Anzahl von Bytes, die vom Client empfangen werden, der vom Server gepuffert wird.</span><span class="sxs-lookup"><span data-stu-id="a6907-1113">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="a6907-1114">Eine Erhöhung dieses Werts ermöglicht es dem Server, größere Nachrichten zu empfangen, kann sich jedoch negativ auf den Arbeitsspeicherverbrauch auswirken.</span><span class="sxs-lookup"><span data-stu-id="a6907-1114">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="a6907-1115">Daten, die automatisch von den `Authorize` auf die Hub-Klasse angewendeten Attributen gesammelt werden.</span><span class="sxs-lookup"><span data-stu-id="a6907-1115">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="a6907-1116">Eine Liste von [iautorizedata](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) -Objekten, die verwendet werden, um zu bestimmen, ob ein Client für die Verbindung mit dem Hub autorisiert ist.</span><span class="sxs-lookup"><span data-stu-id="a6907-1116">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="a6907-1117">32 KB</span><span class="sxs-lookup"><span data-stu-id="a6907-1117">32 KB</span></span> | <span data-ttu-id="a6907-1118">Die maximale Anzahl von Bytes, die von der APP gesendet werden, die der Server puffert.</span><span class="sxs-lookup"><span data-stu-id="a6907-1118">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="a6907-1119">Wenn Sie diesen Wert erhöhen, kann der Server größere Nachrichten senden, kann sich jedoch negativ auf die Arbeitsspeicher Auslastung auswirken.</span><span class="sxs-lookup"><span data-stu-id="a6907-1119">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="a6907-1120">Alle Transporte sind aktiviert.</span><span class="sxs-lookup"><span data-stu-id="a6907-1120">All Transports are enabled.</span></span> | <span data-ttu-id="a6907-1121">Ein Bitflags-Enumeration von `HttpTransportType` Werten, die die Transporte einschränken können, die ein Client für die Verbindungs Herstellung verwenden kann.</span><span class="sxs-lookup"><span data-stu-id="a6907-1121">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="a6907-1122">Siehe unten.</span><span class="sxs-lookup"><span data-stu-id="a6907-1122">See below.</span></span> | <span data-ttu-id="a6907-1123">Zusätzliche Optionen, die für den langen Abruf Transport spezifisch sind.</span><span class="sxs-lookup"><span data-stu-id="a6907-1123">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="a6907-1124">Siehe unten.</span><span class="sxs-lookup"><span data-stu-id="a6907-1124">See below.</span></span> | <span data-ttu-id="a6907-1125">Zusätzliche Optionen für den websockets-Transport.</span><span class="sxs-lookup"><span data-stu-id="a6907-1125">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="a6907-1126">Der lange Abruf Transport bietet zusätzliche Optionen, die mit der-Eigenschaft konfiguriert werden können `LongPolling` :</span><span class="sxs-lookup"><span data-stu-id="a6907-1126">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="a6907-1127">Option</span><span class="sxs-lookup"><span data-stu-id="a6907-1127">Option</span></span> | <span data-ttu-id="a6907-1128">Standardwert</span><span class="sxs-lookup"><span data-stu-id="a6907-1128">Default Value</span></span> | <span data-ttu-id="a6907-1129">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="a6907-1129">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="a6907-1130">90 Sekunden</span><span class="sxs-lookup"><span data-stu-id="a6907-1130">90 seconds</span></span> | <span data-ttu-id="a6907-1131">Die maximale Zeitspanne, die der Server wartet, bis eine Nachricht an den Client gesendet wird, bevor eine einzelne Abruf Anforderung beendet wird.</span><span class="sxs-lookup"><span data-stu-id="a6907-1131">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="a6907-1132">Das verringern dieses Werts bewirkt, dass der Client neue Abruf Anforderungen häufiger ausgibt.</span><span class="sxs-lookup"><span data-stu-id="a6907-1132">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="a6907-1133">Der WebSocket-Transport verfügt über zusätzliche Optionen, die mit der-Eigenschaft konfiguriert werden können `WebSockets` :</span><span class="sxs-lookup"><span data-stu-id="a6907-1133">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="a6907-1134">Option</span><span class="sxs-lookup"><span data-stu-id="a6907-1134">Option</span></span> | <span data-ttu-id="a6907-1135">Standardwert</span><span class="sxs-lookup"><span data-stu-id="a6907-1135">Default Value</span></span> | <span data-ttu-id="a6907-1136">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="a6907-1136">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="a6907-1137">5 Sekunden</span><span class="sxs-lookup"><span data-stu-id="a6907-1137">5 seconds</span></span> | <span data-ttu-id="a6907-1138">Wenn der Client nach dem Schließen des Servers nicht innerhalb dieses Zeitraums geschlossen werden kann, wird die Verbindung beendet.</span><span class="sxs-lookup"><span data-stu-id="a6907-1138">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="a6907-1139">Ein Delegat, der verwendet werden kann, um den- `Sec-WebSocket-Protocol` Header auf einen benutzerdefinierten Wert festzulegen.</span><span class="sxs-lookup"><span data-stu-id="a6907-1139">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="a6907-1140">Der Delegat empfängt die vom Client angeforderten Werte als Eingabe und erwartet, dass der gewünschte Wert zurückgegeben wird.</span><span class="sxs-lookup"><span data-stu-id="a6907-1140">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="a6907-1141">Konfigurieren von Clientoptionen</span><span class="sxs-lookup"><span data-stu-id="a6907-1141">Configure client options</span></span>

<span data-ttu-id="a6907-1142">Client Optionen können für den Typ konfiguriert werden `HubConnectionBuilder` (verfügbar in den .net-und JavaScript-Clients).</span><span class="sxs-lookup"><span data-stu-id="a6907-1142">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="a6907-1143">Sie steht auch im Java-Client zur Verfügung, aber die `HttpHubConnectionBuilder` Unterklasse enthält die Generator-Konfigurationsoptionen sowie die `HubConnection` selbst.</span><span class="sxs-lookup"><span data-stu-id="a6907-1143">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="a6907-1144">Konfigurieren der Protokollierung</span><span class="sxs-lookup"><span data-stu-id="a6907-1144">Configure logging</span></span>

<span data-ttu-id="a6907-1145">Die Protokollierung wird mithilfe der-Methode im .NET-Client konfiguriert `ConfigureLogging` .</span><span class="sxs-lookup"><span data-stu-id="a6907-1145">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="a6907-1146">Protokollierungs Anbieter und Filter können auf die gleiche Weise wie auf dem Server registriert werden.</span><span class="sxs-lookup"><span data-stu-id="a6907-1146">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="a6907-1147">Weitere Informationen finden Sie in der Dokumentation zur [Protokollierung ASP.net Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="a6907-1147">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="a6907-1148">Um Protokollierungs Anbieter zu registrieren, müssen Sie die erforderlichen Pakete installieren.</span><span class="sxs-lookup"><span data-stu-id="a6907-1148">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="a6907-1149">Eine vollständige Liste finden Sie im Abschnitt [integrierte Protokollierungs Anbieter](xref:fundamentals/logging/index#built-in-logging-providers) der Dokumentation.</span><span class="sxs-lookup"><span data-stu-id="a6907-1149">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="a6907-1150">Um z. b. die Konsolen Protokollierung zu aktivieren, installieren Sie das `Microsoft.Extensions.Logging.Console` nuget-Paket.</span><span class="sxs-lookup"><span data-stu-id="a6907-1150">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="a6907-1151">Die `AddConsole` Erweiterungsmethode aufzurufen:</span><span class="sxs-lookup"><span data-stu-id="a6907-1151">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="a6907-1152">Im JavaScript-Client ist eine ähnliche `configureLogging` Methode vorhanden.</span><span class="sxs-lookup"><span data-stu-id="a6907-1152">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="a6907-1153">Geben `LogLevel` Sie einen Wert an, der die minimale Ebene der zu erstellenden Protokollmeldungen angibt.</span><span class="sxs-lookup"><span data-stu-id="a6907-1153">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="a6907-1154">Protokolle werden in das Browser Konsolenfenster geschrieben.</span><span class="sxs-lookup"><span data-stu-id="a6907-1154">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="a6907-1155">Um die Protokollierung vollständig zu deaktivieren, geben Sie `signalR.LogLevel.None` in der `configureLogging` Methode an</span><span class="sxs-lookup"><span data-stu-id="a6907-1155">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="a6907-1156">Weitere Informationen zur Protokollierung finden Sie in der [ SignalR Diagnose Dokumentation](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="a6907-1156">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="a6907-1157">Der SignalR Java-Client verwendet die [SLF4J](https://www.slf4j.org/) -Bibliothek für die Protokollierung.</span><span class="sxs-lookup"><span data-stu-id="a6907-1157">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="a6907-1158">Dabei handelt es sich um eine allgemeine Protokollierungs-API, die es Benutzern der Bibliothek ermöglicht, ihre eigene spezifische Protokollierungs Implementierung zu wählen, indem Sie eine bestimmte Protokollierungs Abhängigkeit einbinden.</span><span class="sxs-lookup"><span data-stu-id="a6907-1158">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="a6907-1159">Der folgende Code Ausschnitt zeigt, wie `java.util.logging` mit dem Java- SignalR Client verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="a6907-1159">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="a6907-1160">Wenn Sie die Protokollierung in ihren Abhängigkeiten nicht konfigurieren, lädt SLF4J eine Standard Protokollierung ohne Vorgang mit folgender Warnmeldung:</span><span class="sxs-lookup"><span data-stu-id="a6907-1160">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="a6907-1161">Dies kann sicher ignoriert werden.</span><span class="sxs-lookup"><span data-stu-id="a6907-1161">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="a6907-1162">Zulässige Transporte konfigurieren</span><span class="sxs-lookup"><span data-stu-id="a6907-1162">Configure allowed transports</span></span>

<span data-ttu-id="a6907-1163">Die von verwendeten Transporte SignalR können im-Befehl `WithUrl` ( `withUrl` in JavaScript) konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="a6907-1163">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="a6907-1164">Ein bitweises OR der Werte von `HttpTransportType` kann verwendet werden, um den Client so einzuschränken, dass nur die angegebenen Transporte verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="a6907-1164">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="a6907-1165">Alle Transporte sind standardmäßig aktiviert.</span><span class="sxs-lookup"><span data-stu-id="a6907-1165">All transports are enabled by default.</span></span>

<span data-ttu-id="a6907-1166">Um z. b. den Server-Sent Ereignis Transport zu deaktivieren, aber websockets und lange Abruf Verbindungen zuzulassen:</span><span class="sxs-lookup"><span data-stu-id="a6907-1166">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="a6907-1167">Im JavaScript-Client werden Transporte durch Festlegen des- `transport` Felds für das Options-Objekt konfiguriert, das für bereitgestellt wird `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="a6907-1167">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

### <a name="configure-bearer-authentication"></a><span data-ttu-id="a6907-1168">Konfigurieren der bearerauthentifizierung</span><span class="sxs-lookup"><span data-stu-id="a6907-1168">Configure bearer authentication</span></span>

<span data-ttu-id="a6907-1169">Um Authentifizierungsdaten zusammen mit Anforderungen bereitzustellen SignalR , verwenden `AccessTokenProvider` Sie die-Option ( `accessTokenFactory` in JavaScript), um eine Funktion anzugeben, die das gewünschte Zugriffs Token zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="a6907-1169">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="a6907-1170">Im .NET-Client wird dieses Zugriffs Token als http-tokenauthentifizierungstoken (mit dem- `Authorization` Header mit dem Typ) an das Token "bearerauthentifizierung" geleitet `Bearer` .</span><span class="sxs-lookup"><span data-stu-id="a6907-1170">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="a6907-1171">Im JavaScript-Client wird das Zugriffs Token als bearertoken verwendet, **außer** in einigen Fällen, in denen Browser-APIs die Fähigkeit zum Anwenden von Headern einschränken (insbesondere in Server-Sent Ereignissen und websockets-Anforderungen).</span><span class="sxs-lookup"><span data-stu-id="a6907-1171">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="a6907-1172">In diesen Fällen wird das Zugriffs Token als Abfrage Zeichenfolgen-Wert bereitgestellt `access_token` .</span><span class="sxs-lookup"><span data-stu-id="a6907-1172">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="a6907-1173">Im .NET-Client kann die `AccessTokenProvider` Option mithilfe des Options Delegaten in angegeben werden `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="a6907-1173">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="a6907-1174">Im JavaScript-Client wird das Zugriffs Token konfiguriert, indem das- `accessTokenFactory` Feld für das Options-Objekt in festgelegt wird `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="a6907-1174">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

<span data-ttu-id="a6907-1175">Im SignalR Java-Client können Sie ein bearertoken für die Authentifizierung konfigurieren, indem Sie eine zugriffstokenfactory für " [httphubconnectionbuilder](/java/api/com.microsoft.signalr.httphubconnectionbuilder?view=aspnet-signalr-java)" bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="a6907-1175">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr.httphubconnectionbuilder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="a6907-1176">Verwenden Sie [withaccesstokenfactory](/java/api/com.microsoft.signalr.httphubconnectionbuilder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) , um eine [einzelne \<String> ](https://reactivex.io/documentation/single.html) [rxjava](https://github.com/ReactiveX/RxJava) -Instanz bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="a6907-1176">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr.httphubconnectionbuilder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="a6907-1177">Beim Aufrufen von " [Single.](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)Write" können Sie Logik schreiben, um Zugriffs Token für den Client zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="a6907-1177">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="a6907-1178">Konfigurieren von Timeout-und Keep-Alive-Optionen</span><span class="sxs-lookup"><span data-stu-id="a6907-1178">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="a6907-1179">Zusätzliche Optionen zum Konfigurieren von Timeout und Keep-Alive-Verhalten sind für das `HubConnection` Objekt selbst verfügbar:</span><span class="sxs-lookup"><span data-stu-id="a6907-1179">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="a6907-1180">.NET</span><span class="sxs-lookup"><span data-stu-id="a6907-1180">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="a6907-1181">Option</span><span class="sxs-lookup"><span data-stu-id="a6907-1181">Option</span></span> | <span data-ttu-id="a6907-1182">Standardwert</span><span class="sxs-lookup"><span data-stu-id="a6907-1182">Default value</span></span> | <span data-ttu-id="a6907-1183">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="a6907-1183">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="a6907-1184">30 Sekunden (30.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="a6907-1184">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="a6907-1185">Timeout für die Serveraktivität.</span><span class="sxs-lookup"><span data-stu-id="a6907-1185">Timeout for server activity.</span></span> <span data-ttu-id="a6907-1186">Wenn der Server in diesem Intervall keine Nachricht gesendet hat, betrachtet der Client den Server als getrennt und löst das `Closed` Ereignis `onclose` aus (in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="a6907-1186">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="a6907-1187">Dieser Wert muss groß genug sein, damit eine Ping-Nachricht vom Server gesendet **und** innerhalb des Timeout Intervalls vom Client empfangen wird.</span><span class="sxs-lookup"><span data-stu-id="a6907-1187">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="a6907-1188">Der empfohlene Wert ist eine Zahl, die mindestens dem Wert des Servers `KeepAliveInterval` entspricht, um Zeit für das Eintreffen von Pings zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="a6907-1188">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="a6907-1189">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="a6907-1189">15 seconds</span></span> | <span data-ttu-id="a6907-1190">Timeout für den anfänglichen Server Hand Shake.</span><span class="sxs-lookup"><span data-stu-id="a6907-1190">Timeout for initial server handshake.</span></span> <span data-ttu-id="a6907-1191">Wenn der Server in diesem Intervall keine Hand Shake Antwort sendet, bricht der Client den Handshake ab und löst das `Closed` Ereignis `onclose` aus (in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="a6907-1191">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="a6907-1192">Dies ist eine erweiterte Einstellung, die nur geändert werden sollte, wenn aufgrund schwerer Netzwerk Latenz Fehler aufgrund von Hand Shake Timeout auftreten.</span><span class="sxs-lookup"><span data-stu-id="a6907-1192">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="a6907-1193">Weitere Details zum Hand Shake Prozess finden Sie in der [ SignalR Hub-Protokollspezifikation](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="a6907-1193">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

<span data-ttu-id="a6907-1194">Im .NET-Client werden Timeout Werte als Werte angegeben `TimeSpan` .</span><span class="sxs-lookup"><span data-stu-id="a6907-1194">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="a6907-1195">JavaScript</span><span class="sxs-lookup"><span data-stu-id="a6907-1195">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="a6907-1196">Option</span><span class="sxs-lookup"><span data-stu-id="a6907-1196">Option</span></span> | <span data-ttu-id="a6907-1197">Standardwert</span><span class="sxs-lookup"><span data-stu-id="a6907-1197">Default value</span></span> | <span data-ttu-id="a6907-1198">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="a6907-1198">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="a6907-1199">30 Sekunden (30.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="a6907-1199">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="a6907-1200">Timeout für die Serveraktivität.</span><span class="sxs-lookup"><span data-stu-id="a6907-1200">Timeout for server activity.</span></span> <span data-ttu-id="a6907-1201">Wenn der Server in diesem Intervall keine Nachricht gesendet hat, betrachtet der Client den Server als getrennt und löst das `onclose` Ereignis aus.</span><span class="sxs-lookup"><span data-stu-id="a6907-1201">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="a6907-1202">Dieser Wert muss groß genug sein, damit eine Ping-Nachricht vom Server gesendet **und** innerhalb des Timeout Intervalls vom Client empfangen wird.</span><span class="sxs-lookup"><span data-stu-id="a6907-1202">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="a6907-1203">Der empfohlene Wert ist eine Zahl, die mindestens dem Wert des Servers `KeepAliveInterval` entspricht, um Zeit für das Eintreffen von Pings zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="a6907-1203">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |

# <a name="java"></a>[<span data-ttu-id="a6907-1204">Java</span><span class="sxs-lookup"><span data-stu-id="a6907-1204">Java</span></span>](#tab/java)

| <span data-ttu-id="a6907-1205">Option</span><span class="sxs-lookup"><span data-stu-id="a6907-1205">Option</span></span> | <span data-ttu-id="a6907-1206">Standardwert</span><span class="sxs-lookup"><span data-stu-id="a6907-1206">Default value</span></span> | <span data-ttu-id="a6907-1207">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="a6907-1207">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="a6907-1208">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="a6907-1208">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="a6907-1209">30 Sekunden (30.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="a6907-1209">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="a6907-1210">Timeout für die Serveraktivität.</span><span class="sxs-lookup"><span data-stu-id="a6907-1210">Timeout for server activity.</span></span> <span data-ttu-id="a6907-1211">Wenn der Server in diesem Intervall keine Nachricht gesendet hat, betrachtet der Client den Server als getrennt und löst das `onClose` Ereignis aus.</span><span class="sxs-lookup"><span data-stu-id="a6907-1211">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="a6907-1212">Dieser Wert muss groß genug sein, damit eine Ping-Nachricht vom Server gesendet **und** innerhalb des Timeout Intervalls vom Client empfangen wird.</span><span class="sxs-lookup"><span data-stu-id="a6907-1212">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="a6907-1213">Der empfohlene Wert ist eine Zahl, die mindestens dem Wert des Servers entspricht `KeepAliveInterval` , um Zeit für das Eintreffen von Pings zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="a6907-1213">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="a6907-1214">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="a6907-1214">15 seconds</span></span> | <span data-ttu-id="a6907-1215">Timeout für den anfänglichen Server Hand Shake.</span><span class="sxs-lookup"><span data-stu-id="a6907-1215">Timeout for initial server handshake.</span></span> <span data-ttu-id="a6907-1216">Wenn der Server in diesem Intervall keine Hand Shake Antwort sendet, bricht der Client den Handshake ab und löst das- `onClose` Ereignis aus.</span><span class="sxs-lookup"><span data-stu-id="a6907-1216">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="a6907-1217">Dies ist eine erweiterte Einstellung, die nur geändert werden sollte, wenn aufgrund schwerer Netzwerk Latenz Fehler aufgrund von Hand Shake Timeout auftreten.</span><span class="sxs-lookup"><span data-stu-id="a6907-1217">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="a6907-1218">Weitere Details zum Hand Shake Prozess finden Sie in der [ SignalR Hub-Protokollspezifikation](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="a6907-1218">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="a6907-1219">Konfigurieren zusätzlicher Optionen</span><span class="sxs-lookup"><span data-stu-id="a6907-1219">Configure additional options</span></span>

<span data-ttu-id="a6907-1220">Zusätzliche Optionen können in der- `WithUrl` Methode ( `withUrl` in JavaScript) für `HubConnectionBuilder` oder für die verschiedenen Konfigurations-APIs auf dem `HttpHubConnectionBuilder` im Java-Client konfiguriert werden:</span><span class="sxs-lookup"><span data-stu-id="a6907-1220">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="a6907-1221">.NET</span><span class="sxs-lookup"><span data-stu-id="a6907-1221">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="a6907-1222">.Net-Option</span><span class="sxs-lookup"><span data-stu-id="a6907-1222">.NET Option</span></span> |  <span data-ttu-id="a6907-1223">Standardwert</span><span class="sxs-lookup"><span data-stu-id="a6907-1223">Default value</span></span> | <span data-ttu-id="a6907-1224">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="a6907-1224">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="a6907-1225">Eine Funktion, die eine Zeichenfolge zurückgibt, die als bearerauthentifizierungstoken in HTTP-Anforderungen bereitgestellt</span><span class="sxs-lookup"><span data-stu-id="a6907-1225">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="a6907-1226">Legen Sie dies auf fest, `true` um den Aushandlungs Schritt zu überspringen</span><span class="sxs-lookup"><span data-stu-id="a6907-1226">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="a6907-1227">Wird **nur unterstützt, wenn der websockets-Transport der einzige aktivierte Transport ist**.</span><span class="sxs-lookup"><span data-stu-id="a6907-1227">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="a6907-1228">Diese Einstellung kann nicht aktiviert werden, wenn der Azure-Dienst verwendet wird SignalR .</span><span class="sxs-lookup"><span data-stu-id="a6907-1228">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="a6907-1229">Leer</span><span class="sxs-lookup"><span data-stu-id="a6907-1229">Empty</span></span> | <span data-ttu-id="a6907-1230">Eine Auflistung von TLS-Zertifikaten, die zum Authentifizieren von Anforderungen gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="a6907-1230">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="a6907-1231">Leer</span><span class="sxs-lookup"><span data-stu-id="a6907-1231">Empty</span></span> | <span data-ttu-id="a6907-1232">Eine Auflistung von http- cookie s, die mit jeder HTTP-Anforderung gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="a6907-1232">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="a6907-1233">Leer</span><span class="sxs-lookup"><span data-stu-id="a6907-1233">Empty</span></span> | <span data-ttu-id="a6907-1234">Anmelde Informationen, die mit jeder HTTP-Anforderung gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="a6907-1234">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="a6907-1235">5 Sekunden</span><span class="sxs-lookup"><span data-stu-id="a6907-1235">5 seconds</span></span> | <span data-ttu-id="a6907-1236">Nur websockets.</span><span class="sxs-lookup"><span data-stu-id="a6907-1236">WebSockets only.</span></span> <span data-ttu-id="a6907-1237">Die maximale Zeitspanne, die der Client nach dem Schließen des Servers wartet, um die Anforderung zum Schließen zu bestätigen.</span><span class="sxs-lookup"><span data-stu-id="a6907-1237">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="a6907-1238">Wenn der Server die Schließung innerhalb dieses Zeitraums nicht anerkennt, trennt der Client die Verbindung.</span><span class="sxs-lookup"><span data-stu-id="a6907-1238">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="a6907-1239">Leer</span><span class="sxs-lookup"><span data-stu-id="a6907-1239">Empty</span></span> | <span data-ttu-id="a6907-1240">Eine Karte mit zusätzlichen HTTP-Headern, die mit jeder HTTP-Anforderung gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="a6907-1240">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="a6907-1241">Ein Delegat, der verwendet werden kann, um den `HttpMessageHandler` zum Senden von HTTP-Anforderungen verwendeten zu konfigurieren oder zu ersetzen.</span><span class="sxs-lookup"><span data-stu-id="a6907-1241">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="a6907-1242">Wird nicht für WebSocket-Verbindungen verwendet.</span><span class="sxs-lookup"><span data-stu-id="a6907-1242">Not used for WebSocket connections.</span></span> <span data-ttu-id="a6907-1243">Dieser Delegat muss einen Wert zurückgeben, der nicht NULL ist, und erhält den Standardwert als Parameter.</span><span class="sxs-lookup"><span data-stu-id="a6907-1243">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="a6907-1244">Ändern Sie die Einstellungen für diesen Standardwert, und geben Sie ihn zurück, oder geben Sie eine neue `HttpMessageHandler` Instanz zurück.</span><span class="sxs-lookup"><span data-stu-id="a6907-1244">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="a6907-1245">**Wenn Sie den Handler ersetzen, stellen Sie sicher, dass die Einstellungen, die Sie beibehalten möchten, vom bereitgestellten Handler kopiert werden. Andernfalls gelten die konfigurierten Optionen (z. b. Cookie s und Header) nicht für den neuen Handler.**</span><span class="sxs-lookup"><span data-stu-id="a6907-1245">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="a6907-1246">Ein HTTP-Proxy, der beim Senden von HTTP-Anforderungen verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="a6907-1246">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="a6907-1247">Legen Sie diesen booleschen Wert fest, um die Standard Anmelde Informationen für http-und websockets-Anforderungen zu senden</span><span class="sxs-lookup"><span data-stu-id="a6907-1247">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="a6907-1248">Dies ermöglicht die Verwendung der Windows-Authentifizierung.</span><span class="sxs-lookup"><span data-stu-id="a6907-1248">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="a6907-1249">Ein Delegat, der zum Konfigurieren zusätzlicher WebSocket-Optionen verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="a6907-1249">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="a6907-1250">Empfängt eine Instanz von [clientwebsocketoptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) , die zum Konfigurieren der Optionen verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="a6907-1250">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="a6907-1251">JavaScript</span><span class="sxs-lookup"><span data-stu-id="a6907-1251">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="a6907-1252">JavaScript-Option</span><span class="sxs-lookup"><span data-stu-id="a6907-1252">JavaScript Option</span></span> | <span data-ttu-id="a6907-1253">Standardwert</span><span class="sxs-lookup"><span data-stu-id="a6907-1253">Default Value</span></span> | <span data-ttu-id="a6907-1254">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="a6907-1254">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="a6907-1255">Eine Funktion, die eine Zeichenfolge zurückgibt, die als bearerauthentifizierungstoken in HTTP-Anforderungen bereitgestellt</span><span class="sxs-lookup"><span data-stu-id="a6907-1255">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `transport` | `null` | <span data-ttu-id="a6907-1256">Ein- <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType> Wert, der den für die Verbindung zu verwendenden Transport angibt.</span><span class="sxs-lookup"><span data-stu-id="a6907-1256">An <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType> value specifying the transport to use for the connection.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="a6907-1257">Legen Sie auf fest, `true` um die Bytes/Zeichen der vom Client gesendeten und empfangenen Nachrichten zu protokollieren.</span><span class="sxs-lookup"><span data-stu-id="a6907-1257">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="a6907-1258">Legen Sie dies auf fest, `true` um den Aushandlungs Schritt zu überspringen</span><span class="sxs-lookup"><span data-stu-id="a6907-1258">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="a6907-1259">Wird **nur unterstützt, wenn der websockets-Transport der einzige aktivierte Transport ist**.</span><span class="sxs-lookup"><span data-stu-id="a6907-1259">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="a6907-1260">Diese Einstellung kann nicht aktiviert werden, wenn der Azure-Dienst verwendet wird SignalR .</span><span class="sxs-lookup"><span data-stu-id="a6907-1260">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="a6907-1261">Java</span><span class="sxs-lookup"><span data-stu-id="a6907-1261">Java</span></span>](#tab/java)

| <span data-ttu-id="a6907-1262">Java-Option</span><span class="sxs-lookup"><span data-stu-id="a6907-1262">Java Option</span></span> | <span data-ttu-id="a6907-1263">Standardwert</span><span class="sxs-lookup"><span data-stu-id="a6907-1263">Default Value</span></span> | <span data-ttu-id="a6907-1264">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="a6907-1264">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="a6907-1265">Eine Funktion, die eine Zeichenfolge zurückgibt, die als bearerauthentifizierungstoken in HTTP-Anforderungen bereitgestellt</span><span class="sxs-lookup"><span data-stu-id="a6907-1265">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="a6907-1266">Legen Sie dies auf fest, `true` um den Aushandlungs Schritt zu überspringen</span><span class="sxs-lookup"><span data-stu-id="a6907-1266">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="a6907-1267">Wird **nur unterstützt, wenn der websockets-Transport der einzige aktivierte Transport ist**.</span><span class="sxs-lookup"><span data-stu-id="a6907-1267">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="a6907-1268">Diese Einstellung kann nicht aktiviert werden, wenn der Azure-Dienst verwendet wird SignalR .</span><span class="sxs-lookup"><span data-stu-id="a6907-1268">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="a6907-1269">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="a6907-1269">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="a6907-1270">Leer</span><span class="sxs-lookup"><span data-stu-id="a6907-1270">Empty</span></span> | <span data-ttu-id="a6907-1271">Eine Karte mit zusätzlichen HTTP-Headern, die mit jeder HTTP-Anforderung gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="a6907-1271">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="a6907-1272">Im .NET-Client können diese Optionen durch den für bereitgestellten Options Delegaten geändert werden `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="a6907-1272">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="a6907-1273">Im JavaScript-Client können diese Optionen in einem JavaScript-Objekt bereitgestellt werden, das für Folgendes bereitgestellt wird `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="a6907-1273">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="a6907-1274">Im Java-Client können diese Optionen mit den Methoden auf der konfiguriert werden, die `HttpHubConnectionBuilder` von der `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="a6907-1274">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="a6907-1275">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="a6907-1275">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
