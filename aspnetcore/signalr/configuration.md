---
title: :::no-loc(SignalR):::-Konfiguration in ASP.NET Core
author: bradygaster
description: 'Erfahren Sie, wie Sie ASP.net Core- :::no-loc(SignalR)::: apps konfigurieren.'
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/12/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: signalr/configuration
ms.openlocfilehash: 7dac8c84683553a52e07ecc61c8bcf8616e77dc6
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061235"
---
# <a name="aspnet-core-no-locsignalr-configuration"></a><span data-ttu-id="f57c3-103">:::no-loc(SignalR):::-Konfiguration in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f57c3-103">ASP.NET Core :::no-loc(SignalR)::: configuration</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="f57c3-104">JSON/messagepack-Serialisierungsoptionen</span><span class="sxs-lookup"><span data-stu-id="f57c3-104">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="f57c3-105">ASP.net Core :::no-loc(SignalR)::: unterstützt zwei Protokolle zum Codieren von Nachrichten: [JSON](https://www.json.org/) und [messagepack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="f57c3-105">ASP.NET Core :::no-loc(SignalR)::: supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="f57c3-106">Jedes Protokoll verfügt über Konfigurationsoptionen für die Serialisierung.</span><span class="sxs-lookup"><span data-stu-id="f57c3-106">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="f57c3-107">Die JSON-Serialisierung kann mithilfe der [addjsonprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) -Erweiterungsmethode auf dem Server konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="f57c3-107">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="f57c3-108">`AddJsonProtocol`kann nach [Add :::no-loc(SignalR)::: ](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in hinzugefügt werden `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="f57c3-108">`AddJsonProtocol` can be added after [Add:::no-loc(SignalR):::](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="f57c3-109">Die- `AddJsonProtocol` Methode nimmt einen Delegaten an, der ein- `options` Objekt empfängt.</span><span class="sxs-lookup"><span data-stu-id="f57c3-109">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="f57c3-110">Die [payloadserializeroptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) -Eigenschaft für dieses Objekt ist ein- `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> Objekt, das zum Konfigurieren der Serialisierung von Argumenten und Rückgabe Werten verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="f57c3-110">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="f57c3-111">Weitere Informationen finden Sie in den [System.Text.Jszur Dokumentation](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="f57c3-111">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="f57c3-112">Verwenden Sie beispielsweise den folgenden Code in, um das Serialisierungsprogramm so zu konfigurieren, dass die Groß-/Kleinschreibung von Eigenschaftsnamen nicht geändert wird. verwenden Sie dazu den folgenden Code in `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="f57c3-112">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.Add:::no-loc(SignalR):::()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="f57c3-113">Im .NET-Client ist dieselbe `AddJsonProtocol` Erweiterungsmethode auf [hubconnectionbuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)vorhanden.</span><span class="sxs-lookup"><span data-stu-id="f57c3-113">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="f57c3-114">Der `Microsoft.Extensions.DependencyInjection` Namespace muss importiert werden, um die Erweiterungsmethode aufzulösen:</span><span class="sxs-lookup"><span data-stu-id="f57c3-114">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="f57c3-115">Zurzeit ist es nicht möglich, JSON-Serialisierung im JavaScript-Client zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="f57c3-115">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="f57c3-116">Zum Newtonsoft.Jswechseln</span><span class="sxs-lookup"><span data-stu-id="f57c3-116">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="f57c3-117">Wenn Sie Features von benötigen `Newtonsoft.Json` , die in nicht unterstützt `System.Text.Json` werden, finden Sie weitere Informationen unter [Wechseln zu Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="f57c3-117">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="f57c3-118">Messagepack-Serialisierungsoptionen</span><span class="sxs-lookup"><span data-stu-id="f57c3-118">MessagePack serialization options</span></span>

<span data-ttu-id="f57c3-119">Die messagepack-Serialisierung kann konfiguriert werden, indem ein Delegat für den [addmessagepackprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) -Aufruf bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="f57c3-119">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="f57c3-120">Weitere Informationen finden Sie [unter :::no-loc(SignalR)::: messagepack in](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="f57c3-120">See [MessagePack in :::no-loc(SignalR):::](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="f57c3-121">Zurzeit ist es nicht möglich, die messagepack-Serialisierung im JavaScript-Client zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="f57c3-121">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="f57c3-122">Konfigurieren von Serveroptionen</span><span class="sxs-lookup"><span data-stu-id="f57c3-122">Configure server options</span></span>

<span data-ttu-id="f57c3-123">In der folgenden Tabelle werden die Optionen zum Konfigurieren von :::no-loc(SignalR)::: Hubs beschrieben:</span><span class="sxs-lookup"><span data-stu-id="f57c3-123">The following table describes options for configuring :::no-loc(SignalR)::: hubs:</span></span>

| <span data-ttu-id="f57c3-124">Option</span><span class="sxs-lookup"><span data-stu-id="f57c3-124">Option</span></span> | <span data-ttu-id="f57c3-125">Standardwert</span><span class="sxs-lookup"><span data-stu-id="f57c3-125">Default Value</span></span> | <span data-ttu-id="f57c3-126">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="f57c3-126">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="f57c3-127">30 Sekunden</span><span class="sxs-lookup"><span data-stu-id="f57c3-127">30 seconds</span></span> | <span data-ttu-id="f57c3-128">Der Server wird vom-Client als getrennt betrachtet, wenn in diesem Intervall keine Nachricht (einschließlich Keep-Alive) empfangen wurde.</span><span class="sxs-lookup"><span data-stu-id="f57c3-128">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="f57c3-129">Es kann länger dauern, bis der Client als getrennt gekennzeichnet ist, weil dies implementiert ist.</span><span class="sxs-lookup"><span data-stu-id="f57c3-129">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="f57c3-130">Der empfohlene Wert ist Double- `KeepAliveInterval` Wert.</span><span class="sxs-lookup"><span data-stu-id="f57c3-130">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="f57c3-131">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="f57c3-131">15 seconds</span></span> | <span data-ttu-id="f57c3-132">Wenn der Client innerhalb dieses Zeitraums keine anfängliche Hand Shake Nachricht sendet, wird die Verbindung geschlossen.</span><span class="sxs-lookup"><span data-stu-id="f57c3-132">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="f57c3-133">Dies ist eine erweiterte Einstellung, die nur geändert werden sollte, wenn aufgrund schwerer Netzwerk Latenz Fehler aufgrund von Hand Shake Timeout auftreten.</span><span class="sxs-lookup"><span data-stu-id="f57c3-133">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="f57c3-134">Weitere Details zum Hand Shake Prozess finden Sie in der [ :::no-loc(SignalR)::: Hub-Protokollspezifikation](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="f57c3-134">For more detail on the handshake process, see the [:::no-loc(SignalR)::: Hub Protocol Specification](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="f57c3-135">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="f57c3-135">15 seconds</span></span> | <span data-ttu-id="f57c3-136">Wenn der Server innerhalb dieses Intervalls keine Nachricht gesendet hat, wird automatisch eine Ping-Nachricht gesendet, um die Verbindung offen zu halten.</span><span class="sxs-lookup"><span data-stu-id="f57c3-136">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="f57c3-137">Ändern Sie `KeepAliveInterval` die `ServerTimeout` / `serverTimeoutInMilliseconds` Einstellung auf dem Client, wenn Sie sich ändern.</span><span class="sxs-lookup"><span data-stu-id="f57c3-137">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="f57c3-138">Der empfohlene `ServerTimeout` / `serverTimeoutInMilliseconds` Wert ist Double- `KeepAliveInterval` Wert.</span><span class="sxs-lookup"><span data-stu-id="f57c3-138">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="f57c3-139">Alle installierten Protokolle</span><span class="sxs-lookup"><span data-stu-id="f57c3-139">All installed protocols</span></span> | <span data-ttu-id="f57c3-140">Protokolle, die von diesem Hub unterstützt werden.</span><span class="sxs-lookup"><span data-stu-id="f57c3-140">Protocols supported by this hub.</span></span> <span data-ttu-id="f57c3-141">Standardmäßig sind alle auf dem Server registrierten Protokolle zulässig, aber Protokolle können aus dieser Liste entfernt werden, um bestimmte Protokolle für einzelne Hubs zu deaktivieren.</span><span class="sxs-lookup"><span data-stu-id="f57c3-141">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="f57c3-142">Wenn der Wert `true` ist, werden ausführliche Ausnahme Meldungen an Clients zurückgegeben, wenn eine Ausnahme in einer Hub-Methode ausgelöst wird.</span><span class="sxs-lookup"><span data-stu-id="f57c3-142">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="f57c3-143">Der Standardwert ist `false` , da diese Ausnahme Meldungen vertrauliche Informationen enthalten können.</span><span class="sxs-lookup"><span data-stu-id="f57c3-143">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="f57c3-144">Die maximale Anzahl von Elementen, die für Client-uploadstreams gepuffert werden können.</span><span class="sxs-lookup"><span data-stu-id="f57c3-144">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="f57c3-145">Wenn dieser Grenzwert erreicht wird, wird die Verarbeitung von Aufrufen blockiert, bis der Server streamingelemente verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="f57c3-145">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="f57c3-146">32 KB</span><span class="sxs-lookup"><span data-stu-id="f57c3-146">32 KB</span></span> | <span data-ttu-id="f57c3-147">Maximale Größe einer einzelnen eingehenden Hub-Nachricht.</span><span class="sxs-lookup"><span data-stu-id="f57c3-147">Maximum size of a single incoming hub message.</span></span> |
| `MaximumParallelInvocationsPerClient` | <span data-ttu-id="f57c3-148">1</span><span class="sxs-lookup"><span data-stu-id="f57c3-148">1</span></span> | <span data-ttu-id="f57c3-149">Die maximale Anzahl von hubmethoden, die jeder Client parallel vor der Warteschlange abrufen kann.</span><span class="sxs-lookup"><span data-stu-id="f57c3-149">The maximum number of hub methods that each client can call in parallel before queueing.</span></span> |

<span data-ttu-id="f57c3-150">Optionen können für alle Hubs konfiguriert werden, indem ein Options Delegat für den Aufruf in bereitgestellt wird `Add:::no-loc(SignalR):::` `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="f57c3-150">Options can be configured for all hubs by providing an options delegate to the `Add:::no-loc(SignalR):::` call in `Startup.ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Add:::no-loc(SignalR):::(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

<span data-ttu-id="f57c3-151">Optionen für einen einzelnen Hub überschreiben die in bereitgestellten globalen Optionen `Add:::no-loc(SignalR):::` und können mithilfe von konfiguriert werden <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(SignalR):::DependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="f57c3-151">Options for a single hub override the global options provided in `Add:::no-loc(SignalR):::` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(SignalR):::DependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.Add:::no-loc(SignalR):::().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="f57c3-152">Erweiterte http-Konfigurationsoptionen</span><span class="sxs-lookup"><span data-stu-id="f57c3-152">Advanced HTTP configuration options</span></span>

<span data-ttu-id="f57c3-153">Verwenden `HttpConnectionDispatcherOptions` Sie, um erweiterte Einstellungen im Zusammenhang mit Transporte und der Speicherpuffer Verwaltung zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="f57c3-153">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="f57c3-154">Diese Optionen werden konfiguriert, indem ein [Delegat an \<T> maphub](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in übergeben wird `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="f57c3-154">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="f57c3-155">In der folgenden Tabelle werden die Optionen zum Konfigurieren der :::no-loc(SignalR)::: erweiterten http-Optionen von ASP.net Core beschrieben:</span><span class="sxs-lookup"><span data-stu-id="f57c3-155">The following table describes options for configuring ASP.NET Core :::no-loc(SignalR):::'s advanced HTTP options:</span></span>

| <span data-ttu-id="f57c3-156">Option</span><span class="sxs-lookup"><span data-stu-id="f57c3-156">Option</span></span> | <span data-ttu-id="f57c3-157">Standardwert</span><span class="sxs-lookup"><span data-stu-id="f57c3-157">Default Value</span></span> | <span data-ttu-id="f57c3-158">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="f57c3-158">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="f57c3-159">32 KB</span><span class="sxs-lookup"><span data-stu-id="f57c3-159">32 KB</span></span> | <span data-ttu-id="f57c3-160">Die maximale Anzahl von Bytes, die vom Client vor dem Anwenden des backdrucks vom Server gepuffert werden.</span><span class="sxs-lookup"><span data-stu-id="f57c3-160">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="f57c3-161">Wenn Sie diesen Wert erhöhen, kann der Server größere Nachrichten schneller empfangen, ohne dass ein Rückdruck angewendet wird, der Arbeitsspeicher Verbrauch kann jedoch gesteigert werden.</span><span class="sxs-lookup"><span data-stu-id="f57c3-161">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="f57c3-162">Daten, die automatisch von den `Authorize` auf die Hub-Klasse angewendeten Attributen gesammelt werden.</span><span class="sxs-lookup"><span data-stu-id="f57c3-162">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="f57c3-163">Eine Liste von [iautorizedata](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) -Objekten, die verwendet werden, um zu bestimmen, ob ein Client für die Verbindung mit dem Hub autorisiert ist.</span><span class="sxs-lookup"><span data-stu-id="f57c3-163">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="f57c3-164">32 KB</span><span class="sxs-lookup"><span data-stu-id="f57c3-164">32 KB</span></span> | <span data-ttu-id="f57c3-165">Die maximale Anzahl von Bytes, die von der APP gesendet werden, die der Server puffert, bevor der Rückdruck beobachtet wird</span><span class="sxs-lookup"><span data-stu-id="f57c3-165">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="f57c3-166">Wenn Sie diesen Wert erhöhen, kann der Server größere Nachrichten schneller Puffern, ohne dass auf den Rückstand gewartet wird, aber die Arbeitsspeicher Nutzung erhöhen kann.</span><span class="sxs-lookup"><span data-stu-id="f57c3-166">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="f57c3-167">Alle Transporte sind aktiviert.</span><span class="sxs-lookup"><span data-stu-id="f57c3-167">All Transports are enabled.</span></span> | <span data-ttu-id="f57c3-168">Ein Bitflags-Enumeration von `HttpTransportType` Werten, die die Transporte einschränken können, die ein Client für die Verbindungs Herstellung verwenden kann.</span><span class="sxs-lookup"><span data-stu-id="f57c3-168">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="f57c3-169">Siehe unten.</span><span class="sxs-lookup"><span data-stu-id="f57c3-169">See below.</span></span> | <span data-ttu-id="f57c3-170">Zusätzliche Optionen, die für den langen Abruf Transport spezifisch sind.</span><span class="sxs-lookup"><span data-stu-id="f57c3-170">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="f57c3-171">Siehe unten.</span><span class="sxs-lookup"><span data-stu-id="f57c3-171">See below.</span></span> | <span data-ttu-id="f57c3-172">Zusätzliche Optionen für den websockets-Transport.</span><span class="sxs-lookup"><span data-stu-id="f57c3-172">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="f57c3-173">0</span><span class="sxs-lookup"><span data-stu-id="f57c3-173">0</span></span> | <span data-ttu-id="f57c3-174">Geben Sie die Mindestversion des Aushandlungs Protokolls an.</span><span class="sxs-lookup"><span data-stu-id="f57c3-174">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="f57c3-175">Hiermit werden Clients auf neuere Versionen beschränkt.</span><span class="sxs-lookup"><span data-stu-id="f57c3-175">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="f57c3-176">Der lange Abruf Transport bietet zusätzliche Optionen, die mit der-Eigenschaft konfiguriert werden können `LongPolling` :</span><span class="sxs-lookup"><span data-stu-id="f57c3-176">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="f57c3-177">Option</span><span class="sxs-lookup"><span data-stu-id="f57c3-177">Option</span></span> | <span data-ttu-id="f57c3-178">Standardwert</span><span class="sxs-lookup"><span data-stu-id="f57c3-178">Default Value</span></span> | <span data-ttu-id="f57c3-179">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="f57c3-179">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="f57c3-180">90 Sekunden</span><span class="sxs-lookup"><span data-stu-id="f57c3-180">90 seconds</span></span> | <span data-ttu-id="f57c3-181">Die maximale Zeitspanne, die der Server wartet, bis eine Nachricht an den Client gesendet wird, bevor eine einzelne Abruf Anforderung beendet wird.</span><span class="sxs-lookup"><span data-stu-id="f57c3-181">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="f57c3-182">Das verringern dieses Werts bewirkt, dass der Client neue Abruf Anforderungen häufiger ausgibt.</span><span class="sxs-lookup"><span data-stu-id="f57c3-182">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="f57c3-183">Der WebSocket-Transport verfügt über zusätzliche Optionen, die mit der-Eigenschaft konfiguriert werden können `WebSockets` :</span><span class="sxs-lookup"><span data-stu-id="f57c3-183">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="f57c3-184">Option</span><span class="sxs-lookup"><span data-stu-id="f57c3-184">Option</span></span> | <span data-ttu-id="f57c3-185">Standardwert</span><span class="sxs-lookup"><span data-stu-id="f57c3-185">Default Value</span></span> | <span data-ttu-id="f57c3-186">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="f57c3-186">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="f57c3-187">5 Sekunden</span><span class="sxs-lookup"><span data-stu-id="f57c3-187">5 seconds</span></span> | <span data-ttu-id="f57c3-188">Wenn der Client nach dem Schließen des Servers nicht innerhalb dieses Zeitraums geschlossen werden kann, wird die Verbindung beendet.</span><span class="sxs-lookup"><span data-stu-id="f57c3-188">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="f57c3-189">Ein Delegat, der verwendet werden kann, um den- `Sec-WebSocket-Protocol` Header auf einen benutzerdefinierten Wert festzulegen.</span><span class="sxs-lookup"><span data-stu-id="f57c3-189">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="f57c3-190">Der Delegat empfängt die vom Client angeforderten Werte als Eingabe und erwartet, dass der gewünschte Wert zurückgegeben wird.</span><span class="sxs-lookup"><span data-stu-id="f57c3-190">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="f57c3-191">Konfigurieren von Clientoptionen</span><span class="sxs-lookup"><span data-stu-id="f57c3-191">Configure client options</span></span>

<span data-ttu-id="f57c3-192">Client Optionen können für den Typ konfiguriert werden `HubConnectionBuilder` (verfügbar in den .net-und JavaScript-Clients).</span><span class="sxs-lookup"><span data-stu-id="f57c3-192">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="f57c3-193">Sie steht auch im Java-Client zur Verfügung, aber die `HttpHubConnectionBuilder` Unterklasse enthält die Generator-Konfigurationsoptionen sowie die `HubConnection` selbst.</span><span class="sxs-lookup"><span data-stu-id="f57c3-193">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="f57c3-194">Konfigurieren der Protokollierung</span><span class="sxs-lookup"><span data-stu-id="f57c3-194">Configure logging</span></span>

<span data-ttu-id="f57c3-195">Die Protokollierung wird mithilfe der-Methode im .NET-Client konfiguriert `ConfigureLogging` .</span><span class="sxs-lookup"><span data-stu-id="f57c3-195">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="f57c3-196">Protokollierungs Anbieter und Filter können auf die gleiche Weise wie auf dem Server registriert werden.</span><span class="sxs-lookup"><span data-stu-id="f57c3-196">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="f57c3-197">Weitere Informationen finden Sie in der Dokumentation zur [Protokollierung ASP.net Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="f57c3-197">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="f57c3-198">Um Protokollierungs Anbieter zu registrieren, müssen Sie die erforderlichen Pakete installieren.</span><span class="sxs-lookup"><span data-stu-id="f57c3-198">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="f57c3-199">Eine vollständige Liste finden Sie im Abschnitt [integrierte Protokollierungs Anbieter](xref:fundamentals/logging/index#built-in-logging-providers) der Dokumentation.</span><span class="sxs-lookup"><span data-stu-id="f57c3-199">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="f57c3-200">Um z. b. die Konsolen Protokollierung zu aktivieren, installieren Sie das `Microsoft.Extensions.Logging.Console` nuget-Paket.</span><span class="sxs-lookup"><span data-stu-id="f57c3-200">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="f57c3-201">Die `AddConsole` Erweiterungsmethode aufzurufen:</span><span class="sxs-lookup"><span data-stu-id="f57c3-201">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="f57c3-202">Im JavaScript-Client ist eine ähnliche `configureLogging` Methode vorhanden.</span><span class="sxs-lookup"><span data-stu-id="f57c3-202">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="f57c3-203">Geben `LogLevel` Sie einen Wert an, der die minimale Ebene der zu erstellenden Protokollmeldungen angibt.</span><span class="sxs-lookup"><span data-stu-id="f57c3-203">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="f57c3-204">Protokolle werden in das Browser Konsolenfenster geschrieben.</span><span class="sxs-lookup"><span data-stu-id="f57c3-204">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="f57c3-205">Anstelle eines `LogLevel` Werts können Sie auch einen Wert angeben, der den `string` Namen einer Protokollebene darstellt.</span><span class="sxs-lookup"><span data-stu-id="f57c3-205">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="f57c3-206">Dies ist nützlich, wenn :::no-loc(SignalR)::: Sie die Protokollierung in Umgebungen konfigurieren, in denen Sie keinen Zugriff auf die `LogLevel` Konstanten haben.</span><span class="sxs-lookup"><span data-stu-id="f57c3-206">This is useful when configuring :::no-loc(SignalR)::: logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="f57c3-207">In der folgenden Tabelle sind die verfügbaren Protokoll Ebenen aufgeführt.</span><span class="sxs-lookup"><span data-stu-id="f57c3-207">The following table lists the available log levels.</span></span> <span data-ttu-id="f57c3-208">Der von Ihnen bereitgestellte Wert, `configureLogging` der die **minimale** Protokollierung festlegt, die protokolliert werden soll.</span><span class="sxs-lookup"><span data-stu-id="f57c3-208">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="f57c3-209">Nachrichten, die auf dieser Ebene protokolliert werden, **oder die nach der Tabelle aufgeführten Ebenen** werden protokolliert.</span><span class="sxs-lookup"><span data-stu-id="f57c3-209">Messages logged at this level, **or the levels listed after it in the table** , will be logged.</span></span>

| <span data-ttu-id="f57c3-210">String</span><span class="sxs-lookup"><span data-stu-id="f57c3-210">String</span></span>                      | <span data-ttu-id="f57c3-211">LogLevel</span><span class="sxs-lookup"><span data-stu-id="f57c3-211">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="f57c3-212">`info` **oder** `information`</span><span class="sxs-lookup"><span data-stu-id="f57c3-212">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="f57c3-213">`warn` **oder** `warning`</span><span class="sxs-lookup"><span data-stu-id="f57c3-213">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="f57c3-214">Um die Protokollierung vollständig zu deaktivieren, geben Sie `signalR.LogLevel.None` in der `configureLogging` Methode an</span><span class="sxs-lookup"><span data-stu-id="f57c3-214">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="f57c3-215">Weitere Informationen zur Protokollierung finden Sie in der [ :::no-loc(SignalR)::: Diagnose Dokumentation](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="f57c3-215">For more information on logging, see the [:::no-loc(SignalR)::: Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="f57c3-216">Der :::no-loc(SignalR)::: Java-Client verwendet die [SLF4J](https://www.slf4j.org/) -Bibliothek für die Protokollierung.</span><span class="sxs-lookup"><span data-stu-id="f57c3-216">The :::no-loc(SignalR)::: Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="f57c3-217">Dabei handelt es sich um eine allgemeine Protokollierungs-API, die es Benutzern der Bibliothek ermöglicht, ihre eigene spezifische Protokollierungs Implementierung zu wählen, indem Sie eine bestimmte Protokollierungs Abhängigkeit einbinden.</span><span class="sxs-lookup"><span data-stu-id="f57c3-217">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="f57c3-218">Der folgende Code Ausschnitt zeigt, wie `java.util.logging` mit dem Java- :::no-loc(SignalR)::: Client verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="f57c3-218">The following code snippet shows how to use `java.util.logging` with the :::no-loc(SignalR)::: Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="f57c3-219">Wenn Sie die Protokollierung in ihren Abhängigkeiten nicht konfigurieren, lädt SLF4J eine Standard Protokollierung ohne Vorgang mit folgender Warnmeldung:</span><span class="sxs-lookup"><span data-stu-id="f57c3-219">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="f57c3-220">Dies kann sicher ignoriert werden.</span><span class="sxs-lookup"><span data-stu-id="f57c3-220">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="f57c3-221">Zulässige Transporte konfigurieren</span><span class="sxs-lookup"><span data-stu-id="f57c3-221">Configure allowed transports</span></span>

<span data-ttu-id="f57c3-222">Die von verwendeten Transporte :::no-loc(SignalR)::: können im-Befehl `WithUrl` ( `withUrl` in JavaScript) konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="f57c3-222">The transports used by :::no-loc(SignalR)::: can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="f57c3-223">Ein bitweises OR der Werte von `HttpTransportType` kann verwendet werden, um den Client so einzuschränken, dass nur die angegebenen Transporte verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="f57c3-223">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="f57c3-224">Alle Transporte sind standardmäßig aktiviert.</span><span class="sxs-lookup"><span data-stu-id="f57c3-224">All transports are enabled by default.</span></span>

<span data-ttu-id="f57c3-225">Um z. b. den Server-Sent Ereignis Transport zu deaktivieren, aber websockets und lange Abruf Verbindungen zuzulassen:</span><span class="sxs-lookup"><span data-stu-id="f57c3-225">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="f57c3-226">Im JavaScript-Client werden Transporte durch Festlegen des- `transport` Felds für das Options-Objekt konfiguriert, das für bereitgestellt wird `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="f57c3-226">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="f57c3-227">In dieser Version des Java-Clients ist websockets der einzige verfügbare Transport.</span><span class="sxs-lookup"><span data-stu-id="f57c3-227">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="f57c3-228">Im Java-Client wird der Transport mit der-Methode im ausgewählt `withTransport` `HttpHubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="f57c3-228">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="f57c3-229">Der Java-Client verwendet standardmäßig den websockets-Transport.</span><span class="sxs-lookup"><span data-stu-id="f57c3-229">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="f57c3-230">Der :::no-loc(SignalR)::: Java-Client unterstützt noch keinen Transport Fall Back.</span><span class="sxs-lookup"><span data-stu-id="f57c3-230">The :::no-loc(SignalR)::: Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="f57c3-231">Konfigurieren der bearerauthentifizierung</span><span class="sxs-lookup"><span data-stu-id="f57c3-231">Configure bearer authentication</span></span>

<span data-ttu-id="f57c3-232">Um Authentifizierungsdaten zusammen mit Anforderungen bereitzustellen :::no-loc(SignalR)::: , verwenden `AccessTokenProvider` Sie die-Option ( `accessTokenFactory` in JavaScript), um eine Funktion anzugeben, die das gewünschte Zugriffs Token zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="f57c3-232">To provide authentication data along with :::no-loc(SignalR)::: requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="f57c3-233">Im .NET-Client wird dieses Zugriffs Token als http-tokenauthentifizierungstoken (mit dem- `Authorization` Header mit dem Typ) an das Token "bearerauthentifizierung" geleitet `Bearer` .</span><span class="sxs-lookup"><span data-stu-id="f57c3-233">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="f57c3-234">Im JavaScript-Client wird das Zugriffs Token als bearertoken verwendet, **außer** in einigen Fällen, in denen Browser-APIs die Fähigkeit zum Anwenden von Headern einschränken (insbesondere in Server-Sent Ereignissen und websockets-Anforderungen).</span><span class="sxs-lookup"><span data-stu-id="f57c3-234">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="f57c3-235">In diesen Fällen wird das Zugriffs Token als Abfrage Zeichenfolgen-Wert bereitgestellt `access_token` .</span><span class="sxs-lookup"><span data-stu-id="f57c3-235">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="f57c3-236">Im .NET-Client kann die `AccessTokenProvider` Option mithilfe des Options Delegaten in angegeben werden `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="f57c3-236">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="f57c3-237">Im JavaScript-Client wird das Zugriffs Token konfiguriert, indem das- `accessTokenFactory` Feld für das Options-Objekt in festgelegt wird `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="f57c3-237">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="f57c3-238">Im :::no-loc(SignalR)::: Java-Client können Sie ein bearertoken für die Authentifizierung konfigurieren, indem Sie eine zugriffstokenfactory für " [httphubconnectionbuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)" bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="f57c3-238">In the :::no-loc(SignalR)::: Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="f57c3-239">Verwenden Sie [withaccesstokenfactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) , um eine [einzelne \<String> ](https://reactivex.io/documentation/single.html) [rxjava](https://github.com/ReactiveX/RxJava) -Instanz bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="f57c3-239">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="f57c3-240">Beim Aufrufen von " [Single.](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)Write" können Sie Logik schreiben, um Zugriffs Token für den Client zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="f57c3-240">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="f57c3-241">Konfigurieren von Timeout-und Keep-Alive-Optionen</span><span class="sxs-lookup"><span data-stu-id="f57c3-241">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="f57c3-242">Zusätzliche Optionen zum Konfigurieren von Timeout und Keep-Alive-Verhalten sind für das `HubConnection` Objekt selbst verfügbar:</span><span class="sxs-lookup"><span data-stu-id="f57c3-242">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="f57c3-243">.NET</span><span class="sxs-lookup"><span data-stu-id="f57c3-243">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="f57c3-244">Option</span><span class="sxs-lookup"><span data-stu-id="f57c3-244">Option</span></span> | <span data-ttu-id="f57c3-245">Standardwert</span><span class="sxs-lookup"><span data-stu-id="f57c3-245">Default value</span></span> | <span data-ttu-id="f57c3-246">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="f57c3-246">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="f57c3-247">30 Sekunden (30.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="f57c3-247">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="f57c3-248">Timeout für die Serveraktivität.</span><span class="sxs-lookup"><span data-stu-id="f57c3-248">Timeout for server activity.</span></span> <span data-ttu-id="f57c3-249">Wenn der Server in diesem Intervall keine Nachricht gesendet hat, betrachtet der Client den Server als getrennt und löst das `Closed` Ereignis `onclose` aus (in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="f57c3-249">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="f57c3-250">Dieser Wert muss groß genug sein, damit eine Ping-Nachricht vom Server gesendet **und** innerhalb des Timeout Intervalls vom Client empfangen wird.</span><span class="sxs-lookup"><span data-stu-id="f57c3-250">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="f57c3-251">Der empfohlene Wert ist eine Zahl, die mindestens dem Wert des Servers `KeepAliveInterval` entspricht, um Zeit für das Eintreffen von Pings zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="f57c3-251">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="f57c3-252">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="f57c3-252">15 seconds</span></span> | <span data-ttu-id="f57c3-253">Timeout für den anfänglichen Server Hand Shake.</span><span class="sxs-lookup"><span data-stu-id="f57c3-253">Timeout for initial server handshake.</span></span> <span data-ttu-id="f57c3-254">Wenn der Server in diesem Intervall keine Hand Shake Antwort sendet, bricht der Client den Handshake ab und löst das `Closed` Ereignis `onclose` aus (in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="f57c3-254">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="f57c3-255">Dies ist eine erweiterte Einstellung, die nur geändert werden sollte, wenn aufgrund schwerer Netzwerk Latenz Fehler aufgrund von Hand Shake Timeout auftreten.</span><span class="sxs-lookup"><span data-stu-id="f57c3-255">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="f57c3-256">Weitere Details zum Hand Shake Prozess finden Sie in der [ :::no-loc(SignalR)::: Hub-Protokollspezifikation](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="f57c3-256">For more detail on the handshake process, see the [:::no-loc(SignalR)::: Hub Protocol Specification](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="f57c3-257">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="f57c3-257">15 seconds</span></span> | <span data-ttu-id="f57c3-258">Bestimmt das Intervall, in dem der Client Ping-Nachrichten sendet.</span><span class="sxs-lookup"><span data-stu-id="f57c3-258">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="f57c3-259">Beim Senden von Nachrichten vom Client wird der Timer auf den Anfang des Intervalls zurückgesetzt.</span><span class="sxs-lookup"><span data-stu-id="f57c3-259">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="f57c3-260">Wenn der Client keine Nachricht in der `ClientTimeoutInterval` Gruppe auf dem Server gesendet hat, wird der Client vom Server als getrennt betrachtet.</span><span class="sxs-lookup"><span data-stu-id="f57c3-260">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="f57c3-261">Im .NET-Client werden Timeout Werte als Werte angegeben `TimeSpan` .</span><span class="sxs-lookup"><span data-stu-id="f57c3-261">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="f57c3-262">JavaScript</span><span class="sxs-lookup"><span data-stu-id="f57c3-262">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="f57c3-263">Option</span><span class="sxs-lookup"><span data-stu-id="f57c3-263">Option</span></span> | <span data-ttu-id="f57c3-264">Standardwert</span><span class="sxs-lookup"><span data-stu-id="f57c3-264">Default value</span></span> | <span data-ttu-id="f57c3-265">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="f57c3-265">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="f57c3-266">30 Sekunden (30.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="f57c3-266">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="f57c3-267">Timeout für die Serveraktivität.</span><span class="sxs-lookup"><span data-stu-id="f57c3-267">Timeout for server activity.</span></span> <span data-ttu-id="f57c3-268">Wenn der Server in diesem Intervall keine Nachricht gesendet hat, betrachtet der Client den Server als getrennt und löst das `onclose` Ereignis aus.</span><span class="sxs-lookup"><span data-stu-id="f57c3-268">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="f57c3-269">Dieser Wert muss groß genug sein, damit eine Ping-Nachricht vom Server gesendet **und** innerhalb des Timeout Intervalls vom Client empfangen wird.</span><span class="sxs-lookup"><span data-stu-id="f57c3-269">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="f57c3-270">Der empfohlene Wert ist eine Zahl, die mindestens dem Wert des Servers `KeepAliveInterval` entspricht, um Zeit für das Eintreffen von Pings zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="f57c3-270">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="f57c3-271">15 Sekunden (15.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="f57c3-271">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="f57c3-272">Bestimmt das Intervall, in dem der Client Ping-Nachrichten sendet.</span><span class="sxs-lookup"><span data-stu-id="f57c3-272">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="f57c3-273">Beim Senden von Nachrichten vom Client wird der Timer auf den Anfang des Intervalls zurückgesetzt.</span><span class="sxs-lookup"><span data-stu-id="f57c3-273">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="f57c3-274">Wenn der Client keine Nachricht in der `ClientTimeoutInterval` Gruppe auf dem Server gesendet hat, wird der Client vom Server als getrennt betrachtet.</span><span class="sxs-lookup"><span data-stu-id="f57c3-274">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="f57c3-275">Java</span><span class="sxs-lookup"><span data-stu-id="f57c3-275">Java</span></span>](#tab/java)

| <span data-ttu-id="f57c3-276">Option</span><span class="sxs-lookup"><span data-stu-id="f57c3-276">Option</span></span> | <span data-ttu-id="f57c3-277">Standardwert</span><span class="sxs-lookup"><span data-stu-id="f57c3-277">Default value</span></span> | <span data-ttu-id="f57c3-278">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="f57c3-278">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="f57c3-279">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="f57c3-279">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="f57c3-280">30 Sekunden (30.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="f57c3-280">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="f57c3-281">Timeout für die Serveraktivität.</span><span class="sxs-lookup"><span data-stu-id="f57c3-281">Timeout for server activity.</span></span> <span data-ttu-id="f57c3-282">Wenn der Server in diesem Intervall keine Nachricht gesendet hat, betrachtet der Client den Server als getrennt und löst das `onClose` Ereignis aus.</span><span class="sxs-lookup"><span data-stu-id="f57c3-282">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="f57c3-283">Dieser Wert muss groß genug sein, damit eine Ping-Nachricht vom Server gesendet **und** innerhalb des Timeout Intervalls vom Client empfangen wird.</span><span class="sxs-lookup"><span data-stu-id="f57c3-283">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="f57c3-284">Der empfohlene Wert ist eine Zahl, die mindestens dem Wert des Servers `KeepAliveInterval` entspricht, um Zeit für das Eintreffen von Pings zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="f57c3-284">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="f57c3-285">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="f57c3-285">15 seconds</span></span> | <span data-ttu-id="f57c3-286">Timeout für den anfänglichen Server Hand Shake.</span><span class="sxs-lookup"><span data-stu-id="f57c3-286">Timeout for initial server handshake.</span></span> <span data-ttu-id="f57c3-287">Wenn der Server in diesem Intervall keine Hand Shake Antwort sendet, bricht der Client den Handshake ab und löst das- `onClose` Ereignis aus.</span><span class="sxs-lookup"><span data-stu-id="f57c3-287">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="f57c3-288">Dies ist eine erweiterte Einstellung, die nur geändert werden sollte, wenn aufgrund schwerer Netzwerk Latenz Fehler aufgrund von Hand Shake Timeout auftreten.</span><span class="sxs-lookup"><span data-stu-id="f57c3-288">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="f57c3-289">Weitere Details zum Hand Shake Prozess finden Sie in der [ :::no-loc(SignalR)::: Hub-Protokollspezifikation](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="f57c3-289">For more detail on the Handshake process, see the [:::no-loc(SignalR)::: Hub Protocol Specification](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="f57c3-290">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="f57c3-290">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="f57c3-291">15 Sekunden (15.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="f57c3-291">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="f57c3-292">Bestimmt das Intervall, in dem der Client Ping-Nachrichten sendet.</span><span class="sxs-lookup"><span data-stu-id="f57c3-292">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="f57c3-293">Beim Senden von Nachrichten vom Client wird der Timer auf den Anfang des Intervalls zurückgesetzt.</span><span class="sxs-lookup"><span data-stu-id="f57c3-293">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="f57c3-294">Wenn der Client keine Nachricht in der `ClientTimeoutInterval` Gruppe auf dem Server gesendet hat, wird der Client vom Server als getrennt betrachtet.</span><span class="sxs-lookup"><span data-stu-id="f57c3-294">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="f57c3-295">Konfigurieren zusätzlicher Optionen</span><span class="sxs-lookup"><span data-stu-id="f57c3-295">Configure additional options</span></span>

<span data-ttu-id="f57c3-296">Zusätzliche Optionen können in der- `WithUrl` Methode ( `withUrl` in JavaScript) für `HubConnectionBuilder` oder für die verschiedenen Konfigurations-APIs auf dem `HttpHubConnectionBuilder` im Java-Client konfiguriert werden:</span><span class="sxs-lookup"><span data-stu-id="f57c3-296">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="f57c3-297">.NET</span><span class="sxs-lookup"><span data-stu-id="f57c3-297">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="f57c3-298">.Net-Option</span><span class="sxs-lookup"><span data-stu-id="f57c3-298">.NET Option</span></span> |  <span data-ttu-id="f57c3-299">Standardwert</span><span class="sxs-lookup"><span data-stu-id="f57c3-299">Default value</span></span> | <span data-ttu-id="f57c3-300">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="f57c3-300">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="f57c3-301">Eine Funktion, die eine Zeichenfolge zurückgibt, die als bearerauthentifizierungstoken in HTTP-Anforderungen bereitgestellt</span><span class="sxs-lookup"><span data-stu-id="f57c3-301">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="f57c3-302">Legen Sie dies auf fest, `true` um den Aushandlungs Schritt zu überspringen</span><span class="sxs-lookup"><span data-stu-id="f57c3-302">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="f57c3-303">Wird **nur unterstützt, wenn der websockets-Transport der einzige aktivierte Transport ist** .</span><span class="sxs-lookup"><span data-stu-id="f57c3-303">**Only supported when the WebSockets transport is the only enabled transport** .</span></span> <span data-ttu-id="f57c3-304">Diese Einstellung kann nicht aktiviert werden, wenn der Azure-Dienst verwendet wird :::no-loc(SignalR)::: .</span><span class="sxs-lookup"><span data-stu-id="f57c3-304">This setting can't be enabled when using the Azure :::no-loc(SignalR)::: Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="f57c3-305">Leer</span><span class="sxs-lookup"><span data-stu-id="f57c3-305">Empty</span></span> | <span data-ttu-id="f57c3-306">Eine Auflistung von TLS-Zertifikaten, die zum Authentifizieren von Anforderungen gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="f57c3-306">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `:::no-loc(Cookie):::s` | <span data-ttu-id="f57c3-307">Leer</span><span class="sxs-lookup"><span data-stu-id="f57c3-307">Empty</span></span> | <span data-ttu-id="f57c3-308">Eine Auflistung von http- :::no-loc(cookie)::: s, die mit jeder HTTP-Anforderung gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="f57c3-308">A collection of HTTP :::no-loc(cookie):::s to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="f57c3-309">Leer</span><span class="sxs-lookup"><span data-stu-id="f57c3-309">Empty</span></span> | <span data-ttu-id="f57c3-310">Anmelde Informationen, die mit jeder HTTP-Anforderung gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="f57c3-310">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="f57c3-311">5 Sekunden</span><span class="sxs-lookup"><span data-stu-id="f57c3-311">5 seconds</span></span> | <span data-ttu-id="f57c3-312">Nur websockets.</span><span class="sxs-lookup"><span data-stu-id="f57c3-312">WebSockets only.</span></span> <span data-ttu-id="f57c3-313">Die maximale Zeitspanne, die der Client nach dem Schließen des Servers wartet, um die Anforderung zum Schließen zu bestätigen.</span><span class="sxs-lookup"><span data-stu-id="f57c3-313">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="f57c3-314">Wenn der Server die Schließung innerhalb dieses Zeitraums nicht anerkennt, trennt der Client die Verbindung.</span><span class="sxs-lookup"><span data-stu-id="f57c3-314">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="f57c3-315">Leer</span><span class="sxs-lookup"><span data-stu-id="f57c3-315">Empty</span></span> | <span data-ttu-id="f57c3-316">Eine Karte mit zusätzlichen HTTP-Headern, die mit jeder HTTP-Anforderung gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="f57c3-316">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="f57c3-317">Ein Delegat, der verwendet werden kann, um den `HttpMessageHandler` zum Senden von HTTP-Anforderungen verwendeten zu konfigurieren oder zu ersetzen.</span><span class="sxs-lookup"><span data-stu-id="f57c3-317">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="f57c3-318">Wird nicht für WebSocket-Verbindungen verwendet.</span><span class="sxs-lookup"><span data-stu-id="f57c3-318">Not used for WebSocket connections.</span></span> <span data-ttu-id="f57c3-319">Dieser Delegat muss einen Wert zurückgeben, der nicht NULL ist, und erhält den Standardwert als Parameter.</span><span class="sxs-lookup"><span data-stu-id="f57c3-319">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="f57c3-320">Ändern Sie die Einstellungen für diesen Standardwert, und geben Sie ihn zurück, oder geben Sie eine neue `HttpMessageHandler` Instanz zurück.</span><span class="sxs-lookup"><span data-stu-id="f57c3-320">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="f57c3-321">**Wenn Sie den Handler ersetzen, stellen Sie sicher, dass die Einstellungen, die Sie beibehalten möchten, vom bereitgestellten Handler kopiert werden. Andernfalls gelten die konfigurierten Optionen (z. b. :::no-loc(Cookie)::: s und Header) nicht für den neuen Handler.**</span><span class="sxs-lookup"><span data-stu-id="f57c3-321">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as :::no-loc(Cookie):::s and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="f57c3-322">Ein HTTP-Proxy, der beim Senden von HTTP-Anforderungen verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="f57c3-322">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="f57c3-323">Legen Sie diesen booleschen Wert fest, um die Standard Anmelde Informationen für http-und websockets-Anforderungen zu senden</span><span class="sxs-lookup"><span data-stu-id="f57c3-323">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="f57c3-324">Dies ermöglicht die Verwendung der Windows-Authentifizierung.</span><span class="sxs-lookup"><span data-stu-id="f57c3-324">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="f57c3-325">Ein Delegat, der zum Konfigurieren zusätzlicher WebSocket-Optionen verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="f57c3-325">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="f57c3-326">Empfängt eine Instanz von [clientwebsocketoptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) , die zum Konfigurieren der Optionen verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="f57c3-326">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="f57c3-327">JavaScript</span><span class="sxs-lookup"><span data-stu-id="f57c3-327">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="f57c3-328">JavaScript-Option</span><span class="sxs-lookup"><span data-stu-id="f57c3-328">JavaScript Option</span></span> | <span data-ttu-id="f57c3-329">Standardwert</span><span class="sxs-lookup"><span data-stu-id="f57c3-329">Default Value</span></span> | <span data-ttu-id="f57c3-330">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="f57c3-330">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="f57c3-331">Eine Funktion, die eine Zeichenfolge zurückgibt, die als bearerauthentifizierungstoken in HTTP-Anforderungen bereitgestellt</span><span class="sxs-lookup"><span data-stu-id="f57c3-331">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `transport` | `null` | <span data-ttu-id="f57c3-332">Ein- <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType> Wert, der den für die Verbindung zu verwendenden Transport angibt.</span><span class="sxs-lookup"><span data-stu-id="f57c3-332">An <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType> value specifying the transport to use for the connection.</span></span> |
| `headers` | `null` | <span data-ttu-id="f57c3-333">Das Wörterbuch der mit jeder HTTP-Anforderung gesendeten Header.</span><span class="sxs-lookup"><span data-stu-id="f57c3-333">Dictionary of headers sent with every HTTP request.</span></span> <span data-ttu-id="f57c3-334">Das Senden von Headern im Browser funktioniert nicht für websockets oder den <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType.ServerSentEvents> Stream.</span><span class="sxs-lookup"><span data-stu-id="f57c3-334">Sending headers in the browser doesn't work for WebSockets or the <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType.ServerSentEvents> stream.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="f57c3-335">Legen Sie auf fest, `true` um die Bytes/Zeichen der vom Client gesendeten und empfangenen Nachrichten zu protokollieren.</span><span class="sxs-lookup"><span data-stu-id="f57c3-335">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="f57c3-336">Legen Sie dies auf fest, `true` um den Aushandlungs Schritt zu überspringen</span><span class="sxs-lookup"><span data-stu-id="f57c3-336">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="f57c3-337">Wird **nur unterstützt, wenn der websockets-Transport der einzige aktivierte Transport ist** .</span><span class="sxs-lookup"><span data-stu-id="f57c3-337">**Only supported when the WebSockets transport is the only enabled transport** .</span></span> <span data-ttu-id="f57c3-338">Diese Einstellung kann nicht aktiviert werden, wenn der Azure-Dienst verwendet wird :::no-loc(SignalR)::: .</span><span class="sxs-lookup"><span data-stu-id="f57c3-338">This setting can't be enabled when using the Azure :::no-loc(SignalR)::: Service.</span></span> |
| `withCredentials` | `true` | <span data-ttu-id="f57c3-339">Gibt an, ob Anmelde Informationen mit der cors-Anforderung gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="f57c3-339">Specifies whether credentials will be sent with the CORS request.</span></span> <span data-ttu-id="f57c3-340">Azure App Service verwendet :::no-loc(cookie)::: s für persistente Sitzungen und erfordert, dass diese Option ordnungsgemäß funktioniert.</span><span class="sxs-lookup"><span data-stu-id="f57c3-340">Azure App Service uses :::no-loc(cookie):::s for sticky sessions and needs this option enabled to work correctly.</span></span> <span data-ttu-id="f57c3-341">Weitere Informationen zu cors mit :::no-loc(SignalR)::: finden Sie unter <xref:signalr/security#cross-origin-resource-sharing> .</span><span class="sxs-lookup"><span data-stu-id="f57c3-341">For more info on CORS with :::no-loc(SignalR):::, see <xref:signalr/security#cross-origin-resource-sharing>.</span></span> |

# <a name="java"></a>[<span data-ttu-id="f57c3-342">Java</span><span class="sxs-lookup"><span data-stu-id="f57c3-342">Java</span></span>](#tab/java)

| <span data-ttu-id="f57c3-343">Java-Option</span><span class="sxs-lookup"><span data-stu-id="f57c3-343">Java Option</span></span> | <span data-ttu-id="f57c3-344">Standardwert</span><span class="sxs-lookup"><span data-stu-id="f57c3-344">Default Value</span></span> | <span data-ttu-id="f57c3-345">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="f57c3-345">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="f57c3-346">Eine Funktion, die eine Zeichenfolge zurückgibt, die als bearerauthentifizierungstoken in HTTP-Anforderungen bereitgestellt</span><span class="sxs-lookup"><span data-stu-id="f57c3-346">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="f57c3-347">Legen Sie dies auf fest, `true` um den Aushandlungs Schritt zu überspringen</span><span class="sxs-lookup"><span data-stu-id="f57c3-347">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="f57c3-348">Wird **nur unterstützt, wenn der websockets-Transport der einzige aktivierte Transport ist** .</span><span class="sxs-lookup"><span data-stu-id="f57c3-348">**Only supported when the WebSockets transport is the only enabled transport** .</span></span> <span data-ttu-id="f57c3-349">Diese Einstellung kann nicht aktiviert werden, wenn der Azure-Dienst verwendet wird :::no-loc(SignalR)::: .</span><span class="sxs-lookup"><span data-stu-id="f57c3-349">This setting can't be enabled when using the Azure :::no-loc(SignalR)::: Service.</span></span> |
| <span data-ttu-id="f57c3-350">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="f57c3-350">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="f57c3-351">Leer</span><span class="sxs-lookup"><span data-stu-id="f57c3-351">Empty</span></span> | <span data-ttu-id="f57c3-352">Eine Karte mit zusätzlichen HTTP-Headern, die mit jeder HTTP-Anforderung gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="f57c3-352">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="f57c3-353">Im .NET-Client können diese Optionen durch den für bereitgestellten Options Delegaten geändert werden `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="f57c3-353">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.SkipNegotiation = true;
        options.Transports = HttpTransportType.WebSockets;
        options.:::no-loc(Cookie):::s.Add(new :::no-loc(Cookie):::(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="f57c3-354">Im JavaScript-Client können diese Optionen in einem JavaScript-Objekt bereitgestellt werden, das für Folgendes bereitgestellt wird `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="f57c3-354">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        // "Foo: Bar" will not be sent with WebSockets or Server-Sent Events requests
        headers: { "Foo": "Bar" },
        transport: signalR.HttpTransportType.LongPolling 
    })
    .build();
```

<span data-ttu-id="f57c3-355">Im Java-Client können diese Optionen mit den Methoden auf der konfiguriert werden, die `HttpHubConnectionBuilder` von der `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="f57c3-355">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="f57c3-356">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="f57c3-356">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.1"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="f57c3-357">JSON/messagepack-Serialisierungsoptionen</span><span class="sxs-lookup"><span data-stu-id="f57c3-357">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="f57c3-358">ASP.net Core :::no-loc(SignalR)::: unterstützt zwei Protokolle zum Codieren von Nachrichten: [JSON](https://www.json.org/) und [messagepack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="f57c3-358">ASP.NET Core :::no-loc(SignalR)::: supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="f57c3-359">Jedes Protokoll verfügt über Konfigurationsoptionen für die Serialisierung.</span><span class="sxs-lookup"><span data-stu-id="f57c3-359">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="f57c3-360">Die JSON-Serialisierung kann mithilfe der [addjsonprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) -Erweiterungsmethode auf dem Server konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="f57c3-360">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="f57c3-361">`AddJsonProtocol`kann nach [Add :::no-loc(SignalR)::: ](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in hinzugefügt werden `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="f57c3-361">`AddJsonProtocol` can be added after [Add:::no-loc(SignalR):::](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="f57c3-362">Die- `AddJsonProtocol` Methode nimmt einen Delegaten an, der ein- `options` Objekt empfängt.</span><span class="sxs-lookup"><span data-stu-id="f57c3-362">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="f57c3-363">Die [payloadserializeroptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) -Eigenschaft für dieses Objekt ist ein- `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> Objekt, das zum Konfigurieren der Serialisierung von Argumenten und Rückgabe Werten verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="f57c3-363">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="f57c3-364">Weitere Informationen finden Sie in den [System.Text.Jszur Dokumentation](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="f57c3-364">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="f57c3-365">Verwenden Sie beispielsweise den folgenden Code in, um das Serialisierungsprogramm so zu konfigurieren, dass die Groß-/Kleinschreibung von Eigenschaftsnamen nicht geändert wird. verwenden Sie dazu den folgenden Code in `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="f57c3-365">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.Add:::no-loc(SignalR):::()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="f57c3-366">Im .NET-Client ist dieselbe `AddJsonProtocol` Erweiterungsmethode auf [hubconnectionbuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)vorhanden.</span><span class="sxs-lookup"><span data-stu-id="f57c3-366">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="f57c3-367">Der `Microsoft.Extensions.DependencyInjection` Namespace muss importiert werden, um die Erweiterungsmethode aufzulösen:</span><span class="sxs-lookup"><span data-stu-id="f57c3-367">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="f57c3-368">Zurzeit ist es nicht möglich, JSON-Serialisierung im JavaScript-Client zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="f57c3-368">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="f57c3-369">Zum Newtonsoft.Jswechseln</span><span class="sxs-lookup"><span data-stu-id="f57c3-369">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="f57c3-370">Wenn Sie Features von benötigen `Newtonsoft.Json` , die in nicht unterstützt `System.Text.Json` werden, finden Sie weitere Informationen unter [Wechseln zu Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="f57c3-370">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="f57c3-371">Messagepack-Serialisierungsoptionen</span><span class="sxs-lookup"><span data-stu-id="f57c3-371">MessagePack serialization options</span></span>

<span data-ttu-id="f57c3-372">Die messagepack-Serialisierung kann konfiguriert werden, indem ein Delegat für den [addmessagepackprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) -Aufruf bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="f57c3-372">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="f57c3-373">Weitere Informationen finden Sie [unter :::no-loc(SignalR)::: messagepack in](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="f57c3-373">See [MessagePack in :::no-loc(SignalR):::](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="f57c3-374">Zurzeit ist es nicht möglich, die messagepack-Serialisierung im JavaScript-Client zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="f57c3-374">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="f57c3-375">Konfigurieren von Serveroptionen</span><span class="sxs-lookup"><span data-stu-id="f57c3-375">Configure server options</span></span>

<span data-ttu-id="f57c3-376">In der folgenden Tabelle werden die Optionen zum Konfigurieren von :::no-loc(SignalR)::: Hubs beschrieben:</span><span class="sxs-lookup"><span data-stu-id="f57c3-376">The following table describes options for configuring :::no-loc(SignalR)::: hubs:</span></span>

| <span data-ttu-id="f57c3-377">Option</span><span class="sxs-lookup"><span data-stu-id="f57c3-377">Option</span></span> | <span data-ttu-id="f57c3-378">Standardwert</span><span class="sxs-lookup"><span data-stu-id="f57c3-378">Default Value</span></span> | <span data-ttu-id="f57c3-379">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="f57c3-379">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="f57c3-380">30 Sekunden</span><span class="sxs-lookup"><span data-stu-id="f57c3-380">30 seconds</span></span> | <span data-ttu-id="f57c3-381">Der Server wird vom-Client als getrennt betrachtet, wenn in diesem Intervall keine Nachricht (einschließlich Keep-Alive) empfangen wurde.</span><span class="sxs-lookup"><span data-stu-id="f57c3-381">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="f57c3-382">Es kann länger dauern, bis der Client als getrennt gekennzeichnet ist, weil dies implementiert ist.</span><span class="sxs-lookup"><span data-stu-id="f57c3-382">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="f57c3-383">Der empfohlene Wert ist Double- `KeepAliveInterval` Wert.</span><span class="sxs-lookup"><span data-stu-id="f57c3-383">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="f57c3-384">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="f57c3-384">15 seconds</span></span> | <span data-ttu-id="f57c3-385">Wenn der Client innerhalb dieses Zeitraums keine anfängliche Hand Shake Nachricht sendet, wird die Verbindung geschlossen.</span><span class="sxs-lookup"><span data-stu-id="f57c3-385">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="f57c3-386">Dies ist eine erweiterte Einstellung, die nur geändert werden sollte, wenn aufgrund schwerer Netzwerk Latenz Fehler aufgrund von Hand Shake Timeout auftreten.</span><span class="sxs-lookup"><span data-stu-id="f57c3-386">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="f57c3-387">Weitere Details zum Hand Shake Prozess finden Sie in der [ :::no-loc(SignalR)::: Hub-Protokollspezifikation](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="f57c3-387">For more detail on the handshake process, see the [:::no-loc(SignalR)::: Hub Protocol Specification](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="f57c3-388">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="f57c3-388">15 seconds</span></span> | <span data-ttu-id="f57c3-389">Wenn der Server innerhalb dieses Intervalls keine Nachricht gesendet hat, wird automatisch eine Ping-Nachricht gesendet, um die Verbindung offen zu halten.</span><span class="sxs-lookup"><span data-stu-id="f57c3-389">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="f57c3-390">Ändern Sie `KeepAliveInterval` die `ServerTimeout` / `serverTimeoutInMilliseconds` Einstellung auf dem Client, wenn Sie sich ändern.</span><span class="sxs-lookup"><span data-stu-id="f57c3-390">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="f57c3-391">Der empfohlene `ServerTimeout` / `serverTimeoutInMilliseconds` Wert ist Double- `KeepAliveInterval` Wert.</span><span class="sxs-lookup"><span data-stu-id="f57c3-391">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="f57c3-392">Alle installierten Protokolle</span><span class="sxs-lookup"><span data-stu-id="f57c3-392">All installed protocols</span></span> | <span data-ttu-id="f57c3-393">Protokolle, die von diesem Hub unterstützt werden.</span><span class="sxs-lookup"><span data-stu-id="f57c3-393">Protocols supported by this hub.</span></span> <span data-ttu-id="f57c3-394">Standardmäßig sind alle auf dem Server registrierten Protokolle zulässig, aber Protokolle können aus dieser Liste entfernt werden, um bestimmte Protokolle für einzelne Hubs zu deaktivieren.</span><span class="sxs-lookup"><span data-stu-id="f57c3-394">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="f57c3-395">Wenn der Wert `true` ist, werden ausführliche Ausnahme Meldungen an Clients zurückgegeben, wenn eine Ausnahme in einer Hub-Methode ausgelöst wird.</span><span class="sxs-lookup"><span data-stu-id="f57c3-395">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="f57c3-396">Der Standardwert ist `false` , da diese Ausnahme Meldungen vertrauliche Informationen enthalten können.</span><span class="sxs-lookup"><span data-stu-id="f57c3-396">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="f57c3-397">Die maximale Anzahl von Elementen, die für Client-uploadstreams gepuffert werden können.</span><span class="sxs-lookup"><span data-stu-id="f57c3-397">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="f57c3-398">Wenn dieser Grenzwert erreicht wird, wird die Verarbeitung von Aufrufen blockiert, bis der Server streamingelemente verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="f57c3-398">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="f57c3-399">32 KB</span><span class="sxs-lookup"><span data-stu-id="f57c3-399">32 KB</span></span> | <span data-ttu-id="f57c3-400">Maximale Größe einer einzelnen eingehenden Hub-Nachricht.</span><span class="sxs-lookup"><span data-stu-id="f57c3-400">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="f57c3-401">Optionen können für alle Hubs konfiguriert werden, indem ein Options Delegat für den Aufruf in bereitgestellt wird `Add:::no-loc(SignalR):::` `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="f57c3-401">Options can be configured for all hubs by providing an options delegate to the `Add:::no-loc(SignalR):::` call in `Startup.ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Add:::no-loc(SignalR):::(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

<span data-ttu-id="f57c3-402">Optionen für einen einzelnen Hub überschreiben die in bereitgestellten globalen Optionen `Add:::no-loc(SignalR):::` und können mithilfe von konfiguriert werden <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(SignalR):::DependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="f57c3-402">Options for a single hub override the global options provided in `Add:::no-loc(SignalR):::` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(SignalR):::DependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.Add:::no-loc(SignalR):::().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="f57c3-403">Erweiterte http-Konfigurationsoptionen</span><span class="sxs-lookup"><span data-stu-id="f57c3-403">Advanced HTTP configuration options</span></span>

<span data-ttu-id="f57c3-404">Verwenden `HttpConnectionDispatcherOptions` Sie, um erweiterte Einstellungen im Zusammenhang mit Transporte und der Speicherpuffer Verwaltung zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="f57c3-404">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="f57c3-405">Diese Optionen werden konfiguriert, indem ein [Delegat an \<T> maphub](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in übergeben wird `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="f57c3-405">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="f57c3-406">In der folgenden Tabelle werden die Optionen zum Konfigurieren der :::no-loc(SignalR)::: erweiterten http-Optionen von ASP.net Core beschrieben:</span><span class="sxs-lookup"><span data-stu-id="f57c3-406">The following table describes options for configuring ASP.NET Core :::no-loc(SignalR):::'s advanced HTTP options:</span></span>

| <span data-ttu-id="f57c3-407">Option</span><span class="sxs-lookup"><span data-stu-id="f57c3-407">Option</span></span> | <span data-ttu-id="f57c3-408">Standardwert</span><span class="sxs-lookup"><span data-stu-id="f57c3-408">Default Value</span></span> | <span data-ttu-id="f57c3-409">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="f57c3-409">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="f57c3-410">32 KB</span><span class="sxs-lookup"><span data-stu-id="f57c3-410">32 KB</span></span> | <span data-ttu-id="f57c3-411">Die maximale Anzahl von Bytes, die vom Client vor dem Anwenden des backdrucks vom Server gepuffert werden.</span><span class="sxs-lookup"><span data-stu-id="f57c3-411">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="f57c3-412">Wenn Sie diesen Wert erhöhen, kann der Server größere Nachrichten schneller empfangen, ohne dass ein Rückdruck angewendet wird, der Arbeitsspeicher Verbrauch kann jedoch gesteigert werden.</span><span class="sxs-lookup"><span data-stu-id="f57c3-412">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="f57c3-413">Daten, die automatisch von den `Authorize` auf die Hub-Klasse angewendeten Attributen gesammelt werden.</span><span class="sxs-lookup"><span data-stu-id="f57c3-413">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="f57c3-414">Eine Liste von [iautorizedata](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) -Objekten, die verwendet werden, um zu bestimmen, ob ein Client für die Verbindung mit dem Hub autorisiert ist.</span><span class="sxs-lookup"><span data-stu-id="f57c3-414">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="f57c3-415">32 KB</span><span class="sxs-lookup"><span data-stu-id="f57c3-415">32 KB</span></span> | <span data-ttu-id="f57c3-416">Die maximale Anzahl von Bytes, die von der APP gesendet werden, die der Server puffert, bevor der Rückdruck beobachtet wird</span><span class="sxs-lookup"><span data-stu-id="f57c3-416">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="f57c3-417">Wenn Sie diesen Wert erhöhen, kann der Server größere Nachrichten schneller Puffern, ohne dass auf den Rückstand gewartet wird, aber die Arbeitsspeicher Nutzung erhöhen kann.</span><span class="sxs-lookup"><span data-stu-id="f57c3-417">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="f57c3-418">Alle Transporte sind aktiviert.</span><span class="sxs-lookup"><span data-stu-id="f57c3-418">All Transports are enabled.</span></span> | <span data-ttu-id="f57c3-419">Ein Bitflags-Enumeration von `HttpTransportType` Werten, die die Transporte einschränken können, die ein Client für die Verbindungs Herstellung verwenden kann.</span><span class="sxs-lookup"><span data-stu-id="f57c3-419">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="f57c3-420">Siehe unten.</span><span class="sxs-lookup"><span data-stu-id="f57c3-420">See below.</span></span> | <span data-ttu-id="f57c3-421">Zusätzliche Optionen, die für den langen Abruf Transport spezifisch sind.</span><span class="sxs-lookup"><span data-stu-id="f57c3-421">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="f57c3-422">Siehe unten.</span><span class="sxs-lookup"><span data-stu-id="f57c3-422">See below.</span></span> | <span data-ttu-id="f57c3-423">Zusätzliche Optionen für den websockets-Transport.</span><span class="sxs-lookup"><span data-stu-id="f57c3-423">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="f57c3-424">0</span><span class="sxs-lookup"><span data-stu-id="f57c3-424">0</span></span> | <span data-ttu-id="f57c3-425">Geben Sie die Mindestversion des Aushandlungs Protokolls an.</span><span class="sxs-lookup"><span data-stu-id="f57c3-425">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="f57c3-426">Hiermit werden Clients auf neuere Versionen beschränkt.</span><span class="sxs-lookup"><span data-stu-id="f57c3-426">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="f57c3-427">Der lange Abruf Transport bietet zusätzliche Optionen, die mit der-Eigenschaft konfiguriert werden können `LongPolling` :</span><span class="sxs-lookup"><span data-stu-id="f57c3-427">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="f57c3-428">Option</span><span class="sxs-lookup"><span data-stu-id="f57c3-428">Option</span></span> | <span data-ttu-id="f57c3-429">Standardwert</span><span class="sxs-lookup"><span data-stu-id="f57c3-429">Default Value</span></span> | <span data-ttu-id="f57c3-430">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="f57c3-430">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="f57c3-431">90 Sekunden</span><span class="sxs-lookup"><span data-stu-id="f57c3-431">90 seconds</span></span> | <span data-ttu-id="f57c3-432">Die maximale Zeitspanne, die der Server wartet, bis eine Nachricht an den Client gesendet wird, bevor eine einzelne Abruf Anforderung beendet wird.</span><span class="sxs-lookup"><span data-stu-id="f57c3-432">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="f57c3-433">Das verringern dieses Werts bewirkt, dass der Client neue Abruf Anforderungen häufiger ausgibt.</span><span class="sxs-lookup"><span data-stu-id="f57c3-433">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="f57c3-434">Der WebSocket-Transport verfügt über zusätzliche Optionen, die mit der-Eigenschaft konfiguriert werden können `WebSockets` :</span><span class="sxs-lookup"><span data-stu-id="f57c3-434">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="f57c3-435">Option</span><span class="sxs-lookup"><span data-stu-id="f57c3-435">Option</span></span> | <span data-ttu-id="f57c3-436">Standardwert</span><span class="sxs-lookup"><span data-stu-id="f57c3-436">Default Value</span></span> | <span data-ttu-id="f57c3-437">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="f57c3-437">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="f57c3-438">5 Sekunden</span><span class="sxs-lookup"><span data-stu-id="f57c3-438">5 seconds</span></span> | <span data-ttu-id="f57c3-439">Wenn der Client nach dem Schließen des Servers nicht innerhalb dieses Zeitraums geschlossen werden kann, wird die Verbindung beendet.</span><span class="sxs-lookup"><span data-stu-id="f57c3-439">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="f57c3-440">Ein Delegat, der verwendet werden kann, um den- `Sec-WebSocket-Protocol` Header auf einen benutzerdefinierten Wert festzulegen.</span><span class="sxs-lookup"><span data-stu-id="f57c3-440">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="f57c3-441">Der Delegat empfängt die vom Client angeforderten Werte als Eingabe und erwartet, dass der gewünschte Wert zurückgegeben wird.</span><span class="sxs-lookup"><span data-stu-id="f57c3-441">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="f57c3-442">Konfigurieren von Clientoptionen</span><span class="sxs-lookup"><span data-stu-id="f57c3-442">Configure client options</span></span>

<span data-ttu-id="f57c3-443">Client Optionen können für den Typ konfiguriert werden `HubConnectionBuilder` (verfügbar in den .net-und JavaScript-Clients).</span><span class="sxs-lookup"><span data-stu-id="f57c3-443">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="f57c3-444">Sie steht auch im Java-Client zur Verfügung, aber die `HttpHubConnectionBuilder` Unterklasse enthält die Generator-Konfigurationsoptionen sowie die `HubConnection` selbst.</span><span class="sxs-lookup"><span data-stu-id="f57c3-444">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="f57c3-445">Konfigurieren der Protokollierung</span><span class="sxs-lookup"><span data-stu-id="f57c3-445">Configure logging</span></span>

<span data-ttu-id="f57c3-446">Die Protokollierung wird mithilfe der-Methode im .NET-Client konfiguriert `ConfigureLogging` .</span><span class="sxs-lookup"><span data-stu-id="f57c3-446">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="f57c3-447">Protokollierungs Anbieter und Filter können auf die gleiche Weise wie auf dem Server registriert werden.</span><span class="sxs-lookup"><span data-stu-id="f57c3-447">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="f57c3-448">Weitere Informationen finden Sie in der Dokumentation zur [Protokollierung ASP.net Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="f57c3-448">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="f57c3-449">Um Protokollierungs Anbieter zu registrieren, müssen Sie die erforderlichen Pakete installieren.</span><span class="sxs-lookup"><span data-stu-id="f57c3-449">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="f57c3-450">Eine vollständige Liste finden Sie im Abschnitt [integrierte Protokollierungs Anbieter](xref:fundamentals/logging/index#built-in-logging-providers) der Dokumentation.</span><span class="sxs-lookup"><span data-stu-id="f57c3-450">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="f57c3-451">Um z. b. die Konsolen Protokollierung zu aktivieren, installieren Sie das `Microsoft.Extensions.Logging.Console` nuget-Paket.</span><span class="sxs-lookup"><span data-stu-id="f57c3-451">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="f57c3-452">Die `AddConsole` Erweiterungsmethode aufzurufen:</span><span class="sxs-lookup"><span data-stu-id="f57c3-452">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="f57c3-453">Im JavaScript-Client ist eine ähnliche `configureLogging` Methode vorhanden.</span><span class="sxs-lookup"><span data-stu-id="f57c3-453">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="f57c3-454">Geben `LogLevel` Sie einen Wert an, der die minimale Ebene der zu erstellenden Protokollmeldungen angibt.</span><span class="sxs-lookup"><span data-stu-id="f57c3-454">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="f57c3-455">Protokolle werden in das Browser Konsolenfenster geschrieben.</span><span class="sxs-lookup"><span data-stu-id="f57c3-455">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="f57c3-456">Anstelle eines `LogLevel` Werts können Sie auch einen Wert angeben, der den `string` Namen einer Protokollebene darstellt.</span><span class="sxs-lookup"><span data-stu-id="f57c3-456">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="f57c3-457">Dies ist nützlich, wenn :::no-loc(SignalR)::: Sie die Protokollierung in Umgebungen konfigurieren, in denen Sie keinen Zugriff auf die `LogLevel` Konstanten haben.</span><span class="sxs-lookup"><span data-stu-id="f57c3-457">This is useful when configuring :::no-loc(SignalR)::: logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="f57c3-458">In der folgenden Tabelle sind die verfügbaren Protokoll Ebenen aufgeführt.</span><span class="sxs-lookup"><span data-stu-id="f57c3-458">The following table lists the available log levels.</span></span> <span data-ttu-id="f57c3-459">Der von Ihnen bereitgestellte Wert, `configureLogging` der die **minimale** Protokollierung festlegt, die protokolliert werden soll.</span><span class="sxs-lookup"><span data-stu-id="f57c3-459">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="f57c3-460">Nachrichten, die auf dieser Ebene protokolliert werden, **oder die nach der Tabelle aufgeführten Ebenen** werden protokolliert.</span><span class="sxs-lookup"><span data-stu-id="f57c3-460">Messages logged at this level, **or the levels listed after it in the table** , will be logged.</span></span>

| <span data-ttu-id="f57c3-461">String</span><span class="sxs-lookup"><span data-stu-id="f57c3-461">String</span></span>                      | <span data-ttu-id="f57c3-462">LogLevel</span><span class="sxs-lookup"><span data-stu-id="f57c3-462">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="f57c3-463">`info` **oder** `information`</span><span class="sxs-lookup"><span data-stu-id="f57c3-463">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="f57c3-464">`warn` **oder** `warning`</span><span class="sxs-lookup"><span data-stu-id="f57c3-464">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="f57c3-465">Um die Protokollierung vollständig zu deaktivieren, geben Sie `signalR.LogLevel.None` in der `configureLogging` Methode an</span><span class="sxs-lookup"><span data-stu-id="f57c3-465">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="f57c3-466">Weitere Informationen zur Protokollierung finden Sie in der [ :::no-loc(SignalR)::: Diagnose Dokumentation](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="f57c3-466">For more information on logging, see the [:::no-loc(SignalR)::: Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="f57c3-467">Der :::no-loc(SignalR)::: Java-Client verwendet die [SLF4J](https://www.slf4j.org/) -Bibliothek für die Protokollierung.</span><span class="sxs-lookup"><span data-stu-id="f57c3-467">The :::no-loc(SignalR)::: Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="f57c3-468">Dabei handelt es sich um eine allgemeine Protokollierungs-API, die es Benutzern der Bibliothek ermöglicht, ihre eigene spezifische Protokollierungs Implementierung zu wählen, indem Sie eine bestimmte Protokollierungs Abhängigkeit einbinden.</span><span class="sxs-lookup"><span data-stu-id="f57c3-468">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="f57c3-469">Der folgende Code Ausschnitt zeigt, wie `java.util.logging` mit dem Java- :::no-loc(SignalR)::: Client verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="f57c3-469">The following code snippet shows how to use `java.util.logging` with the :::no-loc(SignalR)::: Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="f57c3-470">Wenn Sie die Protokollierung in ihren Abhängigkeiten nicht konfigurieren, lädt SLF4J eine Standard Protokollierung ohne Vorgang mit folgender Warnmeldung:</span><span class="sxs-lookup"><span data-stu-id="f57c3-470">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="f57c3-471">Dies kann sicher ignoriert werden.</span><span class="sxs-lookup"><span data-stu-id="f57c3-471">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="f57c3-472">Zulässige Transporte konfigurieren</span><span class="sxs-lookup"><span data-stu-id="f57c3-472">Configure allowed transports</span></span>

<span data-ttu-id="f57c3-473">Die von verwendeten Transporte :::no-loc(SignalR)::: können im-Befehl `WithUrl` ( `withUrl` in JavaScript) konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="f57c3-473">The transports used by :::no-loc(SignalR)::: can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="f57c3-474">Ein bitweises OR der Werte von `HttpTransportType` kann verwendet werden, um den Client so einzuschränken, dass nur die angegebenen Transporte verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="f57c3-474">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="f57c3-475">Alle Transporte sind standardmäßig aktiviert.</span><span class="sxs-lookup"><span data-stu-id="f57c3-475">All transports are enabled by default.</span></span>

<span data-ttu-id="f57c3-476">Um z. b. den Server-Sent Ereignis Transport zu deaktivieren, aber websockets und lange Abruf Verbindungen zuzulassen:</span><span class="sxs-lookup"><span data-stu-id="f57c3-476">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="f57c3-477">Im JavaScript-Client werden Transporte durch Festlegen des- `transport` Felds für das Options-Objekt konfiguriert, das für bereitgestellt wird `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="f57c3-477">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="f57c3-478">In dieser Version des Java-Clients ist websockets der einzige verfügbare Transport.</span><span class="sxs-lookup"><span data-stu-id="f57c3-478">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="f57c3-479">Im Java-Client wird der Transport mit der-Methode im ausgewählt `withTransport` `HttpHubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="f57c3-479">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="f57c3-480">Der Java-Client verwendet standardmäßig den websockets-Transport.</span><span class="sxs-lookup"><span data-stu-id="f57c3-480">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="f57c3-481">Der :::no-loc(SignalR)::: Java-Client unterstützt noch keinen Transport Fall Back.</span><span class="sxs-lookup"><span data-stu-id="f57c3-481">The :::no-loc(SignalR)::: Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="f57c3-482">Konfigurieren der bearerauthentifizierung</span><span class="sxs-lookup"><span data-stu-id="f57c3-482">Configure bearer authentication</span></span>

<span data-ttu-id="f57c3-483">Um Authentifizierungsdaten zusammen mit Anforderungen bereitzustellen :::no-loc(SignalR)::: , verwenden `AccessTokenProvider` Sie die-Option ( `accessTokenFactory` in JavaScript), um eine Funktion anzugeben, die das gewünschte Zugriffs Token zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="f57c3-483">To provide authentication data along with :::no-loc(SignalR)::: requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="f57c3-484">Im .NET-Client wird dieses Zugriffs Token als http-tokenauthentifizierungstoken (mit dem- `Authorization` Header mit dem Typ) an das Token "bearerauthentifizierung" geleitet `Bearer` .</span><span class="sxs-lookup"><span data-stu-id="f57c3-484">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="f57c3-485">Im JavaScript-Client wird das Zugriffs Token als bearertoken verwendet, **außer** in einigen Fällen, in denen Browser-APIs die Fähigkeit zum Anwenden von Headern einschränken (insbesondere in Server-Sent Ereignissen und websockets-Anforderungen).</span><span class="sxs-lookup"><span data-stu-id="f57c3-485">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="f57c3-486">In diesen Fällen wird das Zugriffs Token als Abfrage Zeichenfolgen-Wert bereitgestellt `access_token` .</span><span class="sxs-lookup"><span data-stu-id="f57c3-486">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="f57c3-487">Im .NET-Client kann die `AccessTokenProvider` Option mithilfe des Options Delegaten in angegeben werden `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="f57c3-487">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="f57c3-488">Im JavaScript-Client wird das Zugriffs Token konfiguriert, indem das- `accessTokenFactory` Feld für das Options-Objekt in festgelegt wird `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="f57c3-488">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="f57c3-489">Im :::no-loc(SignalR)::: Java-Client können Sie ein bearertoken für die Authentifizierung konfigurieren, indem Sie eine zugriffstokenfactory für " [httphubconnectionbuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)" bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="f57c3-489">In the :::no-loc(SignalR)::: Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="f57c3-490">Verwenden Sie [withaccesstokenfactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) , um eine [einzelne \<String> ](https://reactivex.io/documentation/single.html) [rxjava](https://github.com/ReactiveX/RxJava) -Instanz bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="f57c3-490">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="f57c3-491">Beim Aufrufen von " [Single.](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)Write" können Sie Logik schreiben, um Zugriffs Token für den Client zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="f57c3-491">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="f57c3-492">Konfigurieren von Timeout-und Keep-Alive-Optionen</span><span class="sxs-lookup"><span data-stu-id="f57c3-492">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="f57c3-493">Zusätzliche Optionen zum Konfigurieren von Timeout und Keep-Alive-Verhalten sind für das `HubConnection` Objekt selbst verfügbar:</span><span class="sxs-lookup"><span data-stu-id="f57c3-493">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="f57c3-494">.NET</span><span class="sxs-lookup"><span data-stu-id="f57c3-494">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="f57c3-495">Option</span><span class="sxs-lookup"><span data-stu-id="f57c3-495">Option</span></span> | <span data-ttu-id="f57c3-496">Standardwert</span><span class="sxs-lookup"><span data-stu-id="f57c3-496">Default value</span></span> | <span data-ttu-id="f57c3-497">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="f57c3-497">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="f57c3-498">30 Sekunden (30.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="f57c3-498">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="f57c3-499">Timeout für die Serveraktivität.</span><span class="sxs-lookup"><span data-stu-id="f57c3-499">Timeout for server activity.</span></span> <span data-ttu-id="f57c3-500">Wenn der Server in diesem Intervall keine Nachricht gesendet hat, betrachtet der Client den Server als getrennt und löst das `Closed` Ereignis `onclose` aus (in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="f57c3-500">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="f57c3-501">Dieser Wert muss groß genug sein, damit eine Ping-Nachricht vom Server gesendet **und** innerhalb des Timeout Intervalls vom Client empfangen wird.</span><span class="sxs-lookup"><span data-stu-id="f57c3-501">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="f57c3-502">Der empfohlene Wert ist eine Zahl, die mindestens dem Wert des Servers `KeepAliveInterval` entspricht, um Zeit für das Eintreffen von Pings zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="f57c3-502">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="f57c3-503">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="f57c3-503">15 seconds</span></span> | <span data-ttu-id="f57c3-504">Timeout für den anfänglichen Server Hand Shake.</span><span class="sxs-lookup"><span data-stu-id="f57c3-504">Timeout for initial server handshake.</span></span> <span data-ttu-id="f57c3-505">Wenn der Server in diesem Intervall keine Hand Shake Antwort sendet, bricht der Client den Handshake ab und löst das `Closed` Ereignis `onclose` aus (in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="f57c3-505">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="f57c3-506">Dies ist eine erweiterte Einstellung, die nur geändert werden sollte, wenn aufgrund schwerer Netzwerk Latenz Fehler aufgrund von Hand Shake Timeout auftreten.</span><span class="sxs-lookup"><span data-stu-id="f57c3-506">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="f57c3-507">Weitere Details zum Hand Shake Prozess finden Sie in der [ :::no-loc(SignalR)::: Hub-Protokollspezifikation](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="f57c3-507">For more detail on the handshake process, see the [:::no-loc(SignalR)::: Hub Protocol Specification](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="f57c3-508">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="f57c3-508">15 seconds</span></span> | <span data-ttu-id="f57c3-509">Bestimmt das Intervall, in dem der Client Ping-Nachrichten sendet.</span><span class="sxs-lookup"><span data-stu-id="f57c3-509">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="f57c3-510">Beim Senden von Nachrichten vom Client wird der Timer auf den Anfang des Intervalls zurückgesetzt.</span><span class="sxs-lookup"><span data-stu-id="f57c3-510">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="f57c3-511">Wenn der Client keine Nachricht in der `ClientTimeoutInterval` Gruppe auf dem Server gesendet hat, wird der Client vom Server als getrennt betrachtet.</span><span class="sxs-lookup"><span data-stu-id="f57c3-511">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="f57c3-512">Im .NET-Client werden Timeout Werte als Werte angegeben `TimeSpan` .</span><span class="sxs-lookup"><span data-stu-id="f57c3-512">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="f57c3-513">JavaScript</span><span class="sxs-lookup"><span data-stu-id="f57c3-513">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="f57c3-514">Option</span><span class="sxs-lookup"><span data-stu-id="f57c3-514">Option</span></span> | <span data-ttu-id="f57c3-515">Standardwert</span><span class="sxs-lookup"><span data-stu-id="f57c3-515">Default value</span></span> | <span data-ttu-id="f57c3-516">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="f57c3-516">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="f57c3-517">30 Sekunden (30.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="f57c3-517">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="f57c3-518">Timeout für die Serveraktivität.</span><span class="sxs-lookup"><span data-stu-id="f57c3-518">Timeout for server activity.</span></span> <span data-ttu-id="f57c3-519">Wenn der Server in diesem Intervall keine Nachricht gesendet hat, betrachtet der Client den Server als getrennt und löst das `onclose` Ereignis aus.</span><span class="sxs-lookup"><span data-stu-id="f57c3-519">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="f57c3-520">Dieser Wert muss groß genug sein, damit eine Ping-Nachricht vom Server gesendet **und** innerhalb des Timeout Intervalls vom Client empfangen wird.</span><span class="sxs-lookup"><span data-stu-id="f57c3-520">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="f57c3-521">Der empfohlene Wert ist eine Zahl, die mindestens dem Wert des Servers `KeepAliveInterval` entspricht, um Zeit für das Eintreffen von Pings zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="f57c3-521">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="f57c3-522">15 Sekunden (15.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="f57c3-522">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="f57c3-523">Bestimmt das Intervall, in dem der Client Ping-Nachrichten sendet.</span><span class="sxs-lookup"><span data-stu-id="f57c3-523">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="f57c3-524">Beim Senden von Nachrichten vom Client wird der Timer auf den Anfang des Intervalls zurückgesetzt.</span><span class="sxs-lookup"><span data-stu-id="f57c3-524">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="f57c3-525">Wenn der Client keine Nachricht in der `ClientTimeoutInterval` Gruppe auf dem Server gesendet hat, wird der Client vom Server als getrennt betrachtet.</span><span class="sxs-lookup"><span data-stu-id="f57c3-525">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="f57c3-526">Java</span><span class="sxs-lookup"><span data-stu-id="f57c3-526">Java</span></span>](#tab/java)

| <span data-ttu-id="f57c3-527">Option</span><span class="sxs-lookup"><span data-stu-id="f57c3-527">Option</span></span> | <span data-ttu-id="f57c3-528">Standardwert</span><span class="sxs-lookup"><span data-stu-id="f57c3-528">Default value</span></span> | <span data-ttu-id="f57c3-529">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="f57c3-529">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="f57c3-530">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="f57c3-530">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="f57c3-531">30 Sekunden (30.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="f57c3-531">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="f57c3-532">Timeout für die Serveraktivität.</span><span class="sxs-lookup"><span data-stu-id="f57c3-532">Timeout for server activity.</span></span> <span data-ttu-id="f57c3-533">Wenn der Server in diesem Intervall keine Nachricht gesendet hat, betrachtet der Client den Server als getrennt und löst das `onClose` Ereignis aus.</span><span class="sxs-lookup"><span data-stu-id="f57c3-533">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="f57c3-534">Dieser Wert muss groß genug sein, damit eine Ping-Nachricht vom Server gesendet **und** innerhalb des Timeout Intervalls vom Client empfangen wird.</span><span class="sxs-lookup"><span data-stu-id="f57c3-534">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="f57c3-535">Der empfohlene Wert ist eine Zahl, die mindestens dem Wert des Servers `KeepAliveInterval` entspricht, um Zeit für das Eintreffen von Pings zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="f57c3-535">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="f57c3-536">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="f57c3-536">15 seconds</span></span> | <span data-ttu-id="f57c3-537">Timeout für den anfänglichen Server Hand Shake.</span><span class="sxs-lookup"><span data-stu-id="f57c3-537">Timeout for initial server handshake.</span></span> <span data-ttu-id="f57c3-538">Wenn der Server in diesem Intervall keine Hand Shake Antwort sendet, bricht der Client den Handshake ab und löst das- `onClose` Ereignis aus.</span><span class="sxs-lookup"><span data-stu-id="f57c3-538">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="f57c3-539">Dies ist eine erweiterte Einstellung, die nur geändert werden sollte, wenn aufgrund schwerer Netzwerk Latenz Fehler aufgrund von Hand Shake Timeout auftreten.</span><span class="sxs-lookup"><span data-stu-id="f57c3-539">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="f57c3-540">Weitere Details zum Hand Shake Prozess finden Sie in der [ :::no-loc(SignalR)::: Hub-Protokollspezifikation](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="f57c3-540">For more detail on the Handshake process, see the [:::no-loc(SignalR)::: Hub Protocol Specification](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="f57c3-541">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="f57c3-541">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="f57c3-542">15 Sekunden (15.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="f57c3-542">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="f57c3-543">Bestimmt das Intervall, in dem der Client Ping-Nachrichten sendet.</span><span class="sxs-lookup"><span data-stu-id="f57c3-543">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="f57c3-544">Beim Senden von Nachrichten vom Client wird der Timer auf den Anfang des Intervalls zurückgesetzt.</span><span class="sxs-lookup"><span data-stu-id="f57c3-544">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="f57c3-545">Wenn der Client keine Nachricht in der `ClientTimeoutInterval` Gruppe auf dem Server gesendet hat, wird der Client vom Server als getrennt betrachtet.</span><span class="sxs-lookup"><span data-stu-id="f57c3-545">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="f57c3-546">Konfigurieren zusätzlicher Optionen</span><span class="sxs-lookup"><span data-stu-id="f57c3-546">Configure additional options</span></span>

<span data-ttu-id="f57c3-547">Zusätzliche Optionen können in der- `WithUrl` Methode ( `withUrl` in JavaScript) für `HubConnectionBuilder` oder für die verschiedenen Konfigurations-APIs auf dem `HttpHubConnectionBuilder` im Java-Client konfiguriert werden:</span><span class="sxs-lookup"><span data-stu-id="f57c3-547">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="f57c3-548">.NET</span><span class="sxs-lookup"><span data-stu-id="f57c3-548">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="f57c3-549">.Net-Option</span><span class="sxs-lookup"><span data-stu-id="f57c3-549">.NET Option</span></span> |  <span data-ttu-id="f57c3-550">Standardwert</span><span class="sxs-lookup"><span data-stu-id="f57c3-550">Default value</span></span> | <span data-ttu-id="f57c3-551">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="f57c3-551">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="f57c3-552">Eine Funktion, die eine Zeichenfolge zurückgibt, die als bearerauthentifizierungstoken in HTTP-Anforderungen bereitgestellt</span><span class="sxs-lookup"><span data-stu-id="f57c3-552">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="f57c3-553">Legen Sie dies auf fest, `true` um den Aushandlungs Schritt zu überspringen</span><span class="sxs-lookup"><span data-stu-id="f57c3-553">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="f57c3-554">Wird **nur unterstützt, wenn der websockets-Transport der einzige aktivierte Transport ist** .</span><span class="sxs-lookup"><span data-stu-id="f57c3-554">**Only supported when the WebSockets transport is the only enabled transport** .</span></span> <span data-ttu-id="f57c3-555">Diese Einstellung kann nicht aktiviert werden, wenn der Azure-Dienst verwendet wird :::no-loc(SignalR)::: .</span><span class="sxs-lookup"><span data-stu-id="f57c3-555">This setting can't be enabled when using the Azure :::no-loc(SignalR)::: Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="f57c3-556">Leer</span><span class="sxs-lookup"><span data-stu-id="f57c3-556">Empty</span></span> | <span data-ttu-id="f57c3-557">Eine Auflistung von TLS-Zertifikaten, die zum Authentifizieren von Anforderungen gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="f57c3-557">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `:::no-loc(Cookie):::s` | <span data-ttu-id="f57c3-558">Leer</span><span class="sxs-lookup"><span data-stu-id="f57c3-558">Empty</span></span> | <span data-ttu-id="f57c3-559">Eine Auflistung von http- :::no-loc(cookie)::: s, die mit jeder HTTP-Anforderung gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="f57c3-559">A collection of HTTP :::no-loc(cookie):::s to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="f57c3-560">Leer</span><span class="sxs-lookup"><span data-stu-id="f57c3-560">Empty</span></span> | <span data-ttu-id="f57c3-561">Anmelde Informationen, die mit jeder HTTP-Anforderung gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="f57c3-561">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="f57c3-562">5 Sekunden</span><span class="sxs-lookup"><span data-stu-id="f57c3-562">5 seconds</span></span> | <span data-ttu-id="f57c3-563">Nur websockets.</span><span class="sxs-lookup"><span data-stu-id="f57c3-563">WebSockets only.</span></span> <span data-ttu-id="f57c3-564">Die maximale Zeitspanne, die der Client nach dem Schließen des Servers wartet, um die Anforderung zum Schließen zu bestätigen.</span><span class="sxs-lookup"><span data-stu-id="f57c3-564">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="f57c3-565">Wenn der Server die Schließung innerhalb dieses Zeitraums nicht anerkennt, trennt der Client die Verbindung.</span><span class="sxs-lookup"><span data-stu-id="f57c3-565">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="f57c3-566">Leer</span><span class="sxs-lookup"><span data-stu-id="f57c3-566">Empty</span></span> | <span data-ttu-id="f57c3-567">Eine Karte mit zusätzlichen HTTP-Headern, die mit jeder HTTP-Anforderung gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="f57c3-567">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="f57c3-568">Ein Delegat, der verwendet werden kann, um den `HttpMessageHandler` zum Senden von HTTP-Anforderungen verwendeten zu konfigurieren oder zu ersetzen.</span><span class="sxs-lookup"><span data-stu-id="f57c3-568">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="f57c3-569">Wird nicht für WebSocket-Verbindungen verwendet.</span><span class="sxs-lookup"><span data-stu-id="f57c3-569">Not used for WebSocket connections.</span></span> <span data-ttu-id="f57c3-570">Dieser Delegat muss einen Wert zurückgeben, der nicht NULL ist, und erhält den Standardwert als Parameter.</span><span class="sxs-lookup"><span data-stu-id="f57c3-570">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="f57c3-571">Ändern Sie die Einstellungen für diesen Standardwert, und geben Sie ihn zurück, oder geben Sie eine neue `HttpMessageHandler` Instanz zurück.</span><span class="sxs-lookup"><span data-stu-id="f57c3-571">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="f57c3-572">**Wenn Sie den Handler ersetzen, stellen Sie sicher, dass die Einstellungen, die Sie beibehalten möchten, vom bereitgestellten Handler kopiert werden. Andernfalls gelten die konfigurierten Optionen (z. b. :::no-loc(Cookie)::: s und Header) nicht für den neuen Handler.**</span><span class="sxs-lookup"><span data-stu-id="f57c3-572">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as :::no-loc(Cookie):::s and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="f57c3-573">Ein HTTP-Proxy, der beim Senden von HTTP-Anforderungen verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="f57c3-573">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="f57c3-574">Legen Sie diesen booleschen Wert fest, um die Standard Anmelde Informationen für http-und websockets-Anforderungen zu senden</span><span class="sxs-lookup"><span data-stu-id="f57c3-574">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="f57c3-575">Dies ermöglicht die Verwendung der Windows-Authentifizierung.</span><span class="sxs-lookup"><span data-stu-id="f57c3-575">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="f57c3-576">Ein Delegat, der zum Konfigurieren zusätzlicher WebSocket-Optionen verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="f57c3-576">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="f57c3-577">Empfängt eine Instanz von [clientwebsocketoptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) , die zum Konfigurieren der Optionen verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="f57c3-577">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="f57c3-578">JavaScript</span><span class="sxs-lookup"><span data-stu-id="f57c3-578">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="f57c3-579">JavaScript-Option</span><span class="sxs-lookup"><span data-stu-id="f57c3-579">JavaScript Option</span></span> | <span data-ttu-id="f57c3-580">Standardwert</span><span class="sxs-lookup"><span data-stu-id="f57c3-580">Default Value</span></span> | <span data-ttu-id="f57c3-581">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="f57c3-581">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="f57c3-582">Eine Funktion, die eine Zeichenfolge zurückgibt, die als bearerauthentifizierungstoken in HTTP-Anforderungen bereitgestellt</span><span class="sxs-lookup"><span data-stu-id="f57c3-582">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `transport` | `null` | <span data-ttu-id="f57c3-583">Ein- <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType> Wert, der den für die Verbindung zu verwendenden Transport angibt.</span><span class="sxs-lookup"><span data-stu-id="f57c3-583">An <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType> value specifying the transport to use for the connection.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="f57c3-584">Legen Sie auf fest, `true` um die Bytes/Zeichen der vom Client gesendeten und empfangenen Nachrichten zu protokollieren.</span><span class="sxs-lookup"><span data-stu-id="f57c3-584">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="f57c3-585">Legen Sie dies auf fest, `true` um den Aushandlungs Schritt zu überspringen</span><span class="sxs-lookup"><span data-stu-id="f57c3-585">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="f57c3-586">Wird **nur unterstützt, wenn der websockets-Transport der einzige aktivierte Transport ist** .</span><span class="sxs-lookup"><span data-stu-id="f57c3-586">**Only supported when the WebSockets transport is the only enabled transport** .</span></span> <span data-ttu-id="f57c3-587">Diese Einstellung kann nicht aktiviert werden, wenn der Azure-Dienst verwendet wird :::no-loc(SignalR)::: .</span><span class="sxs-lookup"><span data-stu-id="f57c3-587">This setting can't be enabled when using the Azure :::no-loc(SignalR)::: Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="f57c3-588">Java</span><span class="sxs-lookup"><span data-stu-id="f57c3-588">Java</span></span>](#tab/java)

| <span data-ttu-id="f57c3-589">Java-Option</span><span class="sxs-lookup"><span data-stu-id="f57c3-589">Java Option</span></span> | <span data-ttu-id="f57c3-590">Standardwert</span><span class="sxs-lookup"><span data-stu-id="f57c3-590">Default Value</span></span> | <span data-ttu-id="f57c3-591">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="f57c3-591">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="f57c3-592">Eine Funktion, die eine Zeichenfolge zurückgibt, die als bearerauthentifizierungstoken in HTTP-Anforderungen bereitgestellt</span><span class="sxs-lookup"><span data-stu-id="f57c3-592">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="f57c3-593">Legen Sie dies auf fest, `true` um den Aushandlungs Schritt zu überspringen</span><span class="sxs-lookup"><span data-stu-id="f57c3-593">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="f57c3-594">Wird **nur unterstützt, wenn der websockets-Transport der einzige aktivierte Transport ist** .</span><span class="sxs-lookup"><span data-stu-id="f57c3-594">**Only supported when the WebSockets transport is the only enabled transport** .</span></span> <span data-ttu-id="f57c3-595">Diese Einstellung kann nicht aktiviert werden, wenn der Azure-Dienst verwendet wird :::no-loc(SignalR)::: .</span><span class="sxs-lookup"><span data-stu-id="f57c3-595">This setting can't be enabled when using the Azure :::no-loc(SignalR)::: Service.</span></span> |
| <span data-ttu-id="f57c3-596">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="f57c3-596">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="f57c3-597">Leer</span><span class="sxs-lookup"><span data-stu-id="f57c3-597">Empty</span></span> | <span data-ttu-id="f57c3-598">Eine Karte mit zusätzlichen HTTP-Headern, die mit jeder HTTP-Anforderung gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="f57c3-598">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="f57c3-599">Im .NET-Client können diese Optionen durch den für bereitgestellten Options Delegaten geändert werden `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="f57c3-599">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.:::no-loc(Cookie):::s.Add(new :::no-loc(Cookie):::(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="f57c3-600">Im JavaScript-Client können diese Optionen in einem JavaScript-Objekt bereitgestellt werden, das für Folgendes bereitgestellt wird `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="f57c3-600">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="f57c3-601">Im Java-Client können diese Optionen mit den Methoden auf der konfiguriert werden, die `HttpHubConnectionBuilder` von der `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="f57c3-601">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="f57c3-602">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="f57c3-602">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="f57c3-603">JSON/messagepack-Serialisierungsoptionen</span><span class="sxs-lookup"><span data-stu-id="f57c3-603">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="f57c3-604">ASP.net Core :::no-loc(SignalR)::: unterstützt zwei Protokolle zum Codieren von Nachrichten: [JSON](https://www.json.org/) und [messagepack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="f57c3-604">ASP.NET Core :::no-loc(SignalR)::: supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="f57c3-605">Jedes Protokoll verfügt über Konfigurationsoptionen für die Serialisierung.</span><span class="sxs-lookup"><span data-stu-id="f57c3-605">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="f57c3-606">Die JSON-Serialisierung kann mithilfe der [addjsonprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) -Erweiterungsmethode auf dem Server konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="f57c3-606">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="f57c3-607">`AddJsonProtocol`kann nach [Add :::no-loc(SignalR)::: ](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in hinzugefügt werden `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="f57c3-607">`AddJsonProtocol` can be added after [Add:::no-loc(SignalR):::](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="f57c3-608">Die- `AddJsonProtocol` Methode nimmt einen Delegaten an, der ein- `options` Objekt empfängt.</span><span class="sxs-lookup"><span data-stu-id="f57c3-608">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="f57c3-609">Die [payloadserializeroptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) -Eigenschaft für dieses Objekt ist ein- `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> Objekt, das zum Konfigurieren der Serialisierung von Argumenten und Rückgabe Werten verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="f57c3-609">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="f57c3-610">Weitere Informationen finden Sie in den [System.Text.Jszur Dokumentation](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="f57c3-610">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="f57c3-611">Verwenden Sie beispielsweise den folgenden Code in, um das Serialisierungsprogramm so zu konfigurieren, dass die Groß-/Kleinschreibung von Eigenschaftsnamen nicht geändert wird. verwenden Sie dazu den folgenden Code in `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="f57c3-611">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.Add:::no-loc(SignalR):::()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    });
```

<span data-ttu-id="f57c3-612">Im .NET-Client ist dieselbe `AddJsonProtocol` Erweiterungsmethode auf [hubconnectionbuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)vorhanden.</span><span class="sxs-lookup"><span data-stu-id="f57c3-612">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="f57c3-613">Der `Microsoft.Extensions.DependencyInjection` Namespace muss importiert werden, um die Erweiterungsmethode aufzulösen:</span><span class="sxs-lookup"><span data-stu-id="f57c3-613">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="f57c3-614">Zurzeit ist es nicht möglich, JSON-Serialisierung im JavaScript-Client zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="f57c3-614">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="f57c3-615">Zum Newtonsoft.Jswechseln</span><span class="sxs-lookup"><span data-stu-id="f57c3-615">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="f57c3-616">Wenn Sie Features von benötigen `Newtonsoft.Json` , die in nicht unterstützt `System.Text.Json` werden, finden Sie weitere Informationen unter [Wechseln zu Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="f57c3-616">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="f57c3-617">Messagepack-Serialisierungsoptionen</span><span class="sxs-lookup"><span data-stu-id="f57c3-617">MessagePack serialization options</span></span>

<span data-ttu-id="f57c3-618">Die messagepack-Serialisierung kann konfiguriert werden, indem ein Delegat für den [addmessagepackprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) -Aufruf bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="f57c3-618">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="f57c3-619">Weitere Informationen finden Sie [unter :::no-loc(SignalR)::: messagepack in](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="f57c3-619">See [MessagePack in :::no-loc(SignalR):::](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="f57c3-620">Zurzeit ist es nicht möglich, die messagepack-Serialisierung im JavaScript-Client zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="f57c3-620">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="f57c3-621">Konfigurieren von Serveroptionen</span><span class="sxs-lookup"><span data-stu-id="f57c3-621">Configure server options</span></span>

<span data-ttu-id="f57c3-622">In der folgenden Tabelle werden die Optionen zum Konfigurieren von :::no-loc(SignalR)::: Hubs beschrieben:</span><span class="sxs-lookup"><span data-stu-id="f57c3-622">The following table describes options for configuring :::no-loc(SignalR)::: hubs:</span></span>

| <span data-ttu-id="f57c3-623">Option</span><span class="sxs-lookup"><span data-stu-id="f57c3-623">Option</span></span> | <span data-ttu-id="f57c3-624">Standardwert</span><span class="sxs-lookup"><span data-stu-id="f57c3-624">Default Value</span></span> | <span data-ttu-id="f57c3-625">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="f57c3-625">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="f57c3-626">30 Sekunden</span><span class="sxs-lookup"><span data-stu-id="f57c3-626">30 seconds</span></span> | <span data-ttu-id="f57c3-627">Der Server wird vom-Client als getrennt betrachtet, wenn in diesem Intervall keine Nachricht (einschließlich Keep-Alive) empfangen wurde.</span><span class="sxs-lookup"><span data-stu-id="f57c3-627">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="f57c3-628">Es kann länger dauern, bis der Client als getrennt gekennzeichnet ist, weil dies implementiert ist.</span><span class="sxs-lookup"><span data-stu-id="f57c3-628">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="f57c3-629">Der empfohlene Wert ist Double- `KeepAliveInterval` Wert.</span><span class="sxs-lookup"><span data-stu-id="f57c3-629">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="f57c3-630">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="f57c3-630">15 seconds</span></span> | <span data-ttu-id="f57c3-631">Wenn der Client innerhalb dieses Zeitraums keine anfängliche Hand Shake Nachricht sendet, wird die Verbindung geschlossen.</span><span class="sxs-lookup"><span data-stu-id="f57c3-631">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="f57c3-632">Dies ist eine erweiterte Einstellung, die nur geändert werden sollte, wenn aufgrund schwerer Netzwerk Latenz Fehler aufgrund von Hand Shake Timeout auftreten.</span><span class="sxs-lookup"><span data-stu-id="f57c3-632">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="f57c3-633">Weitere Details zum Hand Shake Prozess finden Sie in der [ :::no-loc(SignalR)::: Hub-Protokollspezifikation](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="f57c3-633">For more detail on the handshake process, see the [:::no-loc(SignalR)::: Hub Protocol Specification](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="f57c3-634">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="f57c3-634">15 seconds</span></span> | <span data-ttu-id="f57c3-635">Wenn der Server innerhalb dieses Intervalls keine Nachricht gesendet hat, wird automatisch eine Ping-Nachricht gesendet, um die Verbindung offen zu halten.</span><span class="sxs-lookup"><span data-stu-id="f57c3-635">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="f57c3-636">Ändern Sie `KeepAliveInterval` die `ServerTimeout` / `serverTimeoutInMilliseconds` Einstellung auf dem Client, wenn Sie sich ändern.</span><span class="sxs-lookup"><span data-stu-id="f57c3-636">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="f57c3-637">Der empfohlene `ServerTimeout` / `serverTimeoutInMilliseconds` Wert ist Double- `KeepAliveInterval` Wert.</span><span class="sxs-lookup"><span data-stu-id="f57c3-637">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="f57c3-638">Alle installierten Protokolle</span><span class="sxs-lookup"><span data-stu-id="f57c3-638">All installed protocols</span></span> | <span data-ttu-id="f57c3-639">Protokolle, die von diesem Hub unterstützt werden.</span><span class="sxs-lookup"><span data-stu-id="f57c3-639">Protocols supported by this hub.</span></span> <span data-ttu-id="f57c3-640">Standardmäßig sind alle auf dem Server registrierten Protokolle zulässig, aber Protokolle können aus dieser Liste entfernt werden, um bestimmte Protokolle für einzelne Hubs zu deaktivieren.</span><span class="sxs-lookup"><span data-stu-id="f57c3-640">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="f57c3-641">Wenn der Wert `true` ist, werden ausführliche Ausnahme Meldungen an Clients zurückgegeben, wenn eine Ausnahme in einer Hub-Methode ausgelöst wird.</span><span class="sxs-lookup"><span data-stu-id="f57c3-641">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="f57c3-642">Der Standardwert ist `false` , da diese Ausnahme Meldungen vertrauliche Informationen enthalten können.</span><span class="sxs-lookup"><span data-stu-id="f57c3-642">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="f57c3-643">Die maximale Anzahl von Elementen, die für Client-uploadstreams gepuffert werden können.</span><span class="sxs-lookup"><span data-stu-id="f57c3-643">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="f57c3-644">Wenn dieser Grenzwert erreicht wird, wird die Verarbeitung von Aufrufen blockiert, bis der Server streamingelemente verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="f57c3-644">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="f57c3-645">32 KB</span><span class="sxs-lookup"><span data-stu-id="f57c3-645">32 KB</span></span> | <span data-ttu-id="f57c3-646">Maximale Größe einer einzelnen eingehenden Hub-Nachricht.</span><span class="sxs-lookup"><span data-stu-id="f57c3-646">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="f57c3-647">Optionen können für alle Hubs konfiguriert werden, indem ein Options Delegat für den Aufruf in bereitgestellt wird `Add:::no-loc(SignalR):::` `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="f57c3-647">Options can be configured for all hubs by providing an options delegate to the `Add:::no-loc(SignalR):::` call in `Startup.ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Add:::no-loc(SignalR):::(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

<span data-ttu-id="f57c3-648">Optionen für einen einzelnen Hub überschreiben die in bereitgestellten globalen Optionen `Add:::no-loc(SignalR):::` und können mithilfe von konfiguriert werden <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(SignalR):::DependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="f57c3-648">Options for a single hub override the global options provided in `Add:::no-loc(SignalR):::` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(SignalR):::DependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.Add:::no-loc(SignalR):::().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="f57c3-649">Erweiterte http-Konfigurationsoptionen</span><span class="sxs-lookup"><span data-stu-id="f57c3-649">Advanced HTTP configuration options</span></span>

<span data-ttu-id="f57c3-650">Verwenden `HttpConnectionDispatcherOptions` Sie, um erweiterte Einstellungen im Zusammenhang mit Transporte und der Speicherpuffer Verwaltung zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="f57c3-650">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="f57c3-651">Diese Optionen werden konfiguriert, indem ein [Delegat an \<T> maphub](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in übergeben wird `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="f57c3-651">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="f57c3-652">In der folgenden Tabelle werden die Optionen zum Konfigurieren der :::no-loc(SignalR)::: erweiterten http-Optionen von ASP.net Core beschrieben:</span><span class="sxs-lookup"><span data-stu-id="f57c3-652">The following table describes options for configuring ASP.NET Core :::no-loc(SignalR):::'s advanced HTTP options:</span></span>

| <span data-ttu-id="f57c3-653">Option</span><span class="sxs-lookup"><span data-stu-id="f57c3-653">Option</span></span> | <span data-ttu-id="f57c3-654">Standardwert</span><span class="sxs-lookup"><span data-stu-id="f57c3-654">Default Value</span></span> | <span data-ttu-id="f57c3-655">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="f57c3-655">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="f57c3-656">32 KB</span><span class="sxs-lookup"><span data-stu-id="f57c3-656">32 KB</span></span> | <span data-ttu-id="f57c3-657">Die maximale Anzahl von Bytes, die vom Client vor dem Anwenden des backdrucks vom Server gepuffert werden.</span><span class="sxs-lookup"><span data-stu-id="f57c3-657">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="f57c3-658">Wenn Sie diesen Wert erhöhen, kann der Server größere Nachrichten schneller empfangen, ohne dass ein Rückdruck angewendet wird, der Arbeitsspeicher Verbrauch kann jedoch gesteigert werden.</span><span class="sxs-lookup"><span data-stu-id="f57c3-658">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="f57c3-659">Daten, die automatisch von den `Authorize` auf die Hub-Klasse angewendeten Attributen gesammelt werden.</span><span class="sxs-lookup"><span data-stu-id="f57c3-659">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="f57c3-660">Eine Liste von [iautorizedata](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) -Objekten, die verwendet werden, um zu bestimmen, ob ein Client für die Verbindung mit dem Hub autorisiert ist.</span><span class="sxs-lookup"><span data-stu-id="f57c3-660">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="f57c3-661">32 KB</span><span class="sxs-lookup"><span data-stu-id="f57c3-661">32 KB</span></span> | <span data-ttu-id="f57c3-662">Die maximale Anzahl von Bytes, die von der APP gesendet werden, die der Server puffert, bevor der Rückdruck beobachtet wird</span><span class="sxs-lookup"><span data-stu-id="f57c3-662">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="f57c3-663">Wenn Sie diesen Wert erhöhen, kann der Server größere Nachrichten schneller Puffern, ohne dass auf den Rückstand gewartet wird, aber die Arbeitsspeicher Nutzung erhöhen kann.</span><span class="sxs-lookup"><span data-stu-id="f57c3-663">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="f57c3-664">Alle Transporte sind aktiviert.</span><span class="sxs-lookup"><span data-stu-id="f57c3-664">All Transports are enabled.</span></span> | <span data-ttu-id="f57c3-665">Ein Bitflags-Enumeration von `HttpTransportType` Werten, die die Transporte einschränken können, die ein Client für die Verbindungs Herstellung verwenden kann.</span><span class="sxs-lookup"><span data-stu-id="f57c3-665">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="f57c3-666">Siehe unten.</span><span class="sxs-lookup"><span data-stu-id="f57c3-666">See below.</span></span> | <span data-ttu-id="f57c3-667">Zusätzliche Optionen, die für den langen Abruf Transport spezifisch sind.</span><span class="sxs-lookup"><span data-stu-id="f57c3-667">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="f57c3-668">Siehe unten.</span><span class="sxs-lookup"><span data-stu-id="f57c3-668">See below.</span></span> | <span data-ttu-id="f57c3-669">Zusätzliche Optionen für den websockets-Transport.</span><span class="sxs-lookup"><span data-stu-id="f57c3-669">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="f57c3-670">Der lange Abruf Transport bietet zusätzliche Optionen, die mit der-Eigenschaft konfiguriert werden können `LongPolling` :</span><span class="sxs-lookup"><span data-stu-id="f57c3-670">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="f57c3-671">Option</span><span class="sxs-lookup"><span data-stu-id="f57c3-671">Option</span></span> | <span data-ttu-id="f57c3-672">Standardwert</span><span class="sxs-lookup"><span data-stu-id="f57c3-672">Default Value</span></span> | <span data-ttu-id="f57c3-673">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="f57c3-673">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="f57c3-674">90 Sekunden</span><span class="sxs-lookup"><span data-stu-id="f57c3-674">90 seconds</span></span> | <span data-ttu-id="f57c3-675">Die maximale Zeitspanne, die der Server wartet, bis eine Nachricht an den Client gesendet wird, bevor eine einzelne Abruf Anforderung beendet wird.</span><span class="sxs-lookup"><span data-stu-id="f57c3-675">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="f57c3-676">Das verringern dieses Werts bewirkt, dass der Client neue Abruf Anforderungen häufiger ausgibt.</span><span class="sxs-lookup"><span data-stu-id="f57c3-676">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="f57c3-677">Der WebSocket-Transport verfügt über zusätzliche Optionen, die mit der-Eigenschaft konfiguriert werden können `WebSockets` :</span><span class="sxs-lookup"><span data-stu-id="f57c3-677">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="f57c3-678">Option</span><span class="sxs-lookup"><span data-stu-id="f57c3-678">Option</span></span> | <span data-ttu-id="f57c3-679">Standardwert</span><span class="sxs-lookup"><span data-stu-id="f57c3-679">Default Value</span></span> | <span data-ttu-id="f57c3-680">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="f57c3-680">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="f57c3-681">5 Sekunden</span><span class="sxs-lookup"><span data-stu-id="f57c3-681">5 seconds</span></span> | <span data-ttu-id="f57c3-682">Wenn der Client nach dem Schließen des Servers nicht innerhalb dieses Zeitraums geschlossen werden kann, wird die Verbindung beendet.</span><span class="sxs-lookup"><span data-stu-id="f57c3-682">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="f57c3-683">Ein Delegat, der verwendet werden kann, um den- `Sec-WebSocket-Protocol` Header auf einen benutzerdefinierten Wert festzulegen.</span><span class="sxs-lookup"><span data-stu-id="f57c3-683">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="f57c3-684">Der Delegat empfängt die vom Client angeforderten Werte als Eingabe und erwartet, dass der gewünschte Wert zurückgegeben wird.</span><span class="sxs-lookup"><span data-stu-id="f57c3-684">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="f57c3-685">Konfigurieren von Clientoptionen</span><span class="sxs-lookup"><span data-stu-id="f57c3-685">Configure client options</span></span>

<span data-ttu-id="f57c3-686">Client Optionen können für den Typ konfiguriert werden `HubConnectionBuilder` (verfügbar in den .net-und JavaScript-Clients).</span><span class="sxs-lookup"><span data-stu-id="f57c3-686">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="f57c3-687">Sie steht auch im Java-Client zur Verfügung, aber die `HttpHubConnectionBuilder` Unterklasse enthält die Generator-Konfigurationsoptionen sowie die `HubConnection` selbst.</span><span class="sxs-lookup"><span data-stu-id="f57c3-687">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="f57c3-688">Konfigurieren der Protokollierung</span><span class="sxs-lookup"><span data-stu-id="f57c3-688">Configure logging</span></span>

<span data-ttu-id="f57c3-689">Die Protokollierung wird mithilfe der-Methode im .NET-Client konfiguriert `ConfigureLogging` .</span><span class="sxs-lookup"><span data-stu-id="f57c3-689">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="f57c3-690">Protokollierungs Anbieter und Filter können auf die gleiche Weise wie auf dem Server registriert werden.</span><span class="sxs-lookup"><span data-stu-id="f57c3-690">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="f57c3-691">Weitere Informationen finden Sie in der Dokumentation zur [Protokollierung ASP.net Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="f57c3-691">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="f57c3-692">Um Protokollierungs Anbieter zu registrieren, müssen Sie die erforderlichen Pakete installieren.</span><span class="sxs-lookup"><span data-stu-id="f57c3-692">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="f57c3-693">Eine vollständige Liste finden Sie im Abschnitt [integrierte Protokollierungs Anbieter](xref:fundamentals/logging/index#built-in-logging-providers) der Dokumentation.</span><span class="sxs-lookup"><span data-stu-id="f57c3-693">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="f57c3-694">Um z. b. die Konsolen Protokollierung zu aktivieren, installieren Sie das `Microsoft.Extensions.Logging.Console` nuget-Paket.</span><span class="sxs-lookup"><span data-stu-id="f57c3-694">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="f57c3-695">Die `AddConsole` Erweiterungsmethode aufzurufen:</span><span class="sxs-lookup"><span data-stu-id="f57c3-695">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="f57c3-696">Im JavaScript-Client ist eine ähnliche `configureLogging` Methode vorhanden.</span><span class="sxs-lookup"><span data-stu-id="f57c3-696">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="f57c3-697">Geben `LogLevel` Sie einen Wert an, der die minimale Ebene der zu erstellenden Protokollmeldungen angibt.</span><span class="sxs-lookup"><span data-stu-id="f57c3-697">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="f57c3-698">Protokolle werden in das Browser Konsolenfenster geschrieben.</span><span class="sxs-lookup"><span data-stu-id="f57c3-698">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="f57c3-699">Anstelle eines `LogLevel` Werts können Sie auch einen Wert angeben, der den `string` Namen einer Protokollebene darstellt.</span><span class="sxs-lookup"><span data-stu-id="f57c3-699">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="f57c3-700">Dies ist nützlich, wenn :::no-loc(SignalR)::: Sie die Protokollierung in Umgebungen konfigurieren, in denen Sie keinen Zugriff auf die `LogLevel` Konstanten haben.</span><span class="sxs-lookup"><span data-stu-id="f57c3-700">This is useful when configuring :::no-loc(SignalR)::: logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="f57c3-701">In der folgenden Tabelle sind die verfügbaren Protokoll Ebenen aufgeführt.</span><span class="sxs-lookup"><span data-stu-id="f57c3-701">The following table lists the available log levels.</span></span> <span data-ttu-id="f57c3-702">Der von Ihnen bereitgestellte Wert, `configureLogging` der die **minimale** Protokollierung festlegt, die protokolliert werden soll.</span><span class="sxs-lookup"><span data-stu-id="f57c3-702">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="f57c3-703">Nachrichten, die auf dieser Ebene protokolliert werden, **oder die nach der Tabelle aufgeführten Ebenen** werden protokolliert.</span><span class="sxs-lookup"><span data-stu-id="f57c3-703">Messages logged at this level, **or the levels listed after it in the table** , will be logged.</span></span>

| <span data-ttu-id="f57c3-704">String</span><span class="sxs-lookup"><span data-stu-id="f57c3-704">String</span></span>                      | <span data-ttu-id="f57c3-705">LogLevel</span><span class="sxs-lookup"><span data-stu-id="f57c3-705">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="f57c3-706">`info` **oder** `information`</span><span class="sxs-lookup"><span data-stu-id="f57c3-706">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="f57c3-707">`warn` **oder** `warning`</span><span class="sxs-lookup"><span data-stu-id="f57c3-707">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="f57c3-708">Um die Protokollierung vollständig zu deaktivieren, geben Sie `signalR.LogLevel.None` in der `configureLogging` Methode an</span><span class="sxs-lookup"><span data-stu-id="f57c3-708">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="f57c3-709">Weitere Informationen zur Protokollierung finden Sie in der [ :::no-loc(SignalR)::: Diagnose Dokumentation](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="f57c3-709">For more information on logging, see the [:::no-loc(SignalR)::: Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="f57c3-710">Der :::no-loc(SignalR)::: Java-Client verwendet die [SLF4J](https://www.slf4j.org/) -Bibliothek für die Protokollierung.</span><span class="sxs-lookup"><span data-stu-id="f57c3-710">The :::no-loc(SignalR)::: Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="f57c3-711">Dabei handelt es sich um eine allgemeine Protokollierungs-API, die es Benutzern der Bibliothek ermöglicht, ihre eigene spezifische Protokollierungs Implementierung zu wählen, indem Sie eine bestimmte Protokollierungs Abhängigkeit einbinden.</span><span class="sxs-lookup"><span data-stu-id="f57c3-711">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="f57c3-712">Der folgende Code Ausschnitt zeigt, wie `java.util.logging` mit dem Java- :::no-loc(SignalR)::: Client verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="f57c3-712">The following code snippet shows how to use `java.util.logging` with the :::no-loc(SignalR)::: Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="f57c3-713">Wenn Sie die Protokollierung in ihren Abhängigkeiten nicht konfigurieren, lädt SLF4J eine Standard Protokollierung ohne Vorgang mit folgender Warnmeldung:</span><span class="sxs-lookup"><span data-stu-id="f57c3-713">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="f57c3-714">Dies kann sicher ignoriert werden.</span><span class="sxs-lookup"><span data-stu-id="f57c3-714">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="f57c3-715">Zulässige Transporte konfigurieren</span><span class="sxs-lookup"><span data-stu-id="f57c3-715">Configure allowed transports</span></span>

<span data-ttu-id="f57c3-716">Die von verwendeten Transporte :::no-loc(SignalR)::: können im-Befehl `WithUrl` ( `withUrl` in JavaScript) konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="f57c3-716">The transports used by :::no-loc(SignalR)::: can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="f57c3-717">Ein bitweises OR der Werte von `HttpTransportType` kann verwendet werden, um den Client so einzuschränken, dass nur die angegebenen Transporte verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="f57c3-717">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="f57c3-718">Alle Transporte sind standardmäßig aktiviert.</span><span class="sxs-lookup"><span data-stu-id="f57c3-718">All transports are enabled by default.</span></span>

<span data-ttu-id="f57c3-719">Um z. b. den Server-Sent Ereignis Transport zu deaktivieren, aber websockets und lange Abruf Verbindungen zuzulassen:</span><span class="sxs-lookup"><span data-stu-id="f57c3-719">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="f57c3-720">Im JavaScript-Client werden Transporte durch Festlegen des- `transport` Felds für das Options-Objekt konfiguriert, das für bereitgestellt wird `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="f57c3-720">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="f57c3-721">In dieser Version des Java-Clients ist websockets der einzige verfügbare Transport.</span><span class="sxs-lookup"><span data-stu-id="f57c3-721">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="f57c3-722">Im Java-Client wird der Transport mit der-Methode im ausgewählt `withTransport` `HttpHubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="f57c3-722">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="f57c3-723">Der Java-Client verwendet standardmäßig den websockets-Transport.</span><span class="sxs-lookup"><span data-stu-id="f57c3-723">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="f57c3-724">Der :::no-loc(SignalR)::: Java-Client unterstützt noch keinen Transport Fall Back.</span><span class="sxs-lookup"><span data-stu-id="f57c3-724">The :::no-loc(SignalR)::: Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="f57c3-725">Konfigurieren der bearerauthentifizierung</span><span class="sxs-lookup"><span data-stu-id="f57c3-725">Configure bearer authentication</span></span>

<span data-ttu-id="f57c3-726">Um Authentifizierungsdaten zusammen mit Anforderungen bereitzustellen :::no-loc(SignalR)::: , verwenden `AccessTokenProvider` Sie die-Option ( `accessTokenFactory` in JavaScript), um eine Funktion anzugeben, die das gewünschte Zugriffs Token zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="f57c3-726">To provide authentication data along with :::no-loc(SignalR)::: requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="f57c3-727">Im .NET-Client wird dieses Zugriffs Token als http-tokenauthentifizierungstoken (mit dem- `Authorization` Header mit dem Typ) an das Token "bearerauthentifizierung" geleitet `Bearer` .</span><span class="sxs-lookup"><span data-stu-id="f57c3-727">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="f57c3-728">Im JavaScript-Client wird das Zugriffs Token als bearertoken verwendet, **außer** in einigen Fällen, in denen Browser-APIs die Fähigkeit zum Anwenden von Headern einschränken (insbesondere in Server-Sent Ereignissen und websockets-Anforderungen).</span><span class="sxs-lookup"><span data-stu-id="f57c3-728">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="f57c3-729">In diesen Fällen wird das Zugriffs Token als Abfrage Zeichenfolgen-Wert bereitgestellt `access_token` .</span><span class="sxs-lookup"><span data-stu-id="f57c3-729">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="f57c3-730">Im .NET-Client kann die `AccessTokenProvider` Option mithilfe des Options Delegaten in angegeben werden `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="f57c3-730">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="f57c3-731">Im JavaScript-Client wird das Zugriffs Token konfiguriert, indem das- `accessTokenFactory` Feld für das Options-Objekt in festgelegt wird `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="f57c3-731">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="f57c3-732">Im :::no-loc(SignalR)::: Java-Client können Sie ein bearertoken für die Authentifizierung konfigurieren, indem Sie eine zugriffstokenfactory für " [httphubconnectionbuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)" bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="f57c3-732">In the :::no-loc(SignalR)::: Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="f57c3-733">Verwenden Sie [withaccesstokenfactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) , um eine [einzelne \<String> ](https://reactivex.io/documentation/single.html) [rxjava](https://github.com/ReactiveX/RxJava) -Instanz bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="f57c3-733">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="f57c3-734">Beim Aufrufen von " [Single.](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)Write" können Sie Logik schreiben, um Zugriffs Token für den Client zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="f57c3-734">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="f57c3-735">Konfigurieren von Timeout-und Keep-Alive-Optionen</span><span class="sxs-lookup"><span data-stu-id="f57c3-735">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="f57c3-736">Zusätzliche Optionen zum Konfigurieren von Timeout und Keep-Alive-Verhalten sind für das `HubConnection` Objekt selbst verfügbar:</span><span class="sxs-lookup"><span data-stu-id="f57c3-736">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="f57c3-737">.NET</span><span class="sxs-lookup"><span data-stu-id="f57c3-737">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="f57c3-738">Option</span><span class="sxs-lookup"><span data-stu-id="f57c3-738">Option</span></span> | <span data-ttu-id="f57c3-739">Standardwert</span><span class="sxs-lookup"><span data-stu-id="f57c3-739">Default value</span></span> | <span data-ttu-id="f57c3-740">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="f57c3-740">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="f57c3-741">30 Sekunden (30.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="f57c3-741">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="f57c3-742">Timeout für die Serveraktivität.</span><span class="sxs-lookup"><span data-stu-id="f57c3-742">Timeout for server activity.</span></span> <span data-ttu-id="f57c3-743">Wenn der Server in diesem Intervall keine Nachricht gesendet hat, betrachtet der Client den Server als getrennt und löst das `Closed` Ereignis `onclose` aus (in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="f57c3-743">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="f57c3-744">Dieser Wert muss groß genug sein, damit eine Ping-Nachricht vom Server gesendet **und** innerhalb des Timeout Intervalls vom Client empfangen wird.</span><span class="sxs-lookup"><span data-stu-id="f57c3-744">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="f57c3-745">Der empfohlene Wert ist eine Zahl, die mindestens dem Wert des Servers `KeepAliveInterval` entspricht, um Zeit für das Eintreffen von Pings zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="f57c3-745">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="f57c3-746">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="f57c3-746">15 seconds</span></span> | <span data-ttu-id="f57c3-747">Timeout für den anfänglichen Server Hand Shake.</span><span class="sxs-lookup"><span data-stu-id="f57c3-747">Timeout for initial server handshake.</span></span> <span data-ttu-id="f57c3-748">Wenn der Server in diesem Intervall keine Hand Shake Antwort sendet, bricht der Client den Handshake ab und löst das `Closed` Ereignis `onclose` aus (in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="f57c3-748">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="f57c3-749">Dies ist eine erweiterte Einstellung, die nur geändert werden sollte, wenn aufgrund schwerer Netzwerk Latenz Fehler aufgrund von Hand Shake Timeout auftreten.</span><span class="sxs-lookup"><span data-stu-id="f57c3-749">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="f57c3-750">Weitere Details zum Hand Shake Prozess finden Sie in der [ :::no-loc(SignalR)::: Hub-Protokollspezifikation](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="f57c3-750">For more detail on the handshake process, see the [:::no-loc(SignalR)::: Hub Protocol Specification](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="f57c3-751">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="f57c3-751">15 seconds</span></span> | <span data-ttu-id="f57c3-752">Bestimmt das Intervall, in dem der Client Ping-Nachrichten sendet.</span><span class="sxs-lookup"><span data-stu-id="f57c3-752">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="f57c3-753">Beim Senden von Nachrichten vom Client wird der Timer auf den Anfang des Intervalls zurückgesetzt.</span><span class="sxs-lookup"><span data-stu-id="f57c3-753">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="f57c3-754">Wenn der Client keine Nachricht in der `ClientTimeoutInterval` Gruppe auf dem Server gesendet hat, wird der Client vom Server als getrennt betrachtet.</span><span class="sxs-lookup"><span data-stu-id="f57c3-754">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="f57c3-755">Im .NET-Client werden Timeout Werte als Werte angegeben `TimeSpan` .</span><span class="sxs-lookup"><span data-stu-id="f57c3-755">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="f57c3-756">JavaScript</span><span class="sxs-lookup"><span data-stu-id="f57c3-756">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="f57c3-757">Option</span><span class="sxs-lookup"><span data-stu-id="f57c3-757">Option</span></span> | <span data-ttu-id="f57c3-758">Standardwert</span><span class="sxs-lookup"><span data-stu-id="f57c3-758">Default value</span></span> | <span data-ttu-id="f57c3-759">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="f57c3-759">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="f57c3-760">30 Sekunden (30.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="f57c3-760">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="f57c3-761">Timeout für die Serveraktivität.</span><span class="sxs-lookup"><span data-stu-id="f57c3-761">Timeout for server activity.</span></span> <span data-ttu-id="f57c3-762">Wenn der Server in diesem Intervall keine Nachricht gesendet hat, betrachtet der Client den Server als getrennt und löst das `onclose` Ereignis aus.</span><span class="sxs-lookup"><span data-stu-id="f57c3-762">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="f57c3-763">Dieser Wert muss groß genug sein, damit eine Ping-Nachricht vom Server gesendet **und** innerhalb des Timeout Intervalls vom Client empfangen wird.</span><span class="sxs-lookup"><span data-stu-id="f57c3-763">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="f57c3-764">Der empfohlene Wert ist eine Zahl, die mindestens dem Wert des Servers `KeepAliveInterval` entspricht, um Zeit für das Eintreffen von Pings zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="f57c3-764">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="f57c3-765">15 Sekunden (15.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="f57c3-765">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="f57c3-766">Bestimmt das Intervall, in dem der Client Ping-Nachrichten sendet.</span><span class="sxs-lookup"><span data-stu-id="f57c3-766">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="f57c3-767">Beim Senden von Nachrichten vom Client wird der Timer auf den Anfang des Intervalls zurückgesetzt.</span><span class="sxs-lookup"><span data-stu-id="f57c3-767">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="f57c3-768">Wenn der Client keine Nachricht in der `ClientTimeoutInterval` Gruppe auf dem Server gesendet hat, wird der Client vom Server als getrennt betrachtet.</span><span class="sxs-lookup"><span data-stu-id="f57c3-768">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="f57c3-769">Java</span><span class="sxs-lookup"><span data-stu-id="f57c3-769">Java</span></span>](#tab/java)

| <span data-ttu-id="f57c3-770">Option</span><span class="sxs-lookup"><span data-stu-id="f57c3-770">Option</span></span> | <span data-ttu-id="f57c3-771">Standardwert</span><span class="sxs-lookup"><span data-stu-id="f57c3-771">Default value</span></span> | <span data-ttu-id="f57c3-772">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="f57c3-772">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="f57c3-773">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="f57c3-773">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="f57c3-774">30 Sekunden (30.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="f57c3-774">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="f57c3-775">Timeout für die Serveraktivität.</span><span class="sxs-lookup"><span data-stu-id="f57c3-775">Timeout for server activity.</span></span> <span data-ttu-id="f57c3-776">Wenn der Server in diesem Intervall keine Nachricht gesendet hat, betrachtet der Client den Server als getrennt und löst das `onClose` Ereignis aus.</span><span class="sxs-lookup"><span data-stu-id="f57c3-776">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="f57c3-777">Dieser Wert muss groß genug sein, damit eine Ping-Nachricht vom Server gesendet **und** innerhalb des Timeout Intervalls vom Client empfangen wird.</span><span class="sxs-lookup"><span data-stu-id="f57c3-777">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="f57c3-778">Der empfohlene Wert ist eine Zahl, die mindestens dem Wert des Servers `KeepAliveInterval` entspricht, um Zeit für das Eintreffen von Pings zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="f57c3-778">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="f57c3-779">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="f57c3-779">15 seconds</span></span> | <span data-ttu-id="f57c3-780">Timeout für den anfänglichen Server Hand Shake.</span><span class="sxs-lookup"><span data-stu-id="f57c3-780">Timeout for initial server handshake.</span></span> <span data-ttu-id="f57c3-781">Wenn der Server in diesem Intervall keine Hand Shake Antwort sendet, bricht der Client den Handshake ab und löst das- `onClose` Ereignis aus.</span><span class="sxs-lookup"><span data-stu-id="f57c3-781">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="f57c3-782">Dies ist eine erweiterte Einstellung, die nur geändert werden sollte, wenn aufgrund schwerer Netzwerk Latenz Fehler aufgrund von Hand Shake Timeout auftreten.</span><span class="sxs-lookup"><span data-stu-id="f57c3-782">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="f57c3-783">Weitere Details zum Hand Shake Prozess finden Sie in der [ :::no-loc(SignalR)::: Hub-Protokollspezifikation](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="f57c3-783">For more detail on the Handshake process, see the [:::no-loc(SignalR)::: Hub Protocol Specification](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="f57c3-784">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="f57c3-784">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="f57c3-785">15 Sekunden (15.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="f57c3-785">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="f57c3-786">Bestimmt das Intervall, in dem der Client Ping-Nachrichten sendet.</span><span class="sxs-lookup"><span data-stu-id="f57c3-786">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="f57c3-787">Beim Senden von Nachrichten vom Client wird der Timer auf den Anfang des Intervalls zurückgesetzt.</span><span class="sxs-lookup"><span data-stu-id="f57c3-787">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="f57c3-788">Wenn der Client keine Nachricht in der `ClientTimeoutInterval` Gruppe auf dem Server gesendet hat, wird der Client vom Server als getrennt betrachtet.</span><span class="sxs-lookup"><span data-stu-id="f57c3-788">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="f57c3-789">Konfigurieren zusätzlicher Optionen</span><span class="sxs-lookup"><span data-stu-id="f57c3-789">Configure additional options</span></span>

<span data-ttu-id="f57c3-790">Zusätzliche Optionen können in der- `WithUrl` Methode ( `withUrl` in JavaScript) für `HubConnectionBuilder` oder für die verschiedenen Konfigurations-APIs auf dem `HttpHubConnectionBuilder` im Java-Client konfiguriert werden:</span><span class="sxs-lookup"><span data-stu-id="f57c3-790">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="f57c3-791">.NET</span><span class="sxs-lookup"><span data-stu-id="f57c3-791">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="f57c3-792">.Net-Option</span><span class="sxs-lookup"><span data-stu-id="f57c3-792">.NET Option</span></span> |  <span data-ttu-id="f57c3-793">Standardwert</span><span class="sxs-lookup"><span data-stu-id="f57c3-793">Default value</span></span> | <span data-ttu-id="f57c3-794">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="f57c3-794">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="f57c3-795">Eine Funktion, die eine Zeichenfolge zurückgibt, die als bearerauthentifizierungstoken in HTTP-Anforderungen bereitgestellt</span><span class="sxs-lookup"><span data-stu-id="f57c3-795">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="f57c3-796">Legen Sie dies auf fest, `true` um den Aushandlungs Schritt zu überspringen</span><span class="sxs-lookup"><span data-stu-id="f57c3-796">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="f57c3-797">Wird **nur unterstützt, wenn der websockets-Transport der einzige aktivierte Transport ist** .</span><span class="sxs-lookup"><span data-stu-id="f57c3-797">**Only supported when the WebSockets transport is the only enabled transport** .</span></span> <span data-ttu-id="f57c3-798">Diese Einstellung kann nicht aktiviert werden, wenn der Azure-Dienst verwendet wird :::no-loc(SignalR)::: .</span><span class="sxs-lookup"><span data-stu-id="f57c3-798">This setting can't be enabled when using the Azure :::no-loc(SignalR)::: Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="f57c3-799">Leer</span><span class="sxs-lookup"><span data-stu-id="f57c3-799">Empty</span></span> | <span data-ttu-id="f57c3-800">Eine Auflistung von TLS-Zertifikaten, die zum Authentifizieren von Anforderungen gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="f57c3-800">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `:::no-loc(Cookie):::s` | <span data-ttu-id="f57c3-801">Leer</span><span class="sxs-lookup"><span data-stu-id="f57c3-801">Empty</span></span> | <span data-ttu-id="f57c3-802">Eine Auflistung von http- :::no-loc(cookie)::: s, die mit jeder HTTP-Anforderung gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="f57c3-802">A collection of HTTP :::no-loc(cookie):::s to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="f57c3-803">Leer</span><span class="sxs-lookup"><span data-stu-id="f57c3-803">Empty</span></span> | <span data-ttu-id="f57c3-804">Anmelde Informationen, die mit jeder HTTP-Anforderung gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="f57c3-804">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="f57c3-805">5 Sekunden</span><span class="sxs-lookup"><span data-stu-id="f57c3-805">5 seconds</span></span> | <span data-ttu-id="f57c3-806">Nur websockets.</span><span class="sxs-lookup"><span data-stu-id="f57c3-806">WebSockets only.</span></span> <span data-ttu-id="f57c3-807">Die maximale Zeitspanne, die der Client nach dem Schließen des Servers wartet, um die Anforderung zum Schließen zu bestätigen.</span><span class="sxs-lookup"><span data-stu-id="f57c3-807">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="f57c3-808">Wenn der Server die Schließung innerhalb dieses Zeitraums nicht anerkennt, trennt der Client die Verbindung.</span><span class="sxs-lookup"><span data-stu-id="f57c3-808">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="f57c3-809">Leer</span><span class="sxs-lookup"><span data-stu-id="f57c3-809">Empty</span></span> | <span data-ttu-id="f57c3-810">Eine Karte mit zusätzlichen HTTP-Headern, die mit jeder HTTP-Anforderung gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="f57c3-810">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="f57c3-811">Ein Delegat, der verwendet werden kann, um den `HttpMessageHandler` zum Senden von HTTP-Anforderungen verwendeten zu konfigurieren oder zu ersetzen.</span><span class="sxs-lookup"><span data-stu-id="f57c3-811">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="f57c3-812">Wird nicht für WebSocket-Verbindungen verwendet.</span><span class="sxs-lookup"><span data-stu-id="f57c3-812">Not used for WebSocket connections.</span></span> <span data-ttu-id="f57c3-813">Dieser Delegat muss einen Wert zurückgeben, der nicht NULL ist, und erhält den Standardwert als Parameter.</span><span class="sxs-lookup"><span data-stu-id="f57c3-813">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="f57c3-814">Ändern Sie die Einstellungen für diesen Standardwert, und geben Sie ihn zurück, oder geben Sie eine neue `HttpMessageHandler` Instanz zurück.</span><span class="sxs-lookup"><span data-stu-id="f57c3-814">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="f57c3-815">**Wenn Sie den Handler ersetzen, stellen Sie sicher, dass die Einstellungen, die Sie beibehalten möchten, vom bereitgestellten Handler kopiert werden. Andernfalls gelten die konfigurierten Optionen (z. b. :::no-loc(Cookie)::: s und Header) nicht für den neuen Handler.**</span><span class="sxs-lookup"><span data-stu-id="f57c3-815">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as :::no-loc(Cookie):::s and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="f57c3-816">Ein HTTP-Proxy, der beim Senden von HTTP-Anforderungen verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="f57c3-816">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="f57c3-817">Legen Sie diesen booleschen Wert fest, um die Standard Anmelde Informationen für http-und websockets-Anforderungen zu senden</span><span class="sxs-lookup"><span data-stu-id="f57c3-817">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="f57c3-818">Dies ermöglicht die Verwendung der Windows-Authentifizierung.</span><span class="sxs-lookup"><span data-stu-id="f57c3-818">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="f57c3-819">Ein Delegat, der zum Konfigurieren zusätzlicher WebSocket-Optionen verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="f57c3-819">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="f57c3-820">Empfängt eine Instanz von [clientwebsocketoptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) , die zum Konfigurieren der Optionen verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="f57c3-820">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="f57c3-821">JavaScript</span><span class="sxs-lookup"><span data-stu-id="f57c3-821">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="f57c3-822">JavaScript-Option</span><span class="sxs-lookup"><span data-stu-id="f57c3-822">JavaScript Option</span></span> | <span data-ttu-id="f57c3-823">Standardwert</span><span class="sxs-lookup"><span data-stu-id="f57c3-823">Default Value</span></span> | <span data-ttu-id="f57c3-824">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="f57c3-824">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="f57c3-825">Eine Funktion, die eine Zeichenfolge zurückgibt, die als bearerauthentifizierungstoken in HTTP-Anforderungen bereitgestellt</span><span class="sxs-lookup"><span data-stu-id="f57c3-825">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `transport` | `null` | <span data-ttu-id="f57c3-826">Ein- <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType> Wert, der den für die Verbindung zu verwendenden Transport angibt.</span><span class="sxs-lookup"><span data-stu-id="f57c3-826">An <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType> value specifying the transport to use for the connection.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="f57c3-827">Legen Sie auf fest, `true` um die Bytes/Zeichen der vom Client gesendeten und empfangenen Nachrichten zu protokollieren.</span><span class="sxs-lookup"><span data-stu-id="f57c3-827">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="f57c3-828">Legen Sie dies auf fest, `true` um den Aushandlungs Schritt zu überspringen</span><span class="sxs-lookup"><span data-stu-id="f57c3-828">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="f57c3-829">Wird **nur unterstützt, wenn der websockets-Transport der einzige aktivierte Transport ist** .</span><span class="sxs-lookup"><span data-stu-id="f57c3-829">**Only supported when the WebSockets transport is the only enabled transport** .</span></span> <span data-ttu-id="f57c3-830">Diese Einstellung kann nicht aktiviert werden, wenn der Azure-Dienst verwendet wird :::no-loc(SignalR)::: .</span><span class="sxs-lookup"><span data-stu-id="f57c3-830">This setting can't be enabled when using the Azure :::no-loc(SignalR)::: Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="f57c3-831">Java</span><span class="sxs-lookup"><span data-stu-id="f57c3-831">Java</span></span>](#tab/java)

| <span data-ttu-id="f57c3-832">Java-Option</span><span class="sxs-lookup"><span data-stu-id="f57c3-832">Java Option</span></span> | <span data-ttu-id="f57c3-833">Standardwert</span><span class="sxs-lookup"><span data-stu-id="f57c3-833">Default Value</span></span> | <span data-ttu-id="f57c3-834">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="f57c3-834">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="f57c3-835">Eine Funktion, die eine Zeichenfolge zurückgibt, die als bearerauthentifizierungstoken in HTTP-Anforderungen bereitgestellt</span><span class="sxs-lookup"><span data-stu-id="f57c3-835">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="f57c3-836">Legen Sie dies auf fest, `true` um den Aushandlungs Schritt zu überspringen</span><span class="sxs-lookup"><span data-stu-id="f57c3-836">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="f57c3-837">Wird **nur unterstützt, wenn der websockets-Transport der einzige aktivierte Transport ist** .</span><span class="sxs-lookup"><span data-stu-id="f57c3-837">**Only supported when the WebSockets transport is the only enabled transport** .</span></span> <span data-ttu-id="f57c3-838">Diese Einstellung kann nicht aktiviert werden, wenn der Azure-Dienst verwendet wird :::no-loc(SignalR)::: .</span><span class="sxs-lookup"><span data-stu-id="f57c3-838">This setting can't be enabled when using the Azure :::no-loc(SignalR)::: Service.</span></span> |
| <span data-ttu-id="f57c3-839">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="f57c3-839">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="f57c3-840">Leer</span><span class="sxs-lookup"><span data-stu-id="f57c3-840">Empty</span></span> | <span data-ttu-id="f57c3-841">Eine Karte mit zusätzlichen HTTP-Headern, die mit jeder HTTP-Anforderung gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="f57c3-841">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="f57c3-842">Im .NET-Client können diese Optionen durch den für bereitgestellten Options Delegaten geändert werden `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="f57c3-842">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.:::no-loc(Cookie):::s.Add(new :::no-loc(Cookie):::(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="f57c3-843">Im JavaScript-Client können diese Optionen in einem JavaScript-Objekt bereitgestellt werden, das für Folgendes bereitgestellt wird `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="f57c3-843">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="f57c3-844">Im Java-Client können diese Optionen mit den Methoden auf der konfiguriert werden, die `HttpHubConnectionBuilder` von der `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="f57c3-844">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="f57c3-845">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="f57c3-845">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="f57c3-846">JSON/messagepack-Serialisierungsoptionen</span><span class="sxs-lookup"><span data-stu-id="f57c3-846">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="f57c3-847">ASP.net Core :::no-loc(SignalR)::: unterstützt zwei Protokolle zum Codieren von Nachrichten: [JSON](https://www.json.org/) und [messagepack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="f57c3-847">ASP.NET Core :::no-loc(SignalR)::: supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="f57c3-848">Jedes Protokoll verfügt über Konfigurationsoptionen für die Serialisierung.</span><span class="sxs-lookup"><span data-stu-id="f57c3-848">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="f57c3-849">Die JSON-Serialisierung kann mithilfe der [addjsonprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) -Erweiterungsmethode auf dem Server konfiguriert werden, die nach [dem :::no-loc(SignalR)::: Hinzufügen](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in der Methode hinzugefügt werden kann `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="f57c3-849">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [Add:::no-loc(SignalR):::](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="f57c3-850">Die- `AddJsonProtocol` Methode nimmt einen Delegaten an, der ein- `options` Objekt empfängt.</span><span class="sxs-lookup"><span data-stu-id="f57c3-850">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="f57c3-851">Die [payloadserializersettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) -Eigenschaft für dieses Objekt ist ein JSON.net- `JsonSerializerSettings` Objekt, das zum Konfigurieren der Serialisierung von Argumenten und Rückgabe Werten verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="f57c3-851">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="f57c3-852">Weitere Informationen finden Sie in der [JSON.NET-Dokumentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span><span class="sxs-lookup"><span data-stu-id="f57c3-852">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="f57c3-853">Verwenden Sie beispielsweise den folgenden Code in, um das Serialisierungsprogramm so zu konfigurieren, dass "PascalCase"-Eigenschaftsnamen anstelle der Standardnamen "CamelCase" verwendet werden `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="f57c3-853">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.Add:::no-loc(SignalR):::()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="f57c3-854">Im .NET-Client ist dieselbe `AddJsonProtocol` Erweiterungsmethode auf [hubconnectionbuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)vorhanden.</span><span class="sxs-lookup"><span data-stu-id="f57c3-854">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="f57c3-855">Der `Microsoft.Extensions.DependencyInjection` Namespace muss importiert werden, um die Erweiterungsmethode aufzulösen:</span><span class="sxs-lookup"><span data-stu-id="f57c3-855">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="f57c3-856">Zurzeit ist es nicht möglich, JSON-Serialisierung im JavaScript-Client zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="f57c3-856">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="f57c3-857">Messagepack-Serialisierungsoptionen</span><span class="sxs-lookup"><span data-stu-id="f57c3-857">MessagePack serialization options</span></span>

<span data-ttu-id="f57c3-858">Die messagepack-Serialisierung kann konfiguriert werden, indem ein Delegat für den [addmessagepackprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) -Aufruf bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="f57c3-858">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="f57c3-859">Weitere Informationen finden Sie [unter :::no-loc(SignalR)::: messagepack in](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="f57c3-859">See [MessagePack in :::no-loc(SignalR):::](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="f57c3-860">Zurzeit ist es nicht möglich, die messagepack-Serialisierung im JavaScript-Client zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="f57c3-860">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="f57c3-861">Konfigurieren von Serveroptionen</span><span class="sxs-lookup"><span data-stu-id="f57c3-861">Configure server options</span></span>

<span data-ttu-id="f57c3-862">In der folgenden Tabelle werden die Optionen zum Konfigurieren von :::no-loc(SignalR)::: Hubs beschrieben:</span><span class="sxs-lookup"><span data-stu-id="f57c3-862">The following table describes options for configuring :::no-loc(SignalR)::: hubs:</span></span>

| <span data-ttu-id="f57c3-863">Option</span><span class="sxs-lookup"><span data-stu-id="f57c3-863">Option</span></span> | <span data-ttu-id="f57c3-864">Standardwert</span><span class="sxs-lookup"><span data-stu-id="f57c3-864">Default Value</span></span> | <span data-ttu-id="f57c3-865">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="f57c3-865">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="f57c3-866">30 Sekunden</span><span class="sxs-lookup"><span data-stu-id="f57c3-866">30 seconds</span></span> | <span data-ttu-id="f57c3-867">Der Server wird vom-Client als getrennt betrachtet, wenn in diesem Intervall keine Nachricht (einschließlich Keep-Alive) empfangen wurde.</span><span class="sxs-lookup"><span data-stu-id="f57c3-867">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="f57c3-868">Es kann länger dauern, bis der Client als getrennt gekennzeichnet ist, weil dies implementiert ist.</span><span class="sxs-lookup"><span data-stu-id="f57c3-868">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="f57c3-869">Der empfohlene Wert ist Double- `KeepAliveInterval` Wert.</span><span class="sxs-lookup"><span data-stu-id="f57c3-869">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="f57c3-870">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="f57c3-870">15 seconds</span></span> | <span data-ttu-id="f57c3-871">Wenn der Client innerhalb dieses Zeitraums keine anfängliche Hand Shake Nachricht sendet, wird die Verbindung geschlossen.</span><span class="sxs-lookup"><span data-stu-id="f57c3-871">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="f57c3-872">Dies ist eine erweiterte Einstellung, die nur geändert werden sollte, wenn aufgrund schwerer Netzwerk Latenz Fehler aufgrund von Hand Shake Timeout auftreten.</span><span class="sxs-lookup"><span data-stu-id="f57c3-872">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="f57c3-873">Weitere Details zum Hand Shake Prozess finden Sie in der [ :::no-loc(SignalR)::: Hub-Protokollspezifikation](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="f57c3-873">For more detail on the handshake process, see the [:::no-loc(SignalR)::: Hub Protocol Specification](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="f57c3-874">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="f57c3-874">15 seconds</span></span> | <span data-ttu-id="f57c3-875">Wenn der Server innerhalb dieses Intervalls keine Nachricht gesendet hat, wird automatisch eine Ping-Nachricht gesendet, um die Verbindung offen zu halten.</span><span class="sxs-lookup"><span data-stu-id="f57c3-875">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="f57c3-876">Ändern Sie `KeepAliveInterval` die `ServerTimeout` / `serverTimeoutInMilliseconds` Einstellung auf dem Client, wenn Sie sich ändern.</span><span class="sxs-lookup"><span data-stu-id="f57c3-876">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="f57c3-877">Der empfohlene `ServerTimeout` / `serverTimeoutInMilliseconds` Wert ist Double- `KeepAliveInterval` Wert.</span><span class="sxs-lookup"><span data-stu-id="f57c3-877">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="f57c3-878">Alle installierten Protokolle</span><span class="sxs-lookup"><span data-stu-id="f57c3-878">All installed protocols</span></span> | <span data-ttu-id="f57c3-879">Protokolle, die von diesem Hub unterstützt werden.</span><span class="sxs-lookup"><span data-stu-id="f57c3-879">Protocols supported by this hub.</span></span> <span data-ttu-id="f57c3-880">Standardmäßig sind alle auf dem Server registrierten Protokolle zulässig, aber Protokolle können aus dieser Liste entfernt werden, um bestimmte Protokolle für einzelne Hubs zu deaktivieren.</span><span class="sxs-lookup"><span data-stu-id="f57c3-880">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="f57c3-881">Wenn der Wert `true` ist, werden ausführliche Ausnahme Meldungen an Clients zurückgegeben, wenn eine Ausnahme in einer Hub-Methode ausgelöst wird.</span><span class="sxs-lookup"><span data-stu-id="f57c3-881">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="f57c3-882">Der Standardwert ist `false` , da diese Ausnahme Meldungen vertrauliche Informationen enthalten können.</span><span class="sxs-lookup"><span data-stu-id="f57c3-882">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="f57c3-883">Optionen können für alle Hubs konfiguriert werden, indem ein Options Delegat für den Aufruf in bereitgestellt wird `Add:::no-loc(SignalR):::` `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="f57c3-883">Options can be configured for all hubs by providing an options delegate to the `Add:::no-loc(SignalR):::` call in `Startup.ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Add:::no-loc(SignalR):::(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

<span data-ttu-id="f57c3-884">Optionen für einen einzelnen Hub überschreiben die in bereitgestellten globalen Optionen `Add:::no-loc(SignalR):::` und können mithilfe von konfiguriert werden <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(SignalR):::DependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="f57c3-884">Options for a single hub override the global options provided in `Add:::no-loc(SignalR):::` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(SignalR):::DependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.Add:::no-loc(SignalR):::().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="f57c3-885">Erweiterte http-Konfigurationsoptionen</span><span class="sxs-lookup"><span data-stu-id="f57c3-885">Advanced HTTP configuration options</span></span>

<span data-ttu-id="f57c3-886">Verwenden `HttpConnectionDispatcherOptions` Sie, um erweiterte Einstellungen im Zusammenhang mit Transporte und der Speicherpuffer Verwaltung zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="f57c3-886">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="f57c3-887">Diese Optionen werden konfiguriert, indem ein [Delegat an \<T> maphub](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in übergeben wird `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="f57c3-887">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.Use:::no-loc(SignalR):::((configure) =>
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

<span data-ttu-id="f57c3-888">In der folgenden Tabelle werden die Optionen zum Konfigurieren der :::no-loc(SignalR)::: erweiterten http-Optionen von ASP.net Core beschrieben:</span><span class="sxs-lookup"><span data-stu-id="f57c3-888">The following table describes options for configuring ASP.NET Core :::no-loc(SignalR):::'s advanced HTTP options:</span></span>

| <span data-ttu-id="f57c3-889">Option</span><span class="sxs-lookup"><span data-stu-id="f57c3-889">Option</span></span> | <span data-ttu-id="f57c3-890">Standardwert</span><span class="sxs-lookup"><span data-stu-id="f57c3-890">Default Value</span></span> | <span data-ttu-id="f57c3-891">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="f57c3-891">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="f57c3-892">32 KB</span><span class="sxs-lookup"><span data-stu-id="f57c3-892">32 KB</span></span> | <span data-ttu-id="f57c3-893">Die maximale Anzahl von Bytes, die vom Client empfangen werden, der vom Server gepuffert wird.</span><span class="sxs-lookup"><span data-stu-id="f57c3-893">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="f57c3-894">Eine Erhöhung dieses Werts ermöglicht es dem Server, größere Nachrichten zu empfangen, kann sich jedoch negativ auf den Arbeitsspeicherverbrauch auswirken.</span><span class="sxs-lookup"><span data-stu-id="f57c3-894">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="f57c3-895">Daten, die automatisch von den `Authorize` auf die Hub-Klasse angewendeten Attributen gesammelt werden.</span><span class="sxs-lookup"><span data-stu-id="f57c3-895">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="f57c3-896">Eine Liste von [iautorizedata](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) -Objekten, die verwendet werden, um zu bestimmen, ob ein Client für die Verbindung mit dem Hub autorisiert ist.</span><span class="sxs-lookup"><span data-stu-id="f57c3-896">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="f57c3-897">32 KB</span><span class="sxs-lookup"><span data-stu-id="f57c3-897">32 KB</span></span> | <span data-ttu-id="f57c3-898">Die maximale Anzahl von Bytes, die von der APP gesendet werden, die der Server puffert.</span><span class="sxs-lookup"><span data-stu-id="f57c3-898">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="f57c3-899">Wenn Sie diesen Wert erhöhen, kann der Server größere Nachrichten senden, kann sich jedoch negativ auf die Arbeitsspeicher Auslastung auswirken.</span><span class="sxs-lookup"><span data-stu-id="f57c3-899">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="f57c3-900">Alle Transporte sind aktiviert.</span><span class="sxs-lookup"><span data-stu-id="f57c3-900">All Transports are enabled.</span></span> | <span data-ttu-id="f57c3-901">Ein Bitflags-Enumeration von `HttpTransportType` Werten, die die Transporte einschränken können, die ein Client für die Verbindungs Herstellung verwenden kann.</span><span class="sxs-lookup"><span data-stu-id="f57c3-901">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="f57c3-902">Siehe unten.</span><span class="sxs-lookup"><span data-stu-id="f57c3-902">See below.</span></span> | <span data-ttu-id="f57c3-903">Zusätzliche Optionen, die für den langen Abruf Transport spezifisch sind.</span><span class="sxs-lookup"><span data-stu-id="f57c3-903">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="f57c3-904">Siehe unten.</span><span class="sxs-lookup"><span data-stu-id="f57c3-904">See below.</span></span> | <span data-ttu-id="f57c3-905">Zusätzliche Optionen für den websockets-Transport.</span><span class="sxs-lookup"><span data-stu-id="f57c3-905">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="f57c3-906">Der lange Abruf Transport bietet zusätzliche Optionen, die mit der-Eigenschaft konfiguriert werden können `LongPolling` :</span><span class="sxs-lookup"><span data-stu-id="f57c3-906">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="f57c3-907">Option</span><span class="sxs-lookup"><span data-stu-id="f57c3-907">Option</span></span> | <span data-ttu-id="f57c3-908">Standardwert</span><span class="sxs-lookup"><span data-stu-id="f57c3-908">Default Value</span></span> | <span data-ttu-id="f57c3-909">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="f57c3-909">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="f57c3-910">90 Sekunden</span><span class="sxs-lookup"><span data-stu-id="f57c3-910">90 seconds</span></span> | <span data-ttu-id="f57c3-911">Die maximale Zeitspanne, die der Server wartet, bis eine Nachricht an den Client gesendet wird, bevor eine einzelne Abruf Anforderung beendet wird.</span><span class="sxs-lookup"><span data-stu-id="f57c3-911">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="f57c3-912">Das verringern dieses Werts bewirkt, dass der Client neue Abruf Anforderungen häufiger ausgibt.</span><span class="sxs-lookup"><span data-stu-id="f57c3-912">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="f57c3-913">Der WebSocket-Transport verfügt über zusätzliche Optionen, die mit der-Eigenschaft konfiguriert werden können `WebSockets` :</span><span class="sxs-lookup"><span data-stu-id="f57c3-913">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="f57c3-914">Option</span><span class="sxs-lookup"><span data-stu-id="f57c3-914">Option</span></span> | <span data-ttu-id="f57c3-915">Standardwert</span><span class="sxs-lookup"><span data-stu-id="f57c3-915">Default Value</span></span> | <span data-ttu-id="f57c3-916">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="f57c3-916">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="f57c3-917">5 Sekunden</span><span class="sxs-lookup"><span data-stu-id="f57c3-917">5 seconds</span></span> | <span data-ttu-id="f57c3-918">Wenn der Client nach dem Schließen des Servers nicht innerhalb dieses Zeitraums geschlossen werden kann, wird die Verbindung beendet.</span><span class="sxs-lookup"><span data-stu-id="f57c3-918">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="f57c3-919">Ein Delegat, der verwendet werden kann, um den- `Sec-WebSocket-Protocol` Header auf einen benutzerdefinierten Wert festzulegen.</span><span class="sxs-lookup"><span data-stu-id="f57c3-919">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="f57c3-920">Der Delegat empfängt die vom Client angeforderten Werte als Eingabe und erwartet, dass der gewünschte Wert zurückgegeben wird.</span><span class="sxs-lookup"><span data-stu-id="f57c3-920">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="f57c3-921">Konfigurieren von Clientoptionen</span><span class="sxs-lookup"><span data-stu-id="f57c3-921">Configure client options</span></span>

<span data-ttu-id="f57c3-922">Client Optionen können für den Typ konfiguriert werden `HubConnectionBuilder` (verfügbar in den .net-und JavaScript-Clients).</span><span class="sxs-lookup"><span data-stu-id="f57c3-922">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="f57c3-923">Sie steht auch im Java-Client zur Verfügung, aber die `HttpHubConnectionBuilder` Unterklasse enthält die Generator-Konfigurationsoptionen sowie die `HubConnection` selbst.</span><span class="sxs-lookup"><span data-stu-id="f57c3-923">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="f57c3-924">Konfigurieren der Protokollierung</span><span class="sxs-lookup"><span data-stu-id="f57c3-924">Configure logging</span></span>

<span data-ttu-id="f57c3-925">Die Protokollierung wird mithilfe der-Methode im .NET-Client konfiguriert `ConfigureLogging` .</span><span class="sxs-lookup"><span data-stu-id="f57c3-925">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="f57c3-926">Protokollierungs Anbieter und Filter können auf die gleiche Weise wie auf dem Server registriert werden.</span><span class="sxs-lookup"><span data-stu-id="f57c3-926">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="f57c3-927">Weitere Informationen finden Sie in der Dokumentation zur [Protokollierung ASP.net Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="f57c3-927">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="f57c3-928">Um Protokollierungs Anbieter zu registrieren, müssen Sie die erforderlichen Pakete installieren.</span><span class="sxs-lookup"><span data-stu-id="f57c3-928">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="f57c3-929">Eine vollständige Liste finden Sie im Abschnitt [integrierte Protokollierungs Anbieter](xref:fundamentals/logging/index#built-in-logging-providers) der Dokumentation.</span><span class="sxs-lookup"><span data-stu-id="f57c3-929">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="f57c3-930">Um z. b. die Konsolen Protokollierung zu aktivieren, installieren Sie das `Microsoft.Extensions.Logging.Console` nuget-Paket.</span><span class="sxs-lookup"><span data-stu-id="f57c3-930">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="f57c3-931">Die `AddConsole` Erweiterungsmethode aufzurufen:</span><span class="sxs-lookup"><span data-stu-id="f57c3-931">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="f57c3-932">Im JavaScript-Client ist eine ähnliche `configureLogging` Methode vorhanden.</span><span class="sxs-lookup"><span data-stu-id="f57c3-932">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="f57c3-933">Geben `LogLevel` Sie einen Wert an, der die minimale Ebene der zu erstellenden Protokollmeldungen angibt.</span><span class="sxs-lookup"><span data-stu-id="f57c3-933">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="f57c3-934">Protokolle werden in das Browser Konsolenfenster geschrieben.</span><span class="sxs-lookup"><span data-stu-id="f57c3-934">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="f57c3-935">Um die Protokollierung vollständig zu deaktivieren, geben Sie `signalR.LogLevel.None` in der `configureLogging` Methode an</span><span class="sxs-lookup"><span data-stu-id="f57c3-935">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="f57c3-936">Weitere Informationen zur Protokollierung finden Sie in der [ :::no-loc(SignalR)::: Diagnose Dokumentation](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="f57c3-936">For more information on logging, see the [:::no-loc(SignalR)::: Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="f57c3-937">Der :::no-loc(SignalR)::: Java-Client verwendet die [SLF4J](https://www.slf4j.org/) -Bibliothek für die Protokollierung.</span><span class="sxs-lookup"><span data-stu-id="f57c3-937">The :::no-loc(SignalR)::: Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="f57c3-938">Dabei handelt es sich um eine allgemeine Protokollierungs-API, die es Benutzern der Bibliothek ermöglicht, ihre eigene spezifische Protokollierungs Implementierung zu wählen, indem Sie eine bestimmte Protokollierungs Abhängigkeit einbinden.</span><span class="sxs-lookup"><span data-stu-id="f57c3-938">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="f57c3-939">Der folgende Code Ausschnitt zeigt, wie `java.util.logging` mit dem Java- :::no-loc(SignalR)::: Client verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="f57c3-939">The following code snippet shows how to use `java.util.logging` with the :::no-loc(SignalR)::: Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="f57c3-940">Wenn Sie die Protokollierung in ihren Abhängigkeiten nicht konfigurieren, lädt SLF4J eine Standard Protokollierung ohne Vorgang mit folgender Warnmeldung:</span><span class="sxs-lookup"><span data-stu-id="f57c3-940">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="f57c3-941">Dies kann sicher ignoriert werden.</span><span class="sxs-lookup"><span data-stu-id="f57c3-941">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="f57c3-942">Zulässige Transporte konfigurieren</span><span class="sxs-lookup"><span data-stu-id="f57c3-942">Configure allowed transports</span></span>

<span data-ttu-id="f57c3-943">Die von verwendeten Transporte :::no-loc(SignalR)::: können im-Befehl `WithUrl` ( `withUrl` in JavaScript) konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="f57c3-943">The transports used by :::no-loc(SignalR)::: can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="f57c3-944">Ein bitweises OR der Werte von `HttpTransportType` kann verwendet werden, um den Client so einzuschränken, dass nur die angegebenen Transporte verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="f57c3-944">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="f57c3-945">Alle Transporte sind standardmäßig aktiviert.</span><span class="sxs-lookup"><span data-stu-id="f57c3-945">All transports are enabled by default.</span></span>

<span data-ttu-id="f57c3-946">Um z. b. den Server-Sent Ereignis Transport zu deaktivieren, aber websockets und lange Abruf Verbindungen zuzulassen:</span><span class="sxs-lookup"><span data-stu-id="f57c3-946">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="f57c3-947">Im JavaScript-Client werden Transporte durch Festlegen des- `transport` Felds für das Options-Objekt konfiguriert, das für bereitgestellt wird `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="f57c3-947">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="f57c3-948">In dieser Version des Java-Clients ist websockets der einzige verfügbare Transport.</span><span class="sxs-lookup"><span data-stu-id="f57c3-948">In this version of the Java client websockets is the only available transport.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="f57c3-949">Konfigurieren der bearerauthentifizierung</span><span class="sxs-lookup"><span data-stu-id="f57c3-949">Configure bearer authentication</span></span>

<span data-ttu-id="f57c3-950">Um Authentifizierungsdaten zusammen mit Anforderungen bereitzustellen :::no-loc(SignalR)::: , verwenden `AccessTokenProvider` Sie die-Option ( `accessTokenFactory` in JavaScript), um eine Funktion anzugeben, die das gewünschte Zugriffs Token zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="f57c3-950">To provide authentication data along with :::no-loc(SignalR)::: requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="f57c3-951">Im .NET-Client wird dieses Zugriffs Token als http-tokenauthentifizierungstoken (mit dem- `Authorization` Header mit dem Typ) an das Token "bearerauthentifizierung" geleitet `Bearer` .</span><span class="sxs-lookup"><span data-stu-id="f57c3-951">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="f57c3-952">Im JavaScript-Client wird das Zugriffs Token als bearertoken verwendet, **außer** in einigen Fällen, in denen Browser-APIs die Fähigkeit zum Anwenden von Headern einschränken (insbesondere in Server-Sent Ereignissen und websockets-Anforderungen).</span><span class="sxs-lookup"><span data-stu-id="f57c3-952">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="f57c3-953">In diesen Fällen wird das Zugriffs Token als Abfrage Zeichenfolgen-Wert bereitgestellt `access_token` .</span><span class="sxs-lookup"><span data-stu-id="f57c3-953">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="f57c3-954">Im .NET-Client kann die `AccessTokenProvider` Option mithilfe des Options Delegaten in angegeben werden `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="f57c3-954">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="f57c3-955">Im JavaScript-Client wird das Zugriffs Token konfiguriert, indem das- `accessTokenFactory` Feld für das Options-Objekt in festgelegt wird `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="f57c3-955">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="f57c3-956">Im :::no-loc(SignalR)::: Java-Client können Sie ein bearertoken für die Authentifizierung konfigurieren, indem Sie eine zugriffstokenfactory für " [httphubconnectionbuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)" bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="f57c3-956">In the :::no-loc(SignalR)::: Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="f57c3-957">Verwenden Sie [withaccesstokenfactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) , um eine [einzelne \<String> ](https://reactivex.io/documentation/single.html) [rxjava](https://github.com/ReactiveX/RxJava) -Instanz bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="f57c3-957">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="f57c3-958">Beim Aufrufen von " [Single.](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)Write" können Sie Logik schreiben, um Zugriffs Token für den Client zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="f57c3-958">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="f57c3-959">Konfigurieren von Timeout-und Keep-Alive-Optionen</span><span class="sxs-lookup"><span data-stu-id="f57c3-959">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="f57c3-960">Zusätzliche Optionen zum Konfigurieren von Timeout und Keep-Alive-Verhalten sind für das `HubConnection` Objekt selbst verfügbar:</span><span class="sxs-lookup"><span data-stu-id="f57c3-960">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="f57c3-961">.NET</span><span class="sxs-lookup"><span data-stu-id="f57c3-961">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="f57c3-962">Option</span><span class="sxs-lookup"><span data-stu-id="f57c3-962">Option</span></span> | <span data-ttu-id="f57c3-963">Standardwert</span><span class="sxs-lookup"><span data-stu-id="f57c3-963">Default value</span></span> | <span data-ttu-id="f57c3-964">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="f57c3-964">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="f57c3-965">30 Sekunden (30.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="f57c3-965">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="f57c3-966">Timeout für die Serveraktivität.</span><span class="sxs-lookup"><span data-stu-id="f57c3-966">Timeout for server activity.</span></span> <span data-ttu-id="f57c3-967">Wenn der Server in diesem Intervall keine Nachricht gesendet hat, betrachtet der Client den Server als getrennt und löst das `Closed` Ereignis `onclose` aus (in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="f57c3-967">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="f57c3-968">Dieser Wert muss groß genug sein, damit eine Ping-Nachricht vom Server gesendet **und** innerhalb des Timeout Intervalls vom Client empfangen wird.</span><span class="sxs-lookup"><span data-stu-id="f57c3-968">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="f57c3-969">Der empfohlene Wert ist eine Zahl, die mindestens dem Wert des Servers `KeepAliveInterval` entspricht, um Zeit für das Eintreffen von Pings zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="f57c3-969">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="f57c3-970">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="f57c3-970">15 seconds</span></span> | <span data-ttu-id="f57c3-971">Timeout für den anfänglichen Server Hand Shake.</span><span class="sxs-lookup"><span data-stu-id="f57c3-971">Timeout for initial server handshake.</span></span> <span data-ttu-id="f57c3-972">Wenn der Server in diesem Intervall keine Hand Shake Antwort sendet, bricht der Client den Handshake ab und löst das `Closed` Ereignis `onclose` aus (in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="f57c3-972">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="f57c3-973">Dies ist eine erweiterte Einstellung, die nur geändert werden sollte, wenn aufgrund schwerer Netzwerk Latenz Fehler aufgrund von Hand Shake Timeout auftreten.</span><span class="sxs-lookup"><span data-stu-id="f57c3-973">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="f57c3-974">Weitere Details zum Hand Shake Prozess finden Sie in der [ :::no-loc(SignalR)::: Hub-Protokollspezifikation](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="f57c3-974">For more detail on the handshake process, see the [:::no-loc(SignalR)::: Hub Protocol Specification](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="f57c3-975">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="f57c3-975">15 seconds</span></span> | <span data-ttu-id="f57c3-976">Bestimmt das Intervall, in dem der Client Ping-Nachrichten sendet.</span><span class="sxs-lookup"><span data-stu-id="f57c3-976">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="f57c3-977">Beim Senden von Nachrichten vom Client wird der Timer auf den Anfang des Intervalls zurückgesetzt.</span><span class="sxs-lookup"><span data-stu-id="f57c3-977">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="f57c3-978">Wenn der Client keine Nachricht in der `ClientTimeoutInterval` Gruppe auf dem Server gesendet hat, wird der Client vom Server als getrennt betrachtet.</span><span class="sxs-lookup"><span data-stu-id="f57c3-978">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="f57c3-979">Im .NET-Client werden Timeout Werte als Werte angegeben `TimeSpan` .</span><span class="sxs-lookup"><span data-stu-id="f57c3-979">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="f57c3-980">JavaScript</span><span class="sxs-lookup"><span data-stu-id="f57c3-980">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="f57c3-981">Option</span><span class="sxs-lookup"><span data-stu-id="f57c3-981">Option</span></span> | <span data-ttu-id="f57c3-982">Standardwert</span><span class="sxs-lookup"><span data-stu-id="f57c3-982">Default value</span></span> | <span data-ttu-id="f57c3-983">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="f57c3-983">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="f57c3-984">30 Sekunden (30.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="f57c3-984">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="f57c3-985">Timeout für die Serveraktivität.</span><span class="sxs-lookup"><span data-stu-id="f57c3-985">Timeout for server activity.</span></span> <span data-ttu-id="f57c3-986">Wenn der Server in diesem Intervall keine Nachricht gesendet hat, betrachtet der Client den Server als getrennt und löst das `onclose` Ereignis aus.</span><span class="sxs-lookup"><span data-stu-id="f57c3-986">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="f57c3-987">Dieser Wert muss groß genug sein, damit eine Ping-Nachricht vom Server gesendet **und** innerhalb des Timeout Intervalls vom Client empfangen wird.</span><span class="sxs-lookup"><span data-stu-id="f57c3-987">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="f57c3-988">Der empfohlene Wert ist eine Zahl, die mindestens dem Wert des Servers `KeepAliveInterval` entspricht, um Zeit für das Eintreffen von Pings zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="f57c3-988">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="f57c3-989">15 Sekunden (15.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="f57c3-989">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="f57c3-990">Bestimmt das Intervall, in dem der Client Ping-Nachrichten sendet.</span><span class="sxs-lookup"><span data-stu-id="f57c3-990">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="f57c3-991">Beim Senden von Nachrichten vom Client wird der Timer auf den Anfang des Intervalls zurückgesetzt.</span><span class="sxs-lookup"><span data-stu-id="f57c3-991">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="f57c3-992">Wenn der Client keine Nachricht in der `ClientTimeoutInterval` Gruppe auf dem Server gesendet hat, wird der Client vom Server als getrennt betrachtet.</span><span class="sxs-lookup"><span data-stu-id="f57c3-992">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="f57c3-993">Java</span><span class="sxs-lookup"><span data-stu-id="f57c3-993">Java</span></span>](#tab/java)

| <span data-ttu-id="f57c3-994">Option</span><span class="sxs-lookup"><span data-stu-id="f57c3-994">Option</span></span> | <span data-ttu-id="f57c3-995">Standardwert</span><span class="sxs-lookup"><span data-stu-id="f57c3-995">Default value</span></span> | <span data-ttu-id="f57c3-996">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="f57c3-996">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="f57c3-997">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="f57c3-997">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="f57c3-998">30 Sekunden (30.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="f57c3-998">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="f57c3-999">Timeout für die Serveraktivität.</span><span class="sxs-lookup"><span data-stu-id="f57c3-999">Timeout for server activity.</span></span> <span data-ttu-id="f57c3-1000">Wenn der Server in diesem Intervall keine Nachricht gesendet hat, betrachtet der Client den Server als getrennt und löst das `onClose` Ereignis aus.</span><span class="sxs-lookup"><span data-stu-id="f57c3-1000">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="f57c3-1001">Dieser Wert muss groß genug sein, damit eine Ping-Nachricht vom Server gesendet **und** innerhalb des Timeout Intervalls vom Client empfangen wird.</span><span class="sxs-lookup"><span data-stu-id="f57c3-1001">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="f57c3-1002">Der empfohlene Wert ist eine Zahl, die mindestens dem Wert des Servers `KeepAliveInterval` entspricht, um Zeit für das Eintreffen von Pings zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="f57c3-1002">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="f57c3-1003">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="f57c3-1003">15 seconds</span></span> | <span data-ttu-id="f57c3-1004">Timeout für den anfänglichen Server Hand Shake.</span><span class="sxs-lookup"><span data-stu-id="f57c3-1004">Timeout for initial server handshake.</span></span> <span data-ttu-id="f57c3-1005">Wenn der Server in diesem Intervall keine Hand Shake Antwort sendet, bricht der Client den Handshake ab und löst das- `onClose` Ereignis aus.</span><span class="sxs-lookup"><span data-stu-id="f57c3-1005">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="f57c3-1006">Dies ist eine erweiterte Einstellung, die nur geändert werden sollte, wenn aufgrund schwerer Netzwerk Latenz Fehler aufgrund von Hand Shake Timeout auftreten.</span><span class="sxs-lookup"><span data-stu-id="f57c3-1006">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="f57c3-1007">Weitere Details zum Hand Shake Prozess finden Sie in der [ :::no-loc(SignalR)::: Hub-Protokollspezifikation](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="f57c3-1007">For more detail on the Handshake process, see the [:::no-loc(SignalR)::: Hub Protocol Specification](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="f57c3-1008">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="f57c3-1008">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="f57c3-1009">15 Sekunden (15.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="f57c3-1009">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="f57c3-1010">Bestimmt das Intervall, in dem der Client Ping-Nachrichten sendet.</span><span class="sxs-lookup"><span data-stu-id="f57c3-1010">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="f57c3-1011">Beim Senden von Nachrichten vom Client wird der Timer auf den Anfang des Intervalls zurückgesetzt.</span><span class="sxs-lookup"><span data-stu-id="f57c3-1011">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="f57c3-1012">Wenn der Client keine Nachricht in der `ClientTimeoutInterval` Gruppe auf dem Server gesendet hat, wird der Client vom Server als getrennt betrachtet.</span><span class="sxs-lookup"><span data-stu-id="f57c3-1012">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="f57c3-1013">Konfigurieren zusätzlicher Optionen</span><span class="sxs-lookup"><span data-stu-id="f57c3-1013">Configure additional options</span></span>

<span data-ttu-id="f57c3-1014">Zusätzliche Optionen können in der- `WithUrl` Methode ( `withUrl` in JavaScript) für `HubConnectionBuilder` oder für die verschiedenen Konfigurations-APIs auf dem `HttpHubConnectionBuilder` im Java-Client konfiguriert werden:</span><span class="sxs-lookup"><span data-stu-id="f57c3-1014">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="f57c3-1015">.NET</span><span class="sxs-lookup"><span data-stu-id="f57c3-1015">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="f57c3-1016">.Net-Option</span><span class="sxs-lookup"><span data-stu-id="f57c3-1016">.NET Option</span></span> |  <span data-ttu-id="f57c3-1017">Standardwert</span><span class="sxs-lookup"><span data-stu-id="f57c3-1017">Default value</span></span> | <span data-ttu-id="f57c3-1018">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="f57c3-1018">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="f57c3-1019">Eine Funktion, die eine Zeichenfolge zurückgibt, die als bearerauthentifizierungstoken in HTTP-Anforderungen bereitgestellt</span><span class="sxs-lookup"><span data-stu-id="f57c3-1019">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="f57c3-1020">Legen Sie dies auf fest, `true` um den Aushandlungs Schritt zu überspringen</span><span class="sxs-lookup"><span data-stu-id="f57c3-1020">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="f57c3-1021">Wird **nur unterstützt, wenn der websockets-Transport der einzige aktivierte Transport ist** .</span><span class="sxs-lookup"><span data-stu-id="f57c3-1021">**Only supported when the WebSockets transport is the only enabled transport** .</span></span> <span data-ttu-id="f57c3-1022">Diese Einstellung kann nicht aktiviert werden, wenn der Azure-Dienst verwendet wird :::no-loc(SignalR)::: .</span><span class="sxs-lookup"><span data-stu-id="f57c3-1022">This setting can't be enabled when using the Azure :::no-loc(SignalR)::: Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="f57c3-1023">Leer</span><span class="sxs-lookup"><span data-stu-id="f57c3-1023">Empty</span></span> | <span data-ttu-id="f57c3-1024">Eine Auflistung von TLS-Zertifikaten, die zum Authentifizieren von Anforderungen gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="f57c3-1024">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `:::no-loc(Cookie):::s` | <span data-ttu-id="f57c3-1025">Leer</span><span class="sxs-lookup"><span data-stu-id="f57c3-1025">Empty</span></span> | <span data-ttu-id="f57c3-1026">Eine Auflistung von http- :::no-loc(cookie)::: s, die mit jeder HTTP-Anforderung gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="f57c3-1026">A collection of HTTP :::no-loc(cookie):::s to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="f57c3-1027">Leer</span><span class="sxs-lookup"><span data-stu-id="f57c3-1027">Empty</span></span> | <span data-ttu-id="f57c3-1028">Anmelde Informationen, die mit jeder HTTP-Anforderung gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="f57c3-1028">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="f57c3-1029">5 Sekunden</span><span class="sxs-lookup"><span data-stu-id="f57c3-1029">5 seconds</span></span> | <span data-ttu-id="f57c3-1030">Nur websockets.</span><span class="sxs-lookup"><span data-stu-id="f57c3-1030">WebSockets only.</span></span> <span data-ttu-id="f57c3-1031">Die maximale Zeitspanne, die der Client nach dem Schließen des Servers wartet, um die Anforderung zum Schließen zu bestätigen.</span><span class="sxs-lookup"><span data-stu-id="f57c3-1031">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="f57c3-1032">Wenn der Server die Schließung innerhalb dieses Zeitraums nicht anerkennt, trennt der Client die Verbindung.</span><span class="sxs-lookup"><span data-stu-id="f57c3-1032">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="f57c3-1033">Leer</span><span class="sxs-lookup"><span data-stu-id="f57c3-1033">Empty</span></span> | <span data-ttu-id="f57c3-1034">Eine Karte mit zusätzlichen HTTP-Headern, die mit jeder HTTP-Anforderung gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="f57c3-1034">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="f57c3-1035">Ein Delegat, der verwendet werden kann, um den `HttpMessageHandler` zum Senden von HTTP-Anforderungen verwendeten zu konfigurieren oder zu ersetzen.</span><span class="sxs-lookup"><span data-stu-id="f57c3-1035">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="f57c3-1036">Wird nicht für WebSocket-Verbindungen verwendet.</span><span class="sxs-lookup"><span data-stu-id="f57c3-1036">Not used for WebSocket connections.</span></span> <span data-ttu-id="f57c3-1037">Dieser Delegat muss einen Wert zurückgeben, der nicht NULL ist, und erhält den Standardwert als Parameter.</span><span class="sxs-lookup"><span data-stu-id="f57c3-1037">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="f57c3-1038">Ändern Sie die Einstellungen für diesen Standardwert, und geben Sie ihn zurück, oder geben Sie eine neue `HttpMessageHandler` Instanz zurück.</span><span class="sxs-lookup"><span data-stu-id="f57c3-1038">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="f57c3-1039">**Wenn Sie den Handler ersetzen, stellen Sie sicher, dass die Einstellungen, die Sie beibehalten möchten, vom bereitgestellten Handler kopiert werden. Andernfalls gelten die konfigurierten Optionen (z. b. :::no-loc(Cookie)::: s und Header) nicht für den neuen Handler.**</span><span class="sxs-lookup"><span data-stu-id="f57c3-1039">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as :::no-loc(Cookie):::s and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="f57c3-1040">Ein HTTP-Proxy, der beim Senden von HTTP-Anforderungen verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="f57c3-1040">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="f57c3-1041">Legen Sie diesen booleschen Wert fest, um die Standard Anmelde Informationen für http-und websockets-Anforderungen zu senden</span><span class="sxs-lookup"><span data-stu-id="f57c3-1041">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="f57c3-1042">Dies ermöglicht die Verwendung der Windows-Authentifizierung.</span><span class="sxs-lookup"><span data-stu-id="f57c3-1042">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="f57c3-1043">Ein Delegat, der zum Konfigurieren zusätzlicher WebSocket-Optionen verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="f57c3-1043">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="f57c3-1044">Empfängt eine Instanz von [clientwebsocketoptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) , die zum Konfigurieren der Optionen verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="f57c3-1044">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="f57c3-1045">JavaScript</span><span class="sxs-lookup"><span data-stu-id="f57c3-1045">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="f57c3-1046">JavaScript-Option</span><span class="sxs-lookup"><span data-stu-id="f57c3-1046">JavaScript Option</span></span> | <span data-ttu-id="f57c3-1047">Standardwert</span><span class="sxs-lookup"><span data-stu-id="f57c3-1047">Default Value</span></span> | <span data-ttu-id="f57c3-1048">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="f57c3-1048">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="f57c3-1049">Eine Funktion, die eine Zeichenfolge zurückgibt, die als bearerauthentifizierungstoken in HTTP-Anforderungen bereitgestellt</span><span class="sxs-lookup"><span data-stu-id="f57c3-1049">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `transport` | `null` | <span data-ttu-id="f57c3-1050">Ein- <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType> Wert, der den für die Verbindung zu verwendenden Transport angibt.</span><span class="sxs-lookup"><span data-stu-id="f57c3-1050">An <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType> value specifying the transport to use for the connection.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="f57c3-1051">Legen Sie auf fest, `true` um die Bytes/Zeichen der vom Client gesendeten und empfangenen Nachrichten zu protokollieren.</span><span class="sxs-lookup"><span data-stu-id="f57c3-1051">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="f57c3-1052">Legen Sie dies auf fest, `true` um den Aushandlungs Schritt zu überspringen</span><span class="sxs-lookup"><span data-stu-id="f57c3-1052">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="f57c3-1053">Wird **nur unterstützt, wenn der websockets-Transport der einzige aktivierte Transport ist** .</span><span class="sxs-lookup"><span data-stu-id="f57c3-1053">**Only supported when the WebSockets transport is the only enabled transport** .</span></span> <span data-ttu-id="f57c3-1054">Diese Einstellung kann nicht aktiviert werden, wenn der Azure-Dienst verwendet wird :::no-loc(SignalR)::: .</span><span class="sxs-lookup"><span data-stu-id="f57c3-1054">This setting can't be enabled when using the Azure :::no-loc(SignalR)::: Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="f57c3-1055">Java</span><span class="sxs-lookup"><span data-stu-id="f57c3-1055">Java</span></span>](#tab/java)

| <span data-ttu-id="f57c3-1056">Java-Option</span><span class="sxs-lookup"><span data-stu-id="f57c3-1056">Java Option</span></span> | <span data-ttu-id="f57c3-1057">Standardwert</span><span class="sxs-lookup"><span data-stu-id="f57c3-1057">Default Value</span></span> | <span data-ttu-id="f57c3-1058">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="f57c3-1058">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="f57c3-1059">Eine Funktion, die eine Zeichenfolge zurückgibt, die als bearerauthentifizierungstoken in HTTP-Anforderungen bereitgestellt</span><span class="sxs-lookup"><span data-stu-id="f57c3-1059">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="f57c3-1060">Legen Sie dies auf fest, `true` um den Aushandlungs Schritt zu überspringen</span><span class="sxs-lookup"><span data-stu-id="f57c3-1060">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="f57c3-1061">Wird **nur unterstützt, wenn der websockets-Transport der einzige aktivierte Transport ist** .</span><span class="sxs-lookup"><span data-stu-id="f57c3-1061">**Only supported when the WebSockets transport is the only enabled transport** .</span></span> <span data-ttu-id="f57c3-1062">Diese Einstellung kann nicht aktiviert werden, wenn der Azure-Dienst verwendet wird :::no-loc(SignalR)::: .</span><span class="sxs-lookup"><span data-stu-id="f57c3-1062">This setting can't be enabled when using the Azure :::no-loc(SignalR)::: Service.</span></span> |
| <span data-ttu-id="f57c3-1063">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="f57c3-1063">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="f57c3-1064">Leer</span><span class="sxs-lookup"><span data-stu-id="f57c3-1064">Empty</span></span> | <span data-ttu-id="f57c3-1065">Eine Karte mit zusätzlichen HTTP-Headern, die mit jeder HTTP-Anforderung gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="f57c3-1065">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="f57c3-1066">Im .NET-Client können diese Optionen durch den für bereitgestellten Options Delegaten geändert werden `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="f57c3-1066">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.:::no-loc(Cookie):::s.Add(new :::no-loc(Cookie):::(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="f57c3-1067">Im JavaScript-Client können diese Optionen in einem JavaScript-Objekt bereitgestellt werden, das für Folgendes bereitgestellt wird `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="f57c3-1067">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="f57c3-1068">Im Java-Client können diese Optionen mit den Methoden auf der konfiguriert werden, die `HttpHubConnectionBuilder` von der `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="f57c3-1068">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="f57c3-1069">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="f57c3-1069">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="< aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="f57c3-1070">JSON/messagepack-Serialisierungsoptionen</span><span class="sxs-lookup"><span data-stu-id="f57c3-1070">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="f57c3-1071">ASP.net Core :::no-loc(SignalR)::: unterstützt zwei Protokolle zum Codieren von Nachrichten: [JSON](https://www.json.org/) und [messagepack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="f57c3-1071">ASP.NET Core :::no-loc(SignalR)::: supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="f57c3-1072">Jedes Protokoll verfügt über Konfigurationsoptionen für die Serialisierung.</span><span class="sxs-lookup"><span data-stu-id="f57c3-1072">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="f57c3-1073">Die JSON-Serialisierung kann mithilfe der [addjsonprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) -Erweiterungsmethode auf dem Server konfiguriert werden, die nach [dem :::no-loc(SignalR)::: Hinzufügen](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in der Methode hinzugefügt werden kann `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="f57c3-1073">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [Add:::no-loc(SignalR):::](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="f57c3-1074">Die- `AddJsonProtocol` Methode nimmt einen Delegaten an, der ein- `options` Objekt empfängt.</span><span class="sxs-lookup"><span data-stu-id="f57c3-1074">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="f57c3-1075">Die [payloadserializersettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) -Eigenschaft für dieses Objekt ist ein JSON.net- `JsonSerializerSettings` Objekt, das zum Konfigurieren der Serialisierung von Argumenten und Rückgabe Werten verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="f57c3-1075">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="f57c3-1076">Weitere Informationen finden Sie in der [JSON.NET-Dokumentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span><span class="sxs-lookup"><span data-stu-id="f57c3-1076">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="f57c3-1077">Verwenden Sie beispielsweise den folgenden Code in, um das Serialisierungsprogramm so zu konfigurieren, dass "PascalCase"-Eigenschaftsnamen anstelle der Standardnamen "CamelCase" verwendet werden `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="f57c3-1077">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.Add:::no-loc(SignalR):::()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="f57c3-1078">Im .NET-Client ist dieselbe `AddJsonProtocol` Erweiterungsmethode auf [hubconnectionbuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)vorhanden.</span><span class="sxs-lookup"><span data-stu-id="f57c3-1078">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="f57c3-1079">Der `Microsoft.Extensions.DependencyInjection` Namespace muss importiert werden, um die Erweiterungsmethode aufzulösen:</span><span class="sxs-lookup"><span data-stu-id="f57c3-1079">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="f57c3-1080">Zurzeit ist es nicht möglich, JSON-Serialisierung im JavaScript-Client zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="f57c3-1080">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="f57c3-1081">Messagepack-Serialisierungsoptionen</span><span class="sxs-lookup"><span data-stu-id="f57c3-1081">MessagePack serialization options</span></span>

<span data-ttu-id="f57c3-1082">Die messagepack-Serialisierung kann konfiguriert werden, indem ein Delegat für den [addmessagepackprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) -Aufruf bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="f57c3-1082">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="f57c3-1083">Weitere Informationen finden Sie [unter :::no-loc(SignalR)::: messagepack in](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="f57c3-1083">See [MessagePack in :::no-loc(SignalR):::](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="f57c3-1084">Zurzeit ist es nicht möglich, die messagepack-Serialisierung im JavaScript-Client zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="f57c3-1084">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="f57c3-1085">Konfigurieren von Serveroptionen</span><span class="sxs-lookup"><span data-stu-id="f57c3-1085">Configure server options</span></span>

<span data-ttu-id="f57c3-1086">In der folgenden Tabelle werden die Optionen zum Konfigurieren von :::no-loc(SignalR)::: Hubs beschrieben:</span><span class="sxs-lookup"><span data-stu-id="f57c3-1086">The following table describes options for configuring :::no-loc(SignalR)::: hubs:</span></span>

| <span data-ttu-id="f57c3-1087">Option</span><span class="sxs-lookup"><span data-stu-id="f57c3-1087">Option</span></span> | <span data-ttu-id="f57c3-1088">Standardwert</span><span class="sxs-lookup"><span data-stu-id="f57c3-1088">Default Value</span></span> | <span data-ttu-id="f57c3-1089">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="f57c3-1089">Description</span></span> |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | <span data-ttu-id="f57c3-1090">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="f57c3-1090">15 seconds</span></span> | <span data-ttu-id="f57c3-1091">Wenn der Client innerhalb dieses Zeitraums keine anfängliche Hand Shake Nachricht sendet, wird die Verbindung geschlossen.</span><span class="sxs-lookup"><span data-stu-id="f57c3-1091">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="f57c3-1092">Dies ist eine erweiterte Einstellung, die nur geändert werden sollte, wenn aufgrund schwerer Netzwerk Latenz Fehler aufgrund von Hand Shake Timeout auftreten.</span><span class="sxs-lookup"><span data-stu-id="f57c3-1092">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="f57c3-1093">Weitere Details zum Hand Shake Prozess finden Sie in der [ :::no-loc(SignalR)::: Hub-Protokollspezifikation](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="f57c3-1093">For more detail on the handshake process, see the [:::no-loc(SignalR)::: Hub Protocol Specification](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="f57c3-1094">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="f57c3-1094">15 seconds</span></span> | <span data-ttu-id="f57c3-1095">Wenn der Server innerhalb dieses Intervalls keine Nachricht gesendet hat, wird automatisch eine Ping-Nachricht gesendet, um die Verbindung offen zu halten.</span><span class="sxs-lookup"><span data-stu-id="f57c3-1095">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="f57c3-1096">Ändern Sie `KeepAliveInterval` die `ServerTimeout` / `serverTimeoutInMilliseconds` Einstellung auf dem Client, wenn Sie sich ändern.</span><span class="sxs-lookup"><span data-stu-id="f57c3-1096">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="f57c3-1097">Der empfohlene `ServerTimeout` / `serverTimeoutInMilliseconds` Wert ist Double- `KeepAliveInterval` Wert.</span><span class="sxs-lookup"><span data-stu-id="f57c3-1097">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="f57c3-1098">Alle installierten Protokolle</span><span class="sxs-lookup"><span data-stu-id="f57c3-1098">All installed protocols</span></span> | <span data-ttu-id="f57c3-1099">Protokolle, die von diesem Hub unterstützt werden.</span><span class="sxs-lookup"><span data-stu-id="f57c3-1099">Protocols supported by this hub.</span></span> <span data-ttu-id="f57c3-1100">Standardmäßig sind alle auf dem Server registrierten Protokolle zulässig, aber Protokolle können aus dieser Liste entfernt werden, um bestimmte Protokolle für einzelne Hubs zu deaktivieren.</span><span class="sxs-lookup"><span data-stu-id="f57c3-1100">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="f57c3-1101">Wenn der Wert `true` ist, werden ausführliche Ausnahme Meldungen an Clients zurückgegeben, wenn eine Ausnahme in einer Hub-Methode ausgelöst wird.</span><span class="sxs-lookup"><span data-stu-id="f57c3-1101">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="f57c3-1102">Der Standardwert ist `false` , da diese Ausnahme Meldungen vertrauliche Informationen enthalten können.</span><span class="sxs-lookup"><span data-stu-id="f57c3-1102">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="f57c3-1103">Optionen können für alle Hubs konfiguriert werden, indem ein Options Delegat für den Aufruf in bereitgestellt wird `Add:::no-loc(SignalR):::` `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="f57c3-1103">Options can be configured for all hubs by providing an options delegate to the `Add:::no-loc(SignalR):::` call in `Startup.ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Add:::no-loc(SignalR):::(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

<span data-ttu-id="f57c3-1104">Optionen für einen einzelnen Hub überschreiben die in bereitgestellten globalen Optionen `Add:::no-loc(SignalR):::` und können mithilfe von konfiguriert werden <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(SignalR):::DependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="f57c3-1104">Options for a single hub override the global options provided in `Add:::no-loc(SignalR):::` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(SignalR):::DependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.Add:::no-loc(SignalR):::().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="f57c3-1105">Erweiterte http-Konfigurationsoptionen</span><span class="sxs-lookup"><span data-stu-id="f57c3-1105">Advanced HTTP configuration options</span></span>

<span data-ttu-id="f57c3-1106">Verwenden `HttpConnectionDispatcherOptions` Sie, um erweiterte Einstellungen im Zusammenhang mit Transporte und der Speicherpuffer Verwaltung zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="f57c3-1106">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="f57c3-1107">Diese Optionen werden konfiguriert, indem ein [Delegat an \<T> maphub](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in übergeben wird `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="f57c3-1107">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.Use:::no-loc(SignalR):::((configure) =>
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

<span data-ttu-id="f57c3-1108">In der folgenden Tabelle werden die Optionen zum Konfigurieren der :::no-loc(SignalR)::: erweiterten http-Optionen von ASP.net Core beschrieben:</span><span class="sxs-lookup"><span data-stu-id="f57c3-1108">The following table describes options for configuring ASP.NET Core :::no-loc(SignalR):::'s advanced HTTP options:</span></span>

| <span data-ttu-id="f57c3-1109">Option</span><span class="sxs-lookup"><span data-stu-id="f57c3-1109">Option</span></span> | <span data-ttu-id="f57c3-1110">Standardwert</span><span class="sxs-lookup"><span data-stu-id="f57c3-1110">Default Value</span></span> | <span data-ttu-id="f57c3-1111">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="f57c3-1111">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="f57c3-1112">32 KB</span><span class="sxs-lookup"><span data-stu-id="f57c3-1112">32 KB</span></span> | <span data-ttu-id="f57c3-1113">Die maximale Anzahl von Bytes, die vom Client empfangen werden, der vom Server gepuffert wird.</span><span class="sxs-lookup"><span data-stu-id="f57c3-1113">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="f57c3-1114">Eine Erhöhung dieses Werts ermöglicht es dem Server, größere Nachrichten zu empfangen, kann sich jedoch negativ auf den Arbeitsspeicherverbrauch auswirken.</span><span class="sxs-lookup"><span data-stu-id="f57c3-1114">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="f57c3-1115">Daten, die automatisch von den `Authorize` auf die Hub-Klasse angewendeten Attributen gesammelt werden.</span><span class="sxs-lookup"><span data-stu-id="f57c3-1115">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="f57c3-1116">Eine Liste von [iautorizedata](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) -Objekten, die verwendet werden, um zu bestimmen, ob ein Client für die Verbindung mit dem Hub autorisiert ist.</span><span class="sxs-lookup"><span data-stu-id="f57c3-1116">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="f57c3-1117">32 KB</span><span class="sxs-lookup"><span data-stu-id="f57c3-1117">32 KB</span></span> | <span data-ttu-id="f57c3-1118">Die maximale Anzahl von Bytes, die von der APP gesendet werden, die der Server puffert.</span><span class="sxs-lookup"><span data-stu-id="f57c3-1118">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="f57c3-1119">Wenn Sie diesen Wert erhöhen, kann der Server größere Nachrichten senden, kann sich jedoch negativ auf die Arbeitsspeicher Auslastung auswirken.</span><span class="sxs-lookup"><span data-stu-id="f57c3-1119">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="f57c3-1120">Alle Transporte sind aktiviert.</span><span class="sxs-lookup"><span data-stu-id="f57c3-1120">All Transports are enabled.</span></span> | <span data-ttu-id="f57c3-1121">Ein Bitflags-Enumeration von `HttpTransportType` Werten, die die Transporte einschränken können, die ein Client für die Verbindungs Herstellung verwenden kann.</span><span class="sxs-lookup"><span data-stu-id="f57c3-1121">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="f57c3-1122">Siehe unten.</span><span class="sxs-lookup"><span data-stu-id="f57c3-1122">See below.</span></span> | <span data-ttu-id="f57c3-1123">Zusätzliche Optionen, die für den langen Abruf Transport spezifisch sind.</span><span class="sxs-lookup"><span data-stu-id="f57c3-1123">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="f57c3-1124">Siehe unten.</span><span class="sxs-lookup"><span data-stu-id="f57c3-1124">See below.</span></span> | <span data-ttu-id="f57c3-1125">Zusätzliche Optionen für den websockets-Transport.</span><span class="sxs-lookup"><span data-stu-id="f57c3-1125">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="f57c3-1126">Der lange Abruf Transport bietet zusätzliche Optionen, die mit der-Eigenschaft konfiguriert werden können `LongPolling` :</span><span class="sxs-lookup"><span data-stu-id="f57c3-1126">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="f57c3-1127">Option</span><span class="sxs-lookup"><span data-stu-id="f57c3-1127">Option</span></span> | <span data-ttu-id="f57c3-1128">Standardwert</span><span class="sxs-lookup"><span data-stu-id="f57c3-1128">Default Value</span></span> | <span data-ttu-id="f57c3-1129">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="f57c3-1129">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="f57c3-1130">90 Sekunden</span><span class="sxs-lookup"><span data-stu-id="f57c3-1130">90 seconds</span></span> | <span data-ttu-id="f57c3-1131">Die maximale Zeitspanne, die der Server wartet, bis eine Nachricht an den Client gesendet wird, bevor eine einzelne Abruf Anforderung beendet wird.</span><span class="sxs-lookup"><span data-stu-id="f57c3-1131">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="f57c3-1132">Das verringern dieses Werts bewirkt, dass der Client neue Abruf Anforderungen häufiger ausgibt.</span><span class="sxs-lookup"><span data-stu-id="f57c3-1132">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="f57c3-1133">Der WebSocket-Transport verfügt über zusätzliche Optionen, die mit der-Eigenschaft konfiguriert werden können `WebSockets` :</span><span class="sxs-lookup"><span data-stu-id="f57c3-1133">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="f57c3-1134">Option</span><span class="sxs-lookup"><span data-stu-id="f57c3-1134">Option</span></span> | <span data-ttu-id="f57c3-1135">Standardwert</span><span class="sxs-lookup"><span data-stu-id="f57c3-1135">Default Value</span></span> | <span data-ttu-id="f57c3-1136">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="f57c3-1136">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="f57c3-1137">5 Sekunden</span><span class="sxs-lookup"><span data-stu-id="f57c3-1137">5 seconds</span></span> | <span data-ttu-id="f57c3-1138">Wenn der Client nach dem Schließen des Servers nicht innerhalb dieses Zeitraums geschlossen werden kann, wird die Verbindung beendet.</span><span class="sxs-lookup"><span data-stu-id="f57c3-1138">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="f57c3-1139">Ein Delegat, der verwendet werden kann, um den- `Sec-WebSocket-Protocol` Header auf einen benutzerdefinierten Wert festzulegen.</span><span class="sxs-lookup"><span data-stu-id="f57c3-1139">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="f57c3-1140">Der Delegat empfängt die vom Client angeforderten Werte als Eingabe und erwartet, dass der gewünschte Wert zurückgegeben wird.</span><span class="sxs-lookup"><span data-stu-id="f57c3-1140">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="f57c3-1141">Konfigurieren von Clientoptionen</span><span class="sxs-lookup"><span data-stu-id="f57c3-1141">Configure client options</span></span>

<span data-ttu-id="f57c3-1142">Client Optionen können für den Typ konfiguriert werden `HubConnectionBuilder` (verfügbar in den .net-und JavaScript-Clients).</span><span class="sxs-lookup"><span data-stu-id="f57c3-1142">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="f57c3-1143">Sie steht auch im Java-Client zur Verfügung, aber die `HttpHubConnectionBuilder` Unterklasse enthält die Generator-Konfigurationsoptionen sowie die `HubConnection` selbst.</span><span class="sxs-lookup"><span data-stu-id="f57c3-1143">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="f57c3-1144">Konfigurieren der Protokollierung</span><span class="sxs-lookup"><span data-stu-id="f57c3-1144">Configure logging</span></span>

<span data-ttu-id="f57c3-1145">Die Protokollierung wird mithilfe der-Methode im .NET-Client konfiguriert `ConfigureLogging` .</span><span class="sxs-lookup"><span data-stu-id="f57c3-1145">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="f57c3-1146">Protokollierungs Anbieter und Filter können auf die gleiche Weise wie auf dem Server registriert werden.</span><span class="sxs-lookup"><span data-stu-id="f57c3-1146">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="f57c3-1147">Weitere Informationen finden Sie in der Dokumentation zur [Protokollierung ASP.net Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="f57c3-1147">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="f57c3-1148">Um Protokollierungs Anbieter zu registrieren, müssen Sie die erforderlichen Pakete installieren.</span><span class="sxs-lookup"><span data-stu-id="f57c3-1148">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="f57c3-1149">Eine vollständige Liste finden Sie im Abschnitt [integrierte Protokollierungs Anbieter](xref:fundamentals/logging/index#built-in-logging-providers) der Dokumentation.</span><span class="sxs-lookup"><span data-stu-id="f57c3-1149">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="f57c3-1150">Um z. b. die Konsolen Protokollierung zu aktivieren, installieren Sie das `Microsoft.Extensions.Logging.Console` nuget-Paket.</span><span class="sxs-lookup"><span data-stu-id="f57c3-1150">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="f57c3-1151">Die `AddConsole` Erweiterungsmethode aufzurufen:</span><span class="sxs-lookup"><span data-stu-id="f57c3-1151">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="f57c3-1152">Im JavaScript-Client ist eine ähnliche `configureLogging` Methode vorhanden.</span><span class="sxs-lookup"><span data-stu-id="f57c3-1152">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="f57c3-1153">Geben `LogLevel` Sie einen Wert an, der die minimale Ebene der zu erstellenden Protokollmeldungen angibt.</span><span class="sxs-lookup"><span data-stu-id="f57c3-1153">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="f57c3-1154">Protokolle werden in das Browser Konsolenfenster geschrieben.</span><span class="sxs-lookup"><span data-stu-id="f57c3-1154">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="f57c3-1155">Um die Protokollierung vollständig zu deaktivieren, geben Sie `signalR.LogLevel.None` in der `configureLogging` Methode an</span><span class="sxs-lookup"><span data-stu-id="f57c3-1155">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="f57c3-1156">Weitere Informationen zur Protokollierung finden Sie in der [ :::no-loc(SignalR)::: Diagnose Dokumentation](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="f57c3-1156">For more information on logging, see the [:::no-loc(SignalR)::: Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="f57c3-1157">Der :::no-loc(SignalR)::: Java-Client verwendet die [SLF4J](https://www.slf4j.org/) -Bibliothek für die Protokollierung.</span><span class="sxs-lookup"><span data-stu-id="f57c3-1157">The :::no-loc(SignalR)::: Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="f57c3-1158">Dabei handelt es sich um eine allgemeine Protokollierungs-API, die es Benutzern der Bibliothek ermöglicht, ihre eigene spezifische Protokollierungs Implementierung zu wählen, indem Sie eine bestimmte Protokollierungs Abhängigkeit einbinden.</span><span class="sxs-lookup"><span data-stu-id="f57c3-1158">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="f57c3-1159">Der folgende Code Ausschnitt zeigt, wie `java.util.logging` mit dem Java- :::no-loc(SignalR)::: Client verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="f57c3-1159">The following code snippet shows how to use `java.util.logging` with the :::no-loc(SignalR)::: Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="f57c3-1160">Wenn Sie die Protokollierung in ihren Abhängigkeiten nicht konfigurieren, lädt SLF4J eine Standard Protokollierung ohne Vorgang mit folgender Warnmeldung:</span><span class="sxs-lookup"><span data-stu-id="f57c3-1160">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="f57c3-1161">Dies kann sicher ignoriert werden.</span><span class="sxs-lookup"><span data-stu-id="f57c3-1161">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="f57c3-1162">Zulässige Transporte konfigurieren</span><span class="sxs-lookup"><span data-stu-id="f57c3-1162">Configure allowed transports</span></span>

<span data-ttu-id="f57c3-1163">Die von verwendeten Transporte :::no-loc(SignalR)::: können im-Befehl `WithUrl` ( `withUrl` in JavaScript) konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="f57c3-1163">The transports used by :::no-loc(SignalR)::: can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="f57c3-1164">Ein bitweises OR der Werte von `HttpTransportType` kann verwendet werden, um den Client so einzuschränken, dass nur die angegebenen Transporte verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="f57c3-1164">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="f57c3-1165">Alle Transporte sind standardmäßig aktiviert.</span><span class="sxs-lookup"><span data-stu-id="f57c3-1165">All transports are enabled by default.</span></span>

<span data-ttu-id="f57c3-1166">Um z. b. den Server-Sent Ereignis Transport zu deaktivieren, aber websockets und lange Abruf Verbindungen zuzulassen:</span><span class="sxs-lookup"><span data-stu-id="f57c3-1166">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="f57c3-1167">Im JavaScript-Client werden Transporte durch Festlegen des- `transport` Felds für das Options-Objekt konfiguriert, das für bereitgestellt wird `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="f57c3-1167">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

### <a name="configure-bearer-authentication"></a><span data-ttu-id="f57c3-1168">Konfigurieren der bearerauthentifizierung</span><span class="sxs-lookup"><span data-stu-id="f57c3-1168">Configure bearer authentication</span></span>

<span data-ttu-id="f57c3-1169">Um Authentifizierungsdaten zusammen mit Anforderungen bereitzustellen :::no-loc(SignalR)::: , verwenden `AccessTokenProvider` Sie die-Option ( `accessTokenFactory` in JavaScript), um eine Funktion anzugeben, die das gewünschte Zugriffs Token zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="f57c3-1169">To provide authentication data along with :::no-loc(SignalR)::: requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="f57c3-1170">Im .NET-Client wird dieses Zugriffs Token als http-tokenauthentifizierungstoken (mit dem- `Authorization` Header mit dem Typ) an das Token "bearerauthentifizierung" geleitet `Bearer` .</span><span class="sxs-lookup"><span data-stu-id="f57c3-1170">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="f57c3-1171">Im JavaScript-Client wird das Zugriffs Token als bearertoken verwendet, **außer** in einigen Fällen, in denen Browser-APIs die Fähigkeit zum Anwenden von Headern einschränken (insbesondere in Server-Sent Ereignissen und websockets-Anforderungen).</span><span class="sxs-lookup"><span data-stu-id="f57c3-1171">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="f57c3-1172">In diesen Fällen wird das Zugriffs Token als Abfrage Zeichenfolgen-Wert bereitgestellt `access_token` .</span><span class="sxs-lookup"><span data-stu-id="f57c3-1172">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="f57c3-1173">Im .NET-Client kann die `AccessTokenProvider` Option mithilfe des Options Delegaten in angegeben werden `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="f57c3-1173">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="f57c3-1174">Im JavaScript-Client wird das Zugriffs Token konfiguriert, indem das- `accessTokenFactory` Feld für das Options-Objekt in festgelegt wird `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="f57c3-1174">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="f57c3-1175">Im :::no-loc(SignalR)::: Java-Client können Sie ein bearertoken für die Authentifizierung konfigurieren, indem Sie eine zugriffstokenfactory für " [httphubconnectionbuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)" bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="f57c3-1175">In the :::no-loc(SignalR)::: Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="f57c3-1176">Verwenden Sie [withaccesstokenfactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) , um eine [einzelne \<String> ](https://reactivex.io/documentation/single.html) [rxjava](https://github.com/ReactiveX/RxJava) -Instanz bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="f57c3-1176">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="f57c3-1177">Beim Aufrufen von " [Single.](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)Write" können Sie Logik schreiben, um Zugriffs Token für den Client zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="f57c3-1177">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="f57c3-1178">Konfigurieren von Timeout-und Keep-Alive-Optionen</span><span class="sxs-lookup"><span data-stu-id="f57c3-1178">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="f57c3-1179">Zusätzliche Optionen zum Konfigurieren von Timeout und Keep-Alive-Verhalten sind für das `HubConnection` Objekt selbst verfügbar:</span><span class="sxs-lookup"><span data-stu-id="f57c3-1179">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="f57c3-1180">.NET</span><span class="sxs-lookup"><span data-stu-id="f57c3-1180">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="f57c3-1181">Option</span><span class="sxs-lookup"><span data-stu-id="f57c3-1181">Option</span></span> | <span data-ttu-id="f57c3-1182">Standardwert</span><span class="sxs-lookup"><span data-stu-id="f57c3-1182">Default value</span></span> | <span data-ttu-id="f57c3-1183">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="f57c3-1183">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="f57c3-1184">30 Sekunden (30.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="f57c3-1184">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="f57c3-1185">Timeout für die Serveraktivität.</span><span class="sxs-lookup"><span data-stu-id="f57c3-1185">Timeout for server activity.</span></span> <span data-ttu-id="f57c3-1186">Wenn der Server in diesem Intervall keine Nachricht gesendet hat, betrachtet der Client den Server als getrennt und löst das `Closed` Ereignis `onclose` aus (in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="f57c3-1186">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="f57c3-1187">Dieser Wert muss groß genug sein, damit eine Ping-Nachricht vom Server gesendet **und** innerhalb des Timeout Intervalls vom Client empfangen wird.</span><span class="sxs-lookup"><span data-stu-id="f57c3-1187">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="f57c3-1188">Der empfohlene Wert ist eine Zahl, die mindestens dem Wert des Servers `KeepAliveInterval` entspricht, um Zeit für das Eintreffen von Pings zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="f57c3-1188">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="f57c3-1189">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="f57c3-1189">15 seconds</span></span> | <span data-ttu-id="f57c3-1190">Timeout für den anfänglichen Server Hand Shake.</span><span class="sxs-lookup"><span data-stu-id="f57c3-1190">Timeout for initial server handshake.</span></span> <span data-ttu-id="f57c3-1191">Wenn der Server in diesem Intervall keine Hand Shake Antwort sendet, bricht der Client den Handshake ab und löst das `Closed` Ereignis `onclose` aus (in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="f57c3-1191">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="f57c3-1192">Dies ist eine erweiterte Einstellung, die nur geändert werden sollte, wenn aufgrund schwerer Netzwerk Latenz Fehler aufgrund von Hand Shake Timeout auftreten.</span><span class="sxs-lookup"><span data-stu-id="f57c3-1192">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="f57c3-1193">Weitere Details zum Hand Shake Prozess finden Sie in der [ :::no-loc(SignalR)::: Hub-Protokollspezifikation](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="f57c3-1193">For more detail on the handshake process, see the [:::no-loc(SignalR)::: Hub Protocol Specification](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md).</span></span> |

<span data-ttu-id="f57c3-1194">Im .NET-Client werden Timeout Werte als Werte angegeben `TimeSpan` .</span><span class="sxs-lookup"><span data-stu-id="f57c3-1194">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="f57c3-1195">JavaScript</span><span class="sxs-lookup"><span data-stu-id="f57c3-1195">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="f57c3-1196">Option</span><span class="sxs-lookup"><span data-stu-id="f57c3-1196">Option</span></span> | <span data-ttu-id="f57c3-1197">Standardwert</span><span class="sxs-lookup"><span data-stu-id="f57c3-1197">Default value</span></span> | <span data-ttu-id="f57c3-1198">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="f57c3-1198">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="f57c3-1199">30 Sekunden (30.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="f57c3-1199">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="f57c3-1200">Timeout für die Serveraktivität.</span><span class="sxs-lookup"><span data-stu-id="f57c3-1200">Timeout for server activity.</span></span> <span data-ttu-id="f57c3-1201">Wenn der Server in diesem Intervall keine Nachricht gesendet hat, betrachtet der Client den Server als getrennt und löst das `onclose` Ereignis aus.</span><span class="sxs-lookup"><span data-stu-id="f57c3-1201">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="f57c3-1202">Dieser Wert muss groß genug sein, damit eine Ping-Nachricht vom Server gesendet **und** innerhalb des Timeout Intervalls vom Client empfangen wird.</span><span class="sxs-lookup"><span data-stu-id="f57c3-1202">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="f57c3-1203">Der empfohlene Wert ist eine Zahl, die mindestens dem Wert des Servers `KeepAliveInterval` entspricht, um Zeit für das Eintreffen von Pings zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="f57c3-1203">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |

# <a name="java"></a>[<span data-ttu-id="f57c3-1204">Java</span><span class="sxs-lookup"><span data-stu-id="f57c3-1204">Java</span></span>](#tab/java)

| <span data-ttu-id="f57c3-1205">Option</span><span class="sxs-lookup"><span data-stu-id="f57c3-1205">Option</span></span> | <span data-ttu-id="f57c3-1206">Standardwert</span><span class="sxs-lookup"><span data-stu-id="f57c3-1206">Default value</span></span> | <span data-ttu-id="f57c3-1207">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="f57c3-1207">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="f57c3-1208">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="f57c3-1208">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="f57c3-1209">30 Sekunden (30.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="f57c3-1209">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="f57c3-1210">Timeout für die Serveraktivität.</span><span class="sxs-lookup"><span data-stu-id="f57c3-1210">Timeout for server activity.</span></span> <span data-ttu-id="f57c3-1211">Wenn der Server in diesem Intervall keine Nachricht gesendet hat, betrachtet der Client den Server als getrennt und löst das `onClose` Ereignis aus.</span><span class="sxs-lookup"><span data-stu-id="f57c3-1211">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="f57c3-1212">Dieser Wert muss groß genug sein, damit eine Ping-Nachricht vom Server gesendet **und** innerhalb des Timeout Intervalls vom Client empfangen wird.</span><span class="sxs-lookup"><span data-stu-id="f57c3-1212">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="f57c3-1213">Der empfohlene Wert ist eine Zahl, die mindestens dem Wert des Servers entspricht `KeepAliveInterval` , um Zeit für das Eintreffen von Pings zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="f57c3-1213">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="f57c3-1214">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="f57c3-1214">15 seconds</span></span> | <span data-ttu-id="f57c3-1215">Timeout für den anfänglichen Server Hand Shake.</span><span class="sxs-lookup"><span data-stu-id="f57c3-1215">Timeout for initial server handshake.</span></span> <span data-ttu-id="f57c3-1216">Wenn der Server in diesem Intervall keine Hand Shake Antwort sendet, bricht der Client den Handshake ab und löst das- `onClose` Ereignis aus.</span><span class="sxs-lookup"><span data-stu-id="f57c3-1216">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="f57c3-1217">Dies ist eine erweiterte Einstellung, die nur geändert werden sollte, wenn aufgrund schwerer Netzwerk Latenz Fehler aufgrund von Hand Shake Timeout auftreten.</span><span class="sxs-lookup"><span data-stu-id="f57c3-1217">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="f57c3-1218">Weitere Details zum Hand Shake Prozess finden Sie in der [ :::no-loc(SignalR)::: Hub-Protokollspezifikation](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="f57c3-1218">For more detail on the handshake process, see the [:::no-loc(SignalR)::: Hub Protocol Specification](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md).</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="f57c3-1219">Konfigurieren zusätzlicher Optionen</span><span class="sxs-lookup"><span data-stu-id="f57c3-1219">Configure additional options</span></span>

<span data-ttu-id="f57c3-1220">Zusätzliche Optionen können in der- `WithUrl` Methode ( `withUrl` in JavaScript) für `HubConnectionBuilder` oder für die verschiedenen Konfigurations-APIs auf dem `HttpHubConnectionBuilder` im Java-Client konfiguriert werden:</span><span class="sxs-lookup"><span data-stu-id="f57c3-1220">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="f57c3-1221">.NET</span><span class="sxs-lookup"><span data-stu-id="f57c3-1221">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="f57c3-1222">.Net-Option</span><span class="sxs-lookup"><span data-stu-id="f57c3-1222">.NET Option</span></span> |  <span data-ttu-id="f57c3-1223">Standardwert</span><span class="sxs-lookup"><span data-stu-id="f57c3-1223">Default value</span></span> | <span data-ttu-id="f57c3-1224">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="f57c3-1224">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="f57c3-1225">Eine Funktion, die eine Zeichenfolge zurückgibt, die als bearerauthentifizierungstoken in HTTP-Anforderungen bereitgestellt</span><span class="sxs-lookup"><span data-stu-id="f57c3-1225">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="f57c3-1226">Legen Sie dies auf fest, `true` um den Aushandlungs Schritt zu überspringen</span><span class="sxs-lookup"><span data-stu-id="f57c3-1226">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="f57c3-1227">Wird **nur unterstützt, wenn der websockets-Transport der einzige aktivierte Transport ist** .</span><span class="sxs-lookup"><span data-stu-id="f57c3-1227">**Only supported when the WebSockets transport is the only enabled transport** .</span></span> <span data-ttu-id="f57c3-1228">Diese Einstellung kann nicht aktiviert werden, wenn der Azure-Dienst verwendet wird :::no-loc(SignalR)::: .</span><span class="sxs-lookup"><span data-stu-id="f57c3-1228">This setting can't be enabled when using the Azure :::no-loc(SignalR)::: Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="f57c3-1229">Leer</span><span class="sxs-lookup"><span data-stu-id="f57c3-1229">Empty</span></span> | <span data-ttu-id="f57c3-1230">Eine Auflistung von TLS-Zertifikaten, die zum Authentifizieren von Anforderungen gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="f57c3-1230">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `:::no-loc(Cookie):::s` | <span data-ttu-id="f57c3-1231">Leer</span><span class="sxs-lookup"><span data-stu-id="f57c3-1231">Empty</span></span> | <span data-ttu-id="f57c3-1232">Eine Auflistung von http- :::no-loc(cookie)::: s, die mit jeder HTTP-Anforderung gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="f57c3-1232">A collection of HTTP :::no-loc(cookie):::s to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="f57c3-1233">Leer</span><span class="sxs-lookup"><span data-stu-id="f57c3-1233">Empty</span></span> | <span data-ttu-id="f57c3-1234">Anmelde Informationen, die mit jeder HTTP-Anforderung gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="f57c3-1234">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="f57c3-1235">5 Sekunden</span><span class="sxs-lookup"><span data-stu-id="f57c3-1235">5 seconds</span></span> | <span data-ttu-id="f57c3-1236">Nur websockets.</span><span class="sxs-lookup"><span data-stu-id="f57c3-1236">WebSockets only.</span></span> <span data-ttu-id="f57c3-1237">Die maximale Zeitspanne, die der Client nach dem Schließen des Servers wartet, um die Anforderung zum Schließen zu bestätigen.</span><span class="sxs-lookup"><span data-stu-id="f57c3-1237">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="f57c3-1238">Wenn der Server die Schließung innerhalb dieses Zeitraums nicht anerkennt, trennt der Client die Verbindung.</span><span class="sxs-lookup"><span data-stu-id="f57c3-1238">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="f57c3-1239">Leer</span><span class="sxs-lookup"><span data-stu-id="f57c3-1239">Empty</span></span> | <span data-ttu-id="f57c3-1240">Eine Karte mit zusätzlichen HTTP-Headern, die mit jeder HTTP-Anforderung gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="f57c3-1240">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="f57c3-1241">Ein Delegat, der verwendet werden kann, um den `HttpMessageHandler` zum Senden von HTTP-Anforderungen verwendeten zu konfigurieren oder zu ersetzen.</span><span class="sxs-lookup"><span data-stu-id="f57c3-1241">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="f57c3-1242">Wird nicht für WebSocket-Verbindungen verwendet.</span><span class="sxs-lookup"><span data-stu-id="f57c3-1242">Not used for WebSocket connections.</span></span> <span data-ttu-id="f57c3-1243">Dieser Delegat muss einen Wert zurückgeben, der nicht NULL ist, und erhält den Standardwert als Parameter.</span><span class="sxs-lookup"><span data-stu-id="f57c3-1243">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="f57c3-1244">Ändern Sie die Einstellungen für diesen Standardwert, und geben Sie ihn zurück, oder geben Sie eine neue `HttpMessageHandler` Instanz zurück.</span><span class="sxs-lookup"><span data-stu-id="f57c3-1244">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="f57c3-1245">**Wenn Sie den Handler ersetzen, stellen Sie sicher, dass die Einstellungen, die Sie beibehalten möchten, vom bereitgestellten Handler kopiert werden. Andernfalls gelten die konfigurierten Optionen (z. b. :::no-loc(Cookie)::: s und Header) nicht für den neuen Handler.**</span><span class="sxs-lookup"><span data-stu-id="f57c3-1245">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as :::no-loc(Cookie):::s and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="f57c3-1246">Ein HTTP-Proxy, der beim Senden von HTTP-Anforderungen verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="f57c3-1246">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="f57c3-1247">Legen Sie diesen booleschen Wert fest, um die Standard Anmelde Informationen für http-und websockets-Anforderungen zu senden</span><span class="sxs-lookup"><span data-stu-id="f57c3-1247">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="f57c3-1248">Dies ermöglicht die Verwendung der Windows-Authentifizierung.</span><span class="sxs-lookup"><span data-stu-id="f57c3-1248">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="f57c3-1249">Ein Delegat, der zum Konfigurieren zusätzlicher WebSocket-Optionen verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="f57c3-1249">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="f57c3-1250">Empfängt eine Instanz von [clientwebsocketoptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) , die zum Konfigurieren der Optionen verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="f57c3-1250">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="f57c3-1251">JavaScript</span><span class="sxs-lookup"><span data-stu-id="f57c3-1251">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="f57c3-1252">JavaScript-Option</span><span class="sxs-lookup"><span data-stu-id="f57c3-1252">JavaScript Option</span></span> | <span data-ttu-id="f57c3-1253">Standardwert</span><span class="sxs-lookup"><span data-stu-id="f57c3-1253">Default Value</span></span> | <span data-ttu-id="f57c3-1254">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="f57c3-1254">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="f57c3-1255">Eine Funktion, die eine Zeichenfolge zurückgibt, die als bearerauthentifizierungstoken in HTTP-Anforderungen bereitgestellt</span><span class="sxs-lookup"><span data-stu-id="f57c3-1255">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `transport` | `null` | <span data-ttu-id="f57c3-1256">Ein- <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType> Wert, der den für die Verbindung zu verwendenden Transport angibt.</span><span class="sxs-lookup"><span data-stu-id="f57c3-1256">An <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType> value specifying the transport to use for the connection.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="f57c3-1257">Legen Sie auf fest, `true` um die Bytes/Zeichen der vom Client gesendeten und empfangenen Nachrichten zu protokollieren.</span><span class="sxs-lookup"><span data-stu-id="f57c3-1257">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="f57c3-1258">Legen Sie dies auf fest, `true` um den Aushandlungs Schritt zu überspringen</span><span class="sxs-lookup"><span data-stu-id="f57c3-1258">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="f57c3-1259">Wird **nur unterstützt, wenn der websockets-Transport der einzige aktivierte Transport ist** .</span><span class="sxs-lookup"><span data-stu-id="f57c3-1259">**Only supported when the WebSockets transport is the only enabled transport** .</span></span> <span data-ttu-id="f57c3-1260">Diese Einstellung kann nicht aktiviert werden, wenn der Azure-Dienst verwendet wird :::no-loc(SignalR)::: .</span><span class="sxs-lookup"><span data-stu-id="f57c3-1260">This setting can't be enabled when using the Azure :::no-loc(SignalR)::: Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="f57c3-1261">Java</span><span class="sxs-lookup"><span data-stu-id="f57c3-1261">Java</span></span>](#tab/java)

| <span data-ttu-id="f57c3-1262">Java-Option</span><span class="sxs-lookup"><span data-stu-id="f57c3-1262">Java Option</span></span> | <span data-ttu-id="f57c3-1263">Standardwert</span><span class="sxs-lookup"><span data-stu-id="f57c3-1263">Default Value</span></span> | <span data-ttu-id="f57c3-1264">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="f57c3-1264">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="f57c3-1265">Eine Funktion, die eine Zeichenfolge zurückgibt, die als bearerauthentifizierungstoken in HTTP-Anforderungen bereitgestellt</span><span class="sxs-lookup"><span data-stu-id="f57c3-1265">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="f57c3-1266">Legen Sie dies auf fest, `true` um den Aushandlungs Schritt zu überspringen</span><span class="sxs-lookup"><span data-stu-id="f57c3-1266">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="f57c3-1267">Wird **nur unterstützt, wenn der websockets-Transport der einzige aktivierte Transport ist** .</span><span class="sxs-lookup"><span data-stu-id="f57c3-1267">**Only supported when the WebSockets transport is the only enabled transport** .</span></span> <span data-ttu-id="f57c3-1268">Diese Einstellung kann nicht aktiviert werden, wenn der Azure-Dienst verwendet wird :::no-loc(SignalR)::: .</span><span class="sxs-lookup"><span data-stu-id="f57c3-1268">This setting can't be enabled when using the Azure :::no-loc(SignalR)::: Service.</span></span> |
| <span data-ttu-id="f57c3-1269">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="f57c3-1269">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="f57c3-1270">Leer</span><span class="sxs-lookup"><span data-stu-id="f57c3-1270">Empty</span></span> | <span data-ttu-id="f57c3-1271">Eine Karte mit zusätzlichen HTTP-Headern, die mit jeder HTTP-Anforderung gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="f57c3-1271">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="f57c3-1272">Im .NET-Client können diese Optionen durch den für bereitgestellten Options Delegaten geändert werden `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="f57c3-1272">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.:::no-loc(Cookie):::s.Add(new :::no-loc(Cookie):::(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="f57c3-1273">Im JavaScript-Client können diese Optionen in einem JavaScript-Objekt bereitgestellt werden, das für Folgendes bereitgestellt wird `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="f57c3-1273">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="f57c3-1274">Im Java-Client können diese Optionen mit den Methoden auf der konfiguriert werden, die `HttpHubConnectionBuilder` von der `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="f57c3-1274">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="f57c3-1275">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="f57c3-1275">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
