---
title: ASP.NET Core – Grundlagen
author: rick-anderson
description: Lernen Sie die grundlegenden Konzepte zum Erstellen von ASP.NET Core-Apps kennen.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/30/2020
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
uid: fundamentals/index
ms.openlocfilehash: 27b182394abe12a1631e5ba350942904bf4094aa
ms.sourcegitcommit: 3f0ad1e513296ede1bff39a05be6c278e879afed
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96035891"
---
# <a name="aspnet-core-fundamentals"></a><span data-ttu-id="62540-103">ASP.NET Core – Grundlagen</span><span class="sxs-lookup"><span data-stu-id="62540-103">ASP.NET Core fundamentals</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="62540-104">In diesem Artikel finden Sie eine Übersicht über die wichtigsten Themen zum Entwickeln von ASP.NET Core-Apps.</span><span class="sxs-lookup"><span data-stu-id="62540-104">This article provides an overview of key topics for understanding how to develop ASP.NET Core apps.</span></span>

## <a name="the-startup-class"></a><span data-ttu-id="62540-105">Die Startup-Klasse</span><span class="sxs-lookup"><span data-stu-id="62540-105">The Startup class</span></span>

<span data-ttu-id="62540-106">In der `Startup`-Klasse:</span><span class="sxs-lookup"><span data-stu-id="62540-106">The `Startup` class is where:</span></span>

* <span data-ttu-id="62540-107">werden die von der App erforderlichen Dienste konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="62540-107">Services required by the app are configured.</span></span>
* <span data-ttu-id="62540-108">Die Anforderungsverarbeitungspipeline der App wird mit mehreren Middlewarekomponenten definiert.</span><span class="sxs-lookup"><span data-stu-id="62540-108">The app's request handling pipeline is defined, as a series of middleware components.</span></span>

<span data-ttu-id="62540-109">Beispiel für eine `Startup`-Klasse:</span><span class="sxs-lookup"><span data-stu-id="62540-109">Here's a sample `Startup` class:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/Startup.cs?highlight=3,12)]

<span data-ttu-id="62540-110">Weitere Informationen finden Sie unter <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="62540-110">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="dependency-injection-services"></a><span data-ttu-id="62540-111">Abhängigkeitsinjektion (Dienste)</span><span class="sxs-lookup"><span data-stu-id="62540-111">Dependency injection (services)</span></span>

<span data-ttu-id="62540-112">ASP.NET Core umfasst ein integriertes DI-Framework (Dependency Injection), durch das konfigurierte Dienste in einer App bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="62540-112">ASP.NET Core includes a built-in dependency injection (DI) framework that makes configured services available throughout an app.</span></span> <span data-ttu-id="62540-113">Eine Protokollierungskomponente stellt beispielsweise einen Dienst dar.</span><span class="sxs-lookup"><span data-stu-id="62540-113">For example, a logging component is a service.</span></span>

<span data-ttu-id="62540-114">wird Code, der Dienste konfiguriert (oder *registriert*) der `Startup.ConfigureServices`-Methode hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="62540-114">Code to configure (or *register*) services is added to the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="62540-115">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="62540-115">For example:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/ConfigureServices.cs)]

<span data-ttu-id="62540-116">Dienste werden üblicherweise mit einer Constructor Injection aus der DI aufgelöst.</span><span class="sxs-lookup"><span data-stu-id="62540-116">Services are typically resolved from DI using constructor injection.</span></span> <span data-ttu-id="62540-117">Bei der Constructor Injection deklariert eine Klasse einen Konstruktorparameter des erforderlichen Typs oder einer Schnittstelle.</span><span class="sxs-lookup"><span data-stu-id="62540-117">With constructor injection, a class declares a constructor parameter of either the required type or an interface.</span></span> <span data-ttu-id="62540-118">Das DI-Framework stellt zur Laufzeit eine Instanz dieses Diensts bereit.</span><span class="sxs-lookup"><span data-stu-id="62540-118">The DI framework provides an instance of this service at runtime.</span></span>

<span data-ttu-id="62540-119">Im folgenden Beispiel wird die Constructor Injection verwendet, um `RazorPagesMovieContext` aus der DI zu lösen:</span><span class="sxs-lookup"><span data-stu-id="62540-119">The following example uses constructor injection to resolve a `RazorPagesMovieContext` from DI:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/Index.cshtml.cs?highlight=5)]

<span data-ttu-id="62540-120">Wenn der integrierte IoC-Container (Inversion of Control, Steuerungsumkehr) nicht alle Anforderungen einer App erfüllt, kann stattdessen ein IoC-Drittanbietercontainer verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="62540-120">If the built-in Inversion of Control (IoC) container doesn't meet all of an app's needs, a third-party IoC container can be used instead.</span></span>

<span data-ttu-id="62540-121">Weitere Informationen finden Sie unter <xref:fundamentals/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="62540-121">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="middleware"></a><span data-ttu-id="62540-122">Middleware</span><span class="sxs-lookup"><span data-stu-id="62540-122">Middleware</span></span>

<span data-ttu-id="62540-123">Die Pipeline zur Anforderungsverarbeitung besteht aus mehreren Middlewarekomponenten.</span><span class="sxs-lookup"><span data-stu-id="62540-123">The request handling pipeline is composed as a series of middleware components.</span></span> <span data-ttu-id="62540-124">Jede Komponente führt Vorgänge in einem `HttpContext` aus und ruft anschließend entweder die nächste Middleware in der Pipeline auf oder beendet die Anforderung.</span><span class="sxs-lookup"><span data-stu-id="62540-124">Each component performs operations on an `HttpContext` and either invokes the next middleware in the pipeline or terminates the request.</span></span>

<span data-ttu-id="62540-125">Gemäß Konvention wird eine Middlewarekomponente der Pipeline durch Aufrufen einer `Use...`-Erweiterungsmethode in der `Startup.Configure`-Methode hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="62540-125">By convention, a middleware component is added to the pipeline by invoking a `Use...` extension method in the `Startup.Configure` method.</span></span> <span data-ttu-id="62540-126">Um beispielsweise das Rendering statischer Dateien zu aktivieren, rufen Sie `UseStaticFiles` auf.</span><span class="sxs-lookup"><span data-stu-id="62540-126">For example, to enable rendering of static files, call `UseStaticFiles`.</span></span>

<span data-ttu-id="62540-127">Im folgenden Beispiel wird einer Anforderungsverarbeitungspipeline konfiguriert:</span><span class="sxs-lookup"><span data-stu-id="62540-127">The following example configures a request handling pipeline:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/Configure.cs)]

<span data-ttu-id="62540-128">ASP.NET Core enthält zahlreiche integrierte Middlewareanwendungen.</span><span class="sxs-lookup"><span data-stu-id="62540-128">ASP.NET Core includes a rich set of built-in middleware.</span></span> <span data-ttu-id="62540-129">Es können auch benutzerdefinierte Middlewarekomponenten geschrieben werden.</span><span class="sxs-lookup"><span data-stu-id="62540-129">Custom middleware components can also be written.</span></span>

<span data-ttu-id="62540-130">Weitere Informationen finden Sie unter <xref:fundamentals/middleware/index>.</span><span class="sxs-lookup"><span data-stu-id="62540-130">For more information, see <xref:fundamentals/middleware/index>.</span></span>

## <a name="host"></a><span data-ttu-id="62540-131">Host</span><span class="sxs-lookup"><span data-stu-id="62540-131">Host</span></span>

<span data-ttu-id="62540-132">Beim Starten erstellt eine ASP.NET Core-App einen *Host*.</span><span class="sxs-lookup"><span data-stu-id="62540-132">On startup, an ASP.NET Core app builds a *host*.</span></span> <span data-ttu-id="62540-133">Der Host kapselt alle Ressourcen der App, zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="62540-133">The host encapsulates all of the app's resources, such as:</span></span>

* <span data-ttu-id="62540-134">eine HTTP-Serverimplementierung</span><span class="sxs-lookup"><span data-stu-id="62540-134">An HTTP server implementation</span></span>
* <span data-ttu-id="62540-135">Middlewarekomponenten</span><span class="sxs-lookup"><span data-stu-id="62540-135">Middleware components</span></span>
* <span data-ttu-id="62540-136">Protokollierung</span><span class="sxs-lookup"><span data-stu-id="62540-136">Logging</span></span>
* <span data-ttu-id="62540-137">DI-Dienste</span><span class="sxs-lookup"><span data-stu-id="62540-137">Dependency injection (DI) services</span></span>
* <span data-ttu-id="62540-138">Konfiguration</span><span class="sxs-lookup"><span data-stu-id="62540-138">Configuration</span></span>

<span data-ttu-id="62540-139">Es gibt zwei verschiedene Hosts:</span><span class="sxs-lookup"><span data-stu-id="62540-139">There are two different hosts:</span></span> 

* <span data-ttu-id="62540-140">Generischer .NET-Host</span><span class="sxs-lookup"><span data-stu-id="62540-140">.NET Generic Host</span></span>
* <span data-ttu-id="62540-141">ASP.NET Core-Webhost</span><span class="sxs-lookup"><span data-stu-id="62540-141">ASP.NET Core Web Host</span></span>

<span data-ttu-id="62540-142">Der generische .NET-Host wird empfohlen.</span><span class="sxs-lookup"><span data-stu-id="62540-142">The .NET Generic Host is recommended.</span></span> <span data-ttu-id="62540-143">Der ASP.NET Core-Webhost wird lediglich für die Abwärtskompatibilität zur Verfügung gestellt.</span><span class="sxs-lookup"><span data-stu-id="62540-143">The ASP.NET Core Web Host is available only for backwards compatibility.</span></span>

<span data-ttu-id="62540-144">Im folgenden Beispiel wird ein neuer generischer .NET-Host erstellt:</span><span class="sxs-lookup"><span data-stu-id="62540-144">The following example creates a .NET Generic Host:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/Program.cs)]

<span data-ttu-id="62540-145">Mit den Methoden `CreateDefaultBuilder` und `ConfigureWebHostDefaults` wird ein Host mit mehreren Standardoptionen konfiguriert, zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="62540-145">The `CreateDefaultBuilder` and `ConfigureWebHostDefaults` methods configure a host with a set of default options, such as:</span></span>

* <span data-ttu-id="62540-146">Verwenden von [Kestrel](#servers) als Webserver, und aktivieren der Integration von Internetinformationsdiensten.</span><span class="sxs-lookup"><span data-stu-id="62540-146">Use [Kestrel](#servers) as the web server and enable IIS integration.</span></span>
* <span data-ttu-id="62540-147">Laden der Konfiguration aus *appsettings.json* , *appsettings.[EnvironmentName].json*, Umgebungsvariablen, Befehlszeilenargumenten und anderen Konfigurationsquellen.</span><span class="sxs-lookup"><span data-stu-id="62540-147">Load configuration from *appsettings.json*, *appsettings.{Environment Name}.json*, environment variables, command line arguments, and other configuration sources.</span></span>
* <span data-ttu-id="62540-148">Senden von Protokollausgaben an die Konsole und Debuggen von Anbietern.</span><span class="sxs-lookup"><span data-stu-id="62540-148">Send logging output to the console and debug providers.</span></span>

<span data-ttu-id="62540-149">Weitere Informationen finden Sie unter <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="62540-149">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="non-web-scenarios"></a><span data-ttu-id="62540-150">Nicht webbasierte Szenarios</span><span class="sxs-lookup"><span data-stu-id="62540-150">Non-web scenarios</span></span>

<span data-ttu-id="62540-151">Mit dem generischen Host können andere App-Typen querschnittliche Frameworkerweiterungen wie Protokollierung, Dependency Injection (DI), Konfiguration und Lebensdauerverwaltung der App verwenden.</span><span class="sxs-lookup"><span data-stu-id="62540-151">The Generic Host allows other types of apps to use cross-cutting framework extensions, such as logging, dependency injection (DI), configuration, and app lifetime management.</span></span> <span data-ttu-id="62540-152">Weitere Informationen finden Sie unter <xref:fundamentals/host/generic-host> und <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="62540-152">For more information, see <xref:fundamentals/host/generic-host> and <xref:fundamentals/host/hosted-services>.</span></span>

## <a name="servers"></a><span data-ttu-id="62540-153">Server</span><span class="sxs-lookup"><span data-stu-id="62540-153">Servers</span></span>

<span data-ttu-id="62540-154">Eine ASP.NET Core-App verwendet eine HTTP-Serverimplementierung zum Lauschen auf HTTP-Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="62540-154">An ASP.NET Core app uses an HTTP server implementation to listen for HTTP requests.</span></span> <span data-ttu-id="62540-155">Der Server sendet Anforderungen an die App in Form von mehreren [Anforderungsfunktionen](xref:fundamentals/request-features) in einem `HttpContext`.</span><span class="sxs-lookup"><span data-stu-id="62540-155">The server surfaces requests to the app as a set of [request features](xref:fundamentals/request-features) composed into an `HttpContext`.</span></span>

# <a name="windows"></a>[<span data-ttu-id="62540-156">Windows</span><span class="sxs-lookup"><span data-stu-id="62540-156">Windows</span></span>](#tab/windows)

<span data-ttu-id="62540-157">Die folgenden Serverimplementierungen werden von ASP.NET Core bereitgestellt:</span><span class="sxs-lookup"><span data-stu-id="62540-157">ASP.NET Core provides the following server implementations:</span></span>

* <span data-ttu-id="62540-158">*Kestrel* ist ein plattformübergreifender Webserver.</span><span class="sxs-lookup"><span data-stu-id="62540-158">*Kestrel* is a cross-platform web server.</span></span> <span data-ttu-id="62540-159">Kestrel wird häufig in einer Reverseproxykonfiguration unter Verwendung von [IIS](https://www.iis.net/) ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="62540-159">Kestrel is often run in a reverse proxy configuration using [IIS](https://www.iis.net/).</span></span> <span data-ttu-id="62540-160">In ASP.NET Core 2.0 oder höher kann Kestrel als öffentlich zugänglicher Edge-Server ausgeführt werden, der direkt mit dem Internet verknüpft ist.</span><span class="sxs-lookup"><span data-stu-id="62540-160">In ASP.NET Core 2.0 or later, Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span>
* <span data-ttu-id="62540-161">Der *IIS-HTTP-Server* ist ein Server für Windows, der IIS verwendet.</span><span class="sxs-lookup"><span data-stu-id="62540-161">*IIS HTTP Server* is a server for Windows that uses IIS.</span></span> <span data-ttu-id="62540-162">Mit diesem Server werden die ASP.NET Core-App und IIS im gleichen Prozess ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="62540-162">With this server, the ASP.NET Core app and IIS run in the same process.</span></span>
* <span data-ttu-id="62540-163">*HTTP.sys* ist ein Server für Windows, der nicht mit IIS verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="62540-163">*HTTP.sys* is a server for Windows that isn't used with IIS.</span></span>

# <a name="macos"></a>[<span data-ttu-id="62540-164">macOS</span><span class="sxs-lookup"><span data-stu-id="62540-164">macOS</span></span>](#tab/macos)

<span data-ttu-id="62540-165">ASP.NET Core bietet die plattformübergreifende Serverimplementierung von *Kestrel*.</span><span class="sxs-lookup"><span data-stu-id="62540-165">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="62540-166">In ASP.NET Core 2.0 oder höher kann Kestrel als öffentlich zugänglicher Edgeserver ausgeführt werden, der direkt mit dem Internet verknüpft ist.</span><span class="sxs-lookup"><span data-stu-id="62540-166">In ASP.NET Core 2.0 or later, Kestrel can run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="62540-167">Kestrel wird häufig in einer Reverseproxykonfiguration unter Verwendung von [Nginx](https://nginx.org) oder [Apache](https://httpd.apache.org/) ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="62540-167">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

# <a name="linux"></a>[<span data-ttu-id="62540-168">Linux</span><span class="sxs-lookup"><span data-stu-id="62540-168">Linux</span></span>](#tab/linux)

<span data-ttu-id="62540-169">ASP.NET Core bietet die plattformübergreifende Serverimplementierung von *Kestrel*.</span><span class="sxs-lookup"><span data-stu-id="62540-169">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="62540-170">In ASP.NET Core 2.0 oder höher kann Kestrel als öffentlich zugänglicher Edgeserver ausgeführt werden, der direkt mit dem Internet verknüpft ist.</span><span class="sxs-lookup"><span data-stu-id="62540-170">In ASP.NET Core 2.0 or later, Kestrel can run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="62540-171">Kestrel wird häufig in einer Reverseproxykonfiguration unter Verwendung von [Nginx](https://nginx.org) oder [Apache](https://httpd.apache.org/) ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="62540-171">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

---

<span data-ttu-id="62540-172">Weitere Informationen finden Sie unter <xref:fundamentals/servers/index>.</span><span class="sxs-lookup"><span data-stu-id="62540-172">For more information, see <xref:fundamentals/servers/index>.</span></span>

## <a name="configuration"></a><span data-ttu-id="62540-173">Konfiguration</span><span class="sxs-lookup"><span data-stu-id="62540-173">Configuration</span></span>

<span data-ttu-id="62540-174">ASP.NET Core bietet ein Konfigurationsframework, das Einstellungen als Name/Wert-Paare aus einer geordneten Menge von Konfigurationsanbietern abruft.</span><span class="sxs-lookup"><span data-stu-id="62540-174">ASP.NET Core provides a configuration framework that gets settings as name-value pairs from an ordered set of configuration providers.</span></span> <span data-ttu-id="62540-175">Integrierte Konfigurationsanbieter stehen für eine Vielzahl von Quellen zur Verfügung, z. B. für *JSON*-Dateien, *XML*-Dateien, Umgebungsvariablen und Befehlszeilenargumente.</span><span class="sxs-lookup"><span data-stu-id="62540-175">Built-in configuration providers are available for a variety of sources, such as *.json* files, *.xml* files, environment variables, and command-line arguments.</span></span> <span data-ttu-id="62540-176">Schreiben Sie benutzerdefinierte Konfigurationsanbieter, um andere Quellen zu unterstützen.</span><span class="sxs-lookup"><span data-stu-id="62540-176">Write custom configuration providers to support other sources.</span></span>

<span data-ttu-id="62540-177">ASP.NET Core-Apps werden [standardmäßig](xref:fundamentals/configuration/index#default) zum Lesen aus *appsettings.json* , Umgebungsvariablen, der Befehlszeile und mehr konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="62540-177">By [default](xref:fundamentals/configuration/index#default), ASP.NET Core apps are configured to read from *appsettings.json*, environment variables, the command line, and more.</span></span> <span data-ttu-id="62540-178">Wenn die Konfiguration der App geladen wird, überschreiben Werte aus Umgebungsvariablen die Werte von *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="62540-178">When the app's configuration is loaded, values from environment variables override values from *appsettings.json*.</span></span>

<span data-ttu-id="62540-179">Die bevorzugte Methode für das Lesen zugehöriger Konfigurationswerte ist die Verwendung des [Optionsmusters](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="62540-179">The preferred way to read related configuration values is using the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="62540-180">Weitere Informationen finden Sie unter [Binden hierarchischer Konfigurationsdaten mit dem Optionsmuster](xref:fundamentals/configuration/index#optpat).</span><span class="sxs-lookup"><span data-stu-id="62540-180">For more information, see [Bind hierarchical configuration data using the options pattern](xref:fundamentals/configuration/index#optpat).</span></span>

<span data-ttu-id="62540-181">Zum Verwalten vertraulicher Konfigurationsdaten wie Kennwörter bietet .NET Core den [Secret Manager](xref:security/app-secrets#secret-manager).</span><span class="sxs-lookup"><span data-stu-id="62540-181">For managing confidential configuration data such as passwords, .NET Core provides the [Secret Manager](xref:security/app-secrets#secret-manager).</span></span> <span data-ttu-id="62540-182">Für Produktionsgeheimnisse empfehlen wir [Azure Key Vault](xref:security/key-vault-configuration).</span><span class="sxs-lookup"><span data-stu-id="62540-182">For production secrets, we recommend [Azure Key Vault](xref:security/key-vault-configuration).</span></span>

<span data-ttu-id="62540-183">Weitere Informationen finden Sie unter <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="62540-183">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="environments"></a><span data-ttu-id="62540-184">Umgebungen</span><span class="sxs-lookup"><span data-stu-id="62540-184">Environments</span></span>

<span data-ttu-id="62540-185">Ausführungsumgebungen wie `Development`, `Staging` und `Production` sind in ASP.NET Core von besonderer Bedeutung.</span><span class="sxs-lookup"><span data-stu-id="62540-185">Execution environments, such as `Development`, `Staging`, and `Production`, are a first-class notion in ASP.NET Core.</span></span> <span data-ttu-id="62540-186">Legen Sie die Umgebung fest, in der eine App ausgeführt wird, indem Sie die Umgebungsvariable `ASPNETCORE_ENVIRONMENT` festlegen.</span><span class="sxs-lookup"><span data-stu-id="62540-186">Specify the environment an app is running in by setting the `ASPNETCORE_ENVIRONMENT` environment variable.</span></span> <span data-ttu-id="62540-187">ASP.NET Core liest diese Umgebungsvariable beim Start der App und speichert den Wert in einer `IWebHostEnvironment`-Implementierung.</span><span class="sxs-lookup"><span data-stu-id="62540-187">ASP.NET Core reads that environment variable at app startup and stores the value in an `IWebHostEnvironment` implementation.</span></span> <span data-ttu-id="62540-188">Diese Implementierung ist per DI überall in einer App verfügbar.</span><span class="sxs-lookup"><span data-stu-id="62540-188">This implementation is available anywhere in an app via dependency injection (DI).</span></span>

<span data-ttu-id="62540-189">Im Folgenden Beispiel wird die App so konfiguriert, dass sie ausführliche Fehlerinformationen angibt, wenn sie in der `Development`-Umgebung ausgeführt wird:</span><span class="sxs-lookup"><span data-stu-id="62540-189">The following example configures the app to provide detailed error information when running in the `Development` environment:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/StartupConfigure.cs?highlight=3-6)]

<span data-ttu-id="62540-190">Weitere Informationen finden Sie unter <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="62540-190">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="logging"></a><span data-ttu-id="62540-191">Protokollierung</span><span class="sxs-lookup"><span data-stu-id="62540-191">Logging</span></span>

<span data-ttu-id="62540-192">ASP.NET Core unterstützt eine Protokollierungs-API, die mit einer Vielzahl von integrierten Protokollierungsanbietern und Drittanbieter-Protokollierungslösungen zusammenarbeitet.</span><span class="sxs-lookup"><span data-stu-id="62540-192">ASP.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="62540-193">Folgende Anbieter sind verfügbar:</span><span class="sxs-lookup"><span data-stu-id="62540-193">Available providers include:</span></span>

* <span data-ttu-id="62540-194">Konsole</span><span class="sxs-lookup"><span data-stu-id="62540-194">Console</span></span>
* <span data-ttu-id="62540-195">Debug</span><span class="sxs-lookup"><span data-stu-id="62540-195">Debug</span></span>
* <span data-ttu-id="62540-196">Ereignisablaufverfolgung unter Windows</span><span class="sxs-lookup"><span data-stu-id="62540-196">Event Tracing on Windows</span></span>
* <span data-ttu-id="62540-197">Windows-Ereignisprotokoll</span><span class="sxs-lookup"><span data-stu-id="62540-197">Windows Event Log</span></span>
* <span data-ttu-id="62540-198">TraceSource</span><span class="sxs-lookup"><span data-stu-id="62540-198">TraceSource</span></span>
* <span data-ttu-id="62540-199">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="62540-199">Azure App Service</span></span>
* <span data-ttu-id="62540-200">Azure Application Insights</span><span class="sxs-lookup"><span data-stu-id="62540-200">Azure Application Insights</span></span>

<span data-ttu-id="62540-201">Zum Erstellen von Protokollen lösen Sie einen <xref:Microsoft.Extensions.Logging.ILogger%601>-Dienst aus DI, und rufen Sie Protokollierungsmethoden wie <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> auf.</span><span class="sxs-lookup"><span data-stu-id="62540-201">To create logs, resolve an <xref:Microsoft.Extensions.Logging.ILogger%601> service from dependency injection (DI) and call logging methods such as <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>.</span></span> <span data-ttu-id="62540-202">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="62540-202">For example:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/TodoController.cs?highlight=5,13,19)]

<span data-ttu-id="62540-203">Protokollierungsmethoden wie `LogInformation` unterstützen eine beliebige Anzahl von Feldern.</span><span class="sxs-lookup"><span data-stu-id="62540-203">Logging methods such as `LogInformation` support any number of fields.</span></span> <span data-ttu-id="62540-204">Diese Felder werden häufig zum Erstellen einer `string`-Meldung verwendet, einige Protokollierungsanbieter senden sie jedoch als separate Felder an einen Datenspeicher.</span><span class="sxs-lookup"><span data-stu-id="62540-204">These fields are commonly used to construct a message `string`, but some logging providers send these to a data store as separate fields.</span></span> <span data-ttu-id="62540-205">Dieses Funktion ermöglicht Protokollierungsanbietern das Implementieren von [semantischer Protokollierung, auch bezeichnet als strukturierte Protokollierung](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span><span class="sxs-lookup"><span data-stu-id="62540-205">This feature makes it possible for logging providers to implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="62540-206">Weitere Informationen finden Sie unter <xref:fundamentals/logging/index>.</span><span class="sxs-lookup"><span data-stu-id="62540-206">For more information, see <xref:fundamentals/logging/index>.</span></span>

## <a name="routing"></a><span data-ttu-id="62540-207">Routing</span><span class="sxs-lookup"><span data-stu-id="62540-207">Routing</span></span>

<span data-ttu-id="62540-208">Eine *Route* ist ein URL-Muster, das einem Handler zugeordnet ist.</span><span class="sxs-lookup"><span data-stu-id="62540-208">A *route* is a URL pattern that is mapped to a handler.</span></span> <span data-ttu-id="62540-209">Der Handler ist normalerweise eine Razor-Seite, eine Aktionsmethode in einem MVC-Controller oder einer Middleware.</span><span class="sxs-lookup"><span data-stu-id="62540-209">The handler is typically a Razor page, an action method in an MVC controller, or a middleware.</span></span> <span data-ttu-id="62540-210">Mit ASP.NET Core-Routing können Sie steuern, welche URLs von Ihrer App verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="62540-210">ASP.NET Core routing gives you control over the URLs used by your app.</span></span>

<span data-ttu-id="62540-211">Weitere Informationen finden Sie unter <xref:fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="62540-211">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="error-handling"></a><span data-ttu-id="62540-212">Fehlerbehandlung</span><span class="sxs-lookup"><span data-stu-id="62540-212">Error handling</span></span>

<span data-ttu-id="62540-213">ASP.NET Core verfügt über integrierte Funktionen zur Fehlerbehandlung wie beispielsweise:</span><span class="sxs-lookup"><span data-stu-id="62540-213">ASP.NET Core has built-in features for handling errors, such as:</span></span>

* <span data-ttu-id="62540-214">eine Seite mit Ausnahmen für Entwickler</span><span class="sxs-lookup"><span data-stu-id="62540-214">A developer exception page</span></span>
* <span data-ttu-id="62540-215">benutzerdefinierte Fehlerseiten</span><span class="sxs-lookup"><span data-stu-id="62540-215">Custom error pages</span></span>
* <span data-ttu-id="62540-216">statische Statuscodeseiten</span><span class="sxs-lookup"><span data-stu-id="62540-216">Static status code pages</span></span>
* <span data-ttu-id="62540-217">Fehlerbehandlung während des Starts</span><span class="sxs-lookup"><span data-stu-id="62540-217">Startup exception handling</span></span>

<span data-ttu-id="62540-218">Weitere Informationen finden Sie unter <xref:fundamentals/error-handling>.</span><span class="sxs-lookup"><span data-stu-id="62540-218">For more information, see <xref:fundamentals/error-handling>.</span></span>

## <a name="make-http-requests"></a><span data-ttu-id="62540-219">Übermitteln von HTTP-Anforderungen</span><span class="sxs-lookup"><span data-stu-id="62540-219">Make HTTP requests</span></span>

<span data-ttu-id="62540-220">Eine Implementierung von `IHttpClientFactory` ist verfügbar zum Erstellen von `HttpClient`-Instanzen.</span><span class="sxs-lookup"><span data-stu-id="62540-220">An implementation of `IHttpClientFactory` is available for creating `HttpClient` instances.</span></span> <span data-ttu-id="62540-221">Die Factory:</span><span class="sxs-lookup"><span data-stu-id="62540-221">The factory:</span></span>

* <span data-ttu-id="62540-222">Ein zentraler Ort für das Benennen und Konfigurieren logischer `HttpClient`-Instanzen wird damit geboten.</span><span class="sxs-lookup"><span data-stu-id="62540-222">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="62540-223">Registrieren und konfigurieren Sie beispielsweise einen *GitHub*-Client für den Zugriff auf GitHub.</span><span class="sxs-lookup"><span data-stu-id="62540-223">For example, register and configure a *github* client for accessing GitHub.</span></span> <span data-ttu-id="62540-224">Registrieren und konfigurieren Sie einen Standardclient für andere Zwecke.</span><span class="sxs-lookup"><span data-stu-id="62540-224">Register and configure a default client for other purposes.</span></span>
* <span data-ttu-id="62540-225">Unterstützt die Registrierung und Verkettung von mehreren delegierenden Handlern, um eine Pipeline für die Middleware für ausgehende Anforderungen zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="62540-225">Supports registration and chaining of multiple delegating handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="62540-226">Dieses Muster ähnelt der eingehenden Middlewarepipeline von ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="62540-226">This pattern is similar to ASP.NET Core's inbound middleware pipeline.</span></span> <span data-ttu-id="62540-227">Das Muster bietet einen Mechanismus zum Verwalten von übergreifenden Belangen für HTTP-Anforderungen, einschließlich der Zwischenspeicherung, Fehlerbehandlung, Serialisierung und Protokollierung.</span><span class="sxs-lookup"><span data-stu-id="62540-227">The pattern provides a mechanism to manage cross-cutting concerns for HTTP requests, including caching, error handling, serialization, and logging.</span></span>
* <span data-ttu-id="62540-228">Integrierbar in *Polly*, eine beliebte Drittanbieter-Bibliothek zur Behandlung vorübergehender Fehler.</span><span class="sxs-lookup"><span data-stu-id="62540-228">Integrates with *Polly*, a popular third-party library for transient fault handling.</span></span>
* <span data-ttu-id="62540-229">Das Pooling und die Lebensdauer von zugrunde liegenden `HttpClientHandler`-Instanzen werden verwaltet, um gängige DNS-Probleme zu vermeiden, die bei der manuellen Verwaltung der `HttpClient`-Lebensdauer auftreten.</span><span class="sxs-lookup"><span data-stu-id="62540-229">Manages the pooling and lifetime of underlying `HttpClientHandler` instances to avoid common DNS problems that occur when managing `HttpClient` lifetimes manually.</span></span>
* <span data-ttu-id="62540-230">Eine konfigurierbare Protokollierungsfunktion wird über <xref:Microsoft.Extensions.Logging.ILogger> für alle Anforderungen hinzugefügt, die über Clients gesendet werden, die von der Factory erstellt wurden.</span><span class="sxs-lookup"><span data-stu-id="62540-230">Adds a configurable logging experience via <xref:Microsoft.Extensions.Logging.ILogger> for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="62540-231">Weitere Informationen finden Sie unter <xref:fundamentals/http-requests>.</span><span class="sxs-lookup"><span data-stu-id="62540-231">For more information, see <xref:fundamentals/http-requests>.</span></span>

## <a name="content-root"></a><span data-ttu-id="62540-232">Inhaltsstammverzeichnis</span><span class="sxs-lookup"><span data-stu-id="62540-232">Content root</span></span>

<span data-ttu-id="62540-233">Der Inhaltsstamm ist der Basispfad für:</span><span class="sxs-lookup"><span data-stu-id="62540-233">The content root is the base path for:</span></span>

* <span data-ttu-id="62540-234">Die ausführbare Datei, die die App hostet ( *.exe*).</span><span class="sxs-lookup"><span data-stu-id="62540-234">The executable hosting the app (*.exe*).</span></span>
* <span data-ttu-id="62540-235">Kompilierten Assemblys, die die App bilden ( *.dll*).</span><span class="sxs-lookup"><span data-stu-id="62540-235">Compiled assemblies that make up the app (*.dll*).</span></span>
* <span data-ttu-id="62540-236">Inhaltsdateien, die von der App verwendet werden, z. B.:</span><span class="sxs-lookup"><span data-stu-id="62540-236">Content files used by the app, such as:</span></span>
  * <span data-ttu-id="62540-237">Razor-Dateien ( *.cshtml*, *.razor*)</span><span class="sxs-lookup"><span data-stu-id="62540-237">Razor files (*.cshtml*, *.razor*)</span></span>
  * <span data-ttu-id="62540-238">Konfigurationsdateien ( *.json*, *.xml*)</span><span class="sxs-lookup"><span data-stu-id="62540-238">Configuration files (*.json*, *.xml*)</span></span>
  * <span data-ttu-id="62540-239">Datendateien ( *.db*)</span><span class="sxs-lookup"><span data-stu-id="62540-239">Data files (*.db*)</span></span>
* <span data-ttu-id="62540-240">Der [Webstamm](#web-root), in der Regel der Ordner *wwwroot*</span><span class="sxs-lookup"><span data-stu-id="62540-240">The [Web root](#web-root), typically the *wwwroot* folder.</span></span>

<span data-ttu-id="62540-241">Während der Entwicklung wird standardmäßig das Stammverzeichnis des Projekts als Inhaltsstamm verwendet.</span><span class="sxs-lookup"><span data-stu-id="62540-241">During development, the content root defaults to the project's root directory.</span></span> <span data-ttu-id="62540-242">Dieses Verzeichnis ist auch der Basispfad für die Inhaltsdateien der App und den [Webstamm](#web-root).</span><span class="sxs-lookup"><span data-stu-id="62540-242">This directory is also the base path for both the app's content files and the [Web root](#web-root).</span></span> <span data-ttu-id="62540-243">Sie können einen anderen Inhaltsstamm festlegen, indem Sie den entsprechenden Pfad beim [Erstellen des Hosts](#host) festlegen.</span><span class="sxs-lookup"><span data-stu-id="62540-243">Specify a different content root by setting its path when [building the host](#host).</span></span> <span data-ttu-id="62540-244">Weitere Informationen finden Sie unter [Inhaltsstamm](xref:fundamentals/host/generic-host#contentroot).</span><span class="sxs-lookup"><span data-stu-id="62540-244">For more information, see [Content root](xref:fundamentals/host/generic-host#contentroot).</span></span>

## <a name="web-root"></a><span data-ttu-id="62540-245">Webstammverzeichnis</span><span class="sxs-lookup"><span data-stu-id="62540-245">Web root</span></span>

<span data-ttu-id="62540-246">Der Webstamm ist der Basispfad für öffentliche, statische Ressourcendateien, zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="62540-246">The web root is the base path for public, static resource files, such as:</span></span>

* <span data-ttu-id="62540-247">Stylesheets ( *.css*)</span><span class="sxs-lookup"><span data-stu-id="62540-247">Stylesheets (*.css*)</span></span>
* <span data-ttu-id="62540-248">JavaScript ( *.js*)</span><span class="sxs-lookup"><span data-stu-id="62540-248">JavaScript (*.js*)</span></span>
* <span data-ttu-id="62540-249">Bilder ( *.png*, *.jpg*)</span><span class="sxs-lookup"><span data-stu-id="62540-249">Images (*.png*, *.jpg*)</span></span>

<span data-ttu-id="62540-250">Statische Dateien werden standardmäßig nur aus dem Webstammverzeichnis und dessen Unterverzeichnissen bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="62540-250">By default, static files are served only from the web root directory and its sub-directories.</span></span> <span data-ttu-id="62540-251">Standardmäßig lautet der Webstammpfad *{Inhaltsstamm}/wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="62540-251">The web root path defaults to *{content root}/wwwroot*.</span></span> <span data-ttu-id="62540-252">Sie können einen anderen Webstamm festlegen, indem Sie den entsprechenden Pfad beim [Erstellen des Hosts](#host) festlegen.</span><span class="sxs-lookup"><span data-stu-id="62540-252">Specify a different web root by setting its path when [building the host](#host).</span></span> <span data-ttu-id="62540-253">Weitere Informationen finden Sie unter [Webstamm](xref:fundamentals/host/generic-host#webroot).</span><span class="sxs-lookup"><span data-stu-id="62540-253">For more information, see [Web root](xref:fundamentals/host/generic-host#webroot).</span></span>

<span data-ttu-id="62540-254">Verhindern Sie das Veröffentlichen von Dateien in *wwwroot* mit dem [\<Content>-Projektelement](/visualstudio/msbuild/common-msbuild-project-items#content) in der Projektdatei.</span><span class="sxs-lookup"><span data-stu-id="62540-254">Prevent publishing files in *wwwroot* with the [\<Content> project item](/visualstudio/msbuild/common-msbuild-project-items#content) in the project file.</span></span> <span data-ttu-id="62540-255">Im folgenden Beispiel wird verhindert, dass Inhalte im Verzeichnis *wwwroot/local* und dessen Unterverzeichnissen veröffentlicht werden:</span><span class="sxs-lookup"><span data-stu-id="62540-255">The following example prevents publishing content in *wwwroot/local* and its sub-directories:</span></span>

```xml
<ItemGroup>
  <Content Update="wwwroot\local\**\*.*" CopyToPublishDirectory="Never" />
</ItemGroup>
```

<span data-ttu-id="62540-256">In Razor-Dateien ( *.cshtml*) verweisen Tilde und Schrägstrich (`~/`) auf den Webstamm.</span><span class="sxs-lookup"><span data-stu-id="62540-256">In Razor *.cshtml* files, tilde-slash (`~/`) points to the web root.</span></span> <span data-ttu-id="62540-257">Ein mit `~/` beginnender Pfad wird als *virtueller Pfad* bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="62540-257">A path beginning with `~/` is referred to as a *virtual path*.</span></span>

<span data-ttu-id="62540-258">Weitere Informationen finden Sie unter <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="62540-258">For more information, see <xref:fundamentals/static-files>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="62540-259">In diesem Artikel finden Sie eine Übersicht über die wichtigsten Themen zum Entwickeln von ASP.NET Core-Apps.</span><span class="sxs-lookup"><span data-stu-id="62540-259">This article is an overview of key topics for understanding how to develop ASP.NET Core apps.</span></span>

## <a name="the-startup-class"></a><span data-ttu-id="62540-260">Die Startup-Klasse</span><span class="sxs-lookup"><span data-stu-id="62540-260">The Startup class</span></span>

<span data-ttu-id="62540-261">In der `Startup`-Klasse:</span><span class="sxs-lookup"><span data-stu-id="62540-261">The `Startup` class is where:</span></span>

* <span data-ttu-id="62540-262">werden die von der App erforderlichen Dienste konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="62540-262">Services required by the app are configured.</span></span>
* <span data-ttu-id="62540-263">wird die Pipeline zur Anforderungsverarbeitung definiert.</span><span class="sxs-lookup"><span data-stu-id="62540-263">The request handling pipeline is defined.</span></span>

<span data-ttu-id="62540-264">*Dienste* sind Komponenten, die von der App verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="62540-264">*Services* are components that are used by the app.</span></span> <span data-ttu-id="62540-265">Eine Protokollierungskomponente stellt beispielsweise einen Dienst dar.</span><span class="sxs-lookup"><span data-stu-id="62540-265">For example, a logging component is a service.</span></span> <span data-ttu-id="62540-266">wird Code, der Dienste konfiguriert (oder *registriert*) der `Startup.ConfigureServices`-Methode hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="62540-266">Code to configure (or *register*) services is added to the `Startup.ConfigureServices` method.</span></span>

<span data-ttu-id="62540-267">Die Pipeline zur Anforderungsverarbeitung besteht aus mehreren *Middlewarekomponenten*.</span><span class="sxs-lookup"><span data-stu-id="62540-267">The request handling pipeline is composed as a series of *middleware* components.</span></span> <span data-ttu-id="62540-268">Eine Middleware kann beispielsweise Anforderungen für statische Dateien verarbeiten oder HTTP-Anforderungen zu HTTPS umleiten.</span><span class="sxs-lookup"><span data-stu-id="62540-268">For example, a middleware might handle requests for static files or redirect HTTP requests to HTTPS.</span></span> <span data-ttu-id="62540-269">Jede Middleware führt asynchrone Operationen in einem `HttpContext` aus und ruft anschließend entweder die nächste Middleware in der Pipeline auf oder beendet die Anforderung.</span><span class="sxs-lookup"><span data-stu-id="62540-269">Each middleware performs asynchronous operations on an `HttpContext` and then either invokes the next middleware in the pipeline or terminates the request.</span></span> <span data-ttu-id="62540-270">Code zum Konfigurieren der Pipeline zur Anforderungsverarbeitung wird der `Startup.Configure`-Methode hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="62540-270">Code to configure the request handling pipeline is added to the `Startup.Configure` method.</span></span>

<span data-ttu-id="62540-271">Beispiel für eine `Startup`-Klasse:</span><span class="sxs-lookup"><span data-stu-id="62540-271">Here's a sample `Startup` class:</span></span>

[!code-csharp[](index/samples_snapshot/2.x/Startup.cs?highlight=3,12)]

<span data-ttu-id="62540-272">Weitere Informationen finden Sie unter <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="62540-272">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="dependency-injection-services"></a><span data-ttu-id="62540-273">Abhängigkeitsinjektion (Dienste)</span><span class="sxs-lookup"><span data-stu-id="62540-273">Dependency injection (services)</span></span>

<span data-ttu-id="62540-274">ASP.NET Core verfügt über ein integriertes DI-Framework (Dependency Injection), durch das konfigurierte Dienste für die Klassen einer App bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="62540-274">ASP.NET Core has a built-in dependency injection (DI) framework that makes configured services available to an app's classes.</span></span> <span data-ttu-id="62540-275">Eine Möglichkeit zum Abrufen einer Instanz eines Diensts in einer Klasse ist das Erstellen eines Konstruktors mit einem Parameter des erforderlichen Typs.</span><span class="sxs-lookup"><span data-stu-id="62540-275">One way to get an instance of a service in a class is to create a constructor with a parameter of the required type.</span></span> <span data-ttu-id="62540-276">Der Parameter kann der Diensttyp oder eine Schnittstelle sein.</span><span class="sxs-lookup"><span data-stu-id="62540-276">The parameter can be the service type or an interface.</span></span> <span data-ttu-id="62540-277">Das DI-System stellt den Dienst zur Laufzeit bereit.</span><span class="sxs-lookup"><span data-stu-id="62540-277">The DI system provides the service at runtime.</span></span>

<span data-ttu-id="62540-278">Hier ist eine Klasse, in der Dependency Injection verwendet wird, um ein Entity Framework Core-Kontextobjekt abzurufen.</span><span class="sxs-lookup"><span data-stu-id="62540-278">Here's a class that uses DI to get an Entity Framework Core context object.</span></span> <span data-ttu-id="62540-279">Die hervorgehobene Zeile ist ein Beispiel für die Konstruktorinjektion:</span><span class="sxs-lookup"><span data-stu-id="62540-279">The highlighted line is an example of constructor injection:</span></span>

[!code-csharp[](index/samples_snapshot/2.x/Index.cshtml.cs?highlight=5)]

<span data-ttu-id="62540-280">Obwohl die Dependency Injection integriert ist, können Sie hier, falls gewünscht, einen IoC-Container eines Drittanbieters einbinden.</span><span class="sxs-lookup"><span data-stu-id="62540-280">While DI is built in, it's designed to let you plug in a third-party Inversion of Control (IoC) container if you prefer.</span></span>

<span data-ttu-id="62540-281">Weitere Informationen finden Sie unter <xref:fundamentals/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="62540-281">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="middleware"></a><span data-ttu-id="62540-282">Middleware</span><span class="sxs-lookup"><span data-stu-id="62540-282">Middleware</span></span>

<span data-ttu-id="62540-283">Die Pipeline zur Anforderungsverarbeitung besteht aus mehreren Middlewarekomponenten.</span><span class="sxs-lookup"><span data-stu-id="62540-283">The request handling pipeline is composed as a series of middleware components.</span></span> <span data-ttu-id="62540-284">Jede Komponente führt asynchrone Operationen in einem `HttpContext` aus und ruft anschließend entweder die nächste Middleware in der Pipeline auf oder beendet die Anforderung.</span><span class="sxs-lookup"><span data-stu-id="62540-284">Each component performs asynchronous operations on an `HttpContext` and then either invokes the next middleware in the pipeline or terminates the request.</span></span>

<span data-ttu-id="62540-285">Gemäß Konvention wird eine Middlewarekomponente der Pipeline durch Aufrufen ihrer `Use...`-Erweiterungsmethode in der `Startup.Configure`-Methode hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="62540-285">By convention, a middleware component is added to the pipeline by invoking its `Use...` extension method in the `Startup.Configure` method.</span></span> <span data-ttu-id="62540-286">Um beispielsweise das Rendering statischer Dateien zu aktivieren, rufen Sie `UseStaticFiles` auf.</span><span class="sxs-lookup"><span data-stu-id="62540-286">For example, to enable rendering of static files, call `UseStaticFiles`.</span></span>

<span data-ttu-id="62540-287">Der hervorgehobene Code in dem folgenden Beispiel konfiguriert die Pipeline zur Anforderungsverarbeitung:</span><span class="sxs-lookup"><span data-stu-id="62540-287">The highlighted code in the following example configures the request handling pipeline:</span></span>

[!code-csharp[](index/samples_snapshot/2.x/Startup.cs?highlight=14-16)]

<span data-ttu-id="62540-288">ASP.NET Core enthält eine Reihe umfangreicher integrierter Middleware. Außerdem können Sie benutzerdefinierte Middleware erstellen.</span><span class="sxs-lookup"><span data-stu-id="62540-288">ASP.NET Core includes a rich set of built-in middleware, and you can write custom middleware.</span></span>

<span data-ttu-id="62540-289">Weitere Informationen finden Sie unter <xref:fundamentals/middleware/index>.</span><span class="sxs-lookup"><span data-stu-id="62540-289">For more information, see <xref:fundamentals/middleware/index>.</span></span>

## <a name="host"></a><span data-ttu-id="62540-290">Host</span><span class="sxs-lookup"><span data-stu-id="62540-290">Host</span></span>

<span data-ttu-id="62540-291">Beim Starten erstellt eine ASP.NET Core-App einen *Host*.</span><span class="sxs-lookup"><span data-stu-id="62540-291">An ASP.NET Core app builds a *host* on startup.</span></span> <span data-ttu-id="62540-292">Der Host ist ein Objekt, das alle Ressourcen der App kapselt, z. B.:</span><span class="sxs-lookup"><span data-stu-id="62540-292">The host is an object that encapsulates all of the app's resources, such as:</span></span>

* <span data-ttu-id="62540-293">eine HTTP-Serverimplementierung</span><span class="sxs-lookup"><span data-stu-id="62540-293">An HTTP server implementation</span></span>
* <span data-ttu-id="62540-294">Middlewarekomponenten</span><span class="sxs-lookup"><span data-stu-id="62540-294">Middleware components</span></span>
* <span data-ttu-id="62540-295">Protokollierung</span><span class="sxs-lookup"><span data-stu-id="62540-295">Logging</span></span>
* <span data-ttu-id="62540-296">DI</span><span class="sxs-lookup"><span data-stu-id="62540-296">DI</span></span>
* <span data-ttu-id="62540-297">Konfiguration</span><span class="sxs-lookup"><span data-stu-id="62540-297">Configuration</span></span>

<span data-ttu-id="62540-298">Der wichtigste Grund für das Einschließen aller unabhängigen Ressourcen der App in einem Objekt ist die Verwaltung der Lebensdauer: steuern von Start und ordnungsgemäßem Herunterfahren der App.</span><span class="sxs-lookup"><span data-stu-id="62540-298">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

<span data-ttu-id="62540-299">Es stehen zwei Hosts zur Verfügung: der Webhost und der generische Host.</span><span class="sxs-lookup"><span data-stu-id="62540-299">Two hosts are available: the Web Host and the Generic Host.</span></span> <span data-ttu-id="62540-300">In ASP.NET Core 2.x ist der generische Host nur für nicht webbasierte Szenarios verfügbar.</span><span class="sxs-lookup"><span data-stu-id="62540-300">In ASP.NET Core 2.x, the Generic Host is only for non-web scenarios.</span></span>

<span data-ttu-id="62540-301">Der Code zum Erstellen eines Hosts befindet sich in `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="62540-301">The code to create a host is in `Program.Main`:</span></span>

[!code-csharp[](index/samples_snapshot/2.x/Program.cs)]

<span data-ttu-id="62540-302">Die Methode `CreateDefaultBuilder` konfiguriert einen Host mit häufig verwendeten Optionen wie den folgenden:</span><span class="sxs-lookup"><span data-stu-id="62540-302">The `CreateDefaultBuilder` method configures a host with commonly used options, such as the following:</span></span>

* <span data-ttu-id="62540-303">Verwenden von [Kestrel](#servers) als Webserver, und aktivieren der Integration von Internetinformationsdiensten.</span><span class="sxs-lookup"><span data-stu-id="62540-303">Use [Kestrel](#servers) as the web server and enable IIS integration.</span></span>
* <span data-ttu-id="62540-304">Laden der Konfiguration aus *appsettings.json* , *appsettings.[EnvironmentName].json*, Umgebungsvariablen, Befehlszeilenargumenten und anderen Konfigurationsquellen.</span><span class="sxs-lookup"><span data-stu-id="62540-304">Load configuration from *appsettings.json*, *appsettings.{Environment Name}.json*, environment variables, command line arguments, and other configuration sources.</span></span>
* <span data-ttu-id="62540-305">Senden von Protokollausgaben an die Konsole und Debuggen von Anbietern.</span><span class="sxs-lookup"><span data-stu-id="62540-305">Send logging output to the console and debug providers.</span></span>

<span data-ttu-id="62540-306">Weitere Informationen finden Sie unter <xref:fundamentals/host/web-host>.</span><span class="sxs-lookup"><span data-stu-id="62540-306">For more information, see <xref:fundamentals/host/web-host>.</span></span>

### <a name="non-web-scenarios"></a><span data-ttu-id="62540-307">Nicht webbasierte Szenarios</span><span class="sxs-lookup"><span data-stu-id="62540-307">Non-web scenarios</span></span>

<span data-ttu-id="62540-308">Mit dem generischen Host können andere App-Typen querschnittliche Frameworkerweiterungen wie Protokollierung, Dependency Injection (DI), Konfiguration und Lebensdauerverwaltung der App verwenden.</span><span class="sxs-lookup"><span data-stu-id="62540-308">The Generic Host allows other types of apps to use cross-cutting framework extensions, such as logging, dependency injection (DI), configuration, and app lifetime management.</span></span> <span data-ttu-id="62540-309">Weitere Informationen finden Sie unter <xref:fundamentals/host/generic-host> und <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="62540-309">For more information, see <xref:fundamentals/host/generic-host> and <xref:fundamentals/host/hosted-services>.</span></span>

## <a name="servers"></a><span data-ttu-id="62540-310">Server</span><span class="sxs-lookup"><span data-stu-id="62540-310">Servers</span></span>

<span data-ttu-id="62540-311">Eine ASP.NET Core-App verwendet eine HTTP-Serverimplementierung zum Lauschen auf HTTP-Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="62540-311">An ASP.NET Core app uses an HTTP server implementation to listen for HTTP requests.</span></span> <span data-ttu-id="62540-312">Der Server sendet Anforderungen an die App in Form von mehreren [Anforderungsfunktionen](xref:fundamentals/request-features) in einem `HttpContext`.</span><span class="sxs-lookup"><span data-stu-id="62540-312">The server surfaces requests to the app as a set of [request features](xref:fundamentals/request-features) composed into an `HttpContext`.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

# <a name="windows"></a>[<span data-ttu-id="62540-313">Windows</span><span class="sxs-lookup"><span data-stu-id="62540-313">Windows</span></span>](#tab/windows)

<span data-ttu-id="62540-314">Die folgenden Serverimplementierungen werden von ASP.NET Core bereitgestellt:</span><span class="sxs-lookup"><span data-stu-id="62540-314">ASP.NET Core provides the following server implementations:</span></span>

* <span data-ttu-id="62540-315">*Kestrel* ist ein plattformübergreifender Webserver.</span><span class="sxs-lookup"><span data-stu-id="62540-315">*Kestrel* is a cross-platform web server.</span></span> <span data-ttu-id="62540-316">Kestrel wird häufig in einer Reverseproxykonfiguration unter Verwendung von [IIS](https://www.iis.net/) ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="62540-316">Kestrel is often run in a reverse proxy configuration using [IIS](https://www.iis.net/).</span></span> <span data-ttu-id="62540-317">Kestrel kann als öffentlich zugänglicher Edgeserver ausgeführt werden, der direkt mit dem Internet verbunden ist.</span><span class="sxs-lookup"><span data-stu-id="62540-317">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span>
* <span data-ttu-id="62540-318">Der *IIS-HTTP-Server* ist ein Server für Windows, der IIS verwendet.</span><span class="sxs-lookup"><span data-stu-id="62540-318">*IIS HTTP Server* is a server for windows that uses IIS.</span></span> <span data-ttu-id="62540-319">Mit diesem Server werden die ASP.NET Core-App und IIS im gleichen Prozess ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="62540-319">With this server, the ASP.NET Core app and IIS run in the same process.</span></span>
* <span data-ttu-id="62540-320">*HTTP.sys* ist ein Server für Windows, der nicht mit IIS verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="62540-320">*HTTP.sys* is a server for Windows that isn't used with IIS.</span></span>

# <a name="macos"></a>[<span data-ttu-id="62540-321">macOS</span><span class="sxs-lookup"><span data-stu-id="62540-321">macOS</span></span>](#tab/macos)

<span data-ttu-id="62540-322">ASP.NET Core bietet die plattformübergreifende Serverimplementierung von *Kestrel*.</span><span class="sxs-lookup"><span data-stu-id="62540-322">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="62540-323">Kestrel kann als öffentlich zugänglicher Edgeserver ausgeführt werden, der direkt mit dem Internet verbunden ist.</span><span class="sxs-lookup"><span data-stu-id="62540-323">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="62540-324">Kestrel wird häufig in einer Reverseproxykonfiguration unter Verwendung von [Nginx](https://nginx.org) oder [Apache](https://httpd.apache.org/) ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="62540-324">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

# <a name="linux"></a>[<span data-ttu-id="62540-325">Linux</span><span class="sxs-lookup"><span data-stu-id="62540-325">Linux</span></span>](#tab/linux)

<span data-ttu-id="62540-326">ASP.NET Core bietet die plattformübergreifende Serverimplementierung von *Kestrel*.</span><span class="sxs-lookup"><span data-stu-id="62540-326">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="62540-327">Kestrel kann als öffentlich zugänglicher Edgeserver ausgeführt werden, der direkt mit dem Internet verbunden ist.</span><span class="sxs-lookup"><span data-stu-id="62540-327">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="62540-328">Kestrel wird häufig in einer Reverseproxykonfiguration unter Verwendung von [Nginx](https://nginx.org) oder [Apache](https://httpd.apache.org/) ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="62540-328">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

---

::: moniker-end

::: moniker range="< aspnetcore-2.2"

# <a name="windows"></a>[<span data-ttu-id="62540-329">Windows</span><span class="sxs-lookup"><span data-stu-id="62540-329">Windows</span></span>](#tab/windows)

<span data-ttu-id="62540-330">Die folgenden Serverimplementierungen werden von ASP.NET Core bereitgestellt:</span><span class="sxs-lookup"><span data-stu-id="62540-330">ASP.NET Core provides the following server implementations:</span></span>

* <span data-ttu-id="62540-331">*Kestrel* ist ein plattformübergreifender Webserver.</span><span class="sxs-lookup"><span data-stu-id="62540-331">*Kestrel* is a cross-platform web server.</span></span> <span data-ttu-id="62540-332">Kestrel wird häufig in einer Reverseproxykonfiguration unter Verwendung von [IIS](https://www.iis.net/) ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="62540-332">Kestrel is often run in a reverse proxy configuration using [IIS](https://www.iis.net/).</span></span> <span data-ttu-id="62540-333">Kestrel kann als öffentlich zugänglicher Edgeserver ausgeführt werden, der direkt mit dem Internet verbunden ist.</span><span class="sxs-lookup"><span data-stu-id="62540-333">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span>
* <span data-ttu-id="62540-334">*HTTP.sys* ist ein Server für Windows, der nicht mit IIS verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="62540-334">*HTTP.sys* is a server for Windows that isn't used with IIS.</span></span>

# <a name="macos"></a>[<span data-ttu-id="62540-335">macOS</span><span class="sxs-lookup"><span data-stu-id="62540-335">macOS</span></span>](#tab/macos)

<span data-ttu-id="62540-336">ASP.NET Core bietet die plattformübergreifende Serverimplementierung von *Kestrel*.</span><span class="sxs-lookup"><span data-stu-id="62540-336">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="62540-337">Kestrel kann als öffentlich zugänglicher Edgeserver ausgeführt werden, der direkt mit dem Internet verbunden ist.</span><span class="sxs-lookup"><span data-stu-id="62540-337">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="62540-338">Kestrel wird häufig in einer Reverseproxykonfiguration unter Verwendung von [Nginx](https://nginx.org) oder [Apache](https://httpd.apache.org/) ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="62540-338">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

# <a name="linux"></a>[<span data-ttu-id="62540-339">Linux</span><span class="sxs-lookup"><span data-stu-id="62540-339">Linux</span></span>](#tab/linux)

<span data-ttu-id="62540-340">ASP.NET Core bietet die plattformübergreifende Serverimplementierung von *Kestrel*.</span><span class="sxs-lookup"><span data-stu-id="62540-340">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="62540-341">Kestrel kann als öffentlich zugänglicher Edgeserver ausgeführt werden, der direkt mit dem Internet verbunden ist.</span><span class="sxs-lookup"><span data-stu-id="62540-341">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="62540-342">Kestrel wird häufig in einer Reverseproxykonfiguration unter Verwendung von [Nginx](https://nginx.org) oder [Apache](https://httpd.apache.org/) ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="62540-342">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

---

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="62540-343">Weitere Informationen finden Sie unter <xref:fundamentals/servers/index>.</span><span class="sxs-lookup"><span data-stu-id="62540-343">For more information, see <xref:fundamentals/servers/index>.</span></span>

## <a name="configuration"></a><span data-ttu-id="62540-344">Konfiguration</span><span class="sxs-lookup"><span data-stu-id="62540-344">Configuration</span></span>

<span data-ttu-id="62540-345">ASP.NET Core bietet ein Konfigurationsframework, das Einstellungen als Name/Wert-Paare aus einer geordneten Menge von Konfigurationsanbietern abruft.</span><span class="sxs-lookup"><span data-stu-id="62540-345">ASP.NET Core provides a configuration framework that gets settings as name-value pairs from an ordered set of configuration providers.</span></span> <span data-ttu-id="62540-346">Integrierte Konfigurationsanbieter gibt es für zahlreiche Quellen wie *.json*-Dateien, *.xml*-Dateien, Umgebungsvariablen und Befehlszeilenargumente.</span><span class="sxs-lookup"><span data-stu-id="62540-346">There are built-in configuration providers for a variety of sources, such as *.json* files, *.xml* files, environment variables, and command-line arguments.</span></span> <span data-ttu-id="62540-347">Sie können auch benutzerdefinierte Konfigurationsanbieter schreiben.</span><span class="sxs-lookup"><span data-stu-id="62540-347">You can also write custom configuration providers.</span></span>

<span data-ttu-id="62540-348">Zum Beispiel könnten Sie angeben, dass die Konfiguration aus *appsettings.json* und Umgebungsvariablen stammt.</span><span class="sxs-lookup"><span data-stu-id="62540-348">For example, you could specify that configuration comes from *appsettings.json* and environment variables.</span></span> <span data-ttu-id="62540-349">Wenn dann der *ConnectionString*-Wert angefordert wird, sucht das Framework zuerst in der *appsettings.json* -Datei.</span><span class="sxs-lookup"><span data-stu-id="62540-349">Then when the value of *ConnectionString* is requested, the framework looks first in the *appsettings.json* file.</span></span> <span data-ttu-id="62540-350">Wenn der Wert sowohl dort als auch in einer Umgebungsvariablen gefunden wird, hat der Wert aus der Umgebungsvariablen Vorrang.</span><span class="sxs-lookup"><span data-stu-id="62540-350">If the value is found there but also in an environment variable, the value from the environment variable would take precedence.</span></span>

<span data-ttu-id="62540-351">Zum Verwalten von vertraulichen Konfigurationsdaten wie Passwörtern bietet ASP.NET Core ein [Geheimnisverwaltungstool (Secret Manager)](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="62540-351">For managing confidential configuration data such as passwords, .NET Core provides a [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="62540-352">Für Produktionsgeheimnisse empfehlen wir [Azure Key Vault](xref:security/key-vault-configuration).</span><span class="sxs-lookup"><span data-stu-id="62540-352">For production secrets, we recommend [Azure Key Vault](xref:security/key-vault-configuration).</span></span>

<span data-ttu-id="62540-353">Weitere Informationen finden Sie unter <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="62540-353">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="options"></a><span data-ttu-id="62540-354">Optionen</span><span class="sxs-lookup"><span data-stu-id="62540-354">Options</span></span>

<span data-ttu-id="62540-355">Wo möglich, folgt ASP.NET Core dem *Optionsmuster* zum Speichern und Abrufen von Konfigurationswerten.</span><span class="sxs-lookup"><span data-stu-id="62540-355">Where possible, ASP.NET Core follows the *options pattern* for storing and retrieving configuration values.</span></span> <span data-ttu-id="62540-356">Das Optionsmuster verwendet Klassen, um Gruppen von zusammengehörigen Einstellungen darzustellen.</span><span class="sxs-lookup"><span data-stu-id="62540-356">The options pattern uses classes to represent groups of related settings.</span></span>

<span data-ttu-id="62540-357">Im folgenden Codebeispiel werden WebSockets-Optionen festgelegt:</span><span class="sxs-lookup"><span data-stu-id="62540-357">For example, the following code sets WebSockets options:</span></span>

[!code-csharp[](index/samples_snapshot/2.x/UseWebSockets.cs)]

<span data-ttu-id="62540-358">Weitere Informationen finden Sie unter <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="62540-358">For more information, see <xref:fundamentals/configuration/options>.</span></span>

## <a name="environments"></a><span data-ttu-id="62540-359">Umgebungen</span><span class="sxs-lookup"><span data-stu-id="62540-359">Environments</span></span>

<span data-ttu-id="62540-360">Ausführungsumgebungen wie *Entwicklung*, *Staging* und *Produktion* sind in ASP.NET Core von besonderer Bedeutung.</span><span class="sxs-lookup"><span data-stu-id="62540-360">Execution environments, such as *Development*, *Staging*, and *Production*, are a first-class notion in ASP.NET Core.</span></span> <span data-ttu-id="62540-361">Um die Umgebung anzugeben, in der eine App ausgeführt wird, stellen Sie die `ASPNETCORE_ENVIRONMENT`-Umgebungsvariable ein.</span><span class="sxs-lookup"><span data-stu-id="62540-361">You can specify the environment an app is running in by setting the `ASPNETCORE_ENVIRONMENT` environment variable.</span></span> <span data-ttu-id="62540-362">ASP.NET Core liest diese Umgebungsvariable beim Start der App und speichert den Wert in einer `IHostingEnvironment`-Implementierung.</span><span class="sxs-lookup"><span data-stu-id="62540-362">ASP.NET Core reads that environment variable at app startup and stores the value in an `IHostingEnvironment` implementation.</span></span> <span data-ttu-id="62540-363">Das Umgebungsobjekt ist in der gesamten App mithilfe der Dependency Injection verfügbar.</span><span class="sxs-lookup"><span data-stu-id="62540-363">The environment object is available anywhere in the app via DI.</span></span>

<span data-ttu-id="62540-364">Im folgenden Codebeispiel aus der `Startup`-Klasse wird die App so konfiguriert, dass detaillierte Fehlerinformationen nur bereitgestellt werden, wenn die App in der Entwicklung ausgeführt wird:</span><span class="sxs-lookup"><span data-stu-id="62540-364">The following sample code from the `Startup` class configures the app to provide detailed error information only when it runs in development:</span></span>

[!code-csharp[](index/samples_snapshot/2.x/StartupConfigure.cs?highlight=3-6)]

<span data-ttu-id="62540-365">Weitere Informationen finden Sie unter <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="62540-365">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="logging"></a><span data-ttu-id="62540-366">Protokollierung</span><span class="sxs-lookup"><span data-stu-id="62540-366">Logging</span></span>

<span data-ttu-id="62540-367">ASP.NET Core unterstützt eine Protokollierungs-API, die mit einer Vielzahl von integrierten Protokollierungsanbietern und Drittanbieter-Protokollierungslösungen zusammenarbeitet.</span><span class="sxs-lookup"><span data-stu-id="62540-367">ASP.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="62540-368">Zu den verfügbaren Anbietern gehören folgende:</span><span class="sxs-lookup"><span data-stu-id="62540-368">Available providers include the following:</span></span>

* <span data-ttu-id="62540-369">Konsole</span><span class="sxs-lookup"><span data-stu-id="62540-369">Console</span></span>
* <span data-ttu-id="62540-370">Debug</span><span class="sxs-lookup"><span data-stu-id="62540-370">Debug</span></span>
* <span data-ttu-id="62540-371">Ereignisablaufverfolgung unter Windows</span><span class="sxs-lookup"><span data-stu-id="62540-371">Event Tracing on Windows</span></span>
* <span data-ttu-id="62540-372">Windows-Ereignisprotokoll</span><span class="sxs-lookup"><span data-stu-id="62540-372">Windows Event Log</span></span>
* <span data-ttu-id="62540-373">TraceSource</span><span class="sxs-lookup"><span data-stu-id="62540-373">TraceSource</span></span>
* <span data-ttu-id="62540-374">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="62540-374">Azure App Service</span></span>
* <span data-ttu-id="62540-375">Azure Application Insights</span><span class="sxs-lookup"><span data-stu-id="62540-375">Azure Application Insights</span></span>

<span data-ttu-id="62540-376">Schreiben Sie Protokolle aus jeder Stelle im Code einer App, indem Sie ein `ILogger`-Objekt aus Dependency Injection abrufen und Protokollmethoden aufrufen.</span><span class="sxs-lookup"><span data-stu-id="62540-376">Write logs from anywhere in an app's code by getting an `ILogger` object from DI and calling log methods.</span></span>

<span data-ttu-id="62540-377">Hier ist ein Beispielcode, in dem ein `ILogger`-Objekt mit Konstruktorinjektion und den hervorgehobenen Aufrufen der Protokollierungsmethode verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="62540-377">Here's sample code that uses an `ILogger` object, with constructor injection and the logging method calls highlighted.</span></span>

[!code-csharp[](index/samples_snapshot/2.x/TodoController.cs?highlight=5,13,17)]

<span data-ttu-id="62540-378">Mithilfe der `ILogger`-Schnittstelle können Sie eine beliebige Anzahl von Feldern an den Protokollierungsanbieter übergeben.</span><span class="sxs-lookup"><span data-stu-id="62540-378">The `ILogger` interface lets you pass any number of fields to the logging provider.</span></span> <span data-ttu-id="62540-379">Die Felder werden häufig dazu verwendet, eine Meldungszeichenfolge zu erstellen, der Anbieter kann sie aber auch als einzelne Felder an einen Datenspeicher senden.</span><span class="sxs-lookup"><span data-stu-id="62540-379">The fields are commonly used to construct a message string, but the provider can also send them as separate fields to a data store.</span></span> <span data-ttu-id="62540-380">Dieses Funktion ermöglicht Protokollierungsanbietern das Implementieren von [semantischer Protokollierung, auch bezeichnet als strukturierte Protokollierung](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span><span class="sxs-lookup"><span data-stu-id="62540-380">This feature makes it possible for logging providers to implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="62540-381">Weitere Informationen finden Sie unter <xref:fundamentals/logging/index>.</span><span class="sxs-lookup"><span data-stu-id="62540-381">For more information, see <xref:fundamentals/logging/index>.</span></span>

## <a name="routing"></a><span data-ttu-id="62540-382">Routing</span><span class="sxs-lookup"><span data-stu-id="62540-382">Routing</span></span>

<span data-ttu-id="62540-383">Eine *Route* ist ein URL-Muster, das einem Handler zugeordnet ist.</span><span class="sxs-lookup"><span data-stu-id="62540-383">A *route* is a URL pattern that is mapped to a handler.</span></span> <span data-ttu-id="62540-384">Der Handler ist normalerweise eine Razor-Seite, eine Aktionsmethode in einem MVC-Controller oder einer Middleware.</span><span class="sxs-lookup"><span data-stu-id="62540-384">The handler is typically a Razor page, an action method in an MVC controller, or a middleware.</span></span> <span data-ttu-id="62540-385">Mit ASP.NET Core-Routing können Sie steuern, welche URLs von Ihrer App verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="62540-385">ASP.NET Core routing gives you control over the URLs used by your app.</span></span>

<span data-ttu-id="62540-386">Weitere Informationen finden Sie unter <xref:fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="62540-386">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="error-handling"></a><span data-ttu-id="62540-387">Fehlerbehandlung</span><span class="sxs-lookup"><span data-stu-id="62540-387">Error handling</span></span>

<span data-ttu-id="62540-388">ASP.NET Core verfügt über integrierte Funktionen zur Fehlerbehandlung wie beispielsweise:</span><span class="sxs-lookup"><span data-stu-id="62540-388">ASP.NET Core has built-in features for handling errors, such as:</span></span>

* <span data-ttu-id="62540-389">eine Seite mit Ausnahmen für Entwickler</span><span class="sxs-lookup"><span data-stu-id="62540-389">A developer exception page</span></span>
* <span data-ttu-id="62540-390">benutzerdefinierte Fehlerseiten</span><span class="sxs-lookup"><span data-stu-id="62540-390">Custom error pages</span></span>
* <span data-ttu-id="62540-391">statische Statuscodeseiten</span><span class="sxs-lookup"><span data-stu-id="62540-391">Static status code pages</span></span>
* <span data-ttu-id="62540-392">Fehlerbehandlung während des Starts</span><span class="sxs-lookup"><span data-stu-id="62540-392">Startup exception handling</span></span>

<span data-ttu-id="62540-393">Weitere Informationen finden Sie unter <xref:fundamentals/error-handling>.</span><span class="sxs-lookup"><span data-stu-id="62540-393">For more information, see <xref:fundamentals/error-handling>.</span></span>

## <a name="make-http-requests"></a><span data-ttu-id="62540-394">Übermitteln von HTTP-Anforderungen</span><span class="sxs-lookup"><span data-stu-id="62540-394">Make HTTP requests</span></span>

<span data-ttu-id="62540-395">Eine Implementierung von `IHttpClientFactory` ist verfügbar zum Erstellen von `HttpClient`-Instanzen.</span><span class="sxs-lookup"><span data-stu-id="62540-395">An implementation of `IHttpClientFactory` is available for creating `HttpClient` instances.</span></span> <span data-ttu-id="62540-396">Die Factory:</span><span class="sxs-lookup"><span data-stu-id="62540-396">The factory:</span></span>

* <span data-ttu-id="62540-397">Ein zentraler Ort für das Benennen und Konfigurieren logischer `HttpClient`-Instanzen wird damit geboten.</span><span class="sxs-lookup"><span data-stu-id="62540-397">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="62540-398">Zum Beispiel kann ein *github*-Client für den Zugriff auf GitHub registriert und konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="62540-398">For example, a *github* client can be registered and configured to access GitHub.</span></span> <span data-ttu-id="62540-399">Ein Standard-Client kann für andere Zwecke registriert werden.</span><span class="sxs-lookup"><span data-stu-id="62540-399">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="62540-400">Unterstützt die Registrierung und Verkettung von mehreren delegierenden Handlern, um eine Pipeline für die Middleware für ausgehende Anforderungen zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="62540-400">Supports registration and chaining of multiple delegating handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="62540-401">Dieses Muster ähnelt der eingehenden Pipeline für Middleware in ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="62540-401">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="62540-402">Das Muster bietet einen Mechanismus zum Verwalten von übergreifenden Belangen bezüglich HTTP-Anforderungen, einschließlich der Zwischenspeicherung, Fehlerbehandlung, Serialisierung und Protokollierung.</span><span class="sxs-lookup"><span data-stu-id="62540-402">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>
* <span data-ttu-id="62540-403">Integrierbar in *Polly*, eine beliebte Drittanbieter-Bibliothek zur Behandlung vorübergehender Fehler.</span><span class="sxs-lookup"><span data-stu-id="62540-403">Integrates with *Polly*, a popular third-party library for transient fault handling.</span></span>
* <span data-ttu-id="62540-404">Das Pooling und die Lebensdauer von zugrunde liegenden `HttpClientHandler`-Instanzen werden verwaltet, um gängige DNS-Probleme zu vermeiden, die bei der manuellen Verwaltung der `HttpClient`-Lebensdauer auftreten.</span><span class="sxs-lookup"><span data-stu-id="62540-404">Manages the pooling and lifetime of underlying `HttpClientHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="62540-405">Eine konfigurierbare Protokollierungsfunktion wird (über `ILogger`) für alle Anforderungen hinzugefügt, die über Clients gesendet werden, die von der Factory erstellt wurden.</span><span class="sxs-lookup"><span data-stu-id="62540-405">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="62540-406">Weitere Informationen finden Sie unter <xref:fundamentals/http-requests>.</span><span class="sxs-lookup"><span data-stu-id="62540-406">For more information, see <xref:fundamentals/http-requests>.</span></span>

## <a name="content-root"></a><span data-ttu-id="62540-407">Inhaltsstammverzeichnis</span><span class="sxs-lookup"><span data-stu-id="62540-407">Content root</span></span>

<span data-ttu-id="62540-408">Der Inhaltsstamm ist der Basispfad zu Folgendem:</span><span class="sxs-lookup"><span data-stu-id="62540-408">The content root is the base path to the:</span></span>

* <span data-ttu-id="62540-409">Der ausführbaren Datei ( *.exe*), die die App hostet.</span><span class="sxs-lookup"><span data-stu-id="62540-409">Executable hosting the app (*.exe*).</span></span>
* <span data-ttu-id="62540-410">Kompilierten Assemblys, die die App bilden ( *.dll*).</span><span class="sxs-lookup"><span data-stu-id="62540-410">Compiled assemblies that make up the app (*.dll*).</span></span>
* <span data-ttu-id="62540-411">Inhaltsdateien ohne Code, die von der App verwendet werden, wie z. B.:</span><span class="sxs-lookup"><span data-stu-id="62540-411">Non-code content files used by the app, such as:</span></span>
  * <span data-ttu-id="62540-412">Razor-Dateien ( *.cshtml*, *.razor*)</span><span class="sxs-lookup"><span data-stu-id="62540-412">Razor files (*.cshtml*, *.razor*)</span></span>
  * <span data-ttu-id="62540-413">Konfigurationsdateien ( *.json*, *.xml*)</span><span class="sxs-lookup"><span data-stu-id="62540-413">Configuration files (*.json*, *.xml*)</span></span>
  * <span data-ttu-id="62540-414">Datendateien ( *.db*)</span><span class="sxs-lookup"><span data-stu-id="62540-414">Data files (*.db*)</span></span>
* <span data-ttu-id="62540-415">[Webstamm](#web-root), in der Regel der veröffentlichte Ordner *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="62540-415">[Web root](#web-root), typically the published *wwwroot* folder.</span></span>

<span data-ttu-id="62540-416">Entwicklungsphase:</span><span class="sxs-lookup"><span data-stu-id="62540-416">During development:</span></span>

* <span data-ttu-id="62540-417">Der Inhaltsstamm ist standardmäßig auf das Stammverzeichnis des Projekts festgelegt.</span><span class="sxs-lookup"><span data-stu-id="62540-417">The content root defaults to the project's root directory.</span></span>
* <span data-ttu-id="62540-418">Das Stammverzeichnis des Projekts dient zum Erstellen von Folgendem:</span><span class="sxs-lookup"><span data-stu-id="62540-418">The project's root directory is used to create the:</span></span>
  * <span data-ttu-id="62540-419">Pfad zu den Inhaltsdateien ohne Code der App im Stammverzeichnis des Projekts.</span><span class="sxs-lookup"><span data-stu-id="62540-419">Path to the app's non-code content files in the project's root directory.</span></span>
  * <span data-ttu-id="62540-420">[Webstamm](#web-root), in der Regel der Ordner *wwwroot* im Stammverzeichnis des Projekts.</span><span class="sxs-lookup"><span data-stu-id="62540-420">[Web root](#web-root), typically the *wwwroot* folder in the project's root directory.</span></span>

<span data-ttu-id="62540-421">Ein alternativer Inhaltsstammpfad kann beim [Erstellen des Hosts](#host) angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="62540-421">An alternative content root path can be specified when [building the host](#host).</span></span> <span data-ttu-id="62540-422">Weitere Informationen finden Sie unter <xref:fundamentals/host/web-host#content-root>.</span><span class="sxs-lookup"><span data-stu-id="62540-422">For more information, see <xref:fundamentals/host/web-host#content-root>.</span></span>

## <a name="web-root"></a><span data-ttu-id="62540-423">Webstammverzeichnis</span><span class="sxs-lookup"><span data-stu-id="62540-423">Web root</span></span>

<span data-ttu-id="62540-424">Der Webstamm ist der Basispfad zu öffentlichen, statischen Ressourcendateien ohne Code, wie z. B.:</span><span class="sxs-lookup"><span data-stu-id="62540-424">The web root is the base path to public, non-code, static resource files, such as:</span></span>

* <span data-ttu-id="62540-425">Stylesheets ( *.css*)</span><span class="sxs-lookup"><span data-stu-id="62540-425">Stylesheets (*.css*)</span></span>
* <span data-ttu-id="62540-426">JavaScript ( *.js*)</span><span class="sxs-lookup"><span data-stu-id="62540-426">JavaScript (*.js*)</span></span>
* <span data-ttu-id="62540-427">Bilder ( *.png*, *.jpg*)</span><span class="sxs-lookup"><span data-stu-id="62540-427">Images (*.png*, *.jpg*)</span></span>

<span data-ttu-id="62540-428">Statische Dateien werden standardmäßig nur aus dem Webstammverzeichnis (samt Unterverzeichnissen) bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="62540-428">Static files are only served by default from the web root directory (and sub-directories).</span></span>

<span data-ttu-id="62540-429">Der Webstammpfad ist standardmäßig auf *{Inhaltsstamm}/wwwroot* festgelegt, doch beim [Erstellen des Hosts](#host) kann ein anderer Webstamm angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="62540-429">The web root path defaults to *{content root}/wwwroot*, but a different web root can be specified when [building the host](#host).</span></span> <span data-ttu-id="62540-430">Weitere Informationen finden Sie unter [Webstamm](xref:fundamentals/host/web-host#web-root).</span><span class="sxs-lookup"><span data-stu-id="62540-430">For more information, see [Web root](xref:fundamentals/host/web-host#web-root).</span></span>

<span data-ttu-id="62540-431">Verhindern Sie das Veröffentlichen von Dateien in *wwwroot* mit dem [\<Content>-Projektelement](/visualstudio/msbuild/common-msbuild-project-items#content) in der Projektdatei.</span><span class="sxs-lookup"><span data-stu-id="62540-431">Prevent publishing files in *wwwroot* with the [\<Content> project item](/visualstudio/msbuild/common-msbuild-project-items#content) in the project file.</span></span> <span data-ttu-id="62540-432">Im folgenden Beispiel wird verhindert, dass Inhalte im *wwwroot/local*-Verzeichnis und in Unterverzeichnisse veröffentlicht werden:</span><span class="sxs-lookup"><span data-stu-id="62540-432">The following example prevents publishing content in the *wwwroot/local* directory and sub-directories:</span></span>

```xml
<ItemGroup>
  <Content Update="wwwroot\local\**\*.*" CopyToPublishDirectory="Never" />
</ItemGroup>
```

<span data-ttu-id="62540-433">In Razor-Dateien ( *.cshtml*) zeigen Tilde und Schrägstrich (`~/`) auf den Webstamm.</span><span class="sxs-lookup"><span data-stu-id="62540-433">In Razor (*.cshtml*) files, the tilde-slash (`~/`) points to the web root.</span></span> <span data-ttu-id="62540-434">Ein mit `~/` beginnender Pfad wird als *virtueller Pfad* bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="62540-434">A path beginning with `~/` is referred to as a *virtual path*.</span></span>

<span data-ttu-id="62540-435">Weitere Informationen finden Sie unter <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="62540-435">For more information, see <xref:fundamentals/static-files>.</span></span>

::: moniker-end
