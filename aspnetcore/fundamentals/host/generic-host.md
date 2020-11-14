---
title: Generischer .NET-Host in ASP.NET Core
author: rick-anderson
description: Verwenden Sie den generischen .NET Core-Host in den Apps von ASP.NET Core.  Der generische Host ist verantwortlich für das Starten der App und das Verwalten der Lebensdauer.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 4/17/2020
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
uid: fundamentals/host/generic-host
ms.openlocfilehash: 3e44932c302713132a37534b97fffdd91acce2c7
ms.sourcegitcommit: d64bf0cbe763beda22a7728c7f10d07fc5e19262
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2020
ms.locfileid: "93234555"
---
# <a name="net-generic-host-in-aspnet-core"></a><span data-ttu-id="d8f60-104">Generischer .NET-Host in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d8f60-104">.NET Generic Host in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="d8f60-105">Die ASP.NET Core-Vorlagen erstellen einen generischen .NET Core-Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>).</span><span class="sxs-lookup"><span data-stu-id="d8f60-105">The ASP.NET Core templates create a .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>).</span></span>

<span data-ttu-id="d8f60-106">Dieses Thema enthält Informationen zur Verwendung des generischen .NET-Hosts in ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d8f60-106">This topic provides information on using .NET Generic Host in ASP.NET Core.</span></span> <span data-ttu-id="d8f60-107">Informationen zur Verwendung des generischen .NET-Hosts in Konsolen-Apps finden Sie unter [Generischer .NET-Host](/dotnet/core/extensions/generic-host).</span><span class="sxs-lookup"><span data-stu-id="d8f60-107">For information on using .NET Generic Host in console apps, see [.NET Generic Host](/dotnet/core/extensions/generic-host).</span></span>

## <a name="host-definition"></a><span data-ttu-id="d8f60-108">Hostdefinition</span><span class="sxs-lookup"><span data-stu-id="d8f60-108">Host definition</span></span>

<span data-ttu-id="d8f60-109">Der *Host* ist ein Objekt, das alle Ressourcen der App kapselt, z. B.:</span><span class="sxs-lookup"><span data-stu-id="d8f60-109">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="d8f60-110">Abhängigkeitsinjektion</span><span class="sxs-lookup"><span data-stu-id="d8f60-110">Dependency injection (DI)</span></span>
* <span data-ttu-id="d8f60-111">Protokollierung</span><span class="sxs-lookup"><span data-stu-id="d8f60-111">Logging</span></span>
* <span data-ttu-id="d8f60-112">Konfiguration</span><span class="sxs-lookup"><span data-stu-id="d8f60-112">Configuration</span></span>
* <span data-ttu-id="d8f60-113">`IHostedService`-Implementierungen</span><span class="sxs-lookup"><span data-stu-id="d8f60-113">`IHostedService` implementations</span></span>

<span data-ttu-id="d8f60-114">Beim Starten eines Hosts wird <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> für jede Implementierung von <xref:Microsoft.Extensions.Hosting.IHostedService> aufgerufen, die in der Sammlung gehosteter Dienste des Dienstcontainers registriert ist.</span><span class="sxs-lookup"><span data-stu-id="d8f60-114">When a host starts, it calls <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> registered in the service container's collection of hosted services.</span></span> <span data-ttu-id="d8f60-115">In einer Web-App ist eine der `IHostedService`-Implementierungen ein Webdienst, der eine [HTTP-Serverimplementierung](xref:fundamentals/index#servers) startet.</span><span class="sxs-lookup"><span data-stu-id="d8f60-115">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="d8f60-116">Der wichtigste Grund für das Einschließen aller unabhängigen Ressourcen der App in einem Objekt ist die Verwaltung der Lebensdauer: steuern von Start und ordnungsgemäßem Herunterfahren der App.</span><span class="sxs-lookup"><span data-stu-id="d8f60-116">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="d8f60-117">Einrichten eines Hosts</span><span class="sxs-lookup"><span data-stu-id="d8f60-117">Set up a host</span></span>

<span data-ttu-id="d8f60-118">Der Host wird in der Regel per Code in der `Program`-Klasse konfiguriert, erstellt und ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="d8f60-118">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="d8f60-119">Die `Main`-Methode:</span><span class="sxs-lookup"><span data-stu-id="d8f60-119">The `Main` method:</span></span>

* <span data-ttu-id="d8f60-120">Ruft eine `CreateHostBuilder`-Methode zum Erstellen und Konfigurieren eines Generatorobjekts auf.</span><span class="sxs-lookup"><span data-stu-id="d8f60-120">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="d8f60-121">Ruft `Build`- und `Run`-Methoden für das Generatorobjekt auf.</span><span class="sxs-lookup"><span data-stu-id="d8f60-121">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="d8f60-122">Die ASP.NET Core-Webvorlagen generieren den folgenden Code zum Erstellen eines Hosts:</span><span class="sxs-lookup"><span data-stu-id="d8f60-122">The ASP.NET Core web templates generate the following code to create a host:</span></span>

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
                webBuilder.UseStartup<Startup>();
            });
}
```

<span data-ttu-id="d8f60-123">Der folgende Code erstellt eine Nicht-HTTP-Workload mit einer dem DI-Container hinzugefügten `IHostedService`-Implementierung.</span><span class="sxs-lookup"><span data-stu-id="d8f60-123">The following code creates a non-HTTP workload with an `IHostedService` implementation added to the DI container.</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureServices((hostContext, services) =>
            {
               services.AddHostedService<Worker>();
            });
}
```

<span data-ttu-id="d8f60-124">Für einen HTTP-Workload ist die `Main`-Methode die gleiche, `CreateHostBuilder` ruft jedoch `ConfigureWebHostDefaults` auf:</span><span class="sxs-lookup"><span data-stu-id="d8f60-124">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="d8f60-125">Ändern Sie nicht den Namen bzw. die Signatur der `CreateHostBuilder`-Methode, wenn die App Entity Framework Core verwendet.</span><span class="sxs-lookup"><span data-stu-id="d8f60-125">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="d8f60-126">Die [Entity Framework Core-Tools](/ef/core/miscellaneous/cli/) erwarten eine `CreateHostBuilder`-Methode, die den Host konfiguriert, ohne die App auszuführen.</span><span class="sxs-lookup"><span data-stu-id="d8f60-126">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="d8f60-127">Weitere Informationen finden Sie unter [DbContext-Instanzerstellung zur Entwurfszeit](/ef/core/miscellaneous/cli/dbcontext-creation).</span><span class="sxs-lookup"><span data-stu-id="d8f60-127">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="d8f60-128">Standardeinstellungen für den Generator</span><span class="sxs-lookup"><span data-stu-id="d8f60-128">Default builder settings</span></span>

<span data-ttu-id="d8f60-129">Die <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>-Methode:</span><span class="sxs-lookup"><span data-stu-id="d8f60-129">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> method:</span></span>

* <span data-ttu-id="d8f60-130">Legt das [Inhaltsstammverzeichnis](xref:fundamentals/index#content-root) auf den Pfad fest, der von <xref:System.IO.Directory.GetCurrentDirectory*> zurückgegeben wird.</span><span class="sxs-lookup"><span data-stu-id="d8f60-130">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory*>.</span></span>
* <span data-ttu-id="d8f60-131">Lädt Hostkonfiguration aus:</span><span class="sxs-lookup"><span data-stu-id="d8f60-131">Loads host configuration from:</span></span>
  * <span data-ttu-id="d8f60-132">Umgebungsvariablen mit dem Präfix `DOTNET_`.</span><span class="sxs-lookup"><span data-stu-id="d8f60-132">Environment variables prefixed with `DOTNET_`.</span></span>
  * <span data-ttu-id="d8f60-133">Befehlszeilenargumenten</span><span class="sxs-lookup"><span data-stu-id="d8f60-133">Command-line arguments.</span></span>
* <span data-ttu-id="d8f60-134">Lädt App-Konfiguration aus:</span><span class="sxs-lookup"><span data-stu-id="d8f60-134">Loads app configuration from:</span></span>
  * <span data-ttu-id="d8f60-135">*appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="d8f60-135">*appsettings.json*.</span></span>
  * <span data-ttu-id="d8f60-136">*appsettings.{Environment}.json*</span><span class="sxs-lookup"><span data-stu-id="d8f60-136">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="d8f60-137">[Geheimnis-Manager](xref:security/app-secrets), wenn die App in der `Development`-Umgebung ausgeführt wird</span><span class="sxs-lookup"><span data-stu-id="d8f60-137">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="d8f60-138">Umgebungsvariablen.</span><span class="sxs-lookup"><span data-stu-id="d8f60-138">Environment variables.</span></span>
  * <span data-ttu-id="d8f60-139">Befehlszeilenargumenten</span><span class="sxs-lookup"><span data-stu-id="d8f60-139">Command-line arguments.</span></span>
* <span data-ttu-id="d8f60-140">Fügt die folgenden [Protokollierungsanbieter](xref:fundamentals/logging/index) hinzu:</span><span class="sxs-lookup"><span data-stu-id="d8f60-140">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="d8f60-141">Konsole</span><span class="sxs-lookup"><span data-stu-id="d8f60-141">Console</span></span>
  * <span data-ttu-id="d8f60-142">Debug</span><span class="sxs-lookup"><span data-stu-id="d8f60-142">Debug</span></span>
  * <span data-ttu-id="d8f60-143">EventSource</span><span class="sxs-lookup"><span data-stu-id="d8f60-143">EventSource</span></span>
  * <span data-ttu-id="d8f60-144">EventLog (nur bei Ausführung unter Windows)</span><span class="sxs-lookup"><span data-stu-id="d8f60-144">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="d8f60-145">Aktiviert [Bereichsvalidierung](xref:fundamentals/dependency-injection#scope-validation) und [Abhängigkeitsüberprüfung](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild), wenn es sich um eine Entwicklungsumgebung handelt.</span><span class="sxs-lookup"><span data-stu-id="d8f60-145">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="d8f60-146">Die <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*>-Methode:</span><span class="sxs-lookup"><span data-stu-id="d8f60-146">The <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> method:</span></span>

* <span data-ttu-id="d8f60-147">Lädt Hostkonfiguration aus Umgebungsvariablen mit dem Präfix `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="d8f60-147">Loads host configuration from environment variables prefixed with `ASPNETCORE_`.</span></span>
* <span data-ttu-id="d8f60-148">Legt den [Kestrel](xref:fundamentals/servers/kestrel)-Server als Webserver fest und konfiguriert ihn mithilfe der Hostkonfigurationsanbieter der App.</span><span class="sxs-lookup"><span data-stu-id="d8f60-148">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="d8f60-149">Die Standardoptionen des Kestrel-Servers finden Sie unter <xref:fundamentals/servers/kestrel#kestrel-options>.</span><span class="sxs-lookup"><span data-stu-id="d8f60-149">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="d8f60-150">Fügt [Middleware zum Filtern von Hosts](xref:fundamentals/servers/kestrel#host-filtering) hinzu.</span><span class="sxs-lookup"><span data-stu-id="d8f60-150">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="d8f60-151">Fügt [Middleware für weitergeleitete Header](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) hinzu, wenn `ASPNETCORE_FORWARDEDHEADERS_ENABLED` den Wert `true` aufweist.</span><span class="sxs-lookup"><span data-stu-id="d8f60-151">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span></span>
* <span data-ttu-id="d8f60-152">Ermöglicht die Integration von IIS.</span><span class="sxs-lookup"><span data-stu-id="d8f60-152">Enables IIS integration.</span></span> <span data-ttu-id="d8f60-153">Informationen zu den IIS-Standardoptionen finden Sie unter <xref:host-and-deploy/iis/index#iis-options>.</span><span class="sxs-lookup"><span data-stu-id="d8f60-153">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="d8f60-154">In den Abschnitten [Einstellungen für alle App-Typen](#settings-for-all-app-types) und [Einstellungen für Web-Apps](#settings-for-web-apps) weiter unten in diesem Artikel wird beschrieben, wie die Standardeinstellungen des Generators außer Kraft gesetzt werden.</span><span class="sxs-lookup"><span data-stu-id="d8f60-154">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="d8f60-155">Von Frameworks bereitgestellte Dienste</span><span class="sxs-lookup"><span data-stu-id="d8f60-155">Framework-provided services</span></span>

<span data-ttu-id="d8f60-156">Die folgenden Dienste werden automatisch registriert:</span><span class="sxs-lookup"><span data-stu-id="d8f60-156">The following services are registered automatically:</span></span>

* [<span data-ttu-id="d8f60-157">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="d8f60-157">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="d8f60-158">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="d8f60-158">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="d8f60-159">IHostEnvironment/IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="d8f60-159">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="d8f60-160">Weitere Informationen zu den vom Framework bereitgestellten Diensten finden Sie unter <xref:fundamentals/dependency-injection#framework-provided-services>.</span><span class="sxs-lookup"><span data-stu-id="d8f60-160">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="d8f60-161">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="d8f60-161">IHostApplicationLifetime</span></span>

<span data-ttu-id="d8f60-162">Fügt den <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime>-Dienst (vorher `IApplicationLifetime`-Dienst) in eine beliebige Klasse ein, um die Aktivitäten nach dem Starten und die Aufgaben für ordnungsgemäßes Herunterfahren zu verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="d8f60-162">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="d8f60-163">Bei drei der Eigenschaften der Schnittstelle handelt es sich um Abbruchtokens, die zum Registrieren von Ereignishandlermethoden zum Starten und Beenden von Apps verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="d8f60-163">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="d8f60-164">Die Benutzeroberfläche umfasst auch eine `StopApplication`-Methode.</span><span class="sxs-lookup"><span data-stu-id="d8f60-164">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="d8f60-165">Das folgende Beispiel zeigt eine `IHostedService`-Implementierung, die `IHostApplicationLifetime`-Ereignisse registriert:</span><span class="sxs-lookup"><span data-stu-id="d8f60-165">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="d8f60-166">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="d8f60-166">IHostLifetime</span></span>

<span data-ttu-id="d8f60-167">Die <xref:Microsoft.Extensions.Hosting.IHostLifetime>-Implementierung steuert, wann der Host gestartet und beendet wird.</span><span class="sxs-lookup"><span data-stu-id="d8f60-167">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="d8f60-168">Die zuletzt registrierte Implementierung wird verwendet.</span><span class="sxs-lookup"><span data-stu-id="d8f60-168">The last implementation registered is used.</span></span>

<span data-ttu-id="d8f60-169">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` ist die `IHostLifetime`-Standardimplementierung.</span><span class="sxs-lookup"><span data-stu-id="d8f60-169">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="d8f60-170">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="d8f60-170">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="d8f60-171">lauscht auf <kbd>STRG</kbd>+<kbd>C</kbd>/SIGINT oder SIGTERM und ruft <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> auf, um das Herunterfahren zu beginnen.</span><span class="sxs-lookup"><span data-stu-id="d8f60-171">Listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> to start the shutdown process.</span></span>
* <span data-ttu-id="d8f60-172">Hebt die Blockierung von Erweiterungen wie [RunAsync](#runasync) und [WaitForShutdownAsync](#waitforshutdownasync) auf.</span><span class="sxs-lookup"><span data-stu-id="d8f60-172">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="d8f60-173">IHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="d8f60-173">IHostEnvironment</span></span>

<span data-ttu-id="d8f60-174">Fügt den <xref:Microsoft.Extensions.Hosting.IHostEnvironment>-Dienst in eine Klasse ein, um Informationen über die folgenden Einstellungen abzurufen:</span><span class="sxs-lookup"><span data-stu-id="d8f60-174">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following settings:</span></span>

* [<span data-ttu-id="d8f60-175">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="d8f60-175">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="d8f60-176">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="d8f60-176">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="d8f60-177">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="d8f60-177">ContentRootPath</span></span>](#contentroot)

<span data-ttu-id="d8f60-178">Web-Apps implementieren die `IWebHostEnvironment`-Schnittstelle, die `IHostEnvironment` erbt und [WebRootPath](#webroot) hinzufügt.</span><span class="sxs-lookup"><span data-stu-id="d8f60-178">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="d8f60-179">Konfiguration des Hosts</span><span class="sxs-lookup"><span data-stu-id="d8f60-179">Host configuration</span></span>

<span data-ttu-id="d8f60-180">Die Hostkonfiguration wird für die Eigenschaften der <xref:Microsoft.Extensions.Hosting.IHostEnvironment>-Implementierung verwendet.</span><span class="sxs-lookup"><span data-stu-id="d8f60-180">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="d8f60-181">Sie ist über [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> verfügbar.</span><span class="sxs-lookup"><span data-stu-id="d8f60-181">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span></span> <span data-ttu-id="d8f60-182">Nach `ConfigureAppConfiguration` wird `HostBuilderContext.Configuration` durch die App-Konfiguration ersetzt.</span><span class="sxs-lookup"><span data-stu-id="d8f60-182">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="d8f60-183">Rufen Sie <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> für `IHostBuilder` auf, um die Hostkonfiguration hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="d8f60-183">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="d8f60-184">`ConfigureHostConfiguration` kann mehrfach mit additiven Ergebnissen aufgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="d8f60-184">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="d8f60-185">Der Host verwendet die Option, die zuletzt einen Wert für einen bestimmten Schlüssel festlegt.</span><span class="sxs-lookup"><span data-stu-id="d8f60-185">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="d8f60-186">Der Umgebungsvariablenanbieter mit dem Präfix `DOTNET_` und die Befehlszeilenargumente sind in `CreateDefaultBuilder` enthalten.</span><span class="sxs-lookup"><span data-stu-id="d8f60-186">The environment variable provider with prefix `DOTNET_` and command-line arguments are included by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="d8f60-187">Für Web-Apps wird der Umgebungsvariablenanbieter mit dem Präfix `ASPNETCORE_` hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="d8f60-187">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="d8f60-188">Das Präfix wird entfernt, wenn die Umgebungsvariablen gelesen werden.</span><span class="sxs-lookup"><span data-stu-id="d8f60-188">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="d8f60-189">Der Wert der Umgebungsvariable für `ASPNETCORE_ENVIRONMENT` wird z. B. der Hostkonfigurationswert für den `environment`-Schlüssel.</span><span class="sxs-lookup"><span data-stu-id="d8f60-189">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="d8f60-190">Im folgenden Beispiel wird eine Hostkonfiguration erstellt:</span><span class="sxs-lookup"><span data-stu-id="d8f60-190">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="d8f60-191">App-Konfiguration</span><span class="sxs-lookup"><span data-stu-id="d8f60-191">App configuration</span></span>

<span data-ttu-id="d8f60-192">Die App-Konfiguration wird durch Aufrufen von <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> für `IHostBuilder` erstellt.</span><span class="sxs-lookup"><span data-stu-id="d8f60-192">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="d8f60-193">`ConfigureAppConfiguration` kann mehrfach mit additiven Ergebnissen aufgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="d8f60-193">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="d8f60-194">Die App verwendet die Option, die zuletzt einen Wert für einen bestimmten Schlüssel festlegt.</span><span class="sxs-lookup"><span data-stu-id="d8f60-194">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="d8f60-195">Die von `ConfigureAppConfiguration` erstellte Konfiguration ist unter [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) verfügbar und kann für nachfolgende Vorgänge und als Dienst für die Abhängigkeitsinjektion verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="d8f60-195">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="d8f60-196">Die Hostkonfiguration wird ebenfalls der App-Konfiguration hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="d8f60-196">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="d8f60-197">Weitere Informationen finden Sie unter [Konfiguration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span><span class="sxs-lookup"><span data-stu-id="d8f60-197">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="d8f60-198">Einstellungen für alle App-Typen</span><span class="sxs-lookup"><span data-stu-id="d8f60-198">Settings for all app types</span></span>

<span data-ttu-id="d8f60-199">In diesem Abschnitt werden Hosteinstellungen aufgeführt, die sowohl für HTTP- als auch für Nicht-HTTP-Workloads gelten.</span><span class="sxs-lookup"><span data-stu-id="d8f60-199">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="d8f60-200">Standardmäßig können Umgebungsvariablen, die zur Konfiguration dieser Einstellungen verwendet werden, ein `DOTNET_`- oder `ASPNETCORE_`-Präfix haben.</span><span class="sxs-lookup"><span data-stu-id="d8f60-200">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span> <span data-ttu-id="d8f60-201">Weitere Informationen finden Sie im Abschnitt [Standardeinstellungen für den Generator](#default-builder-settings).</span><span class="sxs-lookup"><span data-stu-id="d8f60-201">For more information, see the [Default builder settings](#default-builder-settings) section.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="d8f60-202">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="d8f60-202">ApplicationName</span></span>

<span data-ttu-id="d8f60-203">Die Eigenschaft [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) wird von der Hostkonfiguration während der Hosterstellung festgelegt.</span><span class="sxs-lookup"><span data-stu-id="d8f60-203">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="d8f60-204">**Schlüssel** : `applicationName`</span><span class="sxs-lookup"><span data-stu-id="d8f60-204">**Key** : `applicationName`</span></span>  
<span data-ttu-id="d8f60-205">**Typ:** `string`</span><span class="sxs-lookup"><span data-stu-id="d8f60-205">**Type** : `string`</span></span>  
<span data-ttu-id="d8f60-206">**Standard** : Der Name der Assembly, die den Einstiegspunkt der App enthält.</span><span class="sxs-lookup"><span data-stu-id="d8f60-206">**Default** : The name of the assembly that contains the app's entry point.</span></span>  
<span data-ttu-id="d8f60-207">**Umgebungsvariable:** `<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="d8f60-207">**Environment variable** : `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="d8f60-208">Verwenden Sie die Umgebungsvariable, um diesen Wert festzulegen.</span><span class="sxs-lookup"><span data-stu-id="d8f60-208">To set this value, use the environment variable.</span></span> 

### <a name="contentroot"></a><span data-ttu-id="d8f60-209">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="d8f60-209">ContentRoot</span></span>

<span data-ttu-id="d8f60-210">Die Eigenschaft [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) bestimmt, wo der Host mit der Suche nach Inhaltsdateien beginnt.</span><span class="sxs-lookup"><span data-stu-id="d8f60-210">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="d8f60-211">Wenn der Pfad nicht vorhanden ist, kann der Host nicht gestartet werden.</span><span class="sxs-lookup"><span data-stu-id="d8f60-211">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="d8f60-212">**Schlüssel** : `contentRoot`</span><span class="sxs-lookup"><span data-stu-id="d8f60-212">**Key** : `contentRoot`</span></span>  
<span data-ttu-id="d8f60-213">**Typ:** `string`</span><span class="sxs-lookup"><span data-stu-id="d8f60-213">**Type** : `string`</span></span>  
<span data-ttu-id="d8f60-214">**Standard** : Der Ordner, in dem die App-Assembly gespeichert ist.</span><span class="sxs-lookup"><span data-stu-id="d8f60-214">**Default** : The folder where the app assembly resides.</span></span>  
<span data-ttu-id="d8f60-215">**Umgebungsvariable:** `<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="d8f60-215">**Environment variable** : `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="d8f60-216">Verwenden Sie die Umgebungsvariable, oder rufen Sie `UseContentRoot` für `IHostBuilder` auf, um diesen Wert festzulegen:</span><span class="sxs-lookup"><span data-stu-id="d8f60-216">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="d8f60-217">Weitere Informationen finden Sie unter:</span><span class="sxs-lookup"><span data-stu-id="d8f60-217">For more information, see:</span></span>

* [<span data-ttu-id="d8f60-218">Grundlagen: Inhaltsstammverzeichnis</span><span class="sxs-lookup"><span data-stu-id="d8f60-218">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="d8f60-219">WebRoot</span><span class="sxs-lookup"><span data-stu-id="d8f60-219">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="d8f60-220">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="d8f60-220">EnvironmentName</span></span>

<span data-ttu-id="d8f60-221">Die Eigenschaft [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) kann auf einen beliebigen Wert festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="d8f60-221">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="d8f60-222">Zu den durch Frameworks definierten Werten zählen `Development`, `Staging` und `Production`.</span><span class="sxs-lookup"><span data-stu-id="d8f60-222">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="d8f60-223">Die Groß-/Kleinschreibung wird bei Werten nicht beachtet.</span><span class="sxs-lookup"><span data-stu-id="d8f60-223">Values aren't case-sensitive.</span></span>

<span data-ttu-id="d8f60-224">**Schlüssel** : `environment`</span><span class="sxs-lookup"><span data-stu-id="d8f60-224">**Key** : `environment`</span></span>  
<span data-ttu-id="d8f60-225">**Typ:** `string`</span><span class="sxs-lookup"><span data-stu-id="d8f60-225">**Type** : `string`</span></span>  
<span data-ttu-id="d8f60-226">**Standard** : `Production`</span><span class="sxs-lookup"><span data-stu-id="d8f60-226">**Default** : `Production`</span></span>  
<span data-ttu-id="d8f60-227">**Umgebungsvariable:** `<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="d8f60-227">**Environment variable** : `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="d8f60-228">Verwenden Sie die Umgebungsvariable, oder rufen Sie `UseEnvironment` für `IHostBuilder` auf, um diesen Wert festzulegen:</span><span class="sxs-lookup"><span data-stu-id="d8f60-228">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="d8f60-229">ShutdownTimeout</span><span class="sxs-lookup"><span data-stu-id="d8f60-229">ShutdownTimeout</span></span>

<span data-ttu-id="d8f60-230">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) legt das Zeitlimit für <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> fest.</span><span class="sxs-lookup"><span data-stu-id="d8f60-230">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="d8f60-231">Der Standardwert beträgt fünf Sekunden.</span><span class="sxs-lookup"><span data-stu-id="d8f60-231">The default value is five seconds.</span></span>  <span data-ttu-id="d8f60-232">Während des Zeitlimits verhält sich der Host folgendermaßen:</span><span class="sxs-lookup"><span data-stu-id="d8f60-232">During the timeout period, the host:</span></span>

* <span data-ttu-id="d8f60-233">Er löst [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping) aus.</span><span class="sxs-lookup"><span data-stu-id="d8f60-233">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="d8f60-234">Er versucht, gehostete Dienste zu beenden und protokolliert Fehler für Dienste, die nicht beendet werden können.</span><span class="sxs-lookup"><span data-stu-id="d8f60-234">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="d8f60-235">Wenn das Zeitlimit abläuft bevor alle gehosteten Dienste beendet sind, werden alle verbleibenden aktiven Dienste beendet, wenn die App herunterfährt.</span><span class="sxs-lookup"><span data-stu-id="d8f60-235">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="d8f60-236">Die Dienste werden beendet, selbst wenn die Verarbeitung noch nicht abgeschlossen ist.</span><span class="sxs-lookup"><span data-stu-id="d8f60-236">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="d8f60-237">Wenn die Dienste mehr Zeit zum Beenden benötigen, sollten Sie das Zeitlimit erhöhen.</span><span class="sxs-lookup"><span data-stu-id="d8f60-237">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="d8f60-238">**Schlüssel** : `shutdownTimeoutSeconds`</span><span class="sxs-lookup"><span data-stu-id="d8f60-238">**Key** : `shutdownTimeoutSeconds`</span></span>  
<span data-ttu-id="d8f60-239">**Typ:** `int`</span><span class="sxs-lookup"><span data-stu-id="d8f60-239">**Type** : `int`</span></span>  
<span data-ttu-id="d8f60-240">**Standard** : 5 Sekunden</span><span class="sxs-lookup"><span data-stu-id="d8f60-240">**Default** : 5 seconds</span></span>  
<span data-ttu-id="d8f60-241">**Umgebungsvariable:** `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="d8f60-241">**Environment variable** : `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="d8f60-242">Verwenden Sie die Umgebungsvariable, oder konfigurieren Sie `HostOptions`, um diesen Wert festzulegen.</span><span class="sxs-lookup"><span data-stu-id="d8f60-242">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="d8f60-243">Im folgenden Beispiel wird das Zeitlimit auf 20 Sekunden festgelegt:</span><span class="sxs-lookup"><span data-stu-id="d8f60-243">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

### <a name="disable-app-configuration-reload-on-change"></a><span data-ttu-id="d8f60-244">Deaktivieren des Neuladens der App-Konfiguration bei Änderungen</span><span class="sxs-lookup"><span data-stu-id="d8f60-244">Disable app configuration reload on change</span></span>

<span data-ttu-id="d8f60-245">Die Dateien *appsettings.json* und *appsettings.{Environment}.json* werden [standardmäßig](xref:fundamentals/configuration/index#default) neu geladen, wenn die Datei geändert wird.</span><span class="sxs-lookup"><span data-stu-id="d8f60-245">By [default](xref:fundamentals/configuration/index#default), *appsettings.json* and *appsettings.{Environment}.json* are reloaded when the file changes.</span></span> <span data-ttu-id="d8f60-246">Zum Deaktivieren dieses Verhaltens in ASP.NET Core 5.0 oder höher müssen Sie den Schlüssel `hostBuilder:reloadConfigOnChange` auf `false` festlegen.</span><span class="sxs-lookup"><span data-stu-id="d8f60-246">To disable this reload behavior in ASP.NET Core 5.0 or later, set the `hostBuilder:reloadConfigOnChange` key to `false`.</span></span>

<span data-ttu-id="d8f60-247">**Schlüssel** : `hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="d8f60-247">**Key** : `hostBuilder:reloadConfigOnChange`</span></span>  
<span data-ttu-id="d8f60-248">**Typ:** `bool` (`true` oder `1`)</span><span class="sxs-lookup"><span data-stu-id="d8f60-248">**Type** : `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="d8f60-249">**Standard** : `true`</span><span class="sxs-lookup"><span data-stu-id="d8f60-249">**Default** : `true`</span></span>  
<span data-ttu-id="d8f60-250">**Befehlszeilenargument:** `hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="d8f60-250">**Command-line argument** : `hostBuilder:reloadConfigOnChange`</span></span>  
<span data-ttu-id="d8f60-251">**Umgebungsvariable:** `<PREFIX_>hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="d8f60-251">**Environment variable** : `<PREFIX_>hostBuilder:reloadConfigOnChange`</span></span>

> [!WARNING]
> <span data-ttu-id="d8f60-252">Das Trennzeichen `:` funktioniert nicht auf allen Plattformen mit den hierarchischen Schlüsseln von Umgebungsvariablen.</span><span class="sxs-lookup"><span data-stu-id="d8f60-252">The colon (`:`) separator doesn't work with environment variable hierarchical keys on all platforms.</span></span> <span data-ttu-id="d8f60-253">Weitere Informationen finden Sie unter [Umgebungsvariablen](xref:fundamentals/configuration/index#environment-variables).</span><span class="sxs-lookup"><span data-stu-id="d8f60-253">For more information, see [Environment variables](xref:fundamentals/configuration/index#environment-variables).</span></span>

## <a name="settings-for-web-apps"></a><span data-ttu-id="d8f60-254">Einstellungen für Web-Apps</span><span class="sxs-lookup"><span data-stu-id="d8f60-254">Settings for web apps</span></span>

<span data-ttu-id="d8f60-255">Einige Hosteinstellungen gelten nur für HTTP-Workloads.</span><span class="sxs-lookup"><span data-stu-id="d8f60-255">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="d8f60-256">Standardmäßig können Umgebungsvariablen, die zur Konfiguration dieser Einstellungen verwendet werden, ein `DOTNET_`- oder `ASPNETCORE_`-Präfix haben.</span><span class="sxs-lookup"><span data-stu-id="d8f60-256">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="d8f60-257">Für diese Einstellungen sind Erweiterungsmethoden in `IWebHostBuilder` verfügbar.</span><span class="sxs-lookup"><span data-stu-id="d8f60-257">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="d8f60-258">In den Codebeispielen, die zeigen, wie Sie die Erweiterungsmethoden aufrufen können, wird davon ausgegangen, dass `webBuilder` eine Instanz von `IWebHostBuilder` ist. Sehen Sie hierzu das folgende Beispiel:</span><span class="sxs-lookup"><span data-stu-id="d8f60-258">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="d8f60-259">CaptureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="d8f60-259">CaptureStartupErrors</span></span>

<span data-ttu-id="d8f60-260">Wenn diese `false` ist, führen Fehler beim Start zum Beenden des Hosts.</span><span class="sxs-lookup"><span data-stu-id="d8f60-260">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="d8f60-261">Wenn diese `true` ist, erfasst der Host Ausnahmen während des Starts und versucht, den Server zu starten.</span><span class="sxs-lookup"><span data-stu-id="d8f60-261">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="d8f60-262">**Schlüssel** : `captureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="d8f60-262">**Key** : `captureStartupErrors`</span></span>  
<span data-ttu-id="d8f60-263">**Typ:** `bool` (`true` oder `1`)</span><span class="sxs-lookup"><span data-stu-id="d8f60-263">**Type** : `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="d8f60-264">**Standard** : Die Standardeinstellung ist gleich `false`, es sei denn, die App wird mit Kestrel hinter IIS ausgeführt, dann ist sie gleich `true`.</span><span class="sxs-lookup"><span data-stu-id="d8f60-264">**Default** : Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="d8f60-265">**Umgebungsvariable:** `<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="d8f60-265">**Environment variable** : `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="d8f60-266">Verwenden Sie die Konfiguration, oder rufen Sie `CaptureStartupErrors` auf, um diesen Wert festzulegen:</span><span class="sxs-lookup"><span data-stu-id="d8f60-266">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="d8f60-267">DetailedErrors</span><span class="sxs-lookup"><span data-stu-id="d8f60-267">DetailedErrors</span></span>

<span data-ttu-id="d8f60-268">Wenn diese Einstellung aktiviert ist oder die Umgebung auf `Development` festgelegt ist, erfasst die App detaillierte Fehler.</span><span class="sxs-lookup"><span data-stu-id="d8f60-268">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="d8f60-269">**Schlüssel** : `detailedErrors`</span><span class="sxs-lookup"><span data-stu-id="d8f60-269">**Key** : `detailedErrors`</span></span>  
<span data-ttu-id="d8f60-270">**Typ:** `bool` (`true` oder `1`)</span><span class="sxs-lookup"><span data-stu-id="d8f60-270">**Type** : `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="d8f60-271">**Standard** : `false`</span><span class="sxs-lookup"><span data-stu-id="d8f60-271">**Default** : `false`</span></span>  
<span data-ttu-id="d8f60-272">**Umgebungsvariable:** `<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="d8f60-272">**Environment variable** : `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="d8f60-273">Verwenden Sie die Konfiguration, oder rufen Sie `UseSetting` auf, um diesen Wert festzulegen:</span><span class="sxs-lookup"><span data-stu-id="d8f60-273">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="d8f60-274">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="d8f60-274">HostingStartupAssemblies</span></span>

<span data-ttu-id="d8f60-275">Eine durch Semikolons getrennte Zeichenfolge der Hostingstartassemblys, die beim Start geladen werden soll.</span><span class="sxs-lookup"><span data-stu-id="d8f60-275">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="d8f60-276">Obwohl der Konfigurationswert standardmäßig auf eine leere Zeichenfolge festgelegt ist, enthalten die Hostingstartassemblys immer die Assembly der App.</span><span class="sxs-lookup"><span data-stu-id="d8f60-276">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="d8f60-277">Wenn Hostingstartassemblys bereitgestellt werden, werden diese zur Assembly der App hinzugefügt, damit diese geladen werden, wenn die App während des Starts die allgemeinen Dienste erstellt.</span><span class="sxs-lookup"><span data-stu-id="d8f60-277">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="d8f60-278">**Schlüssel** : `hostingStartupAssemblies`</span><span class="sxs-lookup"><span data-stu-id="d8f60-278">**Key** : `hostingStartupAssemblies`</span></span>  
<span data-ttu-id="d8f60-279">**Typ:** `string`</span><span class="sxs-lookup"><span data-stu-id="d8f60-279">**Type** : `string`</span></span>  
<span data-ttu-id="d8f60-280">**Standard** : Leere Zeichenfolge</span><span class="sxs-lookup"><span data-stu-id="d8f60-280">**Default** : Empty string</span></span>  
<span data-ttu-id="d8f60-281">**Umgebungsvariable:** `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="d8f60-281">**Environment variable** : `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="d8f60-282">Verwenden Sie die Konfiguration, oder rufen Sie `UseSetting` auf, um diesen Wert festzulegen:</span><span class="sxs-lookup"><span data-stu-id="d8f60-282">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="d8f60-283">HostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="d8f60-283">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="d8f60-284">Eine durch Semikolons getrennte Zeichenfolge der Hostingstartassemblys, die beim Start ausgeschlossen werden sollen.</span><span class="sxs-lookup"><span data-stu-id="d8f60-284">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="d8f60-285">**Schlüssel** : `hostingStartupExcludeAssemblies`</span><span class="sxs-lookup"><span data-stu-id="d8f60-285">**Key** : `hostingStartupExcludeAssemblies`</span></span>  
<span data-ttu-id="d8f60-286">**Typ:** `string`</span><span class="sxs-lookup"><span data-stu-id="d8f60-286">**Type** : `string`</span></span>  
<span data-ttu-id="d8f60-287">**Standard** : Leere Zeichenfolge</span><span class="sxs-lookup"><span data-stu-id="d8f60-287">**Default** : Empty string</span></span>  
<span data-ttu-id="d8f60-288">**Umgebungsvariable:** `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="d8f60-288">**Environment variable** : `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="d8f60-289">Verwenden Sie die Konfiguration, oder rufen Sie `UseSetting` auf, um diesen Wert festzulegen:</span><span class="sxs-lookup"><span data-stu-id="d8f60-289">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="d8f60-290">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="d8f60-290">HTTPS_Port</span></span>

<span data-ttu-id="d8f60-291">Der HTTPS-Umleitungsport.</span><span class="sxs-lookup"><span data-stu-id="d8f60-291">The HTTPS redirect port.</span></span> <span data-ttu-id="d8f60-292">Wird in [Erzwingen von HTTPS](xref:security/enforcing-ssl) verwendet.</span><span class="sxs-lookup"><span data-stu-id="d8f60-292">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="d8f60-293">**Schlüssel** : `https_port`</span><span class="sxs-lookup"><span data-stu-id="d8f60-293">**Key** : `https_port`</span></span>  
<span data-ttu-id="d8f60-294">**Typ:** `string`</span><span class="sxs-lookup"><span data-stu-id="d8f60-294">**Type** : `string`</span></span>  
<span data-ttu-id="d8f60-295">**Standard** : Es ist kein Standardwert festgelegt.</span><span class="sxs-lookup"><span data-stu-id="d8f60-295">**Default** : A default value isn't set.</span></span>  
<span data-ttu-id="d8f60-296">**Umgebungsvariable:** `<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="d8f60-296">**Environment variable** : `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="d8f60-297">Verwenden Sie die Konfiguration, oder rufen Sie `UseSetting` auf, um diesen Wert festzulegen:</span><span class="sxs-lookup"><span data-stu-id="d8f60-297">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="d8f60-298">PreferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="d8f60-298">PreferHostingUrls</span></span>

<span data-ttu-id="d8f60-299">Gibt an, ob der Host auf die URLs lauschen soll, die mit `IWebHostBuilder` konfiguriert wurden, anstatt auf die URLs zu lauschen, die mit der `IServer`-Implementierung konfiguriert wurden.</span><span class="sxs-lookup"><span data-stu-id="d8f60-299">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those URLs configured with the `IServer` implementation.</span></span>

<span data-ttu-id="d8f60-300">**Schlüssel** : `preferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="d8f60-300">**Key** : `preferHostingUrls`</span></span>  
<span data-ttu-id="d8f60-301">**Typ:** `bool` (`true` oder `1`)</span><span class="sxs-lookup"><span data-stu-id="d8f60-301">**Type** : `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="d8f60-302">**Standard** : `true`</span><span class="sxs-lookup"><span data-stu-id="d8f60-302">**Default** : `true`</span></span>  
<span data-ttu-id="d8f60-303">**Umgebungsvariable:** `<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="d8f60-303">**Environment variable** : `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="d8f60-304">Verwenden Sie die Umgebungsvariable, oder rufen Sie `PreferHostingUrls` auf, um diesen Wert festzulegen:</span><span class="sxs-lookup"><span data-stu-id="d8f60-304">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="d8f60-305">PreventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="d8f60-305">PreventHostingStartup</span></span>

<span data-ttu-id="d8f60-306">Verhindert das automatische Laden der Hostingstartassemblys, einschließlich denen, die von der Assembly der App konfiguriert wurden.</span><span class="sxs-lookup"><span data-stu-id="d8f60-306">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="d8f60-307">Weitere Informationen finden Sie unter <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="d8f60-307">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="d8f60-308">**Schlüssel** : `preventHostingStartup`</span><span class="sxs-lookup"><span data-stu-id="d8f60-308">**Key** : `preventHostingStartup`</span></span>  
<span data-ttu-id="d8f60-309">**Typ:** `bool` (`true` oder `1`)</span><span class="sxs-lookup"><span data-stu-id="d8f60-309">**Type** : `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="d8f60-310">**Standard** : `false`</span><span class="sxs-lookup"><span data-stu-id="d8f60-310">**Default** : `false`</span></span>  
<span data-ttu-id="d8f60-311">**Umgebungsvariable:** `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="d8f60-311">**Environment variable** : `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="d8f60-312">Verwenden Sie die Umgebungsvariable, oder rufen Sie `UseSetting` auf, um diesen Wert festzulegen:</span><span class="sxs-lookup"><span data-stu-id="d8f60-312">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="d8f60-313">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="d8f60-313">StartupAssembly</span></span>

<span data-ttu-id="d8f60-314">Die Assembly, die nach der `Startup`-Klasse suchen soll.</span><span class="sxs-lookup"><span data-stu-id="d8f60-314">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="d8f60-315">**Schlüssel** : `startupAssembly`</span><span class="sxs-lookup"><span data-stu-id="d8f60-315">**Key** : `startupAssembly`</span></span>  
<span data-ttu-id="d8f60-316">**Typ:** `string`</span><span class="sxs-lookup"><span data-stu-id="d8f60-316">**Type** : `string`</span></span>  
<span data-ttu-id="d8f60-317">**Standard** : Die Assembly der App</span><span class="sxs-lookup"><span data-stu-id="d8f60-317">**Default** : The app's assembly</span></span>  
<span data-ttu-id="d8f60-318">**Umgebungsvariable:** `<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="d8f60-318">**Environment variable** : `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="d8f60-319">Verwenden Sie die Umgebungsvariable, oder rufen Sie `UseStartup` auf, um diesen Wert festzulegen.</span><span class="sxs-lookup"><span data-stu-id="d8f60-319">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="d8f60-320">`UseStartup` kann einen Assemblynamen (`string`) oder einen Typ (`TStartup`) annehmen.</span><span class="sxs-lookup"><span data-stu-id="d8f60-320">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="d8f60-321">Wenn mehrere `UseStartup`-Methoden aufgerufen werden, hat die letzte Vorrang.</span><span class="sxs-lookup"><span data-stu-id="d8f60-321">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="d8f60-322">URLs</span><span class="sxs-lookup"><span data-stu-id="d8f60-322">URLs</span></span>

<span data-ttu-id="d8f60-323">Eine durch Semikolons getrennte Liste mit IP-Adressen oder Hostadressen mit Ports und Protokollen, denen der Server für Anforderungen lauschen soll.</span><span class="sxs-lookup"><span data-stu-id="d8f60-323">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="d8f60-324">Beispielsweise `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="d8f60-324">For example, `http://localhost:123`.</span></span> <span data-ttu-id="d8f60-325">Verwenden Sie \*, um anzugeben, dass der Server mithilfe des angegebenen Ports und Protokolls (z.B. `http://*:5000`) den Anfragen aller IP-Adressen oder Hostnamen lauschen soll.</span><span class="sxs-lookup"><span data-stu-id="d8f60-325">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="d8f60-326">Das Protokoll (`http://` oder `https://`) muss in jeder URL enthalten sein.</span><span class="sxs-lookup"><span data-stu-id="d8f60-326">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="d8f60-327">Die unterstützten Formate variieren bei den verschiedenen Servern.</span><span class="sxs-lookup"><span data-stu-id="d8f60-327">Supported formats vary among servers.</span></span>

<span data-ttu-id="d8f60-328">**Schlüssel** : `urls`</span><span class="sxs-lookup"><span data-stu-id="d8f60-328">**Key** : `urls`</span></span>  
<span data-ttu-id="d8f60-329">**Typ:** `string`</span><span class="sxs-lookup"><span data-stu-id="d8f60-329">**Type** : `string`</span></span>  
<span data-ttu-id="d8f60-330">**Standard** : `http://localhost:5000` und `https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="d8f60-330">**Default** : `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="d8f60-331">**Umgebungsvariable:** `<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="d8f60-331">**Environment variable** : `<PREFIX_>URLS`</span></span>

<span data-ttu-id="d8f60-332">Verwenden Sie die Umgebungsvariable, oder rufen Sie `UseUrls` auf, um diesen Wert festzulegen:</span><span class="sxs-lookup"><span data-stu-id="d8f60-332">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="d8f60-333">Kestrel verfügt über eine eigene API für die Endpunktkonfiguration.</span><span class="sxs-lookup"><span data-stu-id="d8f60-333">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="d8f60-334">Weitere Informationen finden Sie unter <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="d8f60-334">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="d8f60-335">WebRoot</span><span class="sxs-lookup"><span data-stu-id="d8f60-335">WebRoot</span></span>

<span data-ttu-id="d8f60-336">Die [IWebHostEnvironment.WebRootPath-](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath)-Eigenschaft bestimmt den relativen Pfad zu den statischen Objekten der App.</span><span class="sxs-lookup"><span data-stu-id="d8f60-336">The [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) property determines the relative path to the app's static assets.</span></span> <span data-ttu-id="d8f60-337">Wenn der Pfad nicht vorhanden ist, wird ein Dateianbieter ohne Funktion verwendet.</span><span class="sxs-lookup"><span data-stu-id="d8f60-337">If the path doesn't exist, a no-op file provider is used.</span></span>  

<span data-ttu-id="d8f60-338">**Schlüssel** : `webroot`</span><span class="sxs-lookup"><span data-stu-id="d8f60-338">**Key** : `webroot`</span></span>  
<span data-ttu-id="d8f60-339">**Typ:** `string`</span><span class="sxs-lookup"><span data-stu-id="d8f60-339">**Type** : `string`</span></span>  
<span data-ttu-id="d8f60-340">**Standard** : Der Standardwert ist `wwwroot`.</span><span class="sxs-lookup"><span data-stu-id="d8f60-340">**Default** : The default is `wwwroot`.</span></span> <span data-ttu-id="d8f60-341">Der Pfad zu *{Inhaltsstammverzeichnis}/wwwroot* muss vorhanden sein.</span><span class="sxs-lookup"><span data-stu-id="d8f60-341">The path to *{content root}/wwwroot* must exist.</span></span>  
<span data-ttu-id="d8f60-342">**Umgebungsvariable:** `<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="d8f60-342">**Environment variable** : `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="d8f60-343">Verwenden Sie die Umgebungsvariable, oder rufen Sie `UseWebRoot` für `IWebHostBuilder` auf, um diesen Wert festzulegen:</span><span class="sxs-lookup"><span data-stu-id="d8f60-343">To set this value, use the environment variable or call `UseWebRoot` on `IWebHostBuilder`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="d8f60-344">Weitere Informationen finden Sie unter:</span><span class="sxs-lookup"><span data-stu-id="d8f60-344">For more information, see:</span></span>

* [<span data-ttu-id="d8f60-345">Grundlagen: Webstammverzeichnis</span><span class="sxs-lookup"><span data-stu-id="d8f60-345">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="d8f60-346">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="d8f60-346">ContentRoot</span></span>](#contentroot)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="d8f60-347">Verwalten der Lebensdauer des Hosts</span><span class="sxs-lookup"><span data-stu-id="d8f60-347">Manage the host lifetime</span></span>

<span data-ttu-id="d8f60-348">Rufen Sie Methoden für die erstellte <xref:Microsoft.Extensions.Hosting.IHost>-Implementierung auf, um die App zu starten und zu beenden.</span><span class="sxs-lookup"><span data-stu-id="d8f60-348">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="d8f60-349">Diese Methoden wirken sich auf alle <xref:Microsoft.Extensions.Hosting.IHostedService>-Implementierungen aus, die im Dienstcontainer registriert sind.</span><span class="sxs-lookup"><span data-stu-id="d8f60-349">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="d8f60-350">Run</span><span class="sxs-lookup"><span data-stu-id="d8f60-350">Run</span></span>

<span data-ttu-id="d8f60-351">Durch <xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> wird die App gestartet und der aufrufende Thread blockiert, bis der Host heruntergefahren wird.</span><span class="sxs-lookup"><span data-stu-id="d8f60-351"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="d8f60-352">RunAsync</span><span class="sxs-lookup"><span data-stu-id="d8f60-352">RunAsync</span></span>

<span data-ttu-id="d8f60-353">Durch <xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> wird die App ausgeführt und eine <xref:System.Threading.Tasks.Task>-Methode ausgegeben, die abgeschlossen wird, wenn das Abbruchtoken oder das Herunterfahren ausgelöst wird.</span><span class="sxs-lookup"><span data-stu-id="d8f60-353"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="d8f60-354">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="d8f60-354">RunConsoleAsync</span></span>

<span data-ttu-id="d8f60-355"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> aktiviert die Unterstützung der Konsole, erstellt und startet den Host und lauscht auf <kbd>STRG</kbd>+<kbd>C</kbd>/SIGINT oder SIGTERM, um das Herunterfahren auszulösen.</span><span class="sxs-lookup"><span data-stu-id="d8f60-355"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="d8f60-356">Starten</span><span class="sxs-lookup"><span data-stu-id="d8f60-356">Start</span></span>

<span data-ttu-id="d8f60-357"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> startet den Host synchron.</span><span class="sxs-lookup"><span data-stu-id="d8f60-357"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="d8f60-358">StartAsync</span><span class="sxs-lookup"><span data-stu-id="d8f60-358">StartAsync</span></span>

<span data-ttu-id="d8f60-359">Durch <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> wird der Host gestartet und eine <xref:System.Threading.Tasks.Task>-Methode ausgegeben, die abgeschlossen wird, wenn das Abbruchtoken oder das Herunterfahren ausgelöst wird.</span><span class="sxs-lookup"><span data-stu-id="d8f60-359"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="d8f60-360"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> wird am Anfang der `StartAsync`-Methode aufgerufen, die den Vorgang erst fortsetzt, wenn sie abgeschlossen ist.</span><span class="sxs-lookup"><span data-stu-id="d8f60-360"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="d8f60-361">Dies kann verwendet werden, um den Start zu verzögern, bis dieser durch ein externes Ereignis initiiert wird.</span><span class="sxs-lookup"><span data-stu-id="d8f60-361">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="d8f60-362">StopAsync</span><span class="sxs-lookup"><span data-stu-id="d8f60-362">StopAsync</span></span>

<span data-ttu-id="d8f60-363"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> versucht, den Host innerhalb des angegebenen Timeouts zu beenden.</span><span class="sxs-lookup"><span data-stu-id="d8f60-363"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="d8f60-364">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="d8f60-364">WaitForShutdown</span></span>

<span data-ttu-id="d8f60-365"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blockiert den aufrufenden Thread, bis das Herunterfahren durch den IHostLifetime, z. B. über <kbd>STRG</kbd>+<kbd>C</kbd>/SIGINT oder SIGTERM, ausgelöst wird.</span><span class="sxs-lookup"><span data-stu-id="d8f60-365"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="d8f60-366">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="d8f60-366">WaitForShutdownAsync</span></span>

<span data-ttu-id="d8f60-367"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> gibt eine <xref:System.Threading.Tasks.Task>-Methode zurück, die abgeschlossen wird, wenn das Herunterfahren über das angegebene Token ausgelöst wird, und ruft <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> auf.</span><span class="sxs-lookup"><span data-stu-id="d8f60-367"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="d8f60-368">Externe Steuerung</span><span class="sxs-lookup"><span data-stu-id="d8f60-368">External control</span></span>

<span data-ttu-id="d8f60-369">Die Lebensdauer des Hosts kann mithilfe von Methoden, die extern aufgerufen werden können, direkt gesteuert werden:</span><span class="sxs-lookup"><span data-stu-id="d8f60-369">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

```csharp
public class Program
{
    private IHost _host;

    public Program()
    {
        _host = new HostBuilder()
            .Build();
    }

    public async Task StartAsync()
    {
        _host.StartAsync();
    }

    public async Task StopAsync()
    {
        using (_host)
        {
            await _host.StopAsync(TimeSpan.FromSeconds(5));
        }
    }
}
```

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="d8f60-370">Die ASP.NET Core-Vorlagen erstellen einen generischen .NET Core-Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>).</span><span class="sxs-lookup"><span data-stu-id="d8f60-370">The ASP.NET Core templates create a .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>).</span></span>

<span data-ttu-id="d8f60-371">Dieses Thema enthält Informationen zur Verwendung des generischen .NET-Hosts in ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d8f60-371">This topic provides information on using .NET Generic Host in ASP.NET Core.</span></span> <span data-ttu-id="d8f60-372">Informationen zur Verwendung des generischen .NET-Hosts in Konsolen-Apps finden Sie unter [Generischer .NET-Host](/dotnet/core/extensions/generic-host).</span><span class="sxs-lookup"><span data-stu-id="d8f60-372">For information on using .NET Generic Host in console apps, see [.NET Generic Host](/dotnet/core/extensions/generic-host).</span></span>

## <a name="host-definition"></a><span data-ttu-id="d8f60-373">Hostdefinition</span><span class="sxs-lookup"><span data-stu-id="d8f60-373">Host definition</span></span>

<span data-ttu-id="d8f60-374">Der *Host* ist ein Objekt, das alle Ressourcen der App kapselt, z. B.:</span><span class="sxs-lookup"><span data-stu-id="d8f60-374">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="d8f60-375">Abhängigkeitsinjektion</span><span class="sxs-lookup"><span data-stu-id="d8f60-375">Dependency injection (DI)</span></span>
* <span data-ttu-id="d8f60-376">Protokollierung</span><span class="sxs-lookup"><span data-stu-id="d8f60-376">Logging</span></span>
* <span data-ttu-id="d8f60-377">Konfiguration</span><span class="sxs-lookup"><span data-stu-id="d8f60-377">Configuration</span></span>
* <span data-ttu-id="d8f60-378">`IHostedService`-Implementierungen</span><span class="sxs-lookup"><span data-stu-id="d8f60-378">`IHostedService` implementations</span></span>

<span data-ttu-id="d8f60-379">Beim Starten eines Hosts wird <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> für jede Implementierung von <xref:Microsoft.Extensions.Hosting.IHostedService> aufgerufen, die in der Sammlung gehosteter Dienste des Dienstcontainers registriert ist.</span><span class="sxs-lookup"><span data-stu-id="d8f60-379">When a host starts, it calls <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> registered in the service container's collection of hosted services.</span></span> <span data-ttu-id="d8f60-380">In einer Web-App ist eine der `IHostedService`-Implementierungen ein Webdienst, der eine [HTTP-Serverimplementierung](xref:fundamentals/index#servers) startet.</span><span class="sxs-lookup"><span data-stu-id="d8f60-380">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="d8f60-381">Der wichtigste Grund für das Einschließen aller unabhängigen Ressourcen der App in einem Objekt ist die Verwaltung der Lebensdauer: steuern von Start und ordnungsgemäßem Herunterfahren der App.</span><span class="sxs-lookup"><span data-stu-id="d8f60-381">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="d8f60-382">Einrichten eines Hosts</span><span class="sxs-lookup"><span data-stu-id="d8f60-382">Set up a host</span></span>

<span data-ttu-id="d8f60-383">Der Host wird in der Regel per Code in der `Program`-Klasse konfiguriert, erstellt und ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="d8f60-383">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="d8f60-384">Die `Main`-Methode:</span><span class="sxs-lookup"><span data-stu-id="d8f60-384">The `Main` method:</span></span>

* <span data-ttu-id="d8f60-385">Ruft eine `CreateHostBuilder`-Methode zum Erstellen und Konfigurieren eines Generatorobjekts auf.</span><span class="sxs-lookup"><span data-stu-id="d8f60-385">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="d8f60-386">Ruft `Build`- und `Run`-Methoden für das Generatorobjekt auf.</span><span class="sxs-lookup"><span data-stu-id="d8f60-386">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="d8f60-387">Die ASP.NET Core-Webvorlagen generieren den folgenden Code zum Erstellen eines generischen Hosts:</span><span class="sxs-lookup"><span data-stu-id="d8f60-387">The ASP.NET Core web templates generate the following code to create a Generic Host:</span></span>

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
                webBuilder.UseStartup<Startup>();
            });
}
```

<span data-ttu-id="d8f60-388">Der folgende Code erstellt einen generischen Host mithilfe einer Nicht-HTTP-Workload.</span><span class="sxs-lookup"><span data-stu-id="d8f60-388">The following code creates a Generic Host using non-HTTP workload.</span></span> <span data-ttu-id="d8f60-389">Die `IHostedService`-Implementierung wird dem DI-Container hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="d8f60-389">The `IHostedService` implementation is added to the DI container:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureServices((hostContext, services) =>
            {
               services.AddHostedService<Worker>();
            });
}
```

<span data-ttu-id="d8f60-390">Für einen HTTP-Workload ist die `Main`-Methode die gleiche, `CreateHostBuilder` ruft jedoch `ConfigureWebHostDefaults` auf:</span><span class="sxs-lookup"><span data-stu-id="d8f60-390">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="d8f60-391">Der voranstehende Code wird von den ASP.NET Core-Vorlagen generiert.</span><span class="sxs-lookup"><span data-stu-id="d8f60-391">The preceding code is generated by the ASP.NET Core templates.</span></span>

<span data-ttu-id="d8f60-392">Ändern Sie nicht den Namen bzw. die Signatur der `CreateHostBuilder`-Methode, wenn die App Entity Framework Core verwendet.</span><span class="sxs-lookup"><span data-stu-id="d8f60-392">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="d8f60-393">Die [Entity Framework Core-Tools](/ef/core/miscellaneous/cli/) erwarten eine `CreateHostBuilder`-Methode, die den Host konfiguriert, ohne die App auszuführen.</span><span class="sxs-lookup"><span data-stu-id="d8f60-393">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="d8f60-394">Weitere Informationen finden Sie unter [DbContext-Instanzerstellung zur Entwurfszeit](/ef/core/miscellaneous/cli/dbcontext-creation).</span><span class="sxs-lookup"><span data-stu-id="d8f60-394">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="d8f60-395">Standardeinstellungen für den Generator</span><span class="sxs-lookup"><span data-stu-id="d8f60-395">Default builder settings</span></span>

<span data-ttu-id="d8f60-396">Die <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A>-Methode:</span><span class="sxs-lookup"><span data-stu-id="d8f60-396">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> method:</span></span>

* <span data-ttu-id="d8f60-397">Legt das [Inhaltsstammverzeichnis](xref:fundamentals/index#content-root) auf den Pfad fest, der von <xref:System.IO.Directory.GetCurrentDirectory%2A> zurückgegeben wird.</span><span class="sxs-lookup"><span data-stu-id="d8f60-397">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory%2A>.</span></span>
* <span data-ttu-id="d8f60-398">Lädt Hostkonfiguration aus:</span><span class="sxs-lookup"><span data-stu-id="d8f60-398">Loads host configuration from:</span></span>
  * <span data-ttu-id="d8f60-399">Umgebungsvariablen mit dem Präfix `DOTNET_`.</span><span class="sxs-lookup"><span data-stu-id="d8f60-399">Environment variables prefixed with `DOTNET_`.</span></span>
  * <span data-ttu-id="d8f60-400">Befehlszeilenargumenten</span><span class="sxs-lookup"><span data-stu-id="d8f60-400">Command-line arguments.</span></span>
* <span data-ttu-id="d8f60-401">Lädt App-Konfiguration aus:</span><span class="sxs-lookup"><span data-stu-id="d8f60-401">Loads app configuration from:</span></span>
  * <span data-ttu-id="d8f60-402">*appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="d8f60-402">*appsettings.json*.</span></span>
  * <span data-ttu-id="d8f60-403">*appsettings.{Environment}.json*</span><span class="sxs-lookup"><span data-stu-id="d8f60-403">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="d8f60-404">[Geheimnis-Manager](xref:security/app-secrets), wenn die App in der `Development`-Umgebung ausgeführt wird</span><span class="sxs-lookup"><span data-stu-id="d8f60-404">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="d8f60-405">Umgebungsvariablen.</span><span class="sxs-lookup"><span data-stu-id="d8f60-405">Environment variables.</span></span>
  * <span data-ttu-id="d8f60-406">Befehlszeilenargumenten</span><span class="sxs-lookup"><span data-stu-id="d8f60-406">Command-line arguments.</span></span>
* <span data-ttu-id="d8f60-407">Fügt die folgenden [Protokollierungsanbieter](xref:fundamentals/logging/index) hinzu:</span><span class="sxs-lookup"><span data-stu-id="d8f60-407">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="d8f60-408">Konsole</span><span class="sxs-lookup"><span data-stu-id="d8f60-408">Console</span></span>
  * <span data-ttu-id="d8f60-409">Debug</span><span class="sxs-lookup"><span data-stu-id="d8f60-409">Debug</span></span>
  * <span data-ttu-id="d8f60-410">EventSource</span><span class="sxs-lookup"><span data-stu-id="d8f60-410">EventSource</span></span>
  * <span data-ttu-id="d8f60-411">EventLog (nur bei Ausführung unter Windows)</span><span class="sxs-lookup"><span data-stu-id="d8f60-411">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="d8f60-412">Aktiviert [Bereichsvalidierung](xref:fundamentals/dependency-injection#scope-validation) und [Abhängigkeitsüberprüfung](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild), wenn es sich um eine Entwicklungsumgebung handelt.</span><span class="sxs-lookup"><span data-stu-id="d8f60-412">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="d8f60-413">Die `ConfigureWebHostDefaults`-Methode:</span><span class="sxs-lookup"><span data-stu-id="d8f60-413">The `ConfigureWebHostDefaults` method:</span></span>

* <span data-ttu-id="d8f60-414">Lädt Hostkonfiguration aus Umgebungsvariablen mit dem Präfix `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="d8f60-414">Loads host configuration from environment variables prefixed with `ASPNETCORE_`.</span></span>
* <span data-ttu-id="d8f60-415">Legt den [Kestrel](xref:fundamentals/servers/kestrel)-Server als Webserver fest und konfiguriert ihn mithilfe der Hostkonfigurationsanbieter der App.</span><span class="sxs-lookup"><span data-stu-id="d8f60-415">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="d8f60-416">Die Standardoptionen des Kestrel-Servers finden Sie unter <xref:fundamentals/servers/kestrel#kestrel-options>.</span><span class="sxs-lookup"><span data-stu-id="d8f60-416">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="d8f60-417">Fügt [Middleware zum Filtern von Hosts](xref:fundamentals/servers/kestrel#host-filtering) hinzu.</span><span class="sxs-lookup"><span data-stu-id="d8f60-417">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="d8f60-418">Fügt [Middleware für weitergeleitete Header](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) hinzu, wenn `ASPNETCORE_FORWARDEDHEADERS_ENABLED` den Wert `true` aufweist.</span><span class="sxs-lookup"><span data-stu-id="d8f60-418">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span></span>
* <span data-ttu-id="d8f60-419">Ermöglicht die Integration von IIS.</span><span class="sxs-lookup"><span data-stu-id="d8f60-419">Enables IIS integration.</span></span> <span data-ttu-id="d8f60-420">Informationen zu den IIS-Standardoptionen finden Sie unter <xref:host-and-deploy/iis/index#iis-options>.</span><span class="sxs-lookup"><span data-stu-id="d8f60-420">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="d8f60-421">In den Abschnitten [Einstellungen für alle App-Typen](#settings-for-all-app-types) und [Einstellungen für Web-Apps](#settings-for-web-apps) weiter unten in diesem Artikel wird beschrieben, wie die Standardeinstellungen des Generators außer Kraft gesetzt werden.</span><span class="sxs-lookup"><span data-stu-id="d8f60-421">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="d8f60-422">Von Frameworks bereitgestellte Dienste</span><span class="sxs-lookup"><span data-stu-id="d8f60-422">Framework-provided services</span></span>

<span data-ttu-id="d8f60-423">Die folgenden Dienste werden automatisch registriert:</span><span class="sxs-lookup"><span data-stu-id="d8f60-423">The following services are registered automatically:</span></span>

* [<span data-ttu-id="d8f60-424">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="d8f60-424">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="d8f60-425">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="d8f60-425">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="d8f60-426">IHostEnvironment/IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="d8f60-426">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="d8f60-427">Weitere Informationen zu den vom Framework bereitgestellten Diensten finden Sie unter <xref:fundamentals/dependency-injection#framework-provided-services>.</span><span class="sxs-lookup"><span data-stu-id="d8f60-427">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="d8f60-428">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="d8f60-428">IHostApplicationLifetime</span></span>

<span data-ttu-id="d8f60-429">Fügt den <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime>-Dienst (vorher `IApplicationLifetime`-Dienst) in eine beliebige Klasse ein, um die Aktivitäten nach dem Starten und die Aufgaben für ordnungsgemäßes Herunterfahren zu verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="d8f60-429">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="d8f60-430">Bei drei der Eigenschaften der Schnittstelle handelt es sich um Abbruchtokens, die zum Registrieren von Ereignishandlermethoden zum Starten und Beenden von Apps verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="d8f60-430">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="d8f60-431">Die Benutzeroberfläche umfasst auch eine `StopApplication`-Methode.</span><span class="sxs-lookup"><span data-stu-id="d8f60-431">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="d8f60-432">Das folgende Beispiel zeigt eine `IHostedService`-Implementierung, die `IHostApplicationLifetime`-Ereignisse registriert:</span><span class="sxs-lookup"><span data-stu-id="d8f60-432">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="d8f60-433">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="d8f60-433">IHostLifetime</span></span>

<span data-ttu-id="d8f60-434">Die <xref:Microsoft.Extensions.Hosting.IHostLifetime>-Implementierung steuert, wann der Host gestartet und beendet wird.</span><span class="sxs-lookup"><span data-stu-id="d8f60-434">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="d8f60-435">Die zuletzt registrierte Implementierung wird verwendet.</span><span class="sxs-lookup"><span data-stu-id="d8f60-435">The last implementation registered is used.</span></span>

<span data-ttu-id="d8f60-436">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` ist die `IHostLifetime`-Standardimplementierung.</span><span class="sxs-lookup"><span data-stu-id="d8f60-436">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="d8f60-437">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="d8f60-437">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="d8f60-438">lauscht auf <kbd>STRG</kbd>+<kbd>C</kbd>/SIGINT oder SIGTERM und ruft <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication%2A> auf, um das Herunterfahren zu beginnen.</span><span class="sxs-lookup"><span data-stu-id="d8f60-438">Listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication%2A> to start the shutdown process.</span></span>
* <span data-ttu-id="d8f60-439">Hebt die Blockierung von Erweiterungen wie [RunAsync](#runasync) und [WaitForShutdownAsync](#waitforshutdownasync) auf.</span><span class="sxs-lookup"><span data-stu-id="d8f60-439">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="d8f60-440">IHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="d8f60-440">IHostEnvironment</span></span>

<span data-ttu-id="d8f60-441">Fügt den <xref:Microsoft.Extensions.Hosting.IHostEnvironment>-Dienst in eine Klasse ein, um Informationen über die folgenden Einstellungen abzurufen:</span><span class="sxs-lookup"><span data-stu-id="d8f60-441">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following settings:</span></span>

* [<span data-ttu-id="d8f60-442">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="d8f60-442">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="d8f60-443">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="d8f60-443">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="d8f60-444">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="d8f60-444">ContentRootPath</span></span>](#contentroot)

<span data-ttu-id="d8f60-445">Web-Apps implementieren die `IWebHostEnvironment`-Schnittstelle, die `IHostEnvironment` erbt und [WebRootPath](#webroot) hinzufügt.</span><span class="sxs-lookup"><span data-stu-id="d8f60-445">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="d8f60-446">Konfiguration des Hosts</span><span class="sxs-lookup"><span data-stu-id="d8f60-446">Host configuration</span></span>

<span data-ttu-id="d8f60-447">Die Hostkonfiguration wird für die Eigenschaften der <xref:Microsoft.Extensions.Hosting.IHostEnvironment>-Implementierung verwendet.</span><span class="sxs-lookup"><span data-stu-id="d8f60-447">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="d8f60-448">Sie ist über [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration%2A> verfügbar.</span><span class="sxs-lookup"><span data-stu-id="d8f60-448">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration%2A>.</span></span> <span data-ttu-id="d8f60-449">Nach `ConfigureAppConfiguration` wird `HostBuilderContext.Configuration` durch die App-Konfiguration ersetzt.</span><span class="sxs-lookup"><span data-stu-id="d8f60-449">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="d8f60-450">Rufen Sie <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration%2A> für `IHostBuilder` auf, um die Hostkonfiguration hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="d8f60-450">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration%2A> on `IHostBuilder`.</span></span> <span data-ttu-id="d8f60-451">`ConfigureHostConfiguration` kann mehrfach mit additiven Ergebnissen aufgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="d8f60-451">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="d8f60-452">Der Host verwendet die Option, die zuletzt einen Wert für einen bestimmten Schlüssel festlegt.</span><span class="sxs-lookup"><span data-stu-id="d8f60-452">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="d8f60-453">Der Umgebungsvariablenanbieter mit dem Präfix `DOTNET_` und die Befehlszeilenargumente sind in `CreateDefaultBuilder` enthalten.</span><span class="sxs-lookup"><span data-stu-id="d8f60-453">The environment variable provider with prefix `DOTNET_` and command-line arguments are included by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="d8f60-454">Für Web-Apps wird der Umgebungsvariablenanbieter mit dem Präfix `ASPNETCORE_` hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="d8f60-454">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="d8f60-455">Das Präfix wird entfernt, wenn die Umgebungsvariablen gelesen werden.</span><span class="sxs-lookup"><span data-stu-id="d8f60-455">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="d8f60-456">Der Wert der Umgebungsvariable für `ASPNETCORE_ENVIRONMENT` wird z. B. der Hostkonfigurationswert für den `environment`-Schlüssel.</span><span class="sxs-lookup"><span data-stu-id="d8f60-456">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="d8f60-457">Im folgenden Beispiel wird eine Hostkonfiguration erstellt:</span><span class="sxs-lookup"><span data-stu-id="d8f60-457">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="d8f60-458">App-Konfiguration</span><span class="sxs-lookup"><span data-stu-id="d8f60-458">App configuration</span></span>

<span data-ttu-id="d8f60-459">Die App-Konfiguration wird durch Aufrufen von <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> für `IHostBuilder` erstellt.</span><span class="sxs-lookup"><span data-stu-id="d8f60-459">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="d8f60-460">`ConfigureAppConfiguration` kann mehrfach mit additiven Ergebnissen aufgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="d8f60-460">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="d8f60-461">Die App verwendet die Option, die zuletzt einen Wert für einen bestimmten Schlüssel festlegt.</span><span class="sxs-lookup"><span data-stu-id="d8f60-461">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="d8f60-462">Die von `ConfigureAppConfiguration` erstellte Konfiguration ist unter [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) verfügbar und kann für nachfolgende Vorgänge und als Dienst für die Abhängigkeitsinjektion verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="d8f60-462">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="d8f60-463">Die Hostkonfiguration wird ebenfalls der App-Konfiguration hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="d8f60-463">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="d8f60-464">Weitere Informationen finden Sie unter [Konfiguration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span><span class="sxs-lookup"><span data-stu-id="d8f60-464">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="d8f60-465">Einstellungen für alle App-Typen</span><span class="sxs-lookup"><span data-stu-id="d8f60-465">Settings for all app types</span></span>

<span data-ttu-id="d8f60-466">In diesem Abschnitt werden Hosteinstellungen aufgeführt, die sowohl für HTTP- als auch für Nicht-HTTP-Workloads gelten.</span><span class="sxs-lookup"><span data-stu-id="d8f60-466">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="d8f60-467">Standardmäßig können Umgebungsvariablen, die zur Konfiguration dieser Einstellungen verwendet werden, ein `DOTNET_`- oder `ASPNETCORE_`-Präfix haben.</span><span class="sxs-lookup"><span data-stu-id="d8f60-467">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="d8f60-468">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="d8f60-468">ApplicationName</span></span>

<span data-ttu-id="d8f60-469">Die Eigenschaft [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) wird von der Hostkonfiguration während der Hosterstellung festgelegt.</span><span class="sxs-lookup"><span data-stu-id="d8f60-469">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="d8f60-470">**Schlüssel** : `applicationName`</span><span class="sxs-lookup"><span data-stu-id="d8f60-470">**Key** : `applicationName`</span></span>  
<span data-ttu-id="d8f60-471">**Typ:** `string`</span><span class="sxs-lookup"><span data-stu-id="d8f60-471">**Type** : `string`</span></span>  
<span data-ttu-id="d8f60-472">**Standard** : Der Name der Assembly, die den Einstiegspunkt der App enthält.</span><span class="sxs-lookup"><span data-stu-id="d8f60-472">**Default** : The name of the assembly that contains the app's entry point.</span></span>  
<span data-ttu-id="d8f60-473">**Umgebungsvariable:** `<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="d8f60-473">**Environment variable** : `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="d8f60-474">Verwenden Sie die Umgebungsvariable, um diesen Wert festzulegen.</span><span class="sxs-lookup"><span data-stu-id="d8f60-474">To set this value, use the environment variable.</span></span> 

### <a name="contentroot"></a><span data-ttu-id="d8f60-475">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="d8f60-475">ContentRoot</span></span>

<span data-ttu-id="d8f60-476">Die Eigenschaft [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) bestimmt, wo der Host mit der Suche nach Inhaltsdateien beginnt.</span><span class="sxs-lookup"><span data-stu-id="d8f60-476">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="d8f60-477">Wenn der Pfad nicht vorhanden ist, kann der Host nicht gestartet werden.</span><span class="sxs-lookup"><span data-stu-id="d8f60-477">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="d8f60-478">**Schlüssel** : `contentRoot`</span><span class="sxs-lookup"><span data-stu-id="d8f60-478">**Key** : `contentRoot`</span></span>  
<span data-ttu-id="d8f60-479">**Typ:** `string`</span><span class="sxs-lookup"><span data-stu-id="d8f60-479">**Type** : `string`</span></span>  
<span data-ttu-id="d8f60-480">**Standard** : Der Ordner, in dem die App-Assembly gespeichert ist.</span><span class="sxs-lookup"><span data-stu-id="d8f60-480">**Default** : The folder where the app assembly resides.</span></span>  
<span data-ttu-id="d8f60-481">**Umgebungsvariable:** `<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="d8f60-481">**Environment variable** : `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="d8f60-482">Verwenden Sie die Umgebungsvariable, oder rufen Sie `UseContentRoot` für `IHostBuilder` auf, um diesen Wert festzulegen:</span><span class="sxs-lookup"><span data-stu-id="d8f60-482">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="d8f60-483">Weitere Informationen finden Sie unter:</span><span class="sxs-lookup"><span data-stu-id="d8f60-483">For more information, see:</span></span>

* [<span data-ttu-id="d8f60-484">Grundlagen: Inhaltsstammverzeichnis</span><span class="sxs-lookup"><span data-stu-id="d8f60-484">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="d8f60-485">WebRoot</span><span class="sxs-lookup"><span data-stu-id="d8f60-485">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="d8f60-486">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="d8f60-486">EnvironmentName</span></span>

<span data-ttu-id="d8f60-487">Die Eigenschaft [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) kann auf einen beliebigen Wert festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="d8f60-487">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="d8f60-488">Zu den durch Frameworks definierten Werten zählen `Development`, `Staging` und `Production`.</span><span class="sxs-lookup"><span data-stu-id="d8f60-488">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="d8f60-489">Die Groß-/Kleinschreibung wird bei Werten nicht beachtet.</span><span class="sxs-lookup"><span data-stu-id="d8f60-489">Values aren't case-sensitive.</span></span>

<span data-ttu-id="d8f60-490">**Schlüssel** : `environment`</span><span class="sxs-lookup"><span data-stu-id="d8f60-490">**Key** : `environment`</span></span>  
<span data-ttu-id="d8f60-491">**Typ:** `string`</span><span class="sxs-lookup"><span data-stu-id="d8f60-491">**Type** : `string`</span></span>  
<span data-ttu-id="d8f60-492">**Standard** : `Production`</span><span class="sxs-lookup"><span data-stu-id="d8f60-492">**Default** : `Production`</span></span>  
<span data-ttu-id="d8f60-493">**Umgebungsvariable:** `<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="d8f60-493">**Environment variable** : `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="d8f60-494">Verwenden Sie die Umgebungsvariable, oder rufen Sie `UseEnvironment` für `IHostBuilder` auf, um diesen Wert festzulegen:</span><span class="sxs-lookup"><span data-stu-id="d8f60-494">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="d8f60-495">ShutdownTimeout</span><span class="sxs-lookup"><span data-stu-id="d8f60-495">ShutdownTimeout</span></span>

<span data-ttu-id="d8f60-496">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) legt das Zeitlimit für <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> fest.</span><span class="sxs-lookup"><span data-stu-id="d8f60-496">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="d8f60-497">Der Standardwert beträgt fünf Sekunden.</span><span class="sxs-lookup"><span data-stu-id="d8f60-497">The default value is five seconds.</span></span>  <span data-ttu-id="d8f60-498">Während des Zeitlimits verhält sich der Host folgendermaßen:</span><span class="sxs-lookup"><span data-stu-id="d8f60-498">During the timeout period, the host:</span></span>

* <span data-ttu-id="d8f60-499">Er löst [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping) aus.</span><span class="sxs-lookup"><span data-stu-id="d8f60-499">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="d8f60-500">Er versucht, gehostete Dienste zu beenden und protokolliert Fehler für Dienste, die nicht beendet werden können.</span><span class="sxs-lookup"><span data-stu-id="d8f60-500">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="d8f60-501">Wenn das Zeitlimit abläuft bevor alle gehosteten Dienste beendet sind, werden alle verbleibenden aktiven Dienste beendet, wenn die App herunterfährt.</span><span class="sxs-lookup"><span data-stu-id="d8f60-501">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="d8f60-502">Die Dienste werden beendet, selbst wenn die Verarbeitung noch nicht abgeschlossen ist.</span><span class="sxs-lookup"><span data-stu-id="d8f60-502">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="d8f60-503">Wenn die Dienste mehr Zeit zum Beenden benötigen, sollten Sie das Zeitlimit erhöhen.</span><span class="sxs-lookup"><span data-stu-id="d8f60-503">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="d8f60-504">**Schlüssel** : `shutdownTimeoutSeconds`</span><span class="sxs-lookup"><span data-stu-id="d8f60-504">**Key** : `shutdownTimeoutSeconds`</span></span>  
<span data-ttu-id="d8f60-505">**Typ:** `int`</span><span class="sxs-lookup"><span data-stu-id="d8f60-505">**Type** : `int`</span></span>  
<span data-ttu-id="d8f60-506">**Standard** : 5 Sekunden</span><span class="sxs-lookup"><span data-stu-id="d8f60-506">**Default** : 5 seconds</span></span>  
<span data-ttu-id="d8f60-507">**Umgebungsvariable:** `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="d8f60-507">**Environment variable** : `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="d8f60-508">Verwenden Sie die Umgebungsvariable, oder konfigurieren Sie `HostOptions`, um diesen Wert festzulegen.</span><span class="sxs-lookup"><span data-stu-id="d8f60-508">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="d8f60-509">Im folgenden Beispiel wird das Zeitlimit auf 20 Sekunden festgelegt:</span><span class="sxs-lookup"><span data-stu-id="d8f60-509">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

## <a name="settings-for-web-apps"></a><span data-ttu-id="d8f60-510">Einstellungen für Web-Apps</span><span class="sxs-lookup"><span data-stu-id="d8f60-510">Settings for web apps</span></span>

<span data-ttu-id="d8f60-511">Einige Hosteinstellungen gelten nur für HTTP-Workloads.</span><span class="sxs-lookup"><span data-stu-id="d8f60-511">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="d8f60-512">Standardmäßig können Umgebungsvariablen, die zur Konfiguration dieser Einstellungen verwendet werden, ein `DOTNET_`- oder `ASPNETCORE_`-Präfix haben.</span><span class="sxs-lookup"><span data-stu-id="d8f60-512">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="d8f60-513">Für diese Einstellungen sind Erweiterungsmethoden in `IWebHostBuilder` verfügbar.</span><span class="sxs-lookup"><span data-stu-id="d8f60-513">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="d8f60-514">In den Codebeispielen, die zeigen, wie Sie die Erweiterungsmethoden aufrufen können, wird davon ausgegangen, dass `webBuilder` eine Instanz von `IWebHostBuilder` ist. Sehen Sie hierzu das folgende Beispiel:</span><span class="sxs-lookup"><span data-stu-id="d8f60-514">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="d8f60-515">CaptureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="d8f60-515">CaptureStartupErrors</span></span>

<span data-ttu-id="d8f60-516">Wenn diese `false` ist, führen Fehler beim Start zum Beenden des Hosts.</span><span class="sxs-lookup"><span data-stu-id="d8f60-516">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="d8f60-517">Wenn diese `true` ist, erfasst der Host Ausnahmen während des Starts und versucht, den Server zu starten.</span><span class="sxs-lookup"><span data-stu-id="d8f60-517">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="d8f60-518">**Schlüssel** : `captureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="d8f60-518">**Key** : `captureStartupErrors`</span></span>  
<span data-ttu-id="d8f60-519">**Typ:** `bool` (`true` oder `1`)</span><span class="sxs-lookup"><span data-stu-id="d8f60-519">**Type** : `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="d8f60-520">**Standard** : Die Standardeinstellung ist gleich `false`, es sei denn, die App wird mit Kestrel hinter IIS ausgeführt, dann ist sie gleich `true`.</span><span class="sxs-lookup"><span data-stu-id="d8f60-520">**Default** : Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="d8f60-521">**Umgebungsvariable:** `<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="d8f60-521">**Environment variable** : `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="d8f60-522">Verwenden Sie die Konfiguration, oder rufen Sie `CaptureStartupErrors` auf, um diesen Wert festzulegen:</span><span class="sxs-lookup"><span data-stu-id="d8f60-522">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="d8f60-523">DetailedErrors</span><span class="sxs-lookup"><span data-stu-id="d8f60-523">DetailedErrors</span></span>

<span data-ttu-id="d8f60-524">Wenn diese Einstellung aktiviert ist oder die Umgebung auf `Development` festgelegt ist, erfasst die App detaillierte Fehler.</span><span class="sxs-lookup"><span data-stu-id="d8f60-524">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="d8f60-525">**Schlüssel** : `detailedErrors`</span><span class="sxs-lookup"><span data-stu-id="d8f60-525">**Key** : `detailedErrors`</span></span>  
<span data-ttu-id="d8f60-526">**Typ:** `bool` (`true` oder `1`)</span><span class="sxs-lookup"><span data-stu-id="d8f60-526">**Type** : `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="d8f60-527">**Standard** : `false`</span><span class="sxs-lookup"><span data-stu-id="d8f60-527">**Default** : `false`</span></span>  
<span data-ttu-id="d8f60-528">**Umgebungsvariable:** `<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="d8f60-528">**Environment variable** : `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="d8f60-529">Verwenden Sie die Konfiguration, oder rufen Sie `UseSetting` auf, um diesen Wert festzulegen:</span><span class="sxs-lookup"><span data-stu-id="d8f60-529">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="d8f60-530">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="d8f60-530">HostingStartupAssemblies</span></span>

<span data-ttu-id="d8f60-531">Eine durch Semikolons getrennte Zeichenfolge der Hostingstartassemblys, die beim Start geladen werden soll.</span><span class="sxs-lookup"><span data-stu-id="d8f60-531">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="d8f60-532">Obwohl der Konfigurationswert standardmäßig auf eine leere Zeichenfolge festgelegt ist, enthalten die Hostingstartassemblys immer die Assembly der App.</span><span class="sxs-lookup"><span data-stu-id="d8f60-532">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="d8f60-533">Wenn Hostingstartassemblys bereitgestellt werden, werden diese zur Assembly der App hinzugefügt, damit diese geladen werden, wenn die App während des Starts die allgemeinen Dienste erstellt.</span><span class="sxs-lookup"><span data-stu-id="d8f60-533">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="d8f60-534">**Schlüssel** : `hostingStartupAssemblies`</span><span class="sxs-lookup"><span data-stu-id="d8f60-534">**Key** : `hostingStartupAssemblies`</span></span>  
<span data-ttu-id="d8f60-535">**Typ:** `string`</span><span class="sxs-lookup"><span data-stu-id="d8f60-535">**Type** : `string`</span></span>  
<span data-ttu-id="d8f60-536">**Standard** : Leere Zeichenfolge</span><span class="sxs-lookup"><span data-stu-id="d8f60-536">**Default** : Empty string</span></span>  
<span data-ttu-id="d8f60-537">**Umgebungsvariable:** `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="d8f60-537">**Environment variable** : `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="d8f60-538">Verwenden Sie die Konfiguration, oder rufen Sie `UseSetting` auf, um diesen Wert festzulegen:</span><span class="sxs-lookup"><span data-stu-id="d8f60-538">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="d8f60-539">HostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="d8f60-539">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="d8f60-540">Eine durch Semikolons getrennte Zeichenfolge der Hostingstartassemblys, die beim Start ausgeschlossen werden sollen.</span><span class="sxs-lookup"><span data-stu-id="d8f60-540">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="d8f60-541">**Schlüssel** : `hostingStartupExcludeAssemblies`</span><span class="sxs-lookup"><span data-stu-id="d8f60-541">**Key** : `hostingStartupExcludeAssemblies`</span></span>  
<span data-ttu-id="d8f60-542">**Typ:** `string`</span><span class="sxs-lookup"><span data-stu-id="d8f60-542">**Type** : `string`</span></span>  
<span data-ttu-id="d8f60-543">**Standard** : Leere Zeichenfolge</span><span class="sxs-lookup"><span data-stu-id="d8f60-543">**Default** : Empty string</span></span>  
<span data-ttu-id="d8f60-544">**Umgebungsvariable:** `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="d8f60-544">**Environment variable** : `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="d8f60-545">Verwenden Sie die Konfiguration, oder rufen Sie `UseSetting` auf, um diesen Wert festzulegen:</span><span class="sxs-lookup"><span data-stu-id="d8f60-545">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="d8f60-546">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="d8f60-546">HTTPS_Port</span></span>

<span data-ttu-id="d8f60-547">Der HTTPS-Umleitungsport.</span><span class="sxs-lookup"><span data-stu-id="d8f60-547">The HTTPS redirect port.</span></span> <span data-ttu-id="d8f60-548">Wird in [Erzwingen von HTTPS](xref:security/enforcing-ssl) verwendet.</span><span class="sxs-lookup"><span data-stu-id="d8f60-548">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="d8f60-549">**Schlüssel** : `https_port`</span><span class="sxs-lookup"><span data-stu-id="d8f60-549">**Key** : `https_port`</span></span>  
<span data-ttu-id="d8f60-550">**Typ:** `string`</span><span class="sxs-lookup"><span data-stu-id="d8f60-550">**Type** : `string`</span></span>  
<span data-ttu-id="d8f60-551">**Standard** : Es ist kein Standardwert festgelegt.</span><span class="sxs-lookup"><span data-stu-id="d8f60-551">**Default** : A default value isn't set.</span></span>  
<span data-ttu-id="d8f60-552">**Umgebungsvariable:** `<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="d8f60-552">**Environment variable** : `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="d8f60-553">Verwenden Sie die Konfiguration, oder rufen Sie `UseSetting` auf, um diesen Wert festzulegen:</span><span class="sxs-lookup"><span data-stu-id="d8f60-553">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="d8f60-554">PreferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="d8f60-554">PreferHostingUrls</span></span>

<span data-ttu-id="d8f60-555">Gibt an, ob der Host auf die URLs lauschen soll, die mit `IWebHostBuilder` konfiguriert wurden, anstatt auf die URLs zu lauschen, die mit der `IServer`-Implementierung konfiguriert wurden.</span><span class="sxs-lookup"><span data-stu-id="d8f60-555">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those URLs configured with the `IServer` implementation.</span></span>

<span data-ttu-id="d8f60-556">**Schlüssel** : `preferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="d8f60-556">**Key** : `preferHostingUrls`</span></span>  
<span data-ttu-id="d8f60-557">**Typ:** `bool` (`true` oder `1`)</span><span class="sxs-lookup"><span data-stu-id="d8f60-557">**Type** : `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="d8f60-558">**Standard** : `true`</span><span class="sxs-lookup"><span data-stu-id="d8f60-558">**Default** : `true`</span></span>  
<span data-ttu-id="d8f60-559">**Umgebungsvariable:** `<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="d8f60-559">**Environment variable** : `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="d8f60-560">Verwenden Sie die Umgebungsvariable, oder rufen Sie `PreferHostingUrls` auf, um diesen Wert festzulegen:</span><span class="sxs-lookup"><span data-stu-id="d8f60-560">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="d8f60-561">PreventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="d8f60-561">PreventHostingStartup</span></span>

<span data-ttu-id="d8f60-562">Verhindert das automatische Laden der Hostingstartassemblys, einschließlich denen, die von der Assembly der App konfiguriert wurden.</span><span class="sxs-lookup"><span data-stu-id="d8f60-562">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="d8f60-563">Weitere Informationen finden Sie unter <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="d8f60-563">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="d8f60-564">**Schlüssel** : `preventHostingStartup`</span><span class="sxs-lookup"><span data-stu-id="d8f60-564">**Key** : `preventHostingStartup`</span></span>  
<span data-ttu-id="d8f60-565">**Typ:** `bool` (`true` oder `1`)</span><span class="sxs-lookup"><span data-stu-id="d8f60-565">**Type** : `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="d8f60-566">**Standard** : `false`</span><span class="sxs-lookup"><span data-stu-id="d8f60-566">**Default** : `false`</span></span>  
<span data-ttu-id="d8f60-567">**Umgebungsvariable:** `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="d8f60-567">**Environment variable** : `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="d8f60-568">Verwenden Sie die Umgebungsvariable, oder rufen Sie `UseSetting` auf, um diesen Wert festzulegen:</span><span class="sxs-lookup"><span data-stu-id="d8f60-568">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="d8f60-569">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="d8f60-569">StartupAssembly</span></span>

<span data-ttu-id="d8f60-570">Die Assembly, die nach der `Startup`-Klasse suchen soll.</span><span class="sxs-lookup"><span data-stu-id="d8f60-570">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="d8f60-571">**Schlüssel** : `startupAssembly`</span><span class="sxs-lookup"><span data-stu-id="d8f60-571">**Key** : `startupAssembly`</span></span>  
<span data-ttu-id="d8f60-572">**Typ:** `string`</span><span class="sxs-lookup"><span data-stu-id="d8f60-572">**Type** : `string`</span></span>  
<span data-ttu-id="d8f60-573">**Standard** : Die Assembly der App</span><span class="sxs-lookup"><span data-stu-id="d8f60-573">**Default** : The app's assembly</span></span>  
<span data-ttu-id="d8f60-574">**Umgebungsvariable:** `<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="d8f60-574">**Environment variable** : `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="d8f60-575">Verwenden Sie die Umgebungsvariable, oder rufen Sie `UseStartup` auf, um diesen Wert festzulegen.</span><span class="sxs-lookup"><span data-stu-id="d8f60-575">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="d8f60-576">`UseStartup` kann einen Assemblynamen (`string`) oder einen Typ (`TStartup`) annehmen.</span><span class="sxs-lookup"><span data-stu-id="d8f60-576">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="d8f60-577">Wenn mehrere `UseStartup`-Methoden aufgerufen werden, hat die letzte Vorrang.</span><span class="sxs-lookup"><span data-stu-id="d8f60-577">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="d8f60-578">URLs</span><span class="sxs-lookup"><span data-stu-id="d8f60-578">URLs</span></span>

<span data-ttu-id="d8f60-579">Eine durch Semikolons getrennte Liste mit IP-Adressen oder Hostadressen mit Ports und Protokollen, denen der Server für Anforderungen lauschen soll.</span><span class="sxs-lookup"><span data-stu-id="d8f60-579">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="d8f60-580">Beispielsweise `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="d8f60-580">For example, `http://localhost:123`.</span></span> <span data-ttu-id="d8f60-581">Verwenden Sie \*, um anzugeben, dass der Server mithilfe des angegebenen Ports und Protokolls (z.B. `http://*:5000`) den Anfragen aller IP-Adressen oder Hostnamen lauschen soll.</span><span class="sxs-lookup"><span data-stu-id="d8f60-581">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="d8f60-582">Das Protokoll (`http://` oder `https://`) muss in jeder URL enthalten sein.</span><span class="sxs-lookup"><span data-stu-id="d8f60-582">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="d8f60-583">Die unterstützten Formate variieren bei den verschiedenen Servern.</span><span class="sxs-lookup"><span data-stu-id="d8f60-583">Supported formats vary among servers.</span></span>

<span data-ttu-id="d8f60-584">**Schlüssel** : `urls`</span><span class="sxs-lookup"><span data-stu-id="d8f60-584">**Key** : `urls`</span></span>  
<span data-ttu-id="d8f60-585">**Typ:** `string`</span><span class="sxs-lookup"><span data-stu-id="d8f60-585">**Type** : `string`</span></span>  
<span data-ttu-id="d8f60-586">**Standard** : `http://localhost:5000` und `https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="d8f60-586">**Default** : `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="d8f60-587">**Umgebungsvariable:** `<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="d8f60-587">**Environment variable** : `<PREFIX_>URLS`</span></span>

<span data-ttu-id="d8f60-588">Verwenden Sie die Umgebungsvariable, oder rufen Sie `UseUrls` auf, um diesen Wert festzulegen:</span><span class="sxs-lookup"><span data-stu-id="d8f60-588">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="d8f60-589">Kestrel verfügt über eine eigene API für die Endpunktkonfiguration.</span><span class="sxs-lookup"><span data-stu-id="d8f60-589">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="d8f60-590">Weitere Informationen finden Sie unter <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="d8f60-590">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="d8f60-591">WebRoot</span><span class="sxs-lookup"><span data-stu-id="d8f60-591">WebRoot</span></span>

<span data-ttu-id="d8f60-592">Die [IWebHostEnvironment.WebRootPath-](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath)-Eigenschaft bestimmt den relativen Pfad zu den statischen Objekten der App.</span><span class="sxs-lookup"><span data-stu-id="d8f60-592">The [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) property determines the relative path to the app's static assets.</span></span> <span data-ttu-id="d8f60-593">Wenn der Pfad nicht vorhanden ist, wird ein Dateianbieter ohne Funktion verwendet.</span><span class="sxs-lookup"><span data-stu-id="d8f60-593">If the path doesn't exist, a no-op file provider is used.</span></span>  

<span data-ttu-id="d8f60-594">**Schlüssel** : `webroot`</span><span class="sxs-lookup"><span data-stu-id="d8f60-594">**Key** : `webroot`</span></span>  
<span data-ttu-id="d8f60-595">**Typ:** `string`</span><span class="sxs-lookup"><span data-stu-id="d8f60-595">**Type** : `string`</span></span>  
<span data-ttu-id="d8f60-596">**Standard** : Der Standardwert ist `wwwroot`.</span><span class="sxs-lookup"><span data-stu-id="d8f60-596">**Default** : The default is `wwwroot`.</span></span> <span data-ttu-id="d8f60-597">Der Pfad zu *{Inhaltsstammverzeichnis}/wwwroot* muss vorhanden sein.</span><span class="sxs-lookup"><span data-stu-id="d8f60-597">The path to *{content root}/wwwroot* must exist.</span></span>  
<span data-ttu-id="d8f60-598">**Umgebungsvariable:** `<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="d8f60-598">**Environment variable** : `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="d8f60-599">Verwenden Sie die Umgebungsvariable, oder rufen Sie `UseWebRoot` für `IWebHostBuilder` auf, um diesen Wert festzulegen:</span><span class="sxs-lookup"><span data-stu-id="d8f60-599">To set this value, use the environment variable or call `UseWebRoot` on `IWebHostBuilder`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="d8f60-600">Weitere Informationen finden Sie unter:</span><span class="sxs-lookup"><span data-stu-id="d8f60-600">For more information, see:</span></span>

* [<span data-ttu-id="d8f60-601">Grundlagen: Webstammverzeichnis</span><span class="sxs-lookup"><span data-stu-id="d8f60-601">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="d8f60-602">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="d8f60-602">ContentRoot</span></span>](#contentroot)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="d8f60-603">Verwalten der Lebensdauer des Hosts</span><span class="sxs-lookup"><span data-stu-id="d8f60-603">Manage the host lifetime</span></span>

<span data-ttu-id="d8f60-604">Rufen Sie Methoden für die erstellte <xref:Microsoft.Extensions.Hosting.IHost>-Implementierung auf, um die App zu starten und zu beenden.</span><span class="sxs-lookup"><span data-stu-id="d8f60-604">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="d8f60-605">Diese Methoden wirken sich auf alle <xref:Microsoft.Extensions.Hosting.IHostedService>-Implementierungen aus, die im Dienstcontainer registriert sind.</span><span class="sxs-lookup"><span data-stu-id="d8f60-605">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="d8f60-606">Run</span><span class="sxs-lookup"><span data-stu-id="d8f60-606">Run</span></span>

<span data-ttu-id="d8f60-607">Durch <xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> wird die App gestartet und der aufrufende Thread blockiert, bis der Host heruntergefahren wird.</span><span class="sxs-lookup"><span data-stu-id="d8f60-607"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="d8f60-608">RunAsync</span><span class="sxs-lookup"><span data-stu-id="d8f60-608">RunAsync</span></span>

<span data-ttu-id="d8f60-609">Durch <xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> wird die App ausgeführt und eine <xref:System.Threading.Tasks.Task>-Methode ausgegeben, die abgeschlossen wird, wenn das Abbruchtoken oder das Herunterfahren ausgelöst wird.</span><span class="sxs-lookup"><span data-stu-id="d8f60-609"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="d8f60-610">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="d8f60-610">RunConsoleAsync</span></span>

<span data-ttu-id="d8f60-611"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> aktiviert die Unterstützung der Konsole, erstellt und startet den Host und lauscht auf <kbd>STRG</kbd>+<kbd>C</kbd>/SIGINT oder SIGTERM, um das Herunterfahren auszulösen.</span><span class="sxs-lookup"><span data-stu-id="d8f60-611"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="d8f60-612">Starten</span><span class="sxs-lookup"><span data-stu-id="d8f60-612">Start</span></span>

<span data-ttu-id="d8f60-613"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> startet den Host synchron.</span><span class="sxs-lookup"><span data-stu-id="d8f60-613"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="d8f60-614">StartAsync</span><span class="sxs-lookup"><span data-stu-id="d8f60-614">StartAsync</span></span>

<span data-ttu-id="d8f60-615">Durch <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> wird der Host gestartet und eine <xref:System.Threading.Tasks.Task>-Methode ausgegeben, die abgeschlossen wird, wenn das Abbruchtoken oder das Herunterfahren ausgelöst wird.</span><span class="sxs-lookup"><span data-stu-id="d8f60-615"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="d8f60-616"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> wird am Anfang der `StartAsync`-Methode aufgerufen, die den Vorgang erst fortsetzt, wenn sie abgeschlossen ist.</span><span class="sxs-lookup"><span data-stu-id="d8f60-616"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="d8f60-617">Dies kann verwendet werden, um den Start zu verzögern, bis dieser durch ein externes Ereignis initiiert wird.</span><span class="sxs-lookup"><span data-stu-id="d8f60-617">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="d8f60-618">StopAsync</span><span class="sxs-lookup"><span data-stu-id="d8f60-618">StopAsync</span></span>

<span data-ttu-id="d8f60-619"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> versucht, den Host innerhalb des angegebenen Timeouts zu beenden.</span><span class="sxs-lookup"><span data-stu-id="d8f60-619"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="d8f60-620">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="d8f60-620">WaitForShutdown</span></span>

<span data-ttu-id="d8f60-621"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blockiert den aufrufenden Thread, bis das Herunterfahren durch den IHostLifetime, z. B. über <kbd>STRG</kbd>+<kbd>C</kbd>/SIGINT oder SIGTERM, ausgelöst wird.</span><span class="sxs-lookup"><span data-stu-id="d8f60-621"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="d8f60-622">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="d8f60-622">WaitForShutdownAsync</span></span>

<span data-ttu-id="d8f60-623"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> gibt eine <xref:System.Threading.Tasks.Task>-Methode zurück, die abgeschlossen wird, wenn das Herunterfahren über das angegebene Token ausgelöst wird, und ruft <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> auf.</span><span class="sxs-lookup"><span data-stu-id="d8f60-623"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="d8f60-624">Externe Steuerung</span><span class="sxs-lookup"><span data-stu-id="d8f60-624">External control</span></span>

<span data-ttu-id="d8f60-625">Die Lebensdauer des Hosts kann mithilfe von Methoden, die extern aufgerufen werden können, direkt gesteuert werden:</span><span class="sxs-lookup"><span data-stu-id="d8f60-625">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

```csharp
public class Program
{
    private IHost _host;

    public Program()
    {
        _host = new HostBuilder()
            .Build();
    }

    public async Task StartAsync()
    {
        _host.StartAsync();
    }

    public async Task StopAsync()
    {
        using (_host)
        {
            await _host.StopAsync(TimeSpan.FromSeconds(5));
        }
    }
}
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="d8f60-626">Durch ASP.NET Core-Apps kann ein Host gestartet und konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="d8f60-626">ASP.NET Core apps configure and launch a host.</span></span> <span data-ttu-id="d8f60-627">Der Host ist verantwortlich für das Starten der App und das Verwalten der Lebensdauer.</span><span class="sxs-lookup"><span data-stu-id="d8f60-627">The host is responsible for app startup and lifetime management.</span></span>

<span data-ttu-id="d8f60-628">In diesem Artikel wird der generische ASP.NET Core-Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>) erläutert, der für das Hosten von Apps verwendet wird, die keine HTTP-Anforderungen verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="d8f60-628">This article covers the ASP.NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>), which is used for apps that don't process HTTP requests.</span></span>

<span data-ttu-id="d8f60-629">Das Ziel des generischen Hosts besteht darin, die HTTP-Pipeline von der Webhost-API zu entkoppeln, um mehr Hostszenarios zu ermöglichen.</span><span class="sxs-lookup"><span data-stu-id="d8f60-629">The purpose of Generic Host is to decouple the HTTP pipeline from the Web Host API to enable a wider array of host scenarios.</span></span> <span data-ttu-id="d8f60-630">Messaging, Hintergrundtasks und andere Nicht-HTTP-Workloads, die auf dem generischen Host basieren, profitieren von übergreifenden Funktionen wie der Konfiguration, Dependency Injection (DI) und der Protokollerstellung.</span><span class="sxs-lookup"><span data-stu-id="d8f60-630">Messaging, background tasks, and other non-HTTP workloads based on Generic Host benefit from cross-cutting capabilities, such as configuration, dependency injection (DI), and logging.</span></span>

<span data-ttu-id="d8f60-631">Der generische Host ist neu in ASP.NET Core 2.1 und ist nicht für Webhostingszenarios geeignet.</span><span class="sxs-lookup"><span data-stu-id="d8f60-631">Generic Host is new in ASP.NET Core 2.1 and isn't suitable for web hosting scenarios.</span></span> <span data-ttu-id="d8f60-632">Verwenden Sie den [Webhost](xref:fundamentals/host/web-host) für Webhostingszenarios.</span><span class="sxs-lookup"><span data-stu-id="d8f60-632">For web hosting scenarios, use the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="d8f60-633">Der generische Host wird den Webhost in einem zukünftigen Release ersetzen und als primäre Host-API in HTTP- und Nicht-HTTP-Szenarios fungieren.</span><span class="sxs-lookup"><span data-stu-id="d8f60-633">Generic Host will replace Web Host in a future release and act as the primary host API in both HTTP and non-HTTP scenarios.</span></span>

<span data-ttu-id="d8f60-634">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d8f60-634">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="d8f60-635">Wenn Sie die Beispiel-App in [Visual Studio Code](https://code.visualstudio.com/) ausführen, verwenden Sie ein *externes oder integriertes Terminal*.</span><span class="sxs-lookup"><span data-stu-id="d8f60-635">When running the sample app in [Visual Studio Code](https://code.visualstudio.com/), use an *external or integrated terminal*.</span></span> <span data-ttu-id="d8f60-636">Führen Sie das Beispiel nicht in `internalConsole` aus.</span><span class="sxs-lookup"><span data-stu-id="d8f60-636">Don't run the sample in an `internalConsole`.</span></span>

<span data-ttu-id="d8f60-637">So legen Sie die Konsole in Visual Studio Code fest:</span><span class="sxs-lookup"><span data-stu-id="d8f60-637">To set the console in Visual Studio Code:</span></span>

1. <span data-ttu-id="d8f60-638">Öffnen Sie die Datei *.vscode/launch.json*.</span><span class="sxs-lookup"><span data-stu-id="d8f60-638">Open the *.vscode/launch.json* file.</span></span>
1. <span data-ttu-id="d8f60-639">Suchen Sie in der Konfiguration **.NET Core-Start (Konsole)** den Eintrag **Konsole**.</span><span class="sxs-lookup"><span data-stu-id="d8f60-639">In the **.NET Core Launch (console)** configuration, locate the **console** entry.</span></span> <span data-ttu-id="d8f60-640">Legen Sie den Wert auf `externalTerminal` oder `integratedTerminal` fest.</span><span class="sxs-lookup"><span data-stu-id="d8f60-640">Set the value to either `externalTerminal` or `integratedTerminal`.</span></span>

## <a name="introduction"></a><span data-ttu-id="d8f60-641">Einführung</span><span class="sxs-lookup"><span data-stu-id="d8f60-641">Introduction</span></span>

<span data-ttu-id="d8f60-642">Die generische Hostbibliothek ist im Namespace <xref:Microsoft.Extensions.Hosting> verfügbar und wird vom Paket [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="d8f60-642">The Generic Host library is available in the <xref:Microsoft.Extensions.Hosting> namespace and provided by the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) package.</span></span> <span data-ttu-id="d8f60-643">Das Paket `Microsoft.Extensions.Hosting` ist im Metapaket [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 oder höher) enthalten.</span><span class="sxs-lookup"><span data-stu-id="d8f60-643">The `Microsoft.Extensions.Hosting` package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or later).</span></span>

<span data-ttu-id="d8f60-644"><xref:Microsoft.Extensions.Hosting.IHostedService> ist der Einstiegspunkt für die Ausführung des Codes.</span><span class="sxs-lookup"><span data-stu-id="d8f60-644"><xref:Microsoft.Extensions.Hosting.IHostedService> is the entry point to code execution.</span></span> <span data-ttu-id="d8f60-645">Jede Implementierung von <xref:Microsoft.Extensions.Hosting.IHostedService> wird in der Reihenfolge der [Dienstregistrierung in ConfigureServices](#configureservices) ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="d8f60-645">Each <xref:Microsoft.Extensions.Hosting.IHostedService> implementation is executed in the order of [service registration in ConfigureServices](#configureservices).</span></span> <span data-ttu-id="d8f60-646"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> wird in jeder <xref:Microsoft.Extensions.Hosting.IHostedService>-Schnittstelle aufgerufen, wenn der Host gestartet wird, und <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> wird in umgekehrter Registrierungsreihenfolge aufgerufen, wenn der Host ordnungsgemäß heruntergefahren wird.</span><span class="sxs-lookup"><span data-stu-id="d8f60-646"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> is called on each <xref:Microsoft.Extensions.Hosting.IHostedService> when the host starts, and <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> is called in reverse registration order when the host shuts down gracefully.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="d8f60-647">Einrichten eines Hosts</span><span class="sxs-lookup"><span data-stu-id="d8f60-647">Set up a host</span></span>

<span data-ttu-id="d8f60-648"><xref:Microsoft.Extensions.Hosting.IHostBuilder> ist die Hauptkomponente, die Bibliotheken und Apps für die Initialisierung, Erstellung und Ausführung des Hosts verwenden:</span><span class="sxs-lookup"><span data-stu-id="d8f60-648"><xref:Microsoft.Extensions.Hosting.IHostBuilder> is the main component that libraries and apps use to initialize, build, and run the host:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_HostBuilder)]

## <a name="options"></a><span data-ttu-id="d8f60-649">Optionen</span><span class="sxs-lookup"><span data-stu-id="d8f60-649">Options</span></span>

<span data-ttu-id="d8f60-650"><xref:Microsoft.Extensions.Hosting.HostOptions>-Konfigurationsoptionen für <xref:Microsoft.Extensions.Hosting.IHost>.</span><span class="sxs-lookup"><span data-stu-id="d8f60-650"><xref:Microsoft.Extensions.Hosting.HostOptions> configure options for the <xref:Microsoft.Extensions.Hosting.IHost>.</span></span>

### <a name="shutdown-timeout"></a><span data-ttu-id="d8f60-651">Timeout beim Herunterfahren</span><span class="sxs-lookup"><span data-stu-id="d8f60-651">Shutdown timeout</span></span>

<span data-ttu-id="d8f60-652"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> legt den Timeout für <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> fest.</span><span class="sxs-lookup"><span data-stu-id="d8f60-652"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="d8f60-653">Der Standardwert beträgt fünf Sekunden.</span><span class="sxs-lookup"><span data-stu-id="d8f60-653">The default value is five seconds.</span></span>

<span data-ttu-id="d8f60-654">Die folgende Optionskonfiguration in `Program.Main` erhöht den standardmäßigen Timeout beim Herunterfahren von 5 Sekunden auf 20 Sekunden:</span><span class="sxs-lookup"><span data-stu-id="d8f60-654">The following option configuration in `Program.Main` increases the default five-second shutdown timeout to 20 seconds:</span></span>

```csharp
var host = new HostBuilder()
    .ConfigureServices((hostContext, services) =>
    {
        services.Configure<HostOptions>(option =>
        {
            option.ShutdownTimeout = System.TimeSpan.FromSeconds(20);
        });
    })
    .Build();
```

## <a name="default-services"></a><span data-ttu-id="d8f60-655">Standarddienste</span><span class="sxs-lookup"><span data-stu-id="d8f60-655">Default services</span></span>

<span data-ttu-id="d8f60-656">Die folgenden Dienste werden bei der Hostinitialisierung registriert:</span><span class="sxs-lookup"><span data-stu-id="d8f60-656">The following services are registered during host initialization:</span></span>

* <span data-ttu-id="d8f60-657">[Umgebung](xref:fundamentals/environments) (<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>)</span><span class="sxs-lookup"><span data-stu-id="d8f60-657">[Environment](xref:fundamentals/environments) (<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>)</span></span>
* <xref:Microsoft.Extensions.Hosting.HostBuilderContext>
* <span data-ttu-id="d8f60-658">[Konfiguration](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>)</span><span class="sxs-lookup"><span data-stu-id="d8f60-658">[Configuration](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>)</span></span>
* <span data-ttu-id="d8f60-659"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span><span class="sxs-lookup"><span data-stu-id="d8f60-659"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span></span>
* <span data-ttu-id="d8f60-660"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span><span class="sxs-lookup"><span data-stu-id="d8f60-660"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span></span>
* <xref:Microsoft.Extensions.Hosting.IHost>
* <span data-ttu-id="d8f60-661">[Optionen](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)</span><span class="sxs-lookup"><span data-stu-id="d8f60-661">[Options](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)</span></span>
* <span data-ttu-id="d8f60-662">[Protokollierung](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)</span><span class="sxs-lookup"><span data-stu-id="d8f60-662">[Logging](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)</span></span>

## <a name="host-configuration"></a><span data-ttu-id="d8f60-663">Konfiguration des Hosts</span><span class="sxs-lookup"><span data-stu-id="d8f60-663">Host configuration</span></span>

<span data-ttu-id="d8f60-664">Die Konfiguration des Hosts wird durch Folgendes erstellt:</span><span class="sxs-lookup"><span data-stu-id="d8f60-664">Host configuration is created by:</span></span>

* <span data-ttu-id="d8f60-665">Aufrufen von Erweiterungsmethoden in <xref:Microsoft.Extensions.Hosting.IHostBuilder>, um das [Inhaltsstammverzeichnis](#content-root) und die [Umgebung](#environment) festzulegen</span><span class="sxs-lookup"><span data-stu-id="d8f60-665">Calling extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder> to set the [content root](#content-root) and [environment](#environment).</span></span>
* <span data-ttu-id="d8f60-666">Lesen der Konfiguration von Konfigurationsanbietern in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*></span><span class="sxs-lookup"><span data-stu-id="d8f60-666">Reading configuration from configuration providers in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>.</span></span>

### <a name="extension-methods"></a><span data-ttu-id="d8f60-667">Erweiterungsmethoden</span><span class="sxs-lookup"><span data-stu-id="d8f60-667">Extension methods</span></span>

### <a name="application-key-name"></a><span data-ttu-id="d8f60-668">Anwendungsschlüssel (Name)</span><span class="sxs-lookup"><span data-stu-id="d8f60-668">Application key (name)</span></span>

<span data-ttu-id="d8f60-669">Die Eigenschaft [IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) wird von der Hostkonfiguration während der Hostkonstruktion festgelegt.</span><span class="sxs-lookup"><span data-stu-id="d8f60-669">The [IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span> <span data-ttu-id="d8f60-670">Um den Wert explizit festzulegen, verwenden Sie den [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey):</span><span class="sxs-lookup"><span data-stu-id="d8f60-670">To set the value explicitly, use the [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey):</span></span>

<span data-ttu-id="d8f60-671">**Schlüssel** : `applicationName`</span><span class="sxs-lookup"><span data-stu-id="d8f60-671">**Key** : `applicationName`</span></span>  
<span data-ttu-id="d8f60-672">**Typ:** `string`</span><span class="sxs-lookup"><span data-stu-id="d8f60-672">**Type** : `string`</span></span>  
<span data-ttu-id="d8f60-673">**Standard** : Der Name der Assembly, die den Einstiegspunkt der App enthält.</span><span class="sxs-lookup"><span data-stu-id="d8f60-673">**Default** : The name of the assembly containing the app's entry point.</span></span>  
<span data-ttu-id="d8f60-674">**Festlegen mit:** `HostBuilderContext.HostingEnvironment.ApplicationName`</span><span class="sxs-lookup"><span data-stu-id="d8f60-674">**Set using** : `HostBuilderContext.HostingEnvironment.ApplicationName`</span></span>  
<span data-ttu-id="d8f60-675">**Umgebungsvariable:** `<PREFIX_>APPLICATIONNAME` (`<PREFIX_>` ist [optional und benutzerdefiniert](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="d8f60-675">**Environment variable** : `<PREFIX_>APPLICATIONNAME` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

### <a name="content-root"></a><span data-ttu-id="d8f60-676">Inhaltsstammverzeichnis</span><span class="sxs-lookup"><span data-stu-id="d8f60-676">Content root</span></span>

<span data-ttu-id="d8f60-677">Diese Einstellung bestimmt, wo der Host mit der Suche nach Inhaltsdateien beginnt.</span><span class="sxs-lookup"><span data-stu-id="d8f60-677">This setting determines where the host begins searching for content files.</span></span>

<span data-ttu-id="d8f60-678">**Schlüssel** : `contentRoot`</span><span class="sxs-lookup"><span data-stu-id="d8f60-678">**Key** : `contentRoot`</span></span>  
<span data-ttu-id="d8f60-679">**Typ:** `string`</span><span class="sxs-lookup"><span data-stu-id="d8f60-679">**Type** : `string`</span></span>  
<span data-ttu-id="d8f60-680">**Standard** : Entspricht standardmäßig dem Ordner, in dem die App-Assembly gespeichert ist.</span><span class="sxs-lookup"><span data-stu-id="d8f60-680">**Default** : Defaults to the folder where the app assembly resides.</span></span>  
<span data-ttu-id="d8f60-681">**Festlegen mit:** `UseContentRoot`</span><span class="sxs-lookup"><span data-stu-id="d8f60-681">**Set using** : `UseContentRoot`</span></span>  
<span data-ttu-id="d8f60-682">**Umgebungsvariable:** `<PREFIX_>CONTENTROOT` (`<PREFIX_>` ist [optional und benutzerdefiniert](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="d8f60-682">**Environment variable** : `<PREFIX_>CONTENTROOT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="d8f60-683">Wenn der Pfad nicht vorhanden ist, kann der Host nicht gestartet werden.</span><span class="sxs-lookup"><span data-stu-id="d8f60-683">If the path doesn't exist, the host fails to start.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseContentRoot)]

<span data-ttu-id="d8f60-684">Weitere Informationen finden Sie unter [Grundlagen: Inhaltsstammverzeichnis](xref:fundamentals/index#content-root).</span><span class="sxs-lookup"><span data-stu-id="d8f60-684">For more information, see [Fundamentals: Content root](xref:fundamentals/index#content-root).</span></span>

### <a name="environment"></a><span data-ttu-id="d8f60-685">Umgebung</span><span class="sxs-lookup"><span data-stu-id="d8f60-685">Environment</span></span>

<span data-ttu-id="d8f60-686">Legt die [Umgebung](xref:fundamentals/environments) der App fest.</span><span class="sxs-lookup"><span data-stu-id="d8f60-686">Sets the app's [environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="d8f60-687">**Schlüssel** : `environment`</span><span class="sxs-lookup"><span data-stu-id="d8f60-687">**Key** : `environment`</span></span>  
<span data-ttu-id="d8f60-688">**Typ:** `string`</span><span class="sxs-lookup"><span data-stu-id="d8f60-688">**Type** : `string`</span></span>  
<span data-ttu-id="d8f60-689">**Standard** : `Production`</span><span class="sxs-lookup"><span data-stu-id="d8f60-689">**Default** : `Production`</span></span>  
<span data-ttu-id="d8f60-690">**Festlegen mit:** `UseEnvironment`</span><span class="sxs-lookup"><span data-stu-id="d8f60-690">**Set using** : `UseEnvironment`</span></span>  
<span data-ttu-id="d8f60-691">**Umgebungsvariable:** `<PREFIX_>ENVIRONMENT` (`<PREFIX_>` ist [optional und benutzerdefiniert](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="d8f60-691">**Environment variable** : `<PREFIX_>ENVIRONMENT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="d8f60-692">Die Umgebung kann auf einen beliebigen Wert festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="d8f60-692">The environment can be set to any value.</span></span> <span data-ttu-id="d8f60-693">Zu den durch Frameworks definierten Werten zählen `Development`, `Staging` und `Production`.</span><span class="sxs-lookup"><span data-stu-id="d8f60-693">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="d8f60-694">Die Groß-/Kleinschreibung wird bei Werten nicht beachtet.</span><span class="sxs-lookup"><span data-stu-id="d8f60-694">Values aren't case-sensitive.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseEnvironment)]

### <a name="configurehostconfiguration"></a><span data-ttu-id="d8f60-695">ConfigureHostConfiguration</span><span class="sxs-lookup"><span data-stu-id="d8f60-695">ConfigureHostConfiguration</span></span>

<span data-ttu-id="d8f60-696"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> verwendet einen <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder>, um eine <xref:Microsoft.Extensions.Configuration.IConfiguration> für den Host zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="d8f60-696"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the host.</span></span> <span data-ttu-id="d8f60-697">Die Hostkonfiguration dient zum Initialisieren der <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> für die Verwendung im App-Buildprozesses.</span><span class="sxs-lookup"><span data-stu-id="d8f60-697">The host configuration is used to initialize the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> for use in the app's build process.</span></span>

<span data-ttu-id="d8f60-698"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> kann mehrfach mit additiven Ergebnissen aufgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="d8f60-698"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="d8f60-699">Der Host verwendet die Option, die zuletzt einen Wert für einen bestimmten Schlüssel festlegt.</span><span class="sxs-lookup"><span data-stu-id="d8f60-699">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="d8f60-700">Standardmäßig sind keine Anbieter enthalten.</span><span class="sxs-lookup"><span data-stu-id="d8f60-700">No providers are included by default.</span></span> <span data-ttu-id="d8f60-701">Sie müssen explizit angeben, welche Konfigurationsanbieter die App in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> erfordert, einschließlich Folgendes:</span><span class="sxs-lookup"><span data-stu-id="d8f60-701">You must explicitly specify whatever configuration providers the app requires in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>, including:</span></span>

* <span data-ttu-id="d8f60-702">Dateikonfiguration (z.B. von einer Datei namens *hostsettings.json* )</span><span class="sxs-lookup"><span data-stu-id="d8f60-702">File configuration (for example, from a *hostsettings.json* file).</span></span>
* <span data-ttu-id="d8f60-703">Konfiguration von Umgebungsvariablen</span><span class="sxs-lookup"><span data-stu-id="d8f60-703">Environment variable configuration.</span></span>
* <span data-ttu-id="d8f60-704">Konfiguration von Befehlszeilenargumenten</span><span class="sxs-lookup"><span data-stu-id="d8f60-704">Command-line argument configuration.</span></span>
* <span data-ttu-id="d8f60-705">Alle anderen erforderlichen Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="d8f60-705">Any other required configuration providers.</span></span>

<span data-ttu-id="d8f60-706">Die Dateikonfiguration des Hosts erfolgt durch Angabe des App-Basispfads mit `SetBasePath` gefolgt von einem Aufruf eines der [Dateikonfigurationsanbieter](xref:fundamentals/configuration/index#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="d8f60-706">File configuration of the host is enabled by specifying the app's base path with `SetBasePath` followed by a call to one of the [file configuration providers](xref:fundamentals/configuration/index#file-configuration-provider).</span></span> <span data-ttu-id="d8f60-707">Die Beispiel-App verwendet die JSON-Datei *hostsettings.json* und ruft <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> auf, um die Hostkonfigurationseinstellungen der Datei zu nutzen.</span><span class="sxs-lookup"><span data-stu-id="d8f60-707">The sample app uses a JSON file, *hostsettings.json* , and calls <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> to consume the file's host configuration settings.</span></span>

<span data-ttu-id="d8f60-708">Um die [Umgebungsvariablenkonfiguration](xref:fundamentals/configuration/index#environment-variables-configuration-provider) des Hosts hinzuzufügen, rufen Sie <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> im Host-Generator auf.</span><span class="sxs-lookup"><span data-stu-id="d8f60-708">To add [environment variable configuration](xref:fundamentals/configuration/index#environment-variables-configuration-provider) of the host, call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> on the host builder.</span></span> <span data-ttu-id="d8f60-709">`AddEnvironmentVariables` akzeptiert ein optionales benutzerdefiniertes Präfix.</span><span class="sxs-lookup"><span data-stu-id="d8f60-709">`AddEnvironmentVariables` accepts an optional user-defined prefix.</span></span> <span data-ttu-id="d8f60-710">Die Beispiel-App verwendet das Präfix `PREFIX_`.</span><span class="sxs-lookup"><span data-stu-id="d8f60-710">The sample app uses a prefix of `PREFIX_`.</span></span> <span data-ttu-id="d8f60-711">Das Präfix wird entfernt, wenn die Umgebungsvariablen gelesen werden.</span><span class="sxs-lookup"><span data-stu-id="d8f60-711">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="d8f60-712">Wenn der Host der Beispiel-App konfiguriert wird, wird der Wert der Umgebungsvariable für `PREFIX_ENVIRONMENT` der Hostkonfigurationswert für den `environment`-Schlüssel.</span><span class="sxs-lookup"><span data-stu-id="d8f60-712">When the sample app's host is configured, the environment variable value for `PREFIX_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="d8f60-713">Wenn Sie [Visual Studio](https://visualstudio.microsoft.com) oder eine App mit `dotnet run` während der Entwicklung verwenden, können Umgebungsvariablen in der Datei *Properties/launchSettings.json* festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="d8f60-713">During development when using [Visual Studio](https://visualstudio.microsoft.com) or running an app with `dotnet run`, environment variables may be set in the *Properties/launchSettings.json* file.</span></span> <span data-ttu-id="d8f60-714">In [Visual Studio Code](https://code.visualstudio.com/) können Umgebungsvariablen während der Entwicklung in der Datei *.vscode/launch.json* festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="d8f60-714">In [Visual Studio Code](https://code.visualstudio.com/), environment variables may be set in the *.vscode/launch.json* file during development.</span></span> <span data-ttu-id="d8f60-715">Weitere Informationen finden Sie unter <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="d8f60-715">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="d8f60-716">Die [Befehlszeilenkonfiguration](xref:fundamentals/configuration/index#command-line-configuration-provider) wird durch Aufrufen von <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="d8f60-716">[Command-line configuration](xref:fundamentals/configuration/index#command-line-configuration-provider) is added by calling <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span> <span data-ttu-id="d8f60-717">Die Befehlszeilenkonfiguration wird zuletzt hinzugefügt, damit Befehlszeilenargumente die Konfiguration überschreiben können, die von früheren Konfigurationsanbietern bereitgestellt wurden.</span><span class="sxs-lookup"><span data-stu-id="d8f60-717">Command-line configuration is added last to permit command-line arguments to override configuration provided by the earlier configuration providers.</span></span>

<span data-ttu-id="d8f60-718">*hostsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="d8f60-718">*hostsettings.json* :</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/hostsettings.json)]

<span data-ttu-id="d8f60-719">Weitere Konfigurationen können durch die Schlüssel [applicationName](#application-key-name) und [contentRoot](#content-root) bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="d8f60-719">Additional configuration can be provided with the [applicationName](#application-key-name) and [contentRoot](#content-root) keys.</span></span>

<span data-ttu-id="d8f60-720">Beispielkonfiguration für `HostBuilder` mithilfe von <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>:</span><span class="sxs-lookup"><span data-stu-id="d8f60-720">Example `HostBuilder` configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureHostConfiguration)]

## <a name="configureappconfiguration"></a><span data-ttu-id="d8f60-721">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="d8f60-721">ConfigureAppConfiguration</span></span>

<span data-ttu-id="d8f60-722">Die App-Konfiguration wird durch Aufrufen von <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> in der <xref:Microsoft.Extensions.Hosting.IHostBuilder>-Implementierung erstellt.</span><span class="sxs-lookup"><span data-stu-id="d8f60-722">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on the <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation.</span></span> <span data-ttu-id="d8f60-723"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> verwendet einen <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder>, um eine <xref:Microsoft.Extensions.Configuration.IConfiguration> für die App zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="d8f60-723"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the app.</span></span> <span data-ttu-id="d8f60-724"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> kann mehrfach mit additiven Ergebnissen aufgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="d8f60-724"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="d8f60-725">Die App verwendet die Option, die zuletzt einen Wert für einen bestimmten Schlüssel festlegt.</span><span class="sxs-lookup"><span data-stu-id="d8f60-725">The app uses whichever option sets a value last on a given key.</span></span> <span data-ttu-id="d8f60-726">Die von <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> erstellte Konfiguration ist unter [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) verfügbar und kann für nachfolgende Vorgänge und in <xref:Microsoft.Extensions.Hosting.IHost.Services*> verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="d8f60-726">The configuration created by <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and in <xref:Microsoft.Extensions.Hosting.IHost.Services*>.</span></span>

<span data-ttu-id="d8f60-727">Auf die App-Konfiguration wird automatisch die Hostkonfiguration angewendet, die von [ConfigureHostConfiguration](#configurehostconfiguration) bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="d8f60-727">App configuration automatically receives host configuration provided by [ConfigureHostConfiguration](#configurehostconfiguration).</span></span>

<span data-ttu-id="d8f60-728">Beispielkonfiguration für die App mithilfe von <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:</span><span class="sxs-lookup"><span data-stu-id="d8f60-728">Example app configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureAppConfiguration)]

<span data-ttu-id="d8f60-729">*appsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="d8f60-729">*appsettings.json* :</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/appsettings.json)]

<span data-ttu-id="d8f60-730">*appsettings.Development.json* :</span><span class="sxs-lookup"><span data-stu-id="d8f60-730">*appsettings.Development.json* :</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/appsettings.Development.json)]

<span data-ttu-id="d8f60-731">*appsettings.Production.json* :</span><span class="sxs-lookup"><span data-stu-id="d8f60-731">*appsettings.Production.json* :</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/appsettings.Production.json)]

<span data-ttu-id="d8f60-732">Um Einstellungsdateien in das Ausgabeverzeichnis zu verschieben, geben Sie die Einstellungsdateien als [MSBuild-Projektelemente](/visualstudio/msbuild/common-msbuild-project-items) in der Projektdatei an.</span><span class="sxs-lookup"><span data-stu-id="d8f60-732">To move settings files to the output directory, specify the settings files as [MSBuild project items](/visualstudio/msbuild/common-msbuild-project-items) in the project file.</span></span> <span data-ttu-id="d8f60-733">Die Beispiel-App verschiebt ihre JSON-App-Einstellungsdateien und *hostsettings.json* mit dem folgenden `<Content>`-Element:</span><span class="sxs-lookup"><span data-stu-id="d8f60-733">The sample app moves its JSON app settings files and *hostsettings.json* with the following `<Content>` item:</span></span>

```xml
<ItemGroup>
  <Content Include="**\*.json" Exclude="bin\**\*;obj\**\*" 
      CopyToOutputDirectory="PreserveNewest" />
</ItemGroup>
```

> [!NOTE]
> <span data-ttu-id="d8f60-734">Konfigurationserweiterungsmethoden wie <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> und <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> erfordern zusätzliche NuGet-Pakete, z.B. [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) und [Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span><span class="sxs-lookup"><span data-stu-id="d8f60-734">Configuration extension methods, such as <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> and <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> require additional NuGet packages, such as [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) and [Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span></span> <span data-ttu-id="d8f60-735">Wenn die App nicht das [Microsoft.AspNetCore.App-Metapaket](xref:fundamentals/metapackage-app) verwendet, müssen diese Pakete dem Projekt zusätzlich zum [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration)-Kernpaket hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="d8f60-735">Unless the app uses the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), these packages must be added to the project in addition to the core [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) package.</span></span> <span data-ttu-id="d8f60-736">Weitere Informationen finden Sie unter <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="d8f60-736">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="configureservices"></a><span data-ttu-id="d8f60-737">ConfigureServices</span><span class="sxs-lookup"><span data-stu-id="d8f60-737">ConfigureServices</span></span>

<span data-ttu-id="d8f60-738">Mithilfe von <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> werden Dienste zum Container [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) der App hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="d8f60-738"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> adds services to the app's [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="d8f60-739"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> kann mehrfach mit additiven Ergebnissen aufgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="d8f60-739"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="d8f60-740">Ein gehosteter Dienst ist eine Klasse mit Logik für Hintergrundaufgaben, die die Schnittstelle <xref:Microsoft.Extensions.Hosting.IHostedService> implementiert.</span><span class="sxs-lookup"><span data-stu-id="d8f60-740">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="d8f60-741">Weitere Informationen finden Sie unter <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="d8f60-741">For more information, see <xref:fundamentals/host/hosted-services>.</span></span>

<span data-ttu-id="d8f60-742">Die [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) verwendet die Erweiterungsmethode `AddHostedService`, um einen Dienst für Lebensdauerereignisse (`LifetimeEventsHostedService`) und einen zeitgesteuerten Hintergrundtask (`TimedHostedService`) zur App hinzuzufügen:</span><span class="sxs-lookup"><span data-stu-id="d8f60-742">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses the `AddHostedService` extension method to add a service for lifetime events, `LifetimeEventsHostedService`, and a timed background task, `TimedHostedService`, to the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureServices)]

## <a name="configurelogging"></a><span data-ttu-id="d8f60-743">ConfigureLogging</span><span class="sxs-lookup"><span data-stu-id="d8f60-743">ConfigureLogging</span></span>

<span data-ttu-id="d8f60-744"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> fügt einen Delegaten für die Konfiguration des bereitgestellten <xref:Microsoft.Extensions.Logging.ILoggingBuilder> hinzu.</span><span class="sxs-lookup"><span data-stu-id="d8f60-744"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> adds a delegate for configuring the provided <xref:Microsoft.Extensions.Logging.ILoggingBuilder>.</span></span> <span data-ttu-id="d8f60-745"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> kann mehrfach mit additiven Ergebnissen aufgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="d8f60-745"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> may be called multiple times with additive results.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureLogging)]

### <a name="useconsolelifetime"></a><span data-ttu-id="d8f60-746">UseConsoleLifetime</span><span class="sxs-lookup"><span data-stu-id="d8f60-746">UseConsoleLifetime</span></span>

<span data-ttu-id="d8f60-747"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> lauscht auf <kbd>STRG</kbd>+<kbd>C</kbd>/SIGINT oder SIGTERM und ruft <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> auf, um das Herunterfahren zu beginnen.</span><span class="sxs-lookup"><span data-stu-id="d8f60-747"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to start the shutdown process.</span></span> <span data-ttu-id="d8f60-748"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> hebt die Blockierung von Erweiterungen wie [RunAsync](#runasync) und [WaitForShutdownAsync](#waitforshutdownasync) auf.</span><span class="sxs-lookup"><span data-stu-id="d8f60-748"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span> <span data-ttu-id="d8f60-749">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` ist vorab als Standardimplementierung für die Lebensdauer registriert.</span><span class="sxs-lookup"><span data-stu-id="d8f60-749">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is pre-registered as the default lifetime implementation.</span></span> <span data-ttu-id="d8f60-750">Die letzte registrierte Lebensdauer wird verwendet.</span><span class="sxs-lookup"><span data-stu-id="d8f60-750">The last lifetime registered is used.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseConsoleLifetime)]

## <a name="container-configuration"></a><span data-ttu-id="d8f60-751">Containerkonfiguration</span><span class="sxs-lookup"><span data-stu-id="d8f60-751">Container configuration</span></span>

<span data-ttu-id="d8f60-752">Der Host kann eine <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>-Schnittstelle verwenden, um die Integration in andere Container zu unterstützten.</span><span class="sxs-lookup"><span data-stu-id="d8f60-752">To support plugging in other containers, the host can accept an <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>.</span></span> <span data-ttu-id="d8f60-753">Das Bereitstellen einer Factory ist kein Teil der DI-Containerregistrierung, sondern ein Host, der systemintern verwendet wird, um den entsprechenden DI-Container zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="d8f60-753">Providing a factory isn't part of the DI container registration but is instead a host intrinsic used to create the concrete DI container.</span></span> <span data-ttu-id="d8f60-754">[UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) überschreibt die Standardfactory, die zum Erstellen des Dienstanbieters der App verwendet wurde.</span><span class="sxs-lookup"><span data-stu-id="d8f60-754">[UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) overrides the default factory used to create the app's service provider.</span></span>

<span data-ttu-id="d8f60-755">Die benutzerdefinierte Containerkonfiguration wird von der Methode <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> verwaltet.</span><span class="sxs-lookup"><span data-stu-id="d8f60-755">Custom container configuration is managed by the <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> method.</span></span> <span data-ttu-id="d8f60-756"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> stellt eine stark typisierte Möglichkeit für das Konfigurieren des Containers auf Basis der zugrunde liegenden Host-API bereit.</span><span class="sxs-lookup"><span data-stu-id="d8f60-756"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> provides a strongly-typed experience for configuring the container on top of the underlying host API.</span></span> <span data-ttu-id="d8f60-757"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> kann mehrfach mit additiven Ergebnissen aufgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="d8f60-757"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="d8f60-758">Erstellen eines Dienstcontainers für die App:</span><span class="sxs-lookup"><span data-stu-id="d8f60-758">Create a service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainer.cs)]

<span data-ttu-id="d8f60-759">Bereitstellen einer Factory für den Dienstcontainer:</span><span class="sxs-lookup"><span data-stu-id="d8f60-759">Provide a service container factory:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainerFactory.cs)]

<span data-ttu-id="d8f60-760">Verwenden Sie die Factory, und konfigurieren Sie den benutzerdefinierten Dienstcontainer für die App:</span><span class="sxs-lookup"><span data-stu-id="d8f60-760">Use the factory and configure the custom service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ContainerConfiguration)]

## <a name="extensibility"></a><span data-ttu-id="d8f60-761">Erweiterungen</span><span class="sxs-lookup"><span data-stu-id="d8f60-761">Extensibility</span></span>

<span data-ttu-id="d8f60-762">Die Erweiterung des Hosts wird mit der Erweiterungsmethode in <xref:Microsoft.Extensions.Hosting.IHostBuilder> durchgeführt.</span><span class="sxs-lookup"><span data-stu-id="d8f60-762">Host extensibility is performed with extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span></span> <span data-ttu-id="d8f60-763">Im folgenden Beispiel wird dargestellt, wie eine Erweiterungsmethode eine <xref:Microsoft.Extensions.Hosting.IHostBuilder>-Implementierung mit dem [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks)-Beispiel erweitert, das in <xref:fundamentals/host/hosted-services> gezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="d8f60-763">The following example shows how an extension method extends an <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation with the [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) example demonstrated in <xref:fundamentals/host/hosted-services>.</span></span>

```csharp
var host = new HostBuilder()
    .UseHostedService<TimedHostedService>()
    .Build();

await host.StartAsync();
```

<span data-ttu-id="d8f60-764">Eine App richtet die `UseHostedService`-Erweiterungsmethode zum Registrieren des in `T` übergebenen gehosteten Diensts ein:</span><span class="sxs-lookup"><span data-stu-id="d8f60-764">An app establishes the `UseHostedService` extension method to register the hosted service passed in `T`:</span></span>

```csharp
using System;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;

public static class Extensions
{
    public static IHostBuilder UseHostedService<T>(this IHostBuilder hostBuilder)
        where T : class, IHostedService, IDisposable
    {
        return hostBuilder.ConfigureServices(services =>
            services.AddHostedService<T>());
    }
}
```

## <a name="manage-the-host"></a><span data-ttu-id="d8f60-765">Verwalten des Hosts</span><span class="sxs-lookup"><span data-stu-id="d8f60-765">Manage the host</span></span>

<span data-ttu-id="d8f60-766">Die Implementierung von <xref:Microsoft.Extensions.Hosting.IHost> ist für das Starten und Beenden der Implementierungen von <xref:Microsoft.Extensions.Hosting.IHostedService> verantwortlich, die im Dienstcontainer registriert sind.</span><span class="sxs-lookup"><span data-stu-id="d8f60-766">The <xref:Microsoft.Extensions.Hosting.IHost> implementation is responsible for starting and stopping the <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="d8f60-767">Run</span><span class="sxs-lookup"><span data-stu-id="d8f60-767">Run</span></span>

<span data-ttu-id="d8f60-768">Durch <xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> wird die App gestartet und der aufrufende Thread blockiert, bis der Host heruntergefahren wird:</span><span class="sxs-lookup"><span data-stu-id="d8f60-768"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down:</span></span>

```csharp
public class Program
{
    public void Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        host.Run();
    }
}
```

### <a name="runasync"></a><span data-ttu-id="d8f60-769">RunAsync</span><span class="sxs-lookup"><span data-stu-id="d8f60-769">RunAsync</span></span>

<span data-ttu-id="d8f60-770">Durch <xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> wird die App ausgeführt und eine <xref:System.Threading.Tasks.Task>-Methode ausgegeben, die abgeschlossen wird, wenn das Abbruchtoken oder das Herunterfahren ausgelöst wird:</span><span class="sxs-lookup"><span data-stu-id="d8f60-770"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered:</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        await host.RunAsync();
    }
}
```

### <a name="runconsoleasync"></a><span data-ttu-id="d8f60-771">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="d8f60-771">RunConsoleAsync</span></span>

<span data-ttu-id="d8f60-772"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> aktiviert die Unterstützung der Konsole, erstellt und startet den Host und lauscht auf <kbd>STRG</kbd>+<kbd>C</kbd>/SIGINT oder SIGTERM, um das Herunterfahren auszulösen.</span><span class="sxs-lookup"><span data-stu-id="d8f60-772"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var hostBuilder = new HostBuilder();

        await hostBuilder.RunConsoleAsync();
    }
}
```

### <a name="start-and-stopasync"></a><span data-ttu-id="d8f60-773">Start und StopAsync</span><span class="sxs-lookup"><span data-stu-id="d8f60-773">Start and StopAsync</span></span>

<span data-ttu-id="d8f60-774"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> startet den Host synchron.</span><span class="sxs-lookup"><span data-stu-id="d8f60-774"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

<span data-ttu-id="d8f60-775"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> versucht, den Host innerhalb des angegebenen Timeouts zu beenden.</span><span class="sxs-lookup"><span data-stu-id="d8f60-775"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            host.Start();

            await host.StopAsync(TimeSpan.FromSeconds(5));
        }
    }
}
```

### <a name="startasync-and-stopasync"></a><span data-ttu-id="d8f60-776">StartAsync und StopAsync</span><span class="sxs-lookup"><span data-stu-id="d8f60-776">StartAsync and StopAsync</span></span>

<span data-ttu-id="d8f60-777"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> startet die App.</span><span class="sxs-lookup"><span data-stu-id="d8f60-777"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the app.</span></span>

<span data-ttu-id="d8f60-778"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> hält die App an.</span><span class="sxs-lookup"><span data-stu-id="d8f60-778"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> stops the app.</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            await host.StartAsync();

            await host.StopAsync();
        }
    }
}
```

### <a name="waitforshutdown"></a><span data-ttu-id="d8f60-779">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="d8f60-779">WaitForShutdown</span></span>

<span data-ttu-id="d8f60-780"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> wird über die <xref:Microsoft.Extensions.Hosting.IHostLifetime> ausgelöst, z. B. `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` (lauscht auf <kbd>STRG</kbd>+<kbd>C</kbd>/SIGINT oder SIGTERM).</span><span class="sxs-lookup"><span data-stu-id="d8f60-780"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> is triggered via the <xref:Microsoft.Extensions.Hosting.IHostLifetime>, such as `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` (listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM).</span></span> <span data-ttu-id="d8f60-781"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> ruft <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> auf.</span><span class="sxs-lookup"><span data-stu-id="d8f60-781"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

```csharp
public class Program
{
    public void Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            host.Start();

            host.WaitForShutdown();
        }
    }
}
```

### <a name="waitforshutdownasync"></a><span data-ttu-id="d8f60-782">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="d8f60-782">WaitForShutdownAsync</span></span>

<span data-ttu-id="d8f60-783"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> gibt eine <xref:System.Threading.Tasks.Task>-Methode zurück, die abgeschlossen wird, wenn das Herunterfahren über das angegebene Token ausgelöst wird, und ruft <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> auf.</span><span class="sxs-lookup"><span data-stu-id="d8f60-783"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            await host.StartAsync();

            await host.WaitForShutdownAsync();
        }

    }
}
```

### <a name="external-control"></a><span data-ttu-id="d8f60-784">Externe Steuerung</span><span class="sxs-lookup"><span data-stu-id="d8f60-784">External control</span></span>

<span data-ttu-id="d8f60-785">Die externe Steuerung des Hosts kann mithilfe von Methoden durchgeführt werden, die extern aufgerufen werden können:</span><span class="sxs-lookup"><span data-stu-id="d8f60-785">External control of the host can be achieved using methods that can be called externally:</span></span>

```csharp
public class Program
{
    private IHost _host;

    public Program()
    {
        _host = new HostBuilder()
            .Build();
    }

    public async Task StartAsync()
    {
        _host.StartAsync();
    }

    public async Task StopAsync()
    {
        using (_host)
        {
            await _host.StopAsync(TimeSpan.FromSeconds(5));
        }
    }
}
```

<span data-ttu-id="d8f60-786"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> wird am Anfang der <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>-Methode aufgerufen, die den Vorgang erst fortsetzt, wenn sie abgeschlossen ist.</span><span class="sxs-lookup"><span data-stu-id="d8f60-786"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>, which waits until it's complete before continuing.</span></span> <span data-ttu-id="d8f60-787">Dies kann verwendet werden, um den Start zu verzögern, bis dieser durch ein externes Ereignis initiiert wird.</span><span class="sxs-lookup"><span data-stu-id="d8f60-787">This can be used to delay startup until signaled by an external event.</span></span>

## <a name="ihostingenvironment-interface"></a><span data-ttu-id="d8f60-788">IHostingEnvironment-Schnittstelle</span><span class="sxs-lookup"><span data-stu-id="d8f60-788">IHostingEnvironment interface</span></span>

<span data-ttu-id="d8f60-789"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> stellt Informationen über die Hostingumgebung der App bereit.</span><span class="sxs-lookup"><span data-stu-id="d8f60-789"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> provides information about the app's hosting environment.</span></span> <span data-ttu-id="d8f60-790">Verwenden Sie [Constructor Injection](xref:fundamentals/dependency-injection) zum Abrufen der <xref:Microsoft.Extensions.Hosting.IHostingEnvironment>-Schnittstelle, um deren Eigenschaften und Erweiterungsmethoden zu verwenden:</span><span class="sxs-lookup"><span data-stu-id="d8f60-790">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> in order to use its properties and extension methods:</span></span>

```csharp
public class MyClass
{
    private readonly IHostingEnvironment _env;

    public MyClass(IHostingEnvironment env)
    {
        _env = env;
    }

    public void DoSomething()
    {
        var environmentName = _env.EnvironmentName;
    }
}
```

<span data-ttu-id="d8f60-791">Weitere Informationen finden Sie unter <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="d8f60-791">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="iapplicationlifetime-interface"></a><span data-ttu-id="d8f60-792">IApplicationLifetime-Schnittstelle</span><span class="sxs-lookup"><span data-stu-id="d8f60-792">IApplicationLifetime interface</span></span>

<span data-ttu-id="d8f60-793"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> ermöglicht Aktivitäten nach dem Starten und beim Herunterfahren (einschließlich Anforderungen für ordnungsgemäßes Herunterfahren).</span><span class="sxs-lookup"><span data-stu-id="d8f60-793"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> allows for post-startup and shutdown activities, including graceful shutdown requests.</span></span> <span data-ttu-id="d8f60-794">Bei drei der Eigenschaften der Schnittstelle handelt es sich um Abbruchtokens, die zum Registrieren von <xref:System.Action>-Methoden verwendet werden, durch die Ereignisse beim Starten und Herunterfahren definiert werden.</span><span class="sxs-lookup"><span data-stu-id="d8f60-794">Three properties on the interface are cancellation tokens used to register <xref:System.Action> methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="d8f60-795">Abbruchtoken</span><span class="sxs-lookup"><span data-stu-id="d8f60-795">Cancellation Token</span></span> | <span data-ttu-id="d8f60-796">Wird ausgelöst, wenn&#8230;</span><span class="sxs-lookup"><span data-stu-id="d8f60-796">Triggered when&#8230;</span></span> |
| ------------------ | --------------------- |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStarted*> | <span data-ttu-id="d8f60-797">Der Host vollständig gestartet wurde.</span><span class="sxs-lookup"><span data-stu-id="d8f60-797">The host has fully started.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopped*> | <span data-ttu-id="d8f60-798">Der Host ein ordnungsgemäßes Herunterfahren abschließt.</span><span class="sxs-lookup"><span data-stu-id="d8f60-798">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="d8f60-799">Alle Anforderungen sollten verarbeitet worden sein.</span><span class="sxs-lookup"><span data-stu-id="d8f60-799">All requests should be processed.</span></span> <span data-ttu-id="d8f60-800">Das Herunterfahren wird blockiert, bis das Ereignis abgeschlossen ist.</span><span class="sxs-lookup"><span data-stu-id="d8f60-800">Shutdown blocks until this event completes.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopping*> | <span data-ttu-id="d8f60-801">Der Host ein ordnungsgemäßes Herunterfahren ausführt.</span><span class="sxs-lookup"><span data-stu-id="d8f60-801">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="d8f60-802">Anforderungen möglicherweise noch verarbeitet werden.</span><span class="sxs-lookup"><span data-stu-id="d8f60-802">Requests may still be processing.</span></span> <span data-ttu-id="d8f60-803">Das Herunterfahren wird blockiert, bis das Ereignis abgeschlossen ist.</span><span class="sxs-lookup"><span data-stu-id="d8f60-803">Shutdown blocks until this event completes.</span></span> |

<span data-ttu-id="d8f60-804">Fügen Sie den <xref:Microsoft.Extensions.Hosting.IApplicationLifetime>-Dienst über einen Konstruktor in eine beliebige Klasse ein.</span><span class="sxs-lookup"><span data-stu-id="d8f60-804">Constructor-inject the <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> service into any class.</span></span> <span data-ttu-id="d8f60-805">Die [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) verwendet die Konstruktorinjektion für eine `LifetimeEventsHostedService`-Klasse (eine <xref:Microsoft.Extensions.Hosting.IHostedService>-Implementierung), um die Ereignisse zu registrieren.</span><span class="sxs-lookup"><span data-stu-id="d8f60-805">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses constructor injection into a `LifetimeEventsHostedService` class (an <xref:Microsoft.Extensions.Hosting.IHostedService> implementation) to register the events.</span></span>

<span data-ttu-id="d8f60-806">*LifetimeEventsHostedService.cs* :</span><span class="sxs-lookup"><span data-stu-id="d8f60-806">*LifetimeEventsHostedService.cs* :</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/LifetimeEventsHostedService.cs?name=snippet1)]

<span data-ttu-id="d8f60-807"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> fordert das Beenden der App an.</span><span class="sxs-lookup"><span data-stu-id="d8f60-807"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> requests termination of the app.</span></span> <span data-ttu-id="d8f60-808">Die folgende Klasse verwendet <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*>, um eine App ordnungsgemäß herunterzufahren, wenn die `Shutdown`-Methode der Klasse aufgerufen wird:</span><span class="sxs-lookup"><span data-stu-id="d8f60-808">The following class uses <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

```csharp
public class MyClass
{
    private readonly IApplicationLifetime _appLifetime;

    public MyClass(IApplicationLifetime appLifetime)
    {
        _appLifetime = appLifetime;
    }

    public void Shutdown()
    {
        _appLifetime.StopApplication();
    }
}
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="d8f60-809">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="d8f60-809">Additional resources</span></span>

* <xref:fundamentals/host/hosted-services>
