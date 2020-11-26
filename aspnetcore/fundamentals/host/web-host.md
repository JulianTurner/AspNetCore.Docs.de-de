---
title: ASP.NET Core-Webhost
author: rick-anderson
description: Erfahren Sie mehr über den Webhost in ASP.NET Core, der für das Starten der App und das Verwalten der Lebensdauer verantwortlich ist.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/07/2019
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
uid: fundamentals/host/web-host
ms.openlocfilehash: 904b57f95cbc48a8177174dc9be770e8a6abf146
ms.sourcegitcommit: 3f0ad1e513296ede1bff39a05be6c278e879afed
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96035878"
---
# <a name="aspnet-core-web-host"></a><span data-ttu-id="90056-103">ASP.NET Core-Webhost</span><span class="sxs-lookup"><span data-stu-id="90056-103">ASP.NET Core Web Host</span></span>

<span data-ttu-id="90056-104">Durch ASP.NET Core-Apps kann ein *Host* gestartet und konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="90056-104">ASP.NET Core apps configure and launch a *host*.</span></span> <span data-ttu-id="90056-105">Der Host ist verantwortlich für das Starten der App und das Verwalten der Lebensdauer.</span><span class="sxs-lookup"><span data-stu-id="90056-105">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="90056-106">Der Host konfiguriert mindestens einen Server und eine Pipeline für die Anforderungsverarbeitung.</span><span class="sxs-lookup"><span data-stu-id="90056-106">At a minimum, the host configures a server and a request processing pipeline.</span></span> <span data-ttu-id="90056-107">Der Host kann auch die Protokollierung, die Dependency Injection und die Konfiguration einrichten.</span><span class="sxs-lookup"><span data-stu-id="90056-107">The host can also set up logging, dependency injection, and configuration.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="90056-108">In diesem Artikel wird der Webhost behandelt, der nur aus Gründen der Abwärtskompatibilität verfügbar bleibt.</span><span class="sxs-lookup"><span data-stu-id="90056-108">This article covers the Web Host, which remains available only for backward compatibility.</span></span> <span data-ttu-id="90056-109">Für alle App-Typen wird der [Generische Host](xref:fundamentals/host/generic-host) empfohlen.</span><span class="sxs-lookup"><span data-stu-id="90056-109">The [Generic Host](xref:fundamentals/host/generic-host) is recommended for all app types.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="90056-110">In diesem Artikel wird der Webhost behandelt, der zum Hosten von Web-Apps dient.</span><span class="sxs-lookup"><span data-stu-id="90056-110">This article covers the Web Host, which is for hosting web apps.</span></span> <span data-ttu-id="90056-111">Verwenden Sie für andere Arten von Apps den [generischen Host](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="90056-111">For other kinds of apps, use the [Generic Host](xref:fundamentals/host/generic-host).</span></span>

::: moniker-end

## <a name="set-up-a-host"></a><span data-ttu-id="90056-112">Einrichten eines Hosts</span><span class="sxs-lookup"><span data-stu-id="90056-112">Set up a host</span></span>

<span data-ttu-id="90056-113">Erstellen Sie einen Host mithilfe einer Instanz von [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder).</span><span class="sxs-lookup"><span data-stu-id="90056-113">Create a host using an instance of [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder).</span></span> <span data-ttu-id="90056-114">Dies wird üblicherweise am Einstiegspunkt der App (die `Main`-Methode) durchgeführt.</span><span class="sxs-lookup"><span data-stu-id="90056-114">This is typically performed in the app's entry point, the `Main` method.</span></span>

<span data-ttu-id="90056-115">In den Projektvorlagen befindet `Main` sich in *Program.cs*.</span><span class="sxs-lookup"><span data-stu-id="90056-115">In the project templates, `Main` is located in *Program.cs*.</span></span> <span data-ttu-id="90056-116">Eine typische App ruft [CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) auf, um die Einrichtung eines Hosts zu starten:</span><span class="sxs-lookup"><span data-stu-id="90056-116">A typical app calls [CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) to start setting up a host:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .UseStartup<Startup>();
}
```

<span data-ttu-id="90056-117">Der Code, der `CreateDefaultBuilder` aufruft, befindet sich in einer Methode namens `CreateWebHostBuilder`, die diesen Code von dem Code in `Main` trennt, der `Run` im Generatorobjekt aufruft.</span><span class="sxs-lookup"><span data-stu-id="90056-117">The code that calls `CreateDefaultBuilder` is in a method named `CreateWebHostBuilder`, which separates it from the code in `Main` that calls `Run` on the builder object.</span></span> <span data-ttu-id="90056-118">Diese Trennung ist erforderlich, wenn Sie [Entity Framework Core-Tools](/ef/core/miscellaneous/cli/) verwenden.</span><span class="sxs-lookup"><span data-stu-id="90056-118">This separation is required if you use [Entity Framework Core tools](/ef/core/miscellaneous/cli/).</span></span> <span data-ttu-id="90056-119">Die Tools erwarten eine `CreateWebHostBuilder`-Methode, die sie zur Entwurfszeit aufrufen können, um den Host zu konfigurieren, ohne die App auszuführen.</span><span class="sxs-lookup"><span data-stu-id="90056-119">The tools expect to find a `CreateWebHostBuilder` method that they can call at design time to configure the host without running the app.</span></span> <span data-ttu-id="90056-120">Eine Alternative dazu ist die Implementierung von`IDesignTimeDbContextFactory`.</span><span class="sxs-lookup"><span data-stu-id="90056-120">An alternative is to implement `IDesignTimeDbContextFactory`.</span></span> <span data-ttu-id="90056-121">Weitere Informationen finden Sie unter [DbContext-Instanzerstellung zur Entwurfszeit](/ef/core/miscellaneous/cli/dbcontext-creation).</span><span class="sxs-lookup"><span data-stu-id="90056-121">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

<span data-ttu-id="90056-122">`CreateDefaultBuilder` führt folgende Ausgaben aus:</span><span class="sxs-lookup"><span data-stu-id="90056-122">`CreateDefaultBuilder` performs the following tasks:</span></span>

* <span data-ttu-id="90056-123">Konfiguriert des [Kestrel](xref:fundamentals/servers/kestrel)-Servers als Webserver mithilfe der Hostkonfigurationsanbieter der App.</span><span class="sxs-lookup"><span data-stu-id="90056-123">Configures [Kestrel](xref:fundamentals/servers/kestrel) server as the web server using the app's hosting configuration providers.</span></span> <span data-ttu-id="90056-124">Die Standardoptionen des Kestrel-Servers finden Sie unter <xref:fundamentals/servers/kestrel#kestrel-options>.</span><span class="sxs-lookup"><span data-stu-id="90056-124">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="90056-125">Legt das [Inhaltsstammverzeichnis](xref:fundamentals/index#content-root) auf den Pfad fest, der von [Directory.GetCurrentDirectory](/dotnet/api/system.io.directory.getcurrentdirectory) zurückgegeben wird.</span><span class="sxs-lookup"><span data-stu-id="90056-125">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by [Directory.GetCurrentDirectory](/dotnet/api/system.io.directory.getcurrentdirectory).</span></span>
* <span data-ttu-id="90056-126">Lädt [Hostkonfiguration](#host-configuration-values) aus:</span><span class="sxs-lookup"><span data-stu-id="90056-126">Loads [host configuration](#host-configuration-values) from:</span></span>
  * <span data-ttu-id="90056-127">Umgebungsvariablen mit dem Präfix `ASPNETCORE_` (z.B. `ASPNETCORE_ENVIRONMENT`)</span><span class="sxs-lookup"><span data-stu-id="90056-127">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`).</span></span>
  * <span data-ttu-id="90056-128">Befehlszeilenargumenten</span><span class="sxs-lookup"><span data-stu-id="90056-128">Command-line arguments.</span></span>
* <span data-ttu-id="90056-129">Lädt die App-Konfiguration in der folgenden Reihenfolge:</span><span class="sxs-lookup"><span data-stu-id="90056-129">Loads app configuration in the following order from:</span></span>
  * <span data-ttu-id="90056-130">*appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="90056-130">*appsettings.json*.</span></span>
  * <span data-ttu-id="90056-131">*appsettings.{Environment}.json*</span><span class="sxs-lookup"><span data-stu-id="90056-131">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="90056-132">[Benutzergeheimnissen](xref:security/app-secrets), wenn die App in der `Development`-Umgebung mit der Einstiegsassembly ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="90056-132">[User secrets](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="90056-133">Umgebungsvariablen.</span><span class="sxs-lookup"><span data-stu-id="90056-133">Environment variables.</span></span>
  * <span data-ttu-id="90056-134">Befehlszeilenargumenten</span><span class="sxs-lookup"><span data-stu-id="90056-134">Command-line arguments.</span></span>
* <span data-ttu-id="90056-135">Konfiguriert die [Protokollierung](xref:fundamentals/logging/index) für die Konsolen- und Debugausgabe</span><span class="sxs-lookup"><span data-stu-id="90056-135">Configures [logging](xref:fundamentals/logging/index) for console and debug output.</span></span> <span data-ttu-id="90056-136">Die Protokollierung umfasst Regeln zur [Protokollfilterung](xref:fundamentals/logging/index#log-filtering), die im Abschnitt für die Protokollierungskonfiguration einer *appsettings.json* - oder *appsettings.{Environment}.json*-Datei angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="90056-136">Logging includes [log filtering](xref:fundamentals/logging/index#log-filtering) rules specified in a Logging configuration section of an *appsettings.json* or *appsettings.{Environment}.json* file.</span></span>
* <span data-ttu-id="90056-137">Wenn die Ausführung mit dem [ASP.NET Core-Modul](xref:host-and-deploy/aspnet-core-module) hinter den IIS erfolgt, ermöglicht `CreateDefaultBuilder` die [IIS-Integration](xref:host-and-deploy/iis/index), die die Basisadresse und den Port der App konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="90056-137">When running behind IIS with the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), `CreateDefaultBuilder` enables [IIS Integration](xref:host-and-deploy/iis/index), which configures the app's base address and port.</span></span> <span data-ttu-id="90056-138">Die IIS-Integration konfiguriert die App auch für das [Erfassen von Startfehlern](#capture-startup-errors).</span><span class="sxs-lookup"><span data-stu-id="90056-138">IIS Integration also configures the app to [capture startup errors](#capture-startup-errors).</span></span> <span data-ttu-id="90056-139">Informationen zu den IIS-Standardoptionen finden Sie unter <xref:host-and-deploy/iis/index#iis-options>.</span><span class="sxs-lookup"><span data-stu-id="90056-139">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>
* <span data-ttu-id="90056-140">Legt [ServiceProviderOptions.ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) auf `true` fest, wenn die Umgebung der App „Development“ ist.</span><span class="sxs-lookup"><span data-stu-id="90056-140">Sets [ServiceProviderOptions.ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) to `true` if the app's environment is Development.</span></span> <span data-ttu-id="90056-141">Weitere Informationen finden Sie unter [Bereichsvalidierung](#scope-validation).</span><span class="sxs-lookup"><span data-stu-id="90056-141">For more information, see [Scope validation](#scope-validation).</span></span>

<span data-ttu-id="90056-142">Die durch `CreateDefaultBuilder` definierte Konfiguration kann von [ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration), [ConfigureLogging](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configurelogging) und anderen Methoden sowie Erweiterungsmethoden von [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) überschrieben und erweitert werden.</span><span class="sxs-lookup"><span data-stu-id="90056-142">The configuration defined by `CreateDefaultBuilder` can be overridden and augmented by [ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration), [ConfigureLogging](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configurelogging), and other methods and extension methods of [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder).</span></span> <span data-ttu-id="90056-143">Es folgen einige Beispiele:</span><span class="sxs-lookup"><span data-stu-id="90056-143">A few examples follow:</span></span>

* <span data-ttu-id="90056-144">[ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration) wird zum zusätzlichen Festlegen von `IConfiguration` für die App verwendet.</span><span class="sxs-lookup"><span data-stu-id="90056-144">[ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration) is used to specify additional `IConfiguration` for the app.</span></span> <span data-ttu-id="90056-145">Der folgende `ConfigureAppConfiguration`-Aufruf fügt einen Delegaten hinzu, um die App-Konfiguration in die Datei *appsettings.xml* einzufügen.</span><span class="sxs-lookup"><span data-stu-id="90056-145">The following `ConfigureAppConfiguration` call adds a delegate to include app configuration in the *appsettings.xml* file.</span></span> <span data-ttu-id="90056-146">`ConfigureAppConfiguration` kann mehrmals aufgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="90056-146">`ConfigureAppConfiguration` may be called multiple times.</span></span> <span data-ttu-id="90056-147">Beachten Sie, dass diese Konfiguration nicht für den Host gilt (z.B. Server-URLs oder Umgebungen).</span><span class="sxs-lookup"><span data-stu-id="90056-147">Note that this configuration doesn't apply to the host (for example, server URLs or environment).</span></span> <span data-ttu-id="90056-148">Informationen finden Sie im Abschnitt [Hostkonfigurationswerte](#host-configuration-values).</span><span class="sxs-lookup"><span data-stu-id="90056-148">See the [Host configuration values](#host-configuration-values) section.</span></span>

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .ConfigureAppConfiguration((hostingContext, config) =>
        {
            config.AddXmlFile("appsettings.xml", optional: true, reloadOnChange: true);
        })
        ...
    ```

* <span data-ttu-id="90056-149">Der folgende `ConfigureLogging`-Aufruf fügt einen Delegaten hinzu, um den Mindestprotokolliergrad ([SetMinimumLevel](/dotnet/api/microsoft.extensions.logging.loggingbuilderextensions.setminimumlevel)) auf [LogLevel.Warning](/dotnet/api/microsoft.extensions.logging.loglevel) zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="90056-149">The following `ConfigureLogging` call adds a delegate to configure the minimum logging level ([SetMinimumLevel](/dotnet/api/microsoft.extensions.logging.loggingbuilderextensions.setminimumlevel)) to [LogLevel.Warning](/dotnet/api/microsoft.extensions.logging.loglevel).</span></span> <span data-ttu-id="90056-150">Diese Einstellung überschreibt die Einstellungen in *appsettings.Development.json* (`LogLevel.Debug`) und *appsettings.Production.json* (`LogLevel.Error`), die durch `CreateDefaultBuilder` konfiguriert wurden.</span><span class="sxs-lookup"><span data-stu-id="90056-150">This setting overrides the settings in *appsettings.Development.json* (`LogLevel.Debug`) and *appsettings.Production.json* (`LogLevel.Error`) configured by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="90056-151">`ConfigureLogging` kann mehrmals aufgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="90056-151">`ConfigureLogging` may be called multiple times.</span></span>

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .ConfigureLogging(logging => 
        {
            logging.SetMinimumLevel(LogLevel.Warning);
        })
        ...
    ```

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="90056-152">Der folgende Aufruf von `ConfigureKestrel` überschreibt den Standardwert von 30.000.000 Bytes von [Limits.MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize), der festgelegt wurde, als Kestrel durch `CreateDefaultBuilder` konfiguriert wurde:</span><span class="sxs-lookup"><span data-stu-id="90056-152">The following call to `ConfigureKestrel` overrides the default [Limits.MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize) of 30,000,000 bytes established when Kestrel was configured by `CreateDefaultBuilder`:</span></span>

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .ConfigureKestrel((context, options) =>
        {
            options.Limits.MaxRequestBodySize = 20000000;
        });
    ```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* <span data-ttu-id="90056-153">Der folgende Aufruf von [UseKestrel](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderkestrelextensions.usekestrel) überschreibt den Standardwert von 30.000.000 Bytes von [Limits.MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize), der festgelegt wurde, als Kestrel durch `CreateDefaultBuilder` konfiguriert wurde:</span><span class="sxs-lookup"><span data-stu-id="90056-153">The following call to [UseKestrel](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderkestrelextensions.usekestrel) overrides the default [Limits.MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize) of 30,000,000 bytes established when Kestrel was configured by `CreateDefaultBuilder`:</span></span>

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .UseKestrel(options =>
        {
            options.Limits.MaxRequestBodySize = 20000000;
        });
    ```

::: moniker-end

<span data-ttu-id="90056-154">Das [Inhaltsstammverzeichnis](xref:fundamentals/index#content-root) bestimmt, wo der Host nach Inhaltsdateien (z.B. MVC-Ansichtsdateien) sucht.</span><span class="sxs-lookup"><span data-stu-id="90056-154">The [content root](xref:fundamentals/index#content-root) determines where the host searches for content files, such as MVC view files.</span></span> <span data-ttu-id="90056-155">Wenn die App aus dem Stammordner des Projekts gestartet wird, wird dieser als Inhaltsstammverzeichnis verwendet.</span><span class="sxs-lookup"><span data-stu-id="90056-155">When the app is started from the project's root folder, the project's root folder is used as the content root.</span></span> <span data-ttu-id="90056-156">Dies ist die Standardeinstellung in [Visual Studio](https://visualstudio.microsoft.com) und für [neue .NET-Vorlagen](/dotnet/core/tools/dotnet-new).</span><span class="sxs-lookup"><span data-stu-id="90056-156">This is the default used in [Visual Studio](https://visualstudio.microsoft.com) and the [dotnet new templates](/dotnet/core/tools/dotnet-new).</span></span>

<span data-ttu-id="90056-157">Weitere Informationen zur App-Konfiguration finden Sie unter <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="90056-157">For more information on app configuration, see <xref:fundamentals/configuration/index>.</span></span>

> [!NOTE]
> <span data-ttu-id="90056-158">Als Alternative zur Verwendung der statischen `CreateDefaultBuilder`-Methode ist das Erstellen eines Hosts von [WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) ein unterstützter Ansatz mit ASP.NET Core 2.x.</span><span class="sxs-lookup"><span data-stu-id="90056-158">As an alternative to using the static `CreateDefaultBuilder` method, creating a host from [WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) is a supported approach with ASP.NET Core 2.x.</span></span>

<span data-ttu-id="90056-159">Beim Einrichten eines Hosts können die Methoden [Configure](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configure) und [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.configureservices) bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="90056-159">When setting up a host, [Configure](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configure) and [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.configureservices) methods can be provided.</span></span> <span data-ttu-id="90056-160">Wenn eine `Startup`-Klasse angegeben wird, muss diese eine `Configure`-Methode definieren.</span><span class="sxs-lookup"><span data-stu-id="90056-160">If a `Startup` class is specified, it must define a `Configure` method.</span></span> <span data-ttu-id="90056-161">Weitere Informationen finden Sie unter <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="90056-161">For more information, see <xref:fundamentals/startup>.</span></span> <span data-ttu-id="90056-162">Wenn `ConfigureServices` mehrmals aufgerufen wird, werden diese Aufrufe einander angefügt.</span><span class="sxs-lookup"><span data-stu-id="90056-162">Multiple calls to `ConfigureServices` append to one another.</span></span> <span data-ttu-id="90056-163">Durch mehrere Aufrufe von `Configure` oder `UseStartup` in `WebHostBuilder` werden die vorherigen Einstellungen ersetzt.</span><span class="sxs-lookup"><span data-stu-id="90056-163">Multiple calls to `Configure` or `UseStartup` on the `WebHostBuilder` replace previous settings.</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="90056-164">Hostkonfigurationswerte</span><span class="sxs-lookup"><span data-stu-id="90056-164">Host configuration values</span></span>

<span data-ttu-id="90056-165">[WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) basiert auf folgenden Ansätzen, um die Hostkonfigurationswerte festzulegen:</span><span class="sxs-lookup"><span data-stu-id="90056-165">[WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) relies on the following approaches to set the host configuration values:</span></span>

* <span data-ttu-id="90056-166">Hostbuilderkonfigurationen, die Umgebungsvariablen im Format `ASPNETCORE_{configurationKey}` enthalten.</span><span class="sxs-lookup"><span data-stu-id="90056-166">Host builder configuration, which includes environment variables with the format `ASPNETCORE_{configurationKey}`.</span></span> <span data-ttu-id="90056-167">Beispielsweise `ASPNETCORE_ENVIRONMENT`.</span><span class="sxs-lookup"><span data-stu-id="90056-167">For example, `ASPNETCORE_ENVIRONMENT`.</span></span>
* <span data-ttu-id="90056-168">Erweiterungen wie [UseContentRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usecontentroot) und [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) (siehe Abschnitt [Überschreiben der Konfiguration](#override-configuration)).</span><span class="sxs-lookup"><span data-stu-id="90056-168">Extensions such as [UseContentRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usecontentroot) and [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) (see the [Override configuration](#override-configuration) section).</span></span>
* <span data-ttu-id="90056-169">[UseSetting](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.usesetting) und der zugeordnete Schlüssel.</span><span class="sxs-lookup"><span data-stu-id="90056-169">[UseSetting](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.usesetting) and the associated key.</span></span> <span data-ttu-id="90056-170">Wenn Sie einen Wert mit `UseSetting` festlegen, wird dieser unabhängig vom Typ als Zeichenfolge festgelegt.</span><span class="sxs-lookup"><span data-stu-id="90056-170">When setting a value with `UseSetting`, the value is set as a string regardless of the type.</span></span>

<span data-ttu-id="90056-171">Der Host verwendet die Option, die zuletzt einen Wert festlegt.</span><span class="sxs-lookup"><span data-stu-id="90056-171">The host uses whichever option sets a value last.</span></span> <span data-ttu-id="90056-172">Weitere Informationen finden Sie im nächsten Abschnitt unter [Außerkraftsetzen der Konfiguration](#override-configuration).</span><span class="sxs-lookup"><span data-stu-id="90056-172">For more information, see [Override configuration](#override-configuration) in the next section.</span></span>

### <a name="application-key-name"></a><span data-ttu-id="90056-173">Anwendungsschlüssel (Name)</span><span class="sxs-lookup"><span data-stu-id="90056-173">Application Key (Name)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="90056-174">Die Eigenschaft `IWebHostEnvironment.ApplicationName` wird automatisch festgelegt, wenn [UseStartup](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usestartup) oder [Configure](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configure) während der Hosterstellung aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="90056-174">The `IWebHostEnvironment.ApplicationName` property is automatically set when [UseStartup](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usestartup) or [Configure](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configure) is called during host construction.</span></span> <span data-ttu-id="90056-175">Der Wert wird auf den Namen der Assembly festgelegt, die den Einstiegspunkt der App enthält.</span><span class="sxs-lookup"><span data-stu-id="90056-175">The value is set to the name of the assembly containing the app's entry point.</span></span> <span data-ttu-id="90056-176">Um den Wert explizit festzulegen, verwenden Sie den [WebHostDefaults.ApplicationKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.applicationkey):</span><span class="sxs-lookup"><span data-stu-id="90056-176">To set the value explicitly, use the [WebHostDefaults.ApplicationKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.applicationkey):</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="90056-177">Die Eigenschaft [IHostingEnvironment.ApplicationName](/dotnet/api/microsoft.extensions.hosting.ihostingenvironment.applicationname) wird automatisch festgelegt, wenn [UseStartup](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usestartup) oder [Configure](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configure) während der Hosterstellung aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="90056-177">The [IHostingEnvironment.ApplicationName](/dotnet/api/microsoft.extensions.hosting.ihostingenvironment.applicationname) property is automatically set when [UseStartup](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usestartup) or [Configure](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configure) is called during host construction.</span></span> <span data-ttu-id="90056-178">Der Wert wird auf den Namen der Assembly festgelegt, die den Einstiegspunkt der App enthält.</span><span class="sxs-lookup"><span data-stu-id="90056-178">The value is set to the name of the assembly containing the app's entry point.</span></span> <span data-ttu-id="90056-179">Um den Wert explizit festzulegen, verwenden Sie den [WebHostDefaults.ApplicationKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.applicationkey):</span><span class="sxs-lookup"><span data-stu-id="90056-179">To set the value explicitly, use the [WebHostDefaults.ApplicationKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.applicationkey):</span></span>

::: moniker-end

<span data-ttu-id="90056-180">**Schlüssel:** Anwendungsname</span><span class="sxs-lookup"><span data-stu-id="90056-180">**Key**: applicationName</span></span>  
<span data-ttu-id="90056-181">**Typ:** *Zeichenfolge*</span><span class="sxs-lookup"><span data-stu-id="90056-181">**Type**: *string*</span></span>  
<span data-ttu-id="90056-182">**Standardwert**: Der Name der Assembly, die den Einstiegspunkt der App enthält.</span><span class="sxs-lookup"><span data-stu-id="90056-182">**Default**: The name of the assembly containing the app's entry point.</span></span>  
<span data-ttu-id="90056-183">**Festlegen mit:** `UseSetting`</span><span class="sxs-lookup"><span data-stu-id="90056-183">**Set using**: `UseSetting`</span></span>  
<span data-ttu-id="90056-184">**Umgebungsvariable:** `ASPNETCORE_APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="90056-184">**Environment variable**: `ASPNETCORE_APPLICATIONNAME`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.ApplicationKey, "CustomApplicationName")
```

### <a name="capture-startup-errors"></a><span data-ttu-id="90056-185">Erfassen von Startfehlern</span><span class="sxs-lookup"><span data-stu-id="90056-185">Capture Startup Errors</span></span>

<span data-ttu-id="90056-186">Diese Einstellung steuert das Erfassen von Startfehlern.</span><span class="sxs-lookup"><span data-stu-id="90056-186">This setting controls the capture of startup errors.</span></span>

<span data-ttu-id="90056-187">**Schlüssel:** captureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="90056-187">**Key**: captureStartupErrors</span></span>  
<span data-ttu-id="90056-188">**Typ:** *Boolesch* (`true` or `1`)</span><span class="sxs-lookup"><span data-stu-id="90056-188">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="90056-189">**Standard**: Die Standardeinstellung ist gleich `false`, es sei denn, die App wird mit Kestrel hinter IIS ausgeführt, dann ist sie gleich `true`.</span><span class="sxs-lookup"><span data-stu-id="90056-189">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="90056-190">**Festlegen mit:** `CaptureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="90056-190">**Set using**: `CaptureStartupErrors`</span></span>  
<span data-ttu-id="90056-191">**Umgebungsvariable:** `ASPNETCORE_CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="90056-191">**Environment variable**: `ASPNETCORE_CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="90056-192">Wenn diese `false` ist, führen Fehler beim Start zum Beenden des Hosts.</span><span class="sxs-lookup"><span data-stu-id="90056-192">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="90056-193">Wenn diese `true` ist, erfasst der Host Ausnahmen während des Starts und versucht, den Server zu starten.</span><span class="sxs-lookup"><span data-stu-id="90056-193">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .CaptureStartupErrors(true)
```

### <a name="content-root"></a><span data-ttu-id="90056-194">Inhaltsstammverzeichnis</span><span class="sxs-lookup"><span data-stu-id="90056-194">Content root</span></span>

<span data-ttu-id="90056-195">Diese Einstellung bestimmt, wo ASP.NET mit der Suche nach Inhaltsdateien beginnt.</span><span class="sxs-lookup"><span data-stu-id="90056-195">This setting determines where ASP.NET Core begins searching for content files.</span></span>

<span data-ttu-id="90056-196">**Schlüssel:** contentRoot</span><span class="sxs-lookup"><span data-stu-id="90056-196">**Key**: contentRoot</span></span>  
<span data-ttu-id="90056-197">**Typ:** *Zeichenfolge*</span><span class="sxs-lookup"><span data-stu-id="90056-197">**Type**: *string*</span></span>  
<span data-ttu-id="90056-198">**Standard**: Entspricht standardmäßig dem Ordner, in dem die App-Assembly gespeichert ist.</span><span class="sxs-lookup"><span data-stu-id="90056-198">**Default**: Defaults to the folder where the app assembly resides.</span></span>  
<span data-ttu-id="90056-199">**Festlegen mit:** `UseContentRoot`</span><span class="sxs-lookup"><span data-stu-id="90056-199">**Set using**: `UseContentRoot`</span></span>  
<span data-ttu-id="90056-200">**Umgebungsvariable:** `ASPNETCORE_CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="90056-200">**Environment variable**: `ASPNETCORE_CONTENTROOT`</span></span>

<span data-ttu-id="90056-201">Das Inhaltsstammverzeichnis wird ebenfalls als Basispfad für das [Webstammverzeichnis](xref:fundamentals/index#web-root) verwendet.</span><span class="sxs-lookup"><span data-stu-id="90056-201">The content root is also used as the base path for the [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="90056-202">Wenn der Pfad des Inhaltsstammverzeichnisses nicht vorhanden ist, kann der Host nicht gestartet werden.</span><span class="sxs-lookup"><span data-stu-id="90056-202">If the content root path doesn't exist, the host fails to start.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\<content-root>")
```

<span data-ttu-id="90056-203">Weitere Informationen finden Sie unter:</span><span class="sxs-lookup"><span data-stu-id="90056-203">For more information, see:</span></span>

* [<span data-ttu-id="90056-204">Grundlagen: Inhaltsstammverzeichnis</span><span class="sxs-lookup"><span data-stu-id="90056-204">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="90056-205">Webstammverzeichnis</span><span class="sxs-lookup"><span data-stu-id="90056-205">Web root</span></span>](#web-root)

### <a name="detailed-errors"></a><span data-ttu-id="90056-206">Detaillierte Fehler</span><span class="sxs-lookup"><span data-stu-id="90056-206">Detailed Errors</span></span>

<span data-ttu-id="90056-207">Bestimmt, ob detaillierte Fehler erfasst werden sollen.</span><span class="sxs-lookup"><span data-stu-id="90056-207">Determines if detailed errors should be captured.</span></span>

<span data-ttu-id="90056-208">**Schlüssel:** detailedErrors</span><span class="sxs-lookup"><span data-stu-id="90056-208">**Key**: detailedErrors</span></span>  
<span data-ttu-id="90056-209">**Typ:** *Boolesch* (`true` or `1`)</span><span class="sxs-lookup"><span data-stu-id="90056-209">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="90056-210">**Standard:** FALSE</span><span class="sxs-lookup"><span data-stu-id="90056-210">**Default**: false</span></span>  
<span data-ttu-id="90056-211">**Festlegen mit:** `UseSetting`</span><span class="sxs-lookup"><span data-stu-id="90056-211">**Set using**: `UseSetting`</span></span>  
<span data-ttu-id="90056-212">**Umgebungsvariable:** `ASPNETCORE_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="90056-212">**Environment variable**: `ASPNETCORE_DETAILEDERRORS`</span></span>

<span data-ttu-id="90056-213">Wenn diese Einstellung aktiviert ist (oder die <a href="#environment">Umgebung</a> auf `Development` festgelegt ist), erfasst die App detaillierte Ausnahmen.</span><span class="sxs-lookup"><span data-stu-id="90056-213">When enabled (or when the <a href="#environment">Environment</a> is set to `Development`), the app captures detailed exceptions.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.DetailedErrorsKey, "true")
```

### <a name="environment"></a><span data-ttu-id="90056-214">Umgebung</span><span class="sxs-lookup"><span data-stu-id="90056-214">Environment</span></span>

<span data-ttu-id="90056-215">Legt die Umgebung der App fest.</span><span class="sxs-lookup"><span data-stu-id="90056-215">Sets the app's environment.</span></span>

<span data-ttu-id="90056-216">**Schlüssel:** environment</span><span class="sxs-lookup"><span data-stu-id="90056-216">**Key**: environment</span></span>  
<span data-ttu-id="90056-217">**Typ:** *Zeichenfolge*</span><span class="sxs-lookup"><span data-stu-id="90056-217">**Type**: *string*</span></span>  
<span data-ttu-id="90056-218">**Standard**: Produktion</span><span class="sxs-lookup"><span data-stu-id="90056-218">**Default**: Production</span></span>  
<span data-ttu-id="90056-219">**Festlegen mit:** `UseEnvironment`</span><span class="sxs-lookup"><span data-stu-id="90056-219">**Set using**: `UseEnvironment`</span></span>  
<span data-ttu-id="90056-220">**Umgebungsvariable:** `ASPNETCORE_ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="90056-220">**Environment variable**: `ASPNETCORE_ENVIRONMENT`</span></span>

<span data-ttu-id="90056-221">Die Umgebung kann auf einen beliebigen Wert festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="90056-221">The environment can be set to any value.</span></span> <span data-ttu-id="90056-222">Zu den durch Frameworks definierten Werten zählen `Development`, `Staging` und `Production`.</span><span class="sxs-lookup"><span data-stu-id="90056-222">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="90056-223">Die Groß-/Kleinschreibung wird bei Werten nicht beachtet.</span><span class="sxs-lookup"><span data-stu-id="90056-223">Values aren't case sensitive.</span></span> <span data-ttu-id="90056-224">Standardmäßig wird die *Umgebung* aus der `ASPNETCORE_ENVIRONMENT`-Umgebungsvariablen gelesen.</span><span class="sxs-lookup"><span data-stu-id="90056-224">By default, the *Environment* is read from the `ASPNETCORE_ENVIRONMENT` environment variable.</span></span> <span data-ttu-id="90056-225">Wenn Sie [Visual Studio](https://visualstudio.microsoft.com) verwenden, werden Umgebungsvariablen möglicherweise in der Datei *launchSettings.json* festgelegt.</span><span class="sxs-lookup"><span data-stu-id="90056-225">When using [Visual Studio](https://visualstudio.microsoft.com), environment variables may be set in the *launchSettings.json* file.</span></span> <span data-ttu-id="90056-226">Weitere Informationen finden Sie unter <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="90056-226">For more information, see <xref:fundamentals/environments>.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseEnvironment(EnvironmentName.Development)
```

### <a name="hosting-startup-assemblies"></a><span data-ttu-id="90056-227">Hostingstartassemblys</span><span class="sxs-lookup"><span data-stu-id="90056-227">Hosting Startup Assemblies</span></span>

<span data-ttu-id="90056-228">Legt die Hostingstartassemblys der App fest.</span><span class="sxs-lookup"><span data-stu-id="90056-228">Sets the app's hosting startup assemblies.</span></span>

<span data-ttu-id="90056-229">**Schlüssel:** hostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="90056-229">**Key**: hostingStartupAssemblies</span></span>  
<span data-ttu-id="90056-230">**Typ:** *Zeichenfolge*</span><span class="sxs-lookup"><span data-stu-id="90056-230">**Type**: *string*</span></span>  
<span data-ttu-id="90056-231">**Standard**: Leere Zeichenfolge</span><span class="sxs-lookup"><span data-stu-id="90056-231">**Default**: Empty string</span></span>  
<span data-ttu-id="90056-232">**Festlegen mit:** `UseSetting`</span><span class="sxs-lookup"><span data-stu-id="90056-232">**Set using**: `UseSetting`</span></span>  
<span data-ttu-id="90056-233">**Umgebungsvariable:** `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="90056-233">**Environment variable**: `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="90056-234">Eine durch Semikolons getrennte Zeichenfolge der Hostingstartassemblys, die beim Start geladen werden soll.</span><span class="sxs-lookup"><span data-stu-id="90056-234">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span>

<span data-ttu-id="90056-235">Obwohl der Konfigurationswert standardmäßig auf eine leere Zeichenfolge festgelegt ist, enthalten die Hostingstartassemblys immer die Assembly der App.</span><span class="sxs-lookup"><span data-stu-id="90056-235">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="90056-236">Wenn Hostingstartassemblys bereitgestellt werden, werden diese zur Assembly der App hinzugefügt, damit diese geladen werden, wenn die App während des Starts die allgemeinen Dienste erstellt.</span><span class="sxs-lookup"><span data-stu-id="90056-236">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2")
```

### <a name="https-port"></a><span data-ttu-id="90056-237">HTTPS-Anschluss</span><span class="sxs-lookup"><span data-stu-id="90056-237">HTTPS Port</span></span>

<span data-ttu-id="90056-238">Legen Sie den HTTPS-Umleitungsport fest.</span><span class="sxs-lookup"><span data-stu-id="90056-238">Set the HTTPS redirect port.</span></span> <span data-ttu-id="90056-239">Wird in [Erzwingen von HTTPS](xref:security/enforcing-ssl) verwendet.</span><span class="sxs-lookup"><span data-stu-id="90056-239">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="90056-240">**Schlüssel**: https_port **Typ**: *Zeichenfolge*
**Standardwert**: Es ist kein Standardwert festgelegt.</span><span class="sxs-lookup"><span data-stu-id="90056-240">**Key**: https_port **Type**: *string*
**Default**: A default value isn't set.</span></span>
<span data-ttu-id="90056-241">**Festlegen mit**: `UseSetting`
**Umgebungsvariable**: `ASPNETCORE_HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="90056-241">**Set using**: `UseSetting`
**Environment variable**: `ASPNETCORE_HTTPS_PORT`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting("https_port", "8080")
```

### <a name="hosting-startup-exclude-assemblies"></a><span data-ttu-id="90056-242">Auszuschließende Hostingstartassemblys</span><span class="sxs-lookup"><span data-stu-id="90056-242">Hosting Startup Exclude Assemblies</span></span>

<span data-ttu-id="90056-243">Eine durch Semikolons getrennte Zeichenfolge der Hostingstartassemblys, die beim Start ausgeschlossen werden sollen.</span><span class="sxs-lookup"><span data-stu-id="90056-243">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="90056-244">**Schlüssel**: hostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="90056-244">**Key**: hostingStartupExcludeAssemblies</span></span>  
<span data-ttu-id="90056-245">**Typ:** *Zeichenfolge*</span><span class="sxs-lookup"><span data-stu-id="90056-245">**Type**: *string*</span></span>  
<span data-ttu-id="90056-246">**Standard**: Leere Zeichenfolge</span><span class="sxs-lookup"><span data-stu-id="90056-246">**Default**: Empty string</span></span>  
<span data-ttu-id="90056-247">**Festlegen mit:** `UseSetting`</span><span class="sxs-lookup"><span data-stu-id="90056-247">**Set using**: `UseSetting`</span></span>  
<span data-ttu-id="90056-248">**Umgebungsvariable:** `ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="90056-248">**Environment variable**: `ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2")
```

### <a name="prefer-hosting-urls"></a><span data-ttu-id="90056-249">Bevorzugen von Hosting-URLs</span><span class="sxs-lookup"><span data-stu-id="90056-249">Prefer Hosting URLs</span></span>

<span data-ttu-id="90056-250">Gibt an, ob der Hosts den URLs lauschen soll, die mit `WebHostBuilder` konfiguriert wurden, statt denen, die mit der `IServer`-Implementierung konfiguriert wurden.</span><span class="sxs-lookup"><span data-stu-id="90056-250">Indicates whether the host should listen on the URLs configured with the `WebHostBuilder` instead of those configured with the `IServer` implementation.</span></span>

<span data-ttu-id="90056-251">**Schlüssel:** preferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="90056-251">**Key**: preferHostingUrls</span></span>  
<span data-ttu-id="90056-252">**Typ:** *Boolesch* (`true` or `1`)</span><span class="sxs-lookup"><span data-stu-id="90056-252">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="90056-253">**Standard:** TRUE</span><span class="sxs-lookup"><span data-stu-id="90056-253">**Default**: true</span></span>  
<span data-ttu-id="90056-254">**Festlegen mit:** `PreferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="90056-254">**Set using**: `PreferHostingUrls`</span></span>  
<span data-ttu-id="90056-255">**Umgebungsvariable:** `ASPNETCORE_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="90056-255">**Environment variable**: `ASPNETCORE_PREFERHOSTINGURLS`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .PreferHostingUrls(false)
```

### <a name="prevent-hosting-startup"></a><span data-ttu-id="90056-256">Verhindern des Hostingstarts</span><span class="sxs-lookup"><span data-stu-id="90056-256">Prevent Hosting Startup</span></span>

<span data-ttu-id="90056-257">Verhindert das automatische Laden der Hostingstartassemblys, einschließlich denen, die von der Assembly der App konfiguriert wurden.</span><span class="sxs-lookup"><span data-stu-id="90056-257">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="90056-258">Weitere Informationen finden Sie unter <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="90056-258">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="90056-259">**Schlüssel:** preventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="90056-259">**Key**: preventHostingStartup</span></span>  
<span data-ttu-id="90056-260">**Typ:** *Boolesch* (`true` or `1`)</span><span class="sxs-lookup"><span data-stu-id="90056-260">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="90056-261">**Standard:** FALSE</span><span class="sxs-lookup"><span data-stu-id="90056-261">**Default**: false</span></span>  
<span data-ttu-id="90056-262">**Festlegen mit:** `UseSetting`</span><span class="sxs-lookup"><span data-stu-id="90056-262">**Set using**: `UseSetting`</span></span>  
<span data-ttu-id="90056-263">**Umgebungsvariable:** `ASPNETCORE_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="90056-263">**Environment variable**: `ASPNETCORE_PREVENTHOSTINGSTARTUP`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.PreventHostingStartupKey, "true")
```

### <a name="server-urls"></a><span data-ttu-id="90056-264">Server-URLs</span><span class="sxs-lookup"><span data-stu-id="90056-264">Server URLs</span></span>

<span data-ttu-id="90056-265">Gibt die IP-Adressen oder Hostadressen mit Ports und Protokollen an, denen der Server für Anforderungen lauschen soll.</span><span class="sxs-lookup"><span data-stu-id="90056-265">Indicates the IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span>

<span data-ttu-id="90056-266">**Schlüssel:** urls</span><span class="sxs-lookup"><span data-stu-id="90056-266">**Key**: urls</span></span>  
<span data-ttu-id="90056-267">**Typ:** *Zeichenfolge*</span><span class="sxs-lookup"><span data-stu-id="90056-267">**Type**: *string*</span></span>  
<span data-ttu-id="90056-268">**Standard**: http://localhost:5000</span><span class="sxs-lookup"><span data-stu-id="90056-268">**Default**: http://localhost:5000</span></span>  
<span data-ttu-id="90056-269">**Festlegen mit:** `UseUrls`</span><span class="sxs-lookup"><span data-stu-id="90056-269">**Set using**: `UseUrls`</span></span>  
<span data-ttu-id="90056-270">**Umgebungsvariable:** `ASPNETCORE_URLS`</span><span class="sxs-lookup"><span data-stu-id="90056-270">**Environment variable**: `ASPNETCORE_URLS`</span></span>

<span data-ttu-id="90056-271">Legt die Einstellung auf eine durch Semikolons (;) getrennte Liste von URL-Präfixen fest, auf die der Server reagieren soll.</span><span class="sxs-lookup"><span data-stu-id="90056-271">Set to a semicolon-separated (;) list of URL prefixes to which the server should respond.</span></span> <span data-ttu-id="90056-272">Beispielsweise `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="90056-272">For example, `http://localhost:123`.</span></span> <span data-ttu-id="90056-273">Verwenden Sie \*, um anzugeben, dass der Server mithilfe des angegebenen Ports und Protokolls (z.B. `http://*:5000`) den Anfragen aller IP-Adressen oder Hostnamen lauschen soll.</span><span class="sxs-lookup"><span data-stu-id="90056-273">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="90056-274">Das Protokoll (`http://` oder `https://`) muss in jeder URL enthalten sein.</span><span class="sxs-lookup"><span data-stu-id="90056-274">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="90056-275">Die unterstützten Formate variieren bei den verschiedenen Servern.</span><span class="sxs-lookup"><span data-stu-id="90056-275">Supported formats vary among servers.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002")
```

<span data-ttu-id="90056-276">Kestrel verfügt über eine eigene API für die Endpunktkonfiguration.</span><span class="sxs-lookup"><span data-stu-id="90056-276">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="90056-277">Weitere Informationen finden Sie unter <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="90056-277">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="shutdown-timeout"></a><span data-ttu-id="90056-278">Timeout beim Herunterfahren</span><span class="sxs-lookup"><span data-stu-id="90056-278">Shutdown Timeout</span></span>

<span data-ttu-id="90056-279">Gibt die Wartezeit an, bis der Webhost heruntergefahren wird.</span><span class="sxs-lookup"><span data-stu-id="90056-279">Specifies the amount of time to wait for Web Host to shut down.</span></span>

<span data-ttu-id="90056-280">**Schlüssel:** shutdownTimeoutSeconds</span><span class="sxs-lookup"><span data-stu-id="90056-280">**Key**: shutdownTimeoutSeconds</span></span>  
<span data-ttu-id="90056-281">**Typ:** *Ganze Zahl*</span><span class="sxs-lookup"><span data-stu-id="90056-281">**Type**: *int*</span></span>  
<span data-ttu-id="90056-282">**Standard**: 5</span><span class="sxs-lookup"><span data-stu-id="90056-282">**Default**: 5</span></span>  
<span data-ttu-id="90056-283">**Festlegen mit:** `UseShutdownTimeout`</span><span class="sxs-lookup"><span data-stu-id="90056-283">**Set using**: `UseShutdownTimeout`</span></span>  
<span data-ttu-id="90056-284">**Umgebungsvariable:** `ASPNETCORE_SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="90056-284">**Environment variable**: `ASPNETCORE_SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="90056-285">Obwohl der Schlüssel eine *ganze Zahl* mit `UseSetting` (z.B. `.UseSetting(WebHostDefaults.ShutdownTimeoutKey, "10")`) akzeptiert, akzeptiert die Erweiterungsmethode [UseShutdownTimeout](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useshutdowntimeout) ein [TimeSpan](/dotnet/api/system.timespan)-Objekt.</span><span class="sxs-lookup"><span data-stu-id="90056-285">Although the key accepts an *int* with `UseSetting` (for example, `.UseSetting(WebHostDefaults.ShutdownTimeoutKey, "10")`), the [UseShutdownTimeout](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useshutdowntimeout) extension method takes a [TimeSpan](/dotnet/api/system.timespan).</span></span>

<span data-ttu-id="90056-286">Während des Zeitlimits, verhält sich das Hosting folgendermaßen:</span><span class="sxs-lookup"><span data-stu-id="90056-286">During the timeout period, hosting:</span></span>

* <span data-ttu-id="90056-287">Es löst [IApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.applicationstopping) aus.</span><span class="sxs-lookup"><span data-stu-id="90056-287">Triggers [IApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="90056-288">Es versucht, gehostete Dienste zu beenden und protokolliert Fehler für Dienste, die nicht beendet werden können.</span><span class="sxs-lookup"><span data-stu-id="90056-288">Attempts to stop hosted services, logging any errors for services that fail to stop.</span></span>

<span data-ttu-id="90056-289">Wenn das Zeitlimit abläuft bevor alle gehosteten Dienste beendet sind, werden alle verbleibenden aktiven Dienste beendet, wenn die App herunterfährt.</span><span class="sxs-lookup"><span data-stu-id="90056-289">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="90056-290">Die Dienste werden beendet, selbst wenn die Verarbeitung noch nicht abgeschlossen ist.</span><span class="sxs-lookup"><span data-stu-id="90056-290">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="90056-291">Wenn die Dienste mehr Zeit zum Beenden benötigen, sollten Sie das Zeitlimit erhöhen.</span><span class="sxs-lookup"><span data-stu-id="90056-291">If services require additional time to stop, increase the timeout.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseShutdownTimeout(TimeSpan.FromSeconds(10))
```

### <a name="startup-assembly"></a><span data-ttu-id="90056-292">Startassembly</span><span class="sxs-lookup"><span data-stu-id="90056-292">Startup Assembly</span></span>

<span data-ttu-id="90056-293">Bestimmt die Assembly, die nach der `Startup`-Klasse suchen soll.</span><span class="sxs-lookup"><span data-stu-id="90056-293">Determines the assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="90056-294">**Schlüssel:** startupAssembly</span><span class="sxs-lookup"><span data-stu-id="90056-294">**Key**: startupAssembly</span></span>  
<span data-ttu-id="90056-295">**Typ:** *Zeichenfolge*</span><span class="sxs-lookup"><span data-stu-id="90056-295">**Type**: *string*</span></span>  
<span data-ttu-id="90056-296">**Standard**: Die Assembly der App</span><span class="sxs-lookup"><span data-stu-id="90056-296">**Default**: The app's assembly</span></span>  
<span data-ttu-id="90056-297">**Festlegen mit:** `UseStartup`</span><span class="sxs-lookup"><span data-stu-id="90056-297">**Set using**: `UseStartup`</span></span>  
<span data-ttu-id="90056-298">**Umgebungsvariable:** `ASPNETCORE_STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="90056-298">**Environment variable**: `ASPNETCORE_STARTUPASSEMBLY`</span></span>

<span data-ttu-id="90056-299">Es kann auf die Assembly nach Name (`string`) oder Typ (`TStartup`) verwiesen werden.</span><span class="sxs-lookup"><span data-stu-id="90056-299">The assembly by name (`string`) or type (`TStartup`) can be referenced.</span></span> <span data-ttu-id="90056-300">Wenn mehrere `UseStartup`-Methoden aufgerufen werden, hat die letzte Vorrang.</span><span class="sxs-lookup"><span data-stu-id="90056-300">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseStartup("StartupAssemblyName")
```

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseStartup<TStartup>()
```

### <a name="web-root"></a><span data-ttu-id="90056-301">Webstammverzeichnis</span><span class="sxs-lookup"><span data-stu-id="90056-301">Web root</span></span>

<span data-ttu-id="90056-302">Legt den relativen Pfad für die statischen Objekte der App fest.</span><span class="sxs-lookup"><span data-stu-id="90056-302">Sets the relative path to the app's static assets.</span></span>

<span data-ttu-id="90056-303">**Schlüssel:** webroot</span><span class="sxs-lookup"><span data-stu-id="90056-303">**Key**: webroot</span></span>  
<span data-ttu-id="90056-304">**Typ:** *Zeichenfolge*</span><span class="sxs-lookup"><span data-stu-id="90056-304">**Type**: *string*</span></span>  
<span data-ttu-id="90056-305">**Standard**: Der Standardwert ist `wwwroot`.</span><span class="sxs-lookup"><span data-stu-id="90056-305">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="90056-306">Der Pfad zu *{Inhaltsstammverzeichnis}/wwwroot* muss vorhanden sein.</span><span class="sxs-lookup"><span data-stu-id="90056-306">The path to *{content root}/wwwroot* must exist.</span></span> <span data-ttu-id="90056-307">Wenn der Pfad nicht vorhanden ist, wird ein Dateianbieter ohne Funktion verwendet.</span><span class="sxs-lookup"><span data-stu-id="90056-307">If the path doesn't exist, a no-op file provider is used.</span></span>  
<span data-ttu-id="90056-308">**Festlegen mit:** `UseWebRoot`</span><span class="sxs-lookup"><span data-stu-id="90056-308">**Set using**: `UseWebRoot`</span></span>  
<span data-ttu-id="90056-309">**Umgebungsvariable:** `ASPNETCORE_WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="90056-309">**Environment variable**: `ASPNETCORE_WEBROOT`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseWebRoot("public")
```

<span data-ttu-id="90056-310">Weitere Informationen finden Sie unter:</span><span class="sxs-lookup"><span data-stu-id="90056-310">For more information, see:</span></span>

* [<span data-ttu-id="90056-311">Grundlagen: Webstammverzeichnis</span><span class="sxs-lookup"><span data-stu-id="90056-311">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="90056-312">Inhaltsstammverzeichnis</span><span class="sxs-lookup"><span data-stu-id="90056-312">Content root</span></span>](#content-root)

## <a name="override-configuration"></a><span data-ttu-id="90056-313">Außerkraftsetzen der Konfiguration</span><span class="sxs-lookup"><span data-stu-id="90056-313">Override configuration</span></span>

<span data-ttu-id="90056-314">Verwenden Sie die [Konfiguration](xref:fundamentals/configuration/index), um den Webhost zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="90056-314">Use [Configuration](xref:fundamentals/configuration/index) to configure Web Host.</span></span> <span data-ttu-id="90056-315">Im folgenden Beispiel wird die Hostkonfiguration optional in einer *hostsettings.json*-Datei angegeben.</span><span class="sxs-lookup"><span data-stu-id="90056-315">In the following example, host configuration is optionally specified in a *hostsettings.json* file.</span></span> <span data-ttu-id="90056-316">Jede Konfiguration, die aus der Datei *hostsettings.json* geladen wird, kann von Befehlszeilenargumenten überschrieben werden.</span><span class="sxs-lookup"><span data-stu-id="90056-316">Any configuration loaded from the *hostsettings.json* file may be overridden by command-line arguments.</span></span> <span data-ttu-id="90056-317">Die erstellte Konfiguration (in `config`) wird verwendet, um den Host mit [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="90056-317">The built configuration (in `config`) is used to configure the host with [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration).</span></span> <span data-ttu-id="90056-318">Der Konfiguration der App wird die `IWebHostBuilder`-Konfiguration hinzugefügt, das Gegenteil gilt jedoch nicht. `ConfigureAppConfiguration` hat keinen Einfluss auf die `IWebHostBuilder`-Konfiguration.</span><span class="sxs-lookup"><span data-stu-id="90056-318">`IWebHostBuilder` configuration is added to the app's configuration, but the converse isn't true&mdash;`ConfigureAppConfiguration` doesn't affect the `IWebHostBuilder` configuration.</span></span>

<span data-ttu-id="90056-319">Das Überschreiben der Konfiguration wird von `UseUrls` bereitgestellt, hierbei kommt die Konfiguration *hostsettings.json* zuerst und anschließend die Konfiguration der Befehlszeilenargumente:</span><span class="sxs-lookup"><span data-stu-id="90056-319">Overriding the configuration provided by `UseUrls` with *hostsettings.json* config first, command-line argument config second:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args)
    {
        var config = new ConfigurationBuilder()
            .SetBasePath(Directory.GetCurrentDirectory())
            .AddJsonFile("hostsettings.json", optional: true)
            .AddCommandLine(args)
            .Build();

        return WebHost.CreateDefaultBuilder(args)
            .UseUrls("http://*:5000")
            .UseConfiguration(config)
            .Configure(app =>
            {
                app.Run(context => 
                    context.Response.WriteAsync("Hello, World!"));
            });
    }
}
```

<span data-ttu-id="90056-320">*hostsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="90056-320">*hostsettings.json*:</span></span>

```json
{
    urls: "http://*:5005"
}
```

> [!NOTE]
> <span data-ttu-id="90056-321">[UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) kopiert nur Schlüssel aus der bereitgestellten `IConfiguration`-Schnittstelle in die Konfiguration des Host-Generators.</span><span class="sxs-lookup"><span data-stu-id="90056-321">[UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) only copies keys from the provided `IConfiguration` to the host builder configuration.</span></span> <span data-ttu-id="90056-322">Daher hat das Festlegen von `reloadOnChange: true` für JSON-, INI- und XML-Einstellungsdateien keine Auswirkung.</span><span class="sxs-lookup"><span data-stu-id="90056-322">Therefore, setting `reloadOnChange: true` for JSON, INI, and XML settings files has no effect.</span></span>

<span data-ttu-id="90056-323">Damit der Host auf einer bestimmten URL ausgeführt wird, kann der gewünschte Wert von der Befehlszeile aus übergeben werden, wenn [dotnet run](/dotnet/core/tools/dotnet-run) ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="90056-323">To specify the host run on a particular URL, the desired value can be passed in from a command prompt when executing [dotnet run](/dotnet/core/tools/dotnet-run).</span></span> <span data-ttu-id="90056-324">Das Befehlszeilenargument überschreibt den `urls`-Wert der *hostsettings.json*-Datei, und der Server lauscht Port 8080:</span><span class="sxs-lookup"><span data-stu-id="90056-324">The command-line argument overrides the `urls` value from the *hostsettings.json* file, and the server listens on port 8080:</span></span>

```dotnetcli
dotnet run --urls "http://*:8080"
```

## <a name="manage-the-host"></a><span data-ttu-id="90056-325">Verwalten des Hosts</span><span class="sxs-lookup"><span data-stu-id="90056-325">Manage the host</span></span>

<span data-ttu-id="90056-326">**Run**</span><span class="sxs-lookup"><span data-stu-id="90056-326">**Run**</span></span>

<span data-ttu-id="90056-327">Die `Run`-Methode startet die Web-App und blockiert den aufrufenden Thread, bis der Host heruntergefahren wird:</span><span class="sxs-lookup"><span data-stu-id="90056-327">The `Run` method starts the web app and blocks the calling thread until the host is shut down:</span></span>

```csharp
host.Run();
```

<span data-ttu-id="90056-328">**Starten**</span><span class="sxs-lookup"><span data-stu-id="90056-328">**Start**</span></span>

<span data-ttu-id="90056-329">Führen Sie den Host auf nicht blockierende Weise aus, indem Sie dessen `Start`-Methode aufrufen:</span><span class="sxs-lookup"><span data-stu-id="90056-329">Run the host in a non-blocking manner by calling its `Start` method:</span></span>

```csharp
using (host)
{
    host.Start();
    Console.ReadLine();
}
```

<span data-ttu-id="90056-330">Wenn eine Liste mit URLs an die `Start`-Methode übergeben wird, lauscht diese den angegebenen URLs:</span><span class="sxs-lookup"><span data-stu-id="90056-330">If a list of URLs is passed to the `Start` method, it listens on the URLs specified:</span></span>

```csharp
var urls = new List<string>()
{
    "http://*:5000",
    "http://localhost:5001"
};

var host = new WebHostBuilder()
    .UseKestrel()
    .UseStartup<Startup>()
    .Start(urls.ToArray());

using (host)
{
    Console.ReadLine();
}
```

<span data-ttu-id="90056-331">Die App kann einen neuen Host, der die vorab konfigurierten Standardwerte von `CreateDefaultBuilder` verwendet, mithilfe einer statischen Hilfsmethode initialisieren und starten.</span><span class="sxs-lookup"><span data-stu-id="90056-331">The app can initialize and start a new host using the pre-configured defaults of `CreateDefaultBuilder` using a static convenience method.</span></span> <span data-ttu-id="90056-332">Diese Methoden starten den Server ohne Konsolenausgabe und mit der [WaitForShutdown](/dotnet/api/microsoft.aspnetcore.hosting.webhostextensions.waitforshutdown)-Methode, die auf eine Unterbrechung wartet (STRG+C/SIGINT oder SIGTERM):</span><span class="sxs-lookup"><span data-stu-id="90056-332">These methods start the server without console output and with [WaitForShutdown](/dotnet/api/microsoft.aspnetcore.hosting.webhostextensions.waitforshutdown) wait for a break (Ctrl-C/SIGINT or SIGTERM):</span></span>

<span data-ttu-id="90056-333">**Start(RequestDelegate app)**</span><span class="sxs-lookup"><span data-stu-id="90056-333">**Start(RequestDelegate app)**</span></span>

<span data-ttu-id="90056-334">Beginnend mit einer `RequestDelegate`-Funktion:</span><span class="sxs-lookup"><span data-stu-id="90056-334">Start with a `RequestDelegate`:</span></span>

```csharp
using (var host = WebHost.Start(app => app.Response.WriteAsync("Hello, World!")))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="90056-335">Stellen Sie im Browser eine Anforderung an `http://localhost:5000`, um die Antwort „Hello World!“ zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="90056-335">Make a request in the browser to `http://localhost:5000` to receive the response "Hello World!"</span></span> <span data-ttu-id="90056-336">`WaitForShutdown` blockiert, bis eine Unterbrechung (STRG+C/SIGINT oder SIGTERM) ausgelöst wird.</span><span class="sxs-lookup"><span data-stu-id="90056-336">`WaitForShutdown` blocks until a break (Ctrl-C/SIGINT or SIGTERM) is issued.</span></span> <span data-ttu-id="90056-337">Die App zeigt die `Console.WriteLine`-Meldung an und wartet, bis diese durch Tastendruck beendet wird.</span><span class="sxs-lookup"><span data-stu-id="90056-337">The app displays the `Console.WriteLine` message and waits for a keypress to exit.</span></span>

<span data-ttu-id="90056-338">**Start(string url, RequestDelegate app)**</span><span class="sxs-lookup"><span data-stu-id="90056-338">**Start(string url, RequestDelegate app)**</span></span>

<span data-ttu-id="90056-339">Beginnend mit einer URL und `RequestDelegate`:</span><span class="sxs-lookup"><span data-stu-id="90056-339">Start with a URL and `RequestDelegate`:</span></span>

```csharp
using (var host = WebHost.Start("http://localhost:8080", app => app.Response.WriteAsync("Hello, World!")))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="90056-340">Erzeugt das gleiche Ergebnis wie **Start(RequestDelegate app)** , mit dem Unterschied, dass die App auf `http://localhost:8080` reagiert.</span><span class="sxs-lookup"><span data-stu-id="90056-340">Produces the same result as **Start(RequestDelegate app)**, except the app responds on `http://localhost:8080`.</span></span>

<span data-ttu-id="90056-341">**Start(Action\<IRouteBuilder> routeBuilder)**</span><span class="sxs-lookup"><span data-stu-id="90056-341">**Start(Action\<IRouteBuilder> routeBuilder)**</span></span>

<span data-ttu-id="90056-342">Verwenden Sie eine Instanz von `IRouteBuilder` ([Microsoft.AspNetCore.Routing](https://www.nuget.org/packages/Microsoft.AspNetCore.Routing/)), um Routingmiddleware zu verwenden:</span><span class="sxs-lookup"><span data-stu-id="90056-342">Use an instance of `IRouteBuilder` ([Microsoft.AspNetCore.Routing](https://www.nuget.org/packages/Microsoft.AspNetCore.Routing/)) to use routing middleware:</span></span>

```csharp
using (var host = WebHost.Start(router => router
    .MapGet("hello/{name}", (req, res, data) => 
        res.WriteAsync($"Hello, {data.Values["name"]}!"))
    .MapGet("buenosdias/{name}", (req, res, data) => 
        res.WriteAsync($"Buenos dias, {data.Values["name"]}!"))
    .MapGet("throw/{message?}", (req, res, data) => 
        throw new Exception((string)data.Values["message"] ?? "Uh oh!"))
    .MapGet("{greeting}/{name}", (req, res, data) => 
        res.WriteAsync($"{data.Values["greeting"]}, {data.Values["name"]}!"))
    .MapGet("", (req, res, data) => res.WriteAsync("Hello, World!"))))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="90056-343">Verwenden Sie folgende Browseranforderung mit dem Beispiel:</span><span class="sxs-lookup"><span data-stu-id="90056-343">Use the following browser requests with the example:</span></span>

| <span data-ttu-id="90056-344">Anforderung</span><span class="sxs-lookup"><span data-stu-id="90056-344">Request</span></span>                                    | <span data-ttu-id="90056-345">Antwort</span><span class="sxs-lookup"><span data-stu-id="90056-345">Response</span></span>                                 |
| ------------------------------------------ | ---------------------------------------- |
| `http://localhost:5000/hello/Martin`       | <span data-ttu-id="90056-346">Hello, Martin!</span><span class="sxs-lookup"><span data-stu-id="90056-346">Hello, Martin!</span></span>                           |
| `http://localhost:5000/buenosdias/Catrina` | <span data-ttu-id="90056-347">Buenos dias, Catrina!</span><span class="sxs-lookup"><span data-stu-id="90056-347">Buenos dias, Catrina!</span></span>                    |
| `http://localhost:5000/throw/ooops!`       | <span data-ttu-id="90056-348">Löst eine Ausnahme mit der Zeichenfolge „ooops!“ aus.</span><span class="sxs-lookup"><span data-stu-id="90056-348">Throws an exception with string "ooops!"</span></span> |
| `http://localhost:5000/throw`              | <span data-ttu-id="90056-349">Löst eine Ausnahme mit der Zeichenfolge „Uh oh!“ aus.</span><span class="sxs-lookup"><span data-stu-id="90056-349">Throws an exception with string "Uh oh!"</span></span> |
| `http://localhost:5000/Sante/Kevin`        | <span data-ttu-id="90056-350">Sante, Kevin!</span><span class="sxs-lookup"><span data-stu-id="90056-350">Sante, Kevin!</span></span>                            |
| `http://localhost:5000`                    | <span data-ttu-id="90056-351">Hello World!</span><span class="sxs-lookup"><span data-stu-id="90056-351">Hello World!</span></span>                             |

<span data-ttu-id="90056-352">`WaitForShutdown` blockiert, bis eine Unterbrechung (STRG+C/SIGINT oder SIGTERM) ausgelöst wird.</span><span class="sxs-lookup"><span data-stu-id="90056-352">`WaitForShutdown` blocks until a break (Ctrl-C/SIGINT or SIGTERM) is issued.</span></span> <span data-ttu-id="90056-353">Die App zeigt die `Console.WriteLine`-Meldung an und wartet, bis diese durch Tastendruck beendet wird.</span><span class="sxs-lookup"><span data-stu-id="90056-353">The app displays the `Console.WriteLine` message and waits for a keypress to exit.</span></span>

<span data-ttu-id="90056-354">**Start(string url, Action\<IRouteBuilder> routeBuilder)**</span><span class="sxs-lookup"><span data-stu-id="90056-354">**Start(string url, Action\<IRouteBuilder> routeBuilder)**</span></span>

<span data-ttu-id="90056-355">Verwenden Sie eine URL und eine Instanz von `IRouteBuilder`:</span><span class="sxs-lookup"><span data-stu-id="90056-355">Use a URL and an instance of `IRouteBuilder`:</span></span>

```csharp
using (var host = WebHost.Start("http://localhost:8080", router => router
    .MapGet("hello/{name}", (req, res, data) => 
        res.WriteAsync($"Hello, {data.Values["name"]}!"))
    .MapGet("buenosdias/{name}", (req, res, data) => 
        res.WriteAsync($"Buenos dias, {data.Values["name"]}!"))
    .MapGet("throw/{message?}", (req, res, data) => 
        throw new Exception((string)data.Values["message"] ?? "Uh oh!"))
    .MapGet("{greeting}/{name}", (req, res, data) => 
        res.WriteAsync($"{data.Values["greeting"]}, {data.Values["name"]}!"))
    .MapGet("", (req, res, data) => res.WriteAsync("Hello, World!"))))
{
    Console.WriteLine("Use Ctrl-C to shut down the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="90056-356">Erzeugt das gleiche Ergebnis wie **Start(Action\<IRouteBuilder> routeBuilder)** , mit dem Unterschied, dass die App auf `http://localhost:8080` reagiert.</span><span class="sxs-lookup"><span data-stu-id="90056-356">Produces the same result as **Start(Action\<IRouteBuilder> routeBuilder)**, except the app responds at `http://localhost:8080`.</span></span>

<span data-ttu-id="90056-357">**StartWith(Action\<IApplicationBuilder> app)**</span><span class="sxs-lookup"><span data-stu-id="90056-357">**StartWith(Action\<IApplicationBuilder> app)**</span></span>

<span data-ttu-id="90056-358">Stellen Sie einen Delegaten bereit, um eine `IApplicationBuilder`-Schnittstelle zu konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="90056-358">Provide a delegate to configure an `IApplicationBuilder`:</span></span>

```csharp
using (var host = WebHost.StartWith(app => 
    app.Use(next => 
    {
        return async context => 
        {
            await context.Response.WriteAsync("Hello World!");
        };
    })))
{
    Console.WriteLine("Use Ctrl-C to shut down the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="90056-359">Stellen Sie im Browser eine Anforderung an `http://localhost:5000`, um die Antwort „Hello World!“ zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="90056-359">Make a request in the browser to `http://localhost:5000` to receive the response "Hello World!"</span></span> <span data-ttu-id="90056-360">`WaitForShutdown` blockiert, bis eine Unterbrechung (STRG+C/SIGINT oder SIGTERM) ausgelöst wird.</span><span class="sxs-lookup"><span data-stu-id="90056-360">`WaitForShutdown` blocks until a break (Ctrl-C/SIGINT or SIGTERM) is issued.</span></span> <span data-ttu-id="90056-361">Die App zeigt die `Console.WriteLine`-Meldung an und wartet, bis diese durch Tastendruck beendet wird.</span><span class="sxs-lookup"><span data-stu-id="90056-361">The app displays the `Console.WriteLine` message and waits for a keypress to exit.</span></span>

<span data-ttu-id="90056-362">**StartWith(string url, Action\<IApplicationBuilder> app)**</span><span class="sxs-lookup"><span data-stu-id="90056-362">**StartWith(string url, Action\<IApplicationBuilder> app)**</span></span>

<span data-ttu-id="90056-363">Stellen Sie eine URL und einen Delegaten bereit, um eine `IApplicationBuilder`-Schnittstelle zu konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="90056-363">Provide a URL and a delegate to configure an `IApplicationBuilder`:</span></span>

```csharp
using (var host = WebHost.StartWith("http://localhost:8080", app => 
    app.Use(next => 
    {
        return async context => 
        {
            await context.Response.WriteAsync("Hello World!");
        };
    })))
{
    Console.WriteLine("Use Ctrl-C to shut down the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="90056-364">Erzeugt das gleiche Ergebnis wie **StartWith(Action\<IApplicationBuilder> app)** , mit dem Unterschied, dass die App auf `http://localhost:8080` reagiert.</span><span class="sxs-lookup"><span data-stu-id="90056-364">Produces the same result as **StartWith(Action\<IApplicationBuilder> app)**, except the app responds on `http://localhost:8080`.</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="iwebhostenvironment-interface"></a><span data-ttu-id="90056-365">IWebHostEnvironment-Schnittstelle</span><span class="sxs-lookup"><span data-stu-id="90056-365">IWebHostEnvironment interface</span></span>

<span data-ttu-id="90056-366">Die `IWebHostEnvironment`-Schnittstelle stellt Informationen über die Webhostingumgebung der App bereit.</span><span class="sxs-lookup"><span data-stu-id="90056-366">The `IWebHostEnvironment` interface provides information about the app's web hosting environment.</span></span> <span data-ttu-id="90056-367">Verwenden Sie [Constructor Injection](xref:fundamentals/dependency-injection) zum Abrufen der `IWebHostEnvironment`-Schnittstelle, um deren Eigenschaften und Erweiterungsmethoden zu verwenden:</span><span class="sxs-lookup"><span data-stu-id="90056-367">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the `IWebHostEnvironment` in order to use its properties and extension methods:</span></span>

```csharp
public class CustomFileReader
{
    private readonly IWebHostEnvironment _env;

    public CustomFileReader(IWebHostEnvironment env)
    {
        _env = env;
    }

    public string ReadFile(string filePath)
    {
        var fileProvider = _env.WebRootFileProvider;
        // Process the file here
    }
}
```

<span data-ttu-id="90056-368">Ein [konventionsbasierter Ansatz](xref:fundamentals/environments#environment-based-startup-class-and-methods) kann verwendet werden, um die App beim Start je nach Umgebung zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="90056-368">A [convention-based approach](xref:fundamentals/environments#environment-based-startup-class-and-methods) can be used to configure the app at startup based on the environment.</span></span> <span data-ttu-id="90056-369">Fügen Sie alternativ die `IWebHostEnvironment`-Schnittstelle in den `Startup`-Konstruktor für die Verwendung in `ConfigureServices` ein:</span><span class="sxs-lookup"><span data-stu-id="90056-369">Alternatively, inject the `IWebHostEnvironment` into the `Startup` constructor for use in `ConfigureServices`:</span></span>

```csharp
public class Startup
{
    public Startup(IWebHostEnvironment env)
    {
        HostingEnvironment = env;
    }

    public IWebHostEnvironment HostingEnvironment { get; }

    public void ConfigureServices(IServiceCollection services)
    {
        if (HostingEnvironment.IsDevelopment())
        {
            // Development configuration
        }
        else
        {
            // Staging/Production configuration
        }

        var contentRootPath = HostingEnvironment.ContentRootPath;
    }
}
```

> [!NOTE]
> <span data-ttu-id="90056-370">Zusätzlich zur `IsDevelopment`-Erweiterungsmethode stellt die `IWebHostEnvironment`-Schnittstelle die `IsStaging`-, `IsProduction`- und `IsEnvironment(string environmentName)`-Methoden bereit.</span><span class="sxs-lookup"><span data-stu-id="90056-370">In addition to the `IsDevelopment` extension method, `IWebHostEnvironment` offers `IsStaging`, `IsProduction`, and `IsEnvironment(string environmentName)` methods.</span></span> <span data-ttu-id="90056-371">Weitere Informationen finden Sie unter <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="90056-371">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="90056-372">Der `IWebHostEnvironment`-Dienst kann ebenfalls direkt in die `Configure`-Methode eingefügt werden, um die Verarbeitungspipeline einzurichten:</span><span class="sxs-lookup"><span data-stu-id="90056-372">The `IWebHostEnvironment` service can also be injected directly into the `Configure` method for setting up the processing pipeline:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    if (env.IsDevelopment())
    {
        // In Development, use the Developer Exception Page
        app.UseDeveloperExceptionPage();
    }
    else
    {
        // In Staging/Production, route exceptions to /error
        app.UseExceptionHandler("/error");
    }

    var contentRootPath = env.ContentRootPath;
}
```

<span data-ttu-id="90056-373">`IWebHostEnvironment` kann in die `Invoke`-Methode eingefügt werden, wenn eine benutzerdefinierte [Middleware](xref:fundamentals/middleware/write) erstellt wird:</span><span class="sxs-lookup"><span data-stu-id="90056-373">`IWebHostEnvironment` can be injected into the `Invoke` method when creating custom [middleware](xref:fundamentals/middleware/write):</span></span>

```csharp
public async Task Invoke(HttpContext context, IWebHostEnvironment env)
{
    if (env.IsDevelopment())
    {
        // Configure middleware for Development
    }
    else
    {
        // Configure middleware for Staging/Production
    }

    var contentRootPath = env.ContentRootPath;
}
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="ihostingenvironment-interface"></a><span data-ttu-id="90056-374">IHostingEnvironment-Schnittstelle</span><span class="sxs-lookup"><span data-stu-id="90056-374">IHostingEnvironment interface</span></span>

<span data-ttu-id="90056-375">Die [IHostingEnvironment-Schnittstelle](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment) stellt Informationen über die Webhostingumgebung der App bereit.</span><span class="sxs-lookup"><span data-stu-id="90056-375">The [IHostingEnvironment interface](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment) provides information about the app's web hosting environment.</span></span> <span data-ttu-id="90056-376">Verwenden Sie [Constructor Injection](xref:fundamentals/dependency-injection) zum Abrufen der `IHostingEnvironment`-Schnittstelle, um deren Eigenschaften und Erweiterungsmethoden zu verwenden:</span><span class="sxs-lookup"><span data-stu-id="90056-376">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the `IHostingEnvironment` in order to use its properties and extension methods:</span></span>

```csharp
public class CustomFileReader
{
    private readonly IHostingEnvironment _env;

    public CustomFileReader(IHostingEnvironment env)
    {
        _env = env;
    }

    public string ReadFile(string filePath)
    {
        var fileProvider = _env.WebRootFileProvider;
        // Process the file here
    }
}
```

<span data-ttu-id="90056-377">Ein [konventionsbasierter Ansatz](xref:fundamentals/environments#environment-based-startup-class-and-methods) kann verwendet werden, um die App beim Start je nach Umgebung zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="90056-377">A [convention-based approach](xref:fundamentals/environments#environment-based-startup-class-and-methods) can be used to configure the app at startup based on the environment.</span></span> <span data-ttu-id="90056-378">Fügen Sie alternativ die `IHostingEnvironment`-Schnittstelle in den `Startup`-Konstruktor für die Verwendung in `ConfigureServices` ein:</span><span class="sxs-lookup"><span data-stu-id="90056-378">Alternatively, inject the `IHostingEnvironment` into the `Startup` constructor for use in `ConfigureServices`:</span></span>

```csharp
public class Startup
{
    public Startup(IHostingEnvironment env)
    {
        HostingEnvironment = env;
    }

    public IHostingEnvironment HostingEnvironment { get; }

    public void ConfigureServices(IServiceCollection services)
    {
        if (HostingEnvironment.IsDevelopment())
        {
            // Development configuration
        }
        else
        {
            // Staging/Production configuration
        }

        var contentRootPath = HostingEnvironment.ContentRootPath;
    }
}
```

> [!NOTE]
> <span data-ttu-id="90056-379">Zusätzlich zur `IsDevelopment`-Erweiterungsmethode stellt die `IHostingEnvironment`-Schnittstelle die `IsStaging`-, `IsProduction`- und `IsEnvironment(string environmentName)`-Methoden bereit.</span><span class="sxs-lookup"><span data-stu-id="90056-379">In addition to the `IsDevelopment` extension method, `IHostingEnvironment` offers `IsStaging`, `IsProduction`, and `IsEnvironment(string environmentName)` methods.</span></span> <span data-ttu-id="90056-380">Weitere Informationen finden Sie unter <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="90056-380">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="90056-381">Der `IHostingEnvironment`-Dienst kann ebenfalls direkt in die `Configure`-Methode eingefügt werden, um die Verarbeitungspipeline einzurichten:</span><span class="sxs-lookup"><span data-stu-id="90056-381">The `IHostingEnvironment` service can also be injected directly into the `Configure` method for setting up the processing pipeline:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        // In Development, use the Developer Exception Page
        app.UseDeveloperExceptionPage();
    }
    else
    {
        // In Staging/Production, route exceptions to /error
        app.UseExceptionHandler("/error");
    }

    var contentRootPath = env.ContentRootPath;
}
```

<span data-ttu-id="90056-382">`IHostingEnvironment` kann in die `Invoke`-Methode eingefügt werden, wenn eine benutzerdefinierte [Middleware](xref:fundamentals/middleware/write) erstellt wird:</span><span class="sxs-lookup"><span data-stu-id="90056-382">`IHostingEnvironment` can be injected into the `Invoke` method when creating custom [middleware](xref:fundamentals/middleware/write):</span></span>

```csharp
public async Task Invoke(HttpContext context, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        // Configure middleware for Development
    }
    else
    {
        // Configure middleware for Staging/Production
    }

    var contentRootPath = env.ContentRootPath;
}
```

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="ihostapplicationlifetime-interface"></a><span data-ttu-id="90056-383">IHostApplicationLifetime-Schnittstelle</span><span class="sxs-lookup"><span data-stu-id="90056-383">IHostApplicationLifetime interface</span></span>

<span data-ttu-id="90056-384">`IHostApplicationLifetime` ermöglicht Aktivitäten nach dem Starten und beim Herunterfahren.</span><span class="sxs-lookup"><span data-stu-id="90056-384">`IHostApplicationLifetime` allows for post-startup and shutdown activities.</span></span> <span data-ttu-id="90056-385">Bei drei der Eigenschaften der Schnittstelle handelt es sich um Abbruchtokens, die zum Registrieren von `Action`-Methoden verwendet werden, durch die Ereignisse beim Starten und Herunterfahren definiert werden.</span><span class="sxs-lookup"><span data-stu-id="90056-385">Three properties on the interface are cancellation tokens used to register `Action` methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="90056-386">Abbruchtoken</span><span class="sxs-lookup"><span data-stu-id="90056-386">Cancellation Token</span></span>    | <span data-ttu-id="90056-387">Wird ausgelöst, wenn&#8230;</span><span class="sxs-lookup"><span data-stu-id="90056-387">Triggered when&#8230;</span></span> |
| --------------------- | --------------------- |
| `ApplicationStarted`  | <span data-ttu-id="90056-388">Der Host vollständig gestartet wurde.</span><span class="sxs-lookup"><span data-stu-id="90056-388">The host has fully started.</span></span> |
| `ApplicationStopped`  | <span data-ttu-id="90056-389">Der Host ein ordnungsgemäßes Herunterfahren abschließt.</span><span class="sxs-lookup"><span data-stu-id="90056-389">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="90056-390">Alle Anforderungen sollten verarbeitet worden sein.</span><span class="sxs-lookup"><span data-stu-id="90056-390">All requests should be processed.</span></span> <span data-ttu-id="90056-391">Das Herunterfahren wird blockiert, bis das Ereignis abgeschlossen ist.</span><span class="sxs-lookup"><span data-stu-id="90056-391">Shutdown blocks until this event completes.</span></span> |
| `ApplicationStopping` | <span data-ttu-id="90056-392">Der Host ein ordnungsgemäßes Herunterfahren ausführt.</span><span class="sxs-lookup"><span data-stu-id="90056-392">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="90056-393">Anforderungen möglicherweise noch verarbeitet werden.</span><span class="sxs-lookup"><span data-stu-id="90056-393">Requests may still be processing.</span></span> <span data-ttu-id="90056-394">Das Herunterfahren wird blockiert, bis das Ereignis abgeschlossen ist.</span><span class="sxs-lookup"><span data-stu-id="90056-394">Shutdown blocks until this event completes.</span></span> |

```csharp
public class Startup
{
    public void Configure(IApplicationBuilder app, IHostApplicationLifetime appLifetime)
    {
        appLifetime.ApplicationStarted.Register(OnStarted);
        appLifetime.ApplicationStopping.Register(OnStopping);
        appLifetime.ApplicationStopped.Register(OnStopped);

        Console.CancelKeyPress += (sender, eventArgs) =>
        {
            appLifetime.StopApplication();
            // Don't terminate the process immediately, wait for the Main thread to exit gracefully.
            eventArgs.Cancel = true;
        };
    }

    private void OnStarted()
    {
        // Perform post-startup activities here
    }

    private void OnStopping()
    {
        // Perform on-stopping activities here
    }

    private void OnStopped()
    {
        // Perform post-stopped activities here
    }
}
```

<span data-ttu-id="90056-395">`StopApplication` fordert das Beenden der App an.</span><span class="sxs-lookup"><span data-stu-id="90056-395">`StopApplication` requests termination of the app.</span></span> <span data-ttu-id="90056-396">Die folgende Klasse verwendet `StopApplication`, um eine App ordnungsgemäß herunterzufahren, wenn die `Shutdown`-Methode der Klasse aufgerufen wird:</span><span class="sxs-lookup"><span data-stu-id="90056-396">The following class uses `StopApplication` to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

```csharp
public class MyClass
{
    private readonly IHostApplicationLifetime _appLifetime;

    public MyClass(IHostApplicationLifetime appLifetime)
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

::: moniker range="< aspnetcore-3.0"

## <a name="iapplicationlifetime-interface"></a><span data-ttu-id="90056-397">IApplicationLifetime-Schnittstelle</span><span class="sxs-lookup"><span data-stu-id="90056-397">IApplicationLifetime interface</span></span>

<span data-ttu-id="90056-398">[IApplicationLifetime](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime) ermöglicht Aktivitäten nach dem Starten und beim Herunterfahren.</span><span class="sxs-lookup"><span data-stu-id="90056-398">[IApplicationLifetime](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime) allows for post-startup and shutdown activities.</span></span> <span data-ttu-id="90056-399">Bei drei der Eigenschaften der Schnittstelle handelt es sich um Abbruchtokens, die zum Registrieren von `Action`-Methoden verwendet werden, durch die Ereignisse beim Starten und Herunterfahren definiert werden.</span><span class="sxs-lookup"><span data-stu-id="90056-399">Three properties on the interface are cancellation tokens used to register `Action` methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="90056-400">Abbruchtoken</span><span class="sxs-lookup"><span data-stu-id="90056-400">Cancellation Token</span></span>    | <span data-ttu-id="90056-401">Wird ausgelöst, wenn&#8230;</span><span class="sxs-lookup"><span data-stu-id="90056-401">Triggered when&#8230;</span></span> |
| --------------------- | --------------------- |
| [<span data-ttu-id="90056-402">ApplicationStarted</span><span class="sxs-lookup"><span data-stu-id="90056-402">ApplicationStarted</span></span>](/dotnet/api/microsoft.extensions.hosting.iapplicationlifetime.applicationstarted) | <span data-ttu-id="90056-403">Der Host vollständig gestartet wurde.</span><span class="sxs-lookup"><span data-stu-id="90056-403">The host has fully started.</span></span> |
| [<span data-ttu-id="90056-404">ApplicationStopped</span><span class="sxs-lookup"><span data-stu-id="90056-404">ApplicationStopped</span></span>](/dotnet/api/microsoft.extensions.hosting.iapplicationlifetime.applicationstopped) | <span data-ttu-id="90056-405">Der Host ein ordnungsgemäßes Herunterfahren abschließt.</span><span class="sxs-lookup"><span data-stu-id="90056-405">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="90056-406">Alle Anforderungen sollten verarbeitet worden sein.</span><span class="sxs-lookup"><span data-stu-id="90056-406">All requests should be processed.</span></span> <span data-ttu-id="90056-407">Das Herunterfahren wird blockiert, bis das Ereignis abgeschlossen ist.</span><span class="sxs-lookup"><span data-stu-id="90056-407">Shutdown blocks until this event completes.</span></span> |
| [<span data-ttu-id="90056-408">ApplicationStopping</span><span class="sxs-lookup"><span data-stu-id="90056-408">ApplicationStopping</span></span>](/dotnet/api/microsoft.extensions.hosting.iapplicationlifetime.applicationstopping) | <span data-ttu-id="90056-409">Der Host ein ordnungsgemäßes Herunterfahren ausführt.</span><span class="sxs-lookup"><span data-stu-id="90056-409">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="90056-410">Anforderungen möglicherweise noch verarbeitet werden.</span><span class="sxs-lookup"><span data-stu-id="90056-410">Requests may still be processing.</span></span> <span data-ttu-id="90056-411">Das Herunterfahren wird blockiert, bis das Ereignis abgeschlossen ist.</span><span class="sxs-lookup"><span data-stu-id="90056-411">Shutdown blocks until this event completes.</span></span> |

```csharp
public class Startup
{
    public void Configure(IApplicationBuilder app, IApplicationLifetime appLifetime)
    {
        appLifetime.ApplicationStarted.Register(OnStarted);
        appLifetime.ApplicationStopping.Register(OnStopping);
        appLifetime.ApplicationStopped.Register(OnStopped);

        Console.CancelKeyPress += (sender, eventArgs) =>
        {
            appLifetime.StopApplication();
            // Don't terminate the process immediately, wait for the Main thread to exit gracefully.
            eventArgs.Cancel = true;
        };
    }

    private void OnStarted()
    {
        // Perform post-startup activities here
    }

    private void OnStopping()
    {
        // Perform on-stopping activities here
    }

    private void OnStopped()
    {
        // Perform post-stopped activities here
    }
}
```

<span data-ttu-id="90056-412">[StopApplication](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.stopapplication) fordert das Beenden der Anwendung an.</span><span class="sxs-lookup"><span data-stu-id="90056-412">[StopApplication](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.stopapplication) requests termination of the app.</span></span> <span data-ttu-id="90056-413">Die folgende Klasse verwendet `StopApplication`, um eine App ordnungsgemäß herunterzufahren, wenn die `Shutdown`-Methode der Klasse aufgerufen wird:</span><span class="sxs-lookup"><span data-stu-id="90056-413">The following class uses `StopApplication` to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="90056-414">Bereichsvalidierung</span><span class="sxs-lookup"><span data-stu-id="90056-414">Scope validation</span></span>

<span data-ttu-id="90056-415">[CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) legt [ServiceProviderOptions.ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) auf `true` fest, wenn die Umgebung der App „Entwicklung“ ist.</span><span class="sxs-lookup"><span data-stu-id="90056-415">[CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) sets [ServiceProviderOptions.ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) to `true` if the app's environment is Development.</span></span>

<span data-ttu-id="90056-416">Wenn `ValidateScopes` auf `true` festgelegt wird, führt der Standarddienstanbieter Überprüfungen durch, um sicherzustellen, dass:</span><span class="sxs-lookup"><span data-stu-id="90056-416">When `ValidateScopes` is set to `true`, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="90056-417">Bereichsbezogene Dienste nicht direkt oder indirekt vom Stammdienstanbieter aufgelöst werden</span><span class="sxs-lookup"><span data-stu-id="90056-417">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="90056-418">Bereichsbezogene Dienste nicht direkt oder indirekt in Singletons eingefügt werden</span><span class="sxs-lookup"><span data-stu-id="90056-418">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="90056-419">Der Stammdienstanbieter wird erstellt, wenn [BuildServiceProvider](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectioncontainerbuilderextensions.buildserviceprovider) aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="90056-419">The root service provider is created when [BuildServiceProvider](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectioncontainerbuilderextensions.buildserviceprovider) is called.</span></span> <span data-ttu-id="90056-420">Die Lebensdauer des Stammdienstanbieters entspricht der Lebensdauer der App/des Servers, wenn der Anbieter mit der App erstellt wird und verworfen wird, wenn die App beendet wird.</span><span class="sxs-lookup"><span data-stu-id="90056-420">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="90056-421">Bereichsbezogene Dienste werden von dem Container verworfen, der sie erstellt hat.</span><span class="sxs-lookup"><span data-stu-id="90056-421">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="90056-422">Wenn ein bereichsbezogener Dienst im Stammcontainer erstellt wird, wird die Lebensdauer effektiv auf Singleton heraufgestuft, da er nur vom Stammcontainer verworfen wird, wenn die App/der Server heruntergefahren wird.</span><span class="sxs-lookup"><span data-stu-id="90056-422">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="90056-423">Die Überprüfung bereichsbezogener Dienste erfasst diese Situationen, wenn `BuildServiceProvider` aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="90056-423">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="90056-424">Konfigurieren Sie [ServiceProviderOptions](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions) mit [UseDefaultServiceProvider](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usedefaultserviceprovider) auf dem Hostbuilder, um Bereiche immer zu validieren, auch in der Produktionsumgebung:</span><span class="sxs-lookup"><span data-stu-id="90056-424">To always validate scopes, including in the Production environment, configure the [ServiceProviderOptions](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions) with [UseDefaultServiceProvider](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usedefaultserviceprovider) on the host builder:</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseDefaultServiceProvider((context, options) => {
        options.ValidateScopes = true;
    })
```

## <a name="additional-resources"></a><span data-ttu-id="90056-425">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="90056-425">Additional resources</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/linux-nginx>
* <xref:host-and-deploy/linux-apache>
* <xref:host-and-deploy/windows-service>
