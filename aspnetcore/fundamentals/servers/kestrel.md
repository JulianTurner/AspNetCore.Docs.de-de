---
title: Implementierung des Webservers Kestrel in ASP.NET Core
author: rick-anderson
description: Einführung in Kestrel, dem plattformübergreifenden Webserver für ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/04/2020
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
uid: fundamentals/servers/kestrel
ms.openlocfilehash: 44558a0f2fdc61eb860223658f5bef1d0117ba87
ms.sourcegitcommit: e519d95d17443abafba8f712ac168347b15c8b57
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/02/2020
ms.locfileid: "91653943"
---
# <a name="kestrel-web-server-implementation-in-aspnet-core"></a><span data-ttu-id="2cd9d-103">Implementierung des Webservers Kestrel in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2cd9d-103">Kestrel web server implementation in ASP.NET Core</span></span>

<span data-ttu-id="2cd9d-104">Von [Tom Dykstra](https://github.com/tdykstra), [Chris Ross](https://github.com/Tratcher) und [Stephen Halter](https://twitter.com/halter73)</span><span class="sxs-lookup"><span data-stu-id="2cd9d-104">By [Tom Dykstra](https://github.com/tdykstra), [Chris Ross](https://github.com/Tratcher), and [Stephen Halter](https://twitter.com/halter73)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="2cd9d-105">Einführung in Kestrel, dem plattformübergreifenden [Webserver für ASP.NET Core](xref:fundamentals/servers/index).</span><span class="sxs-lookup"><span data-stu-id="2cd9d-105">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="2cd9d-106">Kestrel ist der Webserver, der standardmäßig in ASP.NET Core-Projektvorlagen enthalten ist.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-106">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="2cd9d-107">Kestrel unterstützt die folgenden Szenarios:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-107">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="2cd9d-108">HTTPS</span><span class="sxs-lookup"><span data-stu-id="2cd9d-108">HTTPS</span></span>
* <span data-ttu-id="2cd9d-109">Nicht transparente Upgrades, die zum Aktivieren von [WebSockets](https://github.com/aspnet/websockets) verwendet werden</span><span class="sxs-lookup"><span data-stu-id="2cd9d-109">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="2cd9d-110">Unix-Sockets für eine hohe Leistung im Hintergrund von Nginx</span><span class="sxs-lookup"><span data-stu-id="2cd9d-110">Unix sockets for high performance behind Nginx</span></span>
* <span data-ttu-id="2cd9d-111">HTTP/2 (außer unter macOS&dagger;)</span><span class="sxs-lookup"><span data-stu-id="2cd9d-111">HTTP/2 (except on macOS&dagger;)</span></span>

<span data-ttu-id="2cd9d-112">&dagger;HTTP/2 wird unter macOS in einem zukünftigen Release unterstützt.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-112">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>

<span data-ttu-id="2cd9d-113">Kestrel wird auf allen Plattformen und für alle Versionen unterstützt, die .NET Core unterstützt.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-113">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="2cd9d-114">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="2cd9d-114">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="http2-support"></a><span data-ttu-id="2cd9d-115">HTTP/2-Unterstützung</span><span class="sxs-lookup"><span data-stu-id="2cd9d-115">HTTP/2 support</span></span>

<span data-ttu-id="2cd9d-116">[HTTP/2](https://httpwg.org/specs/rfc7540.html) ist für ASP.NET Core-Apps verfügbar, wenn die folgenden Basisanforderungen erfüllt sind:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-116">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is available for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="2cd9d-117">Betriebssystem&dagger;</span><span class="sxs-lookup"><span data-stu-id="2cd9d-117">Operating system&dagger;</span></span>
  * <span data-ttu-id="2cd9d-118">Windows Server 2016/Windows 10 oder höher&Dagger;</span><span class="sxs-lookup"><span data-stu-id="2cd9d-118">Windows Server 2016/Windows 10 or later&Dagger;</span></span>
  * <span data-ttu-id="2cd9d-119">Linux mit OpenSSL 1.0.2 oder höher (z.B. Ubuntu 16.04 oder höher)</span><span class="sxs-lookup"><span data-stu-id="2cd9d-119">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
* <span data-ttu-id="2cd9d-120">Zielframework: .NET Core 2.2 oder höher</span><span class="sxs-lookup"><span data-stu-id="2cd9d-120">Target framework: .NET Core 2.2 or later</span></span>
* <span data-ttu-id="2cd9d-121">[ALPN](https://tools.ietf.org/html/rfc7301#section-3)-Verbindung (Application-Layer Protocol Negotiation)</span><span class="sxs-lookup"><span data-stu-id="2cd9d-121">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="2cd9d-122">TLS 1.2-Verbindung oder höher</span><span class="sxs-lookup"><span data-stu-id="2cd9d-122">TLS 1.2 or later connection</span></span>

<span data-ttu-id="2cd9d-123">&dagger;HTTP/2 wird unter macOS in einem zukünftigen Release unterstützt.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-123">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>
<span data-ttu-id="2cd9d-124">&Dagger;Kestrel bietet eingeschränkte Unterstützung für HTTP/2 unter Windows Server 2012 R2 und Windows 8.1.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-124">&Dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="2cd9d-125">Die Unterstützung ist eingeschränkt, weil die Liste der unterstützten TLS-Verschlüsselungssammlungen unter diesen Betriebssystemen begrenzt ist.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-125">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="2cd9d-126">Zum Sichern von TLS-Verbindungen ist möglicherweise ein durch einen Elliptic Curve Digital Signature Algorithm (ECDSA) generiertes Zertifikat erforderlich.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-126">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

<span data-ttu-id="2cd9d-127">Wenn eine HTTP/2-Verbindung hergestellt wurde, meldet [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*)`HTTP/2`.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-127">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="2cd9d-128">HTTP/2 ist standardmäßig deaktiviert.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-128">HTTP/2 is disabled by default.</span></span> <span data-ttu-id="2cd9d-129">Weitere Informationen zur Konfiguration finden Sie in den Abschnitten [Kestrel-Optionen](#kestrel-options) und [ListenOptions.Protocols](#listenoptionsprotocols).</span><span class="sxs-lookup"><span data-stu-id="2cd9d-129">For more information on configuration, see the [Kestrel options](#kestrel-options) and [ListenOptions.Protocols](#listenoptionsprotocols) sections.</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="2cd9d-130">Verwenden von Kestrel mit einem Reverseproxy</span><span class="sxs-lookup"><span data-stu-id="2cd9d-130">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="2cd9d-131">Kestrel kann eigenständig oder mit einem *Reverseproxyserver* wie z.B. [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org) oder [Apache](https://httpd.apache.org/) verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-131">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="2cd9d-132">Ein Reverseproxyserver empfängt HTTP-Anforderungen aus dem Netzwerk und leitet diese an Kestrel weiter.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-132">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="2cd9d-133">Kestrel bei Verwendung als Webserver mit direkter Internetverbindung:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-133">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Kestrel kommuniziert direkt und ohne Reverseproxyserver mit dem Internet](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="2cd9d-135">Kestrel bei Verwendung in einer Reverseproxykonfiguration:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-135">Kestrel used in a reverse proxy configuration:</span></span>

![Kestrel kommuniziert indirekt mit dem Internet über einen Reverseproxyserver wie IIS, Nginx oder Apache](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="2cd9d-137">Jede der beiden Konfigurationen – mit oder ohne einen Reverseproxyserver – stellt eine unterstützte Hostingkonfiguration dar.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-137">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="2cd9d-138">Bei Verwendung als Edgeserver ohne Reverseproxyserver unterstützt Kestrel die gemeinsame Nutzung der gleichen IP-Adresse und des gleichen Ports durch mehrere Prozesse nicht.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-138">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="2cd9d-139">Wenn Kestrel für das Lauschen an einem Port konfiguriert ist, verarbeitet Kestrel den gesamten Datenverkehr für diesen Port unabhängig von den `Host`-Headern der Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-139">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="2cd9d-140">Ein Reverseproxy, der Ports freigeben kann, kann Anforderungen an Kestrel über eine eindeutige IP und einen eindeutigen Port weiterleiten.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-140">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="2cd9d-141">Auch wenn kein Reverseproxyserver erforderlich ist, kann die Verwendung eines solchen empfehlenswert sein.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-141">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="2cd9d-142">Für einen Reverseproxy gilt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-142">A reverse proxy:</span></span>

* <span data-ttu-id="2cd9d-143">Er kann die verfügbar gemachten öffentlichen Oberflächen der von ihm gehosteten Apps einschränken.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-143">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="2cd9d-144">Er stellt eine zusätzliche Ebene für Konfiguration und Schutz bereit.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-144">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="2cd9d-145">Er lässt sich besser in die vorhandene Infrastruktur integrieren.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-145">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="2cd9d-146">Er vereinfacht die Konfiguration von Lastenausgleich und sicherer Kommunikation (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="2cd9d-146">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="2cd9d-147">Nur der Reverseproxyserver erfordert ein X.509-Zertifikat, und dieser Server kann mit den Servern der App im internen Netzwerk über einfaches HTTP kommunizieren.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-147">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="2cd9d-148">Das Hosting in einer Reverseproxykonfiguration erfordert [Hostfilterung](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="2cd9d-148">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="kestrel-in-aspnet-core-apps"></a><span data-ttu-id="2cd9d-149">Kestrel in ASP.NET Core-Apps</span><span class="sxs-lookup"><span data-stu-id="2cd9d-149">Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="2cd9d-150">ASP.NET Core-Projektvorlagen verwenden Kestrel standardmäßig.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-150">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="2cd9d-151">In *Program.cs* ruft die <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*>-Methode <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>auf:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-151">In *Program.cs*, the <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> method calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=8)]

<span data-ttu-id="2cd9d-152">Weitere Informationen zum Erstellen des Hosts finden Sie im Artikel <xref:fundamentals/host/generic-host#set-up-a-host> in den Abschnitten *Einrichten eines Hosts* und *Standardeinstellungen für den Generator*.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-152">For more information on building the host, see the *Set up a host* and *Default builder settings* sections of <xref:fundamentals/host/generic-host#set-up-a-host>.</span></span>

<span data-ttu-id="2cd9d-153">Um zusätzliche Konfiguration nach dem Aufruf von `ConfigureWebHostDefaults` bereitzustellen, verwenden Sie `ConfigureKestrel`:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-153">To provide additional configuration after calling `ConfigureWebHostDefaults`, use `ConfigureKestrel`:</span></span>

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

## <a name="kestrel-options"></a><span data-ttu-id="2cd9d-154">Kestrel-Optionen</span><span class="sxs-lookup"><span data-stu-id="2cd9d-154">Kestrel options</span></span>

<span data-ttu-id="2cd9d-155">Der Kestrel-Webserver verfügt über einschränkende Konfigurationsoptionen, die besonders nützlich bei Bereitstellungen mit Internetzugriff sind.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-155">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="2cd9d-156">Legen Sie Einschränkungen für die <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits>-Eigenschaft der <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>-Klasse fest.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-156">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="2cd9d-157">Die `Limits`-Eigenschaft enthält eine Instanz der <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>-Klasse.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-157">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="2cd9d-158">In den folgenden Beispielen wird der <xref:Microsoft.AspNetCore.Server.Kestrel.Core>-Namespace verwendet:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-158">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="2cd9d-159">In den Beispielen, die weiter unten in diesem Artikel aufgeführt sind, werden Kestrel-Optionen in C#-Code konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-159">In examples shown later in this article, Kestrel options are configured in C# code.</span></span> <span data-ttu-id="2cd9d-160">Kestrel-Optionen können ebenso mithilfe eines [Konfigurationsanbieters](xref:fundamentals/configuration/index) festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-160">Kestrel options can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="2cd9d-161">Beispielsweise kann der [Dateikonfigurationsanbieter](xref:fundamentals/configuration/index#file-configuration-provider) die Kestrel-Konfiguration aus einer *appsettings.json*- oder *appsettings.{Umgebung}.json*-Datei laden:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-161">For example, the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider) can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

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
> <span data-ttu-id="2cd9d-162"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> und die [Endpunktkonfiguration](#endpoint-configuration) können über Konfigurationsanbieter konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-162"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> and [endpoint configuration](#endpoint-configuration) are configurable from configuration providers.</span></span> <span data-ttu-id="2cd9d-163">Die verbleibende Kestrel-Konfiguration muss in C#-Code konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-163">Remaining Kestrel configuration must be configured in C# code.</span></span>

<span data-ttu-id="2cd9d-164">Verwenden Sie **einen** der folgenden Ansätze:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-164">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="2cd9d-165">Konfigurieren Sie Kestrel in `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-165">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="2cd9d-166">Fügen Sie eine Instanz von `IConfiguration` in die `Startup`-Klasse ein.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-166">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="2cd9d-167">Im folgenden Beispiel wird davon ausgegangen, dass die eingefügte Konfiguration der `Configuration`-Eigenschaft zugewiesen wird.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-167">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="2cd9d-168">Laden Sie in `Startup.ConfigureServices` den Abschnitt `Kestrel` der Konfiguration in die Konfiguration von Kestrel:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-168">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

* <span data-ttu-id="2cd9d-169">Konfigurieren Sie Kestrel beim Erstellen des Hosts:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-169">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="2cd9d-170">Laden Sie in *Program.cs* den Abschnitt `Kestrel` der Konfiguration in die Konfiguration von Kestrel:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-170">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

<span data-ttu-id="2cd9d-171">Beide vorangehenden Ansätze funktionieren mit jedem [Konfigurationsanbieter](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="2cd9d-171">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="2cd9d-172">Keep-Alive-Timeout</span><span class="sxs-lookup"><span data-stu-id="2cd9d-172">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="2cd9d-173">Ruft das [Keep-Alive-Timeout](https://tools.ietf.org/html/rfc7230#section-6.5) ab oder legt es fest.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-173">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="2cd9d-174">Standardwert: 2 Minuten.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-174">Defaults to 2 minutes.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=19-20)]

### <a name="maximum-client-connections"></a><span data-ttu-id="2cd9d-175">Maximale Anzahl der Clientverbindungen</span><span class="sxs-lookup"><span data-stu-id="2cd9d-175">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="2cd9d-176">Die maximale Anzahl von gleichzeitig geöffneten TCP-Verbindungen kann mithilfe von folgendem Code für die gesamte App festgelegt werden:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-176">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

<span data-ttu-id="2cd9d-177">Es gibt einen separaten Grenzwert für Verbindungen, die von HTTP oder HTTPS auf ein anderes Protokoll aktualisiert wurden (z.B. auf eine WebSockets-Anforderung).</span><span class="sxs-lookup"><span data-stu-id="2cd9d-177">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="2cd9d-178">Nachdem eine Verbindung aktualisiert wurde, zählt diese nicht mehr für den `MaxConcurrentConnections`-Grenzwert.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-178">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

<span data-ttu-id="2cd9d-179">Die maximale Anzahl von Verbindungen ist standardmäßig nicht begrenzt (NULL).</span><span class="sxs-lookup"><span data-stu-id="2cd9d-179">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="2cd9d-180">Maximale Größe des Anforderungstexts</span><span class="sxs-lookup"><span data-stu-id="2cd9d-180">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="2cd9d-181">Die maximale Größe des Anforderungstexts beträgt standardmäßig 30.000.000 Byte, also ungefähr 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-181">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="2cd9d-182">Die empfohlene Methode zur Außerkraftsetzung des Grenzwerts in einer ASP.NET Core-MVC-App besteht im Verwenden des <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute>-Attributs in einer Aktionsmethode:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-182">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="2cd9d-183">Im folgenden Beispiel wird veranschaulicht, wie die Einschränkungen auf jeder Anforderung für die App konfiguriert werden:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-183">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

<span data-ttu-id="2cd9d-184">Außer Kraft setzen der Einstellung für eine bestimmte Anforderung in Middleware:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-184">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="2cd9d-185">Eine Ausnahme wird ausgelöst, wenn die App den Grenzwert einer Anforderung konfiguriert, nachdem die App bereits mit dem Lesen der Anforderung begonnen hat.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-185">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="2cd9d-186">Es gibt eine `IsReadOnly`-Eigenschaft, die angibt, wenn sich die `MaxRequestBodySize`-Eigenschaft im schreibgeschützten Zustand befindet, also wenn der Grenzwert nicht mehr konfiguriert werden kann.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-186">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="2cd9d-187">Wenn eine App [prozessextern](xref:host-and-deploy/iis/index#out-of-process-hosting-model) hinter dem [ASP.NET Core-Modul](xref:host-and-deploy/aspnet-core-module) ausgeführt wird, ist das Größenlimit von Kestrel für Anforderungstext deaktiviert, weil IIS dieses Limit bereits festlegt.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-187">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="2cd9d-188">Minimale Datenrate des Anforderungstexts</span><span class="sxs-lookup"><span data-stu-id="2cd9d-188">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="2cd9d-189">Kestrel überprüft sekündlich, ob Daten mit der angegebenen Rate in Bytes/Sekunde eingehen.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-189">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="2cd9d-190">Wenn die Rate den mindestens erforderlichen Wert unterschreitet, wird für die Verbindung wegen Timeout getrennt. Bei der Toleranzperiode handelt es sich um die Zeitspanne, die Kestrel dem Client gewährt, um die Senderate auf den mindestens erforderlichen Wert zu erhöhen. Die Rate wird währenddessen nicht überprüft.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-190">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="2cd9d-191">Diese Toleranzperiode beugt dem Trennen von Verbindungen vor, die Daten aufgrund eines langsamen TCP-Starts anfänglich mit einer niedrigen Rate senden.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-191">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="2cd9d-192">Die mindestens erforderliche Rate beträgt standardmäßig 240 Bytes/Sekunde mit einer Toleranzperiode von 5 Sekunden.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-192">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="2cd9d-193">Für die Antwort gilt ebenfalls eine mindestens erforderliche Rate.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-193">A minimum rate also applies to the response.</span></span> <span data-ttu-id="2cd9d-194">Der Code zum Festlegen des Grenzwerts für Anforderung und Antwort ist abgesehen von `RequestBody` oder `Response` in den Namen von Eigenschaften und Schnittstelle identisch.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-194">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="2cd9d-195">In diesem Beispiel wird veranschaulicht, wie Sie die mindestens erforderlichen Datenraten in *Program.cs* konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-195">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-11)]

<span data-ttu-id="2cd9d-196">Außer Kraft setzen des minimalen Ratenlimits pro Anforderung in Middleware:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-196">Override the minimum rate limits per request in middleware:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

<span data-ttu-id="2cd9d-197">Das <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature> aus dem vorherigen Beispiel ist in `HttpContext.Features` für HTTP/2-Anforderungen nicht vorhanden, weil die Änderung von Ratenlimits für jede Anforderung aufgrund der Unterstützung für Anforderungsmultiplexing von HTTP/2 von diesem Protokoll unterstützt wird.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-197">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature> referenced in the prior sample is not present in `HttpContext.Features` for HTTP/2 requests because modifying rate limits on a per-request basis is generally not supported for HTTP/2 due to the protocol's support for request multiplexing.</span></span> <span data-ttu-id="2cd9d-198"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> ist jedoch in `HttpContext.Features` für HTTP/2-Anforderungen noch vorhanden, weil das Leseratenlimit weiterhin für jede Anforderung *vollständig deaktiviert* werden kann, indem `IHttpMinRequestBodyDataRateFeature.MinDataRate` auch für eine HTTP/2-Anforderung auf `null` festgelegt wird.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-198">However, the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> is still present `HttpContext.Features` for HTTP/2 requests, because the read rate limit can still be *disabled entirely* on a per-request basis by setting `IHttpMinRequestBodyDataRateFeature.MinDataRate` to `null` even for an HTTP/2 request.</span></span> <span data-ttu-id="2cd9d-199">Der Versuch, `IHttpMinRequestBodyDataRateFeature.MinDataRate` zu lesen oder auf einen anderen Wert als `null` festzulegen, führt dazu, dass bei einer HTTP/2-Anforderung eine `NotSupportedException` ausgelöst wird.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-199">Attempting to read `IHttpMinRequestBodyDataRateFeature.MinDataRate` or attempting to set it to a value other than `null` will result in a `NotSupportedException` being thrown given an HTTP/2 request.</span></span>

<span data-ttu-id="2cd9d-200">Serverweite Ratenlimits, die über `KestrelServerOptions.Limits` konfiguriert sind, gelten auch für HTTP/1.x- und HTTP/2-Verbindungen.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-200">Server-wide rate limits configured via `KestrelServerOptions.Limits` still apply to both HTTP/1.x and HTTP/2 connections.</span></span>

### <a name="request-headers-timeout"></a><span data-ttu-id="2cd9d-201">Timeout für Anforderungsheader</span><span class="sxs-lookup"><span data-stu-id="2cd9d-201">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="2cd9d-202">Ruft die maximale Zeitspanne ab, während der der Server Anforderungsheader empfängt, oder legt diese fest.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-202">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="2cd9d-203">Der Standardwert beträgt 30 Sekunden.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-203">Defaults to 30 seconds.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=21-22)]

### <a name="maximum-streams-per-connection"></a><span data-ttu-id="2cd9d-204">Maximale Datenströme pro Verbindung</span><span class="sxs-lookup"><span data-stu-id="2cd9d-204">Maximum streams per connection</span></span>

<span data-ttu-id="2cd9d-205">`Http2.MaxStreamsPerConnection` schränkt die Anzahl gleichzeitiger Anforderungsdatenströme pro HTTP/2-Verbindung ein.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-205">`Http2.MaxStreamsPerConnection` limits the number of concurrent request streams per HTTP/2 connection.</span></span> <span data-ttu-id="2cd9d-206">Überschüssige Datenströme werden zurückgewiesen.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-206">Excess streams are refused.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
});
```

<span data-ttu-id="2cd9d-207">Der Standardwert ist 100.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-207">The default value is 100.</span></span>

### <a name="header-table-size"></a><span data-ttu-id="2cd9d-208">Größe der Headertabelle</span><span class="sxs-lookup"><span data-stu-id="2cd9d-208">Header table size</span></span>

<span data-ttu-id="2cd9d-209">Der HPACK-Decoder dekomprimiert HTTP-Header für HTTP/2-Verbindungen.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-209">The HPACK decoder decompresses HTTP headers for HTTP/2 connections.</span></span> <span data-ttu-id="2cd9d-210">`Http2.HeaderTableSize` schränkt die Größe der Headerkomprimierungstabelle ein, die der HPACK-Decoder verwendet.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-210">`Http2.HeaderTableSize` limits the size of the header compression table that the HPACK decoder uses.</span></span> <span data-ttu-id="2cd9d-211">Der Wert wird in Oktetten bereitgestellt und muss größer als null (0) sein.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-211">The value is provided in octets and must be greater than zero (0).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.HeaderTableSize = 4096;
});
```

<span data-ttu-id="2cd9d-212">Der Standardwert ist 4096.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-212">The default value is 4096.</span></span>

### <a name="maximum-frame-size"></a><span data-ttu-id="2cd9d-213">Maximale Framegröße</span><span class="sxs-lookup"><span data-stu-id="2cd9d-213">Maximum frame size</span></span>

<span data-ttu-id="2cd9d-214">`Http2.MaxFrameSize` gibt die maximal zulässige Größe einer vom Server empfangenen oder gesendeten HTTP/2-Verbindungsrahmen-Nutzlast an.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-214">`Http2.MaxFrameSize` indicates the maximum allowed size of an HTTP/2 connection frame payload received or sent by the server.</span></span> <span data-ttu-id="2cd9d-215">Der Wert wird in Oktetten bereitgestellt und muss zwischen 2^14 (16.384) und 2^24-1 (16.777.215) liegen.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-215">The value is provided in octets and must be between 2^14 (16,384) and 2^24-1 (16,777,215).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxFrameSize = 16384;
});
```

<span data-ttu-id="2cd9d-216">Der Standardwert ist 2^14 (16.384).</span><span class="sxs-lookup"><span data-stu-id="2cd9d-216">The default value is 2^14 (16,384).</span></span>

### <a name="maximum-request-header-size"></a><span data-ttu-id="2cd9d-217">Maximale Größe des Anforderungsheaders</span><span class="sxs-lookup"><span data-stu-id="2cd9d-217">Maximum request header size</span></span>

<span data-ttu-id="2cd9d-218">`Http2.MaxRequestHeaderFieldSize` gibt die maximal zulässige Größe in Oktetten der Anforderungsheaderwerte an.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-218">`Http2.MaxRequestHeaderFieldSize` indicates the maximum allowed size in octets of request header values.</span></span> <span data-ttu-id="2cd9d-219">Dieser Grenzwert gilt sowohl für den Namen als auch den Wert in der komprimierten und nicht komprimierten Darstellung.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-219">This limit applies to both name and value in their compressed and uncompressed representations.</span></span> <span data-ttu-id="2cd9d-220">Der Wert muss größer als 0 (null) sein.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-220">The value must be greater than zero (0).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
});
```

<span data-ttu-id="2cd9d-221">Der Standardwert ist 8.192.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-221">The default value is 8,192.</span></span>

### <a name="initial-connection-window-size"></a><span data-ttu-id="2cd9d-222">Anfangsfenstergröße der Verbindung</span><span class="sxs-lookup"><span data-stu-id="2cd9d-222">Initial connection window size</span></span>

<span data-ttu-id="2cd9d-223">`Http2.InitialConnectionWindowSize` gibt die maximalen Anforderungstextdaten in Byte an, die der Server auf einmal für alle Anforderungen (Streams) pro Verbindung aggregiert.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-223">`Http2.InitialConnectionWindowSize` indicates the maximum request body data in bytes the server buffers at one time aggregated across all requests (streams) per connection.</span></span> <span data-ttu-id="2cd9d-224">Anforderungen werden auch durch `Http2.InitialStreamWindowSize` beschränkt.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-224">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="2cd9d-225">Der Wert muss größer als oder gleich 65.535 und kleiner als 2^31 (2.147.483.648) sein.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-225">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
});
```

<span data-ttu-id="2cd9d-226">Der Standardwert ist 128 KB (131.072).</span><span class="sxs-lookup"><span data-stu-id="2cd9d-226">The default value is 128 KB (131,072).</span></span>

### <a name="initial-stream-window-size"></a><span data-ttu-id="2cd9d-227">Anfangsfenstergröße des Streams</span><span class="sxs-lookup"><span data-stu-id="2cd9d-227">Initial stream window size</span></span>

<span data-ttu-id="2cd9d-228">`Http2.InitialStreamWindowSize` gibt die maximalen Anforderungstextdaten in Byte an, die der Server auf einmal pro Anforderung (Stream) puffert.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-228">`Http2.InitialStreamWindowSize` indicates the maximum request body data in bytes the server buffers at one time per request (stream).</span></span> <span data-ttu-id="2cd9d-229">Anforderungen werden auch durch `Http2.InitialConnectionWindowSize` beschränkt.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-229">Requests are also limited by `Http2.InitialConnectionWindowSize`.</span></span> <span data-ttu-id="2cd9d-230">Der Wert muss größer als oder gleich 65.535 und kleiner als 2^31 (2.147.483.648) sein.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-230">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
});
```

<span data-ttu-id="2cd9d-231">Der Standardwert ist 96 KB (98.304).</span><span class="sxs-lookup"><span data-stu-id="2cd9d-231">The default value is 96 KB (98,304).</span></span>

### <a name="trailers"></a><span data-ttu-id="2cd9d-232">Trailer</span><span class="sxs-lookup"><span data-stu-id="2cd9d-232">Trailers</span></span>

[!INCLUDE[](~/includes/trailers.md)]

### <a name="reset"></a><span data-ttu-id="2cd9d-233">Reset</span><span class="sxs-lookup"><span data-stu-id="2cd9d-233">Reset</span></span>

[!INCLUDE[](~/includes/reset.md)]

### <a name="synchronous-io"></a><span data-ttu-id="2cd9d-234">Synchrone E/A-Vorgänge</span><span class="sxs-lookup"><span data-stu-id="2cd9d-234">Synchronous I/O</span></span>

<span data-ttu-id="2cd9d-235"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> steuert, ob für Anforderung und Antwort synchrone E/A-Vorgänge zulässig sind.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-235"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous I/O is allowed for the request and response.</span></span> <span data-ttu-id="2cd9d-236">Der Standardwert ist `false`.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-236">The default value is `false`.</span></span>

> [!WARNING]
> <span data-ttu-id="2cd9d-237">Sehr viele blockierende synchrone E/A-Vorgänge können zu einem Ressourcenmangel im Threadpool führen, wodurch die App nicht mehr reagiert.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-237">A large number of blocking synchronous I/O operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="2cd9d-238">Aktivieren Sie `AllowSynchronousIO` nur bei Verwendung einer Bibliothek, die asynchrone E/A-Vorgänge nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-238">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous I/O.</span></span>

<span data-ttu-id="2cd9d-239">Das folgende Beispiel aktiviert synchrone E/A-Vorgänge:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-239">The following example enables synchronous I/O:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

<span data-ttu-id="2cd9d-240">Weitere Informationen zu anderen Kestrel-Optionen und -Einschränkungen finden Sie unter:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-240">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="2cd9d-241">Endpunktkonfiguration</span><span class="sxs-lookup"><span data-stu-id="2cd9d-241">Endpoint configuration</span></span>

<span data-ttu-id="2cd9d-242">Standardmäßig wird ASP.NET Core an Folgendes gebunden:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-242">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="2cd9d-243">`https://localhost:5001` (wenn ein lokales Entwicklungszertifikat vorhanden ist)</span><span class="sxs-lookup"><span data-stu-id="2cd9d-243">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="2cd9d-244">Verwenden Sie Folgendes zum Angeben der URLs:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-244">Specify URLs using the:</span></span>

* <span data-ttu-id="2cd9d-245">Die Umgebungsvariable `ASPNETCORE_URLS`</span><span class="sxs-lookup"><span data-stu-id="2cd9d-245">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="2cd9d-246">Das Befehlszeilenargument `--urls`</span><span class="sxs-lookup"><span data-stu-id="2cd9d-246">`--urls` command-line argument.</span></span>
* <span data-ttu-id="2cd9d-247">Den Hostkonfigurationsschlüssel `urls`</span><span class="sxs-lookup"><span data-stu-id="2cd9d-247">`urls` host configuration key.</span></span>
* <span data-ttu-id="2cd9d-248">Die Erweiterungsmethode `UseUrls`</span><span class="sxs-lookup"><span data-stu-id="2cd9d-248">`UseUrls` extension method.</span></span>

<span data-ttu-id="2cd9d-249">Der Wert, der mit diesen Ansätzen angegeben wird, kann mindestens ein HTTP- oder HTTPS-Endpunkt sein (HTTPS wenn ein Standardzertifikat verfügbar ist).</span><span class="sxs-lookup"><span data-stu-id="2cd9d-249">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="2cd9d-250">Konfigurieren Sie den Wert als eine durch Semikolons getrennte Liste (z.B. `"Urls": "http://localhost:8000;http://localhost:8001"`).</span><span class="sxs-lookup"><span data-stu-id="2cd9d-250">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="2cd9d-251">Weitere Informationen zu diesen Ansätzen finden Sie unter [Server-URLs](xref:fundamentals/host/web-host#server-urls) und [Außerkraftsetzen der Konfiguration](xref:fundamentals/host/web-host#override-configuration).</span><span class="sxs-lookup"><span data-stu-id="2cd9d-251">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="2cd9d-252">Ein Entwicklungszertifikat wird erstellt:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-252">A development certificate is created:</span></span>

* <span data-ttu-id="2cd9d-253">Wenn das [.NET Core SDK](/dotnet/core/sdk) installiert wird.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-253">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="2cd9d-254">Das [dev-cert-Tool](xref:aspnetcore-2.1#https) wird zum Erstellen eines Zertifikats verwendet.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-254">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="2cd9d-255">Einige Browser erfordern, dass Sie die explizite Berechtigung erteilen, dem lokalen Entwicklungszertifikat zu vertrauen.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-255">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="2cd9d-256">Projektvorlagen konfigurieren Apps, damit sie standardmäßig auf HTTPS ausgeführt werden und die [HTTPS-Umleitung und HSTS-Unterstützung](xref:security/enforcing-ssl) enthalten.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-256">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="2cd9d-257">Rufen Sie die <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*>- oder <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*>-Methode unter <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> auf, um URL-Präfixe und Ports für Kestrel zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-257">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="2cd9d-258">`UseUrls`, das Befehlszeilenargument `--urls`, der Hostkonfigurationsschlüssel `urls` und die Umgebungsvariable `ASPNETCORE_URLS` funktionieren ebenfalls, verfügen jedoch über Einschränkungen, die im Verlauf dieses Abschnitts erläutert werden (Ein Standardzertifikat muss für die HTTPS-Endpunktkonfiguration verfügbar sein).</span><span class="sxs-lookup"><span data-stu-id="2cd9d-258">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="2cd9d-259">`KestrelServerOptions`-Konfiguration:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-259">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="2cd9d-260">ConfigureEndpointDefaults(Action\<ListenOptions>)</span><span class="sxs-lookup"><span data-stu-id="2cd9d-260">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="2cd9d-261">Gibt die Konfiguration von `Action` zum Ausführen von jedem angegebenen Endpunkt an.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-261">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="2cd9d-262">Mehrmalige Aufrufe von `ConfigureEndpointDefaults` ersetzen vorherige Instanzen von `Action` mit der zuletzt angegebenen `Action`.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-262">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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
> <span data-ttu-id="2cd9d-263">Auf Endpunkte, die durch Aufrufen von <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **vor** dem Aufrufen von <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> erstellt werden, werden die Standardwerte nicht angewendet.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-263">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> won't have the defaults applied.</span></span>

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="2cd9d-264">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span><span class="sxs-lookup"><span data-stu-id="2cd9d-264">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="2cd9d-265">Gibt die Konfiguration von `Action` zum Ausführen von jedem HTTPS-Endpunkt an.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-265">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="2cd9d-266">Mehrmalige Aufrufe von `ConfigureHttpsDefaults` ersetzen vorherige Instanzen von `Action` mit der zuletzt angegebenen `Action`.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-266">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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
> <span data-ttu-id="2cd9d-267">Auf Endpunkte, die durch Aufrufen von <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **vor** dem Aufrufen von <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> erstellt werden, werden die Standardwerte nicht angewendet.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-267">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>

### <a name="configureiconfiguration"></a><span data-ttu-id="2cd9d-268">Configure(IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="2cd9d-268">Configure(IConfiguration)</span></span>

<span data-ttu-id="2cd9d-269">Erstellt ein Konfigurationsladeprogramm für das Einrichten von Kestrel, was <xref:Microsoft.Extensions.Configuration.IConfiguration> als Eingabe erfordert.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-269">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="2cd9d-270">Die Konfiguration muss auf den Konfigurationsabschnitt für Kestrel festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-270">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="2cd9d-271">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="2cd9d-271">ListenOptions.UseHttps</span></span>

<span data-ttu-id="2cd9d-272">Konfiguriert Kestrel zur Verwendung von HTTPS.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-272">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="2cd9d-273">`ListenOptions.UseHttps`-Erweiterungen:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-273">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="2cd9d-274">`UseHttps`: Hiermit wird Kestrel zur Verwendung von HTTPS mit dem Standardzertifikat konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-274">`UseHttps`: Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="2cd9d-275">Löst eine Ausnahme aus, wenn kein Standardzertifikat konfiguriert ist.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-275">Throws an exception if no default certificate is configured.</span></span>
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

<span data-ttu-id="2cd9d-276">`ListenOptions.UseHttps`-Parameter:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-276">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="2cd9d-277">`filename` entspricht dem Pfad und Dateinamen einer Zertifikatdatei relativ zu dem Verzeichnis, das die Inhaltsdateien der App enthält.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-277">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="2cd9d-278">`password` ist das für den Zugriff auf die X.509-Zertifikatsdaten erforderliche Kennwort.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-278">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="2cd9d-279">`configureOptions` ist eine `Action` zum Konfigurieren von `HttpsConnectionAdapterOptions`.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-279">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="2cd9d-280">Gibt `ListenOptions` zurück.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-280">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="2cd9d-281">`storeName` ist der Zertifikatspeicher, aus dem das Zertifikat geladen wird.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-281">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="2cd9d-282">`subject` ist der Name des Antragstellers für das Zertifikat.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-282">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="2cd9d-283">`allowInvalid` gibt an, ob ungültige Zertifikate berücksichtigt werden sollten, z.B. selbstsignierte Zertifikate.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-283">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="2cd9d-284">`location` ist der Speicherort, aus dem das Zertifikat geladen wird.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-284">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="2cd9d-285">`serverCertificate` ist das X.509-Zertifikat.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-285">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="2cd9d-286">In der Produktion muss HTTPS explizit konfiguriert sein.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-286">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="2cd9d-287">Zumindest muss ein Standardzertifikat angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-287">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="2cd9d-288">Die im Folgenden beschriebenen unterstützten Konfigurationen:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-288">Supported configurations described next:</span></span>

* <span data-ttu-id="2cd9d-289">Keine Konfiguration</span><span class="sxs-lookup"><span data-stu-id="2cd9d-289">No configuration</span></span>
* <span data-ttu-id="2cd9d-290">Ersetzen des Standardzertifikats aus der Konfiguration</span><span class="sxs-lookup"><span data-stu-id="2cd9d-290">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="2cd9d-291">Ändern des Standards im Code</span><span class="sxs-lookup"><span data-stu-id="2cd9d-291">Change the defaults in code</span></span>

<span data-ttu-id="2cd9d-292">*Keine Konfiguration*</span><span class="sxs-lookup"><span data-stu-id="2cd9d-292">*No configuration*</span></span>

<span data-ttu-id="2cd9d-293">Kestrel überwacht `http://localhost:5000` und `https://localhost:5001` (wenn ein Standardzertifikat verfügbar ist).</span><span class="sxs-lookup"><span data-stu-id="2cd9d-293">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="2cd9d-294">*Ersetzen des Standardzertifikats aus der Konfiguration*</span><span class="sxs-lookup"><span data-stu-id="2cd9d-294">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="2cd9d-295">`CreateDefaultBuilder` ruft `Configure(context.Configuration.GetSection("Kestrel"))` standardmäßig zum Laden der Kestrel-Konfiguration auf.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-295">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="2cd9d-296">Ein Standardkonfigurationsschema für HTTPS-App-Einstellungen ist für Kestrel verfügbar.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-296">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="2cd9d-297">Konfigurieren Sie mehrere Endpunkte, einschließlich der zu verwendenden URLs und Zertifikate aus einer Datei auf dem Datenträger oder einem Zertifikatspeicher.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-297">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="2cd9d-298">Die Vorgehensweise im folgenden *appsettings.json*-Beispiel:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-298">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="2cd9d-299">Legen Sie **AllowInvalid** auf `true` fest, um die Verwendung von ungültigen Zertifikaten zu erlauben (z.B. selbstsignierte Zertifikate).</span><span class="sxs-lookup"><span data-stu-id="2cd9d-299">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="2cd9d-300">Jeder HTTPS-Endpunkt, der kein Zertifikat angibt (im folgenden Beispiel **HttpsDefaultCert**), greift auf das unter **Zertifikate** > **Standard** festgelegte Zertifikat oder das Entwicklungszertifikat zurück.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-300">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

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

<span data-ttu-id="2cd9d-301">Alternativ zur Verwendung von **Pfad** und **Kennwort** für alle Zertifikatknoten können Sie das Zertifikat mithilfe von Zertifikatspeicherfeldern angeben.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-301">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="2cd9d-302">Das Zertifikat unter **Zertifikate** > **Standard** kann beispielweise wie folgt angegeben werden:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-302">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="2cd9d-303">Schema-Hinweise:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-303">Schema notes:</span></span>

* <span data-ttu-id="2cd9d-304">Bei den Namen der Endpunkte wird die Groß-/Kleinschreibung nicht beachtet.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-304">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="2cd9d-305">Zum Beispiel sind `HTTPS` und `Https` gültig.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-305">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="2cd9d-306">Der Parameter `Url` ist für jeden Endpunkt erforderlich.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-306">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="2cd9d-307">Das Format für diesen Parameter ist identisch mit dem allgemeinen Konfigurationsparameter `Urls`, mit der Ausnahme, dass er auf einen einzelnen Wert begrenzt ist.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-307">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="2cd9d-308">Diese Endpunkte ersetzen die in der allgemeinen `Urls`-Konfiguration festgelegten Endpunkte, anstatt zu ihnen hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-308">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="2cd9d-309">Endpunkte, die über `Listen` in Code definiert werden, werden den im Konfigurationsabschnitt definierten Endpunkten hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-309">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="2cd9d-310">Der `Certificate`-Abschnitt ist optional.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-310">The `Certificate` section is optional.</span></span> <span data-ttu-id="2cd9d-311">Wenn der `Certificate`-Abschnitt nicht angegeben ist, werden die in früheren Szenarios definierten Standardwerte verwendet.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-311">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="2cd9d-312">Wenn keine Standardwerte verfügbar sind, löst der Server eine Ausnahme aus und startet nicht.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-312">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="2cd9d-313">Der `Certificate`-Abschnitt unterstützt die Zertifikate **Pfad**&ndash;**Kennwort** und **Subject**&ndash;**Store** (Antragsteller–Speicher).</span><span class="sxs-lookup"><span data-stu-id="2cd9d-313">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="2cd9d-314">Auf diese Weise kann eine beliebige Anzahl von Endpunkten definiert werden, solange Sie keine Portkonflikte verursachen.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-314">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="2cd9d-315">`options.Configure(context.Configuration.GetSection("{SECTION}"))` gibt `KestrelConfigurationLoader` mit der Methode `.Endpoint(string name, listenOptions => { })` zurück, die dazu verwendet werden kann, die Einstellungen eines Endpunkts zu ergänzen:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-315">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

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

<span data-ttu-id="2cd9d-316">Auf `KestrelServerOptions.ConfigurationLoader` kann direkt zugegriffen werden, um die Iteration auf dem vorhandenen Ladeprogramm fortzusetzen, etwa auf dem von <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> bereitgestellten Ladeprogramm.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-316">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="2cd9d-317">Der Konfigurationsabschnitt ist für jeden Endpunkt in den Optionen der Methode `Endpoint` verfügbar, sodass benutzerdefinierte Einstellungen gelesen werden können.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-317">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="2cd9d-318">Mehrere Konfigurationen können durch erneutes Aufrufen von `options.Configure(context.Configuration.GetSection("{SECTION}"))` mit einem anderen Abschnitt geladen werden.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-318">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="2cd9d-319">Sofern `Load` nicht in einer vorherigen Instanz explizit aufgerufen wird, wird nur die letzte Konfiguration verwendet.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-319">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="2cd9d-320">Das Metapaket ruft `Load` nicht auf, sodass der Abschnitt mit der Standardkonfiguration ersetzt werden kann.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-320">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="2cd9d-321">`KestrelConfigurationLoader` spiegelt die API-Familie `Listen` von `KestrelServerOptions` als `Endpoint`-Überladungen, weshalb Code und Konfigurationsendpunkte am selben Ort konfiguriert werden können.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-321">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="2cd9d-322">Diese Überladungen verwenden keine Namen und nutzen nur Standardeinstellungen aus der Konfiguration.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-322">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="2cd9d-323">*Ändern des Standards im Code*</span><span class="sxs-lookup"><span data-stu-id="2cd9d-323">*Change the defaults in code*</span></span>

<span data-ttu-id="2cd9d-324">`ConfigureEndpointDefaults` und `ConfigureHttpsDefaults` können zum Ändern der Standardeinstellungen für `ListenOptions` und `HttpsConnectionAdapterOptions` verwendet werden, einschließlich der Standardzertifikate, die im vorherigen Szenario festgelegt wurden.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-324">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="2cd9d-325">`ConfigureEndpointDefaults` und `ConfigureHttpsDefaults` sollten aufgerufen werden, bevor Endpunkte konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-325">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

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

<span data-ttu-id="2cd9d-326">*Kestrel-Unterstützung für SNI*</span><span class="sxs-lookup"><span data-stu-id="2cd9d-326">*Kestrel support for SNI*</span></span>

<span data-ttu-id="2cd9d-327">Die [Servernamensanzeige (SNI)](https://tools.ietf.org/html/rfc6066#section-3) kann zum Hosten mehrerer Domänen auf der gleichen IP-Adresse und dem gleichen Port verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-327">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="2cd9d-328">Damit die Servernamensanzeige funktioniert, sendet der Client während des TLS-Handshakes den Hostnamen für die sichere Sitzung an den Server, sodass der Server das richtige Zertifikat bereitstellen kann.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-328">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="2cd9d-329">Der Client verwendet das beigestellte Zertifikat für die verschlüsselte Kommunikation mit dem Server während der sicheren Sitzung nach dem TLS-Handshake.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-329">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="2cd9d-330">Kestrel unterstützt die Servernamensanzeige über den `ServerCertificateSelector`-Rückruf.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-330">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="2cd9d-331">Der Rückruf wird für jede Verbindung einmal aufgerufen, um der App zu ermöglichen, den Hostnamen zu überprüfen und das entsprechende Zertifikat auszuwählen.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-331">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="2cd9d-332">Für die Unterstützung der Servernamensanzeige benötigen Sie Folgendes:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-332">SNI support requires:</span></span>

* <span data-ttu-id="2cd9d-333">Wird auf dem Zielframework `netcoreapp2.1` oder höher ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-333">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="2cd9d-334">In `net461` oder höher, wird der Rückruf aufgerufen, `name` ist aber immer `null`.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-334">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="2cd9d-335">`name` ist auch `null`, wenn der Client den Hostnamenparameter nicht im TLS-Handshake angibt.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-335">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="2cd9d-336">Alle Websites werden in derselben Kestrel-Instanz ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-336">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="2cd9d-337">Kestrel unterstützt ohne Reverseproxy keine gemeinsame IP-Adresse und keinen gemeinsamen Port für mehrere Instanzen.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-337">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

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

### <a name="connection-logging"></a><span data-ttu-id="2cd9d-338">Verbindungsprotokollierung</span><span class="sxs-lookup"><span data-stu-id="2cd9d-338">Connection logging</span></span>

<span data-ttu-id="2cd9d-339">Rufen Sie <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> auf, um Protokolle auf Debugebene für die Kommunikation auf Byteebene für eine Verbindung auszugeben.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-339">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="2cd9d-340">Die Verbindungsprotokollierung ist beim Beheben von Problemen bei der Low-Level-Kommunikation hilfreich, wie z. B. bei der TLS-Verschlüsselung und bei Proxys.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-340">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="2cd9d-341">Wenn `UseConnectionLogging` vor `UseHttps` platziert wird, wird der verschlüsselte Datenverkehr protokolliert.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-341">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="2cd9d-342">Wenn `UseConnectionLogging` nach `UseHttps` platziert wird, wird der entschlüsselte Datenverkehr protokolliert.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-342">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="2cd9d-343">Binden an einen TCP-Socket</span><span class="sxs-lookup"><span data-stu-id="2cd9d-343">Bind to a TCP socket</span></span>

<span data-ttu-id="2cd9d-344">Die <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*>-Methode wird an ein TCP-Socket gebunden, und ein Lambdaausdruck einer Option lässt die Konfiguration des X.509-Zertifikats zu:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-344">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=12-18)]

<span data-ttu-id="2cd9d-345">Im Beispiel wird HTTPS für einen Endpunkt mit <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions> konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-345">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="2cd9d-346">Verwenden Sie die gleiche API zum Konfigurieren anderer Kestrel-Einstellungen für bestimmte Endpunkte.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-346">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="2cd9d-347">Binden an einen Unix-Socket</span><span class="sxs-lookup"><span data-stu-id="2cd9d-347">Bind to a Unix socket</span></span>

<span data-ttu-id="2cd9d-348">Wie in diesem Beispiel dargestellt, lauschen Sie an einem Unix-Socket mit <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*>, um eine verbesserte Leistung mit Nginx zu erzielen:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-348">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

* <span data-ttu-id="2cd9d-349">Legen Sie den Eintrag `server` > `location` > `proxy_pass` in der Nginx-Konfigurationsdatei auf `http://unix:/tmp/{KESTREL SOCKET}:/;` fest.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-349">In the Nginx configuration file, set the `server` > `location` > `proxy_pass` entry to `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span></span> <span data-ttu-id="2cd9d-350">`{KESTREL SOCKET}` ist der Name des für <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> bereitgestellten Socket (zum Beispiel `kestrel-test.sock` im vorherigen Beispiel).</span><span class="sxs-lookup"><span data-stu-id="2cd9d-350">`{KESTREL SOCKET}` is the name of the socket provided to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (for example, `kestrel-test.sock` in the preceding example).</span></span>
* <span data-ttu-id="2cd9d-351">Stellen Sie sicher, dass der Socket von Nginx beschreibbar ist (z. B. `chmod go+w /tmp/kestrel-test.sock`).</span><span class="sxs-lookup"><span data-stu-id="2cd9d-351">Ensure that the socket is writeable by Nginx (for example, `chmod go+w /tmp/kestrel-test.sock`).</span></span>

### <a name="port-0"></a><span data-ttu-id="2cd9d-352">Port 0</span><span class="sxs-lookup"><span data-stu-id="2cd9d-352">Port 0</span></span>

<span data-ttu-id="2cd9d-353">Wenn die Portnummer `0` angegeben wird, wird Kestrel dynamisch an einen verfügbaren Port gebunden.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-353">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="2cd9d-354">Im folgenden Beispiel wird veranschaulicht, wie bestimmt werden kann, für welchen Port Kestrel zur Laufzeit eine Bindung erstellt hat:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-354">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="2cd9d-355">Wenn die App ausgeführt wird, gibt das Ausgabefenster der Konsole den dynamischen Port an, über den die App erreicht werden kann:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-355">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="2cd9d-356">Einschränkungen</span><span class="sxs-lookup"><span data-stu-id="2cd9d-356">Limitations</span></span>

<span data-ttu-id="2cd9d-357">Konfigurieren Sie Endpunkte mithilfe der folgenden Ansätze:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-357">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="2cd9d-358">Befehlszeilenargument `--urls`</span><span class="sxs-lookup"><span data-stu-id="2cd9d-358">`--urls` command-line argument</span></span>
* <span data-ttu-id="2cd9d-359">Hostkonfigurationsschlüssel `urls`</span><span class="sxs-lookup"><span data-stu-id="2cd9d-359">`urls` host configuration key</span></span>
* <span data-ttu-id="2cd9d-360">Umgebungsvariable `ASPNETCORE_URLS`</span><span class="sxs-lookup"><span data-stu-id="2cd9d-360">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="2cd9d-361">Diese Methoden sind nützlich, wenn Ihr Code mit anderen Servern als Kestrel funktionieren soll.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-361">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="2cd9d-362">Beachten Sie jedoch die folgenden Einschränkungen:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-362">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="2cd9d-363">HTTPS kann nicht mit diesen Ansätzen verwendet werden, außer ein Standardzertifikat wird in der HTTPS-Endpunktkonfiguration angegeben (z.B. wenn Sie wie zuvor in diesem Artikel gezeigt die `KestrelServerOptions`-Konfiguration oder eine Konfigurationsdatei verwenden).</span><span class="sxs-lookup"><span data-stu-id="2cd9d-363">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="2cd9d-364">Wenn die Ansätze `Listen` und `UseUrls` gleichzeitig verwendet werden, überschreiben die `Listen`-Endpunkte die `UseUrls`-Endpunkte.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-364">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="2cd9d-365">IIS-Endpunktkonfiguration</span><span class="sxs-lookup"><span data-stu-id="2cd9d-365">IIS endpoint configuration</span></span>

<span data-ttu-id="2cd9d-366">Bei der Verwendung von IIS werden die URL-Bindungen für IIS-Überschreibungsbindungen durch `Listen` oder `UseUrls` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-366">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="2cd9d-367">Weitere Informationen finden Sie im Artikel [ASP.NET Core-Modul](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="2cd9d-367">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

### <a name="listenoptionsprotocols"></a><span data-ttu-id="2cd9d-368">ListenOptions.Protocols</span><span class="sxs-lookup"><span data-stu-id="2cd9d-368">ListenOptions.Protocols</span></span>

<span data-ttu-id="2cd9d-369">Die `Protocols`-Eigenschaft richtet die HTTP-Protokolle (`HttpProtocols`) ein, die für einen Verbindungsendpunkt oder für den Server aktiviert werden.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-369">The `Protocols` property establishes the HTTP protocols (`HttpProtocols`) enabled on a connection endpoint or for the server.</span></span> <span data-ttu-id="2cd9d-370">Weisen Sie der `Protocols`-Eigenschaft einen Wert aus der `HttpProtocols`-Enumeration zu.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-370">Assign a value to the `Protocols` property from the `HttpProtocols` enum.</span></span>

| <span data-ttu-id="2cd9d-371">`HttpProtocols`-Enumerationswert</span><span class="sxs-lookup"><span data-stu-id="2cd9d-371">`HttpProtocols` enum value</span></span> | <span data-ttu-id="2cd9d-372">Zulässiges Verbindungsprotokoll</span><span class="sxs-lookup"><span data-stu-id="2cd9d-372">Connection protocol permitted</span></span> |
| -------------------------- | ----------------------------- |
| `Http1`                    | <span data-ttu-id="2cd9d-373">Nur HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-373">HTTP/1.1 only.</span></span> <span data-ttu-id="2cd9d-374">Kann mit oder ohne TLS verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-374">Can be used with or without TLS.</span></span> |
| `Http2`                    | <span data-ttu-id="2cd9d-375">Nur HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-375">HTTP/2 only.</span></span> <span data-ttu-id="2cd9d-376">Kann nur ohne TLS verwendet werden, wenn der Client einen [Vorabkenntnis-Modus](https://tools.ietf.org/html/rfc7540#section-3.4) unterstützt.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-376">May be used without TLS only if the client supports a [Prior Knowledge mode](https://tools.ietf.org/html/rfc7540#section-3.4).</span></span> |
| `Http1AndHttp2`            | <span data-ttu-id="2cd9d-377">HTTP/1.1 und HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-377">HTTP/1.1 and HTTP/2.</span></span> <span data-ttu-id="2cd9d-378">Für HTTP/2 muss der Client HTTP/2 im TLS [ALPN-Handshake (Application-Layer Protocol Negotiation)](https://tools.ietf.org/html/rfc7301#section-3) auswählen, andernfalls wird standardmäßig eine HTTP/1.1 verwendet.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-378">HTTP/2 requires the client to select HTTP/2 in the TLS [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) handshake; otherwise, the connection defaults to HTTP/1.1.</span></span> |

<span data-ttu-id="2cd9d-379">Der Standardwert von `ListenOptions.Protocols` ist für alle Endpunkte `HttpProtocols.Http1AndHttp2`.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-379">The default `ListenOptions.Protocols` value for any endpoint is `HttpProtocols.Http1AndHttp2`.</span></span>

<span data-ttu-id="2cd9d-380">TLS-Einschränkungen für HTTP/2:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-380">TLS restrictions for HTTP/2:</span></span>

* <span data-ttu-id="2cd9d-381">TLS Version 1.2 oder höher</span><span class="sxs-lookup"><span data-stu-id="2cd9d-381">TLS version 1.2 or later</span></span>
* <span data-ttu-id="2cd9d-382">Erneute Aushandlung deaktiviert</span><span class="sxs-lookup"><span data-stu-id="2cd9d-382">Renegotiation disabled</span></span>
* <span data-ttu-id="2cd9d-383">Komprimierung deaktiviert</span><span class="sxs-lookup"><span data-stu-id="2cd9d-383">Compression disabled</span></span>
* <span data-ttu-id="2cd9d-384">Minimale Größen für Austausch von flüchtigen Schlüsseln:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-384">Minimum ephemeral key exchange sizes:</span></span>
  * <span data-ttu-id="2cd9d-385">ECDHE (Elliptic Curve Diffie-Hellman) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack;: mindestens 224 Bits</span><span class="sxs-lookup"><span data-stu-id="2cd9d-385">Elliptic curve Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack;: 224 bits minimum</span></span>
  * <span data-ttu-id="2cd9d-386">DHE (Finite Field Diffie-Hellman) &lbrack;`TLS12`&rbrack;: mindestens 2048 Bits</span><span class="sxs-lookup"><span data-stu-id="2cd9d-386">Finite field Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack;: 2048 bits minimum</span></span>
* <span data-ttu-id="2cd9d-387">Verschlüsselungssammlung nicht verboten</span><span class="sxs-lookup"><span data-stu-id="2cd9d-387">Cipher suite not prohibited.</span></span> 

<span data-ttu-id="2cd9d-388">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; mit der elliptischen P-256-Kurve &lbrack;`FIPS186`&rbrack; wird standardmäßig unterstützt.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-388">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; with the P-256 elliptic curve &lbrack;`FIPS186`&rbrack; is supported by default.</span></span>

<span data-ttu-id="2cd9d-389">Das folgende Beispiel erlaubt HTTP/1.1- und HTTP/2-Verbindungen an Port 8000.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-389">The following example permits HTTP/1.1 and HTTP/2 connections on port 8000.</span></span> <span data-ttu-id="2cd9d-390">Die Verbindungen werden durch TLS mit einem bereitgestellten Zertifikat geschützt:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-390">Connections are secured by TLS with a supplied certificate:</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
    });
});
```

<span data-ttu-id="2cd9d-391">Verwenden Sie Verbindungsmiddleware, um TLS-Handshakes auf Verbindungsbasis für bestimmte Verschlüsselungen zu filtern, falls erforderlich.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-391">Use Connection Middleware to filter TLS handshakes on a per-connection basis for specific ciphers if required.</span></span>

<span data-ttu-id="2cd9d-392">Im folgenden Beispiel wird <xref:System.NotSupportedException> für jeden Verschlüsselungsalgorithmus ausgelöst, der von der App nicht unterstützt wird.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-392">The following example throws <xref:System.NotSupportedException> for any cipher algorithm that the app doesn't support.</span></span> <span data-ttu-id="2cd9d-393">Alternativ können Sie [ITlsHandshakeFeature.CipherAlgorithm](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm) definieren und mit einer Liste zulässiger Verschlüsselungssammlungen vergleichen.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-393">Alternatively, define and compare [ITlsHandshakeFeature.CipherAlgorithm](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm) to a list of acceptable cipher suites.</span></span>

<span data-ttu-id="2cd9d-394">Es wird keine Verschlüsselung mit einem [CipherAlgorithmType.Null](xref:System.Security.Authentication.CipherAlgorithmType)-Verschlüsselungsalgorithmus verwendet.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-394">No encryption is used with a [CipherAlgorithmType.Null](xref:System.Security.Authentication.CipherAlgorithmType) cipher algorithm.</span></span>

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

<span data-ttu-id="2cd9d-395">Die Verbindungsfilterung kann auch über einen <xref:Microsoft.AspNetCore.Connections.IConnectionBuilder>-Lambdaausdruck konfiguriert werden:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-395">Connection filtering can also be configured via an <xref:Microsoft.AspNetCore.Connections.IConnectionBuilder> lambda:</span></span>

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

<span data-ttu-id="2cd9d-396">Unter Linux kann <xref:System.Net.Security.CipherSuitesPolicy> zum Filtern von TLS-Handshakes auf Verbindungsbasis verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-396">On Linux, <xref:System.Net.Security.CipherSuitesPolicy> can be used to filter TLS handshakes on a per-connection basis:</span></span>

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

<span data-ttu-id="2cd9d-397">*Festlegen des Protokolls aus der Konfiguration*</span><span class="sxs-lookup"><span data-stu-id="2cd9d-397">*Set the protocol from configuration*</span></span>

<span data-ttu-id="2cd9d-398">`CreateDefaultBuilder` ruft `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` standardmäßig zum Laden der Kestrel-Konfiguration auf.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-398">`CreateDefaultBuilder` calls `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span>

<span data-ttu-id="2cd9d-399">Das folgende *appsettings.json*-Beispiel richtet HTTP/1.1 als Standardverbindungsprotokoll für alle Endpunkte ein:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-399">The following *appsettings.json* example establishes HTTP/1.1 as the default connection protocol for all endpoints:</span></span>

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1"
    }
  }
}
```

<span data-ttu-id="2cd9d-400">Das folgende *appsettings.json*-Beispiel richtet HTTP/1.1 als Verbindungsprotokoll für einen bestimmten Endpunkt ein:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-400">The following *appsettings.json* example establishes the HTTP/1.1 connection protocol for a specific endpoint:</span></span>

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

<span data-ttu-id="2cd9d-401">Protokolle, die in Code angegeben werden, setzen Werte außer Kraft, der durch die Konfiguration festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-401">Protocols specified in code override values set by configuration.</span></span>

## <a name="transport-configuration"></a><span data-ttu-id="2cd9d-402">Transportkonfiguration</span><span class="sxs-lookup"><span data-stu-id="2cd9d-402">Transport configuration</span></span>

<span data-ttu-id="2cd9d-403">Für Projekte, die den Einsatz von Libuv (<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>) erfordern:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-403">For projects that require the use of Libuv (<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>):</span></span>

* <span data-ttu-id="2cd9d-404">Fügen Sie für das [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/)-Paket eine Abhängigkeit auf die Projektdatei der App hinzu:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-404">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

   ```xml
   <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                     Version="{VERSION}" />
   ```

* <span data-ttu-id="2cd9d-405"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> auf `IWebHostBuilder` aufrufen:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-405">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> on the `IWebHostBuilder`:</span></span>

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

### <a name="url-prefixes"></a><span data-ttu-id="2cd9d-406">URL-Präfixe</span><span class="sxs-lookup"><span data-stu-id="2cd9d-406">URL prefixes</span></span>

<span data-ttu-id="2cd9d-407">Bei der Verwendung von `UseUrls`, dem Befehlszeilenargument `--urls`, dem Hostkonfigurationsschlüssel `urls` oder der Umgebungsvariable `ASPNETCORE_URLS` können die URL-Präfixe in den folgenden Formaten vorliegen.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-407">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="2cd9d-408">Nur HTTP-URL-Präfixe sind gültig.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-408">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="2cd9d-409">Kestrel unterstützt HTTPS nicht beim Konfigurieren von URL-Bindungen mit `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-409">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="2cd9d-410">IPv4-Adresse mit Portnummer</span><span class="sxs-lookup"><span data-stu-id="2cd9d-410">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="2cd9d-411">Bei `0.0.0.0` handelt es sich um einen Sonderfall, für den eine Bindung an alle IPv4-Adressen erfolgt.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-411">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="2cd9d-412">IPv6-Adresse mit Portnummer</span><span class="sxs-lookup"><span data-stu-id="2cd9d-412">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="2cd9d-413">`[::]` stellt das Äquivalent von IPv6 zu `0.0.0.0` für IPv4 dar.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-413">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="2cd9d-414">Hostname mit Portnummer</span><span class="sxs-lookup"><span data-stu-id="2cd9d-414">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="2cd9d-415">Hostnamen, `*` und `+` sind nicht spezifisch.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-415">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="2cd9d-416">Alle Elemente, die nicht als gültige IP-Adresse oder `localhost` erkannt werden, werden an alle IPv4- und IPv6-IP-Adressen gebunden.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-416">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="2cd9d-417">Verwenden Sie [HTTP.sys](xref:fundamentals/servers/httpsys) oder einen Reverseproxyserver zum Binden verschiedener Hostnamen an verschiedene ASP.NET Core-Apps auf demselben Port, z.B. IIS, Nginx oder Apache.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-417">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="2cd9d-418">Das Hosting in einer Reverseproxykonfiguration erfordert [Hostfilterung](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="2cd9d-418">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="2cd9d-419">`localhost`-Hostname mit Portnummer oder Loopback-IP mit Portnummer</span><span class="sxs-lookup"><span data-stu-id="2cd9d-419">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="2cd9d-420">Wenn `localhost` angegeben ist, versucht Kestrel, eine Bindung zu IPv4- und IPv6-Loopback-Schnittstellen zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-420">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="2cd9d-421">Wenn der erforderliche Port von einem anderen Dienst auf einer der Loopback-Schnittstellen verwendet wird, tritt beim Starten von Kestrel ein Fehler auf.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-421">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="2cd9d-422">Wenn eine der Loopback-Schnittstellen aus anderen Gründen nicht verfügbar ist (meistens durch die fehlende Unterstützung von IPv6), protokolliert Kestrel eine Warnung.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-422">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="2cd9d-423">Host-Filterung</span><span class="sxs-lookup"><span data-stu-id="2cd9d-423">Host filtering</span></span>

<span data-ttu-id="2cd9d-424">Obwohl Kestrel die Konfiguration basierend auf Präfixe wie `http://example.com:5000` unterstützt, ignoriert Kestrel den Hostnamen weitgehend.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-424">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="2cd9d-425">Der Host `localhost` ist ein Sonderfall, der für die Bindung an Loopback-Adressen verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-425">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="2cd9d-426">Jeder Host, der keine explizite IP-Adresse ist, wird an alle öffentlichen IP-Adressen gebunden.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-426">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="2cd9d-427">`Host`-Header werden nicht überprüft.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-427">`Host` headers aren't validated.</span></span>

<span data-ttu-id="2cd9d-428">Verwenden Sie Middleware zum Filtern von Hosts, um dieses Problem zu umgehen.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-428">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="2cd9d-429">Die Middleware zum Filtern von Hosts wird durch das [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering)-Paket bereitgestellt, das implizit für ASP.NET Core-Apps zur Verfügung steht.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-429">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is implicitly provided for ASP.NET Core apps.</span></span> <span data-ttu-id="2cd9d-430">Die Middleware wird von <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> hinzugefügt, wodurch <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*> aufgerufen wird:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-430">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="2cd9d-431">Die Middleware zum Filtern von Hosts ist standardmäßig deaktiviert.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-431">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="2cd9d-432">Wenn Sie die Middleware aktivieren möchten, definieren Sie einen `AllowedHosts`-Schlüssel in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-432">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="2cd9d-433">Der Wert ist eine durch Semikolons getrennte Liste von Hostnamen ohne Portnummern:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-433">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="2cd9d-434">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-434">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="2cd9d-435">[Middleware für weitergeleitete Header](xref:host-and-deploy/proxy-load-balancer) hat auch eine <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts>-Option.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-435">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="2cd9d-436">Middleware für weitergeleitete Header und Middleware zum Filtern von Hosts besitzen ähnliche Funktionen für unterschiedliche Szenarios.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-436">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="2cd9d-437">Legen Sie `AllowedHosts` mit Middleware für weitergeleitete Header fest, wenn der `Host`-Header beim Weiterleiten von Anforderungen mit einem Reverseproxyserver oder einem Lastenausgleichsmodul nicht beibehalten wird.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-437">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="2cd9d-438">Legen Sie `AllowedHosts` mit Middleware zum Filtern von Hosts fest, wenn Kestrel als öffentlicher Edgeserver verwendet oder der `Host`-Header direkt weitergeleitet wird.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-438">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="2cd9d-439">Weitere Informationen zu Middleware für weitergeleitete Header finden Sie unter <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-439">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="2cd9d-440">Einführung in Kestrel, dem plattformübergreifenden [Webserver für ASP.NET Core](xref:fundamentals/servers/index).</span><span class="sxs-lookup"><span data-stu-id="2cd9d-440">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="2cd9d-441">Kestrel ist der Webserver, der standardmäßig in ASP.NET Core-Projektvorlagen enthalten ist.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-441">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="2cd9d-442">Kestrel unterstützt die folgenden Szenarios:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-442">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="2cd9d-443">HTTPS</span><span class="sxs-lookup"><span data-stu-id="2cd9d-443">HTTPS</span></span>
* <span data-ttu-id="2cd9d-444">Nicht transparente Upgrades, die zum Aktivieren von [WebSockets](https://github.com/aspnet/websockets) verwendet werden</span><span class="sxs-lookup"><span data-stu-id="2cd9d-444">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="2cd9d-445">Unix-Sockets für eine hohe Leistung im Hintergrund von Nginx</span><span class="sxs-lookup"><span data-stu-id="2cd9d-445">Unix sockets for high performance behind Nginx</span></span>
* <span data-ttu-id="2cd9d-446">HTTP/2 (außer unter macOS&dagger;)</span><span class="sxs-lookup"><span data-stu-id="2cd9d-446">HTTP/2 (except on macOS&dagger;)</span></span>

<span data-ttu-id="2cd9d-447">&dagger;HTTP/2 wird unter macOS in einem zukünftigen Release unterstützt.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-447">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>

<span data-ttu-id="2cd9d-448">Kestrel wird auf allen Plattformen und für alle Versionen unterstützt, die .NET Core unterstützt.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-448">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="2cd9d-449">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="2cd9d-449">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="http2-support"></a><span data-ttu-id="2cd9d-450">HTTP/2-Unterstützung</span><span class="sxs-lookup"><span data-stu-id="2cd9d-450">HTTP/2 support</span></span>

<span data-ttu-id="2cd9d-451">[HTTP/2](https://httpwg.org/specs/rfc7540.html) ist für ASP.NET Core-Apps verfügbar, wenn die folgenden Basisanforderungen erfüllt sind:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-451">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is available for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="2cd9d-452">Betriebssystem&dagger;</span><span class="sxs-lookup"><span data-stu-id="2cd9d-452">Operating system&dagger;</span></span>
  * <span data-ttu-id="2cd9d-453">Windows Server 2016/Windows 10 oder höher&Dagger;</span><span class="sxs-lookup"><span data-stu-id="2cd9d-453">Windows Server 2016/Windows 10 or later&Dagger;</span></span>
  * <span data-ttu-id="2cd9d-454">Linux mit OpenSSL 1.0.2 oder höher (z.B. Ubuntu 16.04 oder höher)</span><span class="sxs-lookup"><span data-stu-id="2cd9d-454">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
* <span data-ttu-id="2cd9d-455">Zielframework: .NET Core 2.2 oder höher</span><span class="sxs-lookup"><span data-stu-id="2cd9d-455">Target framework: .NET Core 2.2 or later</span></span>
* <span data-ttu-id="2cd9d-456">[ALPN](https://tools.ietf.org/html/rfc7301#section-3)-Verbindung (Application-Layer Protocol Negotiation)</span><span class="sxs-lookup"><span data-stu-id="2cd9d-456">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="2cd9d-457">TLS 1.2-Verbindung oder höher</span><span class="sxs-lookup"><span data-stu-id="2cd9d-457">TLS 1.2 or later connection</span></span>

<span data-ttu-id="2cd9d-458">&dagger;HTTP/2 wird unter macOS in einem zukünftigen Release unterstützt.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-458">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>
<span data-ttu-id="2cd9d-459">&Dagger;Kestrel bietet eingeschränkte Unterstützung für HTTP/2 unter Windows Server 2012 R2 und Windows 8.1.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-459">&Dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="2cd9d-460">Die Unterstützung ist eingeschränkt, weil die Liste der unterstützten TLS-Verschlüsselungssammlungen unter diesen Betriebssystemen begrenzt ist.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-460">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="2cd9d-461">Zum Sichern von TLS-Verbindungen ist möglicherweise ein durch einen Elliptic Curve Digital Signature Algorithm (ECDSA) generiertes Zertifikat erforderlich.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-461">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

<span data-ttu-id="2cd9d-462">Wenn eine HTTP/2-Verbindung hergestellt wurde, meldet [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*)`HTTP/2`.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-462">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="2cd9d-463">HTTP/2 ist standardmäßig deaktiviert.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-463">HTTP/2 is disabled by default.</span></span> <span data-ttu-id="2cd9d-464">Weitere Informationen zur Konfiguration finden Sie in den Abschnitten [Kestrel-Optionen](#kestrel-options) und [ListenOptions.Protocols](#listenoptionsprotocols).</span><span class="sxs-lookup"><span data-stu-id="2cd9d-464">For more information on configuration, see the [Kestrel options](#kestrel-options) and [ListenOptions.Protocols](#listenoptionsprotocols) sections.</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="2cd9d-465">Verwenden von Kestrel mit einem Reverseproxy</span><span class="sxs-lookup"><span data-stu-id="2cd9d-465">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="2cd9d-466">Kestrel kann eigenständig oder mit einem *Reverseproxyserver* wie z.B. [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org) oder [Apache](https://httpd.apache.org/) verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-466">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="2cd9d-467">Ein Reverseproxyserver empfängt HTTP-Anforderungen aus dem Netzwerk und leitet diese an Kestrel weiter.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-467">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="2cd9d-468">Kestrel bei Verwendung als Webserver mit direkter Internetverbindung:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-468">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Kestrel kommuniziert direkt und ohne Reverseproxyserver mit dem Internet](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="2cd9d-470">Kestrel bei Verwendung in einer Reverseproxykonfiguration:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-470">Kestrel used in a reverse proxy configuration:</span></span>

![Kestrel kommuniziert indirekt mit dem Internet über einen Reverseproxyserver wie IIS, Nginx oder Apache](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="2cd9d-472">Jede der beiden Konfigurationen – mit oder ohne einen Reverseproxyserver – stellt eine unterstützte Hostingkonfiguration dar.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-472">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="2cd9d-473">Bei Verwendung als Edgeserver ohne Reverseproxyserver unterstützt Kestrel die gemeinsame Nutzung der gleichen IP-Adresse und des gleichen Ports durch mehrere Prozesse nicht.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-473">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="2cd9d-474">Wenn Kestrel für das Lauschen an einem Port konfiguriert ist, verarbeitet Kestrel den gesamten Datenverkehr für diesen Port unabhängig von den `Host`-Headern der Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-474">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="2cd9d-475">Ein Reverseproxy, der Ports freigeben kann, kann Anforderungen an Kestrel über eine eindeutige IP und einen eindeutigen Port weiterleiten.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-475">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="2cd9d-476">Auch wenn kein Reverseproxyserver erforderlich ist, kann die Verwendung eines solchen empfehlenswert sein.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-476">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="2cd9d-477">Für einen Reverseproxy gilt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-477">A reverse proxy:</span></span>

* <span data-ttu-id="2cd9d-478">Er kann die verfügbar gemachten öffentlichen Oberflächen der von ihm gehosteten Apps einschränken.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-478">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="2cd9d-479">Er stellt eine zusätzliche Ebene für Konfiguration und Schutz bereit.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-479">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="2cd9d-480">Er lässt sich besser in die vorhandene Infrastruktur integrieren.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-480">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="2cd9d-481">Er vereinfacht die Konfiguration von Lastenausgleich und sicherer Kommunikation (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="2cd9d-481">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="2cd9d-482">Nur der Reverseproxyserver erfordert ein X.509-Zertifikat, und dieser Server kann mit den Servern der App im internen Netzwerk über einfaches HTTP kommunizieren.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-482">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="2cd9d-483">Das Hosting in einer Reverseproxykonfiguration erfordert [Hostfilterung](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="2cd9d-483">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a><span data-ttu-id="2cd9d-484">Verwenden von Kestrel in ASP.NET Core-Apps</span><span class="sxs-lookup"><span data-stu-id="2cd9d-484">How to use Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="2cd9d-485">Das Paket [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) ist im Metapaket [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) enthalten.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-485">The [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="2cd9d-486">ASP.NET Core-Projektvorlagen verwenden Kestrel standardmäßig.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-486">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="2cd9d-487">Der Vorlagencode in *Program.cs* ruft <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> auf, wodurch <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> im Hintergrund aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-487">In *Program.cs*, the template code calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> behind the scenes.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=7)]

<span data-ttu-id="2cd9d-488">Weitere Informationen zum `CreateDefaultBuilder` und zum Erstellen des Hosts finden Sie im Abschnitt *Einrichten eines Hosts* von <xref:fundamentals/host/web-host#set-up-a-host>.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-488">For more information on `CreateDefaultBuilder` and building the host, see the *Set up a host* section of <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

<span data-ttu-id="2cd9d-489">Um zusätzliche Konfiguration nach dem Aufruf von `CreateDefaultBuilder` bereitzustellen, verwenden Sie `ConfigureKestrel`:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-489">To provide additional configuration after calling `CreateDefaultBuilder`, use `ConfigureKestrel`:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            // Set properties and call methods on serverOptions
        });
```

<span data-ttu-id="2cd9d-490">Wenn die App `CreateDefaultBuilder` nicht aufruft, um den Host einzurichten, rufen Sie <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> **auf**, bevor Sie `ConfigureKestrel` aufrufen:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-490">If the app doesn't call `CreateDefaultBuilder` to set up the host, call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> **before** calling `ConfigureKestrel`:</span></span>

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

## <a name="kestrel-options"></a><span data-ttu-id="2cd9d-491">Kestrel-Optionen</span><span class="sxs-lookup"><span data-stu-id="2cd9d-491">Kestrel options</span></span>

<span data-ttu-id="2cd9d-492">Der Kestrel-Webserver verfügt über einschränkende Konfigurationsoptionen, die besonders nützlich bei Bereitstellungen mit Internetzugriff sind.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-492">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="2cd9d-493">Legen Sie Einschränkungen für die <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits>-Eigenschaft der <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>-Klasse fest.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-493">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="2cd9d-494">Die `Limits`-Eigenschaft enthält eine Instanz der <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>-Klasse.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-494">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="2cd9d-495">In den folgenden Beispielen wird der <xref:Microsoft.AspNetCore.Server.Kestrel.Core>-Namespace verwendet:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-495">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="2cd9d-496">Kestrel-Optionen, die in den folgenden Beispielen in C#-Code konfiguriert sind, können auch mit einem [Konfigurationsanbieter](xref:fundamentals/configuration/index) festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-496">Kestrel options, which are configured in C# code in the following examples, can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="2cd9d-497">Beispielsweise kann der Dateikonfigurationsanbieter die Kestrel-Konfiguration aus einer *appsettings.json*- oder *appsettings.{Umgebung}.json*-Datei laden:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-497">For example, the File Configuration Provider can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

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

<span data-ttu-id="2cd9d-498">Verwenden Sie **einen** der folgenden Ansätze:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-498">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="2cd9d-499">Konfigurieren Sie Kestrel in `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-499">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="2cd9d-500">Fügen Sie eine Instanz von `IConfiguration` in die `Startup`-Klasse ein.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-500">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="2cd9d-501">Im folgenden Beispiel wird davon ausgegangen, dass die eingefügte Konfiguration der `Configuration`-Eigenschaft zugewiesen wird.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-501">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="2cd9d-502">Laden Sie in `Startup.ConfigureServices` den Abschnitt `Kestrel` der Konfiguration in die Konfiguration von Kestrel:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-502">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

* <span data-ttu-id="2cd9d-503">Konfigurieren Sie Kestrel beim Erstellen des Hosts:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-503">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="2cd9d-504">Laden Sie in *Program.cs* den Abschnitt `Kestrel` der Konfiguration in die Konfiguration von Kestrel:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-504">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

<span data-ttu-id="2cd9d-505">Beide vorangehenden Ansätze funktionieren mit jedem [Konfigurationsanbieter](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="2cd9d-505">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="2cd9d-506">Keep-Alive-Timeout</span><span class="sxs-lookup"><span data-stu-id="2cd9d-506">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="2cd9d-507">Ruft das [Keep-Alive-Timeout](https://tools.ietf.org/html/rfc7230#section-6.5) ab oder legt es fest.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-507">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="2cd9d-508">Standardwert: 2 Minuten.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-508">Defaults to 2 minutes.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=15)]

### <a name="maximum-client-connections"></a><span data-ttu-id="2cd9d-509">Maximale Anzahl der Clientverbindungen</span><span class="sxs-lookup"><span data-stu-id="2cd9d-509">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="2cd9d-510">Die maximale Anzahl von gleichzeitig geöffneten TCP-Verbindungen kann mithilfe von folgendem Code für die gesamte App festgelegt werden:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-510">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

<span data-ttu-id="2cd9d-511">Es gibt einen separaten Grenzwert für Verbindungen, die von HTTP oder HTTPS auf ein anderes Protokoll aktualisiert wurden (z.B. auf eine WebSockets-Anforderung).</span><span class="sxs-lookup"><span data-stu-id="2cd9d-511">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="2cd9d-512">Nachdem eine Verbindung aktualisiert wurde, zählt diese nicht mehr für den `MaxConcurrentConnections`-Grenzwert.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-512">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

<span data-ttu-id="2cd9d-513">Die maximale Anzahl von Verbindungen ist standardmäßig nicht begrenzt (NULL).</span><span class="sxs-lookup"><span data-stu-id="2cd9d-513">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="2cd9d-514">Maximale Größe des Anforderungstexts</span><span class="sxs-lookup"><span data-stu-id="2cd9d-514">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="2cd9d-515">Die maximale Größe des Anforderungstexts beträgt standardmäßig 30.000.000 Byte, also ungefähr 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-515">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="2cd9d-516">Die empfohlene Methode zur Außerkraftsetzung des Grenzwerts in einer ASP.NET Core-MVC-App besteht im Verwenden des <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute>-Attributs in einer Aktionsmethode:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-516">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="2cd9d-517">Im folgenden Beispiel wird veranschaulicht, wie die Einschränkungen auf jeder Anforderung für die App konfiguriert werden:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-517">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

<span data-ttu-id="2cd9d-518">Außer Kraft setzen der Einstellung für eine bestimmte Anforderung in Middleware:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-518">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="2cd9d-519">Eine Ausnahme wird ausgelöst, wenn die App den Grenzwert einer Anforderung konfiguriert, nachdem die App bereits mit dem Lesen der Anforderung begonnen hat.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-519">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="2cd9d-520">Es gibt eine `IsReadOnly`-Eigenschaft, die angibt, wenn sich die `MaxRequestBodySize`-Eigenschaft im schreibgeschützten Zustand befindet, also wenn der Grenzwert nicht mehr konfiguriert werden kann.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-520">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="2cd9d-521">Wenn eine App [prozessextern](xref:host-and-deploy/iis/index#out-of-process-hosting-model) hinter dem [ASP.NET Core-Modul](xref:host-and-deploy/aspnet-core-module) ausgeführt wird, ist das Größenlimit von Kestrel für Anforderungstext deaktiviert, weil IIS dieses Limit bereits festlegt.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-521">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="2cd9d-522">Minimale Datenrate des Anforderungstexts</span><span class="sxs-lookup"><span data-stu-id="2cd9d-522">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="2cd9d-523">Kestrel überprüft sekündlich, ob Daten mit der angegebenen Rate in Bytes/Sekunde eingehen.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-523">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="2cd9d-524">Wenn die Rate den mindestens erforderlichen Wert unterschreitet, wird für die Verbindung wegen Timeout getrennt. Bei der Toleranzperiode handelt es sich um die Zeitspanne, die Kestrel dem Client gewährt, um die Senderate auf den mindestens erforderlichen Wert zu erhöhen. Die Rate wird währenddessen nicht überprüft.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-524">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="2cd9d-525">Diese Toleranzperiode beugt dem Trennen von Verbindungen vor, die Daten aufgrund eines langsamen TCP-Starts anfänglich mit einer niedrigen Rate senden.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-525">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="2cd9d-526">Die mindestens erforderliche Rate beträgt standardmäßig 240 Bytes/Sekunde mit einer Toleranzperiode von 5 Sekunden.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-526">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="2cd9d-527">Für die Antwort gilt ebenfalls eine mindestens erforderliche Rate.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-527">A minimum rate also applies to the response.</span></span> <span data-ttu-id="2cd9d-528">Der Code zum Festlegen des Grenzwerts für Anforderung und Antwort ist abgesehen von `RequestBody` oder `Response` in den Namen von Eigenschaften und Schnittstelle identisch.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-528">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="2cd9d-529">In diesem Beispiel wird veranschaulicht, wie Sie die mindestens erforderlichen Datenraten in *Program.cs* konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-529">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-9)]

<span data-ttu-id="2cd9d-530">Außer Kraft setzen des minimalen Ratenlimits pro Anforderung in Middleware:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-530">Override the minimum rate limits per request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

<span data-ttu-id="2cd9d-531">Keines dieser Ratenfeatures, auf die im vorherigen Beispiel verwiesen wird, ist in `HttpContext.Features` für HTTP/2-Anforderungen vorhanden, da die Änderung von Ratenlimits für jede Anforderung aufgrund der Unterstützung für Anforderungsmultiplexing des Protokolls nicht für HTTP/2 unterstützt wird.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-531">Neither rate feature referenced in the prior sample are present in `HttpContext.Features` for HTTP/2 requests because modifying rate limits on a per-request basis isn't supported for HTTP/2 due to the protocol's support for request multiplexing.</span></span> <span data-ttu-id="2cd9d-532">Serverweite Ratenlimits, die über `KestrelServerOptions.Limits` konfiguriert sind, gelten auch für HTTP/1.x- und HTTP/2-Verbindungen.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-532">Server-wide rate limits configured via `KestrelServerOptions.Limits` still apply to both HTTP/1.x and HTTP/2 connections.</span></span>

### <a name="request-headers-timeout"></a><span data-ttu-id="2cd9d-533">Timeout für Anforderungsheader</span><span class="sxs-lookup"><span data-stu-id="2cd9d-533">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="2cd9d-534">Ruft die maximale Zeitspanne ab, während der der Server Anforderungsheader empfängt, oder legt diese fest.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-534">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="2cd9d-535">Der Standardwert beträgt 30 Sekunden.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-535">Defaults to 30 seconds.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=16)]

### <a name="maximum-streams-per-connection"></a><span data-ttu-id="2cd9d-536">Maximale Datenströme pro Verbindung</span><span class="sxs-lookup"><span data-stu-id="2cd9d-536">Maximum streams per connection</span></span>

<span data-ttu-id="2cd9d-537">`Http2.MaxStreamsPerConnection` schränkt die Anzahl gleichzeitiger Anforderungsdatenströme pro HTTP/2-Verbindung ein.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-537">`Http2.MaxStreamsPerConnection` limits the number of concurrent request streams per HTTP/2 connection.</span></span> <span data-ttu-id="2cd9d-538">Überschüssige Datenströme werden zurückgewiesen.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-538">Excess streams are refused.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
        });
```

<span data-ttu-id="2cd9d-539">Der Standardwert ist 100.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-539">The default value is 100.</span></span>

### <a name="header-table-size"></a><span data-ttu-id="2cd9d-540">Größe der Headertabelle</span><span class="sxs-lookup"><span data-stu-id="2cd9d-540">Header table size</span></span>

<span data-ttu-id="2cd9d-541">Der HPACK-Decoder dekomprimiert HTTP-Header für HTTP/2-Verbindungen.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-541">The HPACK decoder decompresses HTTP headers for HTTP/2 connections.</span></span> <span data-ttu-id="2cd9d-542">`Http2.HeaderTableSize` schränkt die Größe der Headerkomprimierungstabelle ein, die der HPACK-Decoder verwendet.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-542">`Http2.HeaderTableSize` limits the size of the header compression table that the HPACK decoder uses.</span></span> <span data-ttu-id="2cd9d-543">Der Wert wird in Oktetten bereitgestellt und muss größer als null (0) sein.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-543">The value is provided in octets and must be greater than zero (0).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.HeaderTableSize = 4096;
        });
```

<span data-ttu-id="2cd9d-544">Der Standardwert ist 4096.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-544">The default value is 4096.</span></span>

### <a name="maximum-frame-size"></a><span data-ttu-id="2cd9d-545">Maximale Framegröße</span><span class="sxs-lookup"><span data-stu-id="2cd9d-545">Maximum frame size</span></span>

<span data-ttu-id="2cd9d-546">`Http2.MaxFrameSize` gibt die maximale Größe der zu empfangenden HTTP/2-Verbindungsframenutzlast an.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-546">`Http2.MaxFrameSize` indicates the maximum size of the HTTP/2 connection frame payload to receive.</span></span> <span data-ttu-id="2cd9d-547">Der Wert wird in Oktetten bereitgestellt und muss zwischen 2^14 (16.384) und 2^24-1 (16.777.215) liegen.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-547">The value is provided in octets and must be between 2^14 (16,384) and 2^24-1 (16,777,215).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxFrameSize = 16384;
        });
```

<span data-ttu-id="2cd9d-548">Der Standardwert ist 2^14 (16.384).</span><span class="sxs-lookup"><span data-stu-id="2cd9d-548">The default value is 2^14 (16,384).</span></span>

### <a name="maximum-request-header-size"></a><span data-ttu-id="2cd9d-549">Maximale Größe des Anforderungsheaders</span><span class="sxs-lookup"><span data-stu-id="2cd9d-549">Maximum request header size</span></span>

<span data-ttu-id="2cd9d-550">`Http2.MaxRequestHeaderFieldSize` gibt die maximal zulässige Größe in Oktetten der Anforderungsheaderwerte an.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-550">`Http2.MaxRequestHeaderFieldSize` indicates the maximum allowed size in octets of request header values.</span></span> <span data-ttu-id="2cd9d-551">Dieser Grenzwert gilt zusammen für den Namen und Wert in komprimierten und nicht komprimierten Darstellungen.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-551">This limit applies to both name and value together in their compressed and uncompressed representations.</span></span> <span data-ttu-id="2cd9d-552">Der Wert muss größer als 0 (null) sein.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-552">The value must be greater than zero (0).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
        });
```

<span data-ttu-id="2cd9d-553">Der Standardwert ist 8.192.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-553">The default value is 8,192.</span></span>

### <a name="initial-connection-window-size"></a><span data-ttu-id="2cd9d-554">Anfangsfenstergröße der Verbindung</span><span class="sxs-lookup"><span data-stu-id="2cd9d-554">Initial connection window size</span></span>

<span data-ttu-id="2cd9d-555">`Http2.InitialConnectionWindowSize` gibt die maximalen Anforderungstextdaten in Byte an, die der Server auf einmal für alle Anforderungen (Streams) pro Verbindung aggregiert.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-555">`Http2.InitialConnectionWindowSize` indicates the maximum request body data in bytes the server buffers at one time aggregated across all requests (streams) per connection.</span></span> <span data-ttu-id="2cd9d-556">Anforderungen werden auch durch `Http2.InitialStreamWindowSize` beschränkt.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-556">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="2cd9d-557">Der Wert muss größer als oder gleich 65.535 und kleiner als 2^31 (2.147.483.648) sein.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-557">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
        });
```

<span data-ttu-id="2cd9d-558">Der Standardwert ist 128 KB (131.072).</span><span class="sxs-lookup"><span data-stu-id="2cd9d-558">The default value is 128 KB (131,072).</span></span>

### <a name="initial-stream-window-size"></a><span data-ttu-id="2cd9d-559">Anfangsfenstergröße des Streams</span><span class="sxs-lookup"><span data-stu-id="2cd9d-559">Initial stream window size</span></span>

<span data-ttu-id="2cd9d-560">`Http2.InitialStreamWindowSize` gibt die maximalen Anforderungstextdaten in Byte an, die der Server auf einmal pro Anforderung (Stream) puffert.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-560">`Http2.InitialStreamWindowSize` indicates the maximum request body data in bytes the server buffers at one time per request (stream).</span></span> <span data-ttu-id="2cd9d-561">Anforderungen werden auch durch `Http2.InitialStreamWindowSize` beschränkt.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-561">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="2cd9d-562">Der Wert muss größer als oder gleich 65.535 und kleiner als 2^31 (2.147.483.648) sein.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-562">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
        });
```

<span data-ttu-id="2cd9d-563">Der Standardwert ist 96 KB (98.304).</span><span class="sxs-lookup"><span data-stu-id="2cd9d-563">The default value is 96 KB (98,304).</span></span>

### <a name="synchronous-io"></a><span data-ttu-id="2cd9d-564">Synchrone E/A-Vorgänge</span><span class="sxs-lookup"><span data-stu-id="2cd9d-564">Synchronous I/O</span></span>

<span data-ttu-id="2cd9d-565"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> steuert, ob für Anforderung und Antwort synchrone E/A-Vorgänge zulässig sind.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-565"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous I/O is allowed for the request and response.</span></span> <span data-ttu-id="2cd9d-566">Der Standardwert ist `true`.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-566">The  default value is `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="2cd9d-567">Sehr viele blockierende synchrone E/A-Vorgänge können zu einem Ressourcenmangel im Threadpool führen, wodurch die App nicht mehr reagiert.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-567">A large number of blocking synchronous I/O operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="2cd9d-568">Aktivieren Sie `AllowSynchronousIO` nur bei Verwendung einer Bibliothek, die asynchrone E/A-Vorgänge nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-568">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous I/O.</span></span>

<span data-ttu-id="2cd9d-569">Das folgende Beispiel aktiviert synchrone E/A-Vorgänge:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-569">The following example enables synchronous I/O:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

<span data-ttu-id="2cd9d-570">Weitere Informationen zu anderen Kestrel-Optionen und -Einschränkungen finden Sie unter:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-570">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="2cd9d-571">Endpunktkonfiguration</span><span class="sxs-lookup"><span data-stu-id="2cd9d-571">Endpoint configuration</span></span>

<span data-ttu-id="2cd9d-572">Standardmäßig wird ASP.NET Core an Folgendes gebunden:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-572">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="2cd9d-573">`https://localhost:5001` (wenn ein lokales Entwicklungszertifikat vorhanden ist)</span><span class="sxs-lookup"><span data-stu-id="2cd9d-573">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="2cd9d-574">Verwenden Sie Folgendes zum Angeben der URLs:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-574">Specify URLs using the:</span></span>

* <span data-ttu-id="2cd9d-575">Die Umgebungsvariable `ASPNETCORE_URLS`</span><span class="sxs-lookup"><span data-stu-id="2cd9d-575">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="2cd9d-576">Das Befehlszeilenargument `--urls`</span><span class="sxs-lookup"><span data-stu-id="2cd9d-576">`--urls` command-line argument.</span></span>
* <span data-ttu-id="2cd9d-577">Den Hostkonfigurationsschlüssel `urls`</span><span class="sxs-lookup"><span data-stu-id="2cd9d-577">`urls` host configuration key.</span></span>
* <span data-ttu-id="2cd9d-578">Die Erweiterungsmethode `UseUrls`</span><span class="sxs-lookup"><span data-stu-id="2cd9d-578">`UseUrls` extension method.</span></span>

<span data-ttu-id="2cd9d-579">Der Wert, der mit diesen Ansätzen angegeben wird, kann mindestens ein HTTP- oder HTTPS-Endpunkt sein (HTTPS wenn ein Standardzertifikat verfügbar ist).</span><span class="sxs-lookup"><span data-stu-id="2cd9d-579">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="2cd9d-580">Konfigurieren Sie den Wert als eine durch Semikolons getrennte Liste (z.B. `"Urls": "http://localhost:8000;http://localhost:8001"`).</span><span class="sxs-lookup"><span data-stu-id="2cd9d-580">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="2cd9d-581">Weitere Informationen zu diesen Ansätzen finden Sie unter [Server-URLs](xref:fundamentals/host/web-host#server-urls) und [Außerkraftsetzen der Konfiguration](xref:fundamentals/host/web-host#override-configuration).</span><span class="sxs-lookup"><span data-stu-id="2cd9d-581">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="2cd9d-582">Ein Entwicklungszertifikat wird erstellt:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-582">A development certificate is created:</span></span>

* <span data-ttu-id="2cd9d-583">Wenn das [.NET Core SDK](/dotnet/core/sdk) installiert wird.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-583">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="2cd9d-584">Das [dev-cert-Tool](xref:aspnetcore-2.1#https) wird zum Erstellen eines Zertifikats verwendet.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-584">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="2cd9d-585">Einige Browser erfordern, dass Sie die explizite Berechtigung erteilen, dem lokalen Entwicklungszertifikat zu vertrauen.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-585">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="2cd9d-586">Projektvorlagen konfigurieren Apps, damit sie standardmäßig auf HTTPS ausgeführt werden und die [HTTPS-Umleitung und HSTS-Unterstützung](xref:security/enforcing-ssl) enthalten.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-586">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="2cd9d-587">Rufen Sie die <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*>- oder <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*>-Methode unter <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> auf, um URL-Präfixe und Ports für Kestrel zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-587">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="2cd9d-588">`UseUrls`, das Befehlszeilenargument `--urls`, der Hostkonfigurationsschlüssel `urls` und die Umgebungsvariable `ASPNETCORE_URLS` funktionieren ebenfalls, verfügen jedoch über Einschränkungen, die im Verlauf dieses Abschnitts erläutert werden (Ein Standardzertifikat muss für die HTTPS-Endpunktkonfiguration verfügbar sein).</span><span class="sxs-lookup"><span data-stu-id="2cd9d-588">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="2cd9d-589">`KestrelServerOptions`-Konfiguration:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-589">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="2cd9d-590">ConfigureEndpointDefaults(Action\<ListenOptions>)</span><span class="sxs-lookup"><span data-stu-id="2cd9d-590">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="2cd9d-591">Gibt die Konfiguration von `Action` zum Ausführen von jedem angegebenen Endpunkt an.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-591">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="2cd9d-592">Mehrmalige Aufrufe von `ConfigureEndpointDefaults` ersetzen vorherige Instanzen von `Action` mit der zuletzt angegebenen `Action`.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-592">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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
> <span data-ttu-id="2cd9d-593">Auf Endpunkte, die durch Aufrufen von <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **vor** dem Aufrufen von <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> erstellt werden, werden die Standardwerte nicht angewendet.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-593">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> won't have the defaults applied.</span></span>

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="2cd9d-594">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span><span class="sxs-lookup"><span data-stu-id="2cd9d-594">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="2cd9d-595">Gibt die Konfiguration von `Action` zum Ausführen von jedem HTTPS-Endpunkt an.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-595">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="2cd9d-596">Mehrmalige Aufrufe von `ConfigureHttpsDefaults` ersetzen vorherige Instanzen von `Action` mit der zuletzt angegebenen `Action`.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-596">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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
> <span data-ttu-id="2cd9d-597">Auf Endpunkte, die durch Aufrufen von <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **vor** dem Aufrufen von <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> erstellt werden, werden die Standardwerte nicht angewendet.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-597">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>


### <a name="configureiconfiguration"></a><span data-ttu-id="2cd9d-598">Configure(IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="2cd9d-598">Configure(IConfiguration)</span></span>

<span data-ttu-id="2cd9d-599">Erstellt ein Konfigurationsladeprogramm für das Einrichten von Kestrel, was <xref:Microsoft.Extensions.Configuration.IConfiguration> als Eingabe erfordert.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-599">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="2cd9d-600">Die Konfiguration muss auf den Konfigurationsabschnitt für Kestrel festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-600">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="2cd9d-601">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="2cd9d-601">ListenOptions.UseHttps</span></span>

<span data-ttu-id="2cd9d-602">Konfiguriert Kestrel zur Verwendung von HTTPS.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-602">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="2cd9d-603">`ListenOptions.UseHttps`-Erweiterungen:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-603">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="2cd9d-604">`UseHttps`: Hiermit wird Kestrel zur Verwendung von HTTPS mit dem Standardzertifikat konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-604">`UseHttps`: Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="2cd9d-605">Löst eine Ausnahme aus, wenn kein Standardzertifikat konfiguriert ist.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-605">Throws an exception if no default certificate is configured.</span></span>
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

<span data-ttu-id="2cd9d-606">`ListenOptions.UseHttps`-Parameter:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-606">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="2cd9d-607">`filename` entspricht dem Pfad und Dateinamen einer Zertifikatdatei relativ zu dem Verzeichnis, das die Inhaltsdateien der App enthält.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-607">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="2cd9d-608">`password` ist das für den Zugriff auf die X.509-Zertifikatsdaten erforderliche Kennwort.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-608">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="2cd9d-609">`configureOptions` ist eine `Action` zum Konfigurieren von `HttpsConnectionAdapterOptions`.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-609">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="2cd9d-610">Gibt `ListenOptions` zurück.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-610">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="2cd9d-611">`storeName` ist der Zertifikatspeicher, aus dem das Zertifikat geladen wird.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-611">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="2cd9d-612">`subject` ist der Name des Antragstellers für das Zertifikat.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-612">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="2cd9d-613">`allowInvalid` gibt an, ob ungültige Zertifikate berücksichtigt werden sollten, z.B. selbstsignierte Zertifikate.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-613">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="2cd9d-614">`location` ist der Speicherort, aus dem das Zertifikat geladen wird.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-614">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="2cd9d-615">`serverCertificate` ist das X.509-Zertifikat.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-615">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="2cd9d-616">In der Produktion muss HTTPS explizit konfiguriert sein.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-616">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="2cd9d-617">Zumindest muss ein Standardzertifikat angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-617">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="2cd9d-618">Die im Folgenden beschriebenen unterstützten Konfigurationen:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-618">Supported configurations described next:</span></span>

* <span data-ttu-id="2cd9d-619">Keine Konfiguration</span><span class="sxs-lookup"><span data-stu-id="2cd9d-619">No configuration</span></span>
* <span data-ttu-id="2cd9d-620">Ersetzen des Standardzertifikats aus der Konfiguration</span><span class="sxs-lookup"><span data-stu-id="2cd9d-620">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="2cd9d-621">Ändern des Standards im Code</span><span class="sxs-lookup"><span data-stu-id="2cd9d-621">Change the defaults in code</span></span>

<span data-ttu-id="2cd9d-622">*Keine Konfiguration*</span><span class="sxs-lookup"><span data-stu-id="2cd9d-622">*No configuration*</span></span>

<span data-ttu-id="2cd9d-623">Kestrel überwacht `http://localhost:5000` und `https://localhost:5001` (wenn ein Standardzertifikat verfügbar ist).</span><span class="sxs-lookup"><span data-stu-id="2cd9d-623">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="2cd9d-624">*Ersetzen des Standardzertifikats aus der Konfiguration*</span><span class="sxs-lookup"><span data-stu-id="2cd9d-624">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="2cd9d-625">`CreateDefaultBuilder` ruft `Configure(context.Configuration.GetSection("Kestrel"))` standardmäßig zum Laden der Kestrel-Konfiguration auf.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-625">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="2cd9d-626">Ein Standardkonfigurationsschema für HTTPS-App-Einstellungen ist für Kestrel verfügbar.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-626">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="2cd9d-627">Konfigurieren Sie mehrere Endpunkte, einschließlich der zu verwendenden URLs und Zertifikate aus einer Datei auf dem Datenträger oder einem Zertifikatspeicher.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-627">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="2cd9d-628">Die Vorgehensweise im folgenden *appsettings.json*-Beispiel:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-628">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="2cd9d-629">Legen Sie **AllowInvalid** auf `true` fest, um die Verwendung von ungültigen Zertifikaten zu erlauben (z.B. selbstsignierte Zertifikate).</span><span class="sxs-lookup"><span data-stu-id="2cd9d-629">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="2cd9d-630">Jeder HTTPS-Endpunkt, der kein Zertifikat angibt (im folgenden Beispiel **HttpsDefaultCert**), greift auf das unter **Zertifikate** > **Standard** festgelegte Zertifikat oder das Entwicklungszertifikat zurück.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-630">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

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

<span data-ttu-id="2cd9d-631">Alternativ zur Verwendung von **Pfad** und **Kennwort** für alle Zertifikatknoten können Sie das Zertifikat mithilfe von Zertifikatspeicherfeldern angeben.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-631">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="2cd9d-632">Das Zertifikat unter **Zertifikate** > **Standard** kann beispielweise wie folgt angegeben werden:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-632">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="2cd9d-633">Schema-Hinweise:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-633">Schema notes:</span></span>

* <span data-ttu-id="2cd9d-634">Bei den Namen der Endpunkte wird die Groß-/Kleinschreibung nicht beachtet.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-634">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="2cd9d-635">Zum Beispiel sind `HTTPS` und `Https` gültig.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-635">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="2cd9d-636">Der Parameter `Url` ist für jeden Endpunkt erforderlich.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-636">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="2cd9d-637">Das Format für diesen Parameter ist identisch mit dem allgemeinen Konfigurationsparameter `Urls`, mit der Ausnahme, dass er auf einen einzelnen Wert begrenzt ist.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-637">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="2cd9d-638">Diese Endpunkte ersetzen die in der allgemeinen `Urls`-Konfiguration festgelegten Endpunkte, anstatt zu ihnen hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-638">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="2cd9d-639">Endpunkte, die über `Listen` in Code definiert werden, werden den im Konfigurationsabschnitt definierten Endpunkten hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-639">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="2cd9d-640">Der `Certificate`-Abschnitt ist optional.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-640">The `Certificate` section is optional.</span></span> <span data-ttu-id="2cd9d-641">Wenn der `Certificate`-Abschnitt nicht angegeben ist, werden die in früheren Szenarios definierten Standardwerte verwendet.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-641">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="2cd9d-642">Wenn keine Standardwerte verfügbar sind, löst der Server eine Ausnahme aus und startet nicht.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-642">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="2cd9d-643">Der `Certificate`-Abschnitt unterstützt die Zertifikate **Pfad**&ndash;**Kennwort** und **Subject**&ndash;**Store** (Antragsteller–Speicher).</span><span class="sxs-lookup"><span data-stu-id="2cd9d-643">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="2cd9d-644">Auf diese Weise kann eine beliebige Anzahl von Endpunkten definiert werden, solange Sie keine Portkonflikte verursachen.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-644">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="2cd9d-645">`options.Configure(context.Configuration.GetSection("{SECTION}"))` gibt `KestrelConfigurationLoader` mit der Methode `.Endpoint(string name, listenOptions => { })` zurück, die dazu verwendet werden kann, die Einstellungen eines Endpunkts zu ergänzen:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-645">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

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

<span data-ttu-id="2cd9d-646">Auf `KestrelServerOptions.ConfigurationLoader` kann direkt zugegriffen werden, um die Iteration auf dem vorhandenen Ladeprogramm fortzusetzen, etwa auf dem von <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> bereitgestellten Ladeprogramm.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-646">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="2cd9d-647">Der Konfigurationsabschnitt ist für jeden Endpunkt in den Optionen der Methode `Endpoint` verfügbar, sodass benutzerdefinierte Einstellungen gelesen werden können.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-647">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="2cd9d-648">Mehrere Konfigurationen können durch erneutes Aufrufen von `options.Configure(context.Configuration.GetSection("{SECTION}"))` mit einem anderen Abschnitt geladen werden.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-648">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="2cd9d-649">Sofern `Load` nicht in einer vorherigen Instanz explizit aufgerufen wird, wird nur die letzte Konfiguration verwendet.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-649">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="2cd9d-650">Das Metapaket ruft `Load` nicht auf, sodass der Abschnitt mit der Standardkonfiguration ersetzt werden kann.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-650">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="2cd9d-651">`KestrelConfigurationLoader` spiegelt die API-Familie `Listen` von `KestrelServerOptions` als `Endpoint`-Überladungen, weshalb Code und Konfigurationsendpunkte am selben Ort konfiguriert werden können.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-651">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="2cd9d-652">Diese Überladungen verwenden keine Namen und nutzen nur Standardeinstellungen aus der Konfiguration.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-652">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="2cd9d-653">*Ändern des Standards im Code*</span><span class="sxs-lookup"><span data-stu-id="2cd9d-653">*Change the defaults in code*</span></span>

<span data-ttu-id="2cd9d-654">`ConfigureEndpointDefaults` und `ConfigureHttpsDefaults` können zum Ändern der Standardeinstellungen für `ListenOptions` und `HttpsConnectionAdapterOptions` verwendet werden, einschließlich der Standardzertifikate, die im vorherigen Szenario festgelegt wurden.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-654">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="2cd9d-655">`ConfigureEndpointDefaults` und `ConfigureHttpsDefaults` sollten aufgerufen werden, bevor Endpunkte konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-655">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

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

<span data-ttu-id="2cd9d-656">*Kestrel-Unterstützung für SNI*</span><span class="sxs-lookup"><span data-stu-id="2cd9d-656">*Kestrel support for SNI*</span></span>

<span data-ttu-id="2cd9d-657">Die [Servernamensanzeige (SNI)](https://tools.ietf.org/html/rfc6066#section-3) kann zum Hosten mehrerer Domänen auf der gleichen IP-Adresse und dem gleichen Port verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-657">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="2cd9d-658">Damit die Servernamensanzeige funktioniert, sendet der Client während des TLS-Handshakes den Hostnamen für die sichere Sitzung an den Server, sodass der Server das richtige Zertifikat bereitstellen kann.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-658">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="2cd9d-659">Der Client verwendet das beigestellte Zertifikat für die verschlüsselte Kommunikation mit dem Server während der sicheren Sitzung nach dem TLS-Handshake.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-659">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="2cd9d-660">Kestrel unterstützt die Servernamensanzeige über den `ServerCertificateSelector`-Rückruf.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-660">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="2cd9d-661">Der Rückruf wird für jede Verbindung einmal aufgerufen, um der App zu ermöglichen, den Hostnamen zu überprüfen und das entsprechende Zertifikat auszuwählen.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-661">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="2cd9d-662">Für die Unterstützung der Servernamensanzeige benötigen Sie Folgendes:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-662">SNI support requires:</span></span>

* <span data-ttu-id="2cd9d-663">Wird auf dem Zielframework `netcoreapp2.1` oder höher ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-663">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="2cd9d-664">In `net461` oder höher, wird der Rückruf aufgerufen, `name` ist aber immer `null`.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-664">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="2cd9d-665">`name` ist auch `null`, wenn der Client den Hostnamenparameter nicht im TLS-Handshake angibt.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-665">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="2cd9d-666">Alle Websites werden in derselben Kestrel-Instanz ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-666">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="2cd9d-667">Kestrel unterstützt ohne Reverseproxy keine gemeinsame IP-Adresse und keinen gemeinsamen Port für mehrere Instanzen.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-667">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

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

### <a name="connection-logging"></a><span data-ttu-id="2cd9d-668">Verbindungsprotokollierung</span><span class="sxs-lookup"><span data-stu-id="2cd9d-668">Connection logging</span></span>

<span data-ttu-id="2cd9d-669">Rufen Sie <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> auf, um Protokolle auf Debugebene für die Kommunikation auf Byteebene für eine Verbindung auszugeben.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-669">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="2cd9d-670">Die Verbindungsprotokollierung ist beim Beheben von Problemen bei der Low-Level-Kommunikation hilfreich, wie z. B. bei der TLS-Verschlüsselung und bei Proxys.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-670">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="2cd9d-671">Wenn `UseConnectionLogging` vor `UseHttps` platziert wird, wird der verschlüsselte Datenverkehr protokolliert.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-671">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="2cd9d-672">Wenn `UseConnectionLogging` nach `UseHttps` platziert wird, wird der entschlüsselte Datenverkehr protokolliert.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-672">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="2cd9d-673">Binden an einen TCP-Socket</span><span class="sxs-lookup"><span data-stu-id="2cd9d-673">Bind to a TCP socket</span></span>

<span data-ttu-id="2cd9d-674">Die <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*>-Methode wird an ein TCP-Socket gebunden, und ein Lambdaausdruck einer Option lässt die Konfiguration des X.509-Zertifikats zu:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-674">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=9-16)]

<span data-ttu-id="2cd9d-675">Im Beispiel wird HTTPS für einen Endpunkt mit <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions> konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-675">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="2cd9d-676">Verwenden Sie die gleiche API zum Konfigurieren anderer Kestrel-Einstellungen für bestimmte Endpunkte.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-676">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="2cd9d-677">Binden an einen Unix-Socket</span><span class="sxs-lookup"><span data-stu-id="2cd9d-677">Bind to a Unix socket</span></span>

<span data-ttu-id="2cd9d-678">Wie in diesem Beispiel dargestellt, lauschen Sie an einem Unix-Socket mit <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*>, um eine verbesserte Leistung mit Nginx zu erzielen:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-678">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

* <span data-ttu-id="2cd9d-679">Legen Sie in der Nginx-Konfigurationsdatei den Eintrag `server` > `location` > `proxy_pass` auf `http://unix:/tmp/{KESTREL SOCKET}:/;` fest.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-679">In the Nginx confiuguration file, set the `server` > `location` > `proxy_pass` entry to `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span></span> <span data-ttu-id="2cd9d-680">`{KESTREL SOCKET}` ist der Name des für <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> bereitgestellten Socket (zum Beispiel `kestrel-test.sock` im vorherigen Beispiel).</span><span class="sxs-lookup"><span data-stu-id="2cd9d-680">`{KESTREL SOCKET}` is the name of the socket provided to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (for example, `kestrel-test.sock` in the preceding example).</span></span>
* <span data-ttu-id="2cd9d-681">Stellen Sie sicher, dass der Socket von Nginx beschreibbar ist (z. B. `chmod go+w /tmp/kestrel-test.sock`).</span><span class="sxs-lookup"><span data-stu-id="2cd9d-681">Ensure that the socket is writeable by Nginx (for example, `chmod go+w /tmp/kestrel-test.sock`).</span></span> 

### <a name="port-0"></a><span data-ttu-id="2cd9d-682">Port 0</span><span class="sxs-lookup"><span data-stu-id="2cd9d-682">Port 0</span></span>

<span data-ttu-id="2cd9d-683">Wenn die Portnummer `0` angegeben wird, wird Kestrel dynamisch an einen verfügbaren Port gebunden.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-683">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="2cd9d-684">Im folgenden Beispiel wird veranschaulicht, wie bestimmt werden kann, für welchen Port Kestrel zur Laufzeit eine Bindung erstellt hat:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-684">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="2cd9d-685">Wenn die App ausgeführt wird, gibt das Ausgabefenster der Konsole den dynamischen Port an, über den die App erreicht werden kann:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-685">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="2cd9d-686">Einschränkungen</span><span class="sxs-lookup"><span data-stu-id="2cd9d-686">Limitations</span></span>

<span data-ttu-id="2cd9d-687">Konfigurieren Sie Endpunkte mithilfe der folgenden Ansätze:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-687">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="2cd9d-688">Befehlszeilenargument `--urls`</span><span class="sxs-lookup"><span data-stu-id="2cd9d-688">`--urls` command-line argument</span></span>
* <span data-ttu-id="2cd9d-689">Hostkonfigurationsschlüssel `urls`</span><span class="sxs-lookup"><span data-stu-id="2cd9d-689">`urls` host configuration key</span></span>
* <span data-ttu-id="2cd9d-690">Umgebungsvariable `ASPNETCORE_URLS`</span><span class="sxs-lookup"><span data-stu-id="2cd9d-690">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="2cd9d-691">Diese Methoden sind nützlich, wenn Ihr Code mit anderen Servern als Kestrel funktionieren soll.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-691">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="2cd9d-692">Beachten Sie jedoch die folgenden Einschränkungen:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-692">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="2cd9d-693">HTTPS kann nicht mit diesen Ansätzen verwendet werden, außer ein Standardzertifikat wird in der HTTPS-Endpunktkonfiguration angegeben (z.B. wenn Sie wie zuvor in diesem Artikel gezeigt die `KestrelServerOptions`-Konfiguration oder eine Konfigurationsdatei verwenden).</span><span class="sxs-lookup"><span data-stu-id="2cd9d-693">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="2cd9d-694">Wenn die Ansätze `Listen` und `UseUrls` gleichzeitig verwendet werden, überschreiben die `Listen`-Endpunkte die `UseUrls`-Endpunkte.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-694">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="2cd9d-695">IIS-Endpunktkonfiguration</span><span class="sxs-lookup"><span data-stu-id="2cd9d-695">IIS endpoint configuration</span></span>

<span data-ttu-id="2cd9d-696">Bei der Verwendung von IIS werden die URL-Bindungen für IIS-Überschreibungsbindungen durch `Listen` oder `UseUrls` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-696">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="2cd9d-697">Weitere Informationen finden Sie im Artikel [ASP.NET Core-Modul](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="2cd9d-697">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

### <a name="listenoptionsprotocols"></a><span data-ttu-id="2cd9d-698">ListenOptions.Protocols</span><span class="sxs-lookup"><span data-stu-id="2cd9d-698">ListenOptions.Protocols</span></span>

<span data-ttu-id="2cd9d-699">Die `Protocols`-Eigenschaft richtet die HTTP-Protokolle (`HttpProtocols`) ein, die für einen Verbindungsendpunkt oder für den Server aktiviert werden.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-699">The `Protocols` property establishes the HTTP protocols (`HttpProtocols`) enabled on a connection endpoint or for the server.</span></span> <span data-ttu-id="2cd9d-700">Weisen Sie der `Protocols`-Eigenschaft einen Wert aus der `HttpProtocols`-Enumeration zu.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-700">Assign a value to the `Protocols` property from the `HttpProtocols` enum.</span></span>

| <span data-ttu-id="2cd9d-701">`HttpProtocols`-Enumerationswert</span><span class="sxs-lookup"><span data-stu-id="2cd9d-701">`HttpProtocols` enum value</span></span> | <span data-ttu-id="2cd9d-702">Zulässiges Verbindungsprotokoll</span><span class="sxs-lookup"><span data-stu-id="2cd9d-702">Connection protocol permitted</span></span> |
| -------------------------- | ----------------------------- |
| `Http1`                    | <span data-ttu-id="2cd9d-703">Nur HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-703">HTTP/1.1 only.</span></span> <span data-ttu-id="2cd9d-704">Kann mit oder ohne TLS verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-704">Can be used with or without TLS.</span></span> |
| `Http2`                    | <span data-ttu-id="2cd9d-705">Nur HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-705">HTTP/2 only.</span></span> <span data-ttu-id="2cd9d-706">Kann nur ohne TLS verwendet werden, wenn der Client einen [Vorabkenntnis-Modus](https://tools.ietf.org/html/rfc7540#section-3.4) unterstützt.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-706">May be used without TLS only if the client supports a [Prior Knowledge mode](https://tools.ietf.org/html/rfc7540#section-3.4).</span></span> |
| `Http1AndHttp2`            | <span data-ttu-id="2cd9d-707">HTTP/1.1 und HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-707">HTTP/1.1 and HTTP/2.</span></span> <span data-ttu-id="2cd9d-708">HTTP/2 erfordert eine TLS- und [ALPN](https://tools.ietf.org/html/rfc7301#section-3)-Verbindung (Application-Layer Protocol Negotiation). Andernfalls wird für die Verbindung standardmäßig HTTP/1.1 verwendet.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-708">HTTP/2 requires a TLS and [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection; otherwise, the connection defaults to HTTP/1.1.</span></span> |

<span data-ttu-id="2cd9d-709">Das Standardprotokoll ist HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-709">The default protocol is HTTP/1.1.</span></span>

<span data-ttu-id="2cd9d-710">TLS-Einschränkungen für HTTP/2:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-710">TLS restrictions for HTTP/2:</span></span>

* <span data-ttu-id="2cd9d-711">TLS Version 1.2 oder höher</span><span class="sxs-lookup"><span data-stu-id="2cd9d-711">TLS version 1.2 or later</span></span>
* <span data-ttu-id="2cd9d-712">Erneute Aushandlung deaktiviert</span><span class="sxs-lookup"><span data-stu-id="2cd9d-712">Renegotiation disabled</span></span>
* <span data-ttu-id="2cd9d-713">Komprimierung deaktiviert</span><span class="sxs-lookup"><span data-stu-id="2cd9d-713">Compression disabled</span></span>
* <span data-ttu-id="2cd9d-714">Minimale Größen für Austausch von flüchtigen Schlüsseln:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-714">Minimum ephemeral key exchange sizes:</span></span>
  * <span data-ttu-id="2cd9d-715">ECDHE (Elliptic Curve Diffie-Hellman) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack;: mindestens 224 Bits</span><span class="sxs-lookup"><span data-stu-id="2cd9d-715">Elliptic curve Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack;: 224 bits minimum</span></span>
  * <span data-ttu-id="2cd9d-716">DHE (Finite Field Diffie-Hellman) &lbrack;`TLS12`&rbrack;: mindestens 2048 Bits</span><span class="sxs-lookup"><span data-stu-id="2cd9d-716">Finite field Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack;: 2048 bits minimum</span></span>
* <span data-ttu-id="2cd9d-717">Verschlüsselungssammlung nicht gesperrt</span><span class="sxs-lookup"><span data-stu-id="2cd9d-717">Cipher suite not blocked</span></span>

<span data-ttu-id="2cd9d-718">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; mit der elliptischen P-256-Kurve &lbrack;`FIPS186`&rbrack; wird standardmäßig unterstützt.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-718">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; with the P-256 elliptic curve &lbrack;`FIPS186`&rbrack; is supported by default.</span></span>

<span data-ttu-id="2cd9d-719">Das folgende Beispiel erlaubt HTTP/1.1- und HTTP/2-Verbindungen an Port 8000.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-719">The following example permits HTTP/1.1 and HTTP/2 connections on port 8000.</span></span> <span data-ttu-id="2cd9d-720">Die Verbindungen werden durch TLS mit einem bereitgestellten Zertifikat geschützt:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-720">Connections are secured by TLS with a supplied certificate:</span></span>

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

<span data-ttu-id="2cd9d-721">Erstellen Sie optional eine `IConnectionAdapter`-Implementierung, um TLS-Handshakes auf Verbindungsbasis für bestimmte Verschlüsselungen zu filtern:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-721">Optionally create an `IConnectionAdapter` implementation to filter TLS handshakes on a per-connection basis for specific ciphers:</span></span>

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

<span data-ttu-id="2cd9d-722">*Festlegen des Protokolls aus der Konfiguration*</span><span class="sxs-lookup"><span data-stu-id="2cd9d-722">*Set the protocol from configuration*</span></span>

<span data-ttu-id="2cd9d-723"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> ruft `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` standardmäßig zum Laden der Kestrel-Konfiguration auf.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-723"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> calls `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span>

<span data-ttu-id="2cd9d-724">Im folgenden Beispiel *appsettings.json* wird für alle Endpunkte von Kestrel ein Standardverbindungsprotokoll (HTTP/1.1 und HTTP/2) eingerichtet:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-724">In the following *appsettings.json* example, a default connection protocol (HTTP/1.1 and HTTP/2) is established for all of Kestrel's endpoints:</span></span>

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1AndHttp2"
    }
  }
}
```

<span data-ttu-id="2cd9d-725">Das folgende Beispiel einer Konfigurationsdatei richtet ein Verbindungsprotokoll für einen bestimmten Endpunkt ein:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-725">The following configuration file example establishes a connection protocol for a specific endpoint:</span></span>

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

<span data-ttu-id="2cd9d-726">Protokolle, die in Code angegeben werden, setzen Werte außer Kraft, der durch die Konfiguration festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-726">Protocols specified in code override values set by configuration.</span></span>

## <a name="transport-configuration"></a><span data-ttu-id="2cd9d-727">Transportkonfiguration</span><span class="sxs-lookup"><span data-stu-id="2cd9d-727">Transport configuration</span></span>

<span data-ttu-id="2cd9d-728">Mit dem Release von ASP.NET Core 2.1 basiert der Standardtransport von Kestrel nicht mehr auf Libuv, sondern auf verwalteten Sockets.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-728">With the release of ASP.NET Core 2.1, Kestrel's default transport is no longer based on Libuv but instead based on managed sockets.</span></span> <span data-ttu-id="2cd9d-729">Dies stellt einen Breaking Change für ASP.NET Core 2.0-Apps dar, die auf Version 2.1 upgraden, <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> aufrufen und von einem der folgenden Pakete abhängig sind:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-729">This is a breaking change for ASP.NET Core 2.0 apps upgrading to 2.1 that call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> and depend on either of the following packages:</span></span>

* <span data-ttu-id="2cd9d-730">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (direkter Paketverweis)</span><span class="sxs-lookup"><span data-stu-id="2cd9d-730">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (direct package reference)</span></span>
* [<span data-ttu-id="2cd9d-731">Microsoft.AspNetCore.App</span><span class="sxs-lookup"><span data-stu-id="2cd9d-731">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)

<span data-ttu-id="2cd9d-732">Für Projekte, die den Einsatz von Libuv erfordern:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-732">For projects that require the use of Libuv:</span></span>

* <span data-ttu-id="2cd9d-733">Fügen Sie für das [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/)-Paket eine Abhängigkeit auf die Projektdatei der App hinzu:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-733">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* <span data-ttu-id="2cd9d-734">Rufen Sie <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> auf:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-734">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span></span>

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

### <a name="url-prefixes"></a><span data-ttu-id="2cd9d-735">URL-Präfixe</span><span class="sxs-lookup"><span data-stu-id="2cd9d-735">URL prefixes</span></span>

<span data-ttu-id="2cd9d-736">Bei der Verwendung von `UseUrls`, dem Befehlszeilenargument `--urls`, dem Hostkonfigurationsschlüssel `urls` oder der Umgebungsvariable `ASPNETCORE_URLS` können die URL-Präfixe in den folgenden Formaten vorliegen.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-736">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="2cd9d-737">Nur HTTP-URL-Präfixe sind gültig.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-737">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="2cd9d-738">Kestrel unterstützt HTTPS nicht beim Konfigurieren von URL-Bindungen mit `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-738">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="2cd9d-739">IPv4-Adresse mit Portnummer</span><span class="sxs-lookup"><span data-stu-id="2cd9d-739">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="2cd9d-740">Bei `0.0.0.0` handelt es sich um einen Sonderfall, für den eine Bindung an alle IPv4-Adressen erfolgt.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-740">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="2cd9d-741">IPv6-Adresse mit Portnummer</span><span class="sxs-lookup"><span data-stu-id="2cd9d-741">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="2cd9d-742">`[::]` stellt das Äquivalent von IPv6 zu `0.0.0.0` für IPv4 dar.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-742">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="2cd9d-743">Hostname mit Portnummer</span><span class="sxs-lookup"><span data-stu-id="2cd9d-743">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="2cd9d-744">Hostnamen, `*` und `+` sind nicht spezifisch.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-744">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="2cd9d-745">Alle Elemente, die nicht als gültige IP-Adresse oder `localhost` erkannt werden, werden an alle IPv4- und IPv6-IP-Adressen gebunden.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-745">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="2cd9d-746">Verwenden Sie [HTTP.sys](xref:fundamentals/servers/httpsys) oder einen Reverseproxyserver zum Binden verschiedener Hostnamen an verschiedene ASP.NET Core-Apps auf demselben Port, z.B. IIS, Nginx oder Apache.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-746">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="2cd9d-747">Das Hosting in einer Reverseproxykonfiguration erfordert [Hostfilterung](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="2cd9d-747">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="2cd9d-748">`localhost`-Hostname mit Portnummer oder Loopback-IP mit Portnummer</span><span class="sxs-lookup"><span data-stu-id="2cd9d-748">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="2cd9d-749">Wenn `localhost` angegeben ist, versucht Kestrel, eine Bindung zu IPv4- und IPv6-Loopback-Schnittstellen zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-749">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="2cd9d-750">Wenn der erforderliche Port von einem anderen Dienst auf einer der Loopback-Schnittstellen verwendet wird, tritt beim Starten von Kestrel ein Fehler auf.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-750">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="2cd9d-751">Wenn eine der Loopback-Schnittstellen aus anderen Gründen nicht verfügbar ist (meistens durch die fehlende Unterstützung von IPv6), protokolliert Kestrel eine Warnung.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-751">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="2cd9d-752">Host-Filterung</span><span class="sxs-lookup"><span data-stu-id="2cd9d-752">Host filtering</span></span>

<span data-ttu-id="2cd9d-753">Obwohl Kestrel die Konfiguration basierend auf Präfixe wie `http://example.com:5000` unterstützt, ignoriert Kestrel den Hostnamen weitgehend.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-753">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="2cd9d-754">Der Host `localhost` ist ein Sonderfall, der für die Bindung an Loopback-Adressen verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-754">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="2cd9d-755">Jeder Host, der keine explizite IP-Adresse ist, wird an alle öffentlichen IP-Adressen gebunden.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-755">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="2cd9d-756">`Host`-Header werden nicht überprüft.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-756">`Host` headers aren't validated.</span></span>

<span data-ttu-id="2cd9d-757">Verwenden Sie Middleware zum Filtern von Hosts, um dieses Problem zu umgehen.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-757">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="2cd9d-758">Middleware zum Filtern von Hosts wird im Paket [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) bereitgestellt, das im [Metapaket Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) enthalten ist (ASP.NET Core 2.1 oder 2.2).</span><span class="sxs-lookup"><span data-stu-id="2cd9d-758">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or 2.2).</span></span> <span data-ttu-id="2cd9d-759">Die Middleware wird von <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> hinzugefügt, wodurch <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*> aufgerufen wird:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-759">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="2cd9d-760">Die Middleware zum Filtern von Hosts ist standardmäßig deaktiviert.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-760">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="2cd9d-761">Wenn Sie die Middleware aktivieren möchten, definieren Sie einen `AllowedHosts`-Schlüssel in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-761">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="2cd9d-762">Der Wert ist eine durch Semikolons getrennte Liste von Hostnamen ohne Portnummern:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-762">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="2cd9d-763">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-763">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="2cd9d-764">[Middleware für weitergeleitete Header](xref:host-and-deploy/proxy-load-balancer) hat auch eine <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts>-Option.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-764">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="2cd9d-765">Middleware für weitergeleitete Header und Middleware zum Filtern von Hosts besitzen ähnliche Funktionen für unterschiedliche Szenarios.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-765">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="2cd9d-766">Legen Sie `AllowedHosts` mit Middleware für weitergeleitete Header fest, wenn der `Host`-Header beim Weiterleiten von Anforderungen mit einem Reverseproxyserver oder einem Lastenausgleichsmodul nicht beibehalten wird.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-766">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="2cd9d-767">Legen Sie `AllowedHosts` mit Middleware zum Filtern von Hosts fest, wenn Kestrel als öffentlicher Edgeserver verwendet oder der `Host`-Header direkt weitergeleitet wird.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-767">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="2cd9d-768">Weitere Informationen zu Middleware für weitergeleitete Header finden Sie unter <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-768">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="2cd9d-769">Einführung in Kestrel, dem plattformübergreifenden [Webserver für ASP.NET Core](xref:fundamentals/servers/index).</span><span class="sxs-lookup"><span data-stu-id="2cd9d-769">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="2cd9d-770">Kestrel ist der Webserver, der standardmäßig in ASP.NET Core-Projektvorlagen enthalten ist.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-770">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="2cd9d-771">Kestrel unterstützt die folgenden Szenarios:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-771">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="2cd9d-772">HTTPS</span><span class="sxs-lookup"><span data-stu-id="2cd9d-772">HTTPS</span></span>
* <span data-ttu-id="2cd9d-773">Nicht transparente Upgrades, die zum Aktivieren von [WebSockets](https://github.com/aspnet/websockets) verwendet werden</span><span class="sxs-lookup"><span data-stu-id="2cd9d-773">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="2cd9d-774">Unix-Sockets für eine hohe Leistung im Hintergrund von Nginx</span><span class="sxs-lookup"><span data-stu-id="2cd9d-774">Unix sockets for high performance behind Nginx</span></span>

<span data-ttu-id="2cd9d-775">Kestrel wird auf allen Plattformen und für alle Versionen unterstützt, die .NET Core unterstützt.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-775">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="2cd9d-776">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="2cd9d-776">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="2cd9d-777">Verwenden von Kestrel mit einem Reverseproxy</span><span class="sxs-lookup"><span data-stu-id="2cd9d-777">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="2cd9d-778">Kestrel kann eigenständig oder mit einem *Reverseproxyserver* wie z.B. [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org) oder [Apache](https://httpd.apache.org/) verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-778">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="2cd9d-779">Ein Reverseproxyserver empfängt HTTP-Anforderungen aus dem Netzwerk und leitet diese an Kestrel weiter.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-779">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="2cd9d-780">Kestrel bei Verwendung als Webserver mit direkter Internetverbindung:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-780">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Kestrel kommuniziert direkt und ohne Reverseproxyserver mit dem Internet](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="2cd9d-782">Kestrel bei Verwendung in einer Reverseproxykonfiguration:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-782">Kestrel used in a reverse proxy configuration:</span></span>

![Kestrel kommuniziert indirekt mit dem Internet über einen Reverseproxyserver wie IIS, Nginx oder Apache](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="2cd9d-784">Jede der beiden Konfigurationen – mit oder ohne einen Reverseproxyserver – stellt eine unterstützte Hostingkonfiguration dar.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-784">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="2cd9d-785">Bei Verwendung als Edgeserver ohne Reverseproxyserver unterstützt Kestrel die gemeinsame Nutzung der gleichen IP-Adresse und des gleichen Ports durch mehrere Prozesse nicht.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-785">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="2cd9d-786">Wenn Kestrel für das Lauschen an einem Port konfiguriert ist, verarbeitet Kestrel den gesamten Datenverkehr für diesen Port unabhängig von den `Host`-Headern der Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-786">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="2cd9d-787">Ein Reverseproxy, der Ports freigeben kann, kann Anforderungen an Kestrel über eine eindeutige IP und einen eindeutigen Port weiterleiten.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-787">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="2cd9d-788">Auch wenn kein Reverseproxyserver erforderlich ist, kann die Verwendung eines solchen empfehlenswert sein.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-788">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="2cd9d-789">Für einen Reverseproxy gilt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-789">A reverse proxy:</span></span>

* <span data-ttu-id="2cd9d-790">Er kann die verfügbar gemachten öffentlichen Oberflächen der von ihm gehosteten Apps einschränken.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-790">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="2cd9d-791">Er stellt eine zusätzliche Ebene für Konfiguration und Schutz bereit.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-791">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="2cd9d-792">Er lässt sich besser in die vorhandene Infrastruktur integrieren.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-792">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="2cd9d-793">Er vereinfacht die Konfiguration von Lastenausgleich und sicherer Kommunikation (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="2cd9d-793">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="2cd9d-794">Nur der Reverseproxyserver erfordert ein X.509-Zertifikat, und dieser Server kann mit den Servern der App im internen Netzwerk über einfaches HTTP kommunizieren.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-794">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="2cd9d-795">Das Hosting in einer Reverseproxykonfiguration erfordert [Hostfilterung](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="2cd9d-795">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a><span data-ttu-id="2cd9d-796">Verwenden von Kestrel in ASP.NET Core-Apps</span><span class="sxs-lookup"><span data-stu-id="2cd9d-796">How to use Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="2cd9d-797">Das Paket [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) ist im Metapaket [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) enthalten.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-797">The [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="2cd9d-798">ASP.NET Core-Projektvorlagen verwenden Kestrel standardmäßig.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-798">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="2cd9d-799">Der Vorlagencode in *Program.cs* ruft <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> auf, wodurch <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> im Hintergrund aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-799">In *Program.cs*, the template code calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> behind the scenes.</span></span>

<span data-ttu-id="2cd9d-800">Um zusätzliche Konfiguration nach dem Aufruf von `CreateDefaultBuilder` bereitzustellen, rufen Sie <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> auf:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-800">To provide additional configuration after calling `CreateDefaultBuilder`, call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            // Set properties and call methods on serverOptions
        });
```

<span data-ttu-id="2cd9d-801">Weitere Informationen zum `CreateDefaultBuilder` und zum Erstellen des Hosts finden Sie im Abschnitt *Einrichten eines Hosts* von <xref:fundamentals/host/web-host#set-up-a-host>.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-801">For more information on `CreateDefaultBuilder` and building the host, see the *Set up a host* section of <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

## <a name="kestrel-options"></a><span data-ttu-id="2cd9d-802">Kestrel-Optionen</span><span class="sxs-lookup"><span data-stu-id="2cd9d-802">Kestrel options</span></span>

<span data-ttu-id="2cd9d-803">Der Kestrel-Webserver verfügt über einschränkende Konfigurationsoptionen, die besonders nützlich bei Bereitstellungen mit Internetzugriff sind.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-803">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="2cd9d-804">Legen Sie Einschränkungen für die <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits>-Eigenschaft der <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>-Klasse fest.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-804">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="2cd9d-805">Die `Limits`-Eigenschaft enthält eine Instanz der <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>-Klasse.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-805">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="2cd9d-806">In den folgenden Beispielen wird der <xref:Microsoft.AspNetCore.Server.Kestrel.Core>-Namespace verwendet:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-806">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="2cd9d-807">Kestrel-Optionen, die in den folgenden Beispielen in C#-Code konfiguriert sind, können auch mit einem [Konfigurationsanbieter](xref:fundamentals/configuration/index) festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-807">Kestrel options, which are configured in C# code in the following examples, can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="2cd9d-808">Beispielsweise kann der Dateikonfigurationsanbieter die Kestrel-Konfiguration aus einer *appsettings.json*- oder *appsettings.{Umgebung}.json*-Datei laden:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-808">For example, the File Configuration Provider can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

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

<span data-ttu-id="2cd9d-809">Verwenden Sie **einen** der folgenden Ansätze:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-809">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="2cd9d-810">Konfigurieren Sie Kestrel in `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-810">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="2cd9d-811">Fügen Sie eine Instanz von `IConfiguration` in die `Startup`-Klasse ein.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-811">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="2cd9d-812">Im folgenden Beispiel wird davon ausgegangen, dass die eingefügte Konfiguration der `Configuration`-Eigenschaft zugewiesen wird.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-812">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="2cd9d-813">Laden Sie in `Startup.ConfigureServices` den Abschnitt `Kestrel` der Konfiguration in die Konfiguration von Kestrel:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-813">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

* <span data-ttu-id="2cd9d-814">Konfigurieren Sie Kestrel beim Erstellen des Hosts:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-814">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="2cd9d-815">Laden Sie in *Program.cs* den Abschnitt `Kestrel` der Konfiguration in die Konfiguration von Kestrel:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-815">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

<span data-ttu-id="2cd9d-816">Beide vorangehenden Ansätze funktionieren mit jedem [Konfigurationsanbieter](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="2cd9d-816">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="2cd9d-817">Keep-Alive-Timeout</span><span class="sxs-lookup"><span data-stu-id="2cd9d-817">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="2cd9d-818">Ruft das [Keep-Alive-Timeout](https://tools.ietf.org/html/rfc7230#section-6.5) ab oder legt es fest.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-818">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="2cd9d-819">Standardwert: 2 Minuten.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-819">Defaults to 2 minutes.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.KeepAliveTimeout = TimeSpan.FromMinutes(2);
        });
```

### <a name="maximum-client-connections"></a><span data-ttu-id="2cd9d-820">Maximale Anzahl der Clientverbindungen</span><span class="sxs-lookup"><span data-stu-id="2cd9d-820">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="2cd9d-821">Die maximale Anzahl von gleichzeitig geöffneten TCP-Verbindungen kann mithilfe von folgendem Code für die gesamte App festgelegt werden:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-821">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxConcurrentConnections = 100;
        });
```

<span data-ttu-id="2cd9d-822">Es gibt einen separaten Grenzwert für Verbindungen, die von HTTP oder HTTPS auf ein anderes Protokoll aktualisiert wurden (z.B. auf eine WebSockets-Anforderung).</span><span class="sxs-lookup"><span data-stu-id="2cd9d-822">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="2cd9d-823">Nachdem eine Verbindung aktualisiert wurde, zählt diese nicht mehr für den `MaxConcurrentConnections`-Grenzwert.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-823">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxConcurrentUpgradedConnections = 100;
        });
```

<span data-ttu-id="2cd9d-824">Die maximale Anzahl von Verbindungen ist standardmäßig nicht begrenzt (NULL).</span><span class="sxs-lookup"><span data-stu-id="2cd9d-824">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="2cd9d-825">Maximale Größe des Anforderungstexts</span><span class="sxs-lookup"><span data-stu-id="2cd9d-825">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="2cd9d-826">Die maximale Größe des Anforderungstexts beträgt standardmäßig 30.000.000 Byte, also ungefähr 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-826">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="2cd9d-827">Die empfohlene Methode zur Außerkraftsetzung des Grenzwerts in einer ASP.NET Core-MVC-App besteht im Verwenden des <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute>-Attributs in einer Aktionsmethode:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-827">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="2cd9d-828">Im folgenden Beispiel wird veranschaulicht, wie die Einschränkungen auf jeder Anforderung für die App konfiguriert werden:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-828">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxRequestBodySize = 10 * 1024;
        });
```

<span data-ttu-id="2cd9d-829">Außer Kraft setzen der Einstellung für eine bestimmte Anforderung in Middleware:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-829">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="2cd9d-830">Eine Ausnahme wird ausgelöst, wenn die App den Grenzwert einer Anforderung konfiguriert, nachdem die App bereits mit dem Lesen der Anforderung begonnen hat.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-830">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="2cd9d-831">Es gibt eine `IsReadOnly`-Eigenschaft, die angibt, wenn sich die `MaxRequestBodySize`-Eigenschaft im schreibgeschützten Zustand befindet, also wenn der Grenzwert nicht mehr konfiguriert werden kann.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-831">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="2cd9d-832">Wenn eine App [prozessextern](xref:host-and-deploy/iis/index#out-of-process-hosting-model) hinter dem [ASP.NET Core-Modul](xref:host-and-deploy/aspnet-core-module) ausgeführt wird, ist das Größenlimit von Kestrel für Anforderungstext deaktiviert, weil IIS dieses Limit bereits festlegt.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-832">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="2cd9d-833">Minimale Datenrate des Anforderungstexts</span><span class="sxs-lookup"><span data-stu-id="2cd9d-833">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="2cd9d-834">Kestrel überprüft sekündlich, ob Daten mit der angegebenen Rate in Bytes/Sekunde eingehen.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-834">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="2cd9d-835">Wenn die Rate den mindestens erforderlichen Wert unterschreitet, wird für die Verbindung wegen Timeout getrennt. Bei der Toleranzperiode handelt es sich um die Zeitspanne, die Kestrel dem Client gewährt, um die Senderate auf den mindestens erforderlichen Wert zu erhöhen. Die Rate wird währenddessen nicht überprüft.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-835">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="2cd9d-836">Diese Toleranzperiode beugt dem Trennen von Verbindungen vor, die Daten aufgrund eines langsamen TCP-Starts anfänglich mit einer niedrigen Rate senden.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-836">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="2cd9d-837">Die mindestens erforderliche Rate beträgt standardmäßig 240 Bytes/Sekunde mit einer Toleranzperiode von 5 Sekunden.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-837">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="2cd9d-838">Für die Antwort gilt ebenfalls eine mindestens erforderliche Rate.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-838">A minimum rate also applies to the response.</span></span> <span data-ttu-id="2cd9d-839">Der Code zum Festlegen des Grenzwerts für Anforderung und Antwort ist abgesehen von `RequestBody` oder `Response` in den Namen von Eigenschaften und Schnittstelle identisch.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-839">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="2cd9d-840">In diesem Beispiel wird veranschaulicht, wie Sie die mindestens erforderlichen Datenraten in *Program.cs* konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-840">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

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

### <a name="request-headers-timeout"></a><span data-ttu-id="2cd9d-841">Timeout für Anforderungsheader</span><span class="sxs-lookup"><span data-stu-id="2cd9d-841">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="2cd9d-842">Ruft die maximale Zeitspanne ab, während der der Server Anforderungsheader empfängt, oder legt diese fest.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-842">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="2cd9d-843">Der Standardwert beträgt 30 Sekunden.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-843">Defaults to 30 seconds.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.RequestHeadersTimeout = TimeSpan.FromMinutes(1);
        });
```

### <a name="synchronous-io"></a><span data-ttu-id="2cd9d-844">Synchrone E/A-Vorgänge</span><span class="sxs-lookup"><span data-stu-id="2cd9d-844">Synchronous I/O</span></span>

<span data-ttu-id="2cd9d-845"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> steuert, ob für Anforderung und Antwort synchrone E/A-Vorgänge zulässig sind.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-845"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous I/O is allowed for the request and response.</span></span> <span data-ttu-id="2cd9d-846">Der Standardwert ist `true`.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-846">The  default value is `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="2cd9d-847">Sehr viele blockierende synchrone E/A-Vorgänge können zu einem Ressourcenmangel im Threadpool führen, wodurch die App nicht mehr reagiert.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-847">A large number of blocking synchronous I/O operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="2cd9d-848">Aktivieren Sie `AllowSynchronousIO` nur bei Verwendung einer Bibliothek, die asynchrone E/A-Vorgänge nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-848">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous I/O.</span></span>

<span data-ttu-id="2cd9d-849">Das folgende Beispiel deaktiviert synchrone E/A-Vorgänge:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-849">The following example disables synchronous I/O:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.AllowSynchronousIO = false;
        });
```

<span data-ttu-id="2cd9d-850">Weitere Informationen zu anderen Kestrel-Optionen und -Einschränkungen finden Sie unter:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-850">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="2cd9d-851">Endpunktkonfiguration</span><span class="sxs-lookup"><span data-stu-id="2cd9d-851">Endpoint configuration</span></span>

<span data-ttu-id="2cd9d-852">Standardmäßig wird ASP.NET Core an Folgendes gebunden:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-852">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="2cd9d-853">`https://localhost:5001` (wenn ein lokales Entwicklungszertifikat vorhanden ist)</span><span class="sxs-lookup"><span data-stu-id="2cd9d-853">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="2cd9d-854">Verwenden Sie Folgendes zum Angeben der URLs:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-854">Specify URLs using the:</span></span>

* <span data-ttu-id="2cd9d-855">Die Umgebungsvariable `ASPNETCORE_URLS`</span><span class="sxs-lookup"><span data-stu-id="2cd9d-855">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="2cd9d-856">Das Befehlszeilenargument `--urls`</span><span class="sxs-lookup"><span data-stu-id="2cd9d-856">`--urls` command-line argument.</span></span>
* <span data-ttu-id="2cd9d-857">Den Hostkonfigurationsschlüssel `urls`</span><span class="sxs-lookup"><span data-stu-id="2cd9d-857">`urls` host configuration key.</span></span>
* <span data-ttu-id="2cd9d-858">Die Erweiterungsmethode `UseUrls`</span><span class="sxs-lookup"><span data-stu-id="2cd9d-858">`UseUrls` extension method.</span></span>

<span data-ttu-id="2cd9d-859">Der Wert, der mit diesen Ansätzen angegeben wird, kann mindestens ein HTTP- oder HTTPS-Endpunkt sein (HTTPS wenn ein Standardzertifikat verfügbar ist).</span><span class="sxs-lookup"><span data-stu-id="2cd9d-859">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="2cd9d-860">Konfigurieren Sie den Wert als eine durch Semikolons getrennte Liste (z.B. `"Urls": "http://localhost:8000;http://localhost:8001"`).</span><span class="sxs-lookup"><span data-stu-id="2cd9d-860">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="2cd9d-861">Weitere Informationen zu diesen Ansätzen finden Sie unter [Server-URLs](xref:fundamentals/host/web-host#server-urls) und [Außerkraftsetzen der Konfiguration](xref:fundamentals/host/web-host#override-configuration).</span><span class="sxs-lookup"><span data-stu-id="2cd9d-861">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="2cd9d-862">Ein Entwicklungszertifikat wird erstellt:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-862">A development certificate is created:</span></span>

* <span data-ttu-id="2cd9d-863">Wenn das [.NET Core SDK](/dotnet/core/sdk) installiert wird.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-863">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="2cd9d-864">Das [dev-cert-Tool](xref:aspnetcore-2.1#https) wird zum Erstellen eines Zertifikats verwendet.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-864">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="2cd9d-865">Einige Browser erfordern, dass Sie die explizite Berechtigung erteilen, dem lokalen Entwicklungszertifikat zu vertrauen.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-865">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="2cd9d-866">Projektvorlagen konfigurieren Apps, damit sie standardmäßig auf HTTPS ausgeführt werden und die [HTTPS-Umleitung und HSTS-Unterstützung](xref:security/enforcing-ssl) enthalten.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-866">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="2cd9d-867">Rufen Sie die <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*>- oder <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*>-Methode unter <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> auf, um URL-Präfixe und Ports für Kestrel zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-867">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="2cd9d-868">`UseUrls`, das Befehlszeilenargument `--urls`, der Hostkonfigurationsschlüssel `urls` und die Umgebungsvariable `ASPNETCORE_URLS` funktionieren ebenfalls, verfügen jedoch über Einschränkungen, die im Verlauf dieses Abschnitts erläutert werden (Ein Standardzertifikat muss für die HTTPS-Endpunktkonfiguration verfügbar sein).</span><span class="sxs-lookup"><span data-stu-id="2cd9d-868">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="2cd9d-869">`KestrelServerOptions`-Konfiguration:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-869">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="2cd9d-870">ConfigureEndpointDefaults(Action\<ListenOptions>)</span><span class="sxs-lookup"><span data-stu-id="2cd9d-870">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="2cd9d-871">Gibt die Konfiguration von `Action` zum Ausführen von jedem angegebenen Endpunkt an.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-871">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="2cd9d-872">Mehrmalige Aufrufe von `ConfigureEndpointDefaults` ersetzen vorherige Instanzen von `Action` mit der zuletzt angegebenen `Action`.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-872">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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
> <span data-ttu-id="2cd9d-873">Auf Endpunkte, die durch Aufrufen von <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **vor** dem Aufrufen von <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> erstellt werden, werden die Standardwerte nicht angewendet.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-873">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> won't have the defaults applied.</span></span>

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="2cd9d-874">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span><span class="sxs-lookup"><span data-stu-id="2cd9d-874">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="2cd9d-875">Gibt die Konfiguration von `Action` zum Ausführen von jedem HTTPS-Endpunkt an.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-875">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="2cd9d-876">Mehrmalige Aufrufe von `ConfigureHttpsDefaults` ersetzen vorherige Instanzen von `Action` mit der zuletzt angegebenen `Action`.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-876">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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
> <span data-ttu-id="2cd9d-877">Auf Endpunkte, die durch Aufrufen von <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **vor** dem Aufrufen von <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> erstellt werden, werden die Standardwerte nicht angewendet.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-877">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>

### <a name="configureiconfiguration"></a><span data-ttu-id="2cd9d-878">Configure(IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="2cd9d-878">Configure(IConfiguration)</span></span>

<span data-ttu-id="2cd9d-879">Erstellt ein Konfigurationsladeprogramm für das Einrichten von Kestrel, was <xref:Microsoft.Extensions.Configuration.IConfiguration> als Eingabe erfordert.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-879">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="2cd9d-880">Die Konfiguration muss auf den Konfigurationsabschnitt für Kestrel festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-880">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="2cd9d-881">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="2cd9d-881">ListenOptions.UseHttps</span></span>

<span data-ttu-id="2cd9d-882">Konfiguriert Kestrel zur Verwendung von HTTPS.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-882">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="2cd9d-883">`ListenOptions.UseHttps`-Erweiterungen:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-883">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="2cd9d-884">`UseHttps`: Hiermit wird Kestrel zur Verwendung von HTTPS mit dem Standardzertifikat konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-884">`UseHttps`: Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="2cd9d-885">Löst eine Ausnahme aus, wenn kein Standardzertifikat konfiguriert ist.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-885">Throws an exception if no default certificate is configured.</span></span>
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

<span data-ttu-id="2cd9d-886">`ListenOptions.UseHttps`-Parameter:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-886">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="2cd9d-887">`filename` entspricht dem Pfad und Dateinamen einer Zertifikatdatei relativ zu dem Verzeichnis, das die Inhaltsdateien der App enthält.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-887">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="2cd9d-888">`password` ist das für den Zugriff auf die X.509-Zertifikatsdaten erforderliche Kennwort.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-888">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="2cd9d-889">`configureOptions` ist eine `Action` zum Konfigurieren von `HttpsConnectionAdapterOptions`.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-889">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="2cd9d-890">Gibt `ListenOptions` zurück.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-890">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="2cd9d-891">`storeName` ist der Zertifikatspeicher, aus dem das Zertifikat geladen wird.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-891">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="2cd9d-892">`subject` ist der Name des Antragstellers für das Zertifikat.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-892">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="2cd9d-893">`allowInvalid` gibt an, ob ungültige Zertifikate berücksichtigt werden sollten, z.B. selbstsignierte Zertifikate.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-893">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="2cd9d-894">`location` ist der Speicherort, aus dem das Zertifikat geladen wird.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-894">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="2cd9d-895">`serverCertificate` ist das X.509-Zertifikat.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-895">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="2cd9d-896">In der Produktion muss HTTPS explizit konfiguriert sein.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-896">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="2cd9d-897">Zumindest muss ein Standardzertifikat angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-897">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="2cd9d-898">Die im Folgenden beschriebenen unterstützten Konfigurationen:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-898">Supported configurations described next:</span></span>

* <span data-ttu-id="2cd9d-899">Keine Konfiguration</span><span class="sxs-lookup"><span data-stu-id="2cd9d-899">No configuration</span></span>
* <span data-ttu-id="2cd9d-900">Ersetzen des Standardzertifikats aus der Konfiguration</span><span class="sxs-lookup"><span data-stu-id="2cd9d-900">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="2cd9d-901">Ändern des Standards im Code</span><span class="sxs-lookup"><span data-stu-id="2cd9d-901">Change the defaults in code</span></span>

<span data-ttu-id="2cd9d-902">*Keine Konfiguration*</span><span class="sxs-lookup"><span data-stu-id="2cd9d-902">*No configuration*</span></span>

<span data-ttu-id="2cd9d-903">Kestrel überwacht `http://localhost:5000` und `https://localhost:5001` (wenn ein Standardzertifikat verfügbar ist).</span><span class="sxs-lookup"><span data-stu-id="2cd9d-903">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="2cd9d-904">*Ersetzen des Standardzertifikats aus der Konfiguration*</span><span class="sxs-lookup"><span data-stu-id="2cd9d-904">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="2cd9d-905">`CreateDefaultBuilder` ruft `Configure(context.Configuration.GetSection("Kestrel"))` standardmäßig zum Laden der Kestrel-Konfiguration auf.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-905">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="2cd9d-906">Ein Standardkonfigurationsschema für HTTPS-App-Einstellungen ist für Kestrel verfügbar.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-906">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="2cd9d-907">Konfigurieren Sie mehrere Endpunkte, einschließlich der zu verwendenden URLs und Zertifikate aus einer Datei auf dem Datenträger oder einem Zertifikatspeicher.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-907">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="2cd9d-908">Die Vorgehensweise im folgenden *appsettings.json*-Beispiel:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-908">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="2cd9d-909">Legen Sie **AllowInvalid** auf `true` fest, um die Verwendung von ungültigen Zertifikaten zu erlauben (z.B. selbstsignierte Zertifikate).</span><span class="sxs-lookup"><span data-stu-id="2cd9d-909">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="2cd9d-910">Jeder HTTPS-Endpunkt, der kein Zertifikat angibt (im folgenden Beispiel **HttpsDefaultCert**), greift auf das unter **Zertifikate** > **Standard** festgelegte Zertifikat oder das Entwicklungszertifikat zurück.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-910">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

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

<span data-ttu-id="2cd9d-911">Alternativ zur Verwendung von **Pfad** und **Kennwort** für alle Zertifikatknoten können Sie das Zertifikat mithilfe von Zertifikatspeicherfeldern angeben.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-911">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="2cd9d-912">Das Zertifikat unter **Zertifikate** > **Standard** kann beispielweise wie folgt angegeben werden:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-912">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="2cd9d-913">Schema-Hinweise:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-913">Schema notes:</span></span>

* <span data-ttu-id="2cd9d-914">Bei den Namen der Endpunkte wird die Groß-/Kleinschreibung nicht beachtet.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-914">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="2cd9d-915">Zum Beispiel sind `HTTPS` und `Https` gültig.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-915">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="2cd9d-916">Der Parameter `Url` ist für jeden Endpunkt erforderlich.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-916">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="2cd9d-917">Das Format für diesen Parameter ist identisch mit dem allgemeinen Konfigurationsparameter `Urls`, mit der Ausnahme, dass er auf einen einzelnen Wert begrenzt ist.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-917">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="2cd9d-918">Diese Endpunkte ersetzen die in der allgemeinen `Urls`-Konfiguration festgelegten Endpunkte, anstatt zu ihnen hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-918">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="2cd9d-919">Endpunkte, die über `Listen` in Code definiert werden, werden den im Konfigurationsabschnitt definierten Endpunkten hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-919">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="2cd9d-920">Der `Certificate`-Abschnitt ist optional.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-920">The `Certificate` section is optional.</span></span> <span data-ttu-id="2cd9d-921">Wenn der `Certificate`-Abschnitt nicht angegeben ist, werden die in früheren Szenarios definierten Standardwerte verwendet.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-921">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="2cd9d-922">Wenn keine Standardwerte verfügbar sind, löst der Server eine Ausnahme aus und startet nicht.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-922">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="2cd9d-923">Der `Certificate`-Abschnitt unterstützt die Zertifikate **Pfad**&ndash;**Kennwort** und **Subject**&ndash;**Store** (Antragsteller–Speicher).</span><span class="sxs-lookup"><span data-stu-id="2cd9d-923">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="2cd9d-924">Auf diese Weise kann eine beliebige Anzahl von Endpunkten definiert werden, solange Sie keine Portkonflikte verursachen.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-924">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="2cd9d-925">`options.Configure(context.Configuration.GetSection("{SECTION}"))` gibt `KestrelConfigurationLoader` mit der Methode `.Endpoint(string name, listenOptions => { })` zurück, die dazu verwendet werden kann, die Einstellungen eines Endpunkts zu ergänzen:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-925">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

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

<span data-ttu-id="2cd9d-926">Auf `KestrelServerOptions.ConfigurationLoader` kann direkt zugegriffen werden, um die Iteration auf dem vorhandenen Ladeprogramm fortzusetzen, etwa auf dem von <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> bereitgestellten Ladeprogramm.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-926">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="2cd9d-927">Der Konfigurationsabschnitt ist für jeden Endpunkt in den Optionen der Methode `Endpoint` verfügbar, sodass benutzerdefinierte Einstellungen gelesen werden können.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-927">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="2cd9d-928">Mehrere Konfigurationen können durch erneutes Aufrufen von `options.Configure(context.Configuration.GetSection("{SECTION}"))` mit einem anderen Abschnitt geladen werden.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-928">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="2cd9d-929">Sofern `Load` nicht in einer vorherigen Instanz explizit aufgerufen wird, wird nur die letzte Konfiguration verwendet.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-929">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="2cd9d-930">Das Metapaket ruft `Load` nicht auf, sodass der Abschnitt mit der Standardkonfiguration ersetzt werden kann.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-930">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="2cd9d-931">`KestrelConfigurationLoader` spiegelt die API-Familie `Listen` von `KestrelServerOptions` als `Endpoint`-Überladungen, weshalb Code und Konfigurationsendpunkte am selben Ort konfiguriert werden können.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-931">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="2cd9d-932">Diese Überladungen verwenden keine Namen und nutzen nur Standardeinstellungen aus der Konfiguration.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-932">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="2cd9d-933">*Ändern des Standards im Code*</span><span class="sxs-lookup"><span data-stu-id="2cd9d-933">*Change the defaults in code*</span></span>

<span data-ttu-id="2cd9d-934">`ConfigureEndpointDefaults` und `ConfigureHttpsDefaults` können zum Ändern der Standardeinstellungen für `ListenOptions` und `HttpsConnectionAdapterOptions` verwendet werden, einschließlich der Standardzertifikate, die im vorherigen Szenario festgelegt wurden.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-934">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="2cd9d-935">`ConfigureEndpointDefaults` und `ConfigureHttpsDefaults` sollten aufgerufen werden, bevor Endpunkte konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-935">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

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

<span data-ttu-id="2cd9d-936">*Kestrel-Unterstützung für SNI*</span><span class="sxs-lookup"><span data-stu-id="2cd9d-936">*Kestrel support for SNI*</span></span>

<span data-ttu-id="2cd9d-937">Die [Servernamensanzeige (SNI)](https://tools.ietf.org/html/rfc6066#section-3) kann zum Hosten mehrerer Domänen auf der gleichen IP-Adresse und dem gleichen Port verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-937">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="2cd9d-938">Damit die Servernamensanzeige funktioniert, sendet der Client während des TLS-Handshakes den Hostnamen für die sichere Sitzung an den Server, sodass der Server das richtige Zertifikat bereitstellen kann.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-938">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="2cd9d-939">Der Client verwendet das beigestellte Zertifikat für die verschlüsselte Kommunikation mit dem Server während der sicheren Sitzung nach dem TLS-Handshake.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-939">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="2cd9d-940">Kestrel unterstützt die Servernamensanzeige über den `ServerCertificateSelector`-Rückruf.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-940">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="2cd9d-941">Der Rückruf wird für jede Verbindung einmal aufgerufen, um der App zu ermöglichen, den Hostnamen zu überprüfen und das entsprechende Zertifikat auszuwählen.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-941">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="2cd9d-942">Für die Unterstützung der Servernamensanzeige benötigen Sie Folgendes:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-942">SNI support requires:</span></span>

* <span data-ttu-id="2cd9d-943">Wird auf dem Zielframework `netcoreapp2.1` oder höher ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-943">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="2cd9d-944">In `net461` oder höher, wird der Rückruf aufgerufen, `name` ist aber immer `null`.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-944">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="2cd9d-945">`name` ist auch `null`, wenn der Client den Hostnamenparameter nicht im TLS-Handshake angibt.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-945">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="2cd9d-946">Alle Websites werden in derselben Kestrel-Instanz ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-946">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="2cd9d-947">Kestrel unterstützt ohne Reverseproxy keine gemeinsame IP-Adresse und keinen gemeinsamen Port für mehrere Instanzen.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-947">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

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

### <a name="connection-logging"></a><span data-ttu-id="2cd9d-948">Verbindungsprotokollierung</span><span class="sxs-lookup"><span data-stu-id="2cd9d-948">Connection logging</span></span>

<span data-ttu-id="2cd9d-949">Rufen Sie <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> auf, um Protokolle auf Debugebene für die Kommunikation auf Byteebene für eine Verbindung auszugeben.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-949">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="2cd9d-950">Die Verbindungsprotokollierung ist beim Beheben von Problemen bei der Low-Level-Kommunikation hilfreich, wie z. B. bei der TLS-Verschlüsselung und bei Proxys.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-950">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="2cd9d-951">Wenn `UseConnectionLogging` vor `UseHttps` platziert wird, wird der verschlüsselte Datenverkehr protokolliert.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-951">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="2cd9d-952">Wenn `UseConnectionLogging` nach `UseHttps` platziert wird, wird der entschlüsselte Datenverkehr protokolliert.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-952">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="2cd9d-953">Binden an einen TCP-Socket</span><span class="sxs-lookup"><span data-stu-id="2cd9d-953">Bind to a TCP socket</span></span>

<span data-ttu-id="2cd9d-954">Die <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*>-Methode wird an ein TCP-Socket gebunden, und ein Lambdaausdruck einer Option lässt die Konfiguration des X.509-Zertifikats zu:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-954">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

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

<span data-ttu-id="2cd9d-955">Im Beispiel wird HTTPS für einen Endpunkt mit <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions> konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-955">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="2cd9d-956">Verwenden Sie die gleiche API zum Konfigurieren anderer Kestrel-Einstellungen für bestimmte Endpunkte.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-956">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="2cd9d-957">Binden an einen Unix-Socket</span><span class="sxs-lookup"><span data-stu-id="2cd9d-957">Bind to a Unix socket</span></span>

<span data-ttu-id="2cd9d-958">Wie in diesem Beispiel dargestellt, lauschen Sie an einem Unix-Socket mit <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*>, um eine verbesserte Leistung mit Nginx zu erzielen:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-958">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

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

* <span data-ttu-id="2cd9d-959">Legen Sie in der Nginx-Konfigurationsdatei den Eintrag `server` > `location` > `proxy_pass` auf `http://unix:/tmp/{KESTREL SOCKET}:/;` fest.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-959">In the Nginx confiuguration file, set the `server` > `location` > `proxy_pass` entry to `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span></span> <span data-ttu-id="2cd9d-960">`{KESTREL SOCKET}` ist der Name des für <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> bereitgestellten Socket (zum Beispiel `kestrel-test.sock` im vorherigen Beispiel).</span><span class="sxs-lookup"><span data-stu-id="2cd9d-960">`{KESTREL SOCKET}` is the name of the socket provided to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (for example, `kestrel-test.sock` in the preceding example).</span></span>
* <span data-ttu-id="2cd9d-961">Stellen Sie sicher, dass der Socket von Nginx beschreibbar ist (z. B. `chmod go+w /tmp/kestrel-test.sock`).</span><span class="sxs-lookup"><span data-stu-id="2cd9d-961">Ensure that the socket is writeable by Nginx (for example, `chmod go+w /tmp/kestrel-test.sock`).</span></span> 

### <a name="port-0"></a><span data-ttu-id="2cd9d-962">Port 0</span><span class="sxs-lookup"><span data-stu-id="2cd9d-962">Port 0</span></span>

<span data-ttu-id="2cd9d-963">Wenn die Portnummer `0` angegeben wird, wird Kestrel dynamisch an einen verfügbaren Port gebunden.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-963">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="2cd9d-964">Im folgenden Beispiel wird veranschaulicht, wie bestimmt werden kann, für welchen Port Kestrel zur Laufzeit eine Bindung erstellt hat:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-964">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="2cd9d-965">Wenn die App ausgeführt wird, gibt das Ausgabefenster der Konsole den dynamischen Port an, über den die App erreicht werden kann:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-965">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="2cd9d-966">Einschränkungen</span><span class="sxs-lookup"><span data-stu-id="2cd9d-966">Limitations</span></span>

<span data-ttu-id="2cd9d-967">Konfigurieren Sie Endpunkte mithilfe der folgenden Ansätze:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-967">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="2cd9d-968">Befehlszeilenargument `--urls`</span><span class="sxs-lookup"><span data-stu-id="2cd9d-968">`--urls` command-line argument</span></span>
* <span data-ttu-id="2cd9d-969">Hostkonfigurationsschlüssel `urls`</span><span class="sxs-lookup"><span data-stu-id="2cd9d-969">`urls` host configuration key</span></span>
* <span data-ttu-id="2cd9d-970">Umgebungsvariable `ASPNETCORE_URLS`</span><span class="sxs-lookup"><span data-stu-id="2cd9d-970">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="2cd9d-971">Diese Methoden sind nützlich, wenn Ihr Code mit anderen Servern als Kestrel funktionieren soll.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-971">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="2cd9d-972">Beachten Sie jedoch die folgenden Einschränkungen:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-972">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="2cd9d-973">HTTPS kann nicht mit diesen Ansätzen verwendet werden, außer ein Standardzertifikat wird in der HTTPS-Endpunktkonfiguration angegeben (z.B. wenn Sie wie zuvor in diesem Artikel gezeigt die `KestrelServerOptions`-Konfiguration oder eine Konfigurationsdatei verwenden).</span><span class="sxs-lookup"><span data-stu-id="2cd9d-973">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="2cd9d-974">Wenn die Ansätze `Listen` und `UseUrls` gleichzeitig verwendet werden, überschreiben die `Listen`-Endpunkte die `UseUrls`-Endpunkte.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-974">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="2cd9d-975">IIS-Endpunktkonfiguration</span><span class="sxs-lookup"><span data-stu-id="2cd9d-975">IIS endpoint configuration</span></span>

<span data-ttu-id="2cd9d-976">Bei der Verwendung von IIS werden die URL-Bindungen für IIS-Überschreibungsbindungen durch `Listen` oder `UseUrls` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-976">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="2cd9d-977">Weitere Informationen finden Sie im Artikel [ASP.NET Core-Modul](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="2cd9d-977">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

## <a name="transport-configuration"></a><span data-ttu-id="2cd9d-978">Transportkonfiguration</span><span class="sxs-lookup"><span data-stu-id="2cd9d-978">Transport configuration</span></span>

<span data-ttu-id="2cd9d-979">Mit dem Release von ASP.NET Core 2.1 basiert der Standardtransport von Kestrel nicht mehr auf Libuv, sondern auf verwalteten Sockets.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-979">With the release of ASP.NET Core 2.1, Kestrel's default transport is no longer based on Libuv but instead based on managed sockets.</span></span> <span data-ttu-id="2cd9d-980">Dies stellt einen Breaking Change für ASP.NET Core 2.0-Apps dar, die auf Version 2.1 upgraden, <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> aufrufen und von einem der folgenden Pakete abhängig sind:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-980">This is a breaking change for ASP.NET Core 2.0 apps upgrading to 2.1 that call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> and depend on either of the following packages:</span></span>

* <span data-ttu-id="2cd9d-981">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (direkter Paketverweis)</span><span class="sxs-lookup"><span data-stu-id="2cd9d-981">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (direct package reference)</span></span>
* [<span data-ttu-id="2cd9d-982">Microsoft.AspNetCore.App</span><span class="sxs-lookup"><span data-stu-id="2cd9d-982">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)

<span data-ttu-id="2cd9d-983">Für Projekte, die den Einsatz von Libuv erfordern:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-983">For projects that require the use of Libuv:</span></span>

* <span data-ttu-id="2cd9d-984">Fügen Sie für das [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/)-Paket eine Abhängigkeit auf die Projektdatei der App hinzu:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-984">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* <span data-ttu-id="2cd9d-985">Rufen Sie <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> auf:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-985">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span></span>

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

### <a name="url-prefixes"></a><span data-ttu-id="2cd9d-986">URL-Präfixe</span><span class="sxs-lookup"><span data-stu-id="2cd9d-986">URL prefixes</span></span>

<span data-ttu-id="2cd9d-987">Bei der Verwendung von `UseUrls`, dem Befehlszeilenargument `--urls`, dem Hostkonfigurationsschlüssel `urls` oder der Umgebungsvariable `ASPNETCORE_URLS` können die URL-Präfixe in den folgenden Formaten vorliegen.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-987">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="2cd9d-988">Nur HTTP-URL-Präfixe sind gültig.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-988">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="2cd9d-989">Kestrel unterstützt HTTPS nicht beim Konfigurieren von URL-Bindungen mit `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-989">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="2cd9d-990">IPv4-Adresse mit Portnummer</span><span class="sxs-lookup"><span data-stu-id="2cd9d-990">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="2cd9d-991">Bei `0.0.0.0` handelt es sich um einen Sonderfall, für den eine Bindung an alle IPv4-Adressen erfolgt.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-991">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="2cd9d-992">IPv6-Adresse mit Portnummer</span><span class="sxs-lookup"><span data-stu-id="2cd9d-992">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="2cd9d-993">`[::]` stellt das Äquivalent von IPv6 zu `0.0.0.0` für IPv4 dar.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-993">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="2cd9d-994">Hostname mit Portnummer</span><span class="sxs-lookup"><span data-stu-id="2cd9d-994">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="2cd9d-995">Hostnamen, `*` und `+` sind nicht spezifisch.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-995">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="2cd9d-996">Alle Elemente, die nicht als gültige IP-Adresse oder `localhost` erkannt werden, werden an alle IPv4- und IPv6-IP-Adressen gebunden.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-996">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="2cd9d-997">Verwenden Sie [HTTP.sys](xref:fundamentals/servers/httpsys) oder einen Reverseproxyserver zum Binden verschiedener Hostnamen an verschiedene ASP.NET Core-Apps auf demselben Port, z.B. IIS, Nginx oder Apache.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-997">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="2cd9d-998">Das Hosting in einer Reverseproxykonfiguration erfordert [Hostfilterung](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="2cd9d-998">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="2cd9d-999">`localhost`-Hostname mit Portnummer oder Loopback-IP mit Portnummer</span><span class="sxs-lookup"><span data-stu-id="2cd9d-999">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="2cd9d-1000">Wenn `localhost` angegeben ist, versucht Kestrel, eine Bindung zu IPv4- und IPv6-Loopback-Schnittstellen zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-1000">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="2cd9d-1001">Wenn der erforderliche Port von einem anderen Dienst auf einer der Loopback-Schnittstellen verwendet wird, tritt beim Starten von Kestrel ein Fehler auf.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-1001">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="2cd9d-1002">Wenn eine der Loopback-Schnittstellen aus anderen Gründen nicht verfügbar ist (meistens durch die fehlende Unterstützung von IPv6), protokolliert Kestrel eine Warnung.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-1002">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="2cd9d-1003">Host-Filterung</span><span class="sxs-lookup"><span data-stu-id="2cd9d-1003">Host filtering</span></span>

<span data-ttu-id="2cd9d-1004">Obwohl Kestrel die Konfiguration basierend auf Präfixe wie `http://example.com:5000` unterstützt, ignoriert Kestrel den Hostnamen weitgehend.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-1004">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="2cd9d-1005">Der Host `localhost` ist ein Sonderfall, der für die Bindung an Loopback-Adressen verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-1005">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="2cd9d-1006">Jeder Host, der keine explizite IP-Adresse ist, wird an alle öffentlichen IP-Adressen gebunden.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-1006">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="2cd9d-1007">`Host`-Header werden nicht überprüft.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-1007">`Host` headers aren't validated.</span></span>

<span data-ttu-id="2cd9d-1008">Verwenden Sie Middleware zum Filtern von Hosts, um dieses Problem zu umgehen.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-1008">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="2cd9d-1009">Middleware zum Filtern von Hosts wird im Paket [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) bereitgestellt, das im [Metapaket Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) enthalten ist (ASP.NET Core 2.1 oder 2.2).</span><span class="sxs-lookup"><span data-stu-id="2cd9d-1009">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or 2.2).</span></span> <span data-ttu-id="2cd9d-1010">Die Middleware wird von <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> hinzugefügt, wodurch <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*> aufgerufen wird:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-1010">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="2cd9d-1011">Die Middleware zum Filtern von Hosts ist standardmäßig deaktiviert.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-1011">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="2cd9d-1012">Wenn Sie die Middleware aktivieren möchten, definieren Sie einen `AllowedHosts`-Schlüssel in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-1012">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="2cd9d-1013">Der Wert ist eine durch Semikolons getrennte Liste von Hostnamen ohne Portnummern:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-1013">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="2cd9d-1014">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-1014">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="2cd9d-1015">[Middleware für weitergeleitete Header](xref:host-and-deploy/proxy-load-balancer) hat auch eine <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts>-Option.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-1015">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="2cd9d-1016">Middleware für weitergeleitete Header und Middleware zum Filtern von Hosts besitzen ähnliche Funktionen für unterschiedliche Szenarios.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-1016">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="2cd9d-1017">Legen Sie `AllowedHosts` mit Middleware für weitergeleitete Header fest, wenn der `Host`-Header beim Weiterleiten von Anforderungen mit einem Reverseproxyserver oder einem Lastenausgleichsmodul nicht beibehalten wird.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-1017">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="2cd9d-1018">Legen Sie `AllowedHosts` mit Middleware zum Filtern von Hosts fest, wenn Kestrel als öffentlicher Edgeserver verwendet oder der `Host`-Header direkt weitergeleitet wird.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-1018">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="2cd9d-1019">Weitere Informationen zu Middleware für weitergeleitete Header finden Sie unter <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-1019">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

## <a name="http11-request-draining"></a><span data-ttu-id="2cd9d-1020">Leeren von HTTP/1.1-Anforderungen</span><span class="sxs-lookup"><span data-stu-id="2cd9d-1020">HTTP/1.1 request draining</span></span>

<span data-ttu-id="2cd9d-1021">Das Öffnen von HTTP-Verbindungen ist zeitaufwendig.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-1021">Opening HTTP connections is time consuming.</span></span> <span data-ttu-id="2cd9d-1022">Bei HTTPS ist es zudem ressourcenintensiv.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-1022">For HTTPS, it's also resource intensive.</span></span> <span data-ttu-id="2cd9d-1023">Daher versucht Kestrel, Verbindungen gemäß dem HTTP/1.1-Protokoll wiederzuverwenden.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-1023">Therefore, Kestrel tries to reuse connections per the HTTP/1.1 protocol.</span></span> <span data-ttu-id="2cd9d-1024">Ein Anforderungstext muss vollständig genutzt worden sein, damit die Verbindung wiederverwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-1024">A request body must be fully consumed to allow the connection to be reused.</span></span> <span data-ttu-id="2cd9d-1025">Die App nutzt nicht immer den Anforderungstext, z. B. bei einer `POST`-Anforderung, bei der der Server eine Umleitung oder 404-Antwort zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-1025">The app doesn't always consume the request body, such as a `POST` requests where the server returns a redirect or 404 response.</span></span> <span data-ttu-id="2cd9d-1026">Im Fall der `POST`-Umleitung:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-1026">In the `POST`-redirect case:</span></span>

* <span data-ttu-id="2cd9d-1027">Der Client hat möglicherweise bereits einen Teil der `POST`-Daten gesendet.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-1027">The client may already have sent part of the `POST` data.</span></span>
* <span data-ttu-id="2cd9d-1028">Der Server schreibt die 301-Antwort.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-1028">The server writes the 301 response.</span></span>
* <span data-ttu-id="2cd9d-1029">Die Verbindung kann erst dann für eine neue Anforderung verwendet werden, wenn die `POST`-Daten im vorherigen Anforderungstext vollständig gelesen wurden.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-1029">The connection can't be used for a new request until the `POST` data from the previous request body has been fully read.</span></span>
* <span data-ttu-id="2cd9d-1030">Kestrel versucht, den Anforderungstext zu leeren.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-1030">Kestrel tries to drain the request body.</span></span> <span data-ttu-id="2cd9d-1031">Leeren des Anforderungstexts bedeutet, die Daten zu lesen und zu verwerfen, ohne sie zu verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-1031">Draining the request body means reading and discarding the data without processing it.</span></span>

<span data-ttu-id="2cd9d-1032">Beim Leerungsvorgang wird ein Kompromiss zwischen der Möglichkeit der Wiederverwendung der Verbindung und der Dauer gefunden, die zum Leeren der verbleibenden Daten benötigt wird:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-1032">The draining process makes a tradoff between allowing the connection to be reused and the time it takes to drain any remaining data:</span></span>

* <span data-ttu-id="2cd9d-1033">Für das Leeren gilt ein Zeitlimit von fünf Sekunden, das nicht konfigurierbar ist.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-1033">Draining has a timeout of five seconds, which isn't configurable.</span></span>
* <span data-ttu-id="2cd9d-1034">Wenn vor dem Zeitlimit nicht alle durch den Header `Content-Length` oder `Transfer-Encoding` angegebenen Daten gelesen wurden, wird die Verbindung geschlossen.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-1034">If all of the data specified by the `Content-Length` or `Transfer-Encoding` header hasn't been read before the timeout, the connection is closed.</span></span>

<span data-ttu-id="2cd9d-1035">Mitunter möchten Sie die Anforderung sofort beenden, entweder bevor oder nachdem die Antwort geschrieben wurde.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-1035">Sometimes you may want to terminate the request immediately, before or after writing the response.</span></span> <span data-ttu-id="2cd9d-1036">Beispielsweise können für Clients restriktive Datenobergrenzen gelten, sodass das Begrenzen hochgeladener Daten Priorität haben kann.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-1036">For example, clients may have restrictive data caps, so limiting uploaded data might be a priority.</span></span> <span data-ttu-id="2cd9d-1037">Um in solchen Fällen eine Anforderung zu beenden, rufen Sie [HttpContext.abort](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A) in einem Controller, eine Razor Page oder Middleware auf.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-1037">In such cases to terminate a request, call [HttpContext.Abort](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A) from a controller, Razor Page, or middleware.</span></span>

<span data-ttu-id="2cd9d-1038">Gegen den Aufruf von `Abort` gibt es Vorbehalte:</span><span class="sxs-lookup"><span data-stu-id="2cd9d-1038">There are caveats to calling `Abort`:</span></span>

* <span data-ttu-id="2cd9d-1039">Das Erstellen neuer Verbindungen kann langsam und aufwendig sein.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-1039">Creating new connections can be slow and expensive.</span></span>
* <span data-ttu-id="2cd9d-1040">Es gibt keine Garantie, dass der Client die Antwort gelesen hat, bevor die Verbindung geschlossen wird.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-1040">There's no guarantee that the client has read the response before the connection closes.</span></span>
* <span data-ttu-id="2cd9d-1041">Das Aufrufen von `Abort` sollte selten erfolgen und schweren Fehlerfällen und nicht gewöhnlichen Fehlern vorbehalten sein.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-1041">Calling `Abort` should be rare and reserved for severe error cases, not common errors.</span></span>
  * <span data-ttu-id="2cd9d-1042">Rufen Sie `Abort` nur dann auf, wenn ein konkretes Problem gelöst werden muss.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-1042">Only call `Abort` when a specific problem needs to be solved.</span></span> <span data-ttu-id="2cd9d-1043">Rufen Sie beispielsweise `Abort` auf, wenn böswillige Clients versuchen, Daten per `POST` abzurufen, oder wenn es einen Fehler im Clientcode gibt, der umfangreiche oder zahlreiche Anforderungen verursacht.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-1043">For example, call `Abort` if malicious clients are trying to `POST` data or when there's a bug in client code that causes large or numerous requests.</span></span>
  * <span data-ttu-id="2cd9d-1044">Rufen Sie `Abort` nicht für gewöhnliche Fehlersituationen auf, wie z. B. HTTP 404 (Nicht gefunden).</span><span class="sxs-lookup"><span data-stu-id="2cd9d-1044">Don't call `Abort` for common error situations, such as HTTP 404 (Not Found).</span></span>

<span data-ttu-id="2cd9d-1045">Durch den Aufruf von [HttpResponse.CompleteAsync](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A) vor dem Aufruf von `Abort` wird sichergestellt, dass der Server das Schreiben der Antwort abgeschlossen hat.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-1045">Calling [HttpResponse.CompleteAsync](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A) before calling `Abort` ensures that the server has completed writing the response.</span></span> <span data-ttu-id="2cd9d-1046">Das Clientverhalten ist jedoch nicht vorhersagbar, und es kann sein, dass die Antwort nicht gelesen wird, bevor die Verbindung abgebrochen wird.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-1046">However, client behavior isn't predictable and they may not read the response before the connection is aborted.</span></span>

<span data-ttu-id="2cd9d-1047">Dieser Prozess bei HTTP/2 ist anders, da das Protokoll den Abbruch einzelner Anforderungsströme ohne Schließen der Verbindung unterstützt.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-1047">This process is different for HTTP/2 because the protocol supports aborting individual request streams without closing the connection.</span></span> <span data-ttu-id="2cd9d-1048">Das fünfsekündige Zeitlimit für das Leeren gilt nicht.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-1048">The five second drain timeout doesn't apply.</span></span> <span data-ttu-id="2cd9d-1049">Wenn nach dem Fertigstellen einer Antwort ungelesene Anforderungstextdaten vorhanden sind, sendet der Server einen HTTP/2-RST-Datenrahmen.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-1049">If there's any unread request body data after completing a response, then the server sends an HTTP/2 RST frame.</span></span> <span data-ttu-id="2cd9d-1050">Zusätzliche Datenrahmen im Anforderungstext werden ignoriert.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-1050">Additional request body data frames are ignored.</span></span>

<span data-ttu-id="2cd9d-1051">Wenn möglich, ist es für Clients besser, den Anforderungsheader [Expect: 100-continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) zu verwenden und auf die Antwort des Servers zu warten, bevor mit dem Senden des Anforderungstexts begonnen wird.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-1051">If possible, it's better for clients to utilize the [Expect: 100-continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) request header and wait for the server to respond before starting to send the request body.</span></span> <span data-ttu-id="2cd9d-1052">Das gibt dem Client die Gelegenheit, die Antwort zu prüfen und abzubrechen, bevor nicht benötigte Daten gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-1052">That gives the client an opportunity to examine the response and abort before sending unneeded data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2cd9d-1053">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="2cd9d-1053">Additional resources</span></span>

* <span data-ttu-id="2cd9d-1054">Bei der Verwendung von UNIX-Sockets unter Linux wird der Socket beim Herunterfahren der App nicht automatisch gelöscht.</span><span class="sxs-lookup"><span data-stu-id="2cd9d-1054">When using UNIX sockets on Linux, the socket is not automatically deleted on app shut down.</span></span> <span data-ttu-id="2cd9d-1055">Weitere Informationen finden Sie in [diesem GitHub-Issue](https://github.com/dotnet/aspnetcore/issues/14134).</span><span class="sxs-lookup"><span data-stu-id="2cd9d-1055">For more information, see [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/14134).</span></span>
* <xref:test/troubleshoot>
* <xref:security/enforcing-ssl>
* <xref:host-and-deploy/proxy-load-balancer>
* [<span data-ttu-id="2cd9d-1056">RFC 7230: Message Syntax and Routing (Abschnitt 5.4: Host)</span><span class="sxs-lookup"><span data-stu-id="2cd9d-1056">RFC 7230: Message Syntax and Routing (Section 5.4: Host)</span></span>](https://tools.ietf.org/html/rfc7230#section-5.4)
