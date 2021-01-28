---
title: Konfigurieren von Optionen für den ASP.NET Core-Kestrel-Webserver
author: rick-anderson
description: Hier erhalten Sie Informationen zum Konfigurieren von Optionen mit Kestrel, dem plattformübergreifenden Webserver für ASP.NET Core.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 05/04/2020
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
uid: fundamentals/servers/kestrel/options
ms.openlocfilehash: 198d509a68224077d3764cc836121b89e96c6853
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253846"
---
# <a name="configure-options-for-the-aspnet-core-kestrel-web-server"></a><span data-ttu-id="73759-103">Konfigurieren von Optionen für den ASP.NET Core-Kestrel-Webserver</span><span class="sxs-lookup"><span data-stu-id="73759-103">Configure options for the ASP.NET Core Kestrel web server</span></span>

<span data-ttu-id="73759-104">Der Kestrel-Webserver verfügt über einschränkende Konfigurationsoptionen, die besonders nützlich bei Bereitstellungen mit Internetzugriff sind.</span><span class="sxs-lookup"><span data-stu-id="73759-104">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="73759-105">Um zusätzliche Konfiguration nach dem Aufruf von `ConfigureWebHostDefaults` bereitzustellen, verwenden Sie `ConfigureKestrel`:</span><span class="sxs-lookup"><span data-stu-id="73759-105">To provide additional configuration after calling `ConfigureWebHostDefaults`, use `ConfigureKestrel`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel(serverOptions =>
            {
                // Set properties and call methods on options
            })
            .UseStartup<Startup>();
        });
```

<span data-ttu-id="73759-106">Legen Sie Einschränkungen für die <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits>-Eigenschaft der <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>-Klasse fest.</span><span class="sxs-lookup"><span data-stu-id="73759-106">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="73759-107">Die `Limits`-Eigenschaft enthält eine Instanz der <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>-Klasse.</span><span class="sxs-lookup"><span data-stu-id="73759-107">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="73759-108">In den folgenden Beispielen wird der <xref:Microsoft.AspNetCore.Server.Kestrel.Core>-Namespace verwendet:</span><span class="sxs-lookup"><span data-stu-id="73759-108">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="73759-109">In den Beispielen, die weiter unten in diesem Artikel aufgeführt sind, werden Kestrel-Optionen in C#-Code konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="73759-109">In examples shown later in this article, Kestrel options are configured in C# code.</span></span> <span data-ttu-id="73759-110">Kestrel-Optionen können ebenso mithilfe eines [Konfigurationsanbieters](xref:fundamentals/configuration/index) festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="73759-110">Kestrel options can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="73759-111">Beispielsweise kann der [Dateikonfigurationsanbieter](xref:fundamentals/configuration/index#file-configuration-provider) die Kestrel-Konfiguration aus einer *appsettings.json* - oder *appsettings.{Umgebung}.json*-Datei laden:</span><span class="sxs-lookup"><span data-stu-id="73759-111">For example, the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider) can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

```json
{
  "Kestrel": {
    "Limits": {
      "MaxConcurrentConnections": 100,
      "MaxConcurrentUpgradedConnections": 100
    },
    "DisableStringReuse": true
  }
}
```

> [!NOTE]
> <span data-ttu-id="73759-112"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> und die [Endpunktkonfiguration](xref:fundamentals/servers/kestrel/endpoints) können über Konfigurationsanbieter konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="73759-112"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> and [endpoint configuration](xref:fundamentals/servers/kestrel/endpoints) are configurable from configuration providers.</span></span> <span data-ttu-id="73759-113">Die verbleibende Kestrel-Konfiguration muss in C#-Code konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="73759-113">Remaining Kestrel configuration must be configured in C# code.</span></span>

<span data-ttu-id="73759-114">Verwenden Sie **einen** der folgenden Ansätze:</span><span class="sxs-lookup"><span data-stu-id="73759-114">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="73759-115">Konfigurieren Sie Kestrel in `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="73759-115">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="73759-116">Fügen Sie eine Instanz von `IConfiguration` in die `Startup`-Klasse ein.</span><span class="sxs-lookup"><span data-stu-id="73759-116">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="73759-117">Im folgenden Beispiel wird davon ausgegangen, dass die eingefügte Konfiguration der `Configuration`-Eigenschaft zugewiesen wird.</span><span class="sxs-lookup"><span data-stu-id="73759-117">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="73759-118">Laden Sie in `Startup.ConfigureServices` den Abschnitt `Kestrel` der Konfiguration in die Konfiguration von Kestrel:</span><span class="sxs-lookup"><span data-stu-id="73759-118">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

     ```csharp
     using Microsoft.Extensions.Configuration
     
     public class Startup
     {
         public Startup(IConfiguration configuration)
         {
             Configuration = configuration;
         }

         public IConfiguration Configuration { get; }

         public void ConfigureServices(IServiceCollection services)
         {
             services.Configure<KestrelServerOptions>(
                 Configuration.GetSection("Kestrel"));
         }

         public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
         {
             ...
         }
     }
     ```

* <span data-ttu-id="73759-119">Konfigurieren Sie Kestrel beim Erstellen des Hosts:</span><span class="sxs-lookup"><span data-stu-id="73759-119">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="73759-120">Laden Sie in *Program.cs* den Abschnitt `Kestrel` der Konfiguration in die Konfiguration von Kestrel:</span><span class="sxs-lookup"><span data-stu-id="73759-120">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

  ```csharp
  // using Microsoft.Extensions.DependencyInjection;

  public static IHostBuilder CreateHostBuilder(string[] args) =>
      Host.CreateDefaultBuilder(args)
          .ConfigureServices((context, services) =>
          {
              services.Configure<KestrelServerOptions>(
                  context.Configuration.GetSection("Kestrel"));
          })
          .ConfigureWebHostDefaults(webBuilder =>
          {
              webBuilder.UseStartup<Startup>();
          });
  ```

<span data-ttu-id="73759-121">Beide vorangehenden Ansätze funktionieren mit jedem [Konfigurationsanbieter](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="73759-121">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

## <a name="general-limits"></a><span data-ttu-id="73759-122">Allgemeine Grenzwerte</span><span class="sxs-lookup"><span data-stu-id="73759-122">General limits</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="73759-123">Keep-Alive-Timeout</span><span class="sxs-lookup"><span data-stu-id="73759-123">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="73759-124">Ruft das [Keep-Alive-Timeout](https://tools.ietf.org/html/rfc7230#section-6.5) ab oder legt es fest.</span><span class="sxs-lookup"><span data-stu-id="73759-124">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="73759-125">Standardwert: 2 Minuten.</span><span class="sxs-lookup"><span data-stu-id="73759-125">Defaults to 2 minutes.</span></span>

[!code-csharp[](samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=19-20)]

### <a name="maximum-client-connections"></a><span data-ttu-id="73759-126">Maximale Anzahl der Clientverbindungen</span><span class="sxs-lookup"><span data-stu-id="73759-126">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="73759-127">Die maximale Anzahl von gleichzeitig geöffneten TCP-Verbindungen kann mithilfe von folgendem Code für die gesamte App festgelegt werden:</span><span class="sxs-lookup"><span data-stu-id="73759-127">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

[!code-csharp[](samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

<span data-ttu-id="73759-128">Es gibt einen separaten Grenzwert für Verbindungen, die von HTTP oder HTTPS auf ein anderes Protokoll aktualisiert wurden (z.B. auf eine WebSockets-Anforderung).</span><span class="sxs-lookup"><span data-stu-id="73759-128">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="73759-129">Nachdem eine Verbindung aktualisiert wurde, zählt diese nicht mehr für den `MaxConcurrentConnections`-Grenzwert.</span><span class="sxs-lookup"><span data-stu-id="73759-129">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

[!code-csharp[](samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

<span data-ttu-id="73759-130">Die maximale Anzahl von Verbindungen ist standardmäßig nicht begrenzt (NULL).</span><span class="sxs-lookup"><span data-stu-id="73759-130">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="73759-131">Maximale Größe des Anforderungstexts</span><span class="sxs-lookup"><span data-stu-id="73759-131">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="73759-132">Die maximale Größe des Anforderungstexts beträgt standardmäßig 30.000.000 Byte, also ungefähr 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="73759-132">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="73759-133">Die empfohlene Methode zur Außerkraftsetzung des Grenzwerts in einer ASP.NET Core-MVC-App besteht im Verwenden des <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute>-Attributs in einer Aktionsmethode:</span><span class="sxs-lookup"><span data-stu-id="73759-133">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="73759-134">Im folgenden Beispiel wird veranschaulicht, wie die Einschränkungen auf jeder Anforderung für die App konfiguriert werden:</span><span class="sxs-lookup"><span data-stu-id="73759-134">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

[!code-csharp[](samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

<span data-ttu-id="73759-135">Außer Kraft setzen der Einstellung für eine bestimmte Anforderung in Middleware:</span><span class="sxs-lookup"><span data-stu-id="73759-135">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="73759-136">Eine Ausnahme wird ausgelöst, wenn die App den Grenzwert einer Anforderung konfiguriert, nachdem die App bereits mit dem Lesen der Anforderung begonnen hat.</span><span class="sxs-lookup"><span data-stu-id="73759-136">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="73759-137">Es gibt eine `IsReadOnly`-Eigenschaft, die angibt, wenn sich die `MaxRequestBodySize`-Eigenschaft im schreibgeschützten Zustand befindet, also wenn der Grenzwert nicht mehr konfiguriert werden kann.</span><span class="sxs-lookup"><span data-stu-id="73759-137">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="73759-138">Wenn eine App [prozessextern](xref:host-and-deploy/iis/index#out-of-process-hosting-model) hinter dem [ASP.NET Core-Modul](xref:host-and-deploy/aspnet-core-module) ausgeführt wird, ist das Größenlimit von Kestrel für Anforderungstext deaktiviert.</span><span class="sxs-lookup"><span data-stu-id="73759-138">When an app runs [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled.</span></span> <span data-ttu-id="73759-139">IIS legt das Limit bereits fest.</span><span class="sxs-lookup"><span data-stu-id="73759-139">IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="73759-140">Minimale Datenrate des Anforderungstexts</span><span class="sxs-lookup"><span data-stu-id="73759-140">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="73759-141">Kestrel überprüft sekündlich, ob Daten mit der angegebenen Rate in Bytes/Sekunde eingehen.</span><span class="sxs-lookup"><span data-stu-id="73759-141">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="73759-142">Wenn die Rate den mindestens erforderlichen Wert unterschreitet, wird für die Verbindung wegen Timeout getrennt. Bei der Toleranzperiode handelt es sich um die Zeitspanne, die Kestrel dem Client gewährt, um die Senderate auf den mindestens erforderlichen Wert zu erhöhen.</span><span class="sxs-lookup"><span data-stu-id="73759-142">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time Kestrel allows the client to increase its send rate up to the minimum.</span></span> <span data-ttu-id="73759-143">Die Rate wird während dieser Zeit nicht geprüft.</span><span class="sxs-lookup"><span data-stu-id="73759-143">The rate isn't checked during that time.</span></span> <span data-ttu-id="73759-144">Diese Toleranzperiode beugt dem Trennen von Verbindungen vor, die Daten aufgrund eines langsamen TCP-Starts anfänglich mit einer niedrigen Rate senden.</span><span class="sxs-lookup"><span data-stu-id="73759-144">The grace period helps avoid dropping connections that are initially sending data at a slow rate because of TCP slow-start.</span></span>

<span data-ttu-id="73759-145">Die mindestens erforderliche Rate beträgt standardmäßig 240 Bytes/Sekunde mit einer Toleranzperiode von 5 Sekunden.</span><span class="sxs-lookup"><span data-stu-id="73759-145">The default minimum rate is 240 bytes/second with a 5-second grace period.</span></span>

<span data-ttu-id="73759-146">Für die Antwort gilt ebenfalls eine mindestens erforderliche Rate.</span><span class="sxs-lookup"><span data-stu-id="73759-146">A minimum rate also applies to the response.</span></span> <span data-ttu-id="73759-147">Der Code zum Festlegen des Grenzwerts für Anforderung und Antwort ist abgesehen von `RequestBody` oder `Response` in den Namen von Eigenschaften und Schnittstelle identisch.</span><span class="sxs-lookup"><span data-stu-id="73759-147">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="73759-148">In diesem Beispiel wird veranschaulicht, wie Sie die mindestens erforderlichen Datenraten in *Program.cs* konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="73759-148">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

[!code-csharp[](samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-11)]

<span data-ttu-id="73759-149">Außer Kraft setzen des minimalen Ratenlimits pro Anforderung in Middleware:</span><span class="sxs-lookup"><span data-stu-id="73759-149">Override the minimum rate limits per request in middleware:</span></span>

[!code-csharp[](samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

<span data-ttu-id="73759-150">Das <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature>, auf das im vorherigen Beispiel verwiesen wird, ist in `HttpContext.Features` für HTTP/2-Anforderungen nicht vorhanden.</span><span class="sxs-lookup"><span data-stu-id="73759-150">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature> referenced in the prior sample isn't present in `HttpContext.Features` for HTTP/2 requests.</span></span> <span data-ttu-id="73759-151">Das Ändern der Ratenlimits auf Anforderungsbasis wird in der Regel für HTTP/2 nicht unterstützt, weil das Protokoll Anforderungsmultiplexing unterstützt.</span><span class="sxs-lookup"><span data-stu-id="73759-151">Modifying rate limits on a per-request basis is generally not supported for HTTP/2 because of the protocol's support for request multiplexing.</span></span> <span data-ttu-id="73759-152"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> ist jedoch in `HttpContext.Features` für HTTP/2-Anforderungen noch vorhanden, weil das Leseratenlimit weiterhin für jede Anforderung *vollständig deaktiviert* werden kann, indem `IHttpMinRequestBodyDataRateFeature.MinDataRate` auch für eine HTTP/2-Anforderung auf `null` festgelegt wird.</span><span class="sxs-lookup"><span data-stu-id="73759-152">However, the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> is still present `HttpContext.Features` for HTTP/2 requests, because the read rate limit can still be *disabled entirely* on a per-request basis by setting `IHttpMinRequestBodyDataRateFeature.MinDataRate` to `null` even for an HTTP/2 request.</span></span> <span data-ttu-id="73759-153">Der Versuch, `IHttpMinRequestBodyDataRateFeature.MinDataRate` zu lesen oder auf einen anderen Wert als `null` festzulegen, führt dazu, dass bei einer HTTP/2-Anforderung eine `NotSupportedException` ausgelöst wird.</span><span class="sxs-lookup"><span data-stu-id="73759-153">Attempting to read `IHttpMinRequestBodyDataRateFeature.MinDataRate` or attempting to set it to a value other than `null` will result in a `NotSupportedException` being thrown given an HTTP/2 request.</span></span>

<span data-ttu-id="73759-154">Serverweite Ratenlimits, die über `KestrelServerOptions.Limits` konfiguriert sind, gelten auch für HTTP/1.x- und HTTP/2-Verbindungen.</span><span class="sxs-lookup"><span data-stu-id="73759-154">Server-wide rate limits configured via `KestrelServerOptions.Limits` still apply to both HTTP/1.x and HTTP/2 connections.</span></span>

### <a name="request-headers-timeout"></a><span data-ttu-id="73759-155">Timeout für Anforderungsheader</span><span class="sxs-lookup"><span data-stu-id="73759-155">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="73759-156">Ruft die maximale Zeitspanne ab, während der der Server Anforderungsheader empfängt, oder legt diese fest.</span><span class="sxs-lookup"><span data-stu-id="73759-156">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="73759-157">Der Standardwert beträgt 30 Sekunden.</span><span class="sxs-lookup"><span data-stu-id="73759-157">Defaults to 30 seconds.</span></span>

[!code-csharp[](samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=21-22)]

## <a name="http2-limits"></a><span data-ttu-id="73759-158">HTTP/2-Limits</span><span class="sxs-lookup"><span data-stu-id="73759-158">HTTP/2 limits</span></span>

### <a name="maximum-streams-per-connection"></a><span data-ttu-id="73759-159">Maximale Datenströme pro Verbindung</span><span class="sxs-lookup"><span data-stu-id="73759-159">Maximum streams per connection</span></span>

<span data-ttu-id="73759-160">`Http2.MaxStreamsPerConnection` schränkt die Anzahl gleichzeitiger Anforderungsdatenströme pro HTTP/2-Verbindung ein.</span><span class="sxs-lookup"><span data-stu-id="73759-160">`Http2.MaxStreamsPerConnection` limits the number of concurrent request streams per HTTP/2 connection.</span></span> <span data-ttu-id="73759-161">Überschüssige Datenströme werden zurückgewiesen.</span><span class="sxs-lookup"><span data-stu-id="73759-161">Excess streams are refused.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
});
```

<span data-ttu-id="73759-162">Der Standardwert ist 100.</span><span class="sxs-lookup"><span data-stu-id="73759-162">The default value is 100.</span></span>

### <a name="header-table-size"></a><span data-ttu-id="73759-163">Größe der Headertabelle</span><span class="sxs-lookup"><span data-stu-id="73759-163">Header table size</span></span>

<span data-ttu-id="73759-164">Der HPACK-Decoder dekomprimiert HTTP-Header für HTTP/2-Verbindungen.</span><span class="sxs-lookup"><span data-stu-id="73759-164">The HPACK decoder decompresses HTTP headers for HTTP/2 connections.</span></span> <span data-ttu-id="73759-165">`Http2.HeaderTableSize` schränkt die Größe der Headerkomprimierungstabelle ein, die der HPACK-Decoder verwendet.</span><span class="sxs-lookup"><span data-stu-id="73759-165">`Http2.HeaderTableSize` limits the size of the header compression table that the HPACK decoder uses.</span></span> <span data-ttu-id="73759-166">Der Wert wird in Oktetten bereitgestellt und muss größer als null (0) sein.</span><span class="sxs-lookup"><span data-stu-id="73759-166">The value is provided in octets and must be greater than zero (0).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.HeaderTableSize = 4096;
});
```

<span data-ttu-id="73759-167">Der Standardwert ist 4096.</span><span class="sxs-lookup"><span data-stu-id="73759-167">The default value is 4096.</span></span>

### <a name="maximum-frame-size"></a><span data-ttu-id="73759-168">Maximale Framegröße</span><span class="sxs-lookup"><span data-stu-id="73759-168">Maximum frame size</span></span>

<span data-ttu-id="73759-169">`Http2.MaxFrameSize` gibt die maximal zulässige Größe einer vom Server empfangenen oder gesendeten HTTP/2-Verbindungsrahmen-Nutzlast an.</span><span class="sxs-lookup"><span data-stu-id="73759-169">`Http2.MaxFrameSize` indicates the maximum allowed size of an HTTP/2 connection frame payload received or sent by the server.</span></span> <span data-ttu-id="73759-170">Der Wert wird in Oktetten bereitgestellt und muss zwischen 2^14 (16.384) und 2^24-1 (16.777.215) liegen.</span><span class="sxs-lookup"><span data-stu-id="73759-170">The value is provided in octets and must be between 2^14 (16,384) and 2^24-1 (16,777,215).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxFrameSize = 16384;
});
```

<span data-ttu-id="73759-171">Der Standardwert ist 2^14 (16.384).</span><span class="sxs-lookup"><span data-stu-id="73759-171">The default value is 2^14 (16,384).</span></span>

### <a name="maximum-request-header-size"></a><span data-ttu-id="73759-172">Maximale Größe des Anforderungsheaders</span><span class="sxs-lookup"><span data-stu-id="73759-172">Maximum request header size</span></span>

<span data-ttu-id="73759-173">`Http2.MaxRequestHeaderFieldSize` gibt die maximal zulässige Größe in Oktetten der Anforderungsheaderwerte an.</span><span class="sxs-lookup"><span data-stu-id="73759-173">`Http2.MaxRequestHeaderFieldSize` indicates the maximum allowed size in octets of request header values.</span></span> <span data-ttu-id="73759-174">Dieser Grenzwert gilt sowohl für den Namen als auch den Wert in der komprimierten und nicht komprimierten Darstellung.</span><span class="sxs-lookup"><span data-stu-id="73759-174">This limit applies to both name and value in their compressed and uncompressed representations.</span></span> <span data-ttu-id="73759-175">Der Wert muss größer als 0 (null) sein.</span><span class="sxs-lookup"><span data-stu-id="73759-175">The value must be greater than zero (0).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
});
```

<span data-ttu-id="73759-176">Der Standardwert ist 8.192.</span><span class="sxs-lookup"><span data-stu-id="73759-176">The default value is 8,192.</span></span>

### <a name="initial-connection-window-size"></a><span data-ttu-id="73759-177">Anfangsfenstergröße der Verbindung</span><span class="sxs-lookup"><span data-stu-id="73759-177">Initial connection window size</span></span>

<span data-ttu-id="73759-178">`Http2.InitialConnectionWindowSize` gibt die maximalen Anforderungstextdaten in Byte an, die der Server auf einmal für alle Anforderungen (Streams) pro Verbindung aggregiert.</span><span class="sxs-lookup"><span data-stu-id="73759-178">`Http2.InitialConnectionWindowSize` indicates the maximum request body data in bytes the server buffers at one time aggregated across all requests (streams) per connection.</span></span> <span data-ttu-id="73759-179">Anforderungen werden auch durch `Http2.InitialStreamWindowSize` beschränkt.</span><span class="sxs-lookup"><span data-stu-id="73759-179">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="73759-180">Der Wert muss größer als oder gleich 65.535 und kleiner als 2^31 (2.147.483.648) sein.</span><span class="sxs-lookup"><span data-stu-id="73759-180">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
});
```

<span data-ttu-id="73759-181">Der Standardwert ist 128 KB (131.072).</span><span class="sxs-lookup"><span data-stu-id="73759-181">The default value is 128 KB (131,072).</span></span>

### <a name="initial-stream-window-size"></a><span data-ttu-id="73759-182">Anfangsfenstergröße des Streams</span><span class="sxs-lookup"><span data-stu-id="73759-182">Initial stream window size</span></span>

<span data-ttu-id="73759-183">`Http2.InitialStreamWindowSize` gibt die maximalen Anforderungstextdaten in Byte an, die der Server auf einmal pro Anforderung (Stream) puffert.</span><span class="sxs-lookup"><span data-stu-id="73759-183">`Http2.InitialStreamWindowSize` indicates the maximum request body data in bytes the server buffers at one time per request (stream).</span></span> <span data-ttu-id="73759-184">Anforderungen werden auch durch `Http2.InitialConnectionWindowSize` beschränkt.</span><span class="sxs-lookup"><span data-stu-id="73759-184">Requests are also limited by `Http2.InitialConnectionWindowSize`.</span></span> <span data-ttu-id="73759-185">Der Wert muss größer als oder gleich 65.535 und kleiner als 2^31 (2.147.483.648) sein.</span><span class="sxs-lookup"><span data-stu-id="73759-185">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
});
```

<span data-ttu-id="73759-186">Der Standardwert ist 96 KB (98.304).</span><span class="sxs-lookup"><span data-stu-id="73759-186">The default value is 96 KB (98,304).</span></span>

### <a name="http2-keep-alive-ping-configuration"></a><span data-ttu-id="73759-187">Konfiguration für HTTP/2-Keep-Alive-Pings</span><span class="sxs-lookup"><span data-stu-id="73759-187">HTTP/2 keep alive ping configuration</span></span>

<span data-ttu-id="73759-188">Kestrel kann so konfiguriert werden, dass HTTP/2-Pings an verbundene Clients gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="73759-188">Kestrel can be configured to send HTTP/2 pings to connected clients.</span></span> <span data-ttu-id="73759-189">HTTP/2-Pings dienen mehreren Zwecken:</span><span class="sxs-lookup"><span data-stu-id="73759-189">HTTP/2 pings serve multiple purposes:</span></span>

* <span data-ttu-id="73759-190">Verbindungen im Leerlauf werden aktiv gehalten.</span><span class="sxs-lookup"><span data-stu-id="73759-190">Keep idle connections alive.</span></span> <span data-ttu-id="73759-191">Einige Clients und Proxyserver trennen Verbindungen, die sich im Leerlauf befinden.</span><span class="sxs-lookup"><span data-stu-id="73759-191">Some clients and proxy servers close connections that are idle.</span></span> <span data-ttu-id="73759-192">HTTP/2-Pings werden als Aktivität einer Verbindung betrachtet und verhindern, dass die Verbindung getrennt wird.</span><span class="sxs-lookup"><span data-stu-id="73759-192">HTTP/2 pings are considered as activity on a connection and prevent the connection from being closed as idle.</span></span>
* <span data-ttu-id="73759-193">Fehlerhafte Verbindungen werden getrennt.</span><span class="sxs-lookup"><span data-stu-id="73759-193">Close unhealthy connections.</span></span> <span data-ttu-id="73759-194">Verbindungen, bei denen der Client nicht innerhalb der konfigurierten Zeit auf einen Keep-Alive-Ping antwortet, werden vom Server getrennt.</span><span class="sxs-lookup"><span data-stu-id="73759-194">Connections where the client doesn't respond to the keep alive ping in the configured time are closed by the server.</span></span>

<span data-ttu-id="73759-195">Es gibt zwei Konfigurationsoptionen für HTTP/2-Keep-Alive-Pings:</span><span class="sxs-lookup"><span data-stu-id="73759-195">There are two configuration options related to HTTP/2 keep alive pings:</span></span>

* <span data-ttu-id="73759-196">`Http2.KeepAlivePingInterval` ist eine `TimeSpan`, die das Pingintervall konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="73759-196">`Http2.KeepAlivePingInterval` is a `TimeSpan` that configures the ping interval.</span></span> <span data-ttu-id="73759-197">Der Server sendet einen Keep-Alive-Ping an den Client, wenn dieser für diesen Zeitraum keine Frames erhält.</span><span class="sxs-lookup"><span data-stu-id="73759-197">The server sends a keep alive ping to the client if it doesn't receive any frames for this period of time.</span></span> <span data-ttu-id="73759-198">Durch Festlegen dieser Option auf `TimeSpan.MaxValue` werden Keep-Alive-Pings deaktiviert.</span><span class="sxs-lookup"><span data-stu-id="73759-198">Keep alive pings are disabled when this option is set to `TimeSpan.MaxValue`.</span></span> <span data-ttu-id="73759-199">Standardwert: `TimeSpan.MaxValue`.</span><span class="sxs-lookup"><span data-stu-id="73759-199">The default value is `TimeSpan.MaxValue`.</span></span>
* <span data-ttu-id="73759-200">`Http2.KeepAlivePingTimeout` ist eine `TimeSpan`, die das Ping-Timeout konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="73759-200">`Http2.KeepAlivePingTimeout` is a `TimeSpan` that configures the ping timeout.</span></span> <span data-ttu-id="73759-201">Wenn der Server während dieses Timeouts keine Frames (z. B. als Antwort-Ping) empfängt, wird die Verbindung getrennt.</span><span class="sxs-lookup"><span data-stu-id="73759-201">If the server doesn't receive any frames, such as a response ping, during this timeout then the connection is closed.</span></span> <span data-ttu-id="73759-202">Durch Festlegen dieser Option auf `TimeSpan.MaxValue` wird das Keep-Alive-Timeout deaktiviert.</span><span class="sxs-lookup"><span data-stu-id="73759-202">Keep alive timeout is disabled when this option is set to `TimeSpan.MaxValue`.</span></span> <span data-ttu-id="73759-203">Der Standardwert ist 20 Sekunden.</span><span class="sxs-lookup"><span data-stu-id="73759-203">The default value is 20 seconds.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.KeepAlivePingInterval = TimeSpan.FromSeconds(30);
    serverOptions.Limits.Http2.KeepAlivePingTimeout = TimeSpan.FromSeconds(60);
});
```

## <a name="other-options"></a><span data-ttu-id="73759-204">Weitere Optionen</span><span class="sxs-lookup"><span data-stu-id="73759-204">Other options</span></span>

### <a name="synchronous-io"></a><span data-ttu-id="73759-205">Synchrone E/A-Vorgänge</span><span class="sxs-lookup"><span data-stu-id="73759-205">Synchronous I/O</span></span>

<span data-ttu-id="73759-206"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> steuert, ob für Anforderung und Antwort synchrone E/A-Vorgänge zulässig sind.</span><span class="sxs-lookup"><span data-stu-id="73759-206"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous I/O is allowed for the request and response.</span></span> <span data-ttu-id="73759-207">Der Standardwert ist `false`.</span><span class="sxs-lookup"><span data-stu-id="73759-207">The default value is `false`.</span></span>

> [!WARNING]
> <span data-ttu-id="73759-208">Sehr viele blockierende synchrone E/A-Vorgänge können zu einem Ressourcenmangel im Threadpool führen, wodurch die App nicht mehr reagiert.</span><span class="sxs-lookup"><span data-stu-id="73759-208">A large number of blocking synchronous I/O operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="73759-209">Aktivieren Sie `AllowSynchronousIO` nur bei Verwendung einer Bibliothek, die asynchrone E/A-Vorgänge nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="73759-209">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous I/O.</span></span>

<span data-ttu-id="73759-210">Das folgende Beispiel aktiviert synchrone E/A-Vorgänge:</span><span class="sxs-lookup"><span data-stu-id="73759-210">The following example enables synchronous I/O:</span></span>

[!code-csharp[](samples/5.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

<span data-ttu-id="73759-211">Weitere Informationen zu anderen Kestrel-Optionen und -Einschränkungen finden Sie unter:</span><span class="sxs-lookup"><span data-stu-id="73759-211">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>
