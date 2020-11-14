---
title: Implementierung des Webservers Kestrel in ASP.NET Core
author: rick-anderson
description: Einführung in Kestrel, dem plattformübergreifenden Webserver für ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/04/2020
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
uid: fundamentals/servers/kestrel
ms.openlocfilehash: 56ac6635639eed93a84f47fc915c7013c6ed2381
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93052330"
---
# <a name="kestrel-web-server-implementation-in-aspnet-core"></a><span data-ttu-id="76af1-103">Implementierung des Webservers Kestrel in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="76af1-103">Kestrel web server implementation in ASP.NET Core</span></span>

<span data-ttu-id="76af1-104">Von [Tom Dykstra](https://github.com/tdykstra), [Chris Ross](https://github.com/Tratcher) und [Stephen Halter](https://twitter.com/halter73)</span><span class="sxs-lookup"><span data-stu-id="76af1-104">By [Tom Dykstra](https://github.com/tdykstra), [Chris Ross](https://github.com/Tratcher), and [Stephen Halter](https://twitter.com/halter73)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="76af1-105">Einführung in Kestrel, dem plattformübergreifenden [Webserver für ASP.NET Core](xref:fundamentals/servers/index).</span><span class="sxs-lookup"><span data-stu-id="76af1-105">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="76af1-106">Kestrel ist der Webserver, der standardmäßig in ASP.NET Core-Projektvorlagen enthalten ist.</span><span class="sxs-lookup"><span data-stu-id="76af1-106">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="76af1-107">Kestrel unterstützt die folgenden Szenarios:</span><span class="sxs-lookup"><span data-stu-id="76af1-107">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="76af1-108">HTTPS</span><span class="sxs-lookup"><span data-stu-id="76af1-108">HTTPS</span></span>
* <span data-ttu-id="76af1-109">Nicht transparente Upgrades, die zum Aktivieren von [WebSockets](https://github.com/aspnet/websockets) verwendet werden</span><span class="sxs-lookup"><span data-stu-id="76af1-109">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="76af1-110">Unix-Sockets für eine hohe Leistung im Hintergrund von Nginx</span><span class="sxs-lookup"><span data-stu-id="76af1-110">Unix sockets for high performance behind Nginx</span></span>
* <span data-ttu-id="76af1-111">HTTP/2 (außer unter macOS&dagger;)</span><span class="sxs-lookup"><span data-stu-id="76af1-111">HTTP/2 (except on macOS&dagger;)</span></span>

<span data-ttu-id="76af1-112">&dagger;HTTP/2 wird unter macOS in einem zukünftigen Release unterstützt.</span><span class="sxs-lookup"><span data-stu-id="76af1-112">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>

<span data-ttu-id="76af1-113">Kestrel wird auf allen Plattformen und für alle Versionen unterstützt, die .NET Core unterstützt.</span><span class="sxs-lookup"><span data-stu-id="76af1-113">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="76af1-114">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="76af1-114">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="http2-support"></a><span data-ttu-id="76af1-115">HTTP/2-Unterstützung</span><span class="sxs-lookup"><span data-stu-id="76af1-115">HTTP/2 support</span></span>

<span data-ttu-id="76af1-116">[HTTP/2](https://httpwg.org/specs/rfc7540.html) ist für ASP.NET Core-Apps verfügbar, wenn die folgenden Basisanforderungen erfüllt sind:</span><span class="sxs-lookup"><span data-stu-id="76af1-116">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is available for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="76af1-117">Betriebssystem&dagger;</span><span class="sxs-lookup"><span data-stu-id="76af1-117">Operating system&dagger;</span></span>
  * <span data-ttu-id="76af1-118">Windows Server 2016/Windows 10 oder höher&Dagger;</span><span class="sxs-lookup"><span data-stu-id="76af1-118">Windows Server 2016/Windows 10 or later&Dagger;</span></span>
  * <span data-ttu-id="76af1-119">Linux mit OpenSSL 1.0.2 oder höher (z.B. Ubuntu 16.04 oder höher)</span><span class="sxs-lookup"><span data-stu-id="76af1-119">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
* <span data-ttu-id="76af1-120">Zielframework: .NET Core 2.2 oder höher</span><span class="sxs-lookup"><span data-stu-id="76af1-120">Target framework: .NET Core 2.2 or later</span></span>
* <span data-ttu-id="76af1-121">[ALPN](https://tools.ietf.org/html/rfc7301#section-3)-Verbindung (Application-Layer Protocol Negotiation)</span><span class="sxs-lookup"><span data-stu-id="76af1-121">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="76af1-122">TLS 1.2-Verbindung oder höher</span><span class="sxs-lookup"><span data-stu-id="76af1-122">TLS 1.2 or later connection</span></span>

<span data-ttu-id="76af1-123">&dagger;HTTP/2 wird unter macOS in einem zukünftigen Release unterstützt.</span><span class="sxs-lookup"><span data-stu-id="76af1-123">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>
<span data-ttu-id="76af1-124">&Dagger;Kestrel bietet eingeschränkte Unterstützung für HTTP/2 unter Windows Server 2012 R2 und Windows 8.1.</span><span class="sxs-lookup"><span data-stu-id="76af1-124">&Dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="76af1-125">Die Unterstützung ist eingeschränkt, weil die Liste der unterstützten TLS-Verschlüsselungssammlungen unter diesen Betriebssystemen begrenzt ist.</span><span class="sxs-lookup"><span data-stu-id="76af1-125">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="76af1-126">Zum Sichern von TLS-Verbindungen ist möglicherweise ein durch einen Elliptic Curve Digital Signature Algorithm (ECDSA) generiertes Zertifikat erforderlich.</span><span class="sxs-lookup"><span data-stu-id="76af1-126">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

<span data-ttu-id="76af1-127">Wenn eine HTTP/2-Verbindung hergestellt wurde, meldet [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*)`HTTP/2`.</span><span class="sxs-lookup"><span data-stu-id="76af1-127">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="76af1-128">HTTP/2 ist standardmäßig deaktiviert.</span><span class="sxs-lookup"><span data-stu-id="76af1-128">HTTP/2 is disabled by default.</span></span> <span data-ttu-id="76af1-129">Weitere Informationen zur Konfiguration finden Sie in den Abschnitten [Kestrel-Optionen](#kestrel-options) und [ListenOptions.Protocols](#listenoptionsprotocols).</span><span class="sxs-lookup"><span data-stu-id="76af1-129">For more information on configuration, see the [Kestrel options](#kestrel-options) and [ListenOptions.Protocols](#listenoptionsprotocols) sections.</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="76af1-130">Verwenden von Kestrel mit einem Reverseproxy</span><span class="sxs-lookup"><span data-stu-id="76af1-130">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="76af1-131">Kestrel kann eigenständig oder mit einem *Reverseproxyserver* wie z.B. [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org) oder [Apache](https://httpd.apache.org/) verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="76af1-131">Kestrel can be used by itself or with a *reverse proxy server* , such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="76af1-132">Ein Reverseproxyserver empfängt HTTP-Anforderungen aus dem Netzwerk und leitet diese an Kestrel weiter.</span><span class="sxs-lookup"><span data-stu-id="76af1-132">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="76af1-133">Kestrel bei Verwendung als Webserver mit direkter Internetverbindung:</span><span class="sxs-lookup"><span data-stu-id="76af1-133">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Kestrel kommuniziert direkt und ohne Reverseproxyserver mit dem Internet](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="76af1-135">Kestrel bei Verwendung in einer Reverseproxykonfiguration:</span><span class="sxs-lookup"><span data-stu-id="76af1-135">Kestrel used in a reverse proxy configuration:</span></span>

![Kestrel kommuniziert indirekt mit dem Internet über einen Reverseproxyserver wie IIS, Nginx oder Apache](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="76af1-137">Jede der beiden Konfigurationen – mit oder ohne einen Reverseproxyserver – stellt eine unterstützte Hostingkonfiguration dar.</span><span class="sxs-lookup"><span data-stu-id="76af1-137">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="76af1-138">Bei Verwendung als Edgeserver ohne Reverseproxyserver unterstützt Kestrel die gemeinsame Nutzung der gleichen IP-Adresse und des gleichen Ports durch mehrere Prozesse nicht.</span><span class="sxs-lookup"><span data-stu-id="76af1-138">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="76af1-139">Wenn Kestrel für das Lauschen an einem Port konfiguriert ist, verarbeitet Kestrel den gesamten Datenverkehr für diesen Port unabhängig von den `Host`-Headern der Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="76af1-139">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="76af1-140">Ein Reverseproxy, der Ports freigeben kann, kann Anforderungen an Kestrel über eine eindeutige IP und einen eindeutigen Port weiterleiten.</span><span class="sxs-lookup"><span data-stu-id="76af1-140">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="76af1-141">Auch wenn kein Reverseproxyserver erforderlich ist, kann die Verwendung eines solchen empfehlenswert sein.</span><span class="sxs-lookup"><span data-stu-id="76af1-141">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="76af1-142">Für einen Reverseproxy gilt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="76af1-142">A reverse proxy:</span></span>

* <span data-ttu-id="76af1-143">Er kann die verfügbar gemachten öffentlichen Oberflächen der von ihm gehosteten Apps einschränken.</span><span class="sxs-lookup"><span data-stu-id="76af1-143">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="76af1-144">Er stellt eine zusätzliche Ebene für Konfiguration und Schutz bereit.</span><span class="sxs-lookup"><span data-stu-id="76af1-144">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="76af1-145">Er lässt sich besser in die vorhandene Infrastruktur integrieren.</span><span class="sxs-lookup"><span data-stu-id="76af1-145">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="76af1-146">Er vereinfacht die Konfiguration von Lastenausgleich und sicherer Kommunikation (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="76af1-146">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="76af1-147">Nur der Reverseproxyserver erfordert ein X.509-Zertifikat, und dieser Server kann mit den Servern der App im internen Netzwerk über einfaches HTTP kommunizieren.</span><span class="sxs-lookup"><span data-stu-id="76af1-147">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="76af1-148">Das Hosting in einer Reverseproxykonfiguration erfordert [Hostfilterung](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="76af1-148">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="kestrel-in-aspnet-core-apps"></a><span data-ttu-id="76af1-149">Kestrel in ASP.NET Core-Apps</span><span class="sxs-lookup"><span data-stu-id="76af1-149">Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="76af1-150">ASP.NET Core-Projektvorlagen verwenden Kestrel standardmäßig.</span><span class="sxs-lookup"><span data-stu-id="76af1-150">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="76af1-151">In *Program.cs* ruft die <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*>-Methode <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>auf:</span><span class="sxs-lookup"><span data-stu-id="76af1-151">In *Program.cs* , the <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> method calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=8)]

<span data-ttu-id="76af1-152">Weitere Informationen zum Erstellen des Hosts finden Sie im Artikel <xref:fundamentals/host/generic-host#set-up-a-host> in den Abschnitten *Einrichten eines Hosts* und *Standardeinstellungen für den Generator*.</span><span class="sxs-lookup"><span data-stu-id="76af1-152">For more information on building the host, see the *Set up a host* and *Default builder settings* sections of <xref:fundamentals/host/generic-host#set-up-a-host>.</span></span>

<span data-ttu-id="76af1-153">Um zusätzliche Konfiguration nach dem Aufruf von `ConfigureWebHostDefaults` bereitzustellen, verwenden Sie `ConfigureKestrel`:</span><span class="sxs-lookup"><span data-stu-id="76af1-153">To provide additional configuration after calling `ConfigureWebHostDefaults`, use `ConfigureKestrel`:</span></span>

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

## <a name="kestrel-options"></a><span data-ttu-id="76af1-154">Kestrel-Optionen</span><span class="sxs-lookup"><span data-stu-id="76af1-154">Kestrel options</span></span>

<span data-ttu-id="76af1-155">Der Kestrel-Webserver verfügt über einschränkende Konfigurationsoptionen, die besonders nützlich bei Bereitstellungen mit Internetzugriff sind.</span><span class="sxs-lookup"><span data-stu-id="76af1-155">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="76af1-156">Legen Sie Einschränkungen für die <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits>-Eigenschaft der <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>-Klasse fest.</span><span class="sxs-lookup"><span data-stu-id="76af1-156">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="76af1-157">Die `Limits`-Eigenschaft enthält eine Instanz der <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>-Klasse.</span><span class="sxs-lookup"><span data-stu-id="76af1-157">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="76af1-158">In den folgenden Beispielen wird der <xref:Microsoft.AspNetCore.Server.Kestrel.Core>-Namespace verwendet:</span><span class="sxs-lookup"><span data-stu-id="76af1-158">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="76af1-159">In den Beispielen, die weiter unten in diesem Artikel aufgeführt sind, werden Kestrel-Optionen in C#-Code konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="76af1-159">In examples shown later in this article, Kestrel options are configured in C# code.</span></span> <span data-ttu-id="76af1-160">Kestrel-Optionen können ebenso mithilfe eines [Konfigurationsanbieters](xref:fundamentals/configuration/index) festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="76af1-160">Kestrel options can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="76af1-161">Beispielsweise kann der [Dateikonfigurationsanbieter](xref:fundamentals/configuration/index#file-configuration-provider) die Kestrel-Konfiguration aus einer *appsettings.json* - oder *appsettings.{Umgebung}.json* -Datei laden:</span><span class="sxs-lookup"><span data-stu-id="76af1-161">For example, the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider) can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

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
> <span data-ttu-id="76af1-162"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> und die [Endpunktkonfiguration](#endpoint-configuration) können über Konfigurationsanbieter konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="76af1-162"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> and [endpoint configuration](#endpoint-configuration) are configurable from configuration providers.</span></span> <span data-ttu-id="76af1-163">Die verbleibende Kestrel-Konfiguration muss in C#-Code konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="76af1-163">Remaining Kestrel configuration must be configured in C# code.</span></span>

<span data-ttu-id="76af1-164">Verwenden Sie **einen** der folgenden Ansätze:</span><span class="sxs-lookup"><span data-stu-id="76af1-164">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="76af1-165">Konfigurieren Sie Kestrel in `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="76af1-165">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="76af1-166">Fügen Sie eine Instanz von `IConfiguration` in die `Startup`-Klasse ein.</span><span class="sxs-lookup"><span data-stu-id="76af1-166">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="76af1-167">Im folgenden Beispiel wird davon ausgegangen, dass die eingefügte Konfiguration der `Configuration`-Eigenschaft zugewiesen wird.</span><span class="sxs-lookup"><span data-stu-id="76af1-167">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="76af1-168">Laden Sie in `Startup.ConfigureServices` den Abschnitt `Kestrel` der Konfiguration in die Konfiguration von Kestrel:</span><span class="sxs-lookup"><span data-stu-id="76af1-168">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

* <span data-ttu-id="76af1-169">Konfigurieren Sie Kestrel beim Erstellen des Hosts:</span><span class="sxs-lookup"><span data-stu-id="76af1-169">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="76af1-170">Laden Sie in *Program.cs* den Abschnitt `Kestrel` der Konfiguration in die Konfiguration von Kestrel:</span><span class="sxs-lookup"><span data-stu-id="76af1-170">In *Program.cs* , load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

<span data-ttu-id="76af1-171">Beide vorangehenden Ansätze funktionieren mit jedem [Konfigurationsanbieter](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="76af1-171">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="76af1-172">Keep-Alive-Timeout</span><span class="sxs-lookup"><span data-stu-id="76af1-172">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="76af1-173">Ruft das [Keep-Alive-Timeout](https://tools.ietf.org/html/rfc7230#section-6.5) ab oder legt es fest.</span><span class="sxs-lookup"><span data-stu-id="76af1-173">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="76af1-174">Standardwert: 2 Minuten.</span><span class="sxs-lookup"><span data-stu-id="76af1-174">Defaults to 2 minutes.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=19-20)]

### <a name="maximum-client-connections"></a><span data-ttu-id="76af1-175">Maximale Anzahl der Clientverbindungen</span><span class="sxs-lookup"><span data-stu-id="76af1-175">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="76af1-176">Die maximale Anzahl von gleichzeitig geöffneten TCP-Verbindungen kann mithilfe von folgendem Code für die gesamte App festgelegt werden:</span><span class="sxs-lookup"><span data-stu-id="76af1-176">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

<span data-ttu-id="76af1-177">Es gibt einen separaten Grenzwert für Verbindungen, die von HTTP oder HTTPS auf ein anderes Protokoll aktualisiert wurden (z.B. auf eine WebSockets-Anforderung).</span><span class="sxs-lookup"><span data-stu-id="76af1-177">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="76af1-178">Nachdem eine Verbindung aktualisiert wurde, zählt diese nicht mehr für den `MaxConcurrentConnections`-Grenzwert.</span><span class="sxs-lookup"><span data-stu-id="76af1-178">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

<span data-ttu-id="76af1-179">Die maximale Anzahl von Verbindungen ist standardmäßig nicht begrenzt (NULL).</span><span class="sxs-lookup"><span data-stu-id="76af1-179">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="76af1-180">Maximale Größe des Anforderungstexts</span><span class="sxs-lookup"><span data-stu-id="76af1-180">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="76af1-181">Die maximale Größe des Anforderungstexts beträgt standardmäßig 30.000.000 Byte, also ungefähr 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="76af1-181">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="76af1-182">Die empfohlene Methode zur Außerkraftsetzung des Grenzwerts in einer ASP.NET Core-MVC-App besteht im Verwenden des <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute>-Attributs in einer Aktionsmethode:</span><span class="sxs-lookup"><span data-stu-id="76af1-182">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="76af1-183">Im folgenden Beispiel wird veranschaulicht, wie die Einschränkungen auf jeder Anforderung für die App konfiguriert werden:</span><span class="sxs-lookup"><span data-stu-id="76af1-183">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

<span data-ttu-id="76af1-184">Außer Kraft setzen der Einstellung für eine bestimmte Anforderung in Middleware:</span><span class="sxs-lookup"><span data-stu-id="76af1-184">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="76af1-185">Eine Ausnahme wird ausgelöst, wenn die App den Grenzwert einer Anforderung konfiguriert, nachdem die App bereits mit dem Lesen der Anforderung begonnen hat.</span><span class="sxs-lookup"><span data-stu-id="76af1-185">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="76af1-186">Es gibt eine `IsReadOnly`-Eigenschaft, die angibt, wenn sich die `MaxRequestBodySize`-Eigenschaft im schreibgeschützten Zustand befindet, also wenn der Grenzwert nicht mehr konfiguriert werden kann.</span><span class="sxs-lookup"><span data-stu-id="76af1-186">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="76af1-187">Wenn eine App [prozessextern](xref:host-and-deploy/iis/index#out-of-process-hosting-model) hinter dem [ASP.NET Core-Modul](xref:host-and-deploy/aspnet-core-module) ausgeführt wird, ist das Größenlimit von Kestrel für Anforderungstext deaktiviert, weil IIS dieses Limit bereits festlegt.</span><span class="sxs-lookup"><span data-stu-id="76af1-187">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="76af1-188">Minimale Datenrate des Anforderungstexts</span><span class="sxs-lookup"><span data-stu-id="76af1-188">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="76af1-189">Kestrel überprüft sekündlich, ob Daten mit der angegebenen Rate in Bytes/Sekunde eingehen.</span><span class="sxs-lookup"><span data-stu-id="76af1-189">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="76af1-190">Wenn die Rate den mindestens erforderlichen Wert unterschreitet, wird für die Verbindung wegen Timeout getrennt. Bei der Toleranzperiode handelt es sich um die Zeitspanne, die Kestrel dem Client gewährt, um die Senderate auf den mindestens erforderlichen Wert zu erhöhen. Die Rate wird währenddessen nicht überprüft.</span><span class="sxs-lookup"><span data-stu-id="76af1-190">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="76af1-191">Diese Toleranzperiode beugt dem Trennen von Verbindungen vor, die Daten aufgrund eines langsamen TCP-Starts anfänglich mit einer niedrigen Rate senden.</span><span class="sxs-lookup"><span data-stu-id="76af1-191">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="76af1-192">Die mindestens erforderliche Rate beträgt standardmäßig 240 Bytes/Sekunde mit einer Toleranzperiode von 5 Sekunden.</span><span class="sxs-lookup"><span data-stu-id="76af1-192">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="76af1-193">Für die Antwort gilt ebenfalls eine mindestens erforderliche Rate.</span><span class="sxs-lookup"><span data-stu-id="76af1-193">A minimum rate also applies to the response.</span></span> <span data-ttu-id="76af1-194">Der Code zum Festlegen des Grenzwerts für Anforderung und Antwort ist abgesehen von `RequestBody` oder `Response` in den Namen von Eigenschaften und Schnittstelle identisch.</span><span class="sxs-lookup"><span data-stu-id="76af1-194">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="76af1-195">In diesem Beispiel wird veranschaulicht, wie Sie die mindestens erforderlichen Datenraten in *Program.cs* konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="76af1-195">Here's an example that shows how to configure the minimum data rates in *Program.cs* :</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-11)]

<span data-ttu-id="76af1-196">Außer Kraft setzen des minimalen Ratenlimits pro Anforderung in Middleware:</span><span class="sxs-lookup"><span data-stu-id="76af1-196">Override the minimum rate limits per request in middleware:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

<span data-ttu-id="76af1-197">Das <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature> aus dem vorherigen Beispiel ist in `HttpContext.Features` für HTTP/2-Anforderungen nicht vorhanden, weil die Änderung von Ratenlimits für jede Anforderung aufgrund der Unterstützung für Anforderungsmultiplexing von HTTP/2 von diesem Protokoll unterstützt wird.</span><span class="sxs-lookup"><span data-stu-id="76af1-197">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature> referenced in the prior sample is not present in `HttpContext.Features` for HTTP/2 requests because modifying rate limits on a per-request basis is generally not supported for HTTP/2 due to the protocol's support for request multiplexing.</span></span> <span data-ttu-id="76af1-198"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> ist jedoch in `HttpContext.Features` für HTTP/2-Anforderungen noch vorhanden, weil das Leseratenlimit weiterhin für jede Anforderung *vollständig deaktiviert* werden kann, indem `IHttpMinRequestBodyDataRateFeature.MinDataRate` auch für eine HTTP/2-Anforderung auf `null` festgelegt wird.</span><span class="sxs-lookup"><span data-stu-id="76af1-198">However, the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> is still present `HttpContext.Features` for HTTP/2 requests, because the read rate limit can still be *disabled entirely* on a per-request basis by setting `IHttpMinRequestBodyDataRateFeature.MinDataRate` to `null` even for an HTTP/2 request.</span></span> <span data-ttu-id="76af1-199">Der Versuch, `IHttpMinRequestBodyDataRateFeature.MinDataRate` zu lesen oder auf einen anderen Wert als `null` festzulegen, führt dazu, dass bei einer HTTP/2-Anforderung eine `NotSupportedException` ausgelöst wird.</span><span class="sxs-lookup"><span data-stu-id="76af1-199">Attempting to read `IHttpMinRequestBodyDataRateFeature.MinDataRate` or attempting to set it to a value other than `null` will result in a `NotSupportedException` being thrown given an HTTP/2 request.</span></span>

<span data-ttu-id="76af1-200">Serverweite Ratenlimits, die über `KestrelServerOptions.Limits` konfiguriert sind, gelten auch für HTTP/1.x- und HTTP/2-Verbindungen.</span><span class="sxs-lookup"><span data-stu-id="76af1-200">Server-wide rate limits configured via `KestrelServerOptions.Limits` still apply to both HTTP/1.x and HTTP/2 connections.</span></span>

### <a name="request-headers-timeout"></a><span data-ttu-id="76af1-201">Timeout für Anforderungsheader</span><span class="sxs-lookup"><span data-stu-id="76af1-201">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="76af1-202">Ruft die maximale Zeitspanne ab, während der der Server Anforderungsheader empfängt, oder legt diese fest.</span><span class="sxs-lookup"><span data-stu-id="76af1-202">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="76af1-203">Der Standardwert beträgt 30 Sekunden.</span><span class="sxs-lookup"><span data-stu-id="76af1-203">Defaults to 30 seconds.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=21-22)]

### <a name="maximum-streams-per-connection"></a><span data-ttu-id="76af1-204">Maximale Datenströme pro Verbindung</span><span class="sxs-lookup"><span data-stu-id="76af1-204">Maximum streams per connection</span></span>

<span data-ttu-id="76af1-205">`Http2.MaxStreamsPerConnection` schränkt die Anzahl gleichzeitiger Anforderungsdatenströme pro HTTP/2-Verbindung ein.</span><span class="sxs-lookup"><span data-stu-id="76af1-205">`Http2.MaxStreamsPerConnection` limits the number of concurrent request streams per HTTP/2 connection.</span></span> <span data-ttu-id="76af1-206">Überschüssige Datenströme werden zurückgewiesen.</span><span class="sxs-lookup"><span data-stu-id="76af1-206">Excess streams are refused.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
});
```

<span data-ttu-id="76af1-207">Der Standardwert ist 100.</span><span class="sxs-lookup"><span data-stu-id="76af1-207">The default value is 100.</span></span>

### <a name="header-table-size"></a><span data-ttu-id="76af1-208">Größe der Headertabelle</span><span class="sxs-lookup"><span data-stu-id="76af1-208">Header table size</span></span>

<span data-ttu-id="76af1-209">Der HPACK-Decoder dekomprimiert HTTP-Header für HTTP/2-Verbindungen.</span><span class="sxs-lookup"><span data-stu-id="76af1-209">The HPACK decoder decompresses HTTP headers for HTTP/2 connections.</span></span> <span data-ttu-id="76af1-210">`Http2.HeaderTableSize` schränkt die Größe der Headerkomprimierungstabelle ein, die der HPACK-Decoder verwendet.</span><span class="sxs-lookup"><span data-stu-id="76af1-210">`Http2.HeaderTableSize` limits the size of the header compression table that the HPACK decoder uses.</span></span> <span data-ttu-id="76af1-211">Der Wert wird in Oktetten bereitgestellt und muss größer als null (0) sein.</span><span class="sxs-lookup"><span data-stu-id="76af1-211">The value is provided in octets and must be greater than zero (0).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.HeaderTableSize = 4096;
});
```

<span data-ttu-id="76af1-212">Der Standardwert ist 4096.</span><span class="sxs-lookup"><span data-stu-id="76af1-212">The default value is 4096.</span></span>

### <a name="maximum-frame-size"></a><span data-ttu-id="76af1-213">Maximale Framegröße</span><span class="sxs-lookup"><span data-stu-id="76af1-213">Maximum frame size</span></span>

<span data-ttu-id="76af1-214">`Http2.MaxFrameSize` gibt die maximal zulässige Größe einer vom Server empfangenen oder gesendeten HTTP/2-Verbindungsrahmen-Nutzlast an.</span><span class="sxs-lookup"><span data-stu-id="76af1-214">`Http2.MaxFrameSize` indicates the maximum allowed size of an HTTP/2 connection frame payload received or sent by the server.</span></span> <span data-ttu-id="76af1-215">Der Wert wird in Oktetten bereitgestellt und muss zwischen 2^14 (16.384) und 2^24-1 (16.777.215) liegen.</span><span class="sxs-lookup"><span data-stu-id="76af1-215">The value is provided in octets and must be between 2^14 (16,384) and 2^24-1 (16,777,215).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxFrameSize = 16384;
});
```

<span data-ttu-id="76af1-216">Der Standardwert ist 2^14 (16.384).</span><span class="sxs-lookup"><span data-stu-id="76af1-216">The default value is 2^14 (16,384).</span></span>

### <a name="maximum-request-header-size"></a><span data-ttu-id="76af1-217">Maximale Größe des Anforderungsheaders</span><span class="sxs-lookup"><span data-stu-id="76af1-217">Maximum request header size</span></span>

<span data-ttu-id="76af1-218">`Http2.MaxRequestHeaderFieldSize` gibt die maximal zulässige Größe in Oktetten der Anforderungsheaderwerte an.</span><span class="sxs-lookup"><span data-stu-id="76af1-218">`Http2.MaxRequestHeaderFieldSize` indicates the maximum allowed size in octets of request header values.</span></span> <span data-ttu-id="76af1-219">Dieser Grenzwert gilt sowohl für den Namen als auch den Wert in der komprimierten und nicht komprimierten Darstellung.</span><span class="sxs-lookup"><span data-stu-id="76af1-219">This limit applies to both name and value in their compressed and uncompressed representations.</span></span> <span data-ttu-id="76af1-220">Der Wert muss größer als 0 (null) sein.</span><span class="sxs-lookup"><span data-stu-id="76af1-220">The value must be greater than zero (0).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
});
```

<span data-ttu-id="76af1-221">Der Standardwert ist 8.192.</span><span class="sxs-lookup"><span data-stu-id="76af1-221">The default value is 8,192.</span></span>

### <a name="initial-connection-window-size"></a><span data-ttu-id="76af1-222">Anfangsfenstergröße der Verbindung</span><span class="sxs-lookup"><span data-stu-id="76af1-222">Initial connection window size</span></span>

<span data-ttu-id="76af1-223">`Http2.InitialConnectionWindowSize` gibt die maximalen Anforderungstextdaten in Byte an, die der Server auf einmal für alle Anforderungen (Streams) pro Verbindung aggregiert.</span><span class="sxs-lookup"><span data-stu-id="76af1-223">`Http2.InitialConnectionWindowSize` indicates the maximum request body data in bytes the server buffers at one time aggregated across all requests (streams) per connection.</span></span> <span data-ttu-id="76af1-224">Anforderungen werden auch durch `Http2.InitialStreamWindowSize` beschränkt.</span><span class="sxs-lookup"><span data-stu-id="76af1-224">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="76af1-225">Der Wert muss größer als oder gleich 65.535 und kleiner als 2^31 (2.147.483.648) sein.</span><span class="sxs-lookup"><span data-stu-id="76af1-225">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
});
```

<span data-ttu-id="76af1-226">Der Standardwert ist 128 KB (131.072).</span><span class="sxs-lookup"><span data-stu-id="76af1-226">The default value is 128 KB (131,072).</span></span>

### <a name="initial-stream-window-size"></a><span data-ttu-id="76af1-227">Anfangsfenstergröße des Streams</span><span class="sxs-lookup"><span data-stu-id="76af1-227">Initial stream window size</span></span>

<span data-ttu-id="76af1-228">`Http2.InitialStreamWindowSize` gibt die maximalen Anforderungstextdaten in Byte an, die der Server auf einmal pro Anforderung (Stream) puffert.</span><span class="sxs-lookup"><span data-stu-id="76af1-228">`Http2.InitialStreamWindowSize` indicates the maximum request body data in bytes the server buffers at one time per request (stream).</span></span> <span data-ttu-id="76af1-229">Anforderungen werden auch durch `Http2.InitialConnectionWindowSize` beschränkt.</span><span class="sxs-lookup"><span data-stu-id="76af1-229">Requests are also limited by `Http2.InitialConnectionWindowSize`.</span></span> <span data-ttu-id="76af1-230">Der Wert muss größer als oder gleich 65.535 und kleiner als 2^31 (2.147.483.648) sein.</span><span class="sxs-lookup"><span data-stu-id="76af1-230">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
});
```

<span data-ttu-id="76af1-231">Der Standardwert ist 96 KB (98.304).</span><span class="sxs-lookup"><span data-stu-id="76af1-231">The default value is 96 KB (98,304).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

### <a name="http2-keep-alive-ping-configuration"></a><span data-ttu-id="76af1-232">Konfiguration für HTTP/2-Keep-Alive-Pings</span><span class="sxs-lookup"><span data-stu-id="76af1-232">HTTP/2 keep alive ping configuration</span></span>

<span data-ttu-id="76af1-233">Kestrel kann so konfiguriert werden, dass HTTP/2-Pings an verbundene Clients gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="76af1-233">Kestrel can be configured to send HTTP/2 pings to connected clients.</span></span> <span data-ttu-id="76af1-234">HTTP/2-Pings dienen mehreren Zwecken:</span><span class="sxs-lookup"><span data-stu-id="76af1-234">HTTP/2 pings serve multiple purposes:</span></span>

* <span data-ttu-id="76af1-235">Verbindungen im Leerlauf werden aktiv gehalten.</span><span class="sxs-lookup"><span data-stu-id="76af1-235">Keep idle connections alive.</span></span> <span data-ttu-id="76af1-236">Einige Clients und Proxyserver trennen Verbindungen, die sich im Leerlauf befinden.</span><span class="sxs-lookup"><span data-stu-id="76af1-236">Some clients and proxy servers close connections that are idle.</span></span> <span data-ttu-id="76af1-237">HTTP/2-Pings werden als Aktivität einer Verbindung betrachtet und verhindern, dass die Verbindung getrennt wird.</span><span class="sxs-lookup"><span data-stu-id="76af1-237">HTTP/2 pings are considered as activity on a connection and prevent the connection from being closed as idle.</span></span>
* <span data-ttu-id="76af1-238">Fehlerhafte Verbindungen werden getrennt.</span><span class="sxs-lookup"><span data-stu-id="76af1-238">Close unhealthy connections.</span></span> <span data-ttu-id="76af1-239">Verbindungen, bei denen der Client nicht innerhalb der konfigurierten Zeit auf einen Keep-Alive-Ping antwortet, werden vom Server getrennt.</span><span class="sxs-lookup"><span data-stu-id="76af1-239">Connections where the client doesn't respond to the keep alive ping in the configured time are closed by the server.</span></span>

<span data-ttu-id="76af1-240">Es gibt zwei Konfigurationsoptionen für HTTP/2-Keep-Alive-Pings:</span><span class="sxs-lookup"><span data-stu-id="76af1-240">There are two configuration options related to HTTP/2 keep alive pings:</span></span>

* <span data-ttu-id="76af1-241">`Http2.KeepAlivePingInterval` ist eine `TimeSpan`, die den Ping intern konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="76af1-241">`Http2.KeepAlivePingInterval` is a `TimeSpan` that configures the ping internal.</span></span> <span data-ttu-id="76af1-242">Der Server sendet einen Keep-Alive-Ping an den Client, wenn dieser für diesen Zeitraum keine Frames erhält.</span><span class="sxs-lookup"><span data-stu-id="76af1-242">The server sends a keep alive ping to the client if it doesn't receive any frames for this period of time.</span></span> <span data-ttu-id="76af1-243">Durch Festlegen dieser Option auf `TimeSpan.MaxValue` werden Keep-Alive-Pings deaktiviert.</span><span class="sxs-lookup"><span data-stu-id="76af1-243">Keep alive pings are disabled when this option is set to `TimeSpan.MaxValue`.</span></span> <span data-ttu-id="76af1-244">Standardwert: `TimeSpan.MaxValue`.</span><span class="sxs-lookup"><span data-stu-id="76af1-244">The default value is `TimeSpan.MaxValue`.</span></span>
* <span data-ttu-id="76af1-245">`Http2.KeepAlivePingTimeout` ist eine `TimeSpan`, die das Ping-Timeout konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="76af1-245">`Http2.KeepAlivePingTimeout` is a `TimeSpan` that configures the ping timeout.</span></span> <span data-ttu-id="76af1-246">Wenn der Server während dieses Timeouts keine Frames (z. B. als Antwort-Ping) empfängt, wird die Verbindung getrennt.</span><span class="sxs-lookup"><span data-stu-id="76af1-246">If the server doesn't receive any frames, such as a response ping, during this timeout then the connection is closed.</span></span> <span data-ttu-id="76af1-247">Durch Festlegen dieser Option auf `TimeSpan.MaxValue` wird das Keep-Alive-Timeout deaktiviert.</span><span class="sxs-lookup"><span data-stu-id="76af1-247">Keep alive timeout is disabled when this option is set to `TimeSpan.MaxValue`.</span></span> <span data-ttu-id="76af1-248">Der Standardwert ist 20 Sekunden.</span><span class="sxs-lookup"><span data-stu-id="76af1-248">The default value is 20 seconds.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.KeepAlivePingInterval = TimeSpan.FromSeconds(30);
    serverOptions.Limits.Http2.KeepAlivePingTimeout = TimeSpan.FromSeconds(60);
});
```

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="trailers"></a><span data-ttu-id="76af1-249">Trailer</span><span class="sxs-lookup"><span data-stu-id="76af1-249">Trailers</span></span>

[!INCLUDE[](~/includes/trailers.md)]

### <a name="reset"></a><span data-ttu-id="76af1-250">Reset</span><span class="sxs-lookup"><span data-stu-id="76af1-250">Reset</span></span>

[!INCLUDE[](~/includes/reset.md)]

### <a name="synchronous-io"></a><span data-ttu-id="76af1-251">Synchrone E/A-Vorgänge</span><span class="sxs-lookup"><span data-stu-id="76af1-251">Synchronous I/O</span></span>

<span data-ttu-id="76af1-252"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> steuert, ob für Anforderung und Antwort synchrone E/A-Vorgänge zulässig sind.</span><span class="sxs-lookup"><span data-stu-id="76af1-252"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous I/O is allowed for the request and response.</span></span> <span data-ttu-id="76af1-253">Der Standardwert ist `false`.</span><span class="sxs-lookup"><span data-stu-id="76af1-253">The default value is `false`.</span></span>

> [!WARNING]
> <span data-ttu-id="76af1-254">Sehr viele blockierende synchrone E/A-Vorgänge können zu einem Ressourcenmangel im Threadpool führen, wodurch die App nicht mehr reagiert.</span><span class="sxs-lookup"><span data-stu-id="76af1-254">A large number of blocking synchronous I/O operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="76af1-255">Aktivieren Sie `AllowSynchronousIO` nur bei Verwendung einer Bibliothek, die asynchrone E/A-Vorgänge nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="76af1-255">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous I/O.</span></span>

<span data-ttu-id="76af1-256">Das folgende Beispiel aktiviert synchrone E/A-Vorgänge:</span><span class="sxs-lookup"><span data-stu-id="76af1-256">The following example enables synchronous I/O:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

<span data-ttu-id="76af1-257">Weitere Informationen zu anderen Kestrel-Optionen und -Einschränkungen finden Sie unter:</span><span class="sxs-lookup"><span data-stu-id="76af1-257">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="76af1-258">Endpunktkonfiguration</span><span class="sxs-lookup"><span data-stu-id="76af1-258">Endpoint configuration</span></span>

<span data-ttu-id="76af1-259">Standardmäßig wird ASP.NET Core an Folgendes gebunden:</span><span class="sxs-lookup"><span data-stu-id="76af1-259">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="76af1-260">`https://localhost:5001` (wenn ein lokales Entwicklungszertifikat vorhanden ist)</span><span class="sxs-lookup"><span data-stu-id="76af1-260">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="76af1-261">Verwenden Sie Folgendes zum Angeben der URLs:</span><span class="sxs-lookup"><span data-stu-id="76af1-261">Specify URLs using the:</span></span>

* <span data-ttu-id="76af1-262">Die Umgebungsvariable `ASPNETCORE_URLS`</span><span class="sxs-lookup"><span data-stu-id="76af1-262">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="76af1-263">Das Befehlszeilenargument `--urls`</span><span class="sxs-lookup"><span data-stu-id="76af1-263">`--urls` command-line argument.</span></span>
* <span data-ttu-id="76af1-264">Den Hostkonfigurationsschlüssel `urls`</span><span class="sxs-lookup"><span data-stu-id="76af1-264">`urls` host configuration key.</span></span>
* <span data-ttu-id="76af1-265">Die Erweiterungsmethode `UseUrls`</span><span class="sxs-lookup"><span data-stu-id="76af1-265">`UseUrls` extension method.</span></span>

<span data-ttu-id="76af1-266">Der Wert, der mit diesen Ansätzen angegeben wird, kann mindestens ein HTTP- oder HTTPS-Endpunkt sein (HTTPS wenn ein Standardzertifikat verfügbar ist).</span><span class="sxs-lookup"><span data-stu-id="76af1-266">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="76af1-267">Konfigurieren Sie den Wert als eine durch Semikolons getrennte Liste (z.B. `"Urls": "http://localhost:8000;http://localhost:8001"`).</span><span class="sxs-lookup"><span data-stu-id="76af1-267">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="76af1-268">Weitere Informationen zu diesen Ansätzen finden Sie unter [Server-URLs](xref:fundamentals/host/web-host#server-urls) und [Außerkraftsetzen der Konfiguration](xref:fundamentals/host/web-host#override-configuration).</span><span class="sxs-lookup"><span data-stu-id="76af1-268">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="76af1-269">Ein Entwicklungszertifikat wird erstellt:</span><span class="sxs-lookup"><span data-stu-id="76af1-269">A development certificate is created:</span></span>

* <span data-ttu-id="76af1-270">Wenn das [.NET Core SDK](/dotnet/core/sdk) installiert wird.</span><span class="sxs-lookup"><span data-stu-id="76af1-270">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="76af1-271">Das [dev-cert-Tool](xref:aspnetcore-2.1#https) wird zum Erstellen eines Zertifikats verwendet.</span><span class="sxs-lookup"><span data-stu-id="76af1-271">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="76af1-272">Einige Browser erfordern, dass Sie die explizite Berechtigung erteilen, dem lokalen Entwicklungszertifikat zu vertrauen.</span><span class="sxs-lookup"><span data-stu-id="76af1-272">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="76af1-273">Projektvorlagen konfigurieren Apps, damit sie standardmäßig auf HTTPS ausgeführt werden und die [HTTPS-Umleitung und HSTS-Unterstützung](xref:security/enforcing-ssl) enthalten.</span><span class="sxs-lookup"><span data-stu-id="76af1-273">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="76af1-274">Rufen Sie die <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*>- oder <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*>-Methode unter <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> auf, um URL-Präfixe und Ports für Kestrel zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="76af1-274">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="76af1-275">`UseUrls`, das Befehlszeilenargument `--urls`, der Hostkonfigurationsschlüssel `urls` und die Umgebungsvariable `ASPNETCORE_URLS` funktionieren ebenfalls, verfügen jedoch über Einschränkungen, die im Verlauf dieses Abschnitts erläutert werden (Ein Standardzertifikat muss für die HTTPS-Endpunktkonfiguration verfügbar sein).</span><span class="sxs-lookup"><span data-stu-id="76af1-275">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="76af1-276">`KestrelServerOptions`-Konfiguration:</span><span class="sxs-lookup"><span data-stu-id="76af1-276">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="76af1-277">ConfigureEndpointDefaults(Action\<ListenOptions>)</span><span class="sxs-lookup"><span data-stu-id="76af1-277">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="76af1-278">Gibt die Konfiguration von `Action` zum Ausführen von jedem angegebenen Endpunkt an.</span><span class="sxs-lookup"><span data-stu-id="76af1-278">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="76af1-279">Mehrmalige Aufrufe von `ConfigureEndpointDefaults` ersetzen vorherige Instanzen von `Action` mit der zuletzt angegebenen `Action`.</span><span class="sxs-lookup"><span data-stu-id="76af1-279">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureEndpointDefaults(listenOptions =>
    {
        // Configure endpoint defaults
    });
});
```

> [!NOTE]
> <span data-ttu-id="76af1-280">Auf Endpunkte, die durch Aufrufen von <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **vor** dem Aufrufen von <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> erstellt werden, werden die Standardwerte nicht angewendet.</span><span class="sxs-lookup"><span data-stu-id="76af1-280">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> won't have the defaults applied.</span></span>

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="76af1-281">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span><span class="sxs-lookup"><span data-stu-id="76af1-281">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="76af1-282">Gibt die Konfiguration von `Action` zum Ausführen von jedem HTTPS-Endpunkt an.</span><span class="sxs-lookup"><span data-stu-id="76af1-282">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="76af1-283">Mehrmalige Aufrufe von `ConfigureHttpsDefaults` ersetzen vorherige Instanzen von `Action` mit der zuletzt angegebenen `Action`.</span><span class="sxs-lookup"><span data-stu-id="76af1-283">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureHttpsDefaults(listenOptions =>
    {
        // certificate is an X509Certificate2
        listenOptions.ServerCertificate = certificate;
    });
});
```

> [!NOTE]
> <span data-ttu-id="76af1-284">Auf Endpunkte, die durch Aufrufen von <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **vor** dem Aufrufen von <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> erstellt werden, werden die Standardwerte nicht angewendet.</span><span class="sxs-lookup"><span data-stu-id="76af1-284">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>

### <a name="configureiconfiguration"></a><span data-ttu-id="76af1-285">Configure(IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="76af1-285">Configure(IConfiguration)</span></span>

<span data-ttu-id="76af1-286">Erstellt ein Konfigurationsladeprogramm für das Einrichten von Kestrel, was <xref:Microsoft.Extensions.Configuration.IConfiguration> als Eingabe erfordert.</span><span class="sxs-lookup"><span data-stu-id="76af1-286">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="76af1-287">Die Konfiguration muss auf den Konfigurationsabschnitt für Kestrel festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="76af1-287">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="76af1-288">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="76af1-288">ListenOptions.UseHttps</span></span>

<span data-ttu-id="76af1-289">Konfiguriert Kestrel zur Verwendung von HTTPS.</span><span class="sxs-lookup"><span data-stu-id="76af1-289">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="76af1-290">`ListenOptions.UseHttps`-Erweiterungen:</span><span class="sxs-lookup"><span data-stu-id="76af1-290">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="76af1-291">`UseHttps`: Hiermit wird Kestrel zur Verwendung von HTTPS mit dem Standardzertifikat konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="76af1-291">`UseHttps`: Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="76af1-292">Löst eine Ausnahme aus, wenn kein Standardzertifikat konfiguriert ist.</span><span class="sxs-lookup"><span data-stu-id="76af1-292">Throws an exception if no default certificate is configured.</span></span>
* `UseHttps(string fileName)`
* `UseHttps(string fileName, string password)`
* `UseHttps(string fileName, string password, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(StoreName storeName, string subject)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(X509Certificate2 serverCertificate)`
* `UseHttps(X509Certificate2 serverCertificate, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(Action<HttpsConnectionAdapterOptions> configureOptions)`

<span data-ttu-id="76af1-293">`ListenOptions.UseHttps`-Parameter:</span><span class="sxs-lookup"><span data-stu-id="76af1-293">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="76af1-294">`filename` entspricht dem Pfad und Dateinamen einer Zertifikatdatei relativ zu dem Verzeichnis, das die Inhaltsdateien der App enthält.</span><span class="sxs-lookup"><span data-stu-id="76af1-294">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="76af1-295">`password` ist das für den Zugriff auf die X.509-Zertifikatsdaten erforderliche Kennwort.</span><span class="sxs-lookup"><span data-stu-id="76af1-295">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="76af1-296">`configureOptions` ist eine `Action` zum Konfigurieren von `HttpsConnectionAdapterOptions`.</span><span class="sxs-lookup"><span data-stu-id="76af1-296">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="76af1-297">Gibt `ListenOptions` zurück.</span><span class="sxs-lookup"><span data-stu-id="76af1-297">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="76af1-298">`storeName` ist der Zertifikatspeicher, aus dem das Zertifikat geladen wird.</span><span class="sxs-lookup"><span data-stu-id="76af1-298">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="76af1-299">`subject` ist der Name des Antragstellers für das Zertifikat.</span><span class="sxs-lookup"><span data-stu-id="76af1-299">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="76af1-300">`allowInvalid` gibt an, ob ungültige Zertifikate berücksichtigt werden sollten, z.B. selbstsignierte Zertifikate.</span><span class="sxs-lookup"><span data-stu-id="76af1-300">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="76af1-301">`location` ist der Speicherort, aus dem das Zertifikat geladen wird.</span><span class="sxs-lookup"><span data-stu-id="76af1-301">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="76af1-302">`serverCertificate` ist das X.509-Zertifikat.</span><span class="sxs-lookup"><span data-stu-id="76af1-302">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="76af1-303">In der Produktion muss HTTPS explizit konfiguriert sein.</span><span class="sxs-lookup"><span data-stu-id="76af1-303">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="76af1-304">Zumindest muss ein Standardzertifikat angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="76af1-304">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="76af1-305">Die im Folgenden beschriebenen unterstützten Konfigurationen:</span><span class="sxs-lookup"><span data-stu-id="76af1-305">Supported configurations described next:</span></span>

* <span data-ttu-id="76af1-306">Keine Konfiguration</span><span class="sxs-lookup"><span data-stu-id="76af1-306">No configuration</span></span>
* <span data-ttu-id="76af1-307">Ersetzen des Standardzertifikats aus der Konfiguration</span><span class="sxs-lookup"><span data-stu-id="76af1-307">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="76af1-308">Ändern des Standards im Code</span><span class="sxs-lookup"><span data-stu-id="76af1-308">Change the defaults in code</span></span>

<span data-ttu-id="76af1-309">*Keine Konfiguration*</span><span class="sxs-lookup"><span data-stu-id="76af1-309">*No configuration*</span></span>

<span data-ttu-id="76af1-310">Kestrel überwacht `http://localhost:5000` und `https://localhost:5001` (wenn ein Standardzertifikat verfügbar ist).</span><span class="sxs-lookup"><span data-stu-id="76af1-310">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="76af1-311">*Ersetzen des Standardzertifikats aus der Konfiguration*</span><span class="sxs-lookup"><span data-stu-id="76af1-311">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="76af1-312">`CreateDefaultBuilder` ruft `Configure(context.Configuration.GetSection("Kestrel"))` standardmäßig zum Laden der Kestrel-Konfiguration auf.</span><span class="sxs-lookup"><span data-stu-id="76af1-312">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="76af1-313">Ein Standardkonfigurationsschema für HTTPS-App-Einstellungen ist für Kestrel verfügbar.</span><span class="sxs-lookup"><span data-stu-id="76af1-313">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="76af1-314">Konfigurieren Sie mehrere Endpunkte, einschließlich der zu verwendenden URLs und Zertifikate aus einer Datei auf dem Datenträger oder einem Zertifikatspeicher.</span><span class="sxs-lookup"><span data-stu-id="76af1-314">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="76af1-315">Im folgenden Beispiel für *appsettings.json* gilt:</span><span class="sxs-lookup"><span data-stu-id="76af1-315">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="76af1-316">Legen Sie **AllowInvalid** auf `true` fest, um die Verwendung von ungültigen Zertifikaten zu erlauben (z.B. selbstsignierte Zertifikate).</span><span class="sxs-lookup"><span data-stu-id="76af1-316">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="76af1-317">Jeder HTTPS-Endpunkt, der kein Zertifikat angibt (im folgenden Beispiel **HttpsDefaultCert** ), greift auf das unter **Zertifikate** > **Standard** festgelegte Zertifikat oder das Entwicklungszertifikat zurück.</span><span class="sxs-lookup"><span data-stu-id="76af1-317">Any HTTPS endpoint that doesn't specify a certificate ( **HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "Http": {
        "Url": "http://localhost:5000"
      },
      "HttpsInlineCertFile": {
        "Url": "https://localhost:5001",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      },
      "HttpsInlineCertStore": {
        "Url": "https://localhost:5002",
        "Certificate": {
          "Subject": "<subject; required>",
          "Store": "<certificate store; required>",
          "Location": "<location; defaults to CurrentUser>",
          "AllowInvalid": "<true or false; defaults to false>"
        }
      },
      "HttpsDefaultCert": {
        "Url": "https://localhost:5003"
      },
      "Https": {
        "Url": "https://*:5004",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      }
    },
    "Certificates": {
      "Default": {
        "Path": "<path to .pfx file>",
        "Password": "<certificate password>"
      }
    }
  }
}
```

<span data-ttu-id="76af1-318">Alternativ zur Verwendung von **Pfad** und **Kennwort** für alle Zertifikatknoten können Sie das Zertifikat mithilfe von Zertifikatspeicherfeldern angeben.</span><span class="sxs-lookup"><span data-stu-id="76af1-318">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="76af1-319">Das Zertifikat unter **Zertifikate** > **Standard** kann beispielweise wie folgt angegeben werden:</span><span class="sxs-lookup"><span data-stu-id="76af1-319">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="76af1-320">Schema-Hinweise:</span><span class="sxs-lookup"><span data-stu-id="76af1-320">Schema notes:</span></span>

* <span data-ttu-id="76af1-321">Bei den Namen der Endpunkte wird die Groß-/Kleinschreibung nicht beachtet.</span><span class="sxs-lookup"><span data-stu-id="76af1-321">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="76af1-322">Zum Beispiel sind `HTTPS` und `Https` gültig.</span><span class="sxs-lookup"><span data-stu-id="76af1-322">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="76af1-323">Der Parameter `Url` ist für jeden Endpunkt erforderlich.</span><span class="sxs-lookup"><span data-stu-id="76af1-323">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="76af1-324">Das Format für diesen Parameter ist identisch mit dem allgemeinen Konfigurationsparameter `Urls`, mit der Ausnahme, dass er auf einen einzelnen Wert begrenzt ist.</span><span class="sxs-lookup"><span data-stu-id="76af1-324">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="76af1-325">Diese Endpunkte ersetzen die in der allgemeinen `Urls`-Konfiguration festgelegten Endpunkte, anstatt zu ihnen hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="76af1-325">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="76af1-326">Endpunkte, die über `Listen` in Code definiert werden, werden den im Konfigurationsabschnitt definierten Endpunkten hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="76af1-326">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="76af1-327">Der `Certificate`-Abschnitt ist optional.</span><span class="sxs-lookup"><span data-stu-id="76af1-327">The `Certificate` section is optional.</span></span> <span data-ttu-id="76af1-328">Wenn der `Certificate`-Abschnitt nicht angegeben ist, werden die in früheren Szenarios definierten Standardwerte verwendet.</span><span class="sxs-lookup"><span data-stu-id="76af1-328">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="76af1-329">Wenn keine Standardwerte verfügbar sind, löst der Server eine Ausnahme aus und startet nicht.</span><span class="sxs-lookup"><span data-stu-id="76af1-329">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="76af1-330">Der `Certificate`-Abschnitt unterstützt die Zertifikate **Pfad**&ndash;**Kennwort** und **Subject**&ndash;**Store** (Antragsteller–Speicher).</span><span class="sxs-lookup"><span data-stu-id="76af1-330">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="76af1-331">Auf diese Weise kann eine beliebige Anzahl von Endpunkten definiert werden, solange Sie keine Portkonflikte verursachen.</span><span class="sxs-lookup"><span data-stu-id="76af1-331">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="76af1-332">`options.Configure(context.Configuration.GetSection("{SECTION}"))` gibt `KestrelConfigurationLoader` mit der Methode `.Endpoint(string name, listenOptions => { })` zurück, die dazu verwendet werden kann, die Einstellungen eines Endpunkts zu ergänzen:</span><span class="sxs-lookup"><span data-stu-id="76af1-332">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

```csharp
webBuilder.UseKestrel((context, serverOptions) =>
{
    serverOptions.Configure(context.Configuration.GetSection("Kestrel"))
        .Endpoint("HTTPS", listenOptions =>
        {
            listenOptions.HttpsOptions.SslProtocols = SslProtocols.Tls12;
        });
});
```

<span data-ttu-id="76af1-333">Auf `KestrelServerOptions.ConfigurationLoader` kann direkt zugegriffen werden, um die Iteration auf dem vorhandenen Ladeprogramm fortzusetzen, etwa auf dem von <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> bereitgestellten Ladeprogramm.</span><span class="sxs-lookup"><span data-stu-id="76af1-333">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="76af1-334">Der Konfigurationsabschnitt ist für jeden Endpunkt in den Optionen der Methode `Endpoint` verfügbar, sodass benutzerdefinierte Einstellungen gelesen werden können.</span><span class="sxs-lookup"><span data-stu-id="76af1-334">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="76af1-335">Mehrere Konfigurationen können durch erneutes Aufrufen von `options.Configure(context.Configuration.GetSection("{SECTION}"))` mit einem anderen Abschnitt geladen werden.</span><span class="sxs-lookup"><span data-stu-id="76af1-335">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="76af1-336">Sofern `Load` nicht in einer vorherigen Instanz explizit aufgerufen wird, wird nur die letzte Konfiguration verwendet.</span><span class="sxs-lookup"><span data-stu-id="76af1-336">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="76af1-337">Das Metapaket ruft `Load` nicht auf, sodass der Abschnitt mit der Standardkonfiguration ersetzt werden kann.</span><span class="sxs-lookup"><span data-stu-id="76af1-337">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="76af1-338">`KestrelConfigurationLoader` spiegelt die API-Familie `Listen` von `KestrelServerOptions` als `Endpoint`-Überladungen, weshalb Code und Konfigurationsendpunkte am selben Ort konfiguriert werden können.</span><span class="sxs-lookup"><span data-stu-id="76af1-338">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="76af1-339">Diese Überladungen verwenden keine Namen und nutzen nur Standardeinstellungen aus der Konfiguration.</span><span class="sxs-lookup"><span data-stu-id="76af1-339">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="76af1-340">*Ändern des Standards im Code*</span><span class="sxs-lookup"><span data-stu-id="76af1-340">*Change the defaults in code*</span></span>

<span data-ttu-id="76af1-341">`ConfigureEndpointDefaults` und `ConfigureHttpsDefaults` können zum Ändern der Standardeinstellungen für `ListenOptions` und `HttpsConnectionAdapterOptions` verwendet werden, einschließlich der Standardzertifikate, die im vorherigen Szenario festgelegt wurden.</span><span class="sxs-lookup"><span data-stu-id="76af1-341">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="76af1-342">`ConfigureEndpointDefaults` und `ConfigureHttpsDefaults` sollten aufgerufen werden, bevor Endpunkte konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="76af1-342">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureEndpointDefaults(listenOptions =>
    {
        // Configure endpoint defaults
    });

    serverOptions.ConfigureHttpsDefaults(listenOptions =>
    {
        listenOptions.SslProtocols = SslProtocols.Tls12;
    });
});
```

<span data-ttu-id="76af1-343">*Kestrel-Unterstützung für SNI*</span><span class="sxs-lookup"><span data-stu-id="76af1-343">*Kestrel support for SNI*</span></span>

<span data-ttu-id="76af1-344">Die [Servernamensanzeige (SNI)](https://tools.ietf.org/html/rfc6066#section-3) kann zum Hosten mehrerer Domänen auf der gleichen IP-Adresse und dem gleichen Port verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="76af1-344">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="76af1-345">Damit die Servernamensanzeige funktioniert, sendet der Client während des TLS-Handshakes den Hostnamen für die sichere Sitzung an den Server, sodass der Server das richtige Zertifikat bereitstellen kann.</span><span class="sxs-lookup"><span data-stu-id="76af1-345">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="76af1-346">Der Client verwendet das beigestellte Zertifikat für die verschlüsselte Kommunikation mit dem Server während der sicheren Sitzung nach dem TLS-Handshake.</span><span class="sxs-lookup"><span data-stu-id="76af1-346">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="76af1-347">Kestrel unterstützt die Servernamensanzeige über den `ServerCertificateSelector`-Rückruf.</span><span class="sxs-lookup"><span data-stu-id="76af1-347">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="76af1-348">Der Rückruf wird für jede Verbindung einmal aufgerufen, um der App zu ermöglichen, den Hostnamen zu überprüfen und das entsprechende Zertifikat auszuwählen.</span><span class="sxs-lookup"><span data-stu-id="76af1-348">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="76af1-349">Für die Unterstützung der Servernamensanzeige benötigen Sie Folgendes:</span><span class="sxs-lookup"><span data-stu-id="76af1-349">SNI support requires:</span></span>

* <span data-ttu-id="76af1-350">Wird auf dem Zielframework `netcoreapp2.1` oder höher ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="76af1-350">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="76af1-351">In `net461` oder höher, wird der Rückruf aufgerufen, `name` ist aber immer `null`.</span><span class="sxs-lookup"><span data-stu-id="76af1-351">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="76af1-352">`name` ist auch `null`, wenn der Client den Hostnamenparameter nicht im TLS-Handshake angibt.</span><span class="sxs-lookup"><span data-stu-id="76af1-352">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="76af1-353">Alle Websites werden in derselben Kestrel-Instanz ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="76af1-353">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="76af1-354">Kestrel unterstützt ohne Reverseproxy keine gemeinsame IP-Adresse und keinen gemeinsamen Port für mehrere Instanzen.</span><span class="sxs-lookup"><span data-stu-id="76af1-354">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ListenAnyIP(5005, listenOptions =>
    {
        listenOptions.UseHttps(httpsOptions =>
        {
            var localhostCert = CertificateLoader.LoadFromStoreCert(
                "localhost", "My", StoreLocation.CurrentUser,
                allowInvalid: true);
            var exampleCert = CertificateLoader.LoadFromStoreCert(
                "example.com", "My", StoreLocation.CurrentUser,
                allowInvalid: true);
            var subExampleCert = CertificateLoader.LoadFromStoreCert(
                "sub.example.com", "My", StoreLocation.CurrentUser,
                allowInvalid: true);
            var certs = new Dictionary<string, X509Certificate2>(
                StringComparer.OrdinalIgnoreCase);
            certs["localhost"] = localhostCert;
            certs["example.com"] = exampleCert;
            certs["sub.example.com"] = subExampleCert;

            httpsOptions.ServerCertificateSelector = (connectionContext, name) =>
            {
                if (name != null && certs.TryGetValue(name, out var cert))
                {
                    return cert;
                }

                return exampleCert;
            };
        });
    });
});
```

### <a name="connection-logging"></a><span data-ttu-id="76af1-355">Verbindungsprotokollierung</span><span class="sxs-lookup"><span data-stu-id="76af1-355">Connection logging</span></span>

<span data-ttu-id="76af1-356">Rufen Sie <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> auf, um Protokolle auf Debugebene für die Kommunikation auf Byteebene für eine Verbindung auszugeben.</span><span class="sxs-lookup"><span data-stu-id="76af1-356">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="76af1-357">Die Verbindungsprotokollierung ist beim Beheben von Problemen bei der Low-Level-Kommunikation hilfreich, wie z. B. bei der TLS-Verschlüsselung und bei Proxys.</span><span class="sxs-lookup"><span data-stu-id="76af1-357">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="76af1-358">Wenn `UseConnectionLogging` vor `UseHttps` platziert wird, wird der verschlüsselte Datenverkehr protokolliert.</span><span class="sxs-lookup"><span data-stu-id="76af1-358">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="76af1-359">Wenn `UseConnectionLogging` nach `UseHttps` platziert wird, wird der entschlüsselte Datenverkehr protokolliert.</span><span class="sxs-lookup"><span data-stu-id="76af1-359">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="76af1-360">Binden an einen TCP-Socket</span><span class="sxs-lookup"><span data-stu-id="76af1-360">Bind to a TCP socket</span></span>

<span data-ttu-id="76af1-361">Die <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*>-Methode wird an ein TCP-Socket gebunden, und ein Lambdaausdruck einer Option lässt die Konfiguration des X.509-Zertifikats zu:</span><span class="sxs-lookup"><span data-stu-id="76af1-361">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=12-18)]

<span data-ttu-id="76af1-362">Im Beispiel wird HTTPS für einen Endpunkt mit <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions> konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="76af1-362">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="76af1-363">Verwenden Sie die gleiche API zum Konfigurieren anderer Kestrel-Einstellungen für bestimmte Endpunkte.</span><span class="sxs-lookup"><span data-stu-id="76af1-363">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="76af1-364">Binden an einen Unix-Socket</span><span class="sxs-lookup"><span data-stu-id="76af1-364">Bind to a Unix socket</span></span>

<span data-ttu-id="76af1-365">Wie in diesem Beispiel dargestellt, lauschen Sie an einem Unix-Socket mit <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*>, um eine verbesserte Leistung mit Nginx zu erzielen:</span><span class="sxs-lookup"><span data-stu-id="76af1-365">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

* <span data-ttu-id="76af1-366">Legen Sie den Eintrag `server` > `location` > `proxy_pass` in der Nginx-Konfigurationsdatei auf `http://unix:/tmp/{KESTREL SOCKET}:/;` fest.</span><span class="sxs-lookup"><span data-stu-id="76af1-366">In the Nginx configuration file, set the `server` > `location` > `proxy_pass` entry to `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span></span> <span data-ttu-id="76af1-367">`{KESTREL SOCKET}` ist der Name des für <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> bereitgestellten Socket (zum Beispiel `kestrel-test.sock` im vorherigen Beispiel).</span><span class="sxs-lookup"><span data-stu-id="76af1-367">`{KESTREL SOCKET}` is the name of the socket provided to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (for example, `kestrel-test.sock` in the preceding example).</span></span>
* <span data-ttu-id="76af1-368">Stellen Sie sicher, dass der Socket von Nginx beschreibbar ist (z. B. `chmod go+w /tmp/kestrel-test.sock`).</span><span class="sxs-lookup"><span data-stu-id="76af1-368">Ensure that the socket is writeable by Nginx (for example, `chmod go+w /tmp/kestrel-test.sock`).</span></span>

### <a name="port-0"></a><span data-ttu-id="76af1-369">Port 0</span><span class="sxs-lookup"><span data-stu-id="76af1-369">Port 0</span></span>

<span data-ttu-id="76af1-370">Wenn die Portnummer `0` angegeben wird, wird Kestrel dynamisch an einen verfügbaren Port gebunden.</span><span class="sxs-lookup"><span data-stu-id="76af1-370">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="76af1-371">Im folgenden Beispiel wird veranschaulicht, wie bestimmt werden kann, für welchen Port Kestrel zur Laufzeit eine Bindung erstellt hat:</span><span class="sxs-lookup"><span data-stu-id="76af1-371">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="76af1-372">Wenn die App ausgeführt wird, gibt das Ausgabefenster der Konsole den dynamischen Port an, über den die App erreicht werden kann:</span><span class="sxs-lookup"><span data-stu-id="76af1-372">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="76af1-373">Einschränkungen</span><span class="sxs-lookup"><span data-stu-id="76af1-373">Limitations</span></span>

<span data-ttu-id="76af1-374">Konfigurieren Sie Endpunkte mithilfe der folgenden Ansätze:</span><span class="sxs-lookup"><span data-stu-id="76af1-374">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="76af1-375">Befehlszeilenargument `--urls`</span><span class="sxs-lookup"><span data-stu-id="76af1-375">`--urls` command-line argument</span></span>
* <span data-ttu-id="76af1-376">Hostkonfigurationsschlüssel `urls`</span><span class="sxs-lookup"><span data-stu-id="76af1-376">`urls` host configuration key</span></span>
* <span data-ttu-id="76af1-377">Umgebungsvariable `ASPNETCORE_URLS`</span><span class="sxs-lookup"><span data-stu-id="76af1-377">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="76af1-378">Diese Methoden sind nützlich, wenn Ihr Code mit anderen Servern als Kestrel funktionieren soll.</span><span class="sxs-lookup"><span data-stu-id="76af1-378">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="76af1-379">Beachten Sie jedoch die folgenden Einschränkungen:</span><span class="sxs-lookup"><span data-stu-id="76af1-379">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="76af1-380">HTTPS kann nicht mit diesen Ansätzen verwendet werden, außer ein Standardzertifikat wird in der HTTPS-Endpunktkonfiguration angegeben (z.B. wenn Sie wie zuvor in diesem Artikel gezeigt die `KestrelServerOptions`-Konfiguration oder eine Konfigurationsdatei verwenden).</span><span class="sxs-lookup"><span data-stu-id="76af1-380">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="76af1-381">Wenn die Ansätze `Listen` und `UseUrls` gleichzeitig verwendet werden, überschreiben die `Listen`-Endpunkte die `UseUrls`-Endpunkte.</span><span class="sxs-lookup"><span data-stu-id="76af1-381">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="76af1-382">IIS-Endpunktkonfiguration</span><span class="sxs-lookup"><span data-stu-id="76af1-382">IIS endpoint configuration</span></span>

<span data-ttu-id="76af1-383">Bei der Verwendung von IIS werden die URL-Bindungen für IIS-Überschreibungsbindungen durch `Listen` oder `UseUrls` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="76af1-383">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="76af1-384">Weitere Informationen finden Sie im Artikel [ASP.NET Core-Modul](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="76af1-384">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

### <a name="listenoptionsprotocols"></a><span data-ttu-id="76af1-385">ListenOptions.Protocols</span><span class="sxs-lookup"><span data-stu-id="76af1-385">ListenOptions.Protocols</span></span>

<span data-ttu-id="76af1-386">Die `Protocols`-Eigenschaft richtet die HTTP-Protokolle (`HttpProtocols`) ein, die für einen Verbindungsendpunkt oder für den Server aktiviert werden.</span><span class="sxs-lookup"><span data-stu-id="76af1-386">The `Protocols` property establishes the HTTP protocols (`HttpProtocols`) enabled on a connection endpoint or for the server.</span></span> <span data-ttu-id="76af1-387">Weisen Sie der `Protocols`-Eigenschaft einen Wert aus der `HttpProtocols`-Enumeration zu.</span><span class="sxs-lookup"><span data-stu-id="76af1-387">Assign a value to the `Protocols` property from the `HttpProtocols` enum.</span></span>

| <span data-ttu-id="76af1-388">`HttpProtocols`-Enumerationswert</span><span class="sxs-lookup"><span data-stu-id="76af1-388">`HttpProtocols` enum value</span></span> | <span data-ttu-id="76af1-389">Zulässiges Verbindungsprotokoll</span><span class="sxs-lookup"><span data-stu-id="76af1-389">Connection protocol permitted</span></span> |
| -------------------------- | ----------------------------- |
| `Http1`                    | <span data-ttu-id="76af1-390">Nur HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="76af1-390">HTTP/1.1 only.</span></span> <span data-ttu-id="76af1-391">Kann mit oder ohne TLS verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="76af1-391">Can be used with or without TLS.</span></span> |
| `Http2`                    | <span data-ttu-id="76af1-392">Nur HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="76af1-392">HTTP/2 only.</span></span> <span data-ttu-id="76af1-393">Kann nur ohne TLS verwendet werden, wenn der Client einen [Vorabkenntnis-Modus](https://tools.ietf.org/html/rfc7540#section-3.4) unterstützt.</span><span class="sxs-lookup"><span data-stu-id="76af1-393">May be used without TLS only if the client supports a [Prior Knowledge mode](https://tools.ietf.org/html/rfc7540#section-3.4).</span></span> |
| `Http1AndHttp2`            | <span data-ttu-id="76af1-394">HTTP/1.1 und HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="76af1-394">HTTP/1.1 and HTTP/2.</span></span> <span data-ttu-id="76af1-395">Für HTTP/2 muss der Client HTTP/2 im TLS [ALPN-Handshake (Application-Layer Protocol Negotiation)](https://tools.ietf.org/html/rfc7301#section-3) auswählen, andernfalls wird standardmäßig eine HTTP/1.1 verwendet.</span><span class="sxs-lookup"><span data-stu-id="76af1-395">HTTP/2 requires the client to select HTTP/2 in the TLS [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) handshake; otherwise, the connection defaults to HTTP/1.1.</span></span> |

<span data-ttu-id="76af1-396">Der Standardwert von `ListenOptions.Protocols` ist für alle Endpunkte `HttpProtocols.Http1AndHttp2`.</span><span class="sxs-lookup"><span data-stu-id="76af1-396">The default `ListenOptions.Protocols` value for any endpoint is `HttpProtocols.Http1AndHttp2`.</span></span>

<span data-ttu-id="76af1-397">TLS-Einschränkungen für HTTP/2:</span><span class="sxs-lookup"><span data-stu-id="76af1-397">TLS restrictions for HTTP/2:</span></span>

* <span data-ttu-id="76af1-398">TLS Version 1.2 oder höher</span><span class="sxs-lookup"><span data-stu-id="76af1-398">TLS version 1.2 or later</span></span>
* <span data-ttu-id="76af1-399">Erneute Aushandlung deaktiviert</span><span class="sxs-lookup"><span data-stu-id="76af1-399">Renegotiation disabled</span></span>
* <span data-ttu-id="76af1-400">Komprimierung deaktiviert</span><span class="sxs-lookup"><span data-stu-id="76af1-400">Compression disabled</span></span>
* <span data-ttu-id="76af1-401">Minimale Größen für Austausch von flüchtigen Schlüsseln:</span><span class="sxs-lookup"><span data-stu-id="76af1-401">Minimum ephemeral key exchange sizes:</span></span>
  * <span data-ttu-id="76af1-402">ECDHE (Elliptic Curve Diffie-Hellman) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack;: mindestens 224 Bits</span><span class="sxs-lookup"><span data-stu-id="76af1-402">Elliptic curve Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack;: 224 bits minimum</span></span>
  * <span data-ttu-id="76af1-403">DHE (Finite Field Diffie-Hellman) &lbrack;`TLS12`&rbrack;: mindestens 2048 Bits</span><span class="sxs-lookup"><span data-stu-id="76af1-403">Finite field Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack;: 2048 bits minimum</span></span>
* <span data-ttu-id="76af1-404">Verschlüsselungssammlung nicht verboten</span><span class="sxs-lookup"><span data-stu-id="76af1-404">Cipher suite not prohibited.</span></span> 

<span data-ttu-id="76af1-405">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; mit der elliptischen P-256-Kurve &lbrack;`FIPS186`&rbrack; wird standardmäßig unterstützt.</span><span class="sxs-lookup"><span data-stu-id="76af1-405">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; with the P-256 elliptic curve &lbrack;`FIPS186`&rbrack; is supported by default.</span></span>

<span data-ttu-id="76af1-406">Das folgende Beispiel erlaubt HTTP/1.1- und HTTP/2-Verbindungen an Port 8000.</span><span class="sxs-lookup"><span data-stu-id="76af1-406">The following example permits HTTP/1.1 and HTTP/2 connections on port 8000.</span></span> <span data-ttu-id="76af1-407">Die Verbindungen werden durch TLS mit einem bereitgestellten Zertifikat geschützt:</span><span class="sxs-lookup"><span data-stu-id="76af1-407">Connections are secured by TLS with a supplied certificate:</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
    });
});
```

<span data-ttu-id="76af1-408">Verwenden Sie Verbindungsmiddleware, um TLS-Handshakes auf Verbindungsbasis für bestimmte Verschlüsselungen zu filtern, falls erforderlich.</span><span class="sxs-lookup"><span data-stu-id="76af1-408">Use Connection Middleware to filter TLS handshakes on a per-connection basis for specific ciphers if required.</span></span>

<span data-ttu-id="76af1-409">Im folgenden Beispiel wird <xref:System.NotSupportedException> für jeden Verschlüsselungsalgorithmus ausgelöst, der von der App nicht unterstützt wird.</span><span class="sxs-lookup"><span data-stu-id="76af1-409">The following example throws <xref:System.NotSupportedException> for any cipher algorithm that the app doesn't support.</span></span> <span data-ttu-id="76af1-410">Alternativ können Sie [ITlsHandshakeFeature.CipherAlgorithm](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm) definieren und mit einer Liste zulässiger Verschlüsselungssammlungen vergleichen.</span><span class="sxs-lookup"><span data-stu-id="76af1-410">Alternatively, define and compare [ITlsHandshakeFeature.CipherAlgorithm](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm) to a list of acceptable cipher suites.</span></span>

<span data-ttu-id="76af1-411">Es wird keine Verschlüsselung mit einem [CipherAlgorithmType.Null](xref:System.Security.Authentication.CipherAlgorithmType)-Verschlüsselungsalgorithmus verwendet.</span><span class="sxs-lookup"><span data-stu-id="76af1-411">No encryption is used with a [CipherAlgorithmType.Null](xref:System.Security.Authentication.CipherAlgorithmType) cipher algorithm.</span></span>

```csharp
// using System.Net;
// using Microsoft.AspNetCore.Connections;

webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
        listenOptions.UseTlsFilter();
    });
});
```

```csharp
using System;
using System.Security.Authentication;
using Microsoft.AspNetCore.Connections.Features;

namespace Microsoft.AspNetCore.Connections
{
    public static class TlsFilterConnectionMiddlewareExtensions
    {
        public static IConnectionBuilder UseTlsFilter(
            this IConnectionBuilder builder)
        {
            return builder.Use((connection, next) =>
            {
                var tlsFeature = connection.Features.Get<ITlsHandshakeFeature>();

                if (tlsFeature.CipherAlgorithm == CipherAlgorithmType.Null)
                {
                    throw new NotSupportedException("Prohibited cipher: " +
                        tlsFeature.CipherAlgorithm);
                }

                return next();
            });
        }
    }
}
```

<span data-ttu-id="76af1-412">Die Verbindungsfilterung kann auch über einen <xref:Microsoft.AspNetCore.Connections.IConnectionBuilder>-Lambdaausdruck konfiguriert werden:</span><span class="sxs-lookup"><span data-stu-id="76af1-412">Connection filtering can also be configured via an <xref:Microsoft.AspNetCore.Connections.IConnectionBuilder> lambda:</span></span>

```csharp
// using System;
// using System.Net;
// using System.Security.Authentication;
// using Microsoft.AspNetCore.Connections;
// using Microsoft.AspNetCore.Connections.Features;

webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
        listenOptions.Use((context, next) =>
        {
            var tlsFeature = context.Features.Get<ITlsHandshakeFeature>();

            if (tlsFeature.CipherAlgorithm == CipherAlgorithmType.Null)
            {
                throw new NotSupportedException(
                    $"Prohibited cipher: {tlsFeature.CipherAlgorithm}");
            }

            return next();
        });
    });
});
```

<span data-ttu-id="76af1-413">Unter Linux kann <xref:System.Net.Security.CipherSuitesPolicy> zum Filtern von TLS-Handshakes auf Verbindungsbasis verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="76af1-413">On Linux, <xref:System.Net.Security.CipherSuitesPolicy> can be used to filter TLS handshakes on a per-connection basis:</span></span>

```csharp
// using System.Net.Security;
// using Microsoft.AspNetCore.Hosting;
// using Microsoft.AspNetCore.Server.Kestrel.Core;
// using Microsoft.Extensions.DependencyInjection;
// using Microsoft.Extensions.Hosting;

webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureHttpsDefaults(listenOptions =>
    {
        listenOptions.OnAuthenticate = (context, sslOptions) =>
        {
            sslOptions.CipherSuitesPolicy = new CipherSuitesPolicy(
                new[]
                {
                    TlsCipherSuite.TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256,
                    TlsCipherSuite.TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384,
                    // ...
                });
        };
    });
});
```

<span data-ttu-id="76af1-414">*Festlegen des Protokolls aus der Konfiguration*</span><span class="sxs-lookup"><span data-stu-id="76af1-414">*Set the protocol from configuration*</span></span>

<span data-ttu-id="76af1-415">`CreateDefaultBuilder` ruft `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` standardmäßig zum Laden der Kestrel-Konfiguration auf.</span><span class="sxs-lookup"><span data-stu-id="76af1-415">`CreateDefaultBuilder` calls `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span>

<span data-ttu-id="76af1-416">Das folgende *appsettings.json* -Beispiel richtet HTTP/1.1 als Standardverbindungsprotokoll für alle Endpunkte ein:</span><span class="sxs-lookup"><span data-stu-id="76af1-416">The following *appsettings.json* example establishes HTTP/1.1 as the default connection protocol for all endpoints:</span></span>

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1"
    }
  }
}
```

<span data-ttu-id="76af1-417">Das folgende *appsettings.json* -Beispiel richtet HTTP/1.1 als Verbindungsprotokoll für einen bestimmten Endpunkt ein:</span><span class="sxs-lookup"><span data-stu-id="76af1-417">The following *appsettings.json* example establishes the HTTP/1.1 connection protocol for a specific endpoint:</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "HttpsDefaultCert": {
        "Url": "https://localhost:5001",
        "Protocols": "Http1"
      }
    }
  }
}
```

<span data-ttu-id="76af1-418">Protokolle, die in Code angegeben werden, setzen Werte außer Kraft, der durch die Konfiguration festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="76af1-418">Protocols specified in code override values set by configuration.</span></span>

## <a name="transport-configuration"></a><span data-ttu-id="76af1-419">Transportkonfiguration</span><span class="sxs-lookup"><span data-stu-id="76af1-419">Transport configuration</span></span>

<span data-ttu-id="76af1-420">Für Projekte, die den Einsatz von Libuv (<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>) erfordern:</span><span class="sxs-lookup"><span data-stu-id="76af1-420">For projects that require the use of Libuv (<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>):</span></span>

* <span data-ttu-id="76af1-421">Fügen Sie für das [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/)-Paket eine Abhängigkeit auf die Projektdatei der App hinzu:</span><span class="sxs-lookup"><span data-stu-id="76af1-421">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

   ```xml
   <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                     Version="{VERSION}" />
   ```

* <span data-ttu-id="76af1-422"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> auf `IWebHostBuilder` aufrufen:</span><span class="sxs-lookup"><span data-stu-id="76af1-422">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> on the `IWebHostBuilder`:</span></span>

   ```csharp
   public class Program
   {
       public static void Main(string[] args)
       {
           CreateHostBuilder(args).Build().Run();
       }

       public static IHostBuilder CreateHostBuilder(string[] args) =>
           Host.CreateDefaultBuilder(args)
               .ConfigureWebHostDefaults(webBuilder =>
               {
                   webBuilder.UseLibuv();
                   webBuilder.UseStartup<Startup>();
               });
   }
   ```

### <a name="url-prefixes"></a><span data-ttu-id="76af1-423">URL-Präfixe</span><span class="sxs-lookup"><span data-stu-id="76af1-423">URL prefixes</span></span>

<span data-ttu-id="76af1-424">Bei der Verwendung von `UseUrls`, dem Befehlszeilenargument `--urls`, dem Hostkonfigurationsschlüssel `urls` oder der Umgebungsvariable `ASPNETCORE_URLS` können die URL-Präfixe in den folgenden Formaten vorliegen.</span><span class="sxs-lookup"><span data-stu-id="76af1-424">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="76af1-425">Nur HTTP-URL-Präfixe sind gültig.</span><span class="sxs-lookup"><span data-stu-id="76af1-425">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="76af1-426">Kestrel unterstützt HTTPS nicht beim Konfigurieren von URL-Bindungen mit `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="76af1-426">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="76af1-427">IPv4-Adresse mit Portnummer</span><span class="sxs-lookup"><span data-stu-id="76af1-427">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="76af1-428">Bei `0.0.0.0` handelt es sich um einen Sonderfall, für den eine Bindung an alle IPv4-Adressen erfolgt.</span><span class="sxs-lookup"><span data-stu-id="76af1-428">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="76af1-429">IPv6-Adresse mit Portnummer</span><span class="sxs-lookup"><span data-stu-id="76af1-429">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="76af1-430">`[::]` stellt das Äquivalent von IPv6 zu `0.0.0.0` für IPv4 dar.</span><span class="sxs-lookup"><span data-stu-id="76af1-430">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="76af1-431">Hostname mit Portnummer</span><span class="sxs-lookup"><span data-stu-id="76af1-431">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="76af1-432">Hostnamen, `*` und `+` sind nicht spezifisch.</span><span class="sxs-lookup"><span data-stu-id="76af1-432">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="76af1-433">Alle Elemente, die nicht als gültige IP-Adresse oder `localhost` erkannt werden, werden an alle IPv4- und IPv6-IP-Adressen gebunden.</span><span class="sxs-lookup"><span data-stu-id="76af1-433">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="76af1-434">Verwenden Sie [HTTP.sys](xref:fundamentals/servers/httpsys) oder einen Reverseproxyserver zum Binden verschiedener Hostnamen an verschiedene ASP.NET Core-Apps auf demselben Port, z.B. IIS, Nginx oder Apache.</span><span class="sxs-lookup"><span data-stu-id="76af1-434">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="76af1-435">Das Hosting in einer Reverseproxykonfiguration erfordert [Hostfilterung](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="76af1-435">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="76af1-436">`localhost`-Hostname mit Portnummer oder Loopback-IP mit Portnummer</span><span class="sxs-lookup"><span data-stu-id="76af1-436">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="76af1-437">Wenn `localhost` angegeben ist, versucht Kestrel, eine Bindung zu IPv4- und IPv6-Loopback-Schnittstellen zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="76af1-437">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="76af1-438">Wenn der erforderliche Port von einem anderen Dienst auf einer der Loopback-Schnittstellen verwendet wird, tritt beim Starten von Kestrel ein Fehler auf.</span><span class="sxs-lookup"><span data-stu-id="76af1-438">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="76af1-439">Wenn eine der Loopback-Schnittstellen aus anderen Gründen nicht verfügbar ist (meistens durch die fehlende Unterstützung von IPv6), protokolliert Kestrel eine Warnung.</span><span class="sxs-lookup"><span data-stu-id="76af1-439">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="76af1-440">Host-Filterung</span><span class="sxs-lookup"><span data-stu-id="76af1-440">Host filtering</span></span>

<span data-ttu-id="76af1-441">Obwohl Kestrel die Konfiguration basierend auf Präfixe wie `http://example.com:5000` unterstützt, ignoriert Kestrel den Hostnamen weitgehend.</span><span class="sxs-lookup"><span data-stu-id="76af1-441">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="76af1-442">Der Host `localhost` ist ein Sonderfall, der für die Bindung an Loopback-Adressen verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="76af1-442">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="76af1-443">Jeder Host, der keine explizite IP-Adresse ist, wird an alle öffentlichen IP-Adressen gebunden.</span><span class="sxs-lookup"><span data-stu-id="76af1-443">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="76af1-444">`Host`-Header werden nicht überprüft.</span><span class="sxs-lookup"><span data-stu-id="76af1-444">`Host` headers aren't validated.</span></span>

<span data-ttu-id="76af1-445">Verwenden Sie Middleware zum Filtern von Hosts, um dieses Problem zu umgehen.</span><span class="sxs-lookup"><span data-stu-id="76af1-445">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="76af1-446">Die Middleware zum Filtern von Hosts wird durch das [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering)-Paket bereitgestellt, das implizit für ASP.NET Core-Apps zur Verfügung steht.</span><span class="sxs-lookup"><span data-stu-id="76af1-446">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is implicitly provided for ASP.NET Core apps.</span></span> <span data-ttu-id="76af1-447">Die Middleware wird von <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> hinzugefügt, wodurch <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*> aufgerufen wird:</span><span class="sxs-lookup"><span data-stu-id="76af1-447">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="76af1-448">Die Middleware zum Filtern von Hosts ist standardmäßig deaktiviert.</span><span class="sxs-lookup"><span data-stu-id="76af1-448">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="76af1-449">Wenn Sie die Middleware aktivieren möchten, definieren Sie einen `AllowedHosts`-Schlüssel in *appsettings.json* /*appsettings.\<EnvironmentName>json*.</span><span class="sxs-lookup"><span data-stu-id="76af1-449">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="76af1-450">Der Wert ist eine durch Semikolons getrennte Liste von Hostnamen ohne Portnummern:</span><span class="sxs-lookup"><span data-stu-id="76af1-450">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="76af1-451">*appsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="76af1-451">*appsettings.json* :</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="76af1-452">[Middleware für weitergeleitete Header](xref:host-and-deploy/proxy-load-balancer) hat auch eine <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts>-Option.</span><span class="sxs-lookup"><span data-stu-id="76af1-452">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="76af1-453">Middleware für weitergeleitete Header und Middleware zum Filtern von Hosts besitzen ähnliche Funktionen für unterschiedliche Szenarios.</span><span class="sxs-lookup"><span data-stu-id="76af1-453">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="76af1-454">Legen Sie `AllowedHosts` mit Middleware für weitergeleitete Header fest, wenn der `Host`-Header beim Weiterleiten von Anforderungen mit einem Reverseproxyserver oder einem Lastenausgleichsmodul nicht beibehalten wird.</span><span class="sxs-lookup"><span data-stu-id="76af1-454">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="76af1-455">Legen Sie `AllowedHosts` mit Middleware zum Filtern von Hosts fest, wenn Kestrel als öffentlicher Edgeserver verwendet oder der `Host`-Header direkt weitergeleitet wird.</span><span class="sxs-lookup"><span data-stu-id="76af1-455">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="76af1-456">Weitere Informationen zu Middleware für weitergeleitete Header finden Sie unter <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="76af1-456">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="76af1-457">Einführung in Kestrel, dem plattformübergreifenden [Webserver für ASP.NET Core](xref:fundamentals/servers/index).</span><span class="sxs-lookup"><span data-stu-id="76af1-457">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="76af1-458">Kestrel ist der Webserver, der standardmäßig in ASP.NET Core-Projektvorlagen enthalten ist.</span><span class="sxs-lookup"><span data-stu-id="76af1-458">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="76af1-459">Kestrel unterstützt die folgenden Szenarios:</span><span class="sxs-lookup"><span data-stu-id="76af1-459">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="76af1-460">HTTPS</span><span class="sxs-lookup"><span data-stu-id="76af1-460">HTTPS</span></span>
* <span data-ttu-id="76af1-461">Nicht transparente Upgrades, die zum Aktivieren von [WebSockets](https://github.com/aspnet/websockets) verwendet werden</span><span class="sxs-lookup"><span data-stu-id="76af1-461">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="76af1-462">Unix-Sockets für eine hohe Leistung im Hintergrund von Nginx</span><span class="sxs-lookup"><span data-stu-id="76af1-462">Unix sockets for high performance behind Nginx</span></span>
* <span data-ttu-id="76af1-463">HTTP/2 (außer unter macOS&dagger;)</span><span class="sxs-lookup"><span data-stu-id="76af1-463">HTTP/2 (except on macOS&dagger;)</span></span>

<span data-ttu-id="76af1-464">&dagger;HTTP/2 wird unter macOS in einem zukünftigen Release unterstützt.</span><span class="sxs-lookup"><span data-stu-id="76af1-464">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>

<span data-ttu-id="76af1-465">Kestrel wird auf allen Plattformen und für alle Versionen unterstützt, die .NET Core unterstützt.</span><span class="sxs-lookup"><span data-stu-id="76af1-465">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="76af1-466">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="76af1-466">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="http2-support"></a><span data-ttu-id="76af1-467">HTTP/2-Unterstützung</span><span class="sxs-lookup"><span data-stu-id="76af1-467">HTTP/2 support</span></span>

<span data-ttu-id="76af1-468">[HTTP/2](https://httpwg.org/specs/rfc7540.html) ist für ASP.NET Core-Apps verfügbar, wenn die folgenden Basisanforderungen erfüllt sind:</span><span class="sxs-lookup"><span data-stu-id="76af1-468">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is available for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="76af1-469">Betriebssystem&dagger;</span><span class="sxs-lookup"><span data-stu-id="76af1-469">Operating system&dagger;</span></span>
  * <span data-ttu-id="76af1-470">Windows Server 2016/Windows 10 oder höher&Dagger;</span><span class="sxs-lookup"><span data-stu-id="76af1-470">Windows Server 2016/Windows 10 or later&Dagger;</span></span>
  * <span data-ttu-id="76af1-471">Linux mit OpenSSL 1.0.2 oder höher (z.B. Ubuntu 16.04 oder höher)</span><span class="sxs-lookup"><span data-stu-id="76af1-471">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
* <span data-ttu-id="76af1-472">Zielframework: .NET Core 2.2 oder höher</span><span class="sxs-lookup"><span data-stu-id="76af1-472">Target framework: .NET Core 2.2 or later</span></span>
* <span data-ttu-id="76af1-473">[ALPN](https://tools.ietf.org/html/rfc7301#section-3)-Verbindung (Application-Layer Protocol Negotiation)</span><span class="sxs-lookup"><span data-stu-id="76af1-473">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="76af1-474">TLS 1.2-Verbindung oder höher</span><span class="sxs-lookup"><span data-stu-id="76af1-474">TLS 1.2 or later connection</span></span>

<span data-ttu-id="76af1-475">&dagger;HTTP/2 wird unter macOS in einem zukünftigen Release unterstützt.</span><span class="sxs-lookup"><span data-stu-id="76af1-475">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>
<span data-ttu-id="76af1-476">&Dagger;Kestrel bietet eingeschränkte Unterstützung für HTTP/2 unter Windows Server 2012 R2 und Windows 8.1.</span><span class="sxs-lookup"><span data-stu-id="76af1-476">&Dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="76af1-477">Die Unterstützung ist eingeschränkt, weil die Liste der unterstützten TLS-Verschlüsselungssammlungen unter diesen Betriebssystemen begrenzt ist.</span><span class="sxs-lookup"><span data-stu-id="76af1-477">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="76af1-478">Zum Sichern von TLS-Verbindungen ist möglicherweise ein durch einen Elliptic Curve Digital Signature Algorithm (ECDSA) generiertes Zertifikat erforderlich.</span><span class="sxs-lookup"><span data-stu-id="76af1-478">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

<span data-ttu-id="76af1-479">Wenn eine HTTP/2-Verbindung hergestellt wurde, meldet [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*)`HTTP/2`.</span><span class="sxs-lookup"><span data-stu-id="76af1-479">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="76af1-480">HTTP/2 ist standardmäßig deaktiviert.</span><span class="sxs-lookup"><span data-stu-id="76af1-480">HTTP/2 is disabled by default.</span></span> <span data-ttu-id="76af1-481">Weitere Informationen zur Konfiguration finden Sie in den Abschnitten [Kestrel-Optionen](#kestrel-options) und [ListenOptions.Protocols](#listenoptionsprotocols).</span><span class="sxs-lookup"><span data-stu-id="76af1-481">For more information on configuration, see the [Kestrel options](#kestrel-options) and [ListenOptions.Protocols](#listenoptionsprotocols) sections.</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="76af1-482">Verwenden von Kestrel mit einem Reverseproxy</span><span class="sxs-lookup"><span data-stu-id="76af1-482">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="76af1-483">Kestrel kann eigenständig oder mit einem *Reverseproxyserver* wie z.B. [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org) oder [Apache](https://httpd.apache.org/) verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="76af1-483">Kestrel can be used by itself or with a *reverse proxy server* , such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="76af1-484">Ein Reverseproxyserver empfängt HTTP-Anforderungen aus dem Netzwerk und leitet diese an Kestrel weiter.</span><span class="sxs-lookup"><span data-stu-id="76af1-484">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="76af1-485">Kestrel bei Verwendung als Webserver mit direkter Internetverbindung:</span><span class="sxs-lookup"><span data-stu-id="76af1-485">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Kestrel kommuniziert direkt und ohne Reverseproxyserver mit dem Internet](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="76af1-487">Kestrel bei Verwendung in einer Reverseproxykonfiguration:</span><span class="sxs-lookup"><span data-stu-id="76af1-487">Kestrel used in a reverse proxy configuration:</span></span>

![Kestrel kommuniziert indirekt mit dem Internet über einen Reverseproxyserver wie IIS, Nginx oder Apache](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="76af1-489">Jede der beiden Konfigurationen – mit oder ohne einen Reverseproxyserver – stellt eine unterstützte Hostingkonfiguration dar.</span><span class="sxs-lookup"><span data-stu-id="76af1-489">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="76af1-490">Bei Verwendung als Edgeserver ohne Reverseproxyserver unterstützt Kestrel die gemeinsame Nutzung der gleichen IP-Adresse und des gleichen Ports durch mehrere Prozesse nicht.</span><span class="sxs-lookup"><span data-stu-id="76af1-490">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="76af1-491">Wenn Kestrel für das Lauschen an einem Port konfiguriert ist, verarbeitet Kestrel den gesamten Datenverkehr für diesen Port unabhängig von den `Host`-Headern der Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="76af1-491">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="76af1-492">Ein Reverseproxy, der Ports freigeben kann, kann Anforderungen an Kestrel über eine eindeutige IP und einen eindeutigen Port weiterleiten.</span><span class="sxs-lookup"><span data-stu-id="76af1-492">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="76af1-493">Auch wenn kein Reverseproxyserver erforderlich ist, kann die Verwendung eines solchen empfehlenswert sein.</span><span class="sxs-lookup"><span data-stu-id="76af1-493">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="76af1-494">Für einen Reverseproxy gilt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="76af1-494">A reverse proxy:</span></span>

* <span data-ttu-id="76af1-495">Er kann die verfügbar gemachten öffentlichen Oberflächen der von ihm gehosteten Apps einschränken.</span><span class="sxs-lookup"><span data-stu-id="76af1-495">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="76af1-496">Er stellt eine zusätzliche Ebene für Konfiguration und Schutz bereit.</span><span class="sxs-lookup"><span data-stu-id="76af1-496">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="76af1-497">Er lässt sich besser in die vorhandene Infrastruktur integrieren.</span><span class="sxs-lookup"><span data-stu-id="76af1-497">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="76af1-498">Er vereinfacht die Konfiguration von Lastenausgleich und sicherer Kommunikation (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="76af1-498">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="76af1-499">Nur der Reverseproxyserver erfordert ein X.509-Zertifikat, und dieser Server kann mit den Servern der App im internen Netzwerk über einfaches HTTP kommunizieren.</span><span class="sxs-lookup"><span data-stu-id="76af1-499">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="76af1-500">Das Hosting in einer Reverseproxykonfiguration erfordert [Hostfilterung](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="76af1-500">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a><span data-ttu-id="76af1-501">Verwenden von Kestrel in ASP.NET Core-Apps</span><span class="sxs-lookup"><span data-stu-id="76af1-501">How to use Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="76af1-502">Das Paket [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) ist im Metapaket [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) enthalten.</span><span class="sxs-lookup"><span data-stu-id="76af1-502">The [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="76af1-503">ASP.NET Core-Projektvorlagen verwenden Kestrel standardmäßig.</span><span class="sxs-lookup"><span data-stu-id="76af1-503">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="76af1-504">Der Vorlagencode in *Program.cs* ruft <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> auf, wodurch <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> im Hintergrund aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="76af1-504">In *Program.cs* , the template code calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> behind the scenes.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=7)]

<span data-ttu-id="76af1-505">Weitere Informationen zum `CreateDefaultBuilder` und zum Erstellen des Hosts finden Sie im Abschnitt *Einrichten eines Hosts* von <xref:fundamentals/host/web-host#set-up-a-host>.</span><span class="sxs-lookup"><span data-stu-id="76af1-505">For more information on `CreateDefaultBuilder` and building the host, see the *Set up a host* section of <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

<span data-ttu-id="76af1-506">Um zusätzliche Konfiguration nach dem Aufruf von `CreateDefaultBuilder` bereitzustellen, verwenden Sie `ConfigureKestrel`:</span><span class="sxs-lookup"><span data-stu-id="76af1-506">To provide additional configuration after calling `CreateDefaultBuilder`, use `ConfigureKestrel`:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            // Set properties and call methods on serverOptions
        });
```

<span data-ttu-id="76af1-507">Wenn die App `CreateDefaultBuilder` nicht aufruft, um den Host einzurichten, rufen Sie <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> **auf** , bevor Sie `ConfigureKestrel` aufrufen:</span><span class="sxs-lookup"><span data-stu-id="76af1-507">If the app doesn't call `CreateDefaultBuilder` to set up the host, call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> **before** calling `ConfigureKestrel`:</span></span>

```csharp
public static void Main(string[] args)
{
    var host = new WebHostBuilder()
        .UseContentRoot(Directory.GetCurrentDirectory())
        .UseKestrel()
        .UseIISIntegration()
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            // Set properties and call methods on serverOptions
        })
        .Build();

    host.Run();
}
```

## <a name="kestrel-options"></a><span data-ttu-id="76af1-508">Kestrel-Optionen</span><span class="sxs-lookup"><span data-stu-id="76af1-508">Kestrel options</span></span>

<span data-ttu-id="76af1-509">Der Kestrel-Webserver verfügt über einschränkende Konfigurationsoptionen, die besonders nützlich bei Bereitstellungen mit Internetzugriff sind.</span><span class="sxs-lookup"><span data-stu-id="76af1-509">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="76af1-510">Legen Sie Einschränkungen für die <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits>-Eigenschaft der <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>-Klasse fest.</span><span class="sxs-lookup"><span data-stu-id="76af1-510">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="76af1-511">Die `Limits`-Eigenschaft enthält eine Instanz der <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>-Klasse.</span><span class="sxs-lookup"><span data-stu-id="76af1-511">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="76af1-512">In den folgenden Beispielen wird der <xref:Microsoft.AspNetCore.Server.Kestrel.Core>-Namespace verwendet:</span><span class="sxs-lookup"><span data-stu-id="76af1-512">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="76af1-513">Kestrel-Optionen, die in den folgenden Beispielen in C#-Code konfiguriert sind, können auch mit einem [Konfigurationsanbieter](xref:fundamentals/configuration/index) festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="76af1-513">Kestrel options, which are configured in C# code in the following examples, can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="76af1-514">Beispielsweise kann der Dateikonfigurationsanbieter die Kestrel-Konfiguration aus einer *appsettings.json* - oder *appsettings.{Umgebung}.json* -Datei laden:</span><span class="sxs-lookup"><span data-stu-id="76af1-514">For example, the File Configuration Provider can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

```json
{
  "Kestrel": {
    "Limits": {
      "MaxConcurrentConnections": 100,
      "MaxConcurrentUpgradedConnections": 100
    }
  }
}
```

<span data-ttu-id="76af1-515">Verwenden Sie **einen** der folgenden Ansätze:</span><span class="sxs-lookup"><span data-stu-id="76af1-515">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="76af1-516">Konfigurieren Sie Kestrel in `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="76af1-516">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="76af1-517">Fügen Sie eine Instanz von `IConfiguration` in die `Startup`-Klasse ein.</span><span class="sxs-lookup"><span data-stu-id="76af1-517">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="76af1-518">Im folgenden Beispiel wird davon ausgegangen, dass die eingefügte Konfiguration der `Configuration`-Eigenschaft zugewiesen wird.</span><span class="sxs-lookup"><span data-stu-id="76af1-518">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="76af1-519">Laden Sie in `Startup.ConfigureServices` den Abschnitt `Kestrel` der Konfiguration in die Konfiguration von Kestrel:</span><span class="sxs-lookup"><span data-stu-id="76af1-519">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

* <span data-ttu-id="76af1-520">Konfigurieren Sie Kestrel beim Erstellen des Hosts:</span><span class="sxs-lookup"><span data-stu-id="76af1-520">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="76af1-521">Laden Sie in *Program.cs* den Abschnitt `Kestrel` der Konfiguration in die Konfiguration von Kestrel:</span><span class="sxs-lookup"><span data-stu-id="76af1-521">In *Program.cs* , load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

  ```csharp
  // using Microsoft.Extensions.DependencyInjection;

  public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
      WebHost.CreateDefaultBuilder(args)
          .ConfigureServices((context, services) =>
          {
              services.Configure<KestrelServerOptions>(
                  context.Configuration.GetSection("Kestrel"));
          })
          .UseStartup<Startup>();
  ```

<span data-ttu-id="76af1-522">Beide vorangehenden Ansätze funktionieren mit jedem [Konfigurationsanbieter](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="76af1-522">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="76af1-523">Keep-Alive-Timeout</span><span class="sxs-lookup"><span data-stu-id="76af1-523">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="76af1-524">Ruft das [Keep-Alive-Timeout](https://tools.ietf.org/html/rfc7230#section-6.5) ab oder legt es fest.</span><span class="sxs-lookup"><span data-stu-id="76af1-524">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="76af1-525">Standardwert: 2 Minuten.</span><span class="sxs-lookup"><span data-stu-id="76af1-525">Defaults to 2 minutes.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=15)]

### <a name="maximum-client-connections"></a><span data-ttu-id="76af1-526">Maximale Anzahl der Clientverbindungen</span><span class="sxs-lookup"><span data-stu-id="76af1-526">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="76af1-527">Die maximale Anzahl von gleichzeitig geöffneten TCP-Verbindungen kann mithilfe von folgendem Code für die gesamte App festgelegt werden:</span><span class="sxs-lookup"><span data-stu-id="76af1-527">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

<span data-ttu-id="76af1-528">Es gibt einen separaten Grenzwert für Verbindungen, die von HTTP oder HTTPS auf ein anderes Protokoll aktualisiert wurden (z.B. auf eine WebSockets-Anforderung).</span><span class="sxs-lookup"><span data-stu-id="76af1-528">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="76af1-529">Nachdem eine Verbindung aktualisiert wurde, zählt diese nicht mehr für den `MaxConcurrentConnections`-Grenzwert.</span><span class="sxs-lookup"><span data-stu-id="76af1-529">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

<span data-ttu-id="76af1-530">Die maximale Anzahl von Verbindungen ist standardmäßig nicht begrenzt (NULL).</span><span class="sxs-lookup"><span data-stu-id="76af1-530">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="76af1-531">Maximale Größe des Anforderungstexts</span><span class="sxs-lookup"><span data-stu-id="76af1-531">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="76af1-532">Die maximale Größe des Anforderungstexts beträgt standardmäßig 30.000.000 Byte, also ungefähr 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="76af1-532">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="76af1-533">Die empfohlene Methode zur Außerkraftsetzung des Grenzwerts in einer ASP.NET Core-MVC-App besteht im Verwenden des <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute>-Attributs in einer Aktionsmethode:</span><span class="sxs-lookup"><span data-stu-id="76af1-533">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="76af1-534">Im folgenden Beispiel wird veranschaulicht, wie die Einschränkungen auf jeder Anforderung für die App konfiguriert werden:</span><span class="sxs-lookup"><span data-stu-id="76af1-534">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

<span data-ttu-id="76af1-535">Außer Kraft setzen der Einstellung für eine bestimmte Anforderung in Middleware:</span><span class="sxs-lookup"><span data-stu-id="76af1-535">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="76af1-536">Eine Ausnahme wird ausgelöst, wenn die App den Grenzwert einer Anforderung konfiguriert, nachdem die App bereits mit dem Lesen der Anforderung begonnen hat.</span><span class="sxs-lookup"><span data-stu-id="76af1-536">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="76af1-537">Es gibt eine `IsReadOnly`-Eigenschaft, die angibt, wenn sich die `MaxRequestBodySize`-Eigenschaft im schreibgeschützten Zustand befindet, also wenn der Grenzwert nicht mehr konfiguriert werden kann.</span><span class="sxs-lookup"><span data-stu-id="76af1-537">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="76af1-538">Wenn eine App [prozessextern](xref:host-and-deploy/iis/index#out-of-process-hosting-model) hinter dem [ASP.NET Core-Modul](xref:host-and-deploy/aspnet-core-module) ausgeführt wird, ist das Größenlimit von Kestrel für Anforderungstext deaktiviert, weil IIS dieses Limit bereits festlegt.</span><span class="sxs-lookup"><span data-stu-id="76af1-538">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="76af1-539">Minimale Datenrate des Anforderungstexts</span><span class="sxs-lookup"><span data-stu-id="76af1-539">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="76af1-540">Kestrel überprüft sekündlich, ob Daten mit der angegebenen Rate in Bytes/Sekunde eingehen.</span><span class="sxs-lookup"><span data-stu-id="76af1-540">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="76af1-541">Wenn die Rate den mindestens erforderlichen Wert unterschreitet, wird für die Verbindung wegen Timeout getrennt. Bei der Toleranzperiode handelt es sich um die Zeitspanne, die Kestrel dem Client gewährt, um die Senderate auf den mindestens erforderlichen Wert zu erhöhen. Die Rate wird währenddessen nicht überprüft.</span><span class="sxs-lookup"><span data-stu-id="76af1-541">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="76af1-542">Diese Toleranzperiode beugt dem Trennen von Verbindungen vor, die Daten aufgrund eines langsamen TCP-Starts anfänglich mit einer niedrigen Rate senden.</span><span class="sxs-lookup"><span data-stu-id="76af1-542">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="76af1-543">Die mindestens erforderliche Rate beträgt standardmäßig 240 Bytes/Sekunde mit einer Toleranzperiode von 5 Sekunden.</span><span class="sxs-lookup"><span data-stu-id="76af1-543">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="76af1-544">Für die Antwort gilt ebenfalls eine mindestens erforderliche Rate.</span><span class="sxs-lookup"><span data-stu-id="76af1-544">A minimum rate also applies to the response.</span></span> <span data-ttu-id="76af1-545">Der Code zum Festlegen des Grenzwerts für Anforderung und Antwort ist abgesehen von `RequestBody` oder `Response` in den Namen von Eigenschaften und Schnittstelle identisch.</span><span class="sxs-lookup"><span data-stu-id="76af1-545">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="76af1-546">In diesem Beispiel wird veranschaulicht, wie Sie die mindestens erforderlichen Datenraten in *Program.cs* konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="76af1-546">Here's an example that shows how to configure the minimum data rates in *Program.cs* :</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-9)]

<span data-ttu-id="76af1-547">Außer Kraft setzen des minimalen Ratenlimits pro Anforderung in Middleware:</span><span class="sxs-lookup"><span data-stu-id="76af1-547">Override the minimum rate limits per request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

<span data-ttu-id="76af1-548">Keines dieser Ratenfeatures, auf die im vorherigen Beispiel verwiesen wird, ist in `HttpContext.Features` für HTTP/2-Anforderungen vorhanden, da die Änderung von Ratenlimits für jede Anforderung aufgrund der Unterstützung für Anforderungsmultiplexing des Protokolls nicht für HTTP/2 unterstützt wird.</span><span class="sxs-lookup"><span data-stu-id="76af1-548">Neither rate feature referenced in the prior sample are present in `HttpContext.Features` for HTTP/2 requests because modifying rate limits on a per-request basis isn't supported for HTTP/2 due to the protocol's support for request multiplexing.</span></span> <span data-ttu-id="76af1-549">Serverweite Ratenlimits, die über `KestrelServerOptions.Limits` konfiguriert sind, gelten auch für HTTP/1.x- und HTTP/2-Verbindungen.</span><span class="sxs-lookup"><span data-stu-id="76af1-549">Server-wide rate limits configured via `KestrelServerOptions.Limits` still apply to both HTTP/1.x and HTTP/2 connections.</span></span>

### <a name="request-headers-timeout"></a><span data-ttu-id="76af1-550">Timeout für Anforderungsheader</span><span class="sxs-lookup"><span data-stu-id="76af1-550">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="76af1-551">Ruft die maximale Zeitspanne ab, während der der Server Anforderungsheader empfängt, oder legt diese fest.</span><span class="sxs-lookup"><span data-stu-id="76af1-551">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="76af1-552">Der Standardwert beträgt 30 Sekunden.</span><span class="sxs-lookup"><span data-stu-id="76af1-552">Defaults to 30 seconds.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=16)]

### <a name="maximum-streams-per-connection"></a><span data-ttu-id="76af1-553">Maximale Datenströme pro Verbindung</span><span class="sxs-lookup"><span data-stu-id="76af1-553">Maximum streams per connection</span></span>

<span data-ttu-id="76af1-554">`Http2.MaxStreamsPerConnection` schränkt die Anzahl gleichzeitiger Anforderungsdatenströme pro HTTP/2-Verbindung ein.</span><span class="sxs-lookup"><span data-stu-id="76af1-554">`Http2.MaxStreamsPerConnection` limits the number of concurrent request streams per HTTP/2 connection.</span></span> <span data-ttu-id="76af1-555">Überschüssige Datenströme werden zurückgewiesen.</span><span class="sxs-lookup"><span data-stu-id="76af1-555">Excess streams are refused.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
        });
```

<span data-ttu-id="76af1-556">Der Standardwert ist 100.</span><span class="sxs-lookup"><span data-stu-id="76af1-556">The default value is 100.</span></span>

### <a name="header-table-size"></a><span data-ttu-id="76af1-557">Größe der Headertabelle</span><span class="sxs-lookup"><span data-stu-id="76af1-557">Header table size</span></span>

<span data-ttu-id="76af1-558">Der HPACK-Decoder dekomprimiert HTTP-Header für HTTP/2-Verbindungen.</span><span class="sxs-lookup"><span data-stu-id="76af1-558">The HPACK decoder decompresses HTTP headers for HTTP/2 connections.</span></span> <span data-ttu-id="76af1-559">`Http2.HeaderTableSize` schränkt die Größe der Headerkomprimierungstabelle ein, die der HPACK-Decoder verwendet.</span><span class="sxs-lookup"><span data-stu-id="76af1-559">`Http2.HeaderTableSize` limits the size of the header compression table that the HPACK decoder uses.</span></span> <span data-ttu-id="76af1-560">Der Wert wird in Oktetten bereitgestellt und muss größer als null (0) sein.</span><span class="sxs-lookup"><span data-stu-id="76af1-560">The value is provided in octets and must be greater than zero (0).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.HeaderTableSize = 4096;
        });
```

<span data-ttu-id="76af1-561">Der Standardwert ist 4096.</span><span class="sxs-lookup"><span data-stu-id="76af1-561">The default value is 4096.</span></span>

### <a name="maximum-frame-size"></a><span data-ttu-id="76af1-562">Maximale Framegröße</span><span class="sxs-lookup"><span data-stu-id="76af1-562">Maximum frame size</span></span>

<span data-ttu-id="76af1-563">`Http2.MaxFrameSize` gibt die maximale Größe der zu empfangenden HTTP/2-Verbindungsframenutzlast an.</span><span class="sxs-lookup"><span data-stu-id="76af1-563">`Http2.MaxFrameSize` indicates the maximum size of the HTTP/2 connection frame payload to receive.</span></span> <span data-ttu-id="76af1-564">Der Wert wird in Oktetten bereitgestellt und muss zwischen 2^14 (16.384) und 2^24-1 (16.777.215) liegen.</span><span class="sxs-lookup"><span data-stu-id="76af1-564">The value is provided in octets and must be between 2^14 (16,384) and 2^24-1 (16,777,215).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxFrameSize = 16384;
        });
```

<span data-ttu-id="76af1-565">Der Standardwert ist 2^14 (16.384).</span><span class="sxs-lookup"><span data-stu-id="76af1-565">The default value is 2^14 (16,384).</span></span>

### <a name="maximum-request-header-size"></a><span data-ttu-id="76af1-566">Maximale Größe des Anforderungsheaders</span><span class="sxs-lookup"><span data-stu-id="76af1-566">Maximum request header size</span></span>

<span data-ttu-id="76af1-567">`Http2.MaxRequestHeaderFieldSize` gibt die maximal zulässige Größe in Oktetten der Anforderungsheaderwerte an.</span><span class="sxs-lookup"><span data-stu-id="76af1-567">`Http2.MaxRequestHeaderFieldSize` indicates the maximum allowed size in octets of request header values.</span></span> <span data-ttu-id="76af1-568">Dieser Grenzwert gilt zusammen für den Namen und Wert in komprimierten und nicht komprimierten Darstellungen.</span><span class="sxs-lookup"><span data-stu-id="76af1-568">This limit applies to both name and value together in their compressed and uncompressed representations.</span></span> <span data-ttu-id="76af1-569">Der Wert muss größer als 0 (null) sein.</span><span class="sxs-lookup"><span data-stu-id="76af1-569">The value must be greater than zero (0).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
        });
```

<span data-ttu-id="76af1-570">Der Standardwert ist 8.192.</span><span class="sxs-lookup"><span data-stu-id="76af1-570">The default value is 8,192.</span></span>

### <a name="initial-connection-window-size"></a><span data-ttu-id="76af1-571">Anfangsfenstergröße der Verbindung</span><span class="sxs-lookup"><span data-stu-id="76af1-571">Initial connection window size</span></span>

<span data-ttu-id="76af1-572">`Http2.InitialConnectionWindowSize` gibt die maximalen Anforderungstextdaten in Byte an, die der Server auf einmal für alle Anforderungen (Streams) pro Verbindung aggregiert.</span><span class="sxs-lookup"><span data-stu-id="76af1-572">`Http2.InitialConnectionWindowSize` indicates the maximum request body data in bytes the server buffers at one time aggregated across all requests (streams) per connection.</span></span> <span data-ttu-id="76af1-573">Anforderungen werden auch durch `Http2.InitialStreamWindowSize` beschränkt.</span><span class="sxs-lookup"><span data-stu-id="76af1-573">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="76af1-574">Der Wert muss größer als oder gleich 65.535 und kleiner als 2^31 (2.147.483.648) sein.</span><span class="sxs-lookup"><span data-stu-id="76af1-574">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
        });
```

<span data-ttu-id="76af1-575">Der Standardwert ist 128 KB (131.072).</span><span class="sxs-lookup"><span data-stu-id="76af1-575">The default value is 128 KB (131,072).</span></span>

### <a name="initial-stream-window-size"></a><span data-ttu-id="76af1-576">Anfangsfenstergröße des Streams</span><span class="sxs-lookup"><span data-stu-id="76af1-576">Initial stream window size</span></span>

<span data-ttu-id="76af1-577">`Http2.InitialStreamWindowSize` gibt die maximalen Anforderungstextdaten in Byte an, die der Server auf einmal pro Anforderung (Stream) puffert.</span><span class="sxs-lookup"><span data-stu-id="76af1-577">`Http2.InitialStreamWindowSize` indicates the maximum request body data in bytes the server buffers at one time per request (stream).</span></span> <span data-ttu-id="76af1-578">Anforderungen werden auch durch `Http2.InitialStreamWindowSize` beschränkt.</span><span class="sxs-lookup"><span data-stu-id="76af1-578">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="76af1-579">Der Wert muss größer als oder gleich 65.535 und kleiner als 2^31 (2.147.483.648) sein.</span><span class="sxs-lookup"><span data-stu-id="76af1-579">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
        });
```

<span data-ttu-id="76af1-580">Der Standardwert ist 96 KB (98.304).</span><span class="sxs-lookup"><span data-stu-id="76af1-580">The default value is 96 KB (98,304).</span></span>

### <a name="synchronous-io"></a><span data-ttu-id="76af1-581">Synchrone E/A-Vorgänge</span><span class="sxs-lookup"><span data-stu-id="76af1-581">Synchronous I/O</span></span>

<span data-ttu-id="76af1-582"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> steuert, ob für Anforderung und Antwort synchrone E/A-Vorgänge zulässig sind.</span><span class="sxs-lookup"><span data-stu-id="76af1-582"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous I/O is allowed for the request and response.</span></span> <span data-ttu-id="76af1-583">Der Standardwert ist `true`.</span><span class="sxs-lookup"><span data-stu-id="76af1-583">The  default value is `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="76af1-584">Sehr viele blockierende synchrone E/A-Vorgänge können zu einem Ressourcenmangel im Threadpool führen, wodurch die App nicht mehr reagiert.</span><span class="sxs-lookup"><span data-stu-id="76af1-584">A large number of blocking synchronous I/O operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="76af1-585">Aktivieren Sie `AllowSynchronousIO` nur bei Verwendung einer Bibliothek, die asynchrone E/A-Vorgänge nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="76af1-585">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous I/O.</span></span>

<span data-ttu-id="76af1-586">Das folgende Beispiel aktiviert synchrone E/A-Vorgänge:</span><span class="sxs-lookup"><span data-stu-id="76af1-586">The following example enables synchronous I/O:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

<span data-ttu-id="76af1-587">Weitere Informationen zu anderen Kestrel-Optionen und -Einschränkungen finden Sie unter:</span><span class="sxs-lookup"><span data-stu-id="76af1-587">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="76af1-588">Endpunktkonfiguration</span><span class="sxs-lookup"><span data-stu-id="76af1-588">Endpoint configuration</span></span>

<span data-ttu-id="76af1-589">Standardmäßig wird ASP.NET Core an Folgendes gebunden:</span><span class="sxs-lookup"><span data-stu-id="76af1-589">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="76af1-590">`https://localhost:5001` (wenn ein lokales Entwicklungszertifikat vorhanden ist)</span><span class="sxs-lookup"><span data-stu-id="76af1-590">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="76af1-591">Verwenden Sie Folgendes zum Angeben der URLs:</span><span class="sxs-lookup"><span data-stu-id="76af1-591">Specify URLs using the:</span></span>

* <span data-ttu-id="76af1-592">Die Umgebungsvariable `ASPNETCORE_URLS`</span><span class="sxs-lookup"><span data-stu-id="76af1-592">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="76af1-593">Das Befehlszeilenargument `--urls`</span><span class="sxs-lookup"><span data-stu-id="76af1-593">`--urls` command-line argument.</span></span>
* <span data-ttu-id="76af1-594">Den Hostkonfigurationsschlüssel `urls`</span><span class="sxs-lookup"><span data-stu-id="76af1-594">`urls` host configuration key.</span></span>
* <span data-ttu-id="76af1-595">Die Erweiterungsmethode `UseUrls`</span><span class="sxs-lookup"><span data-stu-id="76af1-595">`UseUrls` extension method.</span></span>

<span data-ttu-id="76af1-596">Der Wert, der mit diesen Ansätzen angegeben wird, kann mindestens ein HTTP- oder HTTPS-Endpunkt sein (HTTPS wenn ein Standardzertifikat verfügbar ist).</span><span class="sxs-lookup"><span data-stu-id="76af1-596">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="76af1-597">Konfigurieren Sie den Wert als eine durch Semikolons getrennte Liste (z.B. `"Urls": "http://localhost:8000;http://localhost:8001"`).</span><span class="sxs-lookup"><span data-stu-id="76af1-597">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="76af1-598">Weitere Informationen zu diesen Ansätzen finden Sie unter [Server-URLs](xref:fundamentals/host/web-host#server-urls) und [Außerkraftsetzen der Konfiguration](xref:fundamentals/host/web-host#override-configuration).</span><span class="sxs-lookup"><span data-stu-id="76af1-598">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="76af1-599">Ein Entwicklungszertifikat wird erstellt:</span><span class="sxs-lookup"><span data-stu-id="76af1-599">A development certificate is created:</span></span>

* <span data-ttu-id="76af1-600">Wenn das [.NET Core SDK](/dotnet/core/sdk) installiert wird.</span><span class="sxs-lookup"><span data-stu-id="76af1-600">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="76af1-601">Das [dev-cert-Tool](xref:aspnetcore-2.1#https) wird zum Erstellen eines Zertifikats verwendet.</span><span class="sxs-lookup"><span data-stu-id="76af1-601">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="76af1-602">Einige Browser erfordern, dass Sie die explizite Berechtigung erteilen, dem lokalen Entwicklungszertifikat zu vertrauen.</span><span class="sxs-lookup"><span data-stu-id="76af1-602">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="76af1-603">Projektvorlagen konfigurieren Apps, damit sie standardmäßig auf HTTPS ausgeführt werden und die [HTTPS-Umleitung und HSTS-Unterstützung](xref:security/enforcing-ssl) enthalten.</span><span class="sxs-lookup"><span data-stu-id="76af1-603">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="76af1-604">Rufen Sie die <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*>- oder <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*>-Methode unter <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> auf, um URL-Präfixe und Ports für Kestrel zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="76af1-604">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="76af1-605">`UseUrls`, das Befehlszeilenargument `--urls`, der Hostkonfigurationsschlüssel `urls` und die Umgebungsvariable `ASPNETCORE_URLS` funktionieren ebenfalls, verfügen jedoch über Einschränkungen, die im Verlauf dieses Abschnitts erläutert werden (Ein Standardzertifikat muss für die HTTPS-Endpunktkonfiguration verfügbar sein).</span><span class="sxs-lookup"><span data-stu-id="76af1-605">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="76af1-606">`KestrelServerOptions`-Konfiguration:</span><span class="sxs-lookup"><span data-stu-id="76af1-606">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="76af1-607">ConfigureEndpointDefaults(Action\<ListenOptions>)</span><span class="sxs-lookup"><span data-stu-id="76af1-607">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="76af1-608">Gibt die Konfiguration von `Action` zum Ausführen von jedem angegebenen Endpunkt an.</span><span class="sxs-lookup"><span data-stu-id="76af1-608">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="76af1-609">Mehrmalige Aufrufe von `ConfigureEndpointDefaults` ersetzen vorherige Instanzen von `Action` mit der zuletzt angegebenen `Action`.</span><span class="sxs-lookup"><span data-stu-id="76af1-609">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureEndpointDefaults(listenOptions =>
            {
                // Configure endpoint defaults
            });
        });
```

> [!NOTE]
> <span data-ttu-id="76af1-610">Auf Endpunkte, die durch Aufrufen von <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **vor** dem Aufrufen von <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> erstellt werden, werden die Standardwerte nicht angewendet.</span><span class="sxs-lookup"><span data-stu-id="76af1-610">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> won't have the defaults applied.</span></span>

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="76af1-611">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span><span class="sxs-lookup"><span data-stu-id="76af1-611">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="76af1-612">Gibt die Konfiguration von `Action` zum Ausführen von jedem HTTPS-Endpunkt an.</span><span class="sxs-lookup"><span data-stu-id="76af1-612">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="76af1-613">Mehrmalige Aufrufe von `ConfigureHttpsDefaults` ersetzen vorherige Instanzen von `Action` mit der zuletzt angegebenen `Action`.</span><span class="sxs-lookup"><span data-stu-id="76af1-613">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureHttpsDefaults(listenOptions =>
            {
                // certificate is an X509Certificate2
                listenOptions.ServerCertificate = certificate;
            });
        });
```

> [!NOTE]
> <span data-ttu-id="76af1-614">Auf Endpunkte, die durch Aufrufen von <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **vor** dem Aufrufen von <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> erstellt werden, werden die Standardwerte nicht angewendet.</span><span class="sxs-lookup"><span data-stu-id="76af1-614">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>


### <a name="configureiconfiguration"></a><span data-ttu-id="76af1-615">Configure(IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="76af1-615">Configure(IConfiguration)</span></span>

<span data-ttu-id="76af1-616">Erstellt ein Konfigurationsladeprogramm für das Einrichten von Kestrel, was <xref:Microsoft.Extensions.Configuration.IConfiguration> als Eingabe erfordert.</span><span class="sxs-lookup"><span data-stu-id="76af1-616">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="76af1-617">Die Konfiguration muss auf den Konfigurationsabschnitt für Kestrel festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="76af1-617">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="76af1-618">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="76af1-618">ListenOptions.UseHttps</span></span>

<span data-ttu-id="76af1-619">Konfiguriert Kestrel zur Verwendung von HTTPS.</span><span class="sxs-lookup"><span data-stu-id="76af1-619">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="76af1-620">`ListenOptions.UseHttps`-Erweiterungen:</span><span class="sxs-lookup"><span data-stu-id="76af1-620">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="76af1-621">`UseHttps`: Hiermit wird Kestrel zur Verwendung von HTTPS mit dem Standardzertifikat konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="76af1-621">`UseHttps`: Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="76af1-622">Löst eine Ausnahme aus, wenn kein Standardzertifikat konfiguriert ist.</span><span class="sxs-lookup"><span data-stu-id="76af1-622">Throws an exception if no default certificate is configured.</span></span>
* `UseHttps(string fileName)`
* `UseHttps(string fileName, string password)`
* `UseHttps(string fileName, string password, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(StoreName storeName, string subject)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(X509Certificate2 serverCertificate)`
* `UseHttps(X509Certificate2 serverCertificate, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(Action<HttpsConnectionAdapterOptions> configureOptions)`

<span data-ttu-id="76af1-623">`ListenOptions.UseHttps`-Parameter:</span><span class="sxs-lookup"><span data-stu-id="76af1-623">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="76af1-624">`filename` entspricht dem Pfad und Dateinamen einer Zertifikatdatei relativ zu dem Verzeichnis, das die Inhaltsdateien der App enthält.</span><span class="sxs-lookup"><span data-stu-id="76af1-624">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="76af1-625">`password` ist das für den Zugriff auf die X.509-Zertifikatsdaten erforderliche Kennwort.</span><span class="sxs-lookup"><span data-stu-id="76af1-625">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="76af1-626">`configureOptions` ist eine `Action` zum Konfigurieren von `HttpsConnectionAdapterOptions`.</span><span class="sxs-lookup"><span data-stu-id="76af1-626">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="76af1-627">Gibt `ListenOptions` zurück.</span><span class="sxs-lookup"><span data-stu-id="76af1-627">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="76af1-628">`storeName` ist der Zertifikatspeicher, aus dem das Zertifikat geladen wird.</span><span class="sxs-lookup"><span data-stu-id="76af1-628">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="76af1-629">`subject` ist der Name des Antragstellers für das Zertifikat.</span><span class="sxs-lookup"><span data-stu-id="76af1-629">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="76af1-630">`allowInvalid` gibt an, ob ungültige Zertifikate berücksichtigt werden sollten, z.B. selbstsignierte Zertifikate.</span><span class="sxs-lookup"><span data-stu-id="76af1-630">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="76af1-631">`location` ist der Speicherort, aus dem das Zertifikat geladen wird.</span><span class="sxs-lookup"><span data-stu-id="76af1-631">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="76af1-632">`serverCertificate` ist das X.509-Zertifikat.</span><span class="sxs-lookup"><span data-stu-id="76af1-632">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="76af1-633">In der Produktion muss HTTPS explizit konfiguriert sein.</span><span class="sxs-lookup"><span data-stu-id="76af1-633">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="76af1-634">Zumindest muss ein Standardzertifikat angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="76af1-634">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="76af1-635">Die im Folgenden beschriebenen unterstützten Konfigurationen:</span><span class="sxs-lookup"><span data-stu-id="76af1-635">Supported configurations described next:</span></span>

* <span data-ttu-id="76af1-636">Keine Konfiguration</span><span class="sxs-lookup"><span data-stu-id="76af1-636">No configuration</span></span>
* <span data-ttu-id="76af1-637">Ersetzen des Standardzertifikats aus der Konfiguration</span><span class="sxs-lookup"><span data-stu-id="76af1-637">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="76af1-638">Ändern des Standards im Code</span><span class="sxs-lookup"><span data-stu-id="76af1-638">Change the defaults in code</span></span>

<span data-ttu-id="76af1-639">*Keine Konfiguration*</span><span class="sxs-lookup"><span data-stu-id="76af1-639">*No configuration*</span></span>

<span data-ttu-id="76af1-640">Kestrel überwacht `http://localhost:5000` und `https://localhost:5001` (wenn ein Standardzertifikat verfügbar ist).</span><span class="sxs-lookup"><span data-stu-id="76af1-640">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="76af1-641">*Ersetzen des Standardzertifikats aus der Konfiguration*</span><span class="sxs-lookup"><span data-stu-id="76af1-641">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="76af1-642">`CreateDefaultBuilder` ruft `Configure(context.Configuration.GetSection("Kestrel"))` standardmäßig zum Laden der Kestrel-Konfiguration auf.</span><span class="sxs-lookup"><span data-stu-id="76af1-642">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="76af1-643">Ein Standardkonfigurationsschema für HTTPS-App-Einstellungen ist für Kestrel verfügbar.</span><span class="sxs-lookup"><span data-stu-id="76af1-643">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="76af1-644">Konfigurieren Sie mehrere Endpunkte, einschließlich der zu verwendenden URLs und Zertifikate aus einer Datei auf dem Datenträger oder einem Zertifikatspeicher.</span><span class="sxs-lookup"><span data-stu-id="76af1-644">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="76af1-645">Im folgenden Beispiel für *appsettings.json* gilt:</span><span class="sxs-lookup"><span data-stu-id="76af1-645">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="76af1-646">Legen Sie **AllowInvalid** auf `true` fest, um die Verwendung von ungültigen Zertifikaten zu erlauben (z.B. selbstsignierte Zertifikate).</span><span class="sxs-lookup"><span data-stu-id="76af1-646">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="76af1-647">Jeder HTTPS-Endpunkt, der kein Zertifikat angibt (im folgenden Beispiel **HttpsDefaultCert** ), greift auf das unter **Zertifikate** > **Standard** festgelegte Zertifikat oder das Entwicklungszertifikat zurück.</span><span class="sxs-lookup"><span data-stu-id="76af1-647">Any HTTPS endpoint that doesn't specify a certificate ( **HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "Http": {
        "Url": "http://localhost:5000"
      },

      "HttpsInlineCertFile": {
        "Url": "https://localhost:5001",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      },

      "HttpsInlineCertStore": {
        "Url": "https://localhost:5002",
        "Certificate": {
          "Subject": "<subject; required>",
          "Store": "<certificate store; required>",
          "Location": "<location; defaults to CurrentUser>",
          "AllowInvalid": "<true or false; defaults to false>"
        }
      },

      "HttpsDefaultCert": {
        "Url": "https://localhost:5003"
      },

      "Https": {
        "Url": "https://*:5004",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      }
    },
    "Certificates": {
      "Default": {
        "Path": "<path to .pfx file>",
        "Password": "<certificate password>"
      }
    }
  }
}
```

<span data-ttu-id="76af1-648">Alternativ zur Verwendung von **Pfad** und **Kennwort** für alle Zertifikatknoten können Sie das Zertifikat mithilfe von Zertifikatspeicherfeldern angeben.</span><span class="sxs-lookup"><span data-stu-id="76af1-648">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="76af1-649">Das Zertifikat unter **Zertifikate** > **Standard** kann beispielweise wie folgt angegeben werden:</span><span class="sxs-lookup"><span data-stu-id="76af1-649">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="76af1-650">Schema-Hinweise:</span><span class="sxs-lookup"><span data-stu-id="76af1-650">Schema notes:</span></span>

* <span data-ttu-id="76af1-651">Bei den Namen der Endpunkte wird die Groß-/Kleinschreibung nicht beachtet.</span><span class="sxs-lookup"><span data-stu-id="76af1-651">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="76af1-652">Zum Beispiel sind `HTTPS` und `Https` gültig.</span><span class="sxs-lookup"><span data-stu-id="76af1-652">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="76af1-653">Der Parameter `Url` ist für jeden Endpunkt erforderlich.</span><span class="sxs-lookup"><span data-stu-id="76af1-653">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="76af1-654">Das Format für diesen Parameter ist identisch mit dem allgemeinen Konfigurationsparameter `Urls`, mit der Ausnahme, dass er auf einen einzelnen Wert begrenzt ist.</span><span class="sxs-lookup"><span data-stu-id="76af1-654">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="76af1-655">Diese Endpunkte ersetzen die in der allgemeinen `Urls`-Konfiguration festgelegten Endpunkte, anstatt zu ihnen hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="76af1-655">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="76af1-656">Endpunkte, die über `Listen` in Code definiert werden, werden den im Konfigurationsabschnitt definierten Endpunkten hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="76af1-656">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="76af1-657">Der `Certificate`-Abschnitt ist optional.</span><span class="sxs-lookup"><span data-stu-id="76af1-657">The `Certificate` section is optional.</span></span> <span data-ttu-id="76af1-658">Wenn der `Certificate`-Abschnitt nicht angegeben ist, werden die in früheren Szenarios definierten Standardwerte verwendet.</span><span class="sxs-lookup"><span data-stu-id="76af1-658">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="76af1-659">Wenn keine Standardwerte verfügbar sind, löst der Server eine Ausnahme aus und startet nicht.</span><span class="sxs-lookup"><span data-stu-id="76af1-659">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="76af1-660">Der `Certificate`-Abschnitt unterstützt die Zertifikate **Pfad**&ndash;**Kennwort** und **Subject**&ndash;**Store** (Antragsteller–Speicher).</span><span class="sxs-lookup"><span data-stu-id="76af1-660">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="76af1-661">Auf diese Weise kann eine beliebige Anzahl von Endpunkten definiert werden, solange Sie keine Portkonflikte verursachen.</span><span class="sxs-lookup"><span data-stu-id="76af1-661">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="76af1-662">`options.Configure(context.Configuration.GetSection("{SECTION}"))` gibt `KestrelConfigurationLoader` mit der Methode `.Endpoint(string name, listenOptions => { })` zurück, die dazu verwendet werden kann, die Einstellungen eines Endpunkts zu ergänzen:</span><span class="sxs-lookup"><span data-stu-id="76af1-662">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.Configure(context.Configuration.GetSection("Kestrel"))
                .Endpoint("HTTPS", listenOptions =>
                {
                    listenOptions.HttpsOptions.SslProtocols = SslProtocols.Tls12;
                });
        });
```

<span data-ttu-id="76af1-663">Auf `KestrelServerOptions.ConfigurationLoader` kann direkt zugegriffen werden, um die Iteration auf dem vorhandenen Ladeprogramm fortzusetzen, etwa auf dem von <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> bereitgestellten Ladeprogramm.</span><span class="sxs-lookup"><span data-stu-id="76af1-663">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="76af1-664">Der Konfigurationsabschnitt ist für jeden Endpunkt in den Optionen der Methode `Endpoint` verfügbar, sodass benutzerdefinierte Einstellungen gelesen werden können.</span><span class="sxs-lookup"><span data-stu-id="76af1-664">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="76af1-665">Mehrere Konfigurationen können durch erneutes Aufrufen von `options.Configure(context.Configuration.GetSection("{SECTION}"))` mit einem anderen Abschnitt geladen werden.</span><span class="sxs-lookup"><span data-stu-id="76af1-665">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="76af1-666">Sofern `Load` nicht in einer vorherigen Instanz explizit aufgerufen wird, wird nur die letzte Konfiguration verwendet.</span><span class="sxs-lookup"><span data-stu-id="76af1-666">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="76af1-667">Das Metapaket ruft `Load` nicht auf, sodass der Abschnitt mit der Standardkonfiguration ersetzt werden kann.</span><span class="sxs-lookup"><span data-stu-id="76af1-667">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="76af1-668">`KestrelConfigurationLoader` spiegelt die API-Familie `Listen` von `KestrelServerOptions` als `Endpoint`-Überladungen, weshalb Code und Konfigurationsendpunkte am selben Ort konfiguriert werden können.</span><span class="sxs-lookup"><span data-stu-id="76af1-668">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="76af1-669">Diese Überladungen verwenden keine Namen und nutzen nur Standardeinstellungen aus der Konfiguration.</span><span class="sxs-lookup"><span data-stu-id="76af1-669">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="76af1-670">*Ändern des Standards im Code*</span><span class="sxs-lookup"><span data-stu-id="76af1-670">*Change the defaults in code*</span></span>

<span data-ttu-id="76af1-671">`ConfigureEndpointDefaults` und `ConfigureHttpsDefaults` können zum Ändern der Standardeinstellungen für `ListenOptions` und `HttpsConnectionAdapterOptions` verwendet werden, einschließlich der Standardzertifikate, die im vorherigen Szenario festgelegt wurden.</span><span class="sxs-lookup"><span data-stu-id="76af1-671">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="76af1-672">`ConfigureEndpointDefaults` und `ConfigureHttpsDefaults` sollten aufgerufen werden, bevor Endpunkte konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="76af1-672">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureEndpointDefaults(listenOptions =>
            {
                // Configure endpoint defaults
            });
            
            serverOptions.ConfigureHttpsDefaults(listenOptions =>
            {
                listenOptions.SslProtocols = SslProtocols.Tls12;
            });
        });
```

<span data-ttu-id="76af1-673">*Kestrel-Unterstützung für SNI*</span><span class="sxs-lookup"><span data-stu-id="76af1-673">*Kestrel support for SNI*</span></span>

<span data-ttu-id="76af1-674">Die [Servernamensanzeige (SNI)](https://tools.ietf.org/html/rfc6066#section-3) kann zum Hosten mehrerer Domänen auf der gleichen IP-Adresse und dem gleichen Port verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="76af1-674">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="76af1-675">Damit die Servernamensanzeige funktioniert, sendet der Client während des TLS-Handshakes den Hostnamen für die sichere Sitzung an den Server, sodass der Server das richtige Zertifikat bereitstellen kann.</span><span class="sxs-lookup"><span data-stu-id="76af1-675">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="76af1-676">Der Client verwendet das beigestellte Zertifikat für die verschlüsselte Kommunikation mit dem Server während der sicheren Sitzung nach dem TLS-Handshake.</span><span class="sxs-lookup"><span data-stu-id="76af1-676">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="76af1-677">Kestrel unterstützt die Servernamensanzeige über den `ServerCertificateSelector`-Rückruf.</span><span class="sxs-lookup"><span data-stu-id="76af1-677">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="76af1-678">Der Rückruf wird für jede Verbindung einmal aufgerufen, um der App zu ermöglichen, den Hostnamen zu überprüfen und das entsprechende Zertifikat auszuwählen.</span><span class="sxs-lookup"><span data-stu-id="76af1-678">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="76af1-679">Für die Unterstützung der Servernamensanzeige benötigen Sie Folgendes:</span><span class="sxs-lookup"><span data-stu-id="76af1-679">SNI support requires:</span></span>

* <span data-ttu-id="76af1-680">Wird auf dem Zielframework `netcoreapp2.1` oder höher ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="76af1-680">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="76af1-681">In `net461` oder höher, wird der Rückruf aufgerufen, `name` ist aber immer `null`.</span><span class="sxs-lookup"><span data-stu-id="76af1-681">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="76af1-682">`name` ist auch `null`, wenn der Client den Hostnamenparameter nicht im TLS-Handshake angibt.</span><span class="sxs-lookup"><span data-stu-id="76af1-682">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="76af1-683">Alle Websites werden in derselben Kestrel-Instanz ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="76af1-683">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="76af1-684">Kestrel unterstützt ohne Reverseproxy keine gemeinsame IP-Adresse und keinen gemeinsamen Port für mehrere Instanzen.</span><span class="sxs-lookup"><span data-stu-id="76af1-684">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ListenAnyIP(5005, listenOptions =>
            {
                listenOptions.UseHttps(httpsOptions =>
                {
                    var localhostCert = CertificateLoader.LoadFromStoreCert(
                        "localhost", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var exampleCert = CertificateLoader.LoadFromStoreCert(
                        "example.com", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var subExampleCert = CertificateLoader.LoadFromStoreCert(
                        "sub.example.com", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var certs = new Dictionary<string, X509Certificate2>(
                        StringComparer.OrdinalIgnoreCase);
                    certs["localhost"] = localhostCert;
                    certs["example.com"] = exampleCert;
                    certs["sub.example.com"] = subExampleCert;

                    httpsOptions.ServerCertificateSelector = (connectionContext, name) =>
                    {
                        if (name != null && certs.TryGetValue(name, out var cert))
                        {
                            return cert;
                        }

                        return exampleCert;
                    };
                });
            });
        });
```

### <a name="connection-logging"></a><span data-ttu-id="76af1-685">Verbindungsprotokollierung</span><span class="sxs-lookup"><span data-stu-id="76af1-685">Connection logging</span></span>

<span data-ttu-id="76af1-686">Rufen Sie <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> auf, um Protokolle auf Debugebene für die Kommunikation auf Byteebene für eine Verbindung auszugeben.</span><span class="sxs-lookup"><span data-stu-id="76af1-686">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="76af1-687">Die Verbindungsprotokollierung ist beim Beheben von Problemen bei der Low-Level-Kommunikation hilfreich, wie z. B. bei der TLS-Verschlüsselung und bei Proxys.</span><span class="sxs-lookup"><span data-stu-id="76af1-687">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="76af1-688">Wenn `UseConnectionLogging` vor `UseHttps` platziert wird, wird der verschlüsselte Datenverkehr protokolliert.</span><span class="sxs-lookup"><span data-stu-id="76af1-688">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="76af1-689">Wenn `UseConnectionLogging` nach `UseHttps` platziert wird, wird der entschlüsselte Datenverkehr protokolliert.</span><span class="sxs-lookup"><span data-stu-id="76af1-689">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="76af1-690">Binden an einen TCP-Socket</span><span class="sxs-lookup"><span data-stu-id="76af1-690">Bind to a TCP socket</span></span>

<span data-ttu-id="76af1-691">Die <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*>-Methode wird an ein TCP-Socket gebunden, und ein Lambdaausdruck einer Option lässt die Konfiguration des X.509-Zertifikats zu:</span><span class="sxs-lookup"><span data-stu-id="76af1-691">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=9-16)]

<span data-ttu-id="76af1-692">Im Beispiel wird HTTPS für einen Endpunkt mit <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions> konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="76af1-692">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="76af1-693">Verwenden Sie die gleiche API zum Konfigurieren anderer Kestrel-Einstellungen für bestimmte Endpunkte.</span><span class="sxs-lookup"><span data-stu-id="76af1-693">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="76af1-694">Binden an einen Unix-Socket</span><span class="sxs-lookup"><span data-stu-id="76af1-694">Bind to a Unix socket</span></span>

<span data-ttu-id="76af1-695">Wie in diesem Beispiel dargestellt, lauschen Sie an einem Unix-Socket mit <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*>, um eine verbesserte Leistung mit Nginx zu erzielen:</span><span class="sxs-lookup"><span data-stu-id="76af1-695">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

* <span data-ttu-id="76af1-696">Legen Sie in der Nginx-Konfigurationsdatei den Eintrag `server` > `location` > `proxy_pass` auf `http://unix:/tmp/{KESTREL SOCKET}:/;` fest.</span><span class="sxs-lookup"><span data-stu-id="76af1-696">In the Nginx confiuguration file, set the `server` > `location` > `proxy_pass` entry to `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span></span> <span data-ttu-id="76af1-697">`{KESTREL SOCKET}` ist der Name des für <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> bereitgestellten Socket (zum Beispiel `kestrel-test.sock` im vorherigen Beispiel).</span><span class="sxs-lookup"><span data-stu-id="76af1-697">`{KESTREL SOCKET}` is the name of the socket provided to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (for example, `kestrel-test.sock` in the preceding example).</span></span>
* <span data-ttu-id="76af1-698">Stellen Sie sicher, dass der Socket von Nginx beschreibbar ist (z. B. `chmod go+w /tmp/kestrel-test.sock`).</span><span class="sxs-lookup"><span data-stu-id="76af1-698">Ensure that the socket is writeable by Nginx (for example, `chmod go+w /tmp/kestrel-test.sock`).</span></span> 

### <a name="port-0"></a><span data-ttu-id="76af1-699">Port 0</span><span class="sxs-lookup"><span data-stu-id="76af1-699">Port 0</span></span>

<span data-ttu-id="76af1-700">Wenn die Portnummer `0` angegeben wird, wird Kestrel dynamisch an einen verfügbaren Port gebunden.</span><span class="sxs-lookup"><span data-stu-id="76af1-700">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="76af1-701">Im folgenden Beispiel wird veranschaulicht, wie bestimmt werden kann, für welchen Port Kestrel zur Laufzeit eine Bindung erstellt hat:</span><span class="sxs-lookup"><span data-stu-id="76af1-701">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="76af1-702">Wenn die App ausgeführt wird, gibt das Ausgabefenster der Konsole den dynamischen Port an, über den die App erreicht werden kann:</span><span class="sxs-lookup"><span data-stu-id="76af1-702">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="76af1-703">Einschränkungen</span><span class="sxs-lookup"><span data-stu-id="76af1-703">Limitations</span></span>

<span data-ttu-id="76af1-704">Konfigurieren Sie Endpunkte mithilfe der folgenden Ansätze:</span><span class="sxs-lookup"><span data-stu-id="76af1-704">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="76af1-705">Befehlszeilenargument `--urls`</span><span class="sxs-lookup"><span data-stu-id="76af1-705">`--urls` command-line argument</span></span>
* <span data-ttu-id="76af1-706">Hostkonfigurationsschlüssel `urls`</span><span class="sxs-lookup"><span data-stu-id="76af1-706">`urls` host configuration key</span></span>
* <span data-ttu-id="76af1-707">Umgebungsvariable `ASPNETCORE_URLS`</span><span class="sxs-lookup"><span data-stu-id="76af1-707">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="76af1-708">Diese Methoden sind nützlich, wenn Ihr Code mit anderen Servern als Kestrel funktionieren soll.</span><span class="sxs-lookup"><span data-stu-id="76af1-708">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="76af1-709">Beachten Sie jedoch die folgenden Einschränkungen:</span><span class="sxs-lookup"><span data-stu-id="76af1-709">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="76af1-710">HTTPS kann nicht mit diesen Ansätzen verwendet werden, außer ein Standardzertifikat wird in der HTTPS-Endpunktkonfiguration angegeben (z.B. wenn Sie wie zuvor in diesem Artikel gezeigt die `KestrelServerOptions`-Konfiguration oder eine Konfigurationsdatei verwenden).</span><span class="sxs-lookup"><span data-stu-id="76af1-710">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="76af1-711">Wenn die Ansätze `Listen` und `UseUrls` gleichzeitig verwendet werden, überschreiben die `Listen`-Endpunkte die `UseUrls`-Endpunkte.</span><span class="sxs-lookup"><span data-stu-id="76af1-711">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="76af1-712">IIS-Endpunktkonfiguration</span><span class="sxs-lookup"><span data-stu-id="76af1-712">IIS endpoint configuration</span></span>

<span data-ttu-id="76af1-713">Bei der Verwendung von IIS werden die URL-Bindungen für IIS-Überschreibungsbindungen durch `Listen` oder `UseUrls` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="76af1-713">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="76af1-714">Weitere Informationen finden Sie im Artikel [ASP.NET Core-Modul](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="76af1-714">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

### <a name="listenoptionsprotocols"></a><span data-ttu-id="76af1-715">ListenOptions.Protocols</span><span class="sxs-lookup"><span data-stu-id="76af1-715">ListenOptions.Protocols</span></span>

<span data-ttu-id="76af1-716">Die `Protocols`-Eigenschaft richtet die HTTP-Protokolle (`HttpProtocols`) ein, die für einen Verbindungsendpunkt oder für den Server aktiviert werden.</span><span class="sxs-lookup"><span data-stu-id="76af1-716">The `Protocols` property establishes the HTTP protocols (`HttpProtocols`) enabled on a connection endpoint or for the server.</span></span> <span data-ttu-id="76af1-717">Weisen Sie der `Protocols`-Eigenschaft einen Wert aus der `HttpProtocols`-Enumeration zu.</span><span class="sxs-lookup"><span data-stu-id="76af1-717">Assign a value to the `Protocols` property from the `HttpProtocols` enum.</span></span>

| <span data-ttu-id="76af1-718">`HttpProtocols`-Enumerationswert</span><span class="sxs-lookup"><span data-stu-id="76af1-718">`HttpProtocols` enum value</span></span> | <span data-ttu-id="76af1-719">Zulässiges Verbindungsprotokoll</span><span class="sxs-lookup"><span data-stu-id="76af1-719">Connection protocol permitted</span></span> |
| -------------------------- | ----------------------------- |
| `Http1`                    | <span data-ttu-id="76af1-720">Nur HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="76af1-720">HTTP/1.1 only.</span></span> <span data-ttu-id="76af1-721">Kann mit oder ohne TLS verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="76af1-721">Can be used with or without TLS.</span></span> |
| `Http2`                    | <span data-ttu-id="76af1-722">Nur HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="76af1-722">HTTP/2 only.</span></span> <span data-ttu-id="76af1-723">Kann nur ohne TLS verwendet werden, wenn der Client einen [Vorabkenntnis-Modus](https://tools.ietf.org/html/rfc7540#section-3.4) unterstützt.</span><span class="sxs-lookup"><span data-stu-id="76af1-723">May be used without TLS only if the client supports a [Prior Knowledge mode](https://tools.ietf.org/html/rfc7540#section-3.4).</span></span> |
| `Http1AndHttp2`            | <span data-ttu-id="76af1-724">HTTP/1.1 und HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="76af1-724">HTTP/1.1 and HTTP/2.</span></span> <span data-ttu-id="76af1-725">HTTP/2 erfordert eine TLS- und [ALPN](https://tools.ietf.org/html/rfc7301#section-3)-Verbindung (Application-Layer Protocol Negotiation). Andernfalls wird für die Verbindung standardmäßig HTTP/1.1 verwendet.</span><span class="sxs-lookup"><span data-stu-id="76af1-725">HTTP/2 requires a TLS and [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection; otherwise, the connection defaults to HTTP/1.1.</span></span> |

<span data-ttu-id="76af1-726">Das Standardprotokoll ist HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="76af1-726">The default protocol is HTTP/1.1.</span></span>

<span data-ttu-id="76af1-727">TLS-Einschränkungen für HTTP/2:</span><span class="sxs-lookup"><span data-stu-id="76af1-727">TLS restrictions for HTTP/2:</span></span>

* <span data-ttu-id="76af1-728">TLS Version 1.2 oder höher</span><span class="sxs-lookup"><span data-stu-id="76af1-728">TLS version 1.2 or later</span></span>
* <span data-ttu-id="76af1-729">Erneute Aushandlung deaktiviert</span><span class="sxs-lookup"><span data-stu-id="76af1-729">Renegotiation disabled</span></span>
* <span data-ttu-id="76af1-730">Komprimierung deaktiviert</span><span class="sxs-lookup"><span data-stu-id="76af1-730">Compression disabled</span></span>
* <span data-ttu-id="76af1-731">Minimale Größen für Austausch von flüchtigen Schlüsseln:</span><span class="sxs-lookup"><span data-stu-id="76af1-731">Minimum ephemeral key exchange sizes:</span></span>
  * <span data-ttu-id="76af1-732">ECDHE (Elliptic Curve Diffie-Hellman) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack;: mindestens 224 Bits</span><span class="sxs-lookup"><span data-stu-id="76af1-732">Elliptic curve Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack;: 224 bits minimum</span></span>
  * <span data-ttu-id="76af1-733">DHE (Finite Field Diffie-Hellman) &lbrack;`TLS12`&rbrack;: mindestens 2048 Bits</span><span class="sxs-lookup"><span data-stu-id="76af1-733">Finite field Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack;: 2048 bits minimum</span></span>
* <span data-ttu-id="76af1-734">Verschlüsselungssammlung nicht gesperrt</span><span class="sxs-lookup"><span data-stu-id="76af1-734">Cipher suite not blocked</span></span>

<span data-ttu-id="76af1-735">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; mit der elliptischen P-256-Kurve &lbrack;`FIPS186`&rbrack; wird standardmäßig unterstützt.</span><span class="sxs-lookup"><span data-stu-id="76af1-735">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; with the P-256 elliptic curve &lbrack;`FIPS186`&rbrack; is supported by default.</span></span>

<span data-ttu-id="76af1-736">Das folgende Beispiel erlaubt HTTP/1.1- und HTTP/2-Verbindungen an Port 8000.</span><span class="sxs-lookup"><span data-stu-id="76af1-736">The following example permits HTTP/1.1 and HTTP/2 connections on port 8000.</span></span> <span data-ttu-id="76af1-737">Die Verbindungen werden durch TLS mit einem bereitgestellten Zertifikat geschützt:</span><span class="sxs-lookup"><span data-stu-id="76af1-737">Connections are secured by TLS with a supplied certificate:</span></span>

```csharp
.ConfigureKestrel((context, serverOptions) =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.Protocols = HttpProtocols.Http1AndHttp2;
        listenOptions.UseHttps("testCert.pfx", "testPassword");
    });
});
```

<span data-ttu-id="76af1-738">Erstellen Sie optional eine `IConnectionAdapter`-Implementierung, um TLS-Handshakes auf Verbindungsbasis für bestimmte Verschlüsselungen zu filtern:</span><span class="sxs-lookup"><span data-stu-id="76af1-738">Optionally create an `IConnectionAdapter` implementation to filter TLS handshakes on a per-connection basis for specific ciphers:</span></span>

```csharp
.ConfigureKestrel((context, serverOptions) =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.Protocols = HttpProtocols.Http1AndHttp2;
        listenOptions.UseHttps("testCert.pfx", "testPassword");
        listenOptions.ConnectionAdapters.Add(new TlsFilterAdapter());
    });
});
```

```csharp
private class TlsFilterAdapter : IConnectionAdapter
{
    public bool IsHttps => false;

    public Task<IAdaptedConnection> OnConnectionAsync(ConnectionAdapterContext context)
    {
        var tlsFeature = context.Features.Get<ITlsHandshakeFeature>();

        // Throw NotSupportedException for any cipher algorithm that the app doesn't
        // wish to support. Alternatively, define and compare
        // ITlsHandshakeFeature.CipherAlgorithm to a list of acceptable cipher
        // suites.
        //
        // No encryption is used with a CipherAlgorithmType.Null cipher algorithm.
        if (tlsFeature.CipherAlgorithm == CipherAlgorithmType.Null)
        {
            throw new NotSupportedException("Prohibited cipher: " + tlsFeature.CipherAlgorithm);
        }

        return Task.FromResult<IAdaptedConnection>(new AdaptedConnection(context.ConnectionStream));
    }

    private class AdaptedConnection : IAdaptedConnection
    {
        public AdaptedConnection(Stream adaptedStream)
        {
            ConnectionStream = adaptedStream;
        }

        public Stream ConnectionStream { get; }

        public void Dispose()
        {
        }
    }
}
```

<span data-ttu-id="76af1-739">*Festlegen des Protokolls aus der Konfiguration*</span><span class="sxs-lookup"><span data-stu-id="76af1-739">*Set the protocol from configuration*</span></span>

<span data-ttu-id="76af1-740"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> ruft `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` standardmäßig zum Laden der Kestrel-Konfiguration auf.</span><span class="sxs-lookup"><span data-stu-id="76af1-740"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> calls `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span>

<span data-ttu-id="76af1-741">Im folgenden Beispiel *appsettings.json* wird für alle Endpunkte von Kestrel ein Standardverbindungsprotokoll (HTTP/1.1 und HTTP/2) eingerichtet:</span><span class="sxs-lookup"><span data-stu-id="76af1-741">In the following *appsettings.json* example, a default connection protocol (HTTP/1.1 and HTTP/2) is established for all of Kestrel's endpoints:</span></span>

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1AndHttp2"
    }
  }
}
```

<span data-ttu-id="76af1-742">Das folgende Beispiel einer Konfigurationsdatei richtet ein Verbindungsprotokoll für einen bestimmten Endpunkt ein:</span><span class="sxs-lookup"><span data-stu-id="76af1-742">The following configuration file example establishes a connection protocol for a specific endpoint:</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "HttpsDefaultCert": {
        "Url": "https://localhost:5001",
        "Protocols": "Http1AndHttp2"
      }
    }
  }
}
```

<span data-ttu-id="76af1-743">Protokolle, die in Code angegeben werden, setzen Werte außer Kraft, der durch die Konfiguration festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="76af1-743">Protocols specified in code override values set by configuration.</span></span>

## <a name="transport-configuration"></a><span data-ttu-id="76af1-744">Transportkonfiguration</span><span class="sxs-lookup"><span data-stu-id="76af1-744">Transport configuration</span></span>

<span data-ttu-id="76af1-745">Mit dem Release von ASP.NET Core 2.1 basiert der Standardtransport von Kestrel nicht mehr auf Libuv, sondern auf verwalteten Sockets.</span><span class="sxs-lookup"><span data-stu-id="76af1-745">With the release of ASP.NET Core 2.1, Kestrel's default transport is no longer based on Libuv but instead based on managed sockets.</span></span> <span data-ttu-id="76af1-746">Dies stellt einen Breaking Change für ASP.NET Core 2.0-Apps dar, die auf Version 2.1 upgraden, <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> aufrufen und von einem der folgenden Pakete abhängig sind:</span><span class="sxs-lookup"><span data-stu-id="76af1-746">This is a breaking change for ASP.NET Core 2.0 apps upgrading to 2.1 that call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> and depend on either of the following packages:</span></span>

* <span data-ttu-id="76af1-747">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (direkter Paketverweis)</span><span class="sxs-lookup"><span data-stu-id="76af1-747">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (direct package reference)</span></span>
* [<span data-ttu-id="76af1-748">Microsoft.AspNetCore.App</span><span class="sxs-lookup"><span data-stu-id="76af1-748">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)

<span data-ttu-id="76af1-749">Für Projekte, die den Einsatz von Libuv erfordern:</span><span class="sxs-lookup"><span data-stu-id="76af1-749">For projects that require the use of Libuv:</span></span>

* <span data-ttu-id="76af1-750">Fügen Sie für das [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/)-Paket eine Abhängigkeit auf die Projektdatei der App hinzu:</span><span class="sxs-lookup"><span data-stu-id="76af1-750">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* <span data-ttu-id="76af1-751">Rufen Sie <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> auf:</span><span class="sxs-lookup"><span data-stu-id="76af1-751">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span></span>

  ```csharp
  public class Program
  {
      public static void Main(string[] args)
      {
          CreateWebHostBuilder(args).Build().Run();
      }

      public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
          WebHost.CreateDefaultBuilder(args)
              .UseLibuv()
              .UseStartup<Startup>();
  }
  ```

### <a name="url-prefixes"></a><span data-ttu-id="76af1-752">URL-Präfixe</span><span class="sxs-lookup"><span data-stu-id="76af1-752">URL prefixes</span></span>

<span data-ttu-id="76af1-753">Bei der Verwendung von `UseUrls`, dem Befehlszeilenargument `--urls`, dem Hostkonfigurationsschlüssel `urls` oder der Umgebungsvariable `ASPNETCORE_URLS` können die URL-Präfixe in den folgenden Formaten vorliegen.</span><span class="sxs-lookup"><span data-stu-id="76af1-753">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="76af1-754">Nur HTTP-URL-Präfixe sind gültig.</span><span class="sxs-lookup"><span data-stu-id="76af1-754">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="76af1-755">Kestrel unterstützt HTTPS nicht beim Konfigurieren von URL-Bindungen mit `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="76af1-755">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="76af1-756">IPv4-Adresse mit Portnummer</span><span class="sxs-lookup"><span data-stu-id="76af1-756">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="76af1-757">Bei `0.0.0.0` handelt es sich um einen Sonderfall, für den eine Bindung an alle IPv4-Adressen erfolgt.</span><span class="sxs-lookup"><span data-stu-id="76af1-757">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="76af1-758">IPv6-Adresse mit Portnummer</span><span class="sxs-lookup"><span data-stu-id="76af1-758">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="76af1-759">`[::]` stellt das Äquivalent von IPv6 zu `0.0.0.0` für IPv4 dar.</span><span class="sxs-lookup"><span data-stu-id="76af1-759">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="76af1-760">Hostname mit Portnummer</span><span class="sxs-lookup"><span data-stu-id="76af1-760">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="76af1-761">Hostnamen, `*` und `+` sind nicht spezifisch.</span><span class="sxs-lookup"><span data-stu-id="76af1-761">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="76af1-762">Alle Elemente, die nicht als gültige IP-Adresse oder `localhost` erkannt werden, werden an alle IPv4- und IPv6-IP-Adressen gebunden.</span><span class="sxs-lookup"><span data-stu-id="76af1-762">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="76af1-763">Verwenden Sie [HTTP.sys](xref:fundamentals/servers/httpsys) oder einen Reverseproxyserver zum Binden verschiedener Hostnamen an verschiedene ASP.NET Core-Apps auf demselben Port, z.B. IIS, Nginx oder Apache.</span><span class="sxs-lookup"><span data-stu-id="76af1-763">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="76af1-764">Das Hosting in einer Reverseproxykonfiguration erfordert [Hostfilterung](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="76af1-764">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="76af1-765">`localhost`-Hostname mit Portnummer oder Loopback-IP mit Portnummer</span><span class="sxs-lookup"><span data-stu-id="76af1-765">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="76af1-766">Wenn `localhost` angegeben ist, versucht Kestrel, eine Bindung zu IPv4- und IPv6-Loopback-Schnittstellen zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="76af1-766">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="76af1-767">Wenn der erforderliche Port von einem anderen Dienst auf einer der Loopback-Schnittstellen verwendet wird, tritt beim Starten von Kestrel ein Fehler auf.</span><span class="sxs-lookup"><span data-stu-id="76af1-767">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="76af1-768">Wenn eine der Loopback-Schnittstellen aus anderen Gründen nicht verfügbar ist (meistens durch die fehlende Unterstützung von IPv6), protokolliert Kestrel eine Warnung.</span><span class="sxs-lookup"><span data-stu-id="76af1-768">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="76af1-769">Host-Filterung</span><span class="sxs-lookup"><span data-stu-id="76af1-769">Host filtering</span></span>

<span data-ttu-id="76af1-770">Obwohl Kestrel die Konfiguration basierend auf Präfixe wie `http://example.com:5000` unterstützt, ignoriert Kestrel den Hostnamen weitgehend.</span><span class="sxs-lookup"><span data-stu-id="76af1-770">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="76af1-771">Der Host `localhost` ist ein Sonderfall, der für die Bindung an Loopback-Adressen verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="76af1-771">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="76af1-772">Jeder Host, der keine explizite IP-Adresse ist, wird an alle öffentlichen IP-Adressen gebunden.</span><span class="sxs-lookup"><span data-stu-id="76af1-772">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="76af1-773">`Host`-Header werden nicht überprüft.</span><span class="sxs-lookup"><span data-stu-id="76af1-773">`Host` headers aren't validated.</span></span>

<span data-ttu-id="76af1-774">Verwenden Sie Middleware zum Filtern von Hosts, um dieses Problem zu umgehen.</span><span class="sxs-lookup"><span data-stu-id="76af1-774">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="76af1-775">Middleware zum Filtern von Hosts wird im Paket [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) bereitgestellt, das im [Metapaket Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) enthalten ist (ASP.NET Core 2.1 oder 2.2).</span><span class="sxs-lookup"><span data-stu-id="76af1-775">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or 2.2).</span></span> <span data-ttu-id="76af1-776">Die Middleware wird von <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> hinzugefügt, wodurch <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*> aufgerufen wird:</span><span class="sxs-lookup"><span data-stu-id="76af1-776">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="76af1-777">Die Middleware zum Filtern von Hosts ist standardmäßig deaktiviert.</span><span class="sxs-lookup"><span data-stu-id="76af1-777">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="76af1-778">Wenn Sie die Middleware aktivieren möchten, definieren Sie einen `AllowedHosts`-Schlüssel in *appsettings.json* /*appsettings.\<EnvironmentName>json*.</span><span class="sxs-lookup"><span data-stu-id="76af1-778">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="76af1-779">Der Wert ist eine durch Semikolons getrennte Liste von Hostnamen ohne Portnummern:</span><span class="sxs-lookup"><span data-stu-id="76af1-779">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="76af1-780">*appsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="76af1-780">*appsettings.json* :</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="76af1-781">[Middleware für weitergeleitete Header](xref:host-and-deploy/proxy-load-balancer) hat auch eine <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts>-Option.</span><span class="sxs-lookup"><span data-stu-id="76af1-781">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="76af1-782">Middleware für weitergeleitete Header und Middleware zum Filtern von Hosts besitzen ähnliche Funktionen für unterschiedliche Szenarios.</span><span class="sxs-lookup"><span data-stu-id="76af1-782">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="76af1-783">Legen Sie `AllowedHosts` mit Middleware für weitergeleitete Header fest, wenn der `Host`-Header beim Weiterleiten von Anforderungen mit einem Reverseproxyserver oder einem Lastenausgleichsmodul nicht beibehalten wird.</span><span class="sxs-lookup"><span data-stu-id="76af1-783">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="76af1-784">Legen Sie `AllowedHosts` mit Middleware zum Filtern von Hosts fest, wenn Kestrel als öffentlicher Edgeserver verwendet oder der `Host`-Header direkt weitergeleitet wird.</span><span class="sxs-lookup"><span data-stu-id="76af1-784">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="76af1-785">Weitere Informationen zu Middleware für weitergeleitete Header finden Sie unter <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="76af1-785">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="76af1-786">Einführung in Kestrel, dem plattformübergreifenden [Webserver für ASP.NET Core](xref:fundamentals/servers/index).</span><span class="sxs-lookup"><span data-stu-id="76af1-786">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="76af1-787">Kestrel ist der Webserver, der standardmäßig in ASP.NET Core-Projektvorlagen enthalten ist.</span><span class="sxs-lookup"><span data-stu-id="76af1-787">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="76af1-788">Kestrel unterstützt die folgenden Szenarios:</span><span class="sxs-lookup"><span data-stu-id="76af1-788">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="76af1-789">HTTPS</span><span class="sxs-lookup"><span data-stu-id="76af1-789">HTTPS</span></span>
* <span data-ttu-id="76af1-790">Nicht transparente Upgrades, die zum Aktivieren von [WebSockets](https://github.com/aspnet/websockets) verwendet werden</span><span class="sxs-lookup"><span data-stu-id="76af1-790">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="76af1-791">Unix-Sockets für eine hohe Leistung im Hintergrund von Nginx</span><span class="sxs-lookup"><span data-stu-id="76af1-791">Unix sockets for high performance behind Nginx</span></span>

<span data-ttu-id="76af1-792">Kestrel wird auf allen Plattformen und für alle Versionen unterstützt, die .NET Core unterstützt.</span><span class="sxs-lookup"><span data-stu-id="76af1-792">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="76af1-793">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="76af1-793">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="76af1-794">Verwenden von Kestrel mit einem Reverseproxy</span><span class="sxs-lookup"><span data-stu-id="76af1-794">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="76af1-795">Kestrel kann eigenständig oder mit einem *Reverseproxyserver* wie z.B. [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org) oder [Apache](https://httpd.apache.org/) verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="76af1-795">Kestrel can be used by itself or with a *reverse proxy server* , such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="76af1-796">Ein Reverseproxyserver empfängt HTTP-Anforderungen aus dem Netzwerk und leitet diese an Kestrel weiter.</span><span class="sxs-lookup"><span data-stu-id="76af1-796">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="76af1-797">Kestrel bei Verwendung als Webserver mit direkter Internetverbindung:</span><span class="sxs-lookup"><span data-stu-id="76af1-797">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Kestrel kommuniziert direkt und ohne Reverseproxyserver mit dem Internet](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="76af1-799">Kestrel bei Verwendung in einer Reverseproxykonfiguration:</span><span class="sxs-lookup"><span data-stu-id="76af1-799">Kestrel used in a reverse proxy configuration:</span></span>

![Kestrel kommuniziert indirekt mit dem Internet über einen Reverseproxyserver wie IIS, Nginx oder Apache](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="76af1-801">Jede der beiden Konfigurationen – mit oder ohne einen Reverseproxyserver – stellt eine unterstützte Hostingkonfiguration dar.</span><span class="sxs-lookup"><span data-stu-id="76af1-801">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="76af1-802">Bei Verwendung als Edgeserver ohne Reverseproxyserver unterstützt Kestrel die gemeinsame Nutzung der gleichen IP-Adresse und des gleichen Ports durch mehrere Prozesse nicht.</span><span class="sxs-lookup"><span data-stu-id="76af1-802">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="76af1-803">Wenn Kestrel für das Lauschen an einem Port konfiguriert ist, verarbeitet Kestrel den gesamten Datenverkehr für diesen Port unabhängig von den `Host`-Headern der Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="76af1-803">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="76af1-804">Ein Reverseproxy, der Ports freigeben kann, kann Anforderungen an Kestrel über eine eindeutige IP und einen eindeutigen Port weiterleiten.</span><span class="sxs-lookup"><span data-stu-id="76af1-804">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="76af1-805">Auch wenn kein Reverseproxyserver erforderlich ist, kann die Verwendung eines solchen empfehlenswert sein.</span><span class="sxs-lookup"><span data-stu-id="76af1-805">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="76af1-806">Für einen Reverseproxy gilt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="76af1-806">A reverse proxy:</span></span>

* <span data-ttu-id="76af1-807">Er kann die verfügbar gemachten öffentlichen Oberflächen der von ihm gehosteten Apps einschränken.</span><span class="sxs-lookup"><span data-stu-id="76af1-807">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="76af1-808">Er stellt eine zusätzliche Ebene für Konfiguration und Schutz bereit.</span><span class="sxs-lookup"><span data-stu-id="76af1-808">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="76af1-809">Er lässt sich besser in die vorhandene Infrastruktur integrieren.</span><span class="sxs-lookup"><span data-stu-id="76af1-809">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="76af1-810">Er vereinfacht die Konfiguration von Lastenausgleich und sicherer Kommunikation (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="76af1-810">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="76af1-811">Nur der Reverseproxyserver erfordert ein X.509-Zertifikat, und dieser Server kann mit den Servern der App im internen Netzwerk über einfaches HTTP kommunizieren.</span><span class="sxs-lookup"><span data-stu-id="76af1-811">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="76af1-812">Das Hosting in einer Reverseproxykonfiguration erfordert [Hostfilterung](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="76af1-812">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a><span data-ttu-id="76af1-813">Verwenden von Kestrel in ASP.NET Core-Apps</span><span class="sxs-lookup"><span data-stu-id="76af1-813">How to use Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="76af1-814">Das Paket [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) ist im Metapaket [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) enthalten.</span><span class="sxs-lookup"><span data-stu-id="76af1-814">The [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="76af1-815">ASP.NET Core-Projektvorlagen verwenden Kestrel standardmäßig.</span><span class="sxs-lookup"><span data-stu-id="76af1-815">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="76af1-816">Der Vorlagencode in *Program.cs* ruft <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> auf, wodurch <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> im Hintergrund aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="76af1-816">In *Program.cs* , the template code calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> behind the scenes.</span></span>

<span data-ttu-id="76af1-817">Um zusätzliche Konfiguration nach dem Aufruf von `CreateDefaultBuilder` bereitzustellen, rufen Sie <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> auf:</span><span class="sxs-lookup"><span data-stu-id="76af1-817">To provide additional configuration after calling `CreateDefaultBuilder`, call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            // Set properties and call methods on serverOptions
        });
```

<span data-ttu-id="76af1-818">Weitere Informationen zum `CreateDefaultBuilder` und zum Erstellen des Hosts finden Sie im Abschnitt *Einrichten eines Hosts* von <xref:fundamentals/host/web-host#set-up-a-host>.</span><span class="sxs-lookup"><span data-stu-id="76af1-818">For more information on `CreateDefaultBuilder` and building the host, see the *Set up a host* section of <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

## <a name="kestrel-options"></a><span data-ttu-id="76af1-819">Kestrel-Optionen</span><span class="sxs-lookup"><span data-stu-id="76af1-819">Kestrel options</span></span>

<span data-ttu-id="76af1-820">Der Kestrel-Webserver verfügt über einschränkende Konfigurationsoptionen, die besonders nützlich bei Bereitstellungen mit Internetzugriff sind.</span><span class="sxs-lookup"><span data-stu-id="76af1-820">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="76af1-821">Legen Sie Einschränkungen für die <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits>-Eigenschaft der <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>-Klasse fest.</span><span class="sxs-lookup"><span data-stu-id="76af1-821">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="76af1-822">Die `Limits`-Eigenschaft enthält eine Instanz der <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>-Klasse.</span><span class="sxs-lookup"><span data-stu-id="76af1-822">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="76af1-823">In den folgenden Beispielen wird der <xref:Microsoft.AspNetCore.Server.Kestrel.Core>-Namespace verwendet:</span><span class="sxs-lookup"><span data-stu-id="76af1-823">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="76af1-824">Kestrel-Optionen, die in den folgenden Beispielen in C#-Code konfiguriert sind, können auch mit einem [Konfigurationsanbieter](xref:fundamentals/configuration/index) festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="76af1-824">Kestrel options, which are configured in C# code in the following examples, can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="76af1-825">Beispielsweise kann der Dateikonfigurationsanbieter die Kestrel-Konfiguration aus einer *appsettings.json* - oder *appsettings.{Umgebung}.json* -Datei laden:</span><span class="sxs-lookup"><span data-stu-id="76af1-825">For example, the File Configuration Provider can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

```json
{
  "Kestrel": {
    "Limits": {
      "MaxConcurrentConnections": 100,
      "MaxConcurrentUpgradedConnections": 100
    }
  }
}
```

<span data-ttu-id="76af1-826">Verwenden Sie **einen** der folgenden Ansätze:</span><span class="sxs-lookup"><span data-stu-id="76af1-826">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="76af1-827">Konfigurieren Sie Kestrel in `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="76af1-827">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="76af1-828">Fügen Sie eine Instanz von `IConfiguration` in die `Startup`-Klasse ein.</span><span class="sxs-lookup"><span data-stu-id="76af1-828">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="76af1-829">Im folgenden Beispiel wird davon ausgegangen, dass die eingefügte Konfiguration der `Configuration`-Eigenschaft zugewiesen wird.</span><span class="sxs-lookup"><span data-stu-id="76af1-829">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="76af1-830">Laden Sie in `Startup.ConfigureServices` den Abschnitt `Kestrel` der Konfiguration in die Konfiguration von Kestrel:</span><span class="sxs-lookup"><span data-stu-id="76af1-830">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

* <span data-ttu-id="76af1-831">Konfigurieren Sie Kestrel beim Erstellen des Hosts:</span><span class="sxs-lookup"><span data-stu-id="76af1-831">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="76af1-832">Laden Sie in *Program.cs* den Abschnitt `Kestrel` der Konfiguration in die Konfiguration von Kestrel:</span><span class="sxs-lookup"><span data-stu-id="76af1-832">In *Program.cs* , load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

  ```csharp
  // using Microsoft.Extensions.DependencyInjection;

  public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
      WebHost.CreateDefaultBuilder(args)
          .ConfigureServices((context, services) =>
          {
              services.Configure<KestrelServerOptions>(
                  context.Configuration.GetSection("Kestrel"));
          })
          .UseStartup<Startup>();
  ```

<span data-ttu-id="76af1-833">Beide vorangehenden Ansätze funktionieren mit jedem [Konfigurationsanbieter](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="76af1-833">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="76af1-834">Keep-Alive-Timeout</span><span class="sxs-lookup"><span data-stu-id="76af1-834">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="76af1-835">Ruft das [Keep-Alive-Timeout](https://tools.ietf.org/html/rfc7230#section-6.5) ab oder legt es fest.</span><span class="sxs-lookup"><span data-stu-id="76af1-835">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="76af1-836">Standardwert: 2 Minuten.</span><span class="sxs-lookup"><span data-stu-id="76af1-836">Defaults to 2 minutes.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.KeepAliveTimeout = TimeSpan.FromMinutes(2);
        });
```

### <a name="maximum-client-connections"></a><span data-ttu-id="76af1-837">Maximale Anzahl der Clientverbindungen</span><span class="sxs-lookup"><span data-stu-id="76af1-837">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="76af1-838">Die maximale Anzahl von gleichzeitig geöffneten TCP-Verbindungen kann mithilfe von folgendem Code für die gesamte App festgelegt werden:</span><span class="sxs-lookup"><span data-stu-id="76af1-838">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxConcurrentConnections = 100;
        });
```

<span data-ttu-id="76af1-839">Es gibt einen separaten Grenzwert für Verbindungen, die von HTTP oder HTTPS auf ein anderes Protokoll aktualisiert wurden (z.B. auf eine WebSockets-Anforderung).</span><span class="sxs-lookup"><span data-stu-id="76af1-839">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="76af1-840">Nachdem eine Verbindung aktualisiert wurde, zählt diese nicht mehr für den `MaxConcurrentConnections`-Grenzwert.</span><span class="sxs-lookup"><span data-stu-id="76af1-840">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxConcurrentUpgradedConnections = 100;
        });
```

<span data-ttu-id="76af1-841">Die maximale Anzahl von Verbindungen ist standardmäßig nicht begrenzt (NULL).</span><span class="sxs-lookup"><span data-stu-id="76af1-841">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="76af1-842">Maximale Größe des Anforderungstexts</span><span class="sxs-lookup"><span data-stu-id="76af1-842">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="76af1-843">Die maximale Größe des Anforderungstexts beträgt standardmäßig 30.000.000 Byte, also ungefähr 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="76af1-843">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="76af1-844">Die empfohlene Methode zur Außerkraftsetzung des Grenzwerts in einer ASP.NET Core-MVC-App besteht im Verwenden des <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute>-Attributs in einer Aktionsmethode:</span><span class="sxs-lookup"><span data-stu-id="76af1-844">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="76af1-845">Im folgenden Beispiel wird veranschaulicht, wie die Einschränkungen auf jeder Anforderung für die App konfiguriert werden:</span><span class="sxs-lookup"><span data-stu-id="76af1-845">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxRequestBodySize = 10 * 1024;
        });
```

<span data-ttu-id="76af1-846">Außer Kraft setzen der Einstellung für eine bestimmte Anforderung in Middleware:</span><span class="sxs-lookup"><span data-stu-id="76af1-846">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="76af1-847">Eine Ausnahme wird ausgelöst, wenn die App den Grenzwert einer Anforderung konfiguriert, nachdem die App bereits mit dem Lesen der Anforderung begonnen hat.</span><span class="sxs-lookup"><span data-stu-id="76af1-847">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="76af1-848">Es gibt eine `IsReadOnly`-Eigenschaft, die angibt, wenn sich die `MaxRequestBodySize`-Eigenschaft im schreibgeschützten Zustand befindet, also wenn der Grenzwert nicht mehr konfiguriert werden kann.</span><span class="sxs-lookup"><span data-stu-id="76af1-848">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="76af1-849">Wenn eine App [prozessextern](xref:host-and-deploy/iis/index#out-of-process-hosting-model) hinter dem [ASP.NET Core-Modul](xref:host-and-deploy/aspnet-core-module) ausgeführt wird, ist das Größenlimit von Kestrel für Anforderungstext deaktiviert, weil IIS dieses Limit bereits festlegt.</span><span class="sxs-lookup"><span data-stu-id="76af1-849">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="76af1-850">Minimale Datenrate des Anforderungstexts</span><span class="sxs-lookup"><span data-stu-id="76af1-850">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="76af1-851">Kestrel überprüft sekündlich, ob Daten mit der angegebenen Rate in Bytes/Sekunde eingehen.</span><span class="sxs-lookup"><span data-stu-id="76af1-851">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="76af1-852">Wenn die Rate den mindestens erforderlichen Wert unterschreitet, wird für die Verbindung wegen Timeout getrennt. Bei der Toleranzperiode handelt es sich um die Zeitspanne, die Kestrel dem Client gewährt, um die Senderate auf den mindestens erforderlichen Wert zu erhöhen. Die Rate wird währenddessen nicht überprüft.</span><span class="sxs-lookup"><span data-stu-id="76af1-852">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="76af1-853">Diese Toleranzperiode beugt dem Trennen von Verbindungen vor, die Daten aufgrund eines langsamen TCP-Starts anfänglich mit einer niedrigen Rate senden.</span><span class="sxs-lookup"><span data-stu-id="76af1-853">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="76af1-854">Die mindestens erforderliche Rate beträgt standardmäßig 240 Bytes/Sekunde mit einer Toleranzperiode von 5 Sekunden.</span><span class="sxs-lookup"><span data-stu-id="76af1-854">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="76af1-855">Für die Antwort gilt ebenfalls eine mindestens erforderliche Rate.</span><span class="sxs-lookup"><span data-stu-id="76af1-855">A minimum rate also applies to the response.</span></span> <span data-ttu-id="76af1-856">Der Code zum Festlegen des Grenzwerts für Anforderung und Antwort ist abgesehen von `RequestBody` oder `Response` in den Namen von Eigenschaften und Schnittstelle identisch.</span><span class="sxs-lookup"><span data-stu-id="76af1-856">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="76af1-857">In diesem Beispiel wird veranschaulicht, wie Sie die mindestens erforderlichen Datenraten in *Program.cs* konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="76af1-857">Here's an example that shows how to configure the minimum data rates in *Program.cs* :</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MinRequestBodyDataRate =
                new MinDataRate(bytesPerSecond: 100, gracePeriod: TimeSpan.FromSeconds(10));
            serverOptions.Limits.MinResponseDataRate =
                new MinDataRate(bytesPerSecond: 100, gracePeriod: TimeSpan.FromSeconds(10));
        });
```

### <a name="request-headers-timeout"></a><span data-ttu-id="76af1-858">Timeout für Anforderungsheader</span><span class="sxs-lookup"><span data-stu-id="76af1-858">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="76af1-859">Ruft die maximale Zeitspanne ab, während der der Server Anforderungsheader empfängt, oder legt diese fest.</span><span class="sxs-lookup"><span data-stu-id="76af1-859">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="76af1-860">Der Standardwert beträgt 30 Sekunden.</span><span class="sxs-lookup"><span data-stu-id="76af1-860">Defaults to 30 seconds.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.RequestHeadersTimeout = TimeSpan.FromMinutes(1);
        });
```

### <a name="synchronous-io"></a><span data-ttu-id="76af1-861">Synchrone E/A-Vorgänge</span><span class="sxs-lookup"><span data-stu-id="76af1-861">Synchronous I/O</span></span>

<span data-ttu-id="76af1-862"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> steuert, ob für Anforderung und Antwort synchrone E/A-Vorgänge zulässig sind.</span><span class="sxs-lookup"><span data-stu-id="76af1-862"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous I/O is allowed for the request and response.</span></span> <span data-ttu-id="76af1-863">Der Standardwert ist `true`.</span><span class="sxs-lookup"><span data-stu-id="76af1-863">The  default value is `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="76af1-864">Sehr viele blockierende synchrone E/A-Vorgänge können zu einem Ressourcenmangel im Threadpool führen, wodurch die App nicht mehr reagiert.</span><span class="sxs-lookup"><span data-stu-id="76af1-864">A large number of blocking synchronous I/O operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="76af1-865">Aktivieren Sie `AllowSynchronousIO` nur bei Verwendung einer Bibliothek, die asynchrone E/A-Vorgänge nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="76af1-865">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous I/O.</span></span>

<span data-ttu-id="76af1-866">Das folgende Beispiel deaktiviert synchrone E/A-Vorgänge:</span><span class="sxs-lookup"><span data-stu-id="76af1-866">The following example disables synchronous I/O:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.AllowSynchronousIO = false;
        });
```

<span data-ttu-id="76af1-867">Weitere Informationen zu anderen Kestrel-Optionen und -Einschränkungen finden Sie unter:</span><span class="sxs-lookup"><span data-stu-id="76af1-867">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="76af1-868">Endpunktkonfiguration</span><span class="sxs-lookup"><span data-stu-id="76af1-868">Endpoint configuration</span></span>

<span data-ttu-id="76af1-869">Standardmäßig wird ASP.NET Core an Folgendes gebunden:</span><span class="sxs-lookup"><span data-stu-id="76af1-869">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="76af1-870">`https://localhost:5001` (wenn ein lokales Entwicklungszertifikat vorhanden ist)</span><span class="sxs-lookup"><span data-stu-id="76af1-870">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="76af1-871">Verwenden Sie Folgendes zum Angeben der URLs:</span><span class="sxs-lookup"><span data-stu-id="76af1-871">Specify URLs using the:</span></span>

* <span data-ttu-id="76af1-872">Die Umgebungsvariable `ASPNETCORE_URLS`</span><span class="sxs-lookup"><span data-stu-id="76af1-872">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="76af1-873">Das Befehlszeilenargument `--urls`</span><span class="sxs-lookup"><span data-stu-id="76af1-873">`--urls` command-line argument.</span></span>
* <span data-ttu-id="76af1-874">Den Hostkonfigurationsschlüssel `urls`</span><span class="sxs-lookup"><span data-stu-id="76af1-874">`urls` host configuration key.</span></span>
* <span data-ttu-id="76af1-875">Die Erweiterungsmethode `UseUrls`</span><span class="sxs-lookup"><span data-stu-id="76af1-875">`UseUrls` extension method.</span></span>

<span data-ttu-id="76af1-876">Der Wert, der mit diesen Ansätzen angegeben wird, kann mindestens ein HTTP- oder HTTPS-Endpunkt sein (HTTPS wenn ein Standardzertifikat verfügbar ist).</span><span class="sxs-lookup"><span data-stu-id="76af1-876">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="76af1-877">Konfigurieren Sie den Wert als eine durch Semikolons getrennte Liste (z.B. `"Urls": "http://localhost:8000;http://localhost:8001"`).</span><span class="sxs-lookup"><span data-stu-id="76af1-877">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="76af1-878">Weitere Informationen zu diesen Ansätzen finden Sie unter [Server-URLs](xref:fundamentals/host/web-host#server-urls) und [Außerkraftsetzen der Konfiguration](xref:fundamentals/host/web-host#override-configuration).</span><span class="sxs-lookup"><span data-stu-id="76af1-878">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="76af1-879">Ein Entwicklungszertifikat wird erstellt:</span><span class="sxs-lookup"><span data-stu-id="76af1-879">A development certificate is created:</span></span>

* <span data-ttu-id="76af1-880">Wenn das [.NET Core SDK](/dotnet/core/sdk) installiert wird.</span><span class="sxs-lookup"><span data-stu-id="76af1-880">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="76af1-881">Das [dev-cert-Tool](xref:aspnetcore-2.1#https) wird zum Erstellen eines Zertifikats verwendet.</span><span class="sxs-lookup"><span data-stu-id="76af1-881">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="76af1-882">Einige Browser erfordern, dass Sie die explizite Berechtigung erteilen, dem lokalen Entwicklungszertifikat zu vertrauen.</span><span class="sxs-lookup"><span data-stu-id="76af1-882">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="76af1-883">Projektvorlagen konfigurieren Apps, damit sie standardmäßig auf HTTPS ausgeführt werden und die [HTTPS-Umleitung und HSTS-Unterstützung](xref:security/enforcing-ssl) enthalten.</span><span class="sxs-lookup"><span data-stu-id="76af1-883">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="76af1-884">Rufen Sie die <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*>- oder <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*>-Methode unter <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> auf, um URL-Präfixe und Ports für Kestrel zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="76af1-884">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="76af1-885">`UseUrls`, das Befehlszeilenargument `--urls`, der Hostkonfigurationsschlüssel `urls` und die Umgebungsvariable `ASPNETCORE_URLS` funktionieren ebenfalls, verfügen jedoch über Einschränkungen, die im Verlauf dieses Abschnitts erläutert werden (Ein Standardzertifikat muss für die HTTPS-Endpunktkonfiguration verfügbar sein).</span><span class="sxs-lookup"><span data-stu-id="76af1-885">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="76af1-886">`KestrelServerOptions`-Konfiguration:</span><span class="sxs-lookup"><span data-stu-id="76af1-886">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="76af1-887">ConfigureEndpointDefaults(Action\<ListenOptions>)</span><span class="sxs-lookup"><span data-stu-id="76af1-887">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="76af1-888">Gibt die Konfiguration von `Action` zum Ausführen von jedem angegebenen Endpunkt an.</span><span class="sxs-lookup"><span data-stu-id="76af1-888">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="76af1-889">Mehrmalige Aufrufe von `ConfigureEndpointDefaults` ersetzen vorherige Instanzen von `Action` mit der zuletzt angegebenen `Action`.</span><span class="sxs-lookup"><span data-stu-id="76af1-889">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureEndpointDefaults(listenOptions =>
            {
                // Configure endpoint defaults
            });
        });
```

> [!NOTE]
> <span data-ttu-id="76af1-890">Auf Endpunkte, die durch Aufrufen von <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **vor** dem Aufrufen von <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> erstellt werden, werden die Standardwerte nicht angewendet.</span><span class="sxs-lookup"><span data-stu-id="76af1-890">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> won't have the defaults applied.</span></span>

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="76af1-891">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span><span class="sxs-lookup"><span data-stu-id="76af1-891">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="76af1-892">Gibt die Konfiguration von `Action` zum Ausführen von jedem HTTPS-Endpunkt an.</span><span class="sxs-lookup"><span data-stu-id="76af1-892">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="76af1-893">Mehrmalige Aufrufe von `ConfigureHttpsDefaults` ersetzen vorherige Instanzen von `Action` mit der zuletzt angegebenen `Action`.</span><span class="sxs-lookup"><span data-stu-id="76af1-893">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureHttpsDefaults(listenOptions =>
            {
                // certificate is an X509Certificate2
                listenOptions.ServerCertificate = certificate;
            });
        });
```

> [!NOTE]
> <span data-ttu-id="76af1-894">Auf Endpunkte, die durch Aufrufen von <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **vor** dem Aufrufen von <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> erstellt werden, werden die Standardwerte nicht angewendet.</span><span class="sxs-lookup"><span data-stu-id="76af1-894">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>

### <a name="configureiconfiguration"></a><span data-ttu-id="76af1-895">Configure(IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="76af1-895">Configure(IConfiguration)</span></span>

<span data-ttu-id="76af1-896">Erstellt ein Konfigurationsladeprogramm für das Einrichten von Kestrel, was <xref:Microsoft.Extensions.Configuration.IConfiguration> als Eingabe erfordert.</span><span class="sxs-lookup"><span data-stu-id="76af1-896">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="76af1-897">Die Konfiguration muss auf den Konfigurationsabschnitt für Kestrel festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="76af1-897">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="76af1-898">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="76af1-898">ListenOptions.UseHttps</span></span>

<span data-ttu-id="76af1-899">Konfiguriert Kestrel zur Verwendung von HTTPS.</span><span class="sxs-lookup"><span data-stu-id="76af1-899">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="76af1-900">`ListenOptions.UseHttps`-Erweiterungen:</span><span class="sxs-lookup"><span data-stu-id="76af1-900">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="76af1-901">`UseHttps`: Hiermit wird Kestrel zur Verwendung von HTTPS mit dem Standardzertifikat konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="76af1-901">`UseHttps`: Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="76af1-902">Löst eine Ausnahme aus, wenn kein Standardzertifikat konfiguriert ist.</span><span class="sxs-lookup"><span data-stu-id="76af1-902">Throws an exception if no default certificate is configured.</span></span>
* `UseHttps(string fileName)`
* `UseHttps(string fileName, string password)`
* `UseHttps(string fileName, string password, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(StoreName storeName, string subject)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(X509Certificate2 serverCertificate)`
* `UseHttps(X509Certificate2 serverCertificate, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(Action<HttpsConnectionAdapterOptions> configureOptions)`

<span data-ttu-id="76af1-903">`ListenOptions.UseHttps`-Parameter:</span><span class="sxs-lookup"><span data-stu-id="76af1-903">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="76af1-904">`filename` entspricht dem Pfad und Dateinamen einer Zertifikatdatei relativ zu dem Verzeichnis, das die Inhaltsdateien der App enthält.</span><span class="sxs-lookup"><span data-stu-id="76af1-904">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="76af1-905">`password` ist das für den Zugriff auf die X.509-Zertifikatsdaten erforderliche Kennwort.</span><span class="sxs-lookup"><span data-stu-id="76af1-905">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="76af1-906">`configureOptions` ist eine `Action` zum Konfigurieren von `HttpsConnectionAdapterOptions`.</span><span class="sxs-lookup"><span data-stu-id="76af1-906">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="76af1-907">Gibt `ListenOptions` zurück.</span><span class="sxs-lookup"><span data-stu-id="76af1-907">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="76af1-908">`storeName` ist der Zertifikatspeicher, aus dem das Zertifikat geladen wird.</span><span class="sxs-lookup"><span data-stu-id="76af1-908">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="76af1-909">`subject` ist der Name des Antragstellers für das Zertifikat.</span><span class="sxs-lookup"><span data-stu-id="76af1-909">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="76af1-910">`allowInvalid` gibt an, ob ungültige Zertifikate berücksichtigt werden sollten, z.B. selbstsignierte Zertifikate.</span><span class="sxs-lookup"><span data-stu-id="76af1-910">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="76af1-911">`location` ist der Speicherort, aus dem das Zertifikat geladen wird.</span><span class="sxs-lookup"><span data-stu-id="76af1-911">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="76af1-912">`serverCertificate` ist das X.509-Zertifikat.</span><span class="sxs-lookup"><span data-stu-id="76af1-912">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="76af1-913">In der Produktion muss HTTPS explizit konfiguriert sein.</span><span class="sxs-lookup"><span data-stu-id="76af1-913">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="76af1-914">Zumindest muss ein Standardzertifikat angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="76af1-914">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="76af1-915">Die im Folgenden beschriebenen unterstützten Konfigurationen:</span><span class="sxs-lookup"><span data-stu-id="76af1-915">Supported configurations described next:</span></span>

* <span data-ttu-id="76af1-916">Keine Konfiguration</span><span class="sxs-lookup"><span data-stu-id="76af1-916">No configuration</span></span>
* <span data-ttu-id="76af1-917">Ersetzen des Standardzertifikats aus der Konfiguration</span><span class="sxs-lookup"><span data-stu-id="76af1-917">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="76af1-918">Ändern des Standards im Code</span><span class="sxs-lookup"><span data-stu-id="76af1-918">Change the defaults in code</span></span>

<span data-ttu-id="76af1-919">*Keine Konfiguration*</span><span class="sxs-lookup"><span data-stu-id="76af1-919">*No configuration*</span></span>

<span data-ttu-id="76af1-920">Kestrel überwacht `http://localhost:5000` und `https://localhost:5001` (wenn ein Standardzertifikat verfügbar ist).</span><span class="sxs-lookup"><span data-stu-id="76af1-920">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="76af1-921">*Ersetzen des Standardzertifikats aus der Konfiguration*</span><span class="sxs-lookup"><span data-stu-id="76af1-921">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="76af1-922">`CreateDefaultBuilder` ruft `Configure(context.Configuration.GetSection("Kestrel"))` standardmäßig zum Laden der Kestrel-Konfiguration auf.</span><span class="sxs-lookup"><span data-stu-id="76af1-922">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="76af1-923">Ein Standardkonfigurationsschema für HTTPS-App-Einstellungen ist für Kestrel verfügbar.</span><span class="sxs-lookup"><span data-stu-id="76af1-923">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="76af1-924">Konfigurieren Sie mehrere Endpunkte, einschließlich der zu verwendenden URLs und Zertifikate aus einer Datei auf dem Datenträger oder einem Zertifikatspeicher.</span><span class="sxs-lookup"><span data-stu-id="76af1-924">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="76af1-925">Im folgenden Beispiel für *appsettings.json* gilt:</span><span class="sxs-lookup"><span data-stu-id="76af1-925">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="76af1-926">Legen Sie **AllowInvalid** auf `true` fest, um die Verwendung von ungültigen Zertifikaten zu erlauben (z.B. selbstsignierte Zertifikate).</span><span class="sxs-lookup"><span data-stu-id="76af1-926">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="76af1-927">Jeder HTTPS-Endpunkt, der kein Zertifikat angibt (im folgenden Beispiel **HttpsDefaultCert** ), greift auf das unter **Zertifikate** > **Standard** festgelegte Zertifikat oder das Entwicklungszertifikat zurück.</span><span class="sxs-lookup"><span data-stu-id="76af1-927">Any HTTPS endpoint that doesn't specify a certificate ( **HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "Http": {
        "Url": "http://localhost:5000"
      },

      "HttpsInlineCertFile": {
        "Url": "https://localhost:5001",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      },

      "HttpsInlineCertStore": {
        "Url": "https://localhost:5002",
        "Certificate": {
          "Subject": "<subject; required>",
          "Store": "<certificate store; required>",
          "Location": "<location; defaults to CurrentUser>",
          "AllowInvalid": "<true or false; defaults to false>"
        }
      },

      "HttpsDefaultCert": {
        "Url": "https://localhost:5003"
      },

      "Https": {
        "Url": "https://*:5004",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      }
    },
    "Certificates": {
      "Default": {
        "Path": "<path to .pfx file>",
        "Password": "<certificate password>"
      }
    }
  }
}
```

<span data-ttu-id="76af1-928">Alternativ zur Verwendung von **Pfad** und **Kennwort** für alle Zertifikatknoten können Sie das Zertifikat mithilfe von Zertifikatspeicherfeldern angeben.</span><span class="sxs-lookup"><span data-stu-id="76af1-928">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="76af1-929">Das Zertifikat unter **Zertifikate** > **Standard** kann beispielweise wie folgt angegeben werden:</span><span class="sxs-lookup"><span data-stu-id="76af1-929">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="76af1-930">Schema-Hinweise:</span><span class="sxs-lookup"><span data-stu-id="76af1-930">Schema notes:</span></span>

* <span data-ttu-id="76af1-931">Bei den Namen der Endpunkte wird die Groß-/Kleinschreibung nicht beachtet.</span><span class="sxs-lookup"><span data-stu-id="76af1-931">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="76af1-932">Zum Beispiel sind `HTTPS` und `Https` gültig.</span><span class="sxs-lookup"><span data-stu-id="76af1-932">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="76af1-933">Der Parameter `Url` ist für jeden Endpunkt erforderlich.</span><span class="sxs-lookup"><span data-stu-id="76af1-933">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="76af1-934">Das Format für diesen Parameter ist identisch mit dem allgemeinen Konfigurationsparameter `Urls`, mit der Ausnahme, dass er auf einen einzelnen Wert begrenzt ist.</span><span class="sxs-lookup"><span data-stu-id="76af1-934">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="76af1-935">Diese Endpunkte ersetzen die in der allgemeinen `Urls`-Konfiguration festgelegten Endpunkte, anstatt zu ihnen hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="76af1-935">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="76af1-936">Endpunkte, die über `Listen` in Code definiert werden, werden den im Konfigurationsabschnitt definierten Endpunkten hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="76af1-936">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="76af1-937">Der `Certificate`-Abschnitt ist optional.</span><span class="sxs-lookup"><span data-stu-id="76af1-937">The `Certificate` section is optional.</span></span> <span data-ttu-id="76af1-938">Wenn der `Certificate`-Abschnitt nicht angegeben ist, werden die in früheren Szenarios definierten Standardwerte verwendet.</span><span class="sxs-lookup"><span data-stu-id="76af1-938">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="76af1-939">Wenn keine Standardwerte verfügbar sind, löst der Server eine Ausnahme aus und startet nicht.</span><span class="sxs-lookup"><span data-stu-id="76af1-939">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="76af1-940">Der `Certificate`-Abschnitt unterstützt die Zertifikate **Pfad**&ndash;**Kennwort** und **Subject**&ndash;**Store** (Antragsteller–Speicher).</span><span class="sxs-lookup"><span data-stu-id="76af1-940">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="76af1-941">Auf diese Weise kann eine beliebige Anzahl von Endpunkten definiert werden, solange Sie keine Portkonflikte verursachen.</span><span class="sxs-lookup"><span data-stu-id="76af1-941">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="76af1-942">`options.Configure(context.Configuration.GetSection("{SECTION}"))` gibt `KestrelConfigurationLoader` mit der Methode `.Endpoint(string name, listenOptions => { })` zurück, die dazu verwendet werden kann, die Einstellungen eines Endpunkts zu ergänzen:</span><span class="sxs-lookup"><span data-stu-id="76af1-942">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.Configure(context.Configuration.GetSection("Kestrel"))
                .Endpoint("HTTPS", listenOptions =>
                {
                    listenOptions.HttpsOptions.SslProtocols = SslProtocols.Tls12;
                });
        });
```

<span data-ttu-id="76af1-943">Auf `KestrelServerOptions.ConfigurationLoader` kann direkt zugegriffen werden, um die Iteration auf dem vorhandenen Ladeprogramm fortzusetzen, etwa auf dem von <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> bereitgestellten Ladeprogramm.</span><span class="sxs-lookup"><span data-stu-id="76af1-943">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="76af1-944">Der Konfigurationsabschnitt ist für jeden Endpunkt in den Optionen der Methode `Endpoint` verfügbar, sodass benutzerdefinierte Einstellungen gelesen werden können.</span><span class="sxs-lookup"><span data-stu-id="76af1-944">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="76af1-945">Mehrere Konfigurationen können durch erneutes Aufrufen von `options.Configure(context.Configuration.GetSection("{SECTION}"))` mit einem anderen Abschnitt geladen werden.</span><span class="sxs-lookup"><span data-stu-id="76af1-945">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="76af1-946">Sofern `Load` nicht in einer vorherigen Instanz explizit aufgerufen wird, wird nur die letzte Konfiguration verwendet.</span><span class="sxs-lookup"><span data-stu-id="76af1-946">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="76af1-947">Das Metapaket ruft `Load` nicht auf, sodass der Abschnitt mit der Standardkonfiguration ersetzt werden kann.</span><span class="sxs-lookup"><span data-stu-id="76af1-947">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="76af1-948">`KestrelConfigurationLoader` spiegelt die API-Familie `Listen` von `KestrelServerOptions` als `Endpoint`-Überladungen, weshalb Code und Konfigurationsendpunkte am selben Ort konfiguriert werden können.</span><span class="sxs-lookup"><span data-stu-id="76af1-948">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="76af1-949">Diese Überladungen verwenden keine Namen und nutzen nur Standardeinstellungen aus der Konfiguration.</span><span class="sxs-lookup"><span data-stu-id="76af1-949">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="76af1-950">*Ändern des Standards im Code*</span><span class="sxs-lookup"><span data-stu-id="76af1-950">*Change the defaults in code*</span></span>

<span data-ttu-id="76af1-951">`ConfigureEndpointDefaults` und `ConfigureHttpsDefaults` können zum Ändern der Standardeinstellungen für `ListenOptions` und `HttpsConnectionAdapterOptions` verwendet werden, einschließlich der Standardzertifikate, die im vorherigen Szenario festgelegt wurden.</span><span class="sxs-lookup"><span data-stu-id="76af1-951">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="76af1-952">`ConfigureEndpointDefaults` und `ConfigureHttpsDefaults` sollten aufgerufen werden, bevor Endpunkte konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="76af1-952">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureEndpointDefaults(listenOptions =>
            {
                // Configure endpoint defaults
            });
            
            serverOptions.ConfigureHttpsDefaults(listenOptions =>
            {
                listenOptions.SslProtocols = SslProtocols.Tls12;
            });
        });
```

<span data-ttu-id="76af1-953">*Kestrel-Unterstützung für SNI*</span><span class="sxs-lookup"><span data-stu-id="76af1-953">*Kestrel support for SNI*</span></span>

<span data-ttu-id="76af1-954">Die [Servernamensanzeige (SNI)](https://tools.ietf.org/html/rfc6066#section-3) kann zum Hosten mehrerer Domänen auf der gleichen IP-Adresse und dem gleichen Port verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="76af1-954">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="76af1-955">Damit die Servernamensanzeige funktioniert, sendet der Client während des TLS-Handshakes den Hostnamen für die sichere Sitzung an den Server, sodass der Server das richtige Zertifikat bereitstellen kann.</span><span class="sxs-lookup"><span data-stu-id="76af1-955">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="76af1-956">Der Client verwendet das beigestellte Zertifikat für die verschlüsselte Kommunikation mit dem Server während der sicheren Sitzung nach dem TLS-Handshake.</span><span class="sxs-lookup"><span data-stu-id="76af1-956">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="76af1-957">Kestrel unterstützt die Servernamensanzeige über den `ServerCertificateSelector`-Rückruf.</span><span class="sxs-lookup"><span data-stu-id="76af1-957">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="76af1-958">Der Rückruf wird für jede Verbindung einmal aufgerufen, um der App zu ermöglichen, den Hostnamen zu überprüfen und das entsprechende Zertifikat auszuwählen.</span><span class="sxs-lookup"><span data-stu-id="76af1-958">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="76af1-959">Für die Unterstützung der Servernamensanzeige benötigen Sie Folgendes:</span><span class="sxs-lookup"><span data-stu-id="76af1-959">SNI support requires:</span></span>

* <span data-ttu-id="76af1-960">Wird auf dem Zielframework `netcoreapp2.1` oder höher ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="76af1-960">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="76af1-961">In `net461` oder höher, wird der Rückruf aufgerufen, `name` ist aber immer `null`.</span><span class="sxs-lookup"><span data-stu-id="76af1-961">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="76af1-962">`name` ist auch `null`, wenn der Client den Hostnamenparameter nicht im TLS-Handshake angibt.</span><span class="sxs-lookup"><span data-stu-id="76af1-962">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="76af1-963">Alle Websites werden in derselben Kestrel-Instanz ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="76af1-963">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="76af1-964">Kestrel unterstützt ohne Reverseproxy keine gemeinsame IP-Adresse und keinen gemeinsamen Port für mehrere Instanzen.</span><span class="sxs-lookup"><span data-stu-id="76af1-964">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.ListenAnyIP(5005, listenOptions =>
            {
                listenOptions.UseHttps(httpsOptions =>
                {
                    var localhostCert = CertificateLoader.LoadFromStoreCert(
                        "localhost", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var exampleCert = CertificateLoader.LoadFromStoreCert(
                        "example.com", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var subExampleCert = CertificateLoader.LoadFromStoreCert(
                        "sub.example.com", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var certs = new Dictionary<string, X509Certificate2>(
                        StringComparer.OrdinalIgnoreCase);
                    certs["localhost"] = localhostCert;
                    certs["example.com"] = exampleCert;
                    certs["sub.example.com"] = subExampleCert;

                    httpsOptions.ServerCertificateSelector = (connectionContext, name) =>
                    {
                        if (name != null && certs.TryGetValue(name, out var cert))
                        {
                            return cert;
                        }

                        return exampleCert;
                    };
                });
            });
        })
        .Build();
```

### <a name="connection-logging"></a><span data-ttu-id="76af1-965">Verbindungsprotokollierung</span><span class="sxs-lookup"><span data-stu-id="76af1-965">Connection logging</span></span>

<span data-ttu-id="76af1-966">Rufen Sie <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> auf, um Protokolle auf Debugebene für die Kommunikation auf Byteebene für eine Verbindung auszugeben.</span><span class="sxs-lookup"><span data-stu-id="76af1-966">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="76af1-967">Die Verbindungsprotokollierung ist beim Beheben von Problemen bei der Low-Level-Kommunikation hilfreich, wie z. B. bei der TLS-Verschlüsselung und bei Proxys.</span><span class="sxs-lookup"><span data-stu-id="76af1-967">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="76af1-968">Wenn `UseConnectionLogging` vor `UseHttps` platziert wird, wird der verschlüsselte Datenverkehr protokolliert.</span><span class="sxs-lookup"><span data-stu-id="76af1-968">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="76af1-969">Wenn `UseConnectionLogging` nach `UseHttps` platziert wird, wird der entschlüsselte Datenverkehr protokolliert.</span><span class="sxs-lookup"><span data-stu-id="76af1-969">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="76af1-970">Binden an einen TCP-Socket</span><span class="sxs-lookup"><span data-stu-id="76af1-970">Bind to a TCP socket</span></span>

<span data-ttu-id="76af1-971">Die <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*>-Methode wird an ein TCP-Socket gebunden, und ein Lambdaausdruck einer Option lässt die Konfiguration des X.509-Zertifikats zu:</span><span class="sxs-lookup"><span data-stu-id="76af1-971">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

```csharp
public static void Main(string[] args)
{
    CreateWebHostBuilder(args).Build().Run();
}

public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Listen(IPAddress.Loopback, 5000);
            serverOptions.Listen(IPAddress.Loopback, 5001, listenOptions =>
            {
                listenOptions.UseHttps("testCert.pfx", "testPassword");
            });
        });
```

```csharp
public static void Main(string[] args)
{
    CreateWebHostBuilder(args).Build().Run();
}

public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Listen(IPAddress.Loopback, 5000);
            serverOptions.Listen(IPAddress.Loopback, 5001, listenOptions =>
            {
                listenOptions.UseHttps("testCert.pfx", "testPassword");
            });
        });
```

<span data-ttu-id="76af1-972">Im Beispiel wird HTTPS für einen Endpunkt mit <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions> konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="76af1-972">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="76af1-973">Verwenden Sie die gleiche API zum Konfigurieren anderer Kestrel-Einstellungen für bestimmte Endpunkte.</span><span class="sxs-lookup"><span data-stu-id="76af1-973">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="76af1-974">Binden an einen Unix-Socket</span><span class="sxs-lookup"><span data-stu-id="76af1-974">Bind to a Unix socket</span></span>

<span data-ttu-id="76af1-975">Wie in diesem Beispiel dargestellt, lauschen Sie an einem Unix-Socket mit <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*>, um eine verbesserte Leistung mit Nginx zu erzielen:</span><span class="sxs-lookup"><span data-stu-id="76af1-975">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.ListenUnixSocket("/tmp/kestrel-test.sock");
            serverOptions.ListenUnixSocket("/tmp/kestrel-test.sock", listenOptions =>
            {
                listenOptions.UseHttps("testCert.pfx", "testpassword");
            });
        });
```

* <span data-ttu-id="76af1-976">Legen Sie in der Nginx-Konfigurationsdatei den Eintrag `server` > `location` > `proxy_pass` auf `http://unix:/tmp/{KESTREL SOCKET}:/;` fest.</span><span class="sxs-lookup"><span data-stu-id="76af1-976">In the Nginx confiuguration file, set the `server` > `location` > `proxy_pass` entry to `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span></span> <span data-ttu-id="76af1-977">`{KESTREL SOCKET}` ist der Name des für <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> bereitgestellten Socket (zum Beispiel `kestrel-test.sock` im vorherigen Beispiel).</span><span class="sxs-lookup"><span data-stu-id="76af1-977">`{KESTREL SOCKET}` is the name of the socket provided to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (for example, `kestrel-test.sock` in the preceding example).</span></span>
* <span data-ttu-id="76af1-978">Stellen Sie sicher, dass der Socket von Nginx beschreibbar ist (z. B. `chmod go+w /tmp/kestrel-test.sock`).</span><span class="sxs-lookup"><span data-stu-id="76af1-978">Ensure that the socket is writeable by Nginx (for example, `chmod go+w /tmp/kestrel-test.sock`).</span></span> 

### <a name="port-0"></a><span data-ttu-id="76af1-979">Port 0</span><span class="sxs-lookup"><span data-stu-id="76af1-979">Port 0</span></span>

<span data-ttu-id="76af1-980">Wenn die Portnummer `0` angegeben wird, wird Kestrel dynamisch an einen verfügbaren Port gebunden.</span><span class="sxs-lookup"><span data-stu-id="76af1-980">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="76af1-981">Im folgenden Beispiel wird veranschaulicht, wie bestimmt werden kann, für welchen Port Kestrel zur Laufzeit eine Bindung erstellt hat:</span><span class="sxs-lookup"><span data-stu-id="76af1-981">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="76af1-982">Wenn die App ausgeführt wird, gibt das Ausgabefenster der Konsole den dynamischen Port an, über den die App erreicht werden kann:</span><span class="sxs-lookup"><span data-stu-id="76af1-982">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="76af1-983">Einschränkungen</span><span class="sxs-lookup"><span data-stu-id="76af1-983">Limitations</span></span>

<span data-ttu-id="76af1-984">Konfigurieren Sie Endpunkte mithilfe der folgenden Ansätze:</span><span class="sxs-lookup"><span data-stu-id="76af1-984">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="76af1-985">Befehlszeilenargument `--urls`</span><span class="sxs-lookup"><span data-stu-id="76af1-985">`--urls` command-line argument</span></span>
* <span data-ttu-id="76af1-986">Hostkonfigurationsschlüssel `urls`</span><span class="sxs-lookup"><span data-stu-id="76af1-986">`urls` host configuration key</span></span>
* <span data-ttu-id="76af1-987">Umgebungsvariable `ASPNETCORE_URLS`</span><span class="sxs-lookup"><span data-stu-id="76af1-987">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="76af1-988">Diese Methoden sind nützlich, wenn Ihr Code mit anderen Servern als Kestrel funktionieren soll.</span><span class="sxs-lookup"><span data-stu-id="76af1-988">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="76af1-989">Beachten Sie jedoch die folgenden Einschränkungen:</span><span class="sxs-lookup"><span data-stu-id="76af1-989">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="76af1-990">HTTPS kann nicht mit diesen Ansätzen verwendet werden, außer ein Standardzertifikat wird in der HTTPS-Endpunktkonfiguration angegeben (z.B. wenn Sie wie zuvor in diesem Artikel gezeigt die `KestrelServerOptions`-Konfiguration oder eine Konfigurationsdatei verwenden).</span><span class="sxs-lookup"><span data-stu-id="76af1-990">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="76af1-991">Wenn die Ansätze `Listen` und `UseUrls` gleichzeitig verwendet werden, überschreiben die `Listen`-Endpunkte die `UseUrls`-Endpunkte.</span><span class="sxs-lookup"><span data-stu-id="76af1-991">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="76af1-992">IIS-Endpunktkonfiguration</span><span class="sxs-lookup"><span data-stu-id="76af1-992">IIS endpoint configuration</span></span>

<span data-ttu-id="76af1-993">Bei der Verwendung von IIS werden die URL-Bindungen für IIS-Überschreibungsbindungen durch `Listen` oder `UseUrls` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="76af1-993">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="76af1-994">Weitere Informationen finden Sie im Artikel [ASP.NET Core-Modul](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="76af1-994">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

## <a name="transport-configuration"></a><span data-ttu-id="76af1-995">Transportkonfiguration</span><span class="sxs-lookup"><span data-stu-id="76af1-995">Transport configuration</span></span>

<span data-ttu-id="76af1-996">Mit dem Release von ASP.NET Core 2.1 basiert der Standardtransport von Kestrel nicht mehr auf Libuv, sondern auf verwalteten Sockets.</span><span class="sxs-lookup"><span data-stu-id="76af1-996">With the release of ASP.NET Core 2.1, Kestrel's default transport is no longer based on Libuv but instead based on managed sockets.</span></span> <span data-ttu-id="76af1-997">Dies stellt einen Breaking Change für ASP.NET Core 2.0-Apps dar, die auf Version 2.1 upgraden, <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> aufrufen und von einem der folgenden Pakete abhängig sind:</span><span class="sxs-lookup"><span data-stu-id="76af1-997">This is a breaking change for ASP.NET Core 2.0 apps upgrading to 2.1 that call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> and depend on either of the following packages:</span></span>

* <span data-ttu-id="76af1-998">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (direkter Paketverweis)</span><span class="sxs-lookup"><span data-stu-id="76af1-998">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (direct package reference)</span></span>
* [<span data-ttu-id="76af1-999">Microsoft.AspNetCore.App</span><span class="sxs-lookup"><span data-stu-id="76af1-999">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)

<span data-ttu-id="76af1-1000">Für Projekte, die den Einsatz von Libuv erfordern:</span><span class="sxs-lookup"><span data-stu-id="76af1-1000">For projects that require the use of Libuv:</span></span>

* <span data-ttu-id="76af1-1001">Fügen Sie für das [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/)-Paket eine Abhängigkeit auf die Projektdatei der App hinzu:</span><span class="sxs-lookup"><span data-stu-id="76af1-1001">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* <span data-ttu-id="76af1-1002">Rufen Sie <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> auf:</span><span class="sxs-lookup"><span data-stu-id="76af1-1002">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span></span>

  ```csharp
  public class Program
  {
      public static void Main(string[] args)
      {
          CreateWebHostBuilder(args).Build().Run();
      }

      public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
          WebHost.CreateDefaultBuilder(args)
              .UseLibuv()
              .UseStartup<Startup>();
  }
  ```

### <a name="url-prefixes"></a><span data-ttu-id="76af1-1003">URL-Präfixe</span><span class="sxs-lookup"><span data-stu-id="76af1-1003">URL prefixes</span></span>

<span data-ttu-id="76af1-1004">Bei der Verwendung von `UseUrls`, dem Befehlszeilenargument `--urls`, dem Hostkonfigurationsschlüssel `urls` oder der Umgebungsvariable `ASPNETCORE_URLS` können die URL-Präfixe in den folgenden Formaten vorliegen.</span><span class="sxs-lookup"><span data-stu-id="76af1-1004">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="76af1-1005">Nur HTTP-URL-Präfixe sind gültig.</span><span class="sxs-lookup"><span data-stu-id="76af1-1005">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="76af1-1006">Kestrel unterstützt HTTPS nicht beim Konfigurieren von URL-Bindungen mit `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="76af1-1006">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="76af1-1007">IPv4-Adresse mit Portnummer</span><span class="sxs-lookup"><span data-stu-id="76af1-1007">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="76af1-1008">Bei `0.0.0.0` handelt es sich um einen Sonderfall, für den eine Bindung an alle IPv4-Adressen erfolgt.</span><span class="sxs-lookup"><span data-stu-id="76af1-1008">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="76af1-1009">IPv6-Adresse mit Portnummer</span><span class="sxs-lookup"><span data-stu-id="76af1-1009">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="76af1-1010">`[::]` stellt das Äquivalent von IPv6 zu `0.0.0.0` für IPv4 dar.</span><span class="sxs-lookup"><span data-stu-id="76af1-1010">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="76af1-1011">Hostname mit Portnummer</span><span class="sxs-lookup"><span data-stu-id="76af1-1011">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="76af1-1012">Hostnamen, `*` und `+` sind nicht spezifisch.</span><span class="sxs-lookup"><span data-stu-id="76af1-1012">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="76af1-1013">Alle Elemente, die nicht als gültige IP-Adresse oder `localhost` erkannt werden, werden an alle IPv4- und IPv6-IP-Adressen gebunden.</span><span class="sxs-lookup"><span data-stu-id="76af1-1013">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="76af1-1014">Verwenden Sie [HTTP.sys](xref:fundamentals/servers/httpsys) oder einen Reverseproxyserver zum Binden verschiedener Hostnamen an verschiedene ASP.NET Core-Apps auf demselben Port, z.B. IIS, Nginx oder Apache.</span><span class="sxs-lookup"><span data-stu-id="76af1-1014">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="76af1-1015">Das Hosting in einer Reverseproxykonfiguration erfordert [Hostfilterung](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="76af1-1015">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="76af1-1016">`localhost`-Hostname mit Portnummer oder Loopback-IP mit Portnummer</span><span class="sxs-lookup"><span data-stu-id="76af1-1016">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="76af1-1017">Wenn `localhost` angegeben ist, versucht Kestrel, eine Bindung zu IPv4- und IPv6-Loopback-Schnittstellen zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="76af1-1017">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="76af1-1018">Wenn der erforderliche Port von einem anderen Dienst auf einer der Loopback-Schnittstellen verwendet wird, tritt beim Starten von Kestrel ein Fehler auf.</span><span class="sxs-lookup"><span data-stu-id="76af1-1018">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="76af1-1019">Wenn eine der Loopback-Schnittstellen aus anderen Gründen nicht verfügbar ist (meistens durch die fehlende Unterstützung von IPv6), protokolliert Kestrel eine Warnung.</span><span class="sxs-lookup"><span data-stu-id="76af1-1019">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="76af1-1020">Host-Filterung</span><span class="sxs-lookup"><span data-stu-id="76af1-1020">Host filtering</span></span>

<span data-ttu-id="76af1-1021">Obwohl Kestrel die Konfiguration basierend auf Präfixe wie `http://example.com:5000` unterstützt, ignoriert Kestrel den Hostnamen weitgehend.</span><span class="sxs-lookup"><span data-stu-id="76af1-1021">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="76af1-1022">Der Host `localhost` ist ein Sonderfall, der für die Bindung an Loopback-Adressen verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="76af1-1022">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="76af1-1023">Jeder Host, der keine explizite IP-Adresse ist, wird an alle öffentlichen IP-Adressen gebunden.</span><span class="sxs-lookup"><span data-stu-id="76af1-1023">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="76af1-1024">`Host`-Header werden nicht überprüft.</span><span class="sxs-lookup"><span data-stu-id="76af1-1024">`Host` headers aren't validated.</span></span>

<span data-ttu-id="76af1-1025">Verwenden Sie Middleware zum Filtern von Hosts, um dieses Problem zu umgehen.</span><span class="sxs-lookup"><span data-stu-id="76af1-1025">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="76af1-1026">Middleware zum Filtern von Hosts wird im Paket [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) bereitgestellt, das im [Metapaket Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) enthalten ist (ASP.NET Core 2.1 oder 2.2).</span><span class="sxs-lookup"><span data-stu-id="76af1-1026">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or 2.2).</span></span> <span data-ttu-id="76af1-1027">Die Middleware wird von <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> hinzugefügt, wodurch <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*> aufgerufen wird:</span><span class="sxs-lookup"><span data-stu-id="76af1-1027">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="76af1-1028">Die Middleware zum Filtern von Hosts ist standardmäßig deaktiviert.</span><span class="sxs-lookup"><span data-stu-id="76af1-1028">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="76af1-1029">Wenn Sie die Middleware aktivieren möchten, definieren Sie einen `AllowedHosts`-Schlüssel in *appsettings.json* /*appsettings.\<EnvironmentName>json*.</span><span class="sxs-lookup"><span data-stu-id="76af1-1029">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="76af1-1030">Der Wert ist eine durch Semikolons getrennte Liste von Hostnamen ohne Portnummern:</span><span class="sxs-lookup"><span data-stu-id="76af1-1030">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="76af1-1031">*appsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="76af1-1031">*appsettings.json* :</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="76af1-1032">[Middleware für weitergeleitete Header](xref:host-and-deploy/proxy-load-balancer) hat auch eine <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts>-Option.</span><span class="sxs-lookup"><span data-stu-id="76af1-1032">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="76af1-1033">Middleware für weitergeleitete Header und Middleware zum Filtern von Hosts besitzen ähnliche Funktionen für unterschiedliche Szenarios.</span><span class="sxs-lookup"><span data-stu-id="76af1-1033">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="76af1-1034">Legen Sie `AllowedHosts` mit Middleware für weitergeleitete Header fest, wenn der `Host`-Header beim Weiterleiten von Anforderungen mit einem Reverseproxyserver oder einem Lastenausgleichsmodul nicht beibehalten wird.</span><span class="sxs-lookup"><span data-stu-id="76af1-1034">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="76af1-1035">Legen Sie `AllowedHosts` mit Middleware zum Filtern von Hosts fest, wenn Kestrel als öffentlicher Edgeserver verwendet oder der `Host`-Header direkt weitergeleitet wird.</span><span class="sxs-lookup"><span data-stu-id="76af1-1035">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="76af1-1036">Weitere Informationen zu Middleware für weitergeleitete Header finden Sie unter <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="76af1-1036">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

## <a name="http11-request-draining"></a><span data-ttu-id="76af1-1037">Leeren von HTTP/1.1-Anforderungen</span><span class="sxs-lookup"><span data-stu-id="76af1-1037">HTTP/1.1 request draining</span></span>

<span data-ttu-id="76af1-1038">Das Öffnen von HTTP-Verbindungen ist zeitaufwendig.</span><span class="sxs-lookup"><span data-stu-id="76af1-1038">Opening HTTP connections is time consuming.</span></span> <span data-ttu-id="76af1-1039">Bei HTTPS ist es zudem ressourcenintensiv.</span><span class="sxs-lookup"><span data-stu-id="76af1-1039">For HTTPS, it's also resource intensive.</span></span> <span data-ttu-id="76af1-1040">Daher versucht Kestrel, Verbindungen gemäß dem HTTP/1.1-Protokoll wiederzuverwenden.</span><span class="sxs-lookup"><span data-stu-id="76af1-1040">Therefore, Kestrel tries to reuse connections per the HTTP/1.1 protocol.</span></span> <span data-ttu-id="76af1-1041">Ein Anforderungstext muss vollständig genutzt worden sein, damit die Verbindung wiederverwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="76af1-1041">A request body must be fully consumed to allow the connection to be reused.</span></span> <span data-ttu-id="76af1-1042">Die App nutzt nicht immer den Anforderungstext, z. B. bei einer `POST`-Anforderung, bei der der Server eine Umleitung oder 404-Antwort zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="76af1-1042">The app doesn't always consume the request body, such as a `POST` requests where the server returns a redirect or 404 response.</span></span> <span data-ttu-id="76af1-1043">Im Fall der `POST`-Umleitung:</span><span class="sxs-lookup"><span data-stu-id="76af1-1043">In the `POST`-redirect case:</span></span>

* <span data-ttu-id="76af1-1044">Der Client hat möglicherweise bereits einen Teil der `POST`-Daten gesendet.</span><span class="sxs-lookup"><span data-stu-id="76af1-1044">The client may already have sent part of the `POST` data.</span></span>
* <span data-ttu-id="76af1-1045">Der Server schreibt die 301-Antwort.</span><span class="sxs-lookup"><span data-stu-id="76af1-1045">The server writes the 301 response.</span></span>
* <span data-ttu-id="76af1-1046">Die Verbindung kann erst dann für eine neue Anforderung verwendet werden, wenn die `POST`-Daten im vorherigen Anforderungstext vollständig gelesen wurden.</span><span class="sxs-lookup"><span data-stu-id="76af1-1046">The connection can't be used for a new request until the `POST` data from the previous request body has been fully read.</span></span>
* <span data-ttu-id="76af1-1047">Kestrel versucht, den Anforderungstext zu leeren.</span><span class="sxs-lookup"><span data-stu-id="76af1-1047">Kestrel tries to drain the request body.</span></span> <span data-ttu-id="76af1-1048">Leeren des Anforderungstexts bedeutet, die Daten zu lesen und zu verwerfen, ohne sie zu verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="76af1-1048">Draining the request body means reading and discarding the data without processing it.</span></span>

<span data-ttu-id="76af1-1049">Beim Leerungsvorgang wird ein Kompromiss zwischen der Möglichkeit der Wiederverwendung der Verbindung und der Dauer gefunden, die zum Leeren der verbleibenden Daten benötigt wird:</span><span class="sxs-lookup"><span data-stu-id="76af1-1049">The draining process makes a tradoff between allowing the connection to be reused and the time it takes to drain any remaining data:</span></span>

* <span data-ttu-id="76af1-1050">Für das Leeren gilt ein Zeitlimit von fünf Sekunden, das nicht konfigurierbar ist.</span><span class="sxs-lookup"><span data-stu-id="76af1-1050">Draining has a timeout of five seconds, which isn't configurable.</span></span>
* <span data-ttu-id="76af1-1051">Wenn vor dem Zeitlimit nicht alle durch den Header `Content-Length` oder `Transfer-Encoding` angegebenen Daten gelesen wurden, wird die Verbindung geschlossen.</span><span class="sxs-lookup"><span data-stu-id="76af1-1051">If all of the data specified by the `Content-Length` or `Transfer-Encoding` header hasn't been read before the timeout, the connection is closed.</span></span>

<span data-ttu-id="76af1-1052">Mitunter möchten Sie die Anforderung sofort beenden, entweder bevor oder nachdem die Antwort geschrieben wurde.</span><span class="sxs-lookup"><span data-stu-id="76af1-1052">Sometimes you may want to terminate the request immediately, before or after writing the response.</span></span> <span data-ttu-id="76af1-1053">Beispielsweise können für Clients restriktive Datenobergrenzen gelten, sodass das Begrenzen hochgeladener Daten Priorität haben kann.</span><span class="sxs-lookup"><span data-stu-id="76af1-1053">For example, clients may have restrictive data caps, so limiting uploaded data might be a priority.</span></span> <span data-ttu-id="76af1-1054">Um in solchen Fällen eine Anforderung zu beenden, rufen Sie [HttpContext.abort](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A) in einem Controller, eine Razor Page oder Middleware auf.</span><span class="sxs-lookup"><span data-stu-id="76af1-1054">In such cases to terminate a request, call [HttpContext.Abort](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A) from a controller, Razor Page, or middleware.</span></span>

<span data-ttu-id="76af1-1055">Gegen den Aufruf von `Abort` gibt es Vorbehalte:</span><span class="sxs-lookup"><span data-stu-id="76af1-1055">There are caveats to calling `Abort`:</span></span>

* <span data-ttu-id="76af1-1056">Das Erstellen neuer Verbindungen kann langsam und aufwendig sein.</span><span class="sxs-lookup"><span data-stu-id="76af1-1056">Creating new connections can be slow and expensive.</span></span>
* <span data-ttu-id="76af1-1057">Es gibt keine Garantie, dass der Client die Antwort gelesen hat, bevor die Verbindung geschlossen wird.</span><span class="sxs-lookup"><span data-stu-id="76af1-1057">There's no guarantee that the client has read the response before the connection closes.</span></span>
* <span data-ttu-id="76af1-1058">Das Aufrufen von `Abort` sollte selten erfolgen und schweren Fehlerfällen und nicht gewöhnlichen Fehlern vorbehalten sein.</span><span class="sxs-lookup"><span data-stu-id="76af1-1058">Calling `Abort` should be rare and reserved for severe error cases, not common errors.</span></span>
  * <span data-ttu-id="76af1-1059">Rufen Sie `Abort` nur dann auf, wenn ein konkretes Problem gelöst werden muss.</span><span class="sxs-lookup"><span data-stu-id="76af1-1059">Only call `Abort` when a specific problem needs to be solved.</span></span> <span data-ttu-id="76af1-1060">Rufen Sie beispielsweise `Abort` auf, wenn böswillige Clients versuchen, Daten per `POST` abzurufen, oder wenn es einen Fehler im Clientcode gibt, der umfangreiche oder zahlreiche Anforderungen verursacht.</span><span class="sxs-lookup"><span data-stu-id="76af1-1060">For example, call `Abort` if malicious clients are trying to `POST` data or when there's a bug in client code that causes large or numerous requests.</span></span>
  * <span data-ttu-id="76af1-1061">Rufen Sie `Abort` nicht für gewöhnliche Fehlersituationen auf, wie z. B. HTTP 404 (Nicht gefunden).</span><span class="sxs-lookup"><span data-stu-id="76af1-1061">Don't call `Abort` for common error situations, such as HTTP 404 (Not Found).</span></span>

<span data-ttu-id="76af1-1062">Durch den Aufruf von [HttpResponse.CompleteAsync](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A) vor dem Aufruf von `Abort` wird sichergestellt, dass der Server das Schreiben der Antwort abgeschlossen hat.</span><span class="sxs-lookup"><span data-stu-id="76af1-1062">Calling [HttpResponse.CompleteAsync](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A) before calling `Abort` ensures that the server has completed writing the response.</span></span> <span data-ttu-id="76af1-1063">Das Clientverhalten ist jedoch nicht vorhersagbar, und es kann sein, dass die Antwort nicht gelesen wird, bevor die Verbindung abgebrochen wird.</span><span class="sxs-lookup"><span data-stu-id="76af1-1063">However, client behavior isn't predictable and they may not read the response before the connection is aborted.</span></span>

<span data-ttu-id="76af1-1064">Dieser Prozess bei HTTP/2 ist anders, da das Protokoll den Abbruch einzelner Anforderungsströme ohne Schließen der Verbindung unterstützt.</span><span class="sxs-lookup"><span data-stu-id="76af1-1064">This process is different for HTTP/2 because the protocol supports aborting individual request streams without closing the connection.</span></span> <span data-ttu-id="76af1-1065">Das fünfsekündige Zeitlimit für das Leeren gilt nicht.</span><span class="sxs-lookup"><span data-stu-id="76af1-1065">The five second drain timeout doesn't apply.</span></span> <span data-ttu-id="76af1-1066">Wenn nach dem Fertigstellen einer Antwort ungelesene Anforderungstextdaten vorhanden sind, sendet der Server einen HTTP/2-RST-Datenrahmen.</span><span class="sxs-lookup"><span data-stu-id="76af1-1066">If there's any unread request body data after completing a response, then the server sends an HTTP/2 RST frame.</span></span> <span data-ttu-id="76af1-1067">Zusätzliche Datenrahmen im Anforderungstext werden ignoriert.</span><span class="sxs-lookup"><span data-stu-id="76af1-1067">Additional request body data frames are ignored.</span></span>

<span data-ttu-id="76af1-1068">Wenn möglich, ist es für Clients besser, den Anforderungsheader [Expect: 100-continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) zu verwenden und auf die Antwort des Servers zu warten, bevor mit dem Senden des Anforderungstexts begonnen wird.</span><span class="sxs-lookup"><span data-stu-id="76af1-1068">If possible, it's better for clients to utilize the [Expect: 100-continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) request header and wait for the server to respond before starting to send the request body.</span></span> <span data-ttu-id="76af1-1069">Das gibt dem Client die Gelegenheit, die Antwort zu prüfen und abzubrechen, bevor nicht benötigte Daten gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="76af1-1069">That gives the client an opportunity to examine the response and abort before sending unneeded data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="76af1-1070">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="76af1-1070">Additional resources</span></span>

* <span data-ttu-id="76af1-1071">Bei der Verwendung von UNIX-Sockets unter Linux wird der Socket beim Herunterfahren der App nicht automatisch gelöscht.</span><span class="sxs-lookup"><span data-stu-id="76af1-1071">When using UNIX sockets on Linux, the socket is not automatically deleted on app shut down.</span></span> <span data-ttu-id="76af1-1072">Weitere Informationen finden Sie in [diesem GitHub-Issue](https://github.com/dotnet/aspnetcore/issues/14134).</span><span class="sxs-lookup"><span data-stu-id="76af1-1072">For more information, see [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/14134).</span></span>
* <xref:test/troubleshoot>
* <xref:security/enforcing-ssl>
* <xref:host-and-deploy/proxy-load-balancer>
* [<span data-ttu-id="76af1-1073">RFC 7230: Message Syntax and Routing (Abschnitt 5.4: Host)</span><span class="sxs-lookup"><span data-stu-id="76af1-1073">RFC 7230: Message Syntax and Routing (Section 5.4: Host)</span></span>](https://tools.ietf.org/html/rfc7230#section-5.4)
