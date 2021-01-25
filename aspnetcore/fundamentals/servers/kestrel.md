---
title: Implementierung des Webservers Kestrel in ASP.NET Core
author: rick-anderson
description: Einführung in Kestrel, dem plattformübergreifenden Webserver für ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
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
uid: fundamentals/servers/kestrel
ms.openlocfilehash: d53cafb605939fd85bdbb71b2fbf13e7bd7a9b7b
ms.sourcegitcommit: cb984e0d7dc23a88c3a4121f23acfaea0acbfe1e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/19/2021
ms.locfileid: "98570996"
---
# <a name="kestrel-web-server-implementation-in-aspnet-core"></a><span data-ttu-id="c7cf9-103">Implementierung des Webservers Kestrel in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c7cf9-103">Kestrel web server implementation in ASP.NET Core</span></span>

<span data-ttu-id="c7cf9-104">Von [Tom Dykstra](https://github.com/tdykstra), [Chris Ross](https://github.com/Tratcher) und [Stephen Halter](https://twitter.com/halter73)</span><span class="sxs-lookup"><span data-stu-id="c7cf9-104">By [Tom Dykstra](https://github.com/tdykstra), [Chris Ross](https://github.com/Tratcher), and [Stephen Halter](https://twitter.com/halter73)</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="c7cf9-105">Einführung in Kestrel, dem plattformübergreifenden [Webserver für ASP.NET Core](xref:fundamentals/servers/index).</span><span class="sxs-lookup"><span data-stu-id="c7cf9-105">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="c7cf9-106">Kestrel ist der Webserver, der standardmäßig in ASP.NET Core-Projektvorlagen enthalten und aktiviert ist.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-106">Kestrel is the web server that's included and enabled by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="c7cf9-107">Kestrel unterstützt die folgenden Szenarios:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-107">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="c7cf9-108">HTTPS</span><span class="sxs-lookup"><span data-stu-id="c7cf9-108">HTTPS</span></span>
* <span data-ttu-id="c7cf9-109">[HTTP/2](xref:fundamentals/servers/kestrel/http2) (außer unter macOS&dagger;)</span><span class="sxs-lookup"><span data-stu-id="c7cf9-109">[HTTP/2](xref:fundamentals/servers/kestrel/http2) (except on macOS&dagger;)</span></span>
* <span data-ttu-id="c7cf9-110">Nicht transparente Upgrades, die zum Aktivieren von [WebSockets](xref:fundamentals/websockets) verwendet werden</span><span class="sxs-lookup"><span data-stu-id="c7cf9-110">Opaque upgrade used to enable [WebSockets](xref:fundamentals/websockets)</span></span>
* <span data-ttu-id="c7cf9-111">Unix-Sockets für eine hohe Leistung im Hintergrund von Nginx</span><span class="sxs-lookup"><span data-stu-id="c7cf9-111">Unix sockets for high performance behind Nginx</span></span>

<span data-ttu-id="c7cf9-112">&dagger;HTTP/2 wird unter macOS in einem zukünftigen Release unterstützt.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-112">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>

<span data-ttu-id="c7cf9-113">Kestrel wird auf allen Plattformen und für alle Versionen unterstützt, die .NET Core unterstützt.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-113">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="c7cf9-114">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples/5.x) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="c7cf9-114">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples/5.x) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="get-started"></a><span data-ttu-id="c7cf9-115">Erste Schritte</span><span class="sxs-lookup"><span data-stu-id="c7cf9-115">Get started</span></span>

<span data-ttu-id="c7cf9-116">ASP.NET Core-Projektvorlagen verwenden Kestrel standardmäßig.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-116">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="c7cf9-117">In *Program.cs* ruft die <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*>-Methode <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>auf:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-117">In *Program.cs*, the <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> method calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span></span>

[!code-csharp[](kestrel/samples/5.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=8)]

<span data-ttu-id="c7cf9-118">Weitere Informationen zum Erstellen des Hosts finden Sie im Artikel <xref:fundamentals/host/generic-host#set-up-a-host> in den Abschnitten *Einrichten eines Hosts* und *Standardeinstellungen für den Generator*.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-118">For more information on building the host, see the *Set up a host* and *Default builder settings* sections of <xref:fundamentals/host/generic-host#set-up-a-host>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c7cf9-119">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="c7cf9-119">Additional resources</span></span>

<a name="endpoint-configuration"></a>
* <xref:fundamentals/servers/kestrel/endpoints>
<a name="kestrel-options"></a>
* <xref:fundamentals/servers/kestrel/options>
<a name="http2-support"></a>
* <xref:fundamentals/servers/kestrel/http2>
<a name="when-to-use-kestrel-with-a-reverse-proxy"></a>
* <xref:fundamentals/servers/kestrel/when-to-use-a-reverse-proxy>
<a name="host-filtering"></a>
* <xref:fundamentals/servers/kestrel/host-filtering>
* <xref:test/troubleshoot>
* <xref:security/enforcing-ssl>
* <xref:host-and-deploy/proxy-load-balancer>
* [<span data-ttu-id="c7cf9-120">RFC 7230: Message Syntax and Routing (Abschnitt 5.4: Host)</span><span class="sxs-lookup"><span data-stu-id="c7cf9-120">RFC 7230: Message Syntax and Routing (Section 5.4: Host)</span></span>](https://tools.ietf.org/html/rfc7230#section-5.4)
* <span data-ttu-id="c7cf9-121">Bei der Verwendung von UNIX-Sockets unter Linux wird der Socket beim Herunterfahren der App nicht automatisch gelöscht.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-121">When using UNIX sockets on Linux, the socket is not automatically deleted on app shut down.</span></span> <span data-ttu-id="c7cf9-122">Weitere Informationen finden Sie in [diesem GitHub-Issue](https://github.com/dotnet/aspnetcore/issues/14134).</span><span class="sxs-lookup"><span data-stu-id="c7cf9-122">For more information, see [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/14134).</span></span>

> [!NOTE]
> <span data-ttu-id="c7cf9-123">Ab ASP.NET Core 5.0 ist der Libuv-Transport von Kestrel veraltet.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-123">As of ASP.NET Core 5.0, Kestrel's libuv transport is obsolete.</span></span> <span data-ttu-id="c7cf9-124">Der Libuv-Transport empfängt keine Updates zur Unterstützung neuer Betriebssystemplattformen wie Windows ARM64 und wird in einem zukünftigen Release entfernt.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-124">The libuv transport doesn't receive updates to support new OS platforms, such as Windows ARM64, and will be removed in a future release.</span></span> <span data-ttu-id="c7cf9-125">Entfernen Sie alle Aufrufe der veralteten <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv%2A>-Methode, und verwenden Sie stattdessen den standardmäßigen Sockettransport von Kestrel.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-125">Remove any calls to the obsolete <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv%2A> method and use Kestrel's default Socket transport instead.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="c7cf9-126">Einführung in Kestrel, dem plattformübergreifenden [Webserver für ASP.NET Core](xref:fundamentals/servers/index).</span><span class="sxs-lookup"><span data-stu-id="c7cf9-126">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="c7cf9-127">Kestrel ist der Webserver, der standardmäßig in ASP.NET Core-Projektvorlagen enthalten ist.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-127">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="c7cf9-128">Kestrel unterstützt die folgenden Szenarios:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-128">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="c7cf9-129">HTTPS</span><span class="sxs-lookup"><span data-stu-id="c7cf9-129">HTTPS</span></span>
* <span data-ttu-id="c7cf9-130">Nicht transparente Upgrades, die zum Aktivieren von [WebSockets](https://github.com/aspnet/websockets) verwendet werden</span><span class="sxs-lookup"><span data-stu-id="c7cf9-130">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="c7cf9-131">Unix-Sockets für eine hohe Leistung im Hintergrund von Nginx</span><span class="sxs-lookup"><span data-stu-id="c7cf9-131">Unix sockets for high performance behind Nginx</span></span>
* <span data-ttu-id="c7cf9-132">HTTP/2 (außer unter macOS&dagger;)</span><span class="sxs-lookup"><span data-stu-id="c7cf9-132">HTTP/2 (except on macOS&dagger;)</span></span>

<span data-ttu-id="c7cf9-133">&dagger;HTTP/2 wird unter macOS in einem zukünftigen Release unterstützt.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-133">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>

<span data-ttu-id="c7cf9-134">Kestrel wird auf allen Plattformen und für alle Versionen unterstützt, die .NET Core unterstützt.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-134">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="c7cf9-135">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples/3.x) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="c7cf9-135">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples/3.x) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="http2-support"></a><span data-ttu-id="c7cf9-136">HTTP/2-Unterstützung</span><span class="sxs-lookup"><span data-stu-id="c7cf9-136">HTTP/2 support</span></span>

<span data-ttu-id="c7cf9-137">[HTTP/2](https://httpwg.org/specs/rfc7540.html) ist für ASP.NET Core-Apps verfügbar, wenn die folgenden Basisanforderungen erfüllt sind:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-137">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is available for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="c7cf9-138">Betriebssystem&dagger;</span><span class="sxs-lookup"><span data-stu-id="c7cf9-138">Operating system&dagger;</span></span>
  * <span data-ttu-id="c7cf9-139">Windows Server 2016/Windows 10 oder höher&Dagger;</span><span class="sxs-lookup"><span data-stu-id="c7cf9-139">Windows Server 2016/Windows 10 or later&Dagger;</span></span>
  * <span data-ttu-id="c7cf9-140">Linux mit OpenSSL 1.0.2 oder höher (z.B. Ubuntu 16.04 oder höher)</span><span class="sxs-lookup"><span data-stu-id="c7cf9-140">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
* <span data-ttu-id="c7cf9-141">Zielframework: .NET Core 2.2 oder höher</span><span class="sxs-lookup"><span data-stu-id="c7cf9-141">Target framework: .NET Core 2.2 or later</span></span>
* <span data-ttu-id="c7cf9-142">[ALPN](https://tools.ietf.org/html/rfc7301#section-3)-Verbindung (Application-Layer Protocol Negotiation)</span><span class="sxs-lookup"><span data-stu-id="c7cf9-142">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="c7cf9-143">TLS 1.2-Verbindung oder höher</span><span class="sxs-lookup"><span data-stu-id="c7cf9-143">TLS 1.2 or later connection</span></span>

<span data-ttu-id="c7cf9-144">&dagger;HTTP/2 wird unter macOS in einem zukünftigen Release unterstützt.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-144">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>
<span data-ttu-id="c7cf9-145">&Dagger;Kestrel bietet eingeschränkte Unterstützung für HTTP/2 unter Windows Server 2012 R2 und Windows 8.1.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-145">&Dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="c7cf9-146">Die Unterstützung ist eingeschränkt, weil die Liste der unterstützten TLS-Verschlüsselungssammlungen unter diesen Betriebssystemen begrenzt ist.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-146">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="c7cf9-147">Zum Sichern von TLS-Verbindungen ist möglicherweise ein durch einen Elliptic Curve Digital Signature Algorithm (ECDSA) generiertes Zertifikat erforderlich.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-147">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

<span data-ttu-id="c7cf9-148">Wenn eine HTTP/2-Verbindung hergestellt wurde, meldet [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*)`HTTP/2`.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-148">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="c7cf9-149">Ab .NET Core 3.0 ist HTTP/2 standardmäßig aktiviert.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-149">Starting with .NET Core 3.0, HTTP/2 is enabled by default.</span></span> <span data-ttu-id="c7cf9-150">Weitere Informationen zur Konfiguration finden Sie in den Abschnitten [Kestrel-Optionen](#kestrel-options) und [ListenOptions.Protocols](#listenoptionsprotocols).</span><span class="sxs-lookup"><span data-stu-id="c7cf9-150">For more information on configuration, see the [Kestrel options](#kestrel-options) and [ListenOptions.Protocols](#listenoptionsprotocols) sections.</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="c7cf9-151">Verwenden von Kestrel mit einem Reverseproxy</span><span class="sxs-lookup"><span data-stu-id="c7cf9-151">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="c7cf9-152">Kestrel kann eigenständig oder mit einem *Reverseproxyserver* wie z.B. [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org) oder [Apache](https://httpd.apache.org/) verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-152">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="c7cf9-153">Ein Reverseproxyserver empfängt HTTP-Anforderungen aus dem Netzwerk und leitet diese an Kestrel weiter.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-153">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="c7cf9-154">Kestrel bei Verwendung als Webserver mit direkter Internetverbindung:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-154">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Kestrel kommuniziert direkt und ohne Reverseproxyserver mit dem Internet](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="c7cf9-156">Kestrel bei Verwendung in einer Reverseproxykonfiguration:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-156">Kestrel used in a reverse proxy configuration:</span></span>

![Kestrel kommuniziert indirekt mit dem Internet über einen Reverseproxyserver wie IIS, Nginx oder Apache](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="c7cf9-158">Jede der beiden Konfigurationen – mit oder ohne einen Reverseproxyserver – stellt eine unterstützte Hostingkonfiguration dar.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-158">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="c7cf9-159">Bei Verwendung als Edgeserver ohne Reverseproxyserver unterstützt Kestrel die gemeinsame Nutzung der gleichen IP-Adresse und des gleichen Ports durch mehrere Prozesse nicht.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-159">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="c7cf9-160">Wenn Kestrel für das Lauschen an einem Port konfiguriert ist, verarbeitet Kestrel den gesamten Datenverkehr für diesen Port unabhängig von den `Host`-Headern der Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-160">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="c7cf9-161">Ein Reverseproxy, der Ports freigeben kann, kann Anforderungen an Kestrel über eine eindeutige IP und einen eindeutigen Port weiterleiten.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-161">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="c7cf9-162">Auch wenn kein Reverseproxyserver erforderlich ist, kann die Verwendung eines solchen empfehlenswert sein.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-162">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="c7cf9-163">Für einen Reverseproxy gilt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-163">A reverse proxy:</span></span>

* <span data-ttu-id="c7cf9-164">Er kann die verfügbar gemachten öffentlichen Oberflächen der von ihm gehosteten Apps einschränken.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-164">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="c7cf9-165">Er stellt eine zusätzliche Ebene für Konfiguration und Schutz bereit.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-165">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="c7cf9-166">Er lässt sich besser in die vorhandene Infrastruktur integrieren.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-166">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="c7cf9-167">Er vereinfacht die Konfiguration von Lastenausgleich und sicherer Kommunikation (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="c7cf9-167">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="c7cf9-168">Nur der Reverseproxyserver erfordert ein X.509-Zertifikat, und dieser Server kann mit den Servern der App im internen Netzwerk über einfaches HTTP kommunizieren.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-168">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="c7cf9-169">Das Hosting in einer Reverseproxykonfiguration erfordert [Hostfilterung](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="c7cf9-169">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="kestrel-in-aspnet-core-apps"></a><span data-ttu-id="c7cf9-170">Kestrel in ASP.NET Core-Apps</span><span class="sxs-lookup"><span data-stu-id="c7cf9-170">Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="c7cf9-171">ASP.NET Core-Projektvorlagen verwenden Kestrel standardmäßig.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-171">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="c7cf9-172">In *Program.cs* ruft die <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*>-Methode <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>auf:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-172">In *Program.cs*, the <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> method calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=8)]

<span data-ttu-id="c7cf9-173">Weitere Informationen zum Erstellen des Hosts finden Sie im Artikel <xref:fundamentals/host/generic-host#set-up-a-host> in den Abschnitten *Einrichten eines Hosts* und *Standardeinstellungen für den Generator*.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-173">For more information on building the host, see the *Set up a host* and *Default builder settings* sections of <xref:fundamentals/host/generic-host#set-up-a-host>.</span></span>

<span data-ttu-id="c7cf9-174">Um zusätzliche Konfiguration nach dem Aufruf von `ConfigureWebHostDefaults` bereitzustellen, verwenden Sie `ConfigureKestrel`:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-174">To provide additional configuration after calling `ConfigureWebHostDefaults`, use `ConfigureKestrel`:</span></span>

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

## <a name="kestrel-options"></a><span data-ttu-id="c7cf9-175">Kestrel-Optionen</span><span class="sxs-lookup"><span data-stu-id="c7cf9-175">Kestrel options</span></span>

<span data-ttu-id="c7cf9-176">Der Kestrel-Webserver verfügt über einschränkende Konfigurationsoptionen, die besonders nützlich bei Bereitstellungen mit Internetzugriff sind.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-176">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="c7cf9-177">Legen Sie Einschränkungen für die <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits>-Eigenschaft der <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>-Klasse fest.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-177">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="c7cf9-178">Die `Limits`-Eigenschaft enthält eine Instanz der <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>-Klasse.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-178">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="c7cf9-179">In den folgenden Beispielen wird der <xref:Microsoft.AspNetCore.Server.Kestrel.Core>-Namespace verwendet:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-179">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="c7cf9-180">In den Beispielen, die weiter unten in diesem Artikel aufgeführt sind, werden Kestrel-Optionen in C#-Code konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-180">In examples shown later in this article, Kestrel options are configured in C# code.</span></span> <span data-ttu-id="c7cf9-181">Kestrel-Optionen können ebenso mithilfe eines [Konfigurationsanbieters](xref:fundamentals/configuration/index) festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-181">Kestrel options can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="c7cf9-182">Beispielsweise kann der [Dateikonfigurationsanbieter](xref:fundamentals/configuration/index#file-configuration-provider) die Kestrel-Konfiguration aus einer *appsettings.json* - oder *appsettings.{Umgebung}.json*-Datei laden:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-182">For example, the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider) can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

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
> <span data-ttu-id="c7cf9-183"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> und die [Endpunktkonfiguration](#endpoint-configuration) können über Konfigurationsanbieter konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-183"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> and [endpoint configuration](#endpoint-configuration) are configurable from configuration providers.</span></span> <span data-ttu-id="c7cf9-184">Die verbleibende Kestrel-Konfiguration muss in C#-Code konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-184">Remaining Kestrel configuration must be configured in C# code.</span></span>

<span data-ttu-id="c7cf9-185">Verwenden Sie **einen** der folgenden Ansätze:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-185">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="c7cf9-186">Konfigurieren Sie Kestrel in `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-186">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="c7cf9-187">Fügen Sie eine Instanz von `IConfiguration` in die `Startup`-Klasse ein.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-187">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="c7cf9-188">Im folgenden Beispiel wird davon ausgegangen, dass die eingefügte Konfiguration der `Configuration`-Eigenschaft zugewiesen wird.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-188">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="c7cf9-189">Laden Sie in `Startup.ConfigureServices` den Abschnitt `Kestrel` der Konfiguration in die Konfiguration von Kestrel:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-189">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

* <span data-ttu-id="c7cf9-190">Konfigurieren Sie Kestrel beim Erstellen des Hosts:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-190">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="c7cf9-191">Laden Sie in *Program.cs* den Abschnitt `Kestrel` der Konfiguration in die Konfiguration von Kestrel:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-191">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

<span data-ttu-id="c7cf9-192">Beide vorangehenden Ansätze funktionieren mit jedem [Konfigurationsanbieter](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="c7cf9-192">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="c7cf9-193">Keep-Alive-Timeout</span><span class="sxs-lookup"><span data-stu-id="c7cf9-193">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="c7cf9-194">Ruft das [Keep-Alive-Timeout](https://tools.ietf.org/html/rfc7230#section-6.5) ab oder legt es fest.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-194">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="c7cf9-195">Standardwert: 2 Minuten.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-195">Defaults to 2 minutes.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=19-20)]

### <a name="maximum-client-connections"></a><span data-ttu-id="c7cf9-196">Maximale Anzahl der Clientverbindungen</span><span class="sxs-lookup"><span data-stu-id="c7cf9-196">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="c7cf9-197">Die maximale Anzahl von gleichzeitig geöffneten TCP-Verbindungen kann mithilfe von folgendem Code für die gesamte App festgelegt werden:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-197">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

<span data-ttu-id="c7cf9-198">Es gibt einen separaten Grenzwert für Verbindungen, die von HTTP oder HTTPS auf ein anderes Protokoll aktualisiert wurden (z.B. auf eine WebSockets-Anforderung).</span><span class="sxs-lookup"><span data-stu-id="c7cf9-198">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="c7cf9-199">Nachdem eine Verbindung aktualisiert wurde, zählt diese nicht mehr für den `MaxConcurrentConnections`-Grenzwert.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-199">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

<span data-ttu-id="c7cf9-200">Die maximale Anzahl von Verbindungen ist standardmäßig nicht begrenzt (NULL).</span><span class="sxs-lookup"><span data-stu-id="c7cf9-200">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="c7cf9-201">Maximale Größe des Anforderungstexts</span><span class="sxs-lookup"><span data-stu-id="c7cf9-201">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="c7cf9-202">Die maximale Größe des Anforderungstexts beträgt standardmäßig 30.000.000 Byte, also ungefähr 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-202">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="c7cf9-203">Die empfohlene Methode zur Außerkraftsetzung des Grenzwerts in einer ASP.NET Core-MVC-App besteht im Verwenden des <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute>-Attributs in einer Aktionsmethode:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-203">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="c7cf9-204">Im folgenden Beispiel wird veranschaulicht, wie die Einschränkungen auf jeder Anforderung für die App konfiguriert werden:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-204">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

<span data-ttu-id="c7cf9-205">Außer Kraft setzen der Einstellung für eine bestimmte Anforderung in Middleware:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-205">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="c7cf9-206">Eine Ausnahme wird ausgelöst, wenn die App den Grenzwert einer Anforderung konfiguriert, nachdem die App bereits mit dem Lesen der Anforderung begonnen hat.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-206">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="c7cf9-207">Es gibt eine `IsReadOnly`-Eigenschaft, die angibt, wenn sich die `MaxRequestBodySize`-Eigenschaft im schreibgeschützten Zustand befindet, also wenn der Grenzwert nicht mehr konfiguriert werden kann.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-207">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="c7cf9-208">Wenn eine App [prozessextern](xref:host-and-deploy/iis/index#out-of-process-hosting-model) hinter dem [ASP.NET Core-Modul](xref:host-and-deploy/aspnet-core-module) ausgeführt wird, ist das Größenlimit von Kestrel für Anforderungstext deaktiviert, weil IIS dieses Limit bereits festlegt.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-208">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="c7cf9-209">Minimale Datenrate des Anforderungstexts</span><span class="sxs-lookup"><span data-stu-id="c7cf9-209">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="c7cf9-210">Kestrel überprüft sekündlich, ob Daten mit der angegebenen Rate in Bytes/Sekunde eingehen.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-210">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="c7cf9-211">Wenn die Rate den mindestens erforderlichen Wert unterschreitet, wird für die Verbindung wegen Timeout getrennt. Bei der Toleranzperiode handelt es sich um die Zeitspanne, die Kestrel dem Client gewährt, um die Senderate auf den mindestens erforderlichen Wert zu erhöhen. Die Rate wird währenddessen nicht überprüft.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-211">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="c7cf9-212">Diese Toleranzperiode beugt dem Trennen von Verbindungen vor, die Daten aufgrund eines langsamen TCP-Starts anfänglich mit einer niedrigen Rate senden.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-212">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="c7cf9-213">Die mindestens erforderliche Rate beträgt standardmäßig 240 Bytes/Sekunde mit einer Toleranzperiode von 5 Sekunden.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-213">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="c7cf9-214">Für die Antwort gilt ebenfalls eine mindestens erforderliche Rate.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-214">A minimum rate also applies to the response.</span></span> <span data-ttu-id="c7cf9-215">Der Code zum Festlegen des Grenzwerts für Anforderung und Antwort ist abgesehen von `RequestBody` oder `Response` in den Namen von Eigenschaften und Schnittstelle identisch.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-215">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="c7cf9-216">In diesem Beispiel wird veranschaulicht, wie Sie die mindestens erforderlichen Datenraten in *Program.cs* konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-216">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-11)]

<span data-ttu-id="c7cf9-217">Außer Kraft setzen des minimalen Ratenlimits pro Anforderung in Middleware:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-217">Override the minimum rate limits per request in middleware:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

<span data-ttu-id="c7cf9-218">Das <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature> aus dem vorherigen Beispiel ist in `HttpContext.Features` für HTTP/2-Anforderungen nicht vorhanden, weil die Änderung von Ratenlimits für jede Anforderung aufgrund der Unterstützung für Anforderungsmultiplexing von HTTP/2 von diesem Protokoll unterstützt wird.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-218">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature> referenced in the prior sample is not present in `HttpContext.Features` for HTTP/2 requests because modifying rate limits on a per-request basis is generally not supported for HTTP/2 due to the protocol's support for request multiplexing.</span></span> <span data-ttu-id="c7cf9-219"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> ist jedoch in `HttpContext.Features` für HTTP/2-Anforderungen noch vorhanden, weil das Leseratenlimit weiterhin für jede Anforderung *vollständig deaktiviert* werden kann, indem `IHttpMinRequestBodyDataRateFeature.MinDataRate` auch für eine HTTP/2-Anforderung auf `null` festgelegt wird.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-219">However, the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> is still present `HttpContext.Features` for HTTP/2 requests, because the read rate limit can still be *disabled entirely* on a per-request basis by setting `IHttpMinRequestBodyDataRateFeature.MinDataRate` to `null` even for an HTTP/2 request.</span></span> <span data-ttu-id="c7cf9-220">Der Versuch, `IHttpMinRequestBodyDataRateFeature.MinDataRate` zu lesen oder auf einen anderen Wert als `null` festzulegen, führt dazu, dass bei einer HTTP/2-Anforderung eine `NotSupportedException` ausgelöst wird.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-220">Attempting to read `IHttpMinRequestBodyDataRateFeature.MinDataRate` or attempting to set it to a value other than `null` will result in a `NotSupportedException` being thrown given an HTTP/2 request.</span></span>

<span data-ttu-id="c7cf9-221">Serverweite Ratenlimits, die über `KestrelServerOptions.Limits` konfiguriert sind, gelten auch für HTTP/1.x- und HTTP/2-Verbindungen.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-221">Server-wide rate limits configured via `KestrelServerOptions.Limits` still apply to both HTTP/1.x and HTTP/2 connections.</span></span>

### <a name="request-headers-timeout"></a><span data-ttu-id="c7cf9-222">Timeout für Anforderungsheader</span><span class="sxs-lookup"><span data-stu-id="c7cf9-222">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="c7cf9-223">Ruft die maximale Zeitspanne ab, während der der Server Anforderungsheader empfängt, oder legt diese fest.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-223">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="c7cf9-224">Der Standardwert beträgt 30 Sekunden.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-224">Defaults to 30 seconds.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=21-22)]

### <a name="maximum-streams-per-connection"></a><span data-ttu-id="c7cf9-225">Maximale Datenströme pro Verbindung</span><span class="sxs-lookup"><span data-stu-id="c7cf9-225">Maximum streams per connection</span></span>

<span data-ttu-id="c7cf9-226">`Http2.MaxStreamsPerConnection` schränkt die Anzahl gleichzeitiger Anforderungsdatenströme pro HTTP/2-Verbindung ein.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-226">`Http2.MaxStreamsPerConnection` limits the number of concurrent request streams per HTTP/2 connection.</span></span> <span data-ttu-id="c7cf9-227">Überschüssige Datenströme werden zurückgewiesen.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-227">Excess streams are refused.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
});
```

<span data-ttu-id="c7cf9-228">Der Standardwert ist 100.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-228">The default value is 100.</span></span>

### <a name="header-table-size"></a><span data-ttu-id="c7cf9-229">Größe der Headertabelle</span><span class="sxs-lookup"><span data-stu-id="c7cf9-229">Header table size</span></span>

<span data-ttu-id="c7cf9-230">Der HPACK-Decoder dekomprimiert HTTP-Header für HTTP/2-Verbindungen.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-230">The HPACK decoder decompresses HTTP headers for HTTP/2 connections.</span></span> <span data-ttu-id="c7cf9-231">`Http2.HeaderTableSize` schränkt die Größe der Headerkomprimierungstabelle ein, die der HPACK-Decoder verwendet.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-231">`Http2.HeaderTableSize` limits the size of the header compression table that the HPACK decoder uses.</span></span> <span data-ttu-id="c7cf9-232">Der Wert wird in Oktetten bereitgestellt und muss größer als null (0) sein.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-232">The value is provided in octets and must be greater than zero (0).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.HeaderTableSize = 4096;
});
```

<span data-ttu-id="c7cf9-233">Der Standardwert ist 4096.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-233">The default value is 4096.</span></span>

### <a name="maximum-frame-size"></a><span data-ttu-id="c7cf9-234">Maximale Framegröße</span><span class="sxs-lookup"><span data-stu-id="c7cf9-234">Maximum frame size</span></span>

<span data-ttu-id="c7cf9-235">`Http2.MaxFrameSize` gibt die maximal zulässige Größe einer vom Server empfangenen oder gesendeten HTTP/2-Verbindungsrahmen-Nutzlast an.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-235">`Http2.MaxFrameSize` indicates the maximum allowed size of an HTTP/2 connection frame payload received or sent by the server.</span></span> <span data-ttu-id="c7cf9-236">Der Wert wird in Oktetten bereitgestellt und muss zwischen 2^14 (16.384) und 2^24-1 (16.777.215) liegen.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-236">The value is provided in octets and must be between 2^14 (16,384) and 2^24-1 (16,777,215).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxFrameSize = 16384;
});
```

<span data-ttu-id="c7cf9-237">Der Standardwert ist 2^14 (16.384).</span><span class="sxs-lookup"><span data-stu-id="c7cf9-237">The default value is 2^14 (16,384).</span></span>

### <a name="maximum-request-header-size"></a><span data-ttu-id="c7cf9-238">Maximale Größe des Anforderungsheaders</span><span class="sxs-lookup"><span data-stu-id="c7cf9-238">Maximum request header size</span></span>

<span data-ttu-id="c7cf9-239">`Http2.MaxRequestHeaderFieldSize` gibt die maximal zulässige Größe in Oktetten der Anforderungsheaderwerte an.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-239">`Http2.MaxRequestHeaderFieldSize` indicates the maximum allowed size in octets of request header values.</span></span> <span data-ttu-id="c7cf9-240">Dieser Grenzwert gilt sowohl für den Namen als auch den Wert in der komprimierten und nicht komprimierten Darstellung.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-240">This limit applies to both name and value in their compressed and uncompressed representations.</span></span> <span data-ttu-id="c7cf9-241">Der Wert muss größer als 0 (null) sein.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-241">The value must be greater than zero (0).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
});
```

<span data-ttu-id="c7cf9-242">Der Standardwert ist 8.192.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-242">The default value is 8,192.</span></span>

### <a name="initial-connection-window-size"></a><span data-ttu-id="c7cf9-243">Anfangsfenstergröße der Verbindung</span><span class="sxs-lookup"><span data-stu-id="c7cf9-243">Initial connection window size</span></span>

<span data-ttu-id="c7cf9-244">`Http2.InitialConnectionWindowSize` gibt die maximalen Anforderungstextdaten in Byte an, die der Server auf einmal für alle Anforderungen (Streams) pro Verbindung aggregiert.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-244">`Http2.InitialConnectionWindowSize` indicates the maximum request body data in bytes the server buffers at one time aggregated across all requests (streams) per connection.</span></span> <span data-ttu-id="c7cf9-245">Anforderungen werden auch durch `Http2.InitialStreamWindowSize` beschränkt.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-245">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="c7cf9-246">Der Wert muss größer als oder gleich 65.535 und kleiner als 2^31 (2.147.483.648) sein.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-246">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
});
```

<span data-ttu-id="c7cf9-247">Der Standardwert ist 128 KB (131.072).</span><span class="sxs-lookup"><span data-stu-id="c7cf9-247">The default value is 128 KB (131,072).</span></span>

### <a name="initial-stream-window-size"></a><span data-ttu-id="c7cf9-248">Anfangsfenstergröße des Streams</span><span class="sxs-lookup"><span data-stu-id="c7cf9-248">Initial stream window size</span></span>

<span data-ttu-id="c7cf9-249">`Http2.InitialStreamWindowSize` gibt die maximalen Anforderungstextdaten in Byte an, die der Server auf einmal pro Anforderung (Stream) puffert.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-249">`Http2.InitialStreamWindowSize` indicates the maximum request body data in bytes the server buffers at one time per request (stream).</span></span> <span data-ttu-id="c7cf9-250">Anforderungen werden auch durch `Http2.InitialConnectionWindowSize` beschränkt.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-250">Requests are also limited by `Http2.InitialConnectionWindowSize`.</span></span> <span data-ttu-id="c7cf9-251">Der Wert muss größer als oder gleich 65.535 und kleiner als 2^31 (2.147.483.648) sein.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-251">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
});
```

<span data-ttu-id="c7cf9-252">Der Standardwert ist 96 KB (98.304).</span><span class="sxs-lookup"><span data-stu-id="c7cf9-252">The default value is 96 KB (98,304).</span></span>

### <a name="trailers"></a><span data-ttu-id="c7cf9-253">Trailer</span><span class="sxs-lookup"><span data-stu-id="c7cf9-253">Trailers</span></span>

[!INCLUDE[](~/includes/trailers.md)]

### <a name="reset"></a><span data-ttu-id="c7cf9-254">Reset</span><span class="sxs-lookup"><span data-stu-id="c7cf9-254">Reset</span></span>

[!INCLUDE[](~/includes/reset.md)]

### <a name="synchronous-io"></a><span data-ttu-id="c7cf9-255">Synchrone E/A-Vorgänge</span><span class="sxs-lookup"><span data-stu-id="c7cf9-255">Synchronous I/O</span></span>

<span data-ttu-id="c7cf9-256"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> steuert, ob für Anforderung und Antwort synchrone E/A-Vorgänge zulässig sind.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-256"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous I/O is allowed for the request and response.</span></span> <span data-ttu-id="c7cf9-257">Der Standardwert ist `false`.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-257">The default value is `false`.</span></span>

> [!WARNING]
> <span data-ttu-id="c7cf9-258">Sehr viele blockierende synchrone E/A-Vorgänge können zu einem Ressourcenmangel im Threadpool führen, wodurch die App nicht mehr reagiert.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-258">A large number of blocking synchronous I/O operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="c7cf9-259">Aktivieren Sie `AllowSynchronousIO` nur bei Verwendung einer Bibliothek, die asynchrone E/A-Vorgänge nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-259">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous I/O.</span></span>

<span data-ttu-id="c7cf9-260">Das folgende Beispiel aktiviert synchrone E/A-Vorgänge:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-260">The following example enables synchronous I/O:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

<span data-ttu-id="c7cf9-261">Weitere Informationen zu anderen Kestrel-Optionen und -Einschränkungen finden Sie unter:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-261">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="c7cf9-262">Endpunktkonfiguration</span><span class="sxs-lookup"><span data-stu-id="c7cf9-262">Endpoint configuration</span></span>

<span data-ttu-id="c7cf9-263">Standardmäßig wird ASP.NET Core an Folgendes gebunden:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-263">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="c7cf9-264">`https://localhost:5001` (wenn ein lokales Entwicklungszertifikat vorhanden ist)</span><span class="sxs-lookup"><span data-stu-id="c7cf9-264">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="c7cf9-265">Verwenden Sie Folgendes zum Angeben der URLs:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-265">Specify URLs using the:</span></span>

* <span data-ttu-id="c7cf9-266">Die Umgebungsvariable `ASPNETCORE_URLS`</span><span class="sxs-lookup"><span data-stu-id="c7cf9-266">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="c7cf9-267">Das Befehlszeilenargument `--urls`</span><span class="sxs-lookup"><span data-stu-id="c7cf9-267">`--urls` command-line argument.</span></span>
* <span data-ttu-id="c7cf9-268">Den Hostkonfigurationsschlüssel `urls`</span><span class="sxs-lookup"><span data-stu-id="c7cf9-268">`urls` host configuration key.</span></span>
* <span data-ttu-id="c7cf9-269">Die Erweiterungsmethode `UseUrls`</span><span class="sxs-lookup"><span data-stu-id="c7cf9-269">`UseUrls` extension method.</span></span>

<span data-ttu-id="c7cf9-270">Der Wert, der mit diesen Ansätzen angegeben wird, kann mindestens ein HTTP- oder HTTPS-Endpunkt sein (HTTPS wenn ein Standardzertifikat verfügbar ist).</span><span class="sxs-lookup"><span data-stu-id="c7cf9-270">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="c7cf9-271">Konfigurieren Sie den Wert als eine durch Semikolons getrennte Liste (z.B. `"Urls": "http://localhost:8000;http://localhost:8001"`).</span><span class="sxs-lookup"><span data-stu-id="c7cf9-271">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="c7cf9-272">Weitere Informationen zu diesen Ansätzen finden Sie unter [Server-URLs](xref:fundamentals/host/web-host#server-urls) und [Außerkraftsetzen der Konfiguration](xref:fundamentals/host/web-host#override-configuration).</span><span class="sxs-lookup"><span data-stu-id="c7cf9-272">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="c7cf9-273">Ein Entwicklungszertifikat wird erstellt:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-273">A development certificate is created:</span></span>

* <span data-ttu-id="c7cf9-274">Wenn das [.NET Core SDK](/dotnet/core/sdk) installiert wird.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-274">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="c7cf9-275">Das [dev-cert-Tool](xref:aspnetcore-2.1#https) wird zum Erstellen eines Zertifikats verwendet.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-275">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="c7cf9-276">Einige Browser erfordern, dass Sie die explizite Berechtigung erteilen, dem lokalen Entwicklungszertifikat zu vertrauen.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-276">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="c7cf9-277">Projektvorlagen konfigurieren Apps, damit sie standardmäßig auf HTTPS ausgeführt werden und die [HTTPS-Umleitung und HSTS-Unterstützung](xref:security/enforcing-ssl) enthalten.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-277">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="c7cf9-278">Rufen Sie die <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*>- oder <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*>-Methode unter <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> auf, um URL-Präfixe und Ports für Kestrel zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-278">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="c7cf9-279">`UseUrls`, das Befehlszeilenargument `--urls`, der Hostkonfigurationsschlüssel `urls` und die Umgebungsvariable `ASPNETCORE_URLS` funktionieren ebenfalls, verfügen jedoch über Einschränkungen, die im Verlauf dieses Abschnitts erläutert werden (Ein Standardzertifikat muss für die HTTPS-Endpunktkonfiguration verfügbar sein).</span><span class="sxs-lookup"><span data-stu-id="c7cf9-279">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="c7cf9-280">`KestrelServerOptions`-Konfiguration:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-280">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="c7cf9-281">ConfigureEndpointDefaults(Action\<ListenOptions>)</span><span class="sxs-lookup"><span data-stu-id="c7cf9-281">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="c7cf9-282">Gibt die Konfiguration von `Action` zum Ausführen von jedem angegebenen Endpunkt an.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-282">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="c7cf9-283">Mehrmalige Aufrufe von `ConfigureEndpointDefaults` ersetzen vorherige Instanzen von `Action` mit der zuletzt angegebenen `Action`.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-283">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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
> <span data-ttu-id="c7cf9-284">Auf Endpunkte, die durch Aufrufen von <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **vor** dem Aufrufen von <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> erstellt werden, werden die Standardwerte nicht angewendet.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-284">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> won't have the defaults applied.</span></span>

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="c7cf9-285">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span><span class="sxs-lookup"><span data-stu-id="c7cf9-285">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="c7cf9-286">Gibt die Konfiguration von `Action` zum Ausführen von jedem HTTPS-Endpunkt an.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-286">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="c7cf9-287">Mehrmalige Aufrufe von `ConfigureHttpsDefaults` ersetzen vorherige Instanzen von `Action` mit der zuletzt angegebenen `Action`.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-287">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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
> <span data-ttu-id="c7cf9-288">Auf Endpunkte, die durch Aufrufen von <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **vor** dem Aufrufen von <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> erstellt werden, werden die Standardwerte nicht angewendet.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-288">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>

### <a name="configureiconfiguration"></a><span data-ttu-id="c7cf9-289">Configure(IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="c7cf9-289">Configure(IConfiguration)</span></span>

<span data-ttu-id="c7cf9-290">Erstellt ein Konfigurationsladeprogramm für das Einrichten von Kestrel, was <xref:Microsoft.Extensions.Configuration.IConfiguration> als Eingabe erfordert.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-290">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="c7cf9-291">Die Konfiguration muss auf den Konfigurationsabschnitt für Kestrel festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-291">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="c7cf9-292">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="c7cf9-292">ListenOptions.UseHttps</span></span>

<span data-ttu-id="c7cf9-293">Konfiguriert Kestrel zur Verwendung von HTTPS.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-293">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="c7cf9-294">`ListenOptions.UseHttps`-Erweiterungen:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-294">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="c7cf9-295">`UseHttps`: Hiermit wird Kestrel zur Verwendung von HTTPS mit dem Standardzertifikat konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-295">`UseHttps`: Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="c7cf9-296">Löst eine Ausnahme aus, wenn kein Standardzertifikat konfiguriert ist.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-296">Throws an exception if no default certificate is configured.</span></span>
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

<span data-ttu-id="c7cf9-297">`ListenOptions.UseHttps`-Parameter:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-297">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="c7cf9-298">`filename` entspricht dem Pfad und Dateinamen einer Zertifikatdatei relativ zu dem Verzeichnis, das die Inhaltsdateien der App enthält.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-298">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="c7cf9-299">`password` ist das für den Zugriff auf die X.509-Zertifikatsdaten erforderliche Kennwort.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-299">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="c7cf9-300">`configureOptions` ist eine `Action` zum Konfigurieren von `HttpsConnectionAdapterOptions`.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-300">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="c7cf9-301">Gibt `ListenOptions` zurück.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-301">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="c7cf9-302">`storeName` ist der Zertifikatspeicher, aus dem das Zertifikat geladen wird.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-302">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="c7cf9-303">`subject` ist der Name des Antragstellers für das Zertifikat.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-303">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="c7cf9-304">`allowInvalid` gibt an, ob ungültige Zertifikate berücksichtigt werden sollten, z.B. selbstsignierte Zertifikate.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-304">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="c7cf9-305">`location` ist der Speicherort, aus dem das Zertifikat geladen wird.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-305">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="c7cf9-306">`serverCertificate` ist das X.509-Zertifikat.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-306">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="c7cf9-307">In der Produktion muss HTTPS explizit konfiguriert sein.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-307">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="c7cf9-308">Zumindest muss ein Standardzertifikat angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-308">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="c7cf9-309">Die im Folgenden beschriebenen unterstützten Konfigurationen:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-309">Supported configurations described next:</span></span>

* <span data-ttu-id="c7cf9-310">Keine Konfiguration</span><span class="sxs-lookup"><span data-stu-id="c7cf9-310">No configuration</span></span>
* <span data-ttu-id="c7cf9-311">Ersetzen des Standardzertifikats aus der Konfiguration</span><span class="sxs-lookup"><span data-stu-id="c7cf9-311">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="c7cf9-312">Ändern des Standards im Code</span><span class="sxs-lookup"><span data-stu-id="c7cf9-312">Change the defaults in code</span></span>

<span data-ttu-id="c7cf9-313">*Keine Konfiguration*</span><span class="sxs-lookup"><span data-stu-id="c7cf9-313">*No configuration*</span></span>

<span data-ttu-id="c7cf9-314">Kestrel überwacht `http://localhost:5000` und `https://localhost:5001` (wenn ein Standardzertifikat verfügbar ist).</span><span class="sxs-lookup"><span data-stu-id="c7cf9-314">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="c7cf9-315">*Ersetzen des Standardzertifikats aus der Konfiguration*</span><span class="sxs-lookup"><span data-stu-id="c7cf9-315">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="c7cf9-316">`CreateDefaultBuilder` ruft `Configure(context.Configuration.GetSection("Kestrel"))` standardmäßig zum Laden der Kestrel-Konfiguration auf.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-316">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="c7cf9-317">Ein Standardkonfigurationsschema für HTTPS-App-Einstellungen ist für Kestrel verfügbar.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-317">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="c7cf9-318">Konfigurieren Sie mehrere Endpunkte, einschließlich der zu verwendenden URLs und Zertifikate aus einer Datei auf dem Datenträger oder einem Zertifikatspeicher.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-318">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="c7cf9-319">Im folgenden Beispiel für *appsettings.json* gilt:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-319">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="c7cf9-320">Legen Sie **AllowInvalid** auf `true` fest, um die Verwendung von ungültigen Zertifikaten zu erlauben (z.B. selbstsignierte Zertifikate).</span><span class="sxs-lookup"><span data-stu-id="c7cf9-320">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="c7cf9-321">Jeder HTTPS-Endpunkt, der kein Zertifikat angibt (im folgenden Beispiel **HttpsDefaultCert**), greift auf das unter **Zertifikate** > **Standard** festgelegte Zertifikat oder das Entwicklungszertifikat zurück.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-321">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

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

<span data-ttu-id="c7cf9-322">Alternativ zur Verwendung von **Pfad** und **Kennwort** für alle Zertifikatknoten können Sie das Zertifikat mithilfe von Zertifikatspeicherfeldern angeben.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-322">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="c7cf9-323">Das Zertifikat unter **Zertifikate** > **Standard** kann beispielweise wie folgt angegeben werden:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-323">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="c7cf9-324">Schema-Hinweise:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-324">Schema notes:</span></span>

* <span data-ttu-id="c7cf9-325">Bei den Namen der Endpunkte wird die Groß-/Kleinschreibung nicht beachtet.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-325">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="c7cf9-326">Zum Beispiel sind `HTTPS` und `Https` gültig.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-326">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="c7cf9-327">Der Parameter `Url` ist für jeden Endpunkt erforderlich.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-327">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="c7cf9-328">Das Format für diesen Parameter ist identisch mit dem allgemeinen Konfigurationsparameter `Urls`, mit der Ausnahme, dass er auf einen einzelnen Wert begrenzt ist.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-328">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="c7cf9-329">Diese Endpunkte ersetzen die in der allgemeinen `Urls`-Konfiguration festgelegten Endpunkte, anstatt zu ihnen hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-329">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="c7cf9-330">Endpunkte, die über `Listen` in Code definiert werden, werden den im Konfigurationsabschnitt definierten Endpunkten hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-330">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="c7cf9-331">Der `Certificate`-Abschnitt ist optional.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-331">The `Certificate` section is optional.</span></span> <span data-ttu-id="c7cf9-332">Wenn der `Certificate`-Abschnitt nicht angegeben ist, werden die in früheren Szenarios definierten Standardwerte verwendet.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-332">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="c7cf9-333">Wenn keine Standardwerte verfügbar sind, löst der Server eine Ausnahme aus und startet nicht.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-333">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="c7cf9-334">Der `Certificate`-Abschnitt unterstützt die Zertifikate **Pfad**&ndash;**Kennwort** und **Subject**&ndash;**Store** (Antragsteller–Speicher).</span><span class="sxs-lookup"><span data-stu-id="c7cf9-334">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="c7cf9-335">Auf diese Weise kann eine beliebige Anzahl von Endpunkten definiert werden, solange Sie keine Portkonflikte verursachen.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-335">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="c7cf9-336">`options.Configure(context.Configuration.GetSection("{SECTION}"))` gibt `KestrelConfigurationLoader` mit der Methode `.Endpoint(string name, listenOptions => { })` zurück, die dazu verwendet werden kann, die Einstellungen eines Endpunkts zu ergänzen:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-336">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

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

<span data-ttu-id="c7cf9-337">Auf `KestrelServerOptions.ConfigurationLoader` kann direkt zugegriffen werden, um die Iteration auf dem vorhandenen Ladeprogramm fortzusetzen, etwa auf dem von <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> bereitgestellten Ladeprogramm.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-337">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="c7cf9-338">Der Konfigurationsabschnitt ist für jeden Endpunkt in den Optionen der Methode `Endpoint` verfügbar, sodass benutzerdefinierte Einstellungen gelesen werden können.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-338">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="c7cf9-339">Mehrere Konfigurationen können durch erneutes Aufrufen von `options.Configure(context.Configuration.GetSection("{SECTION}"))` mit einem anderen Abschnitt geladen werden.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-339">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="c7cf9-340">Sofern `Load` nicht in einer vorherigen Instanz explizit aufgerufen wird, wird nur die letzte Konfiguration verwendet.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-340">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="c7cf9-341">Das Metapaket ruft `Load` nicht auf, sodass der Abschnitt mit der Standardkonfiguration ersetzt werden kann.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-341">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="c7cf9-342">`KestrelConfigurationLoader` spiegelt die API-Familie `Listen` von `KestrelServerOptions` als `Endpoint`-Überladungen, weshalb Code und Konfigurationsendpunkte am selben Ort konfiguriert werden können.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-342">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="c7cf9-343">Diese Überladungen verwenden keine Namen und nutzen nur Standardeinstellungen aus der Konfiguration.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-343">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="c7cf9-344">*Ändern des Standards im Code*</span><span class="sxs-lookup"><span data-stu-id="c7cf9-344">*Change the defaults in code*</span></span>

<span data-ttu-id="c7cf9-345">`ConfigureEndpointDefaults` und `ConfigureHttpsDefaults` können zum Ändern der Standardeinstellungen für `ListenOptions` und `HttpsConnectionAdapterOptions` verwendet werden, einschließlich der Standardzertifikate, die im vorherigen Szenario festgelegt wurden.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-345">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="c7cf9-346">`ConfigureEndpointDefaults` und `ConfigureHttpsDefaults` sollten aufgerufen werden, bevor Endpunkte konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-346">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

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

<span data-ttu-id="c7cf9-347">*Kestrel-Unterstützung für SNI*</span><span class="sxs-lookup"><span data-stu-id="c7cf9-347">*Kestrel support for SNI*</span></span>

<span data-ttu-id="c7cf9-348">Die [Servernamensanzeige (SNI)](https://tools.ietf.org/html/rfc6066#section-3) kann zum Hosten mehrerer Domänen auf der gleichen IP-Adresse und dem gleichen Port verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-348">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="c7cf9-349">Damit die Servernamensanzeige funktioniert, sendet der Client während des TLS-Handshakes den Hostnamen für die sichere Sitzung an den Server, sodass der Server das richtige Zertifikat bereitstellen kann.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-349">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="c7cf9-350">Der Client verwendet das beigestellte Zertifikat für die verschlüsselte Kommunikation mit dem Server während der sicheren Sitzung nach dem TLS-Handshake.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-350">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="c7cf9-351">Kestrel unterstützt die Servernamensanzeige über den `ServerCertificateSelector`-Rückruf.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-351">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="c7cf9-352">Der Rückruf wird für jede Verbindung einmal aufgerufen, um der App zu ermöglichen, den Hostnamen zu überprüfen und das entsprechende Zertifikat auszuwählen.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-352">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="c7cf9-353">Für die Unterstützung der Servernamensanzeige benötigen Sie Folgendes:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-353">SNI support requires:</span></span>

* <span data-ttu-id="c7cf9-354">Wird auf dem Zielframework `netcoreapp2.1` oder höher ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-354">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="c7cf9-355">In `net461` oder höher, wird der Rückruf aufgerufen, `name` ist aber immer `null`.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-355">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="c7cf9-356">`name` ist auch `null`, wenn der Client den Hostnamenparameter nicht im TLS-Handshake angibt.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-356">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="c7cf9-357">Alle Websites werden in derselben Kestrel-Instanz ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-357">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="c7cf9-358">Kestrel unterstützt ohne Reverseproxy keine gemeinsame IP-Adresse und keinen gemeinsamen Port für mehrere Instanzen.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-358">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

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

### <a name="connection-logging"></a><span data-ttu-id="c7cf9-359">Verbindungsprotokollierung</span><span class="sxs-lookup"><span data-stu-id="c7cf9-359">Connection logging</span></span>

<span data-ttu-id="c7cf9-360">Rufen Sie <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> auf, um Protokolle auf Debugebene für die Kommunikation auf Byteebene für eine Verbindung auszugeben.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-360">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="c7cf9-361">Die Verbindungsprotokollierung ist beim Beheben von Problemen bei der Low-Level-Kommunikation hilfreich, wie z. B. bei der TLS-Verschlüsselung und bei Proxys.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-361">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="c7cf9-362">Wenn `UseConnectionLogging` vor `UseHttps` platziert wird, wird der verschlüsselte Datenverkehr protokolliert.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-362">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="c7cf9-363">Wenn `UseConnectionLogging` nach `UseHttps` platziert wird, wird der entschlüsselte Datenverkehr protokolliert.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-363">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="c7cf9-364">Binden an einen TCP-Socket</span><span class="sxs-lookup"><span data-stu-id="c7cf9-364">Bind to a TCP socket</span></span>

<span data-ttu-id="c7cf9-365">Die <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*>-Methode wird an ein TCP-Socket gebunden, und ein Lambdaausdruck einer Option lässt die Konfiguration des X.509-Zertifikats zu:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-365">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=12-18)]

<span data-ttu-id="c7cf9-366">Im Beispiel wird HTTPS für einen Endpunkt mit <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions> konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-366">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="c7cf9-367">Verwenden Sie die gleiche API zum Konfigurieren anderer Kestrel-Einstellungen für bestimmte Endpunkte.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-367">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="c7cf9-368">Binden an einen Unix-Socket</span><span class="sxs-lookup"><span data-stu-id="c7cf9-368">Bind to a Unix socket</span></span>

<span data-ttu-id="c7cf9-369">Wie in diesem Beispiel dargestellt, lauschen Sie an einem Unix-Socket mit <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*>, um eine verbesserte Leistung mit Nginx zu erzielen:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-369">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

* <span data-ttu-id="c7cf9-370">Legen Sie den Eintrag `server` > `location` > `proxy_pass` in der Nginx-Konfigurationsdatei auf `http://unix:/tmp/{KESTREL SOCKET}:/;` fest.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-370">In the Nginx configuration file, set the `server` > `location` > `proxy_pass` entry to `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span></span> <span data-ttu-id="c7cf9-371">`{KESTREL SOCKET}` ist der Name des für <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> bereitgestellten Socket (zum Beispiel `kestrel-test.sock` im vorherigen Beispiel).</span><span class="sxs-lookup"><span data-stu-id="c7cf9-371">`{KESTREL SOCKET}` is the name of the socket provided to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (for example, `kestrel-test.sock` in the preceding example).</span></span>
* <span data-ttu-id="c7cf9-372">Stellen Sie sicher, dass der Socket von Nginx beschreibbar ist (z. B. `chmod go+w /tmp/kestrel-test.sock`).</span><span class="sxs-lookup"><span data-stu-id="c7cf9-372">Ensure that the socket is writeable by Nginx (for example, `chmod go+w /tmp/kestrel-test.sock`).</span></span>

### <a name="port-0"></a><span data-ttu-id="c7cf9-373">Port 0</span><span class="sxs-lookup"><span data-stu-id="c7cf9-373">Port 0</span></span>

<span data-ttu-id="c7cf9-374">Wenn die Portnummer `0` angegeben wird, wird Kestrel dynamisch an einen verfügbaren Port gebunden.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-374">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="c7cf9-375">Im folgenden Beispiel wird veranschaulicht, wie bestimmt werden kann, für welchen Port Kestrel zur Laufzeit eine Bindung erstellt hat:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-375">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="c7cf9-376">Wenn die App ausgeführt wird, gibt das Ausgabefenster der Konsole den dynamischen Port an, über den die App erreicht werden kann:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-376">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="c7cf9-377">Einschränkungen</span><span class="sxs-lookup"><span data-stu-id="c7cf9-377">Limitations</span></span>

<span data-ttu-id="c7cf9-378">Konfigurieren Sie Endpunkte mithilfe der folgenden Ansätze:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-378">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="c7cf9-379">Befehlszeilenargument `--urls`</span><span class="sxs-lookup"><span data-stu-id="c7cf9-379">`--urls` command-line argument</span></span>
* <span data-ttu-id="c7cf9-380">Hostkonfigurationsschlüssel `urls`</span><span class="sxs-lookup"><span data-stu-id="c7cf9-380">`urls` host configuration key</span></span>
* <span data-ttu-id="c7cf9-381">Umgebungsvariable `ASPNETCORE_URLS`</span><span class="sxs-lookup"><span data-stu-id="c7cf9-381">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="c7cf9-382">Diese Methoden sind nützlich, wenn Ihr Code mit anderen Servern als Kestrel funktionieren soll.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-382">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="c7cf9-383">Beachten Sie jedoch die folgenden Einschränkungen:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-383">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="c7cf9-384">HTTPS kann nicht mit diesen Ansätzen verwendet werden, außer ein Standardzertifikat wird in der HTTPS-Endpunktkonfiguration angegeben (z.B. wenn Sie wie zuvor in diesem Artikel gezeigt die `KestrelServerOptions`-Konfiguration oder eine Konfigurationsdatei verwenden).</span><span class="sxs-lookup"><span data-stu-id="c7cf9-384">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="c7cf9-385">Wenn die Ansätze `Listen` und `UseUrls` gleichzeitig verwendet werden, überschreiben die `Listen`-Endpunkte die `UseUrls`-Endpunkte.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-385">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="c7cf9-386">IIS-Endpunktkonfiguration</span><span class="sxs-lookup"><span data-stu-id="c7cf9-386">IIS endpoint configuration</span></span>

<span data-ttu-id="c7cf9-387">Bei der Verwendung von IIS werden die URL-Bindungen für IIS-Überschreibungsbindungen durch `Listen` oder `UseUrls` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-387">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="c7cf9-388">Weitere Informationen finden Sie im Artikel [ASP.NET Core-Modul](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="c7cf9-388">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

### <a name="listenoptionsprotocols"></a><span data-ttu-id="c7cf9-389">ListenOptions.Protocols</span><span class="sxs-lookup"><span data-stu-id="c7cf9-389">ListenOptions.Protocols</span></span>

<span data-ttu-id="c7cf9-390">Die `Protocols`-Eigenschaft richtet die HTTP-Protokolle (`HttpProtocols`) ein, die für einen Verbindungsendpunkt oder für den Server aktiviert werden.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-390">The `Protocols` property establishes the HTTP protocols (`HttpProtocols`) enabled on a connection endpoint or for the server.</span></span> <span data-ttu-id="c7cf9-391">Weisen Sie der `Protocols`-Eigenschaft einen Wert aus der `HttpProtocols`-Enumeration zu.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-391">Assign a value to the `Protocols` property from the `HttpProtocols` enum.</span></span>

| <span data-ttu-id="c7cf9-392">`HttpProtocols`-Enumerationswert</span><span class="sxs-lookup"><span data-stu-id="c7cf9-392">`HttpProtocols` enum value</span></span> | <span data-ttu-id="c7cf9-393">Zulässiges Verbindungsprotokoll</span><span class="sxs-lookup"><span data-stu-id="c7cf9-393">Connection protocol permitted</span></span> |
| -------------------------- | ----------------------------- |
| `Http1`                    | <span data-ttu-id="c7cf9-394">Nur HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-394">HTTP/1.1 only.</span></span> <span data-ttu-id="c7cf9-395">Kann mit oder ohne TLS verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-395">Can be used with or without TLS.</span></span> |
| `Http2`                    | <span data-ttu-id="c7cf9-396">Nur HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-396">HTTP/2 only.</span></span> <span data-ttu-id="c7cf9-397">Kann nur ohne TLS verwendet werden, wenn der Client einen [Vorabkenntnis-Modus](https://tools.ietf.org/html/rfc7540#section-3.4) unterstützt.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-397">May be used without TLS only if the client supports a [Prior Knowledge mode](https://tools.ietf.org/html/rfc7540#section-3.4).</span></span> |
| `Http1AndHttp2`            | <span data-ttu-id="c7cf9-398">HTTP/1.1 und HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-398">HTTP/1.1 and HTTP/2.</span></span> <span data-ttu-id="c7cf9-399">Für HTTP/2 muss der Client HTTP/2 im TLS [ALPN-Handshake (Application-Layer Protocol Negotiation)](https://tools.ietf.org/html/rfc7301#section-3) auswählen, andernfalls wird standardmäßig eine HTTP/1.1 verwendet.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-399">HTTP/2 requires the client to select HTTP/2 in the TLS [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) handshake; otherwise, the connection defaults to HTTP/1.1.</span></span> |

<span data-ttu-id="c7cf9-400">Der Standardwert von `ListenOptions.Protocols` ist für alle Endpunkte `HttpProtocols.Http1AndHttp2`.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-400">The default `ListenOptions.Protocols` value for any endpoint is `HttpProtocols.Http1AndHttp2`.</span></span>

<span data-ttu-id="c7cf9-401">TLS-Einschränkungen für HTTP/2:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-401">TLS restrictions for HTTP/2:</span></span>

* <span data-ttu-id="c7cf9-402">TLS Version 1.2 oder höher</span><span class="sxs-lookup"><span data-stu-id="c7cf9-402">TLS version 1.2 or later</span></span>
* <span data-ttu-id="c7cf9-403">Erneute Aushandlung deaktiviert</span><span class="sxs-lookup"><span data-stu-id="c7cf9-403">Renegotiation disabled</span></span>
* <span data-ttu-id="c7cf9-404">Komprimierung deaktiviert</span><span class="sxs-lookup"><span data-stu-id="c7cf9-404">Compression disabled</span></span>
* <span data-ttu-id="c7cf9-405">Minimale Größen für Austausch von flüchtigen Schlüsseln:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-405">Minimum ephemeral key exchange sizes:</span></span>
  * <span data-ttu-id="c7cf9-406">ECDHE (Elliptic Curve Diffie-Hellman) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack;: mindestens 224 Bits</span><span class="sxs-lookup"><span data-stu-id="c7cf9-406">Elliptic curve Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack;: 224 bits minimum</span></span>
  * <span data-ttu-id="c7cf9-407">DHE (Finite Field Diffie-Hellman) &lbrack;`TLS12`&rbrack;: mindestens 2048 Bits</span><span class="sxs-lookup"><span data-stu-id="c7cf9-407">Finite field Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack;: 2048 bits minimum</span></span>
* <span data-ttu-id="c7cf9-408">Verschlüsselungssammlung nicht verboten</span><span class="sxs-lookup"><span data-stu-id="c7cf9-408">Cipher suite not prohibited.</span></span> 

<span data-ttu-id="c7cf9-409">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; mit der elliptischen P-256-Kurve &lbrack;`FIPS186`&rbrack; wird standardmäßig unterstützt.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-409">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; with the P-256 elliptic curve &lbrack;`FIPS186`&rbrack; is supported by default.</span></span>

<span data-ttu-id="c7cf9-410">Das folgende Beispiel erlaubt HTTP/1.1- und HTTP/2-Verbindungen an Port 8000.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-410">The following example permits HTTP/1.1 and HTTP/2 connections on port 8000.</span></span> <span data-ttu-id="c7cf9-411">Die Verbindungen werden durch TLS mit einem bereitgestellten Zertifikat geschützt:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-411">Connections are secured by TLS with a supplied certificate:</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
    });
});
```

<span data-ttu-id="c7cf9-412">Verwenden Sie Verbindungsmiddleware, um TLS-Handshakes auf Verbindungsbasis für bestimmte Verschlüsselungen zu filtern, falls erforderlich.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-412">Use Connection Middleware to filter TLS handshakes on a per-connection basis for specific ciphers if required.</span></span>

<span data-ttu-id="c7cf9-413">Im folgenden Beispiel wird <xref:System.NotSupportedException> für jeden Verschlüsselungsalgorithmus ausgelöst, der von der App nicht unterstützt wird.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-413">The following example throws <xref:System.NotSupportedException> for any cipher algorithm that the app doesn't support.</span></span> <span data-ttu-id="c7cf9-414">Alternativ können Sie [ITlsHandshakeFeature.CipherAlgorithm](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm) definieren und mit einer Liste zulässiger Verschlüsselungssammlungen vergleichen.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-414">Alternatively, define and compare [ITlsHandshakeFeature.CipherAlgorithm](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm) to a list of acceptable cipher suites.</span></span>

<span data-ttu-id="c7cf9-415">Es wird keine Verschlüsselung mit einem [CipherAlgorithmType.Null](xref:System.Security.Authentication.CipherAlgorithmType)-Verschlüsselungsalgorithmus verwendet.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-415">No encryption is used with a [CipherAlgorithmType.Null](xref:System.Security.Authentication.CipherAlgorithmType) cipher algorithm.</span></span>

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

<span data-ttu-id="c7cf9-416">Die Verbindungsfilterung kann auch über einen <xref:Microsoft.AspNetCore.Connections.IConnectionBuilder>-Lambdaausdruck konfiguriert werden:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-416">Connection filtering can also be configured via an <xref:Microsoft.AspNetCore.Connections.IConnectionBuilder> lambda:</span></span>

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

<span data-ttu-id="c7cf9-417">Unter Linux kann <xref:System.Net.Security.CipherSuitesPolicy> zum Filtern von TLS-Handshakes auf Verbindungsbasis verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-417">On Linux, <xref:System.Net.Security.CipherSuitesPolicy> can be used to filter TLS handshakes on a per-connection basis:</span></span>

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

<span data-ttu-id="c7cf9-418">*Festlegen des Protokolls aus der Konfiguration*</span><span class="sxs-lookup"><span data-stu-id="c7cf9-418">*Set the protocol from configuration*</span></span>

<span data-ttu-id="c7cf9-419">`CreateDefaultBuilder` ruft `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` standardmäßig zum Laden der Kestrel-Konfiguration auf.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-419">`CreateDefaultBuilder` calls `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span>

<span data-ttu-id="c7cf9-420">Das folgende *appsettings.json* -Beispiel richtet HTTP/1.1 als Standardverbindungsprotokoll für alle Endpunkte ein:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-420">The following *appsettings.json* example establishes HTTP/1.1 as the default connection protocol for all endpoints:</span></span>

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1"
    }
  }
}
```

<span data-ttu-id="c7cf9-421">Das folgende *appsettings.json* -Beispiel richtet HTTP/1.1 als Verbindungsprotokoll für einen bestimmten Endpunkt ein:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-421">The following *appsettings.json* example establishes the HTTP/1.1 connection protocol for a specific endpoint:</span></span>

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

<span data-ttu-id="c7cf9-422">Protokolle, die in Code angegeben werden, setzen Werte außer Kraft, der durch die Konfiguration festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-422">Protocols specified in code override values set by configuration.</span></span>

### <a name="url-prefixes"></a><span data-ttu-id="c7cf9-423">URL-Präfixe</span><span class="sxs-lookup"><span data-stu-id="c7cf9-423">URL prefixes</span></span>

<span data-ttu-id="c7cf9-424">Bei der Verwendung von `UseUrls`, dem Befehlszeilenargument `--urls`, dem Hostkonfigurationsschlüssel `urls` oder der Umgebungsvariable `ASPNETCORE_URLS` können die URL-Präfixe in den folgenden Formaten vorliegen.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-424">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="c7cf9-425">Nur HTTP-URL-Präfixe sind gültig.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-425">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="c7cf9-426">Kestrel unterstützt HTTPS nicht beim Konfigurieren von URL-Bindungen mit `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-426">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="c7cf9-427">IPv4-Adresse mit Portnummer</span><span class="sxs-lookup"><span data-stu-id="c7cf9-427">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="c7cf9-428">Bei `0.0.0.0` handelt es sich um einen Sonderfall, für den eine Bindung an alle IPv4-Adressen erfolgt.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-428">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="c7cf9-429">IPv6-Adresse mit Portnummer</span><span class="sxs-lookup"><span data-stu-id="c7cf9-429">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="c7cf9-430">`[::]` stellt das Äquivalent von IPv6 zu `0.0.0.0` für IPv4 dar.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-430">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="c7cf9-431">Hostname mit Portnummer</span><span class="sxs-lookup"><span data-stu-id="c7cf9-431">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="c7cf9-432">Hostnamen, `*` und `+` sind nicht spezifisch.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-432">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="c7cf9-433">Alle Elemente, die nicht als gültige IP-Adresse oder `localhost` erkannt werden, werden an alle IPv4- und IPv6-IP-Adressen gebunden.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-433">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="c7cf9-434">Verwenden Sie [HTTP.sys](xref:fundamentals/servers/httpsys) oder einen Reverseproxyserver zum Binden verschiedener Hostnamen an verschiedene ASP.NET Core-Apps auf demselben Port, z.B. IIS, Nginx oder Apache.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-434">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="c7cf9-435">Das Hosting in einer Reverseproxykonfiguration erfordert [Hostfilterung](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="c7cf9-435">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="c7cf9-436">`localhost`-Hostname mit Portnummer oder Loopback-IP mit Portnummer</span><span class="sxs-lookup"><span data-stu-id="c7cf9-436">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="c7cf9-437">Wenn `localhost` angegeben ist, versucht Kestrel, eine Bindung zu IPv4- und IPv6-Loopback-Schnittstellen zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-437">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="c7cf9-438">Wenn der erforderliche Port von einem anderen Dienst auf einer der Loopback-Schnittstellen verwendet wird, tritt beim Starten von Kestrel ein Fehler auf.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-438">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="c7cf9-439">Wenn eine der Loopback-Schnittstellen aus anderen Gründen nicht verfügbar ist (meistens durch die fehlende Unterstützung von IPv6), protokolliert Kestrel eine Warnung.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-439">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="c7cf9-440">Host-Filterung</span><span class="sxs-lookup"><span data-stu-id="c7cf9-440">Host filtering</span></span>

<span data-ttu-id="c7cf9-441">Obwohl Kestrel die Konfiguration basierend auf Präfixe wie `http://example.com:5000` unterstützt, ignoriert Kestrel den Hostnamen weitgehend.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-441">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="c7cf9-442">Der Host `localhost` ist ein Sonderfall, der für die Bindung an Loopback-Adressen verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-442">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="c7cf9-443">Jeder Host, der keine explizite IP-Adresse ist, wird an alle öffentlichen IP-Adressen gebunden.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-443">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="c7cf9-444">`Host`-Header werden nicht überprüft.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-444">`Host` headers aren't validated.</span></span>

<span data-ttu-id="c7cf9-445">Verwenden Sie Middleware zum Filtern von Hosts, um dieses Problem zu umgehen.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-445">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="c7cf9-446">Die Middleware zum Filtern von Hosts wird durch das [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering)-Paket bereitgestellt, das implizit für ASP.NET Core-Apps zur Verfügung steht.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-446">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is implicitly provided for ASP.NET Core apps.</span></span> <span data-ttu-id="c7cf9-447">Die Middleware wird von <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> hinzugefügt, wodurch <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*> aufgerufen wird:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-447">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="c7cf9-448">Die Middleware zum Filtern von Hosts ist standardmäßig deaktiviert.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-448">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="c7cf9-449">Wenn Sie die Middleware aktivieren möchten, definieren Sie einen `AllowedHosts`-Schlüssel in *appsettings.json* /*appsettings.\<EnvironmentName>json*.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-449">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="c7cf9-450">Der Wert ist eine durch Semikolons getrennte Liste von Hostnamen ohne Portnummern:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-450">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="c7cf9-451">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-451">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="c7cf9-452">[Middleware für weitergeleitete Header](xref:host-and-deploy/proxy-load-balancer) hat auch eine <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts>-Option.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-452">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="c7cf9-453">Middleware für weitergeleitete Header und Middleware zum Filtern von Hosts besitzen ähnliche Funktionen für unterschiedliche Szenarios.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-453">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="c7cf9-454">Legen Sie `AllowedHosts` mit Middleware für weitergeleitete Header fest, wenn der `Host`-Header beim Weiterleiten von Anforderungen mit einem Reverseproxyserver oder einem Lastenausgleichsmodul nicht beibehalten wird.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-454">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="c7cf9-455">Legen Sie `AllowedHosts` mit Middleware zum Filtern von Hosts fest, wenn Kestrel als öffentlicher Edgeserver verwendet oder der `Host`-Header direkt weitergeleitet wird.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-455">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="c7cf9-456">Weitere Informationen zu Middleware für weitergeleitete Header finden Sie unter <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-456">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="libuv-transport-configuration"></a><span data-ttu-id="c7cf9-457">Libuv-Transportkonfiguration</span><span class="sxs-lookup"><span data-stu-id="c7cf9-457">Libuv transport configuration</span></span>

<span data-ttu-id="c7cf9-458">Für Projekte, die den Einsatz von Libuv (<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv%2A>) erfordern:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-458">For projects that require the use of Libuv (<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv%2A>):</span></span>

* <span data-ttu-id="c7cf9-459">Fügen Sie der Projektdatei der App eine Abhängigkeit für das [`Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv`](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv)-Paket hinzu:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-459">Add a dependency for the [`Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv`](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv) package to the app's project file:</span></span>

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* <span data-ttu-id="c7cf9-460"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv%2A> auf `IWebHostBuilder` aufrufen:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-460">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv%2A> on the `IWebHostBuilder`:</span></span>

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

## <a name="http11-request-draining"></a><span data-ttu-id="c7cf9-461">Leeren von HTTP/1.1-Anforderungen</span><span class="sxs-lookup"><span data-stu-id="c7cf9-461">HTTP/1.1 request draining</span></span>

<span data-ttu-id="c7cf9-462">Das Öffnen von HTTP-Verbindungen ist zeitaufwendig.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-462">Opening HTTP connections is time consuming.</span></span> <span data-ttu-id="c7cf9-463">Bei HTTPS ist es zudem ressourcenintensiv.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-463">For HTTPS, it's also resource intensive.</span></span> <span data-ttu-id="c7cf9-464">Daher versucht Kestrel, Verbindungen gemäß dem HTTP/1.1-Protokoll wiederzuverwenden.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-464">Therefore, Kestrel tries to reuse connections per the HTTP/1.1 protocol.</span></span> <span data-ttu-id="c7cf9-465">Ein Anforderungstext muss vollständig genutzt worden sein, damit die Verbindung wiederverwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-465">A request body must be fully consumed to allow the connection to be reused.</span></span> <span data-ttu-id="c7cf9-466">Die App nutzt nicht immer den Anforderungstext, z. B. bei einer `POST`-Anforderung, bei der der Server eine Umleitung oder 404-Antwort zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-466">The app doesn't always consume the request body, such as a `POST` requests where the server returns a redirect or 404 response.</span></span> <span data-ttu-id="c7cf9-467">Im Fall der `POST`-Umleitung:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-467">In the `POST`-redirect case:</span></span>

* <span data-ttu-id="c7cf9-468">Der Client hat möglicherweise bereits einen Teil der `POST`-Daten gesendet.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-468">The client may already have sent part of the `POST` data.</span></span>
* <span data-ttu-id="c7cf9-469">Der Server schreibt die 301-Antwort.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-469">The server writes the 301 response.</span></span>
* <span data-ttu-id="c7cf9-470">Die Verbindung kann erst dann für eine neue Anforderung verwendet werden, wenn die `POST`-Daten im vorherigen Anforderungstext vollständig gelesen wurden.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-470">The connection can't be used for a new request until the `POST` data from the previous request body has been fully read.</span></span>
* <span data-ttu-id="c7cf9-471">Kestrel versucht, den Anforderungstext zu leeren.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-471">Kestrel tries to drain the request body.</span></span> <span data-ttu-id="c7cf9-472">Leeren des Anforderungstexts bedeutet, die Daten zu lesen und zu verwerfen, ohne sie zu verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-472">Draining the request body means reading and discarding the data without processing it.</span></span>

<span data-ttu-id="c7cf9-473">Beim Leerungsvorgang wird ein Kompromiss zwischen der Möglichkeit der Wiederverwendung der Verbindung und der Dauer gefunden, die zum Leeren der verbleibenden Daten benötigt wird:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-473">The draining process makes a tradoff between allowing the connection to be reused and the time it takes to drain any remaining data:</span></span>

* <span data-ttu-id="c7cf9-474">Für das Leeren gilt ein Zeitlimit von fünf Sekunden, das nicht konfigurierbar ist.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-474">Draining has a timeout of five seconds, which isn't configurable.</span></span>
* <span data-ttu-id="c7cf9-475">Wenn vor dem Zeitlimit nicht alle durch den Header `Content-Length` oder `Transfer-Encoding` angegebenen Daten gelesen wurden, wird die Verbindung geschlossen.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-475">If all of the data specified by the `Content-Length` or `Transfer-Encoding` header hasn't been read before the timeout, the connection is closed.</span></span>

<span data-ttu-id="c7cf9-476">Mitunter möchten Sie die Anforderung sofort beenden, entweder bevor oder nachdem die Antwort geschrieben wurde.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-476">Sometimes you may want to terminate the request immediately, before or after writing the response.</span></span> <span data-ttu-id="c7cf9-477">Beispielsweise können für Clients restriktive Datenobergrenzen gelten, sodass das Begrenzen hochgeladener Daten Priorität haben kann.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-477">For example, clients may have restrictive data caps, so limiting uploaded data might be a priority.</span></span> <span data-ttu-id="c7cf9-478">Um in solchen Fällen eine Anforderung zu beenden, rufen Sie [HttpContext.abort](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A) in einem Controller, eine Razor Page oder Middleware auf.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-478">In such cases to terminate a request, call [HttpContext.Abort](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A) from a controller, Razor Page, or middleware.</span></span>

<span data-ttu-id="c7cf9-479">Gegen den Aufruf von `Abort` gibt es Vorbehalte:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-479">There are caveats to calling `Abort`:</span></span>

* <span data-ttu-id="c7cf9-480">Das Erstellen neuer Verbindungen kann langsam und aufwendig sein.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-480">Creating new connections can be slow and expensive.</span></span>
* <span data-ttu-id="c7cf9-481">Es gibt keine Garantie, dass der Client die Antwort gelesen hat, bevor die Verbindung geschlossen wird.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-481">There's no guarantee that the client has read the response before the connection closes.</span></span>
* <span data-ttu-id="c7cf9-482">Das Aufrufen von `Abort` sollte selten erfolgen und schweren Fehlerfällen und nicht gewöhnlichen Fehlern vorbehalten sein.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-482">Calling `Abort` should be rare and reserved for severe error cases, not common errors.</span></span>
  * <span data-ttu-id="c7cf9-483">Rufen Sie `Abort` nur dann auf, wenn ein konkretes Problem gelöst werden muss.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-483">Only call `Abort` when a specific problem needs to be solved.</span></span> <span data-ttu-id="c7cf9-484">Rufen Sie beispielsweise `Abort` auf, wenn böswillige Clients versuchen, Daten per `POST` abzurufen, oder wenn es einen Fehler im Clientcode gibt, der umfangreiche oder zahlreiche Anforderungen verursacht.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-484">For example, call `Abort` if malicious clients are trying to `POST` data or when there's a bug in client code that causes large or numerous requests.</span></span>
  * <span data-ttu-id="c7cf9-485">Rufen Sie `Abort` nicht für gewöhnliche Fehlersituationen auf, wie z. B. HTTP 404 (Nicht gefunden).</span><span class="sxs-lookup"><span data-stu-id="c7cf9-485">Don't call `Abort` for common error situations, such as HTTP 404 (Not Found).</span></span>

<span data-ttu-id="c7cf9-486">Durch den Aufruf von [HttpResponse.CompleteAsync](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A) vor dem Aufruf von `Abort` wird sichergestellt, dass der Server das Schreiben der Antwort abgeschlossen hat.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-486">Calling [HttpResponse.CompleteAsync](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A) before calling `Abort` ensures that the server has completed writing the response.</span></span> <span data-ttu-id="c7cf9-487">Das Clientverhalten ist jedoch nicht vorhersagbar, und es kann sein, dass die Antwort nicht gelesen wird, bevor die Verbindung abgebrochen wird.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-487">However, client behavior isn't predictable and they may not read the response before the connection is aborted.</span></span>

<span data-ttu-id="c7cf9-488">Dieser Prozess bei HTTP/2 ist anders, da das Protokoll den Abbruch einzelner Anforderungsströme ohne Schließen der Verbindung unterstützt.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-488">This process is different for HTTP/2 because the protocol supports aborting individual request streams without closing the connection.</span></span> <span data-ttu-id="c7cf9-489">Das fünfsekündige Zeitlimit für das Leeren gilt nicht.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-489">The five second drain timeout doesn't apply.</span></span> <span data-ttu-id="c7cf9-490">Wenn nach dem Fertigstellen einer Antwort ungelesene Anforderungstextdaten vorhanden sind, sendet der Server einen HTTP/2-RST-Datenrahmen.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-490">If there's any unread request body data after completing a response, then the server sends an HTTP/2 RST frame.</span></span> <span data-ttu-id="c7cf9-491">Zusätzliche Datenrahmen im Anforderungstext werden ignoriert.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-491">Additional request body data frames are ignored.</span></span>

<span data-ttu-id="c7cf9-492">Wenn möglich, ist es für Clients besser, den Anforderungsheader [Expect: 100-continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) zu verwenden und auf die Antwort des Servers zu warten, bevor mit dem Senden des Anforderungstexts begonnen wird.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-492">If possible, it's better for clients to utilize the [Expect: 100-continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) request header and wait for the server to respond before starting to send the request body.</span></span> <span data-ttu-id="c7cf9-493">Das gibt dem Client die Gelegenheit, die Antwort zu prüfen und abzubrechen, bevor nicht benötigte Daten gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-493">That gives the client an opportunity to examine the response and abort before sending unneeded data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c7cf9-494">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="c7cf9-494">Additional resources</span></span>

* <span data-ttu-id="c7cf9-495">Bei der Verwendung von UNIX-Sockets unter Linux wird der Socket beim Herunterfahren der App nicht automatisch gelöscht.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-495">When using UNIX sockets on Linux, the socket is not automatically deleted on app shut down.</span></span> <span data-ttu-id="c7cf9-496">Weitere Informationen finden Sie in [diesem GitHub-Issue](https://github.com/dotnet/aspnetcore/issues/14134).</span><span class="sxs-lookup"><span data-stu-id="c7cf9-496">For more information, see [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/14134).</span></span>
* <xref:test/troubleshoot>
* <xref:security/enforcing-ssl>
* <xref:host-and-deploy/proxy-load-balancer>
* [<span data-ttu-id="c7cf9-497">RFC 7230: Message Syntax and Routing (Abschnitt 5.4: Host)</span><span class="sxs-lookup"><span data-stu-id="c7cf9-497">RFC 7230: Message Syntax and Routing (Section 5.4: Host)</span></span>](https://tools.ietf.org/html/rfc7230#section-5.4)

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="c7cf9-498">Einführung in Kestrel, dem plattformübergreifenden [Webserver für ASP.NET Core](xref:fundamentals/servers/index).</span><span class="sxs-lookup"><span data-stu-id="c7cf9-498">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="c7cf9-499">Kestrel ist der Webserver, der standardmäßig in ASP.NET Core-Projektvorlagen enthalten ist.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-499">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="c7cf9-500">Kestrel unterstützt die folgenden Szenarios:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-500">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="c7cf9-501">HTTPS</span><span class="sxs-lookup"><span data-stu-id="c7cf9-501">HTTPS</span></span>
* <span data-ttu-id="c7cf9-502">Nicht transparente Upgrades, die zum Aktivieren von [WebSockets](https://github.com/aspnet/websockets) verwendet werden</span><span class="sxs-lookup"><span data-stu-id="c7cf9-502">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="c7cf9-503">Unix-Sockets für eine hohe Leistung im Hintergrund von Nginx</span><span class="sxs-lookup"><span data-stu-id="c7cf9-503">Unix sockets for high performance behind Nginx</span></span>
* <span data-ttu-id="c7cf9-504">HTTP/2 (außer unter macOS&dagger;)</span><span class="sxs-lookup"><span data-stu-id="c7cf9-504">HTTP/2 (except on macOS&dagger;)</span></span>

<span data-ttu-id="c7cf9-505">&dagger;HTTP/2 wird unter macOS in einem zukünftigen Release unterstützt.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-505">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>

<span data-ttu-id="c7cf9-506">Kestrel wird auf allen Plattformen und für alle Versionen unterstützt, die .NET Core unterstützt.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-506">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="c7cf9-507">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples/2.x) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="c7cf9-507">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples/2.x) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="http2-support"></a><span data-ttu-id="c7cf9-508">HTTP/2-Unterstützung</span><span class="sxs-lookup"><span data-stu-id="c7cf9-508">HTTP/2 support</span></span>

<span data-ttu-id="c7cf9-509">[HTTP/2](https://httpwg.org/specs/rfc7540.html) ist für ASP.NET Core-Apps verfügbar, wenn die folgenden Basisanforderungen erfüllt sind:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-509">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is available for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="c7cf9-510">Betriebssystem&dagger;</span><span class="sxs-lookup"><span data-stu-id="c7cf9-510">Operating system&dagger;</span></span>
  * <span data-ttu-id="c7cf9-511">Windows Server 2016/Windows 10 oder höher&Dagger;</span><span class="sxs-lookup"><span data-stu-id="c7cf9-511">Windows Server 2016/Windows 10 or later&Dagger;</span></span>
  * <span data-ttu-id="c7cf9-512">Linux mit OpenSSL 1.0.2 oder höher (z.B. Ubuntu 16.04 oder höher)</span><span class="sxs-lookup"><span data-stu-id="c7cf9-512">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
* <span data-ttu-id="c7cf9-513">Zielframework: .NET Core 2.2 oder höher</span><span class="sxs-lookup"><span data-stu-id="c7cf9-513">Target framework: .NET Core 2.2 or later</span></span>
* <span data-ttu-id="c7cf9-514">[ALPN](https://tools.ietf.org/html/rfc7301#section-3)-Verbindung (Application-Layer Protocol Negotiation)</span><span class="sxs-lookup"><span data-stu-id="c7cf9-514">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="c7cf9-515">TLS 1.2-Verbindung oder höher</span><span class="sxs-lookup"><span data-stu-id="c7cf9-515">TLS 1.2 or later connection</span></span>

<span data-ttu-id="c7cf9-516">&dagger;HTTP/2 wird unter macOS in einem zukünftigen Release unterstützt.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-516">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>
<span data-ttu-id="c7cf9-517">&Dagger;Kestrel bietet eingeschränkte Unterstützung für HTTP/2 unter Windows Server 2012 R2 und Windows 8.1.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-517">&Dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="c7cf9-518">Die Unterstützung ist eingeschränkt, weil die Liste der unterstützten TLS-Verschlüsselungssammlungen unter diesen Betriebssystemen begrenzt ist.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-518">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="c7cf9-519">Zum Sichern von TLS-Verbindungen ist möglicherweise ein durch einen Elliptic Curve Digital Signature Algorithm (ECDSA) generiertes Zertifikat erforderlich.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-519">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

<span data-ttu-id="c7cf9-520">Wenn eine HTTP/2-Verbindung hergestellt wurde, meldet [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*)`HTTP/2`.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-520">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="c7cf9-521">HTTP/2 ist standardmäßig deaktiviert.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-521">HTTP/2 is disabled by default.</span></span> <span data-ttu-id="c7cf9-522">Weitere Informationen zur Konfiguration finden Sie in den Abschnitten [Kestrel-Optionen](#kestrel-options) und [ListenOptions.Protocols](#listenoptionsprotocols).</span><span class="sxs-lookup"><span data-stu-id="c7cf9-522">For more information on configuration, see the [Kestrel options](#kestrel-options) and [ListenOptions.Protocols](#listenoptionsprotocols) sections.</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="c7cf9-523">Verwenden von Kestrel mit einem Reverseproxy</span><span class="sxs-lookup"><span data-stu-id="c7cf9-523">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="c7cf9-524">Kestrel kann eigenständig oder mit einem *Reverseproxyserver* wie z.B. [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org) oder [Apache](https://httpd.apache.org/) verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-524">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="c7cf9-525">Ein Reverseproxyserver empfängt HTTP-Anforderungen aus dem Netzwerk und leitet diese an Kestrel weiter.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-525">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="c7cf9-526">Kestrel bei Verwendung als Webserver mit direkter Internetverbindung:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-526">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Kestrel kommuniziert direkt und ohne Reverseproxyserver mit dem Internet](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="c7cf9-528">Kestrel bei Verwendung in einer Reverseproxykonfiguration:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-528">Kestrel used in a reverse proxy configuration:</span></span>

![Kestrel kommuniziert indirekt mit dem Internet über einen Reverseproxyserver wie IIS, Nginx oder Apache](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="c7cf9-530">Jede der beiden Konfigurationen – mit oder ohne einen Reverseproxyserver – stellt eine unterstützte Hostingkonfiguration dar.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-530">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="c7cf9-531">Bei Verwendung als Edgeserver ohne Reverseproxyserver unterstützt Kestrel die gemeinsame Nutzung der gleichen IP-Adresse und des gleichen Ports durch mehrere Prozesse nicht.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-531">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="c7cf9-532">Wenn Kestrel für das Lauschen an einem Port konfiguriert ist, verarbeitet Kestrel den gesamten Datenverkehr für diesen Port unabhängig von den `Host`-Headern der Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-532">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="c7cf9-533">Ein Reverseproxy, der Ports freigeben kann, kann Anforderungen an Kestrel über eine eindeutige IP und einen eindeutigen Port weiterleiten.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-533">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="c7cf9-534">Auch wenn kein Reverseproxyserver erforderlich ist, kann die Verwendung eines solchen empfehlenswert sein.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-534">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="c7cf9-535">Für einen Reverseproxy gilt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-535">A reverse proxy:</span></span>

* <span data-ttu-id="c7cf9-536">Er kann die verfügbar gemachten öffentlichen Oberflächen der von ihm gehosteten Apps einschränken.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-536">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="c7cf9-537">Er stellt eine zusätzliche Ebene für Konfiguration und Schutz bereit.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-537">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="c7cf9-538">Er lässt sich besser in die vorhandene Infrastruktur integrieren.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-538">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="c7cf9-539">Er vereinfacht die Konfiguration von Lastenausgleich und sicherer Kommunikation (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="c7cf9-539">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="c7cf9-540">Nur der Reverseproxyserver erfordert ein X.509-Zertifikat, und dieser Server kann mit den Servern der App im internen Netzwerk über einfaches HTTP kommunizieren.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-540">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="c7cf9-541">Das Hosting in einer Reverseproxykonfiguration erfordert [Hostfilterung](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="c7cf9-541">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a><span data-ttu-id="c7cf9-542">Verwenden von Kestrel in ASP.NET Core-Apps</span><span class="sxs-lookup"><span data-stu-id="c7cf9-542">How to use Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="c7cf9-543">Das Paket [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) ist im Metapaket [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) enthalten.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-543">The [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="c7cf9-544">ASP.NET Core-Projektvorlagen verwenden Kestrel standardmäßig.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-544">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="c7cf9-545">Der Vorlagencode in *Program.cs* ruft <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> auf, wodurch <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> im Hintergrund aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-545">In *Program.cs*, the template code calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> behind the scenes.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=7)]

<span data-ttu-id="c7cf9-546">Weitere Informationen zum `CreateDefaultBuilder` und zum Erstellen des Hosts finden Sie im Abschnitt *Einrichten eines Hosts* von <xref:fundamentals/host/web-host#set-up-a-host>.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-546">For more information on `CreateDefaultBuilder` and building the host, see the *Set up a host* section of <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

<span data-ttu-id="c7cf9-547">Um zusätzliche Konfiguration nach dem Aufruf von `CreateDefaultBuilder` bereitzustellen, verwenden Sie `ConfigureKestrel`:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-547">To provide additional configuration after calling `CreateDefaultBuilder`, use `ConfigureKestrel`:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            // Set properties and call methods on serverOptions
        });
```

<span data-ttu-id="c7cf9-548">Wenn die App `CreateDefaultBuilder` nicht aufruft, um den Host einzurichten, rufen Sie <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> **auf**, bevor Sie `ConfigureKestrel` aufrufen:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-548">If the app doesn't call `CreateDefaultBuilder` to set up the host, call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> **before** calling `ConfigureKestrel`:</span></span>

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

## <a name="kestrel-options"></a><span data-ttu-id="c7cf9-549">Kestrel-Optionen</span><span class="sxs-lookup"><span data-stu-id="c7cf9-549">Kestrel options</span></span>

<span data-ttu-id="c7cf9-550">Der Kestrel-Webserver verfügt über einschränkende Konfigurationsoptionen, die besonders nützlich bei Bereitstellungen mit Internetzugriff sind.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-550">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="c7cf9-551">Legen Sie Einschränkungen für die <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits>-Eigenschaft der <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>-Klasse fest.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-551">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="c7cf9-552">Die `Limits`-Eigenschaft enthält eine Instanz der <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>-Klasse.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-552">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="c7cf9-553">In den folgenden Beispielen wird der <xref:Microsoft.AspNetCore.Server.Kestrel.Core>-Namespace verwendet:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-553">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="c7cf9-554">Kestrel-Optionen, die in den folgenden Beispielen in C#-Code konfiguriert sind, können auch mit einem [Konfigurationsanbieter](xref:fundamentals/configuration/index) festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-554">Kestrel options, which are configured in C# code in the following examples, can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="c7cf9-555">Beispielsweise kann der Dateikonfigurationsanbieter die Kestrel-Konfiguration aus einer *appsettings.json* - oder *appsettings.{Umgebung}.json*-Datei laden:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-555">For example, the File Configuration Provider can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

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

<span data-ttu-id="c7cf9-556">Verwenden Sie **einen** der folgenden Ansätze:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-556">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="c7cf9-557">Konfigurieren Sie Kestrel in `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-557">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="c7cf9-558">Fügen Sie eine Instanz von `IConfiguration` in die `Startup`-Klasse ein.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-558">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="c7cf9-559">Im folgenden Beispiel wird davon ausgegangen, dass die eingefügte Konfiguration der `Configuration`-Eigenschaft zugewiesen wird.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-559">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="c7cf9-560">Laden Sie in `Startup.ConfigureServices` den Abschnitt `Kestrel` der Konfiguration in die Konfiguration von Kestrel:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-560">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

* <span data-ttu-id="c7cf9-561">Konfigurieren Sie Kestrel beim Erstellen des Hosts:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-561">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="c7cf9-562">Laden Sie in *Program.cs* den Abschnitt `Kestrel` der Konfiguration in die Konfiguration von Kestrel:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-562">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

<span data-ttu-id="c7cf9-563">Beide vorangehenden Ansätze funktionieren mit jedem [Konfigurationsanbieter](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="c7cf9-563">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="c7cf9-564">Keep-Alive-Timeout</span><span class="sxs-lookup"><span data-stu-id="c7cf9-564">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="c7cf9-565">Ruft das [Keep-Alive-Timeout](https://tools.ietf.org/html/rfc7230#section-6.5) ab oder legt es fest.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-565">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="c7cf9-566">Standardwert: 2 Minuten.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-566">Defaults to 2 minutes.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=15)]

### <a name="maximum-client-connections"></a><span data-ttu-id="c7cf9-567">Maximale Anzahl der Clientverbindungen</span><span class="sxs-lookup"><span data-stu-id="c7cf9-567">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="c7cf9-568">Die maximale Anzahl von gleichzeitig geöffneten TCP-Verbindungen kann mithilfe von folgendem Code für die gesamte App festgelegt werden:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-568">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

<span data-ttu-id="c7cf9-569">Es gibt einen separaten Grenzwert für Verbindungen, die von HTTP oder HTTPS auf ein anderes Protokoll aktualisiert wurden (z.B. auf eine WebSockets-Anforderung).</span><span class="sxs-lookup"><span data-stu-id="c7cf9-569">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="c7cf9-570">Nachdem eine Verbindung aktualisiert wurde, zählt diese nicht mehr für den `MaxConcurrentConnections`-Grenzwert.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-570">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

<span data-ttu-id="c7cf9-571">Die maximale Anzahl von Verbindungen ist standardmäßig nicht begrenzt (NULL).</span><span class="sxs-lookup"><span data-stu-id="c7cf9-571">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="c7cf9-572">Maximale Größe des Anforderungstexts</span><span class="sxs-lookup"><span data-stu-id="c7cf9-572">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="c7cf9-573">Die maximale Größe des Anforderungstexts beträgt standardmäßig 30.000.000 Byte, also ungefähr 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-573">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="c7cf9-574">Die empfohlene Methode zur Außerkraftsetzung des Grenzwerts in einer ASP.NET Core-MVC-App besteht im Verwenden des <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute>-Attributs in einer Aktionsmethode:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-574">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="c7cf9-575">Im folgenden Beispiel wird veranschaulicht, wie die Einschränkungen auf jeder Anforderung für die App konfiguriert werden:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-575">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

<span data-ttu-id="c7cf9-576">Außer Kraft setzen der Einstellung für eine bestimmte Anforderung in Middleware:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-576">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="c7cf9-577">Eine Ausnahme wird ausgelöst, wenn die App den Grenzwert einer Anforderung konfiguriert, nachdem die App bereits mit dem Lesen der Anforderung begonnen hat.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-577">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="c7cf9-578">Es gibt eine `IsReadOnly`-Eigenschaft, die angibt, wenn sich die `MaxRequestBodySize`-Eigenschaft im schreibgeschützten Zustand befindet, also wenn der Grenzwert nicht mehr konfiguriert werden kann.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-578">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="c7cf9-579">Wenn eine App [prozessextern](xref:host-and-deploy/iis/index#out-of-process-hosting-model) hinter dem [ASP.NET Core-Modul](xref:host-and-deploy/aspnet-core-module) ausgeführt wird, ist das Größenlimit von Kestrel für Anforderungstext deaktiviert, weil IIS dieses Limit bereits festlegt.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-579">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="c7cf9-580">Minimale Datenrate des Anforderungstexts</span><span class="sxs-lookup"><span data-stu-id="c7cf9-580">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="c7cf9-581">Kestrel überprüft sekündlich, ob Daten mit der angegebenen Rate in Bytes/Sekunde eingehen.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-581">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="c7cf9-582">Wenn die Rate den mindestens erforderlichen Wert unterschreitet, wird für die Verbindung wegen Timeout getrennt. Bei der Toleranzperiode handelt es sich um die Zeitspanne, die Kestrel dem Client gewährt, um die Senderate auf den mindestens erforderlichen Wert zu erhöhen. Die Rate wird währenddessen nicht überprüft.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-582">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="c7cf9-583">Diese Toleranzperiode beugt dem Trennen von Verbindungen vor, die Daten aufgrund eines langsamen TCP-Starts anfänglich mit einer niedrigen Rate senden.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-583">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="c7cf9-584">Die mindestens erforderliche Rate beträgt standardmäßig 240 Bytes/Sekunde mit einer Toleranzperiode von 5 Sekunden.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-584">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="c7cf9-585">Für die Antwort gilt ebenfalls eine mindestens erforderliche Rate.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-585">A minimum rate also applies to the response.</span></span> <span data-ttu-id="c7cf9-586">Der Code zum Festlegen des Grenzwerts für Anforderung und Antwort ist abgesehen von `RequestBody` oder `Response` in den Namen von Eigenschaften und Schnittstelle identisch.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-586">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="c7cf9-587">In diesem Beispiel wird veranschaulicht, wie Sie die mindestens erforderlichen Datenraten in *Program.cs* konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-587">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-9)]

<span data-ttu-id="c7cf9-588">Außer Kraft setzen des minimalen Ratenlimits pro Anforderung in Middleware:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-588">Override the minimum rate limits per request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

<span data-ttu-id="c7cf9-589">Keines dieser Ratenfeatures, auf die im vorherigen Beispiel verwiesen wird, ist in `HttpContext.Features` für HTTP/2-Anforderungen vorhanden, da die Änderung von Ratenlimits für jede Anforderung aufgrund der Unterstützung für Anforderungsmultiplexing des Protokolls nicht für HTTP/2 unterstützt wird.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-589">Neither rate feature referenced in the prior sample are present in `HttpContext.Features` for HTTP/2 requests because modifying rate limits on a per-request basis isn't supported for HTTP/2 due to the protocol's support for request multiplexing.</span></span> <span data-ttu-id="c7cf9-590">Serverweite Ratenlimits, die über `KestrelServerOptions.Limits` konfiguriert sind, gelten auch für HTTP/1.x- und HTTP/2-Verbindungen.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-590">Server-wide rate limits configured via `KestrelServerOptions.Limits` still apply to both HTTP/1.x and HTTP/2 connections.</span></span>

### <a name="request-headers-timeout"></a><span data-ttu-id="c7cf9-591">Timeout für Anforderungsheader</span><span class="sxs-lookup"><span data-stu-id="c7cf9-591">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="c7cf9-592">Ruft die maximale Zeitspanne ab, während der der Server Anforderungsheader empfängt, oder legt diese fest.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-592">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="c7cf9-593">Der Standardwert beträgt 30 Sekunden.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-593">Defaults to 30 seconds.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=16)]

### <a name="maximum-streams-per-connection"></a><span data-ttu-id="c7cf9-594">Maximale Datenströme pro Verbindung</span><span class="sxs-lookup"><span data-stu-id="c7cf9-594">Maximum streams per connection</span></span>

<span data-ttu-id="c7cf9-595">`Http2.MaxStreamsPerConnection` schränkt die Anzahl gleichzeitiger Anforderungsdatenströme pro HTTP/2-Verbindung ein.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-595">`Http2.MaxStreamsPerConnection` limits the number of concurrent request streams per HTTP/2 connection.</span></span> <span data-ttu-id="c7cf9-596">Überschüssige Datenströme werden zurückgewiesen.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-596">Excess streams are refused.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
        });
```

<span data-ttu-id="c7cf9-597">Der Standardwert ist 100.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-597">The default value is 100.</span></span>

### <a name="header-table-size"></a><span data-ttu-id="c7cf9-598">Größe der Headertabelle</span><span class="sxs-lookup"><span data-stu-id="c7cf9-598">Header table size</span></span>

<span data-ttu-id="c7cf9-599">Der HPACK-Decoder dekomprimiert HTTP-Header für HTTP/2-Verbindungen.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-599">The HPACK decoder decompresses HTTP headers for HTTP/2 connections.</span></span> <span data-ttu-id="c7cf9-600">`Http2.HeaderTableSize` schränkt die Größe der Headerkomprimierungstabelle ein, die der HPACK-Decoder verwendet.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-600">`Http2.HeaderTableSize` limits the size of the header compression table that the HPACK decoder uses.</span></span> <span data-ttu-id="c7cf9-601">Der Wert wird in Oktetten bereitgestellt und muss größer als null (0) sein.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-601">The value is provided in octets and must be greater than zero (0).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.HeaderTableSize = 4096;
        });
```

<span data-ttu-id="c7cf9-602">Der Standardwert ist 4096.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-602">The default value is 4096.</span></span>

### <a name="maximum-frame-size"></a><span data-ttu-id="c7cf9-603">Maximale Framegröße</span><span class="sxs-lookup"><span data-stu-id="c7cf9-603">Maximum frame size</span></span>

<span data-ttu-id="c7cf9-604">`Http2.MaxFrameSize` gibt die maximale Größe der zu empfangenden HTTP/2-Verbindungsframenutzlast an.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-604">`Http2.MaxFrameSize` indicates the maximum size of the HTTP/2 connection frame payload to receive.</span></span> <span data-ttu-id="c7cf9-605">Der Wert wird in Oktetten bereitgestellt und muss zwischen 2^14 (16.384) und 2^24-1 (16.777.215) liegen.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-605">The value is provided in octets and must be between 2^14 (16,384) and 2^24-1 (16,777,215).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxFrameSize = 16384;
        });
```

<span data-ttu-id="c7cf9-606">Der Standardwert ist 2^14 (16.384).</span><span class="sxs-lookup"><span data-stu-id="c7cf9-606">The default value is 2^14 (16,384).</span></span>

### <a name="maximum-request-header-size"></a><span data-ttu-id="c7cf9-607">Maximale Größe des Anforderungsheaders</span><span class="sxs-lookup"><span data-stu-id="c7cf9-607">Maximum request header size</span></span>

<span data-ttu-id="c7cf9-608">`Http2.MaxRequestHeaderFieldSize` gibt die maximal zulässige Größe in Oktetten der Anforderungsheaderwerte an.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-608">`Http2.MaxRequestHeaderFieldSize` indicates the maximum allowed size in octets of request header values.</span></span> <span data-ttu-id="c7cf9-609">Dieser Grenzwert gilt zusammen für den Namen und Wert in komprimierten und nicht komprimierten Darstellungen.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-609">This limit applies to both name and value together in their compressed and uncompressed representations.</span></span> <span data-ttu-id="c7cf9-610">Der Wert muss größer als 0 (null) sein.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-610">The value must be greater than zero (0).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
        });
```

<span data-ttu-id="c7cf9-611">Der Standardwert ist 8.192.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-611">The default value is 8,192.</span></span>

### <a name="initial-connection-window-size"></a><span data-ttu-id="c7cf9-612">Anfangsfenstergröße der Verbindung</span><span class="sxs-lookup"><span data-stu-id="c7cf9-612">Initial connection window size</span></span>

<span data-ttu-id="c7cf9-613">`Http2.InitialConnectionWindowSize` gibt die maximalen Anforderungstextdaten in Byte an, die der Server auf einmal für alle Anforderungen (Streams) pro Verbindung aggregiert.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-613">`Http2.InitialConnectionWindowSize` indicates the maximum request body data in bytes the server buffers at one time aggregated across all requests (streams) per connection.</span></span> <span data-ttu-id="c7cf9-614">Anforderungen werden auch durch `Http2.InitialStreamWindowSize` beschränkt.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-614">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="c7cf9-615">Der Wert muss größer als oder gleich 65.535 und kleiner als 2^31 (2.147.483.648) sein.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-615">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
        });
```

<span data-ttu-id="c7cf9-616">Der Standardwert ist 128 KB (131.072).</span><span class="sxs-lookup"><span data-stu-id="c7cf9-616">The default value is 128 KB (131,072).</span></span>

### <a name="initial-stream-window-size"></a><span data-ttu-id="c7cf9-617">Anfangsfenstergröße des Streams</span><span class="sxs-lookup"><span data-stu-id="c7cf9-617">Initial stream window size</span></span>

<span data-ttu-id="c7cf9-618">`Http2.InitialStreamWindowSize` gibt die maximalen Anforderungstextdaten in Byte an, die der Server auf einmal pro Anforderung (Stream) puffert.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-618">`Http2.InitialStreamWindowSize` indicates the maximum request body data in bytes the server buffers at one time per request (stream).</span></span> <span data-ttu-id="c7cf9-619">Anforderungen werden auch durch `Http2.InitialStreamWindowSize` beschränkt.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-619">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="c7cf9-620">Der Wert muss größer als oder gleich 65.535 und kleiner als 2^31 (2.147.483.648) sein.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-620">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
        });
```

<span data-ttu-id="c7cf9-621">Der Standardwert ist 96 KB (98.304).</span><span class="sxs-lookup"><span data-stu-id="c7cf9-621">The default value is 96 KB (98,304).</span></span>

### <a name="synchronous-io"></a><span data-ttu-id="c7cf9-622">Synchrone E/A-Vorgänge</span><span class="sxs-lookup"><span data-stu-id="c7cf9-622">Synchronous I/O</span></span>

<span data-ttu-id="c7cf9-623"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> steuert, ob für Anforderung und Antwort synchrone E/A-Vorgänge zulässig sind.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-623"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous I/O is allowed for the request and response.</span></span> <span data-ttu-id="c7cf9-624">Der Standardwert ist `true`.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-624">The  default value is `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="c7cf9-625">Sehr viele blockierende synchrone E/A-Vorgänge können zu einem Ressourcenmangel im Threadpool führen, wodurch die App nicht mehr reagiert.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-625">A large number of blocking synchronous I/O operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="c7cf9-626">Aktivieren Sie `AllowSynchronousIO` nur bei Verwendung einer Bibliothek, die asynchrone E/A-Vorgänge nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-626">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous I/O.</span></span>

<span data-ttu-id="c7cf9-627">Das folgende Beispiel aktiviert synchrone E/A-Vorgänge:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-627">The following example enables synchronous I/O:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

<span data-ttu-id="c7cf9-628">Weitere Informationen zu anderen Kestrel-Optionen und -Einschränkungen finden Sie unter:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-628">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="c7cf9-629">Endpunktkonfiguration</span><span class="sxs-lookup"><span data-stu-id="c7cf9-629">Endpoint configuration</span></span>

<span data-ttu-id="c7cf9-630">Standardmäßig wird ASP.NET Core an Folgendes gebunden:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-630">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="c7cf9-631">`https://localhost:5001` (wenn ein lokales Entwicklungszertifikat vorhanden ist)</span><span class="sxs-lookup"><span data-stu-id="c7cf9-631">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="c7cf9-632">Verwenden Sie Folgendes zum Angeben der URLs:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-632">Specify URLs using the:</span></span>

* <span data-ttu-id="c7cf9-633">Die Umgebungsvariable `ASPNETCORE_URLS`</span><span class="sxs-lookup"><span data-stu-id="c7cf9-633">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="c7cf9-634">Das Befehlszeilenargument `--urls`</span><span class="sxs-lookup"><span data-stu-id="c7cf9-634">`--urls` command-line argument.</span></span>
* <span data-ttu-id="c7cf9-635">Den Hostkonfigurationsschlüssel `urls`</span><span class="sxs-lookup"><span data-stu-id="c7cf9-635">`urls` host configuration key.</span></span>
* <span data-ttu-id="c7cf9-636">Die Erweiterungsmethode `UseUrls`</span><span class="sxs-lookup"><span data-stu-id="c7cf9-636">`UseUrls` extension method.</span></span>

<span data-ttu-id="c7cf9-637">Der Wert, der mit diesen Ansätzen angegeben wird, kann mindestens ein HTTP- oder HTTPS-Endpunkt sein (HTTPS wenn ein Standardzertifikat verfügbar ist).</span><span class="sxs-lookup"><span data-stu-id="c7cf9-637">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="c7cf9-638">Konfigurieren Sie den Wert als eine durch Semikolons getrennte Liste (z.B. `"Urls": "http://localhost:8000;http://localhost:8001"`).</span><span class="sxs-lookup"><span data-stu-id="c7cf9-638">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="c7cf9-639">Weitere Informationen zu diesen Ansätzen finden Sie unter [Server-URLs](xref:fundamentals/host/web-host#server-urls) und [Außerkraftsetzen der Konfiguration](xref:fundamentals/host/web-host#override-configuration).</span><span class="sxs-lookup"><span data-stu-id="c7cf9-639">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="c7cf9-640">Ein Entwicklungszertifikat wird erstellt:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-640">A development certificate is created:</span></span>

* <span data-ttu-id="c7cf9-641">Wenn das [.NET Core SDK](/dotnet/core/sdk) installiert wird.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-641">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="c7cf9-642">Das [dev-cert-Tool](xref:aspnetcore-2.1#https) wird zum Erstellen eines Zertifikats verwendet.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-642">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="c7cf9-643">Einige Browser erfordern, dass Sie die explizite Berechtigung erteilen, dem lokalen Entwicklungszertifikat zu vertrauen.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-643">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="c7cf9-644">Projektvorlagen konfigurieren Apps, damit sie standardmäßig auf HTTPS ausgeführt werden und die [HTTPS-Umleitung und HSTS-Unterstützung](xref:security/enforcing-ssl) enthalten.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-644">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="c7cf9-645">Rufen Sie die <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*>- oder <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*>-Methode unter <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> auf, um URL-Präfixe und Ports für Kestrel zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-645">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="c7cf9-646">`UseUrls`, das Befehlszeilenargument `--urls`, der Hostkonfigurationsschlüssel `urls` und die Umgebungsvariable `ASPNETCORE_URLS` funktionieren ebenfalls, verfügen jedoch über Einschränkungen, die im Verlauf dieses Abschnitts erläutert werden (Ein Standardzertifikat muss für die HTTPS-Endpunktkonfiguration verfügbar sein).</span><span class="sxs-lookup"><span data-stu-id="c7cf9-646">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="c7cf9-647">`KestrelServerOptions`-Konfiguration:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-647">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="c7cf9-648">ConfigureEndpointDefaults(Action\<ListenOptions>)</span><span class="sxs-lookup"><span data-stu-id="c7cf9-648">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="c7cf9-649">Gibt die Konfiguration von `Action` zum Ausführen von jedem angegebenen Endpunkt an.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-649">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="c7cf9-650">Mehrmalige Aufrufe von `ConfigureEndpointDefaults` ersetzen vorherige Instanzen von `Action` mit der zuletzt angegebenen `Action`.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-650">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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
> <span data-ttu-id="c7cf9-651">Auf Endpunkte, die durch Aufrufen von <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **vor** dem Aufrufen von <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> erstellt werden, werden die Standardwerte nicht angewendet.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-651">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> won't have the defaults applied.</span></span>

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="c7cf9-652">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span><span class="sxs-lookup"><span data-stu-id="c7cf9-652">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="c7cf9-653">Gibt die Konfiguration von `Action` zum Ausführen von jedem HTTPS-Endpunkt an.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-653">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="c7cf9-654">Mehrmalige Aufrufe von `ConfigureHttpsDefaults` ersetzen vorherige Instanzen von `Action` mit der zuletzt angegebenen `Action`.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-654">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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
> <span data-ttu-id="c7cf9-655">Auf Endpunkte, die durch Aufrufen von <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **vor** dem Aufrufen von <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> erstellt werden, werden die Standardwerte nicht angewendet.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-655">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>


### <a name="configureiconfiguration"></a><span data-ttu-id="c7cf9-656">Configure(IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="c7cf9-656">Configure(IConfiguration)</span></span>

<span data-ttu-id="c7cf9-657">Erstellt ein Konfigurationsladeprogramm für das Einrichten von Kestrel, was <xref:Microsoft.Extensions.Configuration.IConfiguration> als Eingabe erfordert.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-657">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="c7cf9-658">Die Konfiguration muss auf den Konfigurationsabschnitt für Kestrel festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-658">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="c7cf9-659">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="c7cf9-659">ListenOptions.UseHttps</span></span>

<span data-ttu-id="c7cf9-660">Konfiguriert Kestrel zur Verwendung von HTTPS.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-660">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="c7cf9-661">`ListenOptions.UseHttps`-Erweiterungen:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-661">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="c7cf9-662">`UseHttps`: Hiermit wird Kestrel zur Verwendung von HTTPS mit dem Standardzertifikat konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-662">`UseHttps`: Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="c7cf9-663">Löst eine Ausnahme aus, wenn kein Standardzertifikat konfiguriert ist.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-663">Throws an exception if no default certificate is configured.</span></span>
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

<span data-ttu-id="c7cf9-664">`ListenOptions.UseHttps`-Parameter:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-664">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="c7cf9-665">`filename` entspricht dem Pfad und Dateinamen einer Zertifikatdatei relativ zu dem Verzeichnis, das die Inhaltsdateien der App enthält.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-665">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="c7cf9-666">`password` ist das für den Zugriff auf die X.509-Zertifikatsdaten erforderliche Kennwort.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-666">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="c7cf9-667">`configureOptions` ist eine `Action` zum Konfigurieren von `HttpsConnectionAdapterOptions`.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-667">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="c7cf9-668">Gibt `ListenOptions` zurück.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-668">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="c7cf9-669">`storeName` ist der Zertifikatspeicher, aus dem das Zertifikat geladen wird.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-669">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="c7cf9-670">`subject` ist der Name des Antragstellers für das Zertifikat.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-670">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="c7cf9-671">`allowInvalid` gibt an, ob ungültige Zertifikate berücksichtigt werden sollten, z.B. selbstsignierte Zertifikate.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-671">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="c7cf9-672">`location` ist der Speicherort, aus dem das Zertifikat geladen wird.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-672">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="c7cf9-673">`serverCertificate` ist das X.509-Zertifikat.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-673">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="c7cf9-674">In der Produktion muss HTTPS explizit konfiguriert sein.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-674">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="c7cf9-675">Zumindest muss ein Standardzertifikat angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-675">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="c7cf9-676">Die im Folgenden beschriebenen unterstützten Konfigurationen:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-676">Supported configurations described next:</span></span>

* <span data-ttu-id="c7cf9-677">Keine Konfiguration</span><span class="sxs-lookup"><span data-stu-id="c7cf9-677">No configuration</span></span>
* <span data-ttu-id="c7cf9-678">Ersetzen des Standardzertifikats aus der Konfiguration</span><span class="sxs-lookup"><span data-stu-id="c7cf9-678">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="c7cf9-679">Ändern des Standards im Code</span><span class="sxs-lookup"><span data-stu-id="c7cf9-679">Change the defaults in code</span></span>

<span data-ttu-id="c7cf9-680">*Keine Konfiguration*</span><span class="sxs-lookup"><span data-stu-id="c7cf9-680">*No configuration*</span></span>

<span data-ttu-id="c7cf9-681">Kestrel überwacht `http://localhost:5000` und `https://localhost:5001` (wenn ein Standardzertifikat verfügbar ist).</span><span class="sxs-lookup"><span data-stu-id="c7cf9-681">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="c7cf9-682">*Ersetzen des Standardzertifikats aus der Konfiguration*</span><span class="sxs-lookup"><span data-stu-id="c7cf9-682">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="c7cf9-683">`CreateDefaultBuilder` ruft `Configure(context.Configuration.GetSection("Kestrel"))` standardmäßig zum Laden der Kestrel-Konfiguration auf.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-683">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="c7cf9-684">Ein Standardkonfigurationsschema für HTTPS-App-Einstellungen ist für Kestrel verfügbar.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-684">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="c7cf9-685">Konfigurieren Sie mehrere Endpunkte, einschließlich der zu verwendenden URLs und Zertifikate aus einer Datei auf dem Datenträger oder einem Zertifikatspeicher.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-685">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="c7cf9-686">Im folgenden Beispiel für *appsettings.json* gilt:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-686">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="c7cf9-687">Legen Sie **AllowInvalid** auf `true` fest, um die Verwendung von ungültigen Zertifikaten zu erlauben (z.B. selbstsignierte Zertifikate).</span><span class="sxs-lookup"><span data-stu-id="c7cf9-687">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="c7cf9-688">Jeder HTTPS-Endpunkt, der kein Zertifikat angibt (im folgenden Beispiel **HttpsDefaultCert**), greift auf das unter **Zertifikate** > **Standard** festgelegte Zertifikat oder das Entwicklungszertifikat zurück.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-688">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

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

<span data-ttu-id="c7cf9-689">Alternativ zur Verwendung von **Pfad** und **Kennwort** für alle Zertifikatknoten können Sie das Zertifikat mithilfe von Zertifikatspeicherfeldern angeben.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-689">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="c7cf9-690">Das Zertifikat unter **Zertifikate** > **Standard** kann beispielweise wie folgt angegeben werden:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-690">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="c7cf9-691">Schema-Hinweise:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-691">Schema notes:</span></span>

* <span data-ttu-id="c7cf9-692">Bei den Namen der Endpunkte wird die Groß-/Kleinschreibung nicht beachtet.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-692">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="c7cf9-693">Zum Beispiel sind `HTTPS` und `Https` gültig.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-693">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="c7cf9-694">Der Parameter `Url` ist für jeden Endpunkt erforderlich.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-694">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="c7cf9-695">Das Format für diesen Parameter ist identisch mit dem allgemeinen Konfigurationsparameter `Urls`, mit der Ausnahme, dass er auf einen einzelnen Wert begrenzt ist.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-695">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="c7cf9-696">Diese Endpunkte ersetzen die in der allgemeinen `Urls`-Konfiguration festgelegten Endpunkte, anstatt zu ihnen hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-696">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="c7cf9-697">Endpunkte, die über `Listen` in Code definiert werden, werden den im Konfigurationsabschnitt definierten Endpunkten hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-697">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="c7cf9-698">Der `Certificate`-Abschnitt ist optional.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-698">The `Certificate` section is optional.</span></span> <span data-ttu-id="c7cf9-699">Wenn der `Certificate`-Abschnitt nicht angegeben ist, werden die in früheren Szenarios definierten Standardwerte verwendet.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-699">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="c7cf9-700">Wenn keine Standardwerte verfügbar sind, löst der Server eine Ausnahme aus und startet nicht.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-700">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="c7cf9-701">Der `Certificate`-Abschnitt unterstützt die Zertifikate **Pfad**&ndash;**Kennwort** und **Subject**&ndash;**Store** (Antragsteller–Speicher).</span><span class="sxs-lookup"><span data-stu-id="c7cf9-701">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="c7cf9-702">Auf diese Weise kann eine beliebige Anzahl von Endpunkten definiert werden, solange Sie keine Portkonflikte verursachen.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-702">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="c7cf9-703">`options.Configure(context.Configuration.GetSection("{SECTION}"))` gibt `KestrelConfigurationLoader` mit der Methode `.Endpoint(string name, listenOptions => { })` zurück, die dazu verwendet werden kann, die Einstellungen eines Endpunkts zu ergänzen:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-703">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

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

<span data-ttu-id="c7cf9-704">Auf `KestrelServerOptions.ConfigurationLoader` kann direkt zugegriffen werden, um die Iteration auf dem vorhandenen Ladeprogramm fortzusetzen, etwa auf dem von <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> bereitgestellten Ladeprogramm.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-704">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="c7cf9-705">Der Konfigurationsabschnitt ist für jeden Endpunkt in den Optionen der Methode `Endpoint` verfügbar, sodass benutzerdefinierte Einstellungen gelesen werden können.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-705">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="c7cf9-706">Mehrere Konfigurationen können durch erneutes Aufrufen von `options.Configure(context.Configuration.GetSection("{SECTION}"))` mit einem anderen Abschnitt geladen werden.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-706">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="c7cf9-707">Sofern `Load` nicht in einer vorherigen Instanz explizit aufgerufen wird, wird nur die letzte Konfiguration verwendet.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-707">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="c7cf9-708">Das Metapaket ruft `Load` nicht auf, sodass der Abschnitt mit der Standardkonfiguration ersetzt werden kann.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-708">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="c7cf9-709">`KestrelConfigurationLoader` spiegelt die API-Familie `Listen` von `KestrelServerOptions` als `Endpoint`-Überladungen, weshalb Code und Konfigurationsendpunkte am selben Ort konfiguriert werden können.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-709">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="c7cf9-710">Diese Überladungen verwenden keine Namen und nutzen nur Standardeinstellungen aus der Konfiguration.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-710">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="c7cf9-711">*Ändern des Standards im Code*</span><span class="sxs-lookup"><span data-stu-id="c7cf9-711">*Change the defaults in code*</span></span>

<span data-ttu-id="c7cf9-712">`ConfigureEndpointDefaults` und `ConfigureHttpsDefaults` können zum Ändern der Standardeinstellungen für `ListenOptions` und `HttpsConnectionAdapterOptions` verwendet werden, einschließlich der Standardzertifikate, die im vorherigen Szenario festgelegt wurden.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-712">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="c7cf9-713">`ConfigureEndpointDefaults` und `ConfigureHttpsDefaults` sollten aufgerufen werden, bevor Endpunkte konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-713">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

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

<span data-ttu-id="c7cf9-714">*Kestrel-Unterstützung für SNI*</span><span class="sxs-lookup"><span data-stu-id="c7cf9-714">*Kestrel support for SNI*</span></span>

<span data-ttu-id="c7cf9-715">Die [Servernamensanzeige (SNI)](https://tools.ietf.org/html/rfc6066#section-3) kann zum Hosten mehrerer Domänen auf der gleichen IP-Adresse und dem gleichen Port verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-715">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="c7cf9-716">Damit die Servernamensanzeige funktioniert, sendet der Client während des TLS-Handshakes den Hostnamen für die sichere Sitzung an den Server, sodass der Server das richtige Zertifikat bereitstellen kann.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-716">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="c7cf9-717">Der Client verwendet das beigestellte Zertifikat für die verschlüsselte Kommunikation mit dem Server während der sicheren Sitzung nach dem TLS-Handshake.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-717">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="c7cf9-718">Kestrel unterstützt die Servernamensanzeige über den `ServerCertificateSelector`-Rückruf.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-718">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="c7cf9-719">Der Rückruf wird für jede Verbindung einmal aufgerufen, um der App zu ermöglichen, den Hostnamen zu überprüfen und das entsprechende Zertifikat auszuwählen.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-719">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="c7cf9-720">Für die Unterstützung der Servernamensanzeige benötigen Sie Folgendes:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-720">SNI support requires:</span></span>

* <span data-ttu-id="c7cf9-721">Wird auf dem Zielframework `netcoreapp2.1` oder höher ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-721">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="c7cf9-722">In `net461` oder höher, wird der Rückruf aufgerufen, `name` ist aber immer `null`.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-722">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="c7cf9-723">`name` ist auch `null`, wenn der Client den Hostnamenparameter nicht im TLS-Handshake angibt.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-723">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="c7cf9-724">Alle Websites werden in derselben Kestrel-Instanz ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-724">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="c7cf9-725">Kestrel unterstützt ohne Reverseproxy keine gemeinsame IP-Adresse und keinen gemeinsamen Port für mehrere Instanzen.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-725">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

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

### <a name="connection-logging"></a><span data-ttu-id="c7cf9-726">Verbindungsprotokollierung</span><span class="sxs-lookup"><span data-stu-id="c7cf9-726">Connection logging</span></span>

<span data-ttu-id="c7cf9-727">Rufen Sie <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> auf, um Protokolle auf Debugebene für die Kommunikation auf Byteebene für eine Verbindung auszugeben.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-727">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="c7cf9-728">Die Verbindungsprotokollierung ist beim Beheben von Problemen bei der Low-Level-Kommunikation hilfreich, wie z. B. bei der TLS-Verschlüsselung und bei Proxys.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-728">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="c7cf9-729">Wenn `UseConnectionLogging` vor `UseHttps` platziert wird, wird der verschlüsselte Datenverkehr protokolliert.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-729">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="c7cf9-730">Wenn `UseConnectionLogging` nach `UseHttps` platziert wird, wird der entschlüsselte Datenverkehr protokolliert.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-730">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="c7cf9-731">Binden an einen TCP-Socket</span><span class="sxs-lookup"><span data-stu-id="c7cf9-731">Bind to a TCP socket</span></span>

<span data-ttu-id="c7cf9-732">Die <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*>-Methode wird an ein TCP-Socket gebunden, und ein Lambdaausdruck einer Option lässt die Konfiguration des X.509-Zertifikats zu:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-732">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=9-16)]

<span data-ttu-id="c7cf9-733">Im Beispiel wird HTTPS für einen Endpunkt mit <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions> konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-733">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="c7cf9-734">Verwenden Sie die gleiche API zum Konfigurieren anderer Kestrel-Einstellungen für bestimmte Endpunkte.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-734">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="c7cf9-735">Binden an einen Unix-Socket</span><span class="sxs-lookup"><span data-stu-id="c7cf9-735">Bind to a Unix socket</span></span>

<span data-ttu-id="c7cf9-736">Wie in diesem Beispiel dargestellt, lauschen Sie an einem Unix-Socket mit <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*>, um eine verbesserte Leistung mit Nginx zu erzielen:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-736">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

* <span data-ttu-id="c7cf9-737">Legen Sie in der Nginx-Konfigurationsdatei den Eintrag `server` > `location` > `proxy_pass` auf `http://unix:/tmp/{KESTREL SOCKET}:/;` fest.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-737">In the Nginx confiuguration file, set the `server` > `location` > `proxy_pass` entry to `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span></span> <span data-ttu-id="c7cf9-738">`{KESTREL SOCKET}` ist der Name des für <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> bereitgestellten Socket (zum Beispiel `kestrel-test.sock` im vorherigen Beispiel).</span><span class="sxs-lookup"><span data-stu-id="c7cf9-738">`{KESTREL SOCKET}` is the name of the socket provided to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (for example, `kestrel-test.sock` in the preceding example).</span></span>
* <span data-ttu-id="c7cf9-739">Stellen Sie sicher, dass der Socket von Nginx beschreibbar ist (z. B. `chmod go+w /tmp/kestrel-test.sock`).</span><span class="sxs-lookup"><span data-stu-id="c7cf9-739">Ensure that the socket is writeable by Nginx (for example, `chmod go+w /tmp/kestrel-test.sock`).</span></span> 

### <a name="port-0"></a><span data-ttu-id="c7cf9-740">Port 0</span><span class="sxs-lookup"><span data-stu-id="c7cf9-740">Port 0</span></span>

<span data-ttu-id="c7cf9-741">Wenn die Portnummer `0` angegeben wird, wird Kestrel dynamisch an einen verfügbaren Port gebunden.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-741">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="c7cf9-742">Im folgenden Beispiel wird veranschaulicht, wie bestimmt werden kann, für welchen Port Kestrel zur Laufzeit eine Bindung erstellt hat:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-742">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="c7cf9-743">Wenn die App ausgeführt wird, gibt das Ausgabefenster der Konsole den dynamischen Port an, über den die App erreicht werden kann:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-743">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="c7cf9-744">Einschränkungen</span><span class="sxs-lookup"><span data-stu-id="c7cf9-744">Limitations</span></span>

<span data-ttu-id="c7cf9-745">Konfigurieren Sie Endpunkte mithilfe der folgenden Ansätze:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-745">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="c7cf9-746">Befehlszeilenargument `--urls`</span><span class="sxs-lookup"><span data-stu-id="c7cf9-746">`--urls` command-line argument</span></span>
* <span data-ttu-id="c7cf9-747">Hostkonfigurationsschlüssel `urls`</span><span class="sxs-lookup"><span data-stu-id="c7cf9-747">`urls` host configuration key</span></span>
* <span data-ttu-id="c7cf9-748">Umgebungsvariable `ASPNETCORE_URLS`</span><span class="sxs-lookup"><span data-stu-id="c7cf9-748">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="c7cf9-749">Diese Methoden sind nützlich, wenn Ihr Code mit anderen Servern als Kestrel funktionieren soll.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-749">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="c7cf9-750">Beachten Sie jedoch die folgenden Einschränkungen:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-750">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="c7cf9-751">HTTPS kann nicht mit diesen Ansätzen verwendet werden, außer ein Standardzertifikat wird in der HTTPS-Endpunktkonfiguration angegeben (z.B. wenn Sie wie zuvor in diesem Artikel gezeigt die `KestrelServerOptions`-Konfiguration oder eine Konfigurationsdatei verwenden).</span><span class="sxs-lookup"><span data-stu-id="c7cf9-751">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="c7cf9-752">Wenn die Ansätze `Listen` und `UseUrls` gleichzeitig verwendet werden, überschreiben die `Listen`-Endpunkte die `UseUrls`-Endpunkte.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-752">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="c7cf9-753">IIS-Endpunktkonfiguration</span><span class="sxs-lookup"><span data-stu-id="c7cf9-753">IIS endpoint configuration</span></span>

<span data-ttu-id="c7cf9-754">Bei der Verwendung von IIS werden die URL-Bindungen für IIS-Überschreibungsbindungen durch `Listen` oder `UseUrls` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-754">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="c7cf9-755">Weitere Informationen finden Sie im Artikel [ASP.NET Core-Modul](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="c7cf9-755">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

### <a name="listenoptionsprotocols"></a><span data-ttu-id="c7cf9-756">ListenOptions.Protocols</span><span class="sxs-lookup"><span data-stu-id="c7cf9-756">ListenOptions.Protocols</span></span>

<span data-ttu-id="c7cf9-757">Die `Protocols`-Eigenschaft richtet die HTTP-Protokolle (`HttpProtocols`) ein, die für einen Verbindungsendpunkt oder für den Server aktiviert werden.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-757">The `Protocols` property establishes the HTTP protocols (`HttpProtocols`) enabled on a connection endpoint or for the server.</span></span> <span data-ttu-id="c7cf9-758">Weisen Sie der `Protocols`-Eigenschaft einen Wert aus der `HttpProtocols`-Enumeration zu.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-758">Assign a value to the `Protocols` property from the `HttpProtocols` enum.</span></span>

| <span data-ttu-id="c7cf9-759">`HttpProtocols`-Enumerationswert</span><span class="sxs-lookup"><span data-stu-id="c7cf9-759">`HttpProtocols` enum value</span></span> | <span data-ttu-id="c7cf9-760">Zulässiges Verbindungsprotokoll</span><span class="sxs-lookup"><span data-stu-id="c7cf9-760">Connection protocol permitted</span></span> |
| -------------------------- | ----------------------------- |
| `Http1`                    | <span data-ttu-id="c7cf9-761">Nur HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-761">HTTP/1.1 only.</span></span> <span data-ttu-id="c7cf9-762">Kann mit oder ohne TLS verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-762">Can be used with or without TLS.</span></span> |
| `Http2`                    | <span data-ttu-id="c7cf9-763">Nur HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-763">HTTP/2 only.</span></span> <span data-ttu-id="c7cf9-764">Kann nur ohne TLS verwendet werden, wenn der Client einen [Vorabkenntnis-Modus](https://tools.ietf.org/html/rfc7540#section-3.4) unterstützt.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-764">May be used without TLS only if the client supports a [Prior Knowledge mode](https://tools.ietf.org/html/rfc7540#section-3.4).</span></span> |
| `Http1AndHttp2`            | <span data-ttu-id="c7cf9-765">HTTP/1.1 und HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-765">HTTP/1.1 and HTTP/2.</span></span> <span data-ttu-id="c7cf9-766">HTTP/2 erfordert eine TLS- und [ALPN](https://tools.ietf.org/html/rfc7301#section-3)-Verbindung (Application-Layer Protocol Negotiation). Andernfalls wird für die Verbindung standardmäßig HTTP/1.1 verwendet.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-766">HTTP/2 requires a TLS and [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection; otherwise, the connection defaults to HTTP/1.1.</span></span> |

<span data-ttu-id="c7cf9-767">Das Standardprotokoll ist HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-767">The default protocol is HTTP/1.1.</span></span>

<span data-ttu-id="c7cf9-768">TLS-Einschränkungen für HTTP/2:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-768">TLS restrictions for HTTP/2:</span></span>

* <span data-ttu-id="c7cf9-769">TLS Version 1.2 oder höher</span><span class="sxs-lookup"><span data-stu-id="c7cf9-769">TLS version 1.2 or later</span></span>
* <span data-ttu-id="c7cf9-770">Erneute Aushandlung deaktiviert</span><span class="sxs-lookup"><span data-stu-id="c7cf9-770">Renegotiation disabled</span></span>
* <span data-ttu-id="c7cf9-771">Komprimierung deaktiviert</span><span class="sxs-lookup"><span data-stu-id="c7cf9-771">Compression disabled</span></span>
* <span data-ttu-id="c7cf9-772">Minimale Größen für Austausch von flüchtigen Schlüsseln:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-772">Minimum ephemeral key exchange sizes:</span></span>
  * <span data-ttu-id="c7cf9-773">ECDHE (Elliptic Curve Diffie-Hellman) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack;: mindestens 224 Bits</span><span class="sxs-lookup"><span data-stu-id="c7cf9-773">Elliptic curve Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack;: 224 bits minimum</span></span>
  * <span data-ttu-id="c7cf9-774">DHE (Finite Field Diffie-Hellman) &lbrack;`TLS12`&rbrack;: mindestens 2048 Bits</span><span class="sxs-lookup"><span data-stu-id="c7cf9-774">Finite field Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack;: 2048 bits minimum</span></span>
* <span data-ttu-id="c7cf9-775">Verschlüsselungssammlung nicht gesperrt</span><span class="sxs-lookup"><span data-stu-id="c7cf9-775">Cipher suite not blocked</span></span>

<span data-ttu-id="c7cf9-776">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; mit der elliptischen P-256-Kurve &lbrack;`FIPS186`&rbrack; wird standardmäßig unterstützt.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-776">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; with the P-256 elliptic curve &lbrack;`FIPS186`&rbrack; is supported by default.</span></span>

<span data-ttu-id="c7cf9-777">Das folgende Beispiel erlaubt HTTP/1.1- und HTTP/2-Verbindungen an Port 8000.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-777">The following example permits HTTP/1.1 and HTTP/2 connections on port 8000.</span></span> <span data-ttu-id="c7cf9-778">Die Verbindungen werden durch TLS mit einem bereitgestellten Zertifikat geschützt:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-778">Connections are secured by TLS with a supplied certificate:</span></span>

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

<span data-ttu-id="c7cf9-779">Erstellen Sie optional eine `IConnectionAdapter`-Implementierung, um TLS-Handshakes auf Verbindungsbasis für bestimmte Verschlüsselungen zu filtern:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-779">Optionally create an `IConnectionAdapter` implementation to filter TLS handshakes on a per-connection basis for specific ciphers:</span></span>

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

<span data-ttu-id="c7cf9-780">*Festlegen des Protokolls aus der Konfiguration*</span><span class="sxs-lookup"><span data-stu-id="c7cf9-780">*Set the protocol from configuration*</span></span>

<span data-ttu-id="c7cf9-781"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> ruft `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` standardmäßig zum Laden der Kestrel-Konfiguration auf.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-781"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> calls `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span>

<span data-ttu-id="c7cf9-782">Im folgenden Beispiel *appsettings.json* wird für alle Endpunkte von Kestrel ein Standardverbindungsprotokoll (HTTP/1.1 und HTTP/2) eingerichtet:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-782">In the following *appsettings.json* example, a default connection protocol (HTTP/1.1 and HTTP/2) is established for all of Kestrel's endpoints:</span></span>

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1AndHttp2"
    }
  }
}
```

<span data-ttu-id="c7cf9-783">Das folgende Beispiel einer Konfigurationsdatei richtet ein Verbindungsprotokoll für einen bestimmten Endpunkt ein:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-783">The following configuration file example establishes a connection protocol for a specific endpoint:</span></span>

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

<span data-ttu-id="c7cf9-784">Protokolle, die in Code angegeben werden, setzen Werte außer Kraft, der durch die Konfiguration festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-784">Protocols specified in code override values set by configuration.</span></span>

## <a name="libuv-transport-configuration"></a><span data-ttu-id="c7cf9-785">Libuv-Transportkonfiguration</span><span class="sxs-lookup"><span data-stu-id="c7cf9-785">Libuv transport configuration</span></span>

<span data-ttu-id="c7cf9-786">Mit dem Release von ASP.NET Core 2.1 basiert der Standardtransport von Kestrel nicht mehr auf Libuv, sondern auf verwalteten Sockets.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-786">With the release of ASP.NET Core 2.1, Kestrel's default transport is no longer based on Libuv but instead based on managed sockets.</span></span> <span data-ttu-id="c7cf9-787">Dies stellt einen Breaking Change für ASP.NET Core 2.0-Apps dar, die auf Version 2.1 upgraden, <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> aufrufen und von einem der folgenden Pakete abhängig sind:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-787">This is a breaking change for ASP.NET Core 2.0 apps upgrading to 2.1 that call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> and depend on either of the following packages:</span></span>

* <span data-ttu-id="c7cf9-788">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (direkter Paketverweis)</span><span class="sxs-lookup"><span data-stu-id="c7cf9-788">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (direct package reference)</span></span>
* [<span data-ttu-id="c7cf9-789">Microsoft.AspNetCore.App</span><span class="sxs-lookup"><span data-stu-id="c7cf9-789">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)

<span data-ttu-id="c7cf9-790">Für Projekte, die den Einsatz von Libuv erfordern:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-790">For projects that require the use of Libuv:</span></span>

* <span data-ttu-id="c7cf9-791">Fügen Sie für das [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/)-Paket eine Abhängigkeit auf die Projektdatei der App hinzu:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-791">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* <span data-ttu-id="c7cf9-792">Rufen Sie <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> auf:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-792">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span></span>

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

### <a name="url-prefixes"></a><span data-ttu-id="c7cf9-793">URL-Präfixe</span><span class="sxs-lookup"><span data-stu-id="c7cf9-793">URL prefixes</span></span>

<span data-ttu-id="c7cf9-794">Bei der Verwendung von `UseUrls`, dem Befehlszeilenargument `--urls`, dem Hostkonfigurationsschlüssel `urls` oder der Umgebungsvariable `ASPNETCORE_URLS` können die URL-Präfixe in den folgenden Formaten vorliegen.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-794">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="c7cf9-795">Nur HTTP-URL-Präfixe sind gültig.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-795">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="c7cf9-796">Kestrel unterstützt HTTPS nicht beim Konfigurieren von URL-Bindungen mit `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-796">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="c7cf9-797">IPv4-Adresse mit Portnummer</span><span class="sxs-lookup"><span data-stu-id="c7cf9-797">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="c7cf9-798">Bei `0.0.0.0` handelt es sich um einen Sonderfall, für den eine Bindung an alle IPv4-Adressen erfolgt.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-798">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="c7cf9-799">IPv6-Adresse mit Portnummer</span><span class="sxs-lookup"><span data-stu-id="c7cf9-799">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="c7cf9-800">`[::]` stellt das Äquivalent von IPv6 zu `0.0.0.0` für IPv4 dar.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-800">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="c7cf9-801">Hostname mit Portnummer</span><span class="sxs-lookup"><span data-stu-id="c7cf9-801">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="c7cf9-802">Hostnamen, `*` und `+` sind nicht spezifisch.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-802">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="c7cf9-803">Alle Elemente, die nicht als gültige IP-Adresse oder `localhost` erkannt werden, werden an alle IPv4- und IPv6-IP-Adressen gebunden.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-803">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="c7cf9-804">Verwenden Sie [HTTP.sys](xref:fundamentals/servers/httpsys) oder einen Reverseproxyserver zum Binden verschiedener Hostnamen an verschiedene ASP.NET Core-Apps auf demselben Port, z.B. IIS, Nginx oder Apache.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-804">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="c7cf9-805">Das Hosting in einer Reverseproxykonfiguration erfordert [Hostfilterung](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="c7cf9-805">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="c7cf9-806">`localhost`-Hostname mit Portnummer oder Loopback-IP mit Portnummer</span><span class="sxs-lookup"><span data-stu-id="c7cf9-806">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="c7cf9-807">Wenn `localhost` angegeben ist, versucht Kestrel, eine Bindung zu IPv4- und IPv6-Loopback-Schnittstellen zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-807">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="c7cf9-808">Wenn der erforderliche Port von einem anderen Dienst auf einer der Loopback-Schnittstellen verwendet wird, tritt beim Starten von Kestrel ein Fehler auf.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-808">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="c7cf9-809">Wenn eine der Loopback-Schnittstellen aus anderen Gründen nicht verfügbar ist (meistens durch die fehlende Unterstützung von IPv6), protokolliert Kestrel eine Warnung.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-809">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="c7cf9-810">Host-Filterung</span><span class="sxs-lookup"><span data-stu-id="c7cf9-810">Host filtering</span></span>

<span data-ttu-id="c7cf9-811">Obwohl Kestrel die Konfiguration basierend auf Präfixe wie `http://example.com:5000` unterstützt, ignoriert Kestrel den Hostnamen weitgehend.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-811">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="c7cf9-812">Der Host `localhost` ist ein Sonderfall, der für die Bindung an Loopback-Adressen verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-812">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="c7cf9-813">Jeder Host, der keine explizite IP-Adresse ist, wird an alle öffentlichen IP-Adressen gebunden.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-813">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="c7cf9-814">`Host`-Header werden nicht überprüft.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-814">`Host` headers aren't validated.</span></span>

<span data-ttu-id="c7cf9-815">Verwenden Sie Middleware zum Filtern von Hosts, um dieses Problem zu umgehen.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-815">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="c7cf9-816">Middleware zum Filtern von Hosts wird im Paket [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) bereitgestellt, das im [Metapaket Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) enthalten ist (ASP.NET Core 2.1 oder 2.2).</span><span class="sxs-lookup"><span data-stu-id="c7cf9-816">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or 2.2).</span></span> <span data-ttu-id="c7cf9-817">Die Middleware wird von <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> hinzugefügt, wodurch <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*> aufgerufen wird:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-817">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="c7cf9-818">Die Middleware zum Filtern von Hosts ist standardmäßig deaktiviert.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-818">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="c7cf9-819">Wenn Sie die Middleware aktivieren möchten, definieren Sie einen `AllowedHosts`-Schlüssel in *appsettings.json* /*appsettings.\<EnvironmentName>json*.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-819">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="c7cf9-820">Der Wert ist eine durch Semikolons getrennte Liste von Hostnamen ohne Portnummern:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-820">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="c7cf9-821">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-821">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="c7cf9-822">[Middleware für weitergeleitete Header](xref:host-and-deploy/proxy-load-balancer) hat auch eine <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts>-Option.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-822">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="c7cf9-823">Middleware für weitergeleitete Header und Middleware zum Filtern von Hosts besitzen ähnliche Funktionen für unterschiedliche Szenarios.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-823">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="c7cf9-824">Legen Sie `AllowedHosts` mit Middleware für weitergeleitete Header fest, wenn der `Host`-Header beim Weiterleiten von Anforderungen mit einem Reverseproxyserver oder einem Lastenausgleichsmodul nicht beibehalten wird.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-824">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="c7cf9-825">Legen Sie `AllowedHosts` mit Middleware zum Filtern von Hosts fest, wenn Kestrel als öffentlicher Edgeserver verwendet oder der `Host`-Header direkt weitergeleitet wird.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-825">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="c7cf9-826">Weitere Informationen zu Middleware für weitergeleitete Header finden Sie unter <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-826">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="http11-request-draining"></a><span data-ttu-id="c7cf9-827">Leeren von HTTP/1.1-Anforderungen</span><span class="sxs-lookup"><span data-stu-id="c7cf9-827">HTTP/1.1 request draining</span></span>

<span data-ttu-id="c7cf9-828">Das Öffnen von HTTP-Verbindungen ist zeitaufwendig.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-828">Opening HTTP connections is time consuming.</span></span> <span data-ttu-id="c7cf9-829">Bei HTTPS ist es zudem ressourcenintensiv.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-829">For HTTPS, it's also resource intensive.</span></span> <span data-ttu-id="c7cf9-830">Daher versucht Kestrel, Verbindungen gemäß dem HTTP/1.1-Protokoll wiederzuverwenden.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-830">Therefore, Kestrel tries to reuse connections per the HTTP/1.1 protocol.</span></span> <span data-ttu-id="c7cf9-831">Ein Anforderungstext muss vollständig genutzt worden sein, damit die Verbindung wiederverwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-831">A request body must be fully consumed to allow the connection to be reused.</span></span> <span data-ttu-id="c7cf9-832">Die App nutzt nicht immer den Anforderungstext, z. B. bei einer `POST`-Anforderung, bei der der Server eine Umleitung oder 404-Antwort zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-832">The app doesn't always consume the request body, such as a `POST` requests where the server returns a redirect or 404 response.</span></span> <span data-ttu-id="c7cf9-833">Im Fall der `POST`-Umleitung:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-833">In the `POST`-redirect case:</span></span>

* <span data-ttu-id="c7cf9-834">Der Client hat möglicherweise bereits einen Teil der `POST`-Daten gesendet.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-834">The client may already have sent part of the `POST` data.</span></span>
* <span data-ttu-id="c7cf9-835">Der Server schreibt die 301-Antwort.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-835">The server writes the 301 response.</span></span>
* <span data-ttu-id="c7cf9-836">Die Verbindung kann erst dann für eine neue Anforderung verwendet werden, wenn die `POST`-Daten im vorherigen Anforderungstext vollständig gelesen wurden.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-836">The connection can't be used for a new request until the `POST` data from the previous request body has been fully read.</span></span>
* <span data-ttu-id="c7cf9-837">Kestrel versucht, den Anforderungstext zu leeren.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-837">Kestrel tries to drain the request body.</span></span> <span data-ttu-id="c7cf9-838">Leeren des Anforderungstexts bedeutet, die Daten zu lesen und zu verwerfen, ohne sie zu verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-838">Draining the request body means reading and discarding the data without processing it.</span></span>

<span data-ttu-id="c7cf9-839">Beim Leerungsvorgang wird ein Kompromiss zwischen der Möglichkeit der Wiederverwendung der Verbindung und der Dauer gefunden, die zum Leeren der verbleibenden Daten benötigt wird:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-839">The draining process makes a tradoff between allowing the connection to be reused and the time it takes to drain any remaining data:</span></span>

* <span data-ttu-id="c7cf9-840">Für das Leeren gilt ein Zeitlimit von fünf Sekunden, das nicht konfigurierbar ist.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-840">Draining has a timeout of five seconds, which isn't configurable.</span></span>
* <span data-ttu-id="c7cf9-841">Wenn vor dem Zeitlimit nicht alle durch den Header `Content-Length` oder `Transfer-Encoding` angegebenen Daten gelesen wurden, wird die Verbindung geschlossen.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-841">If all of the data specified by the `Content-Length` or `Transfer-Encoding` header hasn't been read before the timeout, the connection is closed.</span></span>

<span data-ttu-id="c7cf9-842">Mitunter möchten Sie die Anforderung sofort beenden, entweder bevor oder nachdem die Antwort geschrieben wurde.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-842">Sometimes you may want to terminate the request immediately, before or after writing the response.</span></span> <span data-ttu-id="c7cf9-843">Beispielsweise können für Clients restriktive Datenobergrenzen gelten, sodass das Begrenzen hochgeladener Daten Priorität haben kann.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-843">For example, clients may have restrictive data caps, so limiting uploaded data might be a priority.</span></span> <span data-ttu-id="c7cf9-844">Um in solchen Fällen eine Anforderung zu beenden, rufen Sie [HttpContext.abort](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A) in einem Controller, eine Razor Page oder Middleware auf.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-844">In such cases to terminate a request, call [HttpContext.Abort](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A) from a controller, Razor Page, or middleware.</span></span>

<span data-ttu-id="c7cf9-845">Gegen den Aufruf von `Abort` gibt es Vorbehalte:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-845">There are caveats to calling `Abort`:</span></span>

* <span data-ttu-id="c7cf9-846">Das Erstellen neuer Verbindungen kann langsam und aufwendig sein.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-846">Creating new connections can be slow and expensive.</span></span>
* <span data-ttu-id="c7cf9-847">Es gibt keine Garantie, dass der Client die Antwort gelesen hat, bevor die Verbindung geschlossen wird.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-847">There's no guarantee that the client has read the response before the connection closes.</span></span>
* <span data-ttu-id="c7cf9-848">Das Aufrufen von `Abort` sollte selten erfolgen und schweren Fehlerfällen und nicht gewöhnlichen Fehlern vorbehalten sein.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-848">Calling `Abort` should be rare and reserved for severe error cases, not common errors.</span></span>
  * <span data-ttu-id="c7cf9-849">Rufen Sie `Abort` nur dann auf, wenn ein konkretes Problem gelöst werden muss.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-849">Only call `Abort` when a specific problem needs to be solved.</span></span> <span data-ttu-id="c7cf9-850">Rufen Sie beispielsweise `Abort` auf, wenn böswillige Clients versuchen, Daten per `POST` abzurufen, oder wenn es einen Fehler im Clientcode gibt, der umfangreiche oder zahlreiche Anforderungen verursacht.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-850">For example, call `Abort` if malicious clients are trying to `POST` data or when there's a bug in client code that causes large or numerous requests.</span></span>
  * <span data-ttu-id="c7cf9-851">Rufen Sie `Abort` nicht für gewöhnliche Fehlersituationen auf, wie z. B. HTTP 404 (Nicht gefunden).</span><span class="sxs-lookup"><span data-stu-id="c7cf9-851">Don't call `Abort` for common error situations, such as HTTP 404 (Not Found).</span></span>

<span data-ttu-id="c7cf9-852">Durch den Aufruf von [HttpResponse.CompleteAsync](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A) vor dem Aufruf von `Abort` wird sichergestellt, dass der Server das Schreiben der Antwort abgeschlossen hat.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-852">Calling [HttpResponse.CompleteAsync](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A) before calling `Abort` ensures that the server has completed writing the response.</span></span> <span data-ttu-id="c7cf9-853">Das Clientverhalten ist jedoch nicht vorhersagbar, und es kann sein, dass die Antwort nicht gelesen wird, bevor die Verbindung abgebrochen wird.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-853">However, client behavior isn't predictable and they may not read the response before the connection is aborted.</span></span>

<span data-ttu-id="c7cf9-854">Dieser Prozess bei HTTP/2 ist anders, da das Protokoll den Abbruch einzelner Anforderungsströme ohne Schließen der Verbindung unterstützt.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-854">This process is different for HTTP/2 because the protocol supports aborting individual request streams without closing the connection.</span></span> <span data-ttu-id="c7cf9-855">Das fünfsekündige Zeitlimit für das Leeren gilt nicht.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-855">The five second drain timeout doesn't apply.</span></span> <span data-ttu-id="c7cf9-856">Wenn nach dem Fertigstellen einer Antwort ungelesene Anforderungstextdaten vorhanden sind, sendet der Server einen HTTP/2-RST-Datenrahmen.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-856">If there's any unread request body data after completing a response, then the server sends an HTTP/2 RST frame.</span></span> <span data-ttu-id="c7cf9-857">Zusätzliche Datenrahmen im Anforderungstext werden ignoriert.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-857">Additional request body data frames are ignored.</span></span>

<span data-ttu-id="c7cf9-858">Wenn möglich, ist es für Clients besser, den Anforderungsheader [Expect: 100-continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) zu verwenden und auf die Antwort des Servers zu warten, bevor mit dem Senden des Anforderungstexts begonnen wird.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-858">If possible, it's better for clients to utilize the [Expect: 100-continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) request header and wait for the server to respond before starting to send the request body.</span></span> <span data-ttu-id="c7cf9-859">Das gibt dem Client die Gelegenheit, die Antwort zu prüfen und abzubrechen, bevor nicht benötigte Daten gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-859">That gives the client an opportunity to examine the response and abort before sending unneeded data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c7cf9-860">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="c7cf9-860">Additional resources</span></span>

* <span data-ttu-id="c7cf9-861">Bei der Verwendung von UNIX-Sockets unter Linux wird der Socket beim Herunterfahren der App nicht automatisch gelöscht.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-861">When using UNIX sockets on Linux, the socket is not automatically deleted on app shut down.</span></span> <span data-ttu-id="c7cf9-862">Weitere Informationen finden Sie in [diesem GitHub-Issue](https://github.com/dotnet/aspnetcore/issues/14134).</span><span class="sxs-lookup"><span data-stu-id="c7cf9-862">For more information, see [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/14134).</span></span>
* <xref:test/troubleshoot>
* <xref:security/enforcing-ssl>
* <xref:host-and-deploy/proxy-load-balancer>
* [<span data-ttu-id="c7cf9-863">RFC 7230: Message Syntax and Routing (Abschnitt 5.4: Host)</span><span class="sxs-lookup"><span data-stu-id="c7cf9-863">RFC 7230: Message Syntax and Routing (Section 5.4: Host)</span></span>](https://tools.ietf.org/html/rfc7230#section-5.4)

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="c7cf9-864">Einführung in Kestrel, dem plattformübergreifenden [Webserver für ASP.NET Core](xref:fundamentals/servers/index).</span><span class="sxs-lookup"><span data-stu-id="c7cf9-864">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="c7cf9-865">Kestrel ist der Webserver, der standardmäßig in ASP.NET Core-Projektvorlagen enthalten ist.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-865">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="c7cf9-866">Kestrel unterstützt die folgenden Szenarios:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-866">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="c7cf9-867">HTTPS</span><span class="sxs-lookup"><span data-stu-id="c7cf9-867">HTTPS</span></span>
* <span data-ttu-id="c7cf9-868">Nicht transparente Upgrades, die zum Aktivieren von [WebSockets](https://github.com/aspnet/websockets) verwendet werden</span><span class="sxs-lookup"><span data-stu-id="c7cf9-868">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="c7cf9-869">Unix-Sockets für eine hohe Leistung im Hintergrund von Nginx</span><span class="sxs-lookup"><span data-stu-id="c7cf9-869">Unix sockets for high performance behind Nginx</span></span>

<span data-ttu-id="c7cf9-870">Kestrel wird auf allen Plattformen und für alle Versionen unterstützt, die .NET Core unterstützt.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-870">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="c7cf9-871">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples/2.x) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="c7cf9-871">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples/2.x) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="c7cf9-872">Verwenden von Kestrel mit einem Reverseproxy</span><span class="sxs-lookup"><span data-stu-id="c7cf9-872">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="c7cf9-873">Kestrel kann eigenständig oder mit einem *Reverseproxyserver* wie z.B. [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org) oder [Apache](https://httpd.apache.org/) verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-873">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="c7cf9-874">Ein Reverseproxyserver empfängt HTTP-Anforderungen aus dem Netzwerk und leitet diese an Kestrel weiter.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-874">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="c7cf9-875">Kestrel bei Verwendung als Webserver mit direkter Internetverbindung:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-875">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Kestrel kommuniziert direkt und ohne Reverseproxyserver mit dem Internet](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="c7cf9-877">Kestrel bei Verwendung in einer Reverseproxykonfiguration:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-877">Kestrel used in a reverse proxy configuration:</span></span>

![Kestrel kommuniziert indirekt mit dem Internet über einen Reverseproxyserver wie IIS, Nginx oder Apache](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="c7cf9-879">Jede der beiden Konfigurationen – mit oder ohne einen Reverseproxyserver – stellt eine unterstützte Hostingkonfiguration dar.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-879">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="c7cf9-880">Bei Verwendung als Edgeserver ohne Reverseproxyserver unterstützt Kestrel die gemeinsame Nutzung der gleichen IP-Adresse und des gleichen Ports durch mehrere Prozesse nicht.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-880">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="c7cf9-881">Wenn Kestrel für das Lauschen an einem Port konfiguriert ist, verarbeitet Kestrel den gesamten Datenverkehr für diesen Port unabhängig von den `Host`-Headern der Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-881">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="c7cf9-882">Ein Reverseproxy, der Ports freigeben kann, kann Anforderungen an Kestrel über eine eindeutige IP und einen eindeutigen Port weiterleiten.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-882">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="c7cf9-883">Auch wenn kein Reverseproxyserver erforderlich ist, kann die Verwendung eines solchen empfehlenswert sein.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-883">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="c7cf9-884">Für einen Reverseproxy gilt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-884">A reverse proxy:</span></span>

* <span data-ttu-id="c7cf9-885">Er kann die verfügbar gemachten öffentlichen Oberflächen der von ihm gehosteten Apps einschränken.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-885">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="c7cf9-886">Er stellt eine zusätzliche Ebene für Konfiguration und Schutz bereit.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-886">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="c7cf9-887">Er lässt sich besser in die vorhandene Infrastruktur integrieren.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-887">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="c7cf9-888">Er vereinfacht die Konfiguration von Lastenausgleich und sicherer Kommunikation (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="c7cf9-888">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="c7cf9-889">Nur der Reverseproxyserver erfordert ein X.509-Zertifikat, und dieser Server kann mit den Servern der App im internen Netzwerk über einfaches HTTP kommunizieren.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-889">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="c7cf9-890">Das Hosting in einer Reverseproxykonfiguration erfordert [Hostfilterung](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="c7cf9-890">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a><span data-ttu-id="c7cf9-891">Verwenden von Kestrel in ASP.NET Core-Apps</span><span class="sxs-lookup"><span data-stu-id="c7cf9-891">How to use Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="c7cf9-892">Das Paket [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) ist im Metapaket [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) enthalten.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-892">The [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="c7cf9-893">ASP.NET Core-Projektvorlagen verwenden Kestrel standardmäßig.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-893">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="c7cf9-894">Der Vorlagencode in *Program.cs* ruft <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> auf, wodurch <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> im Hintergrund aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-894">In *Program.cs*, the template code calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> behind the scenes.</span></span>

<span data-ttu-id="c7cf9-895">Um zusätzliche Konfiguration nach dem Aufruf von `CreateDefaultBuilder` bereitzustellen, rufen Sie <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> auf:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-895">To provide additional configuration after calling `CreateDefaultBuilder`, call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            // Set properties and call methods on serverOptions
        });
```

<span data-ttu-id="c7cf9-896">Weitere Informationen zum `CreateDefaultBuilder` und zum Erstellen des Hosts finden Sie im Abschnitt *Einrichten eines Hosts* von <xref:fundamentals/host/web-host#set-up-a-host>.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-896">For more information on `CreateDefaultBuilder` and building the host, see the *Set up a host* section of <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

## <a name="kestrel-options"></a><span data-ttu-id="c7cf9-897">Kestrel-Optionen</span><span class="sxs-lookup"><span data-stu-id="c7cf9-897">Kestrel options</span></span>

<span data-ttu-id="c7cf9-898">Der Kestrel-Webserver verfügt über einschränkende Konfigurationsoptionen, die besonders nützlich bei Bereitstellungen mit Internetzugriff sind.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-898">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="c7cf9-899">Legen Sie Einschränkungen für die <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits>-Eigenschaft der <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>-Klasse fest.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-899">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="c7cf9-900">Die `Limits`-Eigenschaft enthält eine Instanz der <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>-Klasse.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-900">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="c7cf9-901">In den folgenden Beispielen wird der <xref:Microsoft.AspNetCore.Server.Kestrel.Core>-Namespace verwendet:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-901">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="c7cf9-902">Kestrel-Optionen, die in den folgenden Beispielen in C#-Code konfiguriert sind, können auch mit einem [Konfigurationsanbieter](xref:fundamentals/configuration/index) festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-902">Kestrel options, which are configured in C# code in the following examples, can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="c7cf9-903">Beispielsweise kann der Dateikonfigurationsanbieter die Kestrel-Konfiguration aus einer *appsettings.json* - oder *appsettings.{Umgebung}.json*-Datei laden:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-903">For example, the File Configuration Provider can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

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

<span data-ttu-id="c7cf9-904">Verwenden Sie **einen** der folgenden Ansätze:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-904">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="c7cf9-905">Konfigurieren Sie Kestrel in `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-905">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="c7cf9-906">Fügen Sie eine Instanz von `IConfiguration` in die `Startup`-Klasse ein.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-906">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="c7cf9-907">Im folgenden Beispiel wird davon ausgegangen, dass die eingefügte Konfiguration der `Configuration`-Eigenschaft zugewiesen wird.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-907">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="c7cf9-908">Laden Sie in `Startup.ConfigureServices` den Abschnitt `Kestrel` der Konfiguration in die Konfiguration von Kestrel:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-908">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

* <span data-ttu-id="c7cf9-909">Konfigurieren Sie Kestrel beim Erstellen des Hosts:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-909">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="c7cf9-910">Laden Sie in *Program.cs* den Abschnitt `Kestrel` der Konfiguration in die Konfiguration von Kestrel:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-910">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

<span data-ttu-id="c7cf9-911">Beide vorangehenden Ansätze funktionieren mit jedem [Konfigurationsanbieter](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="c7cf9-911">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="c7cf9-912">Keep-Alive-Timeout</span><span class="sxs-lookup"><span data-stu-id="c7cf9-912">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="c7cf9-913">Ruft das [Keep-Alive-Timeout](https://tools.ietf.org/html/rfc7230#section-6.5) ab oder legt es fest.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-913">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="c7cf9-914">Standardwert: 2 Minuten.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-914">Defaults to 2 minutes.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.KeepAliveTimeout = TimeSpan.FromMinutes(2);
        });
```

### <a name="maximum-client-connections"></a><span data-ttu-id="c7cf9-915">Maximale Anzahl der Clientverbindungen</span><span class="sxs-lookup"><span data-stu-id="c7cf9-915">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="c7cf9-916">Die maximale Anzahl von gleichzeitig geöffneten TCP-Verbindungen kann mithilfe von folgendem Code für die gesamte App festgelegt werden:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-916">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxConcurrentConnections = 100;
        });
```

<span data-ttu-id="c7cf9-917">Es gibt einen separaten Grenzwert für Verbindungen, die von HTTP oder HTTPS auf ein anderes Protokoll aktualisiert wurden (z.B. auf eine WebSockets-Anforderung).</span><span class="sxs-lookup"><span data-stu-id="c7cf9-917">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="c7cf9-918">Nachdem eine Verbindung aktualisiert wurde, zählt diese nicht mehr für den `MaxConcurrentConnections`-Grenzwert.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-918">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxConcurrentUpgradedConnections = 100;
        });
```

<span data-ttu-id="c7cf9-919">Die maximale Anzahl von Verbindungen ist standardmäßig nicht begrenzt (NULL).</span><span class="sxs-lookup"><span data-stu-id="c7cf9-919">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="c7cf9-920">Maximale Größe des Anforderungstexts</span><span class="sxs-lookup"><span data-stu-id="c7cf9-920">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="c7cf9-921">Die maximale Größe des Anforderungstexts beträgt standardmäßig 30.000.000 Byte, also ungefähr 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-921">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="c7cf9-922">Die empfohlene Methode zur Außerkraftsetzung des Grenzwerts in einer ASP.NET Core-MVC-App besteht im Verwenden des <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute>-Attributs in einer Aktionsmethode:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-922">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="c7cf9-923">Im folgenden Beispiel wird veranschaulicht, wie die Einschränkungen auf jeder Anforderung für die App konfiguriert werden:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-923">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxRequestBodySize = 10 * 1024;
        });
```

<span data-ttu-id="c7cf9-924">Außer Kraft setzen der Einstellung für eine bestimmte Anforderung in Middleware:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-924">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="c7cf9-925">Eine Ausnahme wird ausgelöst, wenn die App den Grenzwert einer Anforderung konfiguriert, nachdem die App bereits mit dem Lesen der Anforderung begonnen hat.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-925">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="c7cf9-926">Es gibt eine `IsReadOnly`-Eigenschaft, die angibt, wenn sich die `MaxRequestBodySize`-Eigenschaft im schreibgeschützten Zustand befindet, also wenn der Grenzwert nicht mehr konfiguriert werden kann.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-926">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="c7cf9-927">Wenn eine App [prozessextern](xref:host-and-deploy/iis/index#out-of-process-hosting-model) hinter dem [ASP.NET Core-Modul](xref:host-and-deploy/aspnet-core-module) ausgeführt wird, ist das Größenlimit von Kestrel für Anforderungstext deaktiviert, weil IIS dieses Limit bereits festlegt.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-927">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="c7cf9-928">Minimale Datenrate des Anforderungstexts</span><span class="sxs-lookup"><span data-stu-id="c7cf9-928">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="c7cf9-929">Kestrel überprüft sekündlich, ob Daten mit der angegebenen Rate in Bytes/Sekunde eingehen.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-929">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="c7cf9-930">Wenn die Rate den mindestens erforderlichen Wert unterschreitet, wird für die Verbindung wegen Timeout getrennt. Bei der Toleranzperiode handelt es sich um die Zeitspanne, die Kestrel dem Client gewährt, um die Senderate auf den mindestens erforderlichen Wert zu erhöhen. Die Rate wird währenddessen nicht überprüft.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-930">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="c7cf9-931">Diese Toleranzperiode beugt dem Trennen von Verbindungen vor, die Daten aufgrund eines langsamen TCP-Starts anfänglich mit einer niedrigen Rate senden.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-931">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="c7cf9-932">Die mindestens erforderliche Rate beträgt standardmäßig 240 Bytes/Sekunde mit einer Toleranzperiode von 5 Sekunden.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-932">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="c7cf9-933">Für die Antwort gilt ebenfalls eine mindestens erforderliche Rate.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-933">A minimum rate also applies to the response.</span></span> <span data-ttu-id="c7cf9-934">Der Code zum Festlegen des Grenzwerts für Anforderung und Antwort ist abgesehen von `RequestBody` oder `Response` in den Namen von Eigenschaften und Schnittstelle identisch.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-934">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="c7cf9-935">In diesem Beispiel wird veranschaulicht, wie Sie die mindestens erforderlichen Datenraten in *Program.cs* konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-935">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

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

### <a name="request-headers-timeout"></a><span data-ttu-id="c7cf9-936">Timeout für Anforderungsheader</span><span class="sxs-lookup"><span data-stu-id="c7cf9-936">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="c7cf9-937">Ruft die maximale Zeitspanne ab, während der der Server Anforderungsheader empfängt, oder legt diese fest.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-937">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="c7cf9-938">Der Standardwert beträgt 30 Sekunden.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-938">Defaults to 30 seconds.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.RequestHeadersTimeout = TimeSpan.FromMinutes(1);
        });
```

### <a name="synchronous-io"></a><span data-ttu-id="c7cf9-939">Synchrone E/A-Vorgänge</span><span class="sxs-lookup"><span data-stu-id="c7cf9-939">Synchronous I/O</span></span>

<span data-ttu-id="c7cf9-940"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> steuert, ob für Anforderung und Antwort synchrone E/A-Vorgänge zulässig sind.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-940"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous I/O is allowed for the request and response.</span></span> <span data-ttu-id="c7cf9-941">Der Standardwert ist `true`.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-941">The  default value is `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="c7cf9-942">Sehr viele blockierende synchrone E/A-Vorgänge können zu einem Ressourcenmangel im Threadpool führen, wodurch die App nicht mehr reagiert.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-942">A large number of blocking synchronous I/O operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="c7cf9-943">Aktivieren Sie `AllowSynchronousIO` nur bei Verwendung einer Bibliothek, die asynchrone E/A-Vorgänge nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-943">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous I/O.</span></span>

<span data-ttu-id="c7cf9-944">Das folgende Beispiel deaktiviert synchrone E/A-Vorgänge:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-944">The following example disables synchronous I/O:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.AllowSynchronousIO = false;
        });
```

<span data-ttu-id="c7cf9-945">Weitere Informationen zu anderen Kestrel-Optionen und -Einschränkungen finden Sie unter:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-945">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="c7cf9-946">Endpunktkonfiguration</span><span class="sxs-lookup"><span data-stu-id="c7cf9-946">Endpoint configuration</span></span>

<span data-ttu-id="c7cf9-947">Standardmäßig wird ASP.NET Core an Folgendes gebunden:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-947">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="c7cf9-948">`https://localhost:5001` (wenn ein lokales Entwicklungszertifikat vorhanden ist)</span><span class="sxs-lookup"><span data-stu-id="c7cf9-948">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="c7cf9-949">Verwenden Sie Folgendes zum Angeben der URLs:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-949">Specify URLs using the:</span></span>

* <span data-ttu-id="c7cf9-950">Die Umgebungsvariable `ASPNETCORE_URLS`</span><span class="sxs-lookup"><span data-stu-id="c7cf9-950">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="c7cf9-951">Das Befehlszeilenargument `--urls`</span><span class="sxs-lookup"><span data-stu-id="c7cf9-951">`--urls` command-line argument.</span></span>
* <span data-ttu-id="c7cf9-952">Den Hostkonfigurationsschlüssel `urls`</span><span class="sxs-lookup"><span data-stu-id="c7cf9-952">`urls` host configuration key.</span></span>
* <span data-ttu-id="c7cf9-953">Die Erweiterungsmethode `UseUrls`</span><span class="sxs-lookup"><span data-stu-id="c7cf9-953">`UseUrls` extension method.</span></span>

<span data-ttu-id="c7cf9-954">Der Wert, der mit diesen Ansätzen angegeben wird, kann mindestens ein HTTP- oder HTTPS-Endpunkt sein (HTTPS wenn ein Standardzertifikat verfügbar ist).</span><span class="sxs-lookup"><span data-stu-id="c7cf9-954">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="c7cf9-955">Konfigurieren Sie den Wert als eine durch Semikolons getrennte Liste (z.B. `"Urls": "http://localhost:8000;http://localhost:8001"`).</span><span class="sxs-lookup"><span data-stu-id="c7cf9-955">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="c7cf9-956">Weitere Informationen zu diesen Ansätzen finden Sie unter [Server-URLs](xref:fundamentals/host/web-host#server-urls) und [Außerkraftsetzen der Konfiguration](xref:fundamentals/host/web-host#override-configuration).</span><span class="sxs-lookup"><span data-stu-id="c7cf9-956">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="c7cf9-957">Ein Entwicklungszertifikat wird erstellt:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-957">A development certificate is created:</span></span>

* <span data-ttu-id="c7cf9-958">Wenn das [.NET Core SDK](/dotnet/core/sdk) installiert wird.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-958">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="c7cf9-959">Das [dev-cert-Tool](xref:aspnetcore-2.1#https) wird zum Erstellen eines Zertifikats verwendet.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-959">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="c7cf9-960">Einige Browser erfordern, dass Sie die explizite Berechtigung erteilen, dem lokalen Entwicklungszertifikat zu vertrauen.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-960">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="c7cf9-961">Projektvorlagen konfigurieren Apps, damit sie standardmäßig auf HTTPS ausgeführt werden und die [HTTPS-Umleitung und HSTS-Unterstützung](xref:security/enforcing-ssl) enthalten.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-961">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="c7cf9-962">Rufen Sie die <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*>- oder <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*>-Methode unter <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> auf, um URL-Präfixe und Ports für Kestrel zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-962">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="c7cf9-963">`UseUrls`, das Befehlszeilenargument `--urls`, der Hostkonfigurationsschlüssel `urls` und die Umgebungsvariable `ASPNETCORE_URLS` funktionieren ebenfalls, verfügen jedoch über Einschränkungen, die im Verlauf dieses Abschnitts erläutert werden (Ein Standardzertifikat muss für die HTTPS-Endpunktkonfiguration verfügbar sein).</span><span class="sxs-lookup"><span data-stu-id="c7cf9-963">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="c7cf9-964">`KestrelServerOptions`-Konfiguration:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-964">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="c7cf9-965">ConfigureEndpointDefaults(Action\<ListenOptions>)</span><span class="sxs-lookup"><span data-stu-id="c7cf9-965">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="c7cf9-966">Gibt die Konfiguration von `Action` zum Ausführen von jedem angegebenen Endpunkt an.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-966">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="c7cf9-967">Mehrmalige Aufrufe von `ConfigureEndpointDefaults` ersetzen vorherige Instanzen von `Action` mit der zuletzt angegebenen `Action`.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-967">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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
> <span data-ttu-id="c7cf9-968">Auf Endpunkte, die durch Aufrufen von <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **vor** dem Aufrufen von <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> erstellt werden, werden die Standardwerte nicht angewendet.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-968">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> won't have the defaults applied.</span></span>

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="c7cf9-969">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span><span class="sxs-lookup"><span data-stu-id="c7cf9-969">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="c7cf9-970">Gibt die Konfiguration von `Action` zum Ausführen von jedem HTTPS-Endpunkt an.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-970">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="c7cf9-971">Mehrmalige Aufrufe von `ConfigureHttpsDefaults` ersetzen vorherige Instanzen von `Action` mit der zuletzt angegebenen `Action`.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-971">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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
> <span data-ttu-id="c7cf9-972">Auf Endpunkte, die durch Aufrufen von <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **vor** dem Aufrufen von <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> erstellt werden, werden die Standardwerte nicht angewendet.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-972">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>

### <a name="configureiconfiguration"></a><span data-ttu-id="c7cf9-973">Configure(IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="c7cf9-973">Configure(IConfiguration)</span></span>

<span data-ttu-id="c7cf9-974">Erstellt ein Konfigurationsladeprogramm für das Einrichten von Kestrel, was <xref:Microsoft.Extensions.Configuration.IConfiguration> als Eingabe erfordert.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-974">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="c7cf9-975">Die Konfiguration muss auf den Konfigurationsabschnitt für Kestrel festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-975">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="c7cf9-976">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="c7cf9-976">ListenOptions.UseHttps</span></span>

<span data-ttu-id="c7cf9-977">Konfiguriert Kestrel zur Verwendung von HTTPS.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-977">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="c7cf9-978">`ListenOptions.UseHttps`-Erweiterungen:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-978">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="c7cf9-979">`UseHttps`: Hiermit wird Kestrel zur Verwendung von HTTPS mit dem Standardzertifikat konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-979">`UseHttps`: Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="c7cf9-980">Löst eine Ausnahme aus, wenn kein Standardzertifikat konfiguriert ist.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-980">Throws an exception if no default certificate is configured.</span></span>
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

<span data-ttu-id="c7cf9-981">`ListenOptions.UseHttps`-Parameter:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-981">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="c7cf9-982">`filename` entspricht dem Pfad und Dateinamen einer Zertifikatdatei relativ zu dem Verzeichnis, das die Inhaltsdateien der App enthält.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-982">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="c7cf9-983">`password` ist das für den Zugriff auf die X.509-Zertifikatsdaten erforderliche Kennwort.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-983">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="c7cf9-984">`configureOptions` ist eine `Action` zum Konfigurieren von `HttpsConnectionAdapterOptions`.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-984">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="c7cf9-985">Gibt `ListenOptions` zurück.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-985">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="c7cf9-986">`storeName` ist der Zertifikatspeicher, aus dem das Zertifikat geladen wird.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-986">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="c7cf9-987">`subject` ist der Name des Antragstellers für das Zertifikat.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-987">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="c7cf9-988">`allowInvalid` gibt an, ob ungültige Zertifikate berücksichtigt werden sollten, z.B. selbstsignierte Zertifikate.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-988">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="c7cf9-989">`location` ist der Speicherort, aus dem das Zertifikat geladen wird.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-989">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="c7cf9-990">`serverCertificate` ist das X.509-Zertifikat.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-990">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="c7cf9-991">In der Produktion muss HTTPS explizit konfiguriert sein.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-991">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="c7cf9-992">Zumindest muss ein Standardzertifikat angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-992">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="c7cf9-993">Die im Folgenden beschriebenen unterstützten Konfigurationen:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-993">Supported configurations described next:</span></span>

* <span data-ttu-id="c7cf9-994">Keine Konfiguration</span><span class="sxs-lookup"><span data-stu-id="c7cf9-994">No configuration</span></span>
* <span data-ttu-id="c7cf9-995">Ersetzen des Standardzertifikats aus der Konfiguration</span><span class="sxs-lookup"><span data-stu-id="c7cf9-995">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="c7cf9-996">Ändern des Standards im Code</span><span class="sxs-lookup"><span data-stu-id="c7cf9-996">Change the defaults in code</span></span>

<span data-ttu-id="c7cf9-997">*Keine Konfiguration*</span><span class="sxs-lookup"><span data-stu-id="c7cf9-997">*No configuration*</span></span>

<span data-ttu-id="c7cf9-998">Kestrel überwacht `http://localhost:5000` und `https://localhost:5001` (wenn ein Standardzertifikat verfügbar ist).</span><span class="sxs-lookup"><span data-stu-id="c7cf9-998">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="c7cf9-999">*Ersetzen des Standardzertifikats aus der Konfiguration*</span><span class="sxs-lookup"><span data-stu-id="c7cf9-999">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="c7cf9-1000">`CreateDefaultBuilder` ruft `Configure(context.Configuration.GetSection("Kestrel"))` standardmäßig zum Laden der Kestrel-Konfiguration auf.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1000">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="c7cf9-1001">Ein Standardkonfigurationsschema für HTTPS-App-Einstellungen ist für Kestrel verfügbar.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1001">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="c7cf9-1002">Konfigurieren Sie mehrere Endpunkte, einschließlich der zu verwendenden URLs und Zertifikate aus einer Datei auf dem Datenträger oder einem Zertifikatspeicher.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1002">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="c7cf9-1003">Im folgenden Beispiel für *appsettings.json* gilt:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1003">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="c7cf9-1004">Legen Sie **AllowInvalid** auf `true` fest, um die Verwendung von ungültigen Zertifikaten zu erlauben (z.B. selbstsignierte Zertifikate).</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1004">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="c7cf9-1005">Jeder HTTPS-Endpunkt, der kein Zertifikat angibt (im folgenden Beispiel **HttpsDefaultCert**), greift auf das unter **Zertifikate** > **Standard** festgelegte Zertifikat oder das Entwicklungszertifikat zurück.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1005">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

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

<span data-ttu-id="c7cf9-1006">Alternativ zur Verwendung von **Pfad** und **Kennwort** für alle Zertifikatknoten können Sie das Zertifikat mithilfe von Zertifikatspeicherfeldern angeben.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1006">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="c7cf9-1007">Das Zertifikat unter **Zertifikate** > **Standard** kann beispielweise wie folgt angegeben werden:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1007">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="c7cf9-1008">Schema-Hinweise:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1008">Schema notes:</span></span>

* <span data-ttu-id="c7cf9-1009">Bei den Namen der Endpunkte wird die Groß-/Kleinschreibung nicht beachtet.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1009">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="c7cf9-1010">Zum Beispiel sind `HTTPS` und `Https` gültig.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1010">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="c7cf9-1011">Der Parameter `Url` ist für jeden Endpunkt erforderlich.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1011">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="c7cf9-1012">Das Format für diesen Parameter ist identisch mit dem allgemeinen Konfigurationsparameter `Urls`, mit der Ausnahme, dass er auf einen einzelnen Wert begrenzt ist.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1012">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="c7cf9-1013">Diese Endpunkte ersetzen die in der allgemeinen `Urls`-Konfiguration festgelegten Endpunkte, anstatt zu ihnen hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1013">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="c7cf9-1014">Endpunkte, die über `Listen` in Code definiert werden, werden den im Konfigurationsabschnitt definierten Endpunkten hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1014">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="c7cf9-1015">Der `Certificate`-Abschnitt ist optional.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1015">The `Certificate` section is optional.</span></span> <span data-ttu-id="c7cf9-1016">Wenn der `Certificate`-Abschnitt nicht angegeben ist, werden die in früheren Szenarios definierten Standardwerte verwendet.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1016">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="c7cf9-1017">Wenn keine Standardwerte verfügbar sind, löst der Server eine Ausnahme aus und startet nicht.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1017">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="c7cf9-1018">Der `Certificate`-Abschnitt unterstützt die Zertifikate **Pfad**&ndash;**Kennwort** und **Subject**&ndash;**Store** (Antragsteller–Speicher).</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1018">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="c7cf9-1019">Auf diese Weise kann eine beliebige Anzahl von Endpunkten definiert werden, solange Sie keine Portkonflikte verursachen.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1019">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="c7cf9-1020">`options.Configure(context.Configuration.GetSection("{SECTION}"))` gibt `KestrelConfigurationLoader` mit der Methode `.Endpoint(string name, listenOptions => { })` zurück, die dazu verwendet werden kann, die Einstellungen eines Endpunkts zu ergänzen:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1020">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

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

<span data-ttu-id="c7cf9-1021">Auf `KestrelServerOptions.ConfigurationLoader` kann direkt zugegriffen werden, um die Iteration auf dem vorhandenen Ladeprogramm fortzusetzen, etwa auf dem von <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> bereitgestellten Ladeprogramm.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1021">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="c7cf9-1022">Der Konfigurationsabschnitt ist für jeden Endpunkt in den Optionen der Methode `Endpoint` verfügbar, sodass benutzerdefinierte Einstellungen gelesen werden können.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1022">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="c7cf9-1023">Mehrere Konfigurationen können durch erneutes Aufrufen von `options.Configure(context.Configuration.GetSection("{SECTION}"))` mit einem anderen Abschnitt geladen werden.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1023">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="c7cf9-1024">Sofern `Load` nicht in einer vorherigen Instanz explizit aufgerufen wird, wird nur die letzte Konfiguration verwendet.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1024">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="c7cf9-1025">Das Metapaket ruft `Load` nicht auf, sodass der Abschnitt mit der Standardkonfiguration ersetzt werden kann.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1025">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="c7cf9-1026">`KestrelConfigurationLoader` spiegelt die API-Familie `Listen` von `KestrelServerOptions` als `Endpoint`-Überladungen, weshalb Code und Konfigurationsendpunkte am selben Ort konfiguriert werden können.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1026">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="c7cf9-1027">Diese Überladungen verwenden keine Namen und nutzen nur Standardeinstellungen aus der Konfiguration.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1027">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="c7cf9-1028">*Ändern des Standards im Code*</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1028">*Change the defaults in code*</span></span>

<span data-ttu-id="c7cf9-1029">`ConfigureEndpointDefaults` und `ConfigureHttpsDefaults` können zum Ändern der Standardeinstellungen für `ListenOptions` und `HttpsConnectionAdapterOptions` verwendet werden, einschließlich der Standardzertifikate, die im vorherigen Szenario festgelegt wurden.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1029">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="c7cf9-1030">`ConfigureEndpointDefaults` und `ConfigureHttpsDefaults` sollten aufgerufen werden, bevor Endpunkte konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1030">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

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

<span data-ttu-id="c7cf9-1031">*Kestrel-Unterstützung für SNI*</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1031">*Kestrel support for SNI*</span></span>

<span data-ttu-id="c7cf9-1032">Die [Servernamensanzeige (SNI)](https://tools.ietf.org/html/rfc6066#section-3) kann zum Hosten mehrerer Domänen auf der gleichen IP-Adresse und dem gleichen Port verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1032">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="c7cf9-1033">Damit die Servernamensanzeige funktioniert, sendet der Client während des TLS-Handshakes den Hostnamen für die sichere Sitzung an den Server, sodass der Server das richtige Zertifikat bereitstellen kann.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1033">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="c7cf9-1034">Der Client verwendet das beigestellte Zertifikat für die verschlüsselte Kommunikation mit dem Server während der sicheren Sitzung nach dem TLS-Handshake.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1034">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="c7cf9-1035">Kestrel unterstützt die Servernamensanzeige über den `ServerCertificateSelector`-Rückruf.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1035">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="c7cf9-1036">Der Rückruf wird für jede Verbindung einmal aufgerufen, um der App zu ermöglichen, den Hostnamen zu überprüfen und das entsprechende Zertifikat auszuwählen.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1036">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="c7cf9-1037">Für die Unterstützung der Servernamensanzeige benötigen Sie Folgendes:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1037">SNI support requires:</span></span>

* <span data-ttu-id="c7cf9-1038">Wird auf dem Zielframework `netcoreapp2.1` oder höher ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1038">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="c7cf9-1039">In `net461` oder höher, wird der Rückruf aufgerufen, `name` ist aber immer `null`.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1039">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="c7cf9-1040">`name` ist auch `null`, wenn der Client den Hostnamenparameter nicht im TLS-Handshake angibt.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1040">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="c7cf9-1041">Alle Websites werden in derselben Kestrel-Instanz ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1041">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="c7cf9-1042">Kestrel unterstützt ohne Reverseproxy keine gemeinsame IP-Adresse und keinen gemeinsamen Port für mehrere Instanzen.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1042">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

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

### <a name="connection-logging"></a><span data-ttu-id="c7cf9-1043">Verbindungsprotokollierung</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1043">Connection logging</span></span>

<span data-ttu-id="c7cf9-1044">Rufen Sie <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> auf, um Protokolle auf Debugebene für die Kommunikation auf Byteebene für eine Verbindung auszugeben.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1044">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="c7cf9-1045">Die Verbindungsprotokollierung ist beim Beheben von Problemen bei der Low-Level-Kommunikation hilfreich, wie z. B. bei der TLS-Verschlüsselung und bei Proxys.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1045">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="c7cf9-1046">Wenn `UseConnectionLogging` vor `UseHttps` platziert wird, wird der verschlüsselte Datenverkehr protokolliert.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1046">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="c7cf9-1047">Wenn `UseConnectionLogging` nach `UseHttps` platziert wird, wird der entschlüsselte Datenverkehr protokolliert.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1047">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="c7cf9-1048">Binden an einen TCP-Socket</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1048">Bind to a TCP socket</span></span>

<span data-ttu-id="c7cf9-1049">Die <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*>-Methode wird an ein TCP-Socket gebunden, und ein Lambdaausdruck einer Option lässt die Konfiguration des X.509-Zertifikats zu:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1049">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

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

<span data-ttu-id="c7cf9-1050">Im Beispiel wird HTTPS für einen Endpunkt mit <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions> konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1050">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="c7cf9-1051">Verwenden Sie die gleiche API zum Konfigurieren anderer Kestrel-Einstellungen für bestimmte Endpunkte.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1051">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="c7cf9-1052">Binden an einen Unix-Socket</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1052">Bind to a Unix socket</span></span>

<span data-ttu-id="c7cf9-1053">Wie in diesem Beispiel dargestellt, lauschen Sie an einem Unix-Socket mit <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*>, um eine verbesserte Leistung mit Nginx zu erzielen:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1053">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

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

* <span data-ttu-id="c7cf9-1054">Legen Sie in der Nginx-Konfigurationsdatei den Eintrag `server` > `location` > `proxy_pass` auf `http://unix:/tmp/{KESTREL SOCKET}:/;` fest.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1054">In the Nginx confiuguration file, set the `server` > `location` > `proxy_pass` entry to `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span></span> <span data-ttu-id="c7cf9-1055">`{KESTREL SOCKET}` ist der Name des für <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> bereitgestellten Socket (zum Beispiel `kestrel-test.sock` im vorherigen Beispiel).</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1055">`{KESTREL SOCKET}` is the name of the socket provided to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (for example, `kestrel-test.sock` in the preceding example).</span></span>
* <span data-ttu-id="c7cf9-1056">Stellen Sie sicher, dass der Socket von Nginx beschreibbar ist (z. B. `chmod go+w /tmp/kestrel-test.sock`).</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1056">Ensure that the socket is writeable by Nginx (for example, `chmod go+w /tmp/kestrel-test.sock`).</span></span> 

### <a name="port-0"></a><span data-ttu-id="c7cf9-1057">Port 0</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1057">Port 0</span></span>

<span data-ttu-id="c7cf9-1058">Wenn die Portnummer `0` angegeben wird, wird Kestrel dynamisch an einen verfügbaren Port gebunden.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1058">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="c7cf9-1059">Im folgenden Beispiel wird veranschaulicht, wie bestimmt werden kann, für welchen Port Kestrel zur Laufzeit eine Bindung erstellt hat:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1059">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="c7cf9-1060">Wenn die App ausgeführt wird, gibt das Ausgabefenster der Konsole den dynamischen Port an, über den die App erreicht werden kann:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1060">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="c7cf9-1061">Einschränkungen</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1061">Limitations</span></span>

<span data-ttu-id="c7cf9-1062">Konfigurieren Sie Endpunkte mithilfe der folgenden Ansätze:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1062">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="c7cf9-1063">Befehlszeilenargument `--urls`</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1063">`--urls` command-line argument</span></span>
* <span data-ttu-id="c7cf9-1064">Hostkonfigurationsschlüssel `urls`</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1064">`urls` host configuration key</span></span>
* <span data-ttu-id="c7cf9-1065">Umgebungsvariable `ASPNETCORE_URLS`</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1065">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="c7cf9-1066">Diese Methoden sind nützlich, wenn Ihr Code mit anderen Servern als Kestrel funktionieren soll.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1066">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="c7cf9-1067">Beachten Sie jedoch die folgenden Einschränkungen:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1067">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="c7cf9-1068">HTTPS kann nicht mit diesen Ansätzen verwendet werden, außer ein Standardzertifikat wird in der HTTPS-Endpunktkonfiguration angegeben (z.B. wenn Sie wie zuvor in diesem Artikel gezeigt die `KestrelServerOptions`-Konfiguration oder eine Konfigurationsdatei verwenden).</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1068">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="c7cf9-1069">Wenn die Ansätze `Listen` und `UseUrls` gleichzeitig verwendet werden, überschreiben die `Listen`-Endpunkte die `UseUrls`-Endpunkte.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1069">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="c7cf9-1070">IIS-Endpunktkonfiguration</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1070">IIS endpoint configuration</span></span>

<span data-ttu-id="c7cf9-1071">Bei der Verwendung von IIS werden die URL-Bindungen für IIS-Überschreibungsbindungen durch `Listen` oder `UseUrls` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1071">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="c7cf9-1072">Weitere Informationen finden Sie im Artikel [ASP.NET Core-Modul](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1072">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

## <a name="libuv-transport-configuration"></a><span data-ttu-id="c7cf9-1073">Libuv-Transportkonfiguration</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1073">Libuv transport configuration</span></span>

<span data-ttu-id="c7cf9-1074">Mit dem Release von ASP.NET Core 2.1 basiert der Standardtransport von Kestrel nicht mehr auf Libuv, sondern auf verwalteten Sockets.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1074">With the release of ASP.NET Core 2.1, Kestrel's default transport is no longer based on Libuv but instead based on managed sockets.</span></span> <span data-ttu-id="c7cf9-1075">Dies stellt einen Breaking Change für ASP.NET Core 2.0-Apps dar, die auf Version 2.1 upgraden, <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> aufrufen und von einem der folgenden Pakete abhängig sind:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1075">This is a breaking change for ASP.NET Core 2.0 apps upgrading to 2.1 that call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> and depend on either of the following packages:</span></span>

* <span data-ttu-id="c7cf9-1076">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (direkter Paketverweis)</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1076">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (direct package reference)</span></span>
* [<span data-ttu-id="c7cf9-1077">Microsoft.AspNetCore.App</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1077">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)

<span data-ttu-id="c7cf9-1078">Für Projekte, die den Einsatz von Libuv erfordern:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1078">For projects that require the use of Libuv:</span></span>

* <span data-ttu-id="c7cf9-1079">Fügen Sie für das [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/)-Paket eine Abhängigkeit auf die Projektdatei der App hinzu:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1079">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* <span data-ttu-id="c7cf9-1080">Rufen Sie <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> auf:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1080">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span></span>

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

### <a name="url-prefixes"></a><span data-ttu-id="c7cf9-1081">URL-Präfixe</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1081">URL prefixes</span></span>

<span data-ttu-id="c7cf9-1082">Bei der Verwendung von `UseUrls`, dem Befehlszeilenargument `--urls`, dem Hostkonfigurationsschlüssel `urls` oder der Umgebungsvariable `ASPNETCORE_URLS` können die URL-Präfixe in den folgenden Formaten vorliegen.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1082">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="c7cf9-1083">Nur HTTP-URL-Präfixe sind gültig.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1083">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="c7cf9-1084">Kestrel unterstützt HTTPS nicht beim Konfigurieren von URL-Bindungen mit `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1084">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="c7cf9-1085">IPv4-Adresse mit Portnummer</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1085">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="c7cf9-1086">Bei `0.0.0.0` handelt es sich um einen Sonderfall, für den eine Bindung an alle IPv4-Adressen erfolgt.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1086">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="c7cf9-1087">IPv6-Adresse mit Portnummer</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1087">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="c7cf9-1088">`[::]` stellt das Äquivalent von IPv6 zu `0.0.0.0` für IPv4 dar.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1088">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="c7cf9-1089">Hostname mit Portnummer</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1089">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="c7cf9-1090">Hostnamen, `*` und `+` sind nicht spezifisch.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1090">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="c7cf9-1091">Alle Elemente, die nicht als gültige IP-Adresse oder `localhost` erkannt werden, werden an alle IPv4- und IPv6-IP-Adressen gebunden.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1091">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="c7cf9-1092">Verwenden Sie [HTTP.sys](xref:fundamentals/servers/httpsys) oder einen Reverseproxyserver zum Binden verschiedener Hostnamen an verschiedene ASP.NET Core-Apps auf demselben Port, z.B. IIS, Nginx oder Apache.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1092">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="c7cf9-1093">Das Hosting in einer Reverseproxykonfiguration erfordert [Hostfilterung](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1093">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="c7cf9-1094">`localhost`-Hostname mit Portnummer oder Loopback-IP mit Portnummer</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1094">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="c7cf9-1095">Wenn `localhost` angegeben ist, versucht Kestrel, eine Bindung zu IPv4- und IPv6-Loopback-Schnittstellen zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1095">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="c7cf9-1096">Wenn der erforderliche Port von einem anderen Dienst auf einer der Loopback-Schnittstellen verwendet wird, tritt beim Starten von Kestrel ein Fehler auf.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1096">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="c7cf9-1097">Wenn eine der Loopback-Schnittstellen aus anderen Gründen nicht verfügbar ist (meistens durch die fehlende Unterstützung von IPv6), protokolliert Kestrel eine Warnung.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1097">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="c7cf9-1098">Host-Filterung</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1098">Host filtering</span></span>

<span data-ttu-id="c7cf9-1099">Obwohl Kestrel die Konfiguration basierend auf Präfixe wie `http://example.com:5000` unterstützt, ignoriert Kestrel den Hostnamen weitgehend.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1099">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="c7cf9-1100">Der Host `localhost` ist ein Sonderfall, der für die Bindung an Loopback-Adressen verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1100">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="c7cf9-1101">Jeder Host, der keine explizite IP-Adresse ist, wird an alle öffentlichen IP-Adressen gebunden.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1101">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="c7cf9-1102">`Host`-Header werden nicht überprüft.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1102">`Host` headers aren't validated.</span></span>

<span data-ttu-id="c7cf9-1103">Verwenden Sie Middleware zum Filtern von Hosts, um dieses Problem zu umgehen.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1103">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="c7cf9-1104">Middleware zum Filtern von Hosts wird im Paket [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) bereitgestellt, das im [Metapaket Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) enthalten ist (ASP.NET Core 2.1 oder 2.2).</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1104">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or 2.2).</span></span> <span data-ttu-id="c7cf9-1105">Die Middleware wird von <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> hinzugefügt, wodurch <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*> aufgerufen wird:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1105">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="c7cf9-1106">Die Middleware zum Filtern von Hosts ist standardmäßig deaktiviert.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1106">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="c7cf9-1107">Wenn Sie die Middleware aktivieren möchten, definieren Sie einen `AllowedHosts`-Schlüssel in *appsettings.json* /*appsettings.\<EnvironmentName>json*.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1107">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="c7cf9-1108">Der Wert ist eine durch Semikolons getrennte Liste von Hostnamen ohne Portnummern:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1108">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="c7cf9-1109">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1109">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="c7cf9-1110">[Middleware für weitergeleitete Header](xref:host-and-deploy/proxy-load-balancer) hat auch eine <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts>-Option.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1110">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="c7cf9-1111">Middleware für weitergeleitete Header und Middleware zum Filtern von Hosts besitzen ähnliche Funktionen für unterschiedliche Szenarios.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1111">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="c7cf9-1112">Legen Sie `AllowedHosts` mit Middleware für weitergeleitete Header fest, wenn der `Host`-Header beim Weiterleiten von Anforderungen mit einem Reverseproxyserver oder einem Lastenausgleichsmodul nicht beibehalten wird.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1112">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="c7cf9-1113">Legen Sie `AllowedHosts` mit Middleware zum Filtern von Hosts fest, wenn Kestrel als öffentlicher Edgeserver verwendet oder der `Host`-Header direkt weitergeleitet wird.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1113">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="c7cf9-1114">Weitere Informationen zu Middleware für weitergeleitete Header finden Sie unter <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1114">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="http11-request-draining"></a><span data-ttu-id="c7cf9-1115">Leeren von HTTP/1.1-Anforderungen</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1115">HTTP/1.1 request draining</span></span>

<span data-ttu-id="c7cf9-1116">Das Öffnen von HTTP-Verbindungen ist zeitaufwendig.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1116">Opening HTTP connections is time consuming.</span></span> <span data-ttu-id="c7cf9-1117">Bei HTTPS ist es zudem ressourcenintensiv.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1117">For HTTPS, it's also resource intensive.</span></span> <span data-ttu-id="c7cf9-1118">Daher versucht Kestrel, Verbindungen gemäß dem HTTP/1.1-Protokoll wiederzuverwenden.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1118">Therefore, Kestrel tries to reuse connections per the HTTP/1.1 protocol.</span></span> <span data-ttu-id="c7cf9-1119">Ein Anforderungstext muss vollständig genutzt worden sein, damit die Verbindung wiederverwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1119">A request body must be fully consumed to allow the connection to be reused.</span></span> <span data-ttu-id="c7cf9-1120">Die App nutzt nicht immer den Anforderungstext, z. B. bei einer `POST`-Anforderung, bei der der Server eine Umleitung oder 404-Antwort zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1120">The app doesn't always consume the request body, such as a `POST` requests where the server returns a redirect or 404 response.</span></span> <span data-ttu-id="c7cf9-1121">Im Fall der `POST`-Umleitung:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1121">In the `POST`-redirect case:</span></span>

* <span data-ttu-id="c7cf9-1122">Der Client hat möglicherweise bereits einen Teil der `POST`-Daten gesendet.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1122">The client may already have sent part of the `POST` data.</span></span>
* <span data-ttu-id="c7cf9-1123">Der Server schreibt die 301-Antwort.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1123">The server writes the 301 response.</span></span>
* <span data-ttu-id="c7cf9-1124">Die Verbindung kann erst dann für eine neue Anforderung verwendet werden, wenn die `POST`-Daten im vorherigen Anforderungstext vollständig gelesen wurden.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1124">The connection can't be used for a new request until the `POST` data from the previous request body has been fully read.</span></span>
* <span data-ttu-id="c7cf9-1125">Kestrel versucht, den Anforderungstext zu leeren.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1125">Kestrel tries to drain the request body.</span></span> <span data-ttu-id="c7cf9-1126">Leeren des Anforderungstexts bedeutet, die Daten zu lesen und zu verwerfen, ohne sie zu verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1126">Draining the request body means reading and discarding the data without processing it.</span></span>

<span data-ttu-id="c7cf9-1127">Beim Leerungsvorgang wird ein Kompromiss zwischen der Möglichkeit der Wiederverwendung der Verbindung und der Dauer gefunden, die zum Leeren der verbleibenden Daten benötigt wird:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1127">The draining process makes a tradoff between allowing the connection to be reused and the time it takes to drain any remaining data:</span></span>

* <span data-ttu-id="c7cf9-1128">Für das Leeren gilt ein Zeitlimit von fünf Sekunden, das nicht konfigurierbar ist.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1128">Draining has a timeout of five seconds, which isn't configurable.</span></span>
* <span data-ttu-id="c7cf9-1129">Wenn vor dem Zeitlimit nicht alle durch den Header `Content-Length` oder `Transfer-Encoding` angegebenen Daten gelesen wurden, wird die Verbindung geschlossen.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1129">If all of the data specified by the `Content-Length` or `Transfer-Encoding` header hasn't been read before the timeout, the connection is closed.</span></span>

<span data-ttu-id="c7cf9-1130">Mitunter möchten Sie die Anforderung sofort beenden, entweder bevor oder nachdem die Antwort geschrieben wurde.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1130">Sometimes you may want to terminate the request immediately, before or after writing the response.</span></span> <span data-ttu-id="c7cf9-1131">Beispielsweise können für Clients restriktive Datenobergrenzen gelten, sodass das Begrenzen hochgeladener Daten Priorität haben kann.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1131">For example, clients may have restrictive data caps, so limiting uploaded data might be a priority.</span></span> <span data-ttu-id="c7cf9-1132">Um in solchen Fällen eine Anforderung zu beenden, rufen Sie [HttpContext.abort](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A) in einem Controller, eine Razor Page oder Middleware auf.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1132">In such cases to terminate a request, call [HttpContext.Abort](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A) from a controller, Razor Page, or middleware.</span></span>

<span data-ttu-id="c7cf9-1133">Gegen den Aufruf von `Abort` gibt es Vorbehalte:</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1133">There are caveats to calling `Abort`:</span></span>

* <span data-ttu-id="c7cf9-1134">Das Erstellen neuer Verbindungen kann langsam und aufwendig sein.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1134">Creating new connections can be slow and expensive.</span></span>
* <span data-ttu-id="c7cf9-1135">Es gibt keine Garantie, dass der Client die Antwort gelesen hat, bevor die Verbindung geschlossen wird.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1135">There's no guarantee that the client has read the response before the connection closes.</span></span>
* <span data-ttu-id="c7cf9-1136">Das Aufrufen von `Abort` sollte selten erfolgen und schweren Fehlerfällen und nicht gewöhnlichen Fehlern vorbehalten sein.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1136">Calling `Abort` should be rare and reserved for severe error cases, not common errors.</span></span>
  * <span data-ttu-id="c7cf9-1137">Rufen Sie `Abort` nur dann auf, wenn ein konkretes Problem gelöst werden muss.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1137">Only call `Abort` when a specific problem needs to be solved.</span></span> <span data-ttu-id="c7cf9-1138">Rufen Sie beispielsweise `Abort` auf, wenn böswillige Clients versuchen, Daten per `POST` abzurufen, oder wenn es einen Fehler im Clientcode gibt, der umfangreiche oder zahlreiche Anforderungen verursacht.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1138">For example, call `Abort` if malicious clients are trying to `POST` data or when there's a bug in client code that causes large or numerous requests.</span></span>
  * <span data-ttu-id="c7cf9-1139">Rufen Sie `Abort` nicht für gewöhnliche Fehlersituationen auf, wie z. B. HTTP 404 (Nicht gefunden).</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1139">Don't call `Abort` for common error situations, such as HTTP 404 (Not Found).</span></span>

<span data-ttu-id="c7cf9-1140">Durch den Aufruf von [HttpResponse.CompleteAsync](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A) vor dem Aufruf von `Abort` wird sichergestellt, dass der Server das Schreiben der Antwort abgeschlossen hat.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1140">Calling [HttpResponse.CompleteAsync](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A) before calling `Abort` ensures that the server has completed writing the response.</span></span> <span data-ttu-id="c7cf9-1141">Das Clientverhalten ist jedoch nicht vorhersagbar, und es kann sein, dass die Antwort nicht gelesen wird, bevor die Verbindung abgebrochen wird.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1141">However, client behavior isn't predictable and they may not read the response before the connection is aborted.</span></span>

<span data-ttu-id="c7cf9-1142">Dieser Prozess bei HTTP/2 ist anders, da das Protokoll den Abbruch einzelner Anforderungsströme ohne Schließen der Verbindung unterstützt.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1142">This process is different for HTTP/2 because the protocol supports aborting individual request streams without closing the connection.</span></span> <span data-ttu-id="c7cf9-1143">Das fünfsekündige Zeitlimit für das Leeren gilt nicht.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1143">The five second drain timeout doesn't apply.</span></span> <span data-ttu-id="c7cf9-1144">Wenn nach dem Fertigstellen einer Antwort ungelesene Anforderungstextdaten vorhanden sind, sendet der Server einen HTTP/2-RST-Datenrahmen.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1144">If there's any unread request body data after completing a response, then the server sends an HTTP/2 RST frame.</span></span> <span data-ttu-id="c7cf9-1145">Zusätzliche Datenrahmen im Anforderungstext werden ignoriert.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1145">Additional request body data frames are ignored.</span></span>

<span data-ttu-id="c7cf9-1146">Wenn möglich, ist es für Clients besser, den Anforderungsheader [Expect: 100-continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) zu verwenden und auf die Antwort des Servers zu warten, bevor mit dem Senden des Anforderungstexts begonnen wird.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1146">If possible, it's better for clients to utilize the [Expect: 100-continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) request header and wait for the server to respond before starting to send the request body.</span></span> <span data-ttu-id="c7cf9-1147">Das gibt dem Client die Gelegenheit, die Antwort zu prüfen und abzubrechen, bevor nicht benötigte Daten gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1147">That gives the client an opportunity to examine the response and abort before sending unneeded data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c7cf9-1148">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1148">Additional resources</span></span>

* <span data-ttu-id="c7cf9-1149">Bei der Verwendung von UNIX-Sockets unter Linux wird der Socket beim Herunterfahren der App nicht automatisch gelöscht.</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1149">When using UNIX sockets on Linux, the socket is not automatically deleted on app shut down.</span></span> <span data-ttu-id="c7cf9-1150">Weitere Informationen finden Sie in [diesem GitHub-Issue](https://github.com/dotnet/aspnetcore/issues/14134).</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1150">For more information, see [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/14134).</span></span>
* <xref:test/troubleshoot>
* <xref:security/enforcing-ssl>
* <xref:host-and-deploy/proxy-load-balancer>
* [<span data-ttu-id="c7cf9-1151">RFC 7230: Message Syntax and Routing (Abschnitt 5.4: Host)</span><span class="sxs-lookup"><span data-stu-id="c7cf9-1151">RFC 7230: Message Syntax and Routing (Section 5.4: Host)</span></span>](https://tools.ietf.org/html/rfc7230#section-5.4)

::: moniker-end
