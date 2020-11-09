---
title: Verwenden von Hostingstartassemblys in ASP.NET Core
author: rick-anderson
description: Erfahren Sie, wie Sie eine ASP.NET Core-App aus einer externen Assembly mithilfe einer Implementierung von IHostingStartup erweitern.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 09/26/2019
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
uid: fundamentals/configuration/platform-specific-configuration
ms.openlocfilehash: c12487875db69472ee328dfc7a611ee99974c770
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061053"
---
# <a name="use-hosting-startup-assemblies-in-aspnet-core"></a><span data-ttu-id="c702a-103">Verwenden von Hostingstartassemblys in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c702a-103">Use hosting startup assemblies in ASP.NET Core</span></span>

<span data-ttu-id="c702a-104">Von [Pavel Krymets](https://github.com/pakrym)</span><span class="sxs-lookup"><span data-stu-id="c702a-104">By [Pavel Krymets](https://github.com/pakrym)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c702a-105">Mit einer <xref:Microsoft.AspNetCore.Hosting.IHostingStartup>-Implementierung (Hostingstart) werden Verbesserungen an einer App beim Start von einer externen Assembly aus vorgenommen.</span><span class="sxs-lookup"><span data-stu-id="c702a-105">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> (hosting startup) implementation adds enhancements to an app at startup from an external assembly.</span></span> <span data-ttu-id="c702a-106">Eine externe Bibliothek kann beispielsweise eine Hostingstartimplementierung verwenden, um zusätzliche Konfigurationsanbieter oder -dienste für eine App bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="c702a-106">For example, an external library can use a hosting startup implementation to provide additional configuration providers or services to an app.</span></span>

<span data-ttu-id="c702a-107">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="c702a-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="hostingstartup-attribute"></a><span data-ttu-id="c702a-108">Das Attribut HostingStartup</span><span class="sxs-lookup"><span data-stu-id="c702a-108">HostingStartup attribute</span></span>

<span data-ttu-id="c702a-109">Ein [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute)-Attribut gibt an, ob eine Hostingstartassembly vorhanden ist, die zur Laufzeit aktiviert werden kann.</span><span class="sxs-lookup"><span data-stu-id="c702a-109">A [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute indicates the presence of a hosting startup assembly to activate at runtime.</span></span>

<span data-ttu-id="c702a-110">Bei der Einstiegsassembly oder bei der Assembly, die die Klasse `Startup` enthält, wird automatisch geprüft, ob sie das Attribut `HostingStartup` enthält.</span><span class="sxs-lookup"><span data-stu-id="c702a-110">The entry assembly or the assembly containing the `Startup` class is automatically scanned for the `HostingStartup` attribute.</span></span> <span data-ttu-id="c702a-111">Die Liste der Assemblys für die Suche nach `HostingStartup`-Attributen wird zur Laufzeit aus der Konfiguration in [WebHostDefaults.HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey) geladen.</span><span class="sxs-lookup"><span data-stu-id="c702a-111">The list of assemblies to search for `HostingStartup` attributes is loaded at runtime from configuration in the [WebHostDefaults.HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey).</span></span> <span data-ttu-id="c702a-112">Die Liste der Assemblys, die aus der Ermittlung ausgeschlossen werden sollen, wird aus [WebHostDefaults.HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey) geladen.</span><span class="sxs-lookup"><span data-stu-id="c702a-112">The list of assemblies to exclude from discovery is loaded from the [WebHostDefaults.HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey).</span></span>

<span data-ttu-id="c702a-113">Im folgenden Beispiel lautet der Namespace der Hostingstartassembly `StartupEnhancement`.</span><span class="sxs-lookup"><span data-stu-id="c702a-113">In the following example, the namespace of the hosting startup assembly is `StartupEnhancement`.</span></span> <span data-ttu-id="c702a-114">`StartupEnhancementHostingStartup` ist die Klasse mit dem Hostingstartcode:</span><span class="sxs-lookup"><span data-stu-id="c702a-114">The class containing the hosting startup code is `StartupEnhancementHostingStartup`:</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.cs?name=snippet1)]

<span data-ttu-id="c702a-115">Das Attribut `HostingStartup` befindet sich in der Regel in der `IHostingStartup`-Implementierungsklassendatei der Hostingstartassembly.</span><span class="sxs-lookup"><span data-stu-id="c702a-115">The `HostingStartup` attribute is typically located in the hosting startup assembly's `IHostingStartup` implementation class file.</span></span>

## <a name="discover-loaded-hosting-startup-assemblies"></a><span data-ttu-id="c702a-116">Ermitteln geladener Hostingstartassemblys</span><span class="sxs-lookup"><span data-stu-id="c702a-116">Discover loaded hosting startup assemblies</span></span>

<span data-ttu-id="c702a-117">Zum Ermitteln von geladenen Hostingstartassemblys aktivieren Sie die Protokollierung und überprüfen die Protokolle der App.</span><span class="sxs-lookup"><span data-stu-id="c702a-117">To discover loaded hosting startup assemblies, enable logging and check the app's logs.</span></span> <span data-ttu-id="c702a-118">Fehler, die beim Laden von Assemblys auftreten, werden protokolliert.</span><span class="sxs-lookup"><span data-stu-id="c702a-118">Errors that occur when loading assemblies are logged.</span></span> <span data-ttu-id="c702a-119">Geladene Hostingstartassemblys werden auf der Debugebene protokolliert. Außerdem werden alle Fehler protokolliert.</span><span class="sxs-lookup"><span data-stu-id="c702a-119">Loaded hosting startup assemblies are logged at the Debug level, and all errors are logged.</span></span>

## <a name="disable-automatic-loading-of-hosting-startup-assemblies"></a><span data-ttu-id="c702a-120">Deaktivieren des automatischen Ladens von Hostingstartassemblys</span><span class="sxs-lookup"><span data-stu-id="c702a-120">Disable automatic loading of hosting startup assemblies</span></span>

<span data-ttu-id="c702a-121">Um das automatische Laden von Hostingstartassemblys zu deaktivieren, verwenden Sie einen der folgenden Ansätze:</span><span class="sxs-lookup"><span data-stu-id="c702a-121">To disable automatic loading of hosting startup assemblies, use one of the following approaches:</span></span>

* <span data-ttu-id="c702a-122">Um zu verhindern, dass alle Hostingstartassemblys geladen werden, legen Sie eine der folgenden Einstellungen auf `true` oder `1` fest:</span><span class="sxs-lookup"><span data-stu-id="c702a-122">To prevent all hosting startup assemblies from loading, set one of the following to `true` or `1`:</span></span>

  * <span data-ttu-id="c702a-123">Hostkonfigurationseinstellung „Verhindern des Hostingstarts“:</span><span class="sxs-lookup"><span data-stu-id="c702a-123">Prevent Hosting Startup host configuration setting:</span></span>

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseSetting(
                        WebHostDefaults.PreventHostingStartupKey, "true")
                    .UseStartup<Startup>();
            });
    ```

  * <span data-ttu-id="c702a-124">Die Umgebungsvariable `ASPNETCORE_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="c702a-124">`ASPNETCORE_PREVENTHOSTINGSTARTUP` environment variable.</span></span>

* <span data-ttu-id="c702a-125">Um zu verhindern, dass bestimmte Hostingstartassemblys geladen werden, legen Sie eine der folgenden Einstellungen auf eine durch Semikolons getrennte Zeichenfolge mit Hostingstartassemblys fest, die beim Starten ausgeschlossen werden sollen:</span><span class="sxs-lookup"><span data-stu-id="c702a-125">To prevent specific hosting startup assemblies from loading, set one of the following to a semicolon-delimited string of hosting startup assemblies to exclude at startup:</span></span>

  * <span data-ttu-id="c702a-126">Hostkonfigurationseinstellung „Hostingstartausschlussassemblys“:</span><span class="sxs-lookup"><span data-stu-id="c702a-126">Hosting Startup Exclude Assemblies host configuration setting:</span></span>

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseSetting(
                        WebHostDefaults.HostingStartupExcludeAssembliesKey, 
                        "{ASSEMBLY1;ASSEMBLY2; ...}")
                    .UseStartup<Startup>();
            });
    ```

  * <span data-ttu-id="c702a-127">Die Umgebungsvariable `ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="c702a-127">`ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES` environment variable.</span></span>

<span data-ttu-id="c702a-128">Wenn sowohl die Konfigurationseinstellung für den Host als auch die Umgebungsvariable festgelegt werden, wird das Verhalten durch die Hosteinstellung gesteuert.</span><span class="sxs-lookup"><span data-stu-id="c702a-128">If both the host configuration setting and the environment variable are set, the host setting controls the behavior.</span></span>

<span data-ttu-id="c702a-129">Durch das Deaktivieren von Hostingstartassemblys mithilfe der Hosteinstellung oder der Umgebungsvariable wird die Assembly global deaktiviert. Einige Eigenschaften einer App können ebenfalls deaktiviert werden.</span><span class="sxs-lookup"><span data-stu-id="c702a-129">Disabling hosting startup assemblies using the host setting or environment variable disables the assembly globally and may disable several characteristics of an app.</span></span>

## <a name="project"></a><span data-ttu-id="c702a-130">Projekt</span><span class="sxs-lookup"><span data-stu-id="c702a-130">Project</span></span>

<span data-ttu-id="c702a-131">Erstellen Sie einen Hostingstart mit einem der folgenden Projekttypen:</span><span class="sxs-lookup"><span data-stu-id="c702a-131">Create a hosting startup with either of the following project types:</span></span>

* [<span data-ttu-id="c702a-132">Klassenbibliothek</span><span class="sxs-lookup"><span data-stu-id="c702a-132">Class library</span></span>](#class-library)
* [<span data-ttu-id="c702a-133">Konsolen-App ohne Einstiegspunkt</span><span class="sxs-lookup"><span data-stu-id="c702a-133">Console app without an entry point</span></span>](#console-app-without-an-entry-point)

### <a name="class-library"></a><span data-ttu-id="c702a-134">Klassenbibliothek</span><span class="sxs-lookup"><span data-stu-id="c702a-134">Class library</span></span>

<span data-ttu-id="c702a-135">In einer Klassenbibliothek kann eine Hostingstarterweiterung bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="c702a-135">A hosting startup enhancement can be provided in a class library.</span></span> <span data-ttu-id="c702a-136">Die Bibliothek enthält ein `HostingStartup`-Attribut.</span><span class="sxs-lookup"><span data-stu-id="c702a-136">The library contains a `HostingStartup` attribute.</span></span>

<span data-ttu-id="c702a-137">Der [Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) enthält die Razor Pages-App *HostingStartupApp* und die Klassenbibliothek *HostingStartupLibrary*.</span><span class="sxs-lookup"><span data-stu-id="c702a-137">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) includes a Razor Pages app, *HostingStartupApp* , and a class library, *HostingStartupLibrary*.</span></span> <span data-ttu-id="c702a-138">Die Klassenbibliothek:</span><span class="sxs-lookup"><span data-stu-id="c702a-138">The class library:</span></span>

* <span data-ttu-id="c702a-139">Enthält die Hostingstartklasse `ServiceKeyInjection`, die `IHostingStartup` implementiert.</span><span class="sxs-lookup"><span data-stu-id="c702a-139">Contains a hosting startup class, `ServiceKeyInjection`, which implements `IHostingStartup`.</span></span> <span data-ttu-id="c702a-140">`ServiceKeyInjection` fügt mithilfe des Anbieters der Konfiguration im Arbeitsspeicher ([AddInMemoryCollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)) der App-Konfiguration zwei Dienstzeichenfolgen hinzu.</span><span class="sxs-lookup"><span data-stu-id="c702a-140">`ServiceKeyInjection` adds a pair of service strings to the app's configuration using the in-memory configuration provider ([AddInMemoryCollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)).</span></span>
* <span data-ttu-id="c702a-141">Enthält ein `HostingStartup`-Attribut, das den Namespace und die Klasse des Hostingstarts angibt.</span><span class="sxs-lookup"><span data-stu-id="c702a-141">Includes a `HostingStartup` attribute that identifies the hosting startup's namespace and class.</span></span>

<span data-ttu-id="c702a-142">Die <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*>-Methode der `ServiceKeyInjection`-Klasse verwendet einen <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder>, um einer App Erweiterungen hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="c702a-142">The `ServiceKeyInjection` class's <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> method uses an <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> to add enhancements to an app.</span></span>

<span data-ttu-id="c702a-143">*HostingStartupLibrary/ServiceKeyInjection.cs* :</span><span class="sxs-lookup"><span data-stu-id="c702a-143">*HostingStartupLibrary/ServiceKeyInjection.cs* :</span></span>

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupLibrary/ServiceKeyInjection.cs?name=snippet1)]

<span data-ttu-id="c702a-144">Die Indexseite der App liest und rendert die Konfigurationswerte für die beiden Schlüssel, die von der Hostingstartassembly der Klassenbibliothek festgelegt werden:</span><span class="sxs-lookup"><span data-stu-id="c702a-144">The app's Index page reads and renders the configuration values for the two keys set by the class library's hosting startup assembly:</span></span>

<span data-ttu-id="c702a-145">*HostingStartupApp/Pages/Index.cshtml.cs* :</span><span class="sxs-lookup"><span data-stu-id="c702a-145">*HostingStartupApp/Pages/Index.cshtml.cs* :</span></span>

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=5-6,11-12)]

<span data-ttu-id="c702a-146">Der [Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) enthält darüber hinaus auch ein NuGet-Paketprojekt, das den separaten Hostingstart *HostingStartupPackage* bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="c702a-146">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) also includes a NuGet package project that provides a separate hosting startup, *HostingStartupPackage*.</span></span> <span data-ttu-id="c702a-147">Das Paket weist dieselben Merkmale wie die bereits beschriebene Klassenbibliothek auf.</span><span class="sxs-lookup"><span data-stu-id="c702a-147">The package has the same characteristics of the class library described earlier.</span></span> <span data-ttu-id="c702a-148">Das Paket:</span><span class="sxs-lookup"><span data-stu-id="c702a-148">The package:</span></span>

* <span data-ttu-id="c702a-149">Enthält die Hostingstartklasse `ServiceKeyInjection`, die `IHostingStartup` implementiert.</span><span class="sxs-lookup"><span data-stu-id="c702a-149">Contains a hosting startup class, `ServiceKeyInjection`, which implements `IHostingStartup`.</span></span> <span data-ttu-id="c702a-150">`ServiceKeyInjection` fügt der App-Konfiguration zwei Dienstzeichenfolgen hinzu.</span><span class="sxs-lookup"><span data-stu-id="c702a-150">`ServiceKeyInjection` adds a pair of service strings to the app's configuration.</span></span>
* <span data-ttu-id="c702a-151">Enthält ein `HostingStartup`-Attribut.</span><span class="sxs-lookup"><span data-stu-id="c702a-151">Includes a `HostingStartup` attribute.</span></span>

<span data-ttu-id="c702a-152">*HostingStartupPackage/ServiceKeyInjection.cs* :</span><span class="sxs-lookup"><span data-stu-id="c702a-152">*HostingStartupPackage/ServiceKeyInjection.cs* :</span></span>

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupPackage/ServiceKeyInjection.cs?name=snippet1)]

<span data-ttu-id="c702a-153">Die Indexseite der App liest und rendert die Konfigurationswerte für die beiden Schlüssel, die von der Hostingstartassembly der Bibliothek festgelegt werden:</span><span class="sxs-lookup"><span data-stu-id="c702a-153">The app's Index page reads and renders the configuration values for the two keys set by the package's hosting startup assembly:</span></span>

<span data-ttu-id="c702a-154">*HostingStartupApp/Pages/Index.cshtml.cs* :</span><span class="sxs-lookup"><span data-stu-id="c702a-154">*HostingStartupApp/Pages/Index.cshtml.cs* :</span></span>

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=7-8,13-14)]

### <a name="console-app-without-an-entry-point"></a><span data-ttu-id="c702a-155">Konsolen-App ohne Einstiegspunkt</span><span class="sxs-lookup"><span data-stu-id="c702a-155">Console app without an entry point</span></span>

<span data-ttu-id="c702a-156">*Dieses Verfahren ist nur für .NET Core-Apps, nicht jedoch für .NET Framework verfügbar.*</span><span class="sxs-lookup"><span data-stu-id="c702a-156">*This approach is only available for .NET Core apps, not .NET Framework.*</span></span>

<span data-ttu-id="c702a-157">Eine dynamische Hostingstarterweiterung, die zur Aktivierung keinen Kompilierungszeitverweis erfordert, kann in einer Konsolen-App ohne Einstiegspunkt bereitgestellt werden, die ein `HostingStartup`-Attribut enthält.</span><span class="sxs-lookup"><span data-stu-id="c702a-157">A dynamic hosting startup enhancement that doesn't require a compile-time reference for activation can be provided in a console app without an entry point that contains a `HostingStartup` attribute.</span></span> <span data-ttu-id="c702a-158">Durch die Veröffentlichung der Konsolen-App wird eine Hostingstartassembly erstellt, die über den Laufzeitspeicher genutzt werden kann.</span><span class="sxs-lookup"><span data-stu-id="c702a-158">Publishing the console app produces a hosting startup assembly that can be consumed from the runtime store.</span></span>

<span data-ttu-id="c702a-159">In diesem Prozess wird aus folgenden Gründen eine Konsolen-App ohne Einstiegspunkt verwendet:</span><span class="sxs-lookup"><span data-stu-id="c702a-159">A console app without an entry point is used in this process because:</span></span>

* <span data-ttu-id="c702a-160">Eine Abhängigkeitendatei ist erforderlich, um den Hostingstart in der Hostingstartassembly zu nutzen.</span><span class="sxs-lookup"><span data-stu-id="c702a-160">A dependencies file is required to consume the hosting startup in the hosting startup assembly.</span></span> <span data-ttu-id="c702a-161">Eine Abhängigkeitendatei ist eine ausführbare App-Ressource, die durch das Veröffentlichen einer App, nicht einer Bibliothek, erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="c702a-161">A dependencies file is a runnable app asset that's produced by publishing an app, not a library.</span></span>
* <span data-ttu-id="c702a-162">Eine Bibliothek kann dem [Laufzeitpaketspeicher](/dotnet/core/deploying/runtime-store), der ein ausführbares Projekt benötigt, das die freigegebene Laufzeit als Ziel verwendet, nicht direkt hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="c702a-162">A library can't be added directly to the [runtime package store](/dotnet/core/deploying/runtime-store), which requires a runnable project that targets the shared runtime.</span></span>

<span data-ttu-id="c702a-163">Bei der Erstellung eines dynamischen Hostingstarts geschieht Folgendes:</span><span class="sxs-lookup"><span data-stu-id="c702a-163">In the creation of a dynamic hosting startup:</span></span>

* <span data-ttu-id="c702a-164">Eine Hostingstartassembly wird über die Konsolen-App ohne Einstiegspunkt erstellt, für die Folgendes gilt:</span><span class="sxs-lookup"><span data-stu-id="c702a-164">A hosting startup assembly is created from the console app without an entry point that:</span></span>
  * <span data-ttu-id="c702a-165">Sie enthält eine Klasse mit der `IHostingStartup`-Implementierung.</span><span class="sxs-lookup"><span data-stu-id="c702a-165">Includes a class that contains the `IHostingStartup` implementation.</span></span>
  * <span data-ttu-id="c702a-166">Sie enthält ein [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute)-Attribut zum Identifizieren der `IHostingStartup`-Implementierungsklasse.</span><span class="sxs-lookup"><span data-stu-id="c702a-166">Includes a [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute to identify the `IHostingStartup` implementation class.</span></span>
* <span data-ttu-id="c702a-167">Die Konsolen-App wird veröffentlicht, um die Abhängigkeiten des Hostingstarts abzurufen.</span><span class="sxs-lookup"><span data-stu-id="c702a-167">The console app is published to obtain the hosting startup's dependencies.</span></span> <span data-ttu-id="c702a-168">Die Veröffentlichung der Konsolen-App hat unter anderem zur Folge, dass nicht verwendete Abhängigkeiten aus der Abhängigkeitendatei entfernt werden.</span><span class="sxs-lookup"><span data-stu-id="c702a-168">A consequence of publishing the console app is that unused dependencies are trimmed from the dependencies file.</span></span>
* <span data-ttu-id="c702a-169">Die Abhängigkeitendatei wird so geändert, dass der Laufzeitspeicherort der Hostingstartassembly festgelegt wird.</span><span class="sxs-lookup"><span data-stu-id="c702a-169">The dependencies file is modified to set the runtime location of the hosting startup assembly.</span></span>
* <span data-ttu-id="c702a-170">Die Hostingstartassembly und die dazu gehörende Abhängigkeitendatei werden im Laufzeitpaketspeicher abgelegt.</span><span class="sxs-lookup"><span data-stu-id="c702a-170">The hosting startup assembly and its dependencies file is placed into the runtime package store.</span></span> <span data-ttu-id="c702a-171">Damit die Hostingstartassembly und die entsprechende Abhängigkeitendatei erkannt werden, werden sie in einem Paar von Umgebungsvariablen aufgeführt.</span><span class="sxs-lookup"><span data-stu-id="c702a-171">To discover the hosting startup assembly and its dependencies file, they're listed in a pair of environment variables.</span></span>

<span data-ttu-id="c702a-172">Die Konsolen-App verweist auf das Paket [Microsoft.AspNetCore.Hosting.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/):</span><span class="sxs-lookup"><span data-stu-id="c702a-172">The console app references the [Microsoft.AspNetCore.Hosting.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/) package:</span></span>

[!code-xml[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.csproj)]

<span data-ttu-id="c702a-173">Ein [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute)-Attribut identifiziert eine Klasse bei der Erstellung von <xref:Microsoft.AspNetCore.Hosting.IWebHost> als eine Implementierung von `IHostingStartup` zum Laden und Ausführen.</span><span class="sxs-lookup"><span data-stu-id="c702a-173">A [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute identifies a class as an implementation of `IHostingStartup` for loading and execution when building the <xref:Microsoft.AspNetCore.Hosting.IWebHost>.</span></span> <span data-ttu-id="c702a-174">Im folgenden Beispiel ist der Namespace `StartupEnhancement` und die Klasse ist `StartupEnhancementHostingStartup`:</span><span class="sxs-lookup"><span data-stu-id="c702a-174">In the following example, the namespace is `StartupEnhancement`, and the class is `StartupEnhancementHostingStartup`:</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.cs?name=snippet1)]

<span data-ttu-id="c702a-175">Eine Klasse implementiert `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="c702a-175">A class implements `IHostingStartup`.</span></span> <span data-ttu-id="c702a-176">Die <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*>-Methode der Klasse verwendet einen <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder>, um einer App Erweiterungen hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="c702a-176">The class's <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> method uses an <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> to add enhancements to an app.</span></span> <span data-ttu-id="c702a-177">`IHostingStartup.Configure` wird in der Hoststartassembly von der Runtime vor `Startup.Configure` im Benutzercode aufgerufen. Dadurch kann Benutzercode jede Konfiguration der Hoststartassembly überschreiben.</span><span class="sxs-lookup"><span data-stu-id="c702a-177">`IHostingStartup.Configure` in the hosting startup assembly is called by the runtime before `Startup.Configure` in user code, which allows user code to overwrite any configuration provided by the hosting startup assembly.</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.cs?name=snippet2&highlight=3,5)]

<span data-ttu-id="c702a-178">Die Datei für die Abhängigkeiten ( *.deps.json* ) legt beim Erstellen eine `IHostingStartup`-Projekts den `runtime`-Speicherort der Assembly auf den *bin* -Ordner fest:</span><span class="sxs-lookup"><span data-stu-id="c702a-178">When building an `IHostingStartup` project, the dependencies file ( *.deps.json* ) sets the `runtime` location of the assembly to the *bin* folder:</span></span>

[!code-json[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement1.deps.json?range=2-13&highlight=8)]

<span data-ttu-id="c702a-179">Nur ein Teil der Datei wird angezeigt.</span><span class="sxs-lookup"><span data-stu-id="c702a-179">Only part of the file is shown.</span></span> <span data-ttu-id="c702a-180">`StartupEnhancement` ist der Name der Assembly im Beispiel.</span><span class="sxs-lookup"><span data-stu-id="c702a-180">The assembly name in the example is `StartupEnhancement`.</span></span>

## <a name="configuration-provided-by-the-hosting-startup"></a><span data-ttu-id="c702a-181">Beim Hostingstart bereitgestellte Konfiguration</span><span class="sxs-lookup"><span data-stu-id="c702a-181">Configuration provided by the hosting startup</span></span>

<span data-ttu-id="c702a-182">Es gibt zwei Ansätze zur Verarbeitung der Konfiguration, je nachdem, ob die Konfiguration des Hostingstarts oder die der App Vorrang haben soll:</span><span class="sxs-lookup"><span data-stu-id="c702a-182">There are two approaches to handling configuration depending on whether you want the hosting startup's configuration to take precedence or the app's configuration to take precedence:</span></span>

1. <span data-ttu-id="c702a-183">Bereitstellen der Konfiguration für die App mit <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> zum Laden der Konfiguration, nachdem die <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*>-Delegaten der App ausgeführt wurden.</span><span class="sxs-lookup"><span data-stu-id="c702a-183">Provide configuration to the app using <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> to load the configuration after the app's <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> delegates execute.</span></span> <span data-ttu-id="c702a-184">Bei diesem Ansatz hat die Hostingstartkonfiguration Vorrang vor der Konfiguration der App.</span><span class="sxs-lookup"><span data-stu-id="c702a-184">Hosting startup configuration takes priority over the app's configuration using this approach.</span></span>
1. <span data-ttu-id="c702a-185">Bereitstellen der Konfiguration für die App mit <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> zum Laden der Konfiguration, bevor die <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*>-Delegaten der App ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="c702a-185">Provide configuration to the app using <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> to load the configuration before the app's <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> delegates execute.</span></span> <span data-ttu-id="c702a-186">Die Konfigurationswerte der App haben bei diesem Ansatz Vorrang vor denen des Hostingstarts.</span><span class="sxs-lookup"><span data-stu-id="c702a-186">The app's configuration values take priority over those provided by the hosting startup using this approach.</span></span>

```csharp
public class ConfigurationInjection : IHostingStartup
{
    public void Configure(IWebHostBuilder builder)
    {
        Dictionary<string, string> dict;

        builder.ConfigureAppConfiguration(config =>
        {
            dict = new Dictionary<string, string>
            {
                {"ConfigurationKey1", 
                    "From IHostingStartup: Higher priority " +
                    "than the app's configuration."},
            };

            config.AddInMemoryCollection(dict);
        });

        dict = new Dictionary<string, string>
        {
            {"ConfigurationKey2", 
                "From IHostingStartup: Lower priority " +
                "than the app's configuration."},
        };

        var builtConfig = new ConfigurationBuilder()
            .AddInMemoryCollection(dict)
            .Build();

        builder.UseConfiguration(builtConfig);
    }
}
```

## <a name="specify-the-hosting-startup-assembly"></a><span data-ttu-id="c702a-187">Angeben der Hostingstartassembly</span><span class="sxs-lookup"><span data-stu-id="c702a-187">Specify the hosting startup assembly</span></span>

<span data-ttu-id="c702a-188">Geben Sie für einen von der Klassenbibliothek oder von der Konsolen-App bereitgestellten Hostingstart den Namen der Hostingstartassembly in der Umgebungsvariablen `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` an.</span><span class="sxs-lookup"><span data-stu-id="c702a-188">For either a class library- or console app-supplied hosting startup, specify the hosting startup assembly's name in the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span> <span data-ttu-id="c702a-189">Die Umgebungsvariable ist eine durch Semikolons getrennte Liste von Assemblys.</span><span class="sxs-lookup"><span data-stu-id="c702a-189">The environment variable is a semicolon-delimited list of assemblies.</span></span>

<span data-ttu-id="c702a-190">Nur in Hostingstartassemblys wird nach dem `HostingStartup`-Attribut gesucht.</span><span class="sxs-lookup"><span data-stu-id="c702a-190">Only hosting startup assemblies are scanned for the `HostingStartup` attribute.</span></span> <span data-ttu-id="c702a-191">Damit die Beispiel-App *HostingStartupApp* die weiter oben beschriebenen Hostingstarts erkennt, wird für die Umgebungsvariable der folgende Wert festgelegt:</span><span class="sxs-lookup"><span data-stu-id="c702a-191">For the sample app, *HostingStartupApp* , to discover the hosting startups described earlier, the environment variable is set to the following value:</span></span>

```
HostingStartupLibrary;HostingStartupPackage;StartupDiagnostics
```

<span data-ttu-id="c702a-192">Eine Hostingstartassembly kann auch mithilfe der Hostkonfigurationseinstellung „Hostingstartassemblys“ festgelegt werden:</span><span class="sxs-lookup"><span data-stu-id="c702a-192">A hosting startup assembly can also be set using the Hosting Startup Assemblies host configuration setting:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseSetting(
                    WebHostDefaults.HostingStartupAssembliesKey, 
                    "{ASSEMBLY1;ASSEMBLY2; ...}")
                .UseStartup<Startup>();
        });
```

<span data-ttu-id="c702a-193">Wenn mehrere Hoststartassemblys vorhanden sind, werden ihre <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*>-Methoden in der Reihenfolge der aufgelisteten Assemblys ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="c702a-193">When multiple hosting startup assembles are present, their <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> methods are executed in the order that the assemblies are listed.</span></span>

## <a name="activation"></a><span data-ttu-id="c702a-194">Aktivierung</span><span class="sxs-lookup"><span data-stu-id="c702a-194">Activation</span></span>

<span data-ttu-id="c702a-195">Optionen für die Hostingstartaktivierung:</span><span class="sxs-lookup"><span data-stu-id="c702a-195">Options for hosting startup activation are:</span></span>

* <span data-ttu-id="c702a-196">[Laufzeitspeicher:](#runtime-store) Für die Aktivierung ist kein Kompilierzeitverweis erforderlich.</span><span class="sxs-lookup"><span data-stu-id="c702a-196">[Runtime store](#runtime-store): Activation doesn't require a compile-time reference for activation.</span></span> <span data-ttu-id="c702a-197">Die Beispiel-App legt die Hostingstartassembly und die Abhängigkeitendateien im Ordner *deployment* ab, um die Bereitstellung des Hostingstarts in einer Umgebung mit mehreren Computern zu erleichtern.</span><span class="sxs-lookup"><span data-stu-id="c702a-197">The sample app places the hosting startup assembly and dependencies files into a folder, *deployment* , to facilitate deployment of the hosting startup in a multimachine environment.</span></span> <span data-ttu-id="c702a-198">Der Ordner *deployment* enthält darüber hinaus auch ein PowerShell-Skript, das im Bereitstellungssystem Umgebungsvariablen erstellt oder ändert, um den Hostingstart zu ermöglichen.</span><span class="sxs-lookup"><span data-stu-id="c702a-198">The *deployment* folder also includes a PowerShell script that creates or modifies environment variables on the deployment system to enable the hosting startup.</span></span>
* <span data-ttu-id="c702a-199">Kompilierzeitverweis für die Aktivierung erforderlich</span><span class="sxs-lookup"><span data-stu-id="c702a-199">Compile-time reference required for activation</span></span>
  * [<span data-ttu-id="c702a-200">NuGet-Paket</span><span class="sxs-lookup"><span data-stu-id="c702a-200">NuGet package</span></span>](#nuget-package)
  * [<span data-ttu-id="c702a-201">Ordner „Bin“ des Projekts</span><span class="sxs-lookup"><span data-stu-id="c702a-201">Project bin folder</span></span>](#project-bin-folder)

### <a name="runtime-store"></a><span data-ttu-id="c702a-202">Laufzeitspeicher</span><span class="sxs-lookup"><span data-stu-id="c702a-202">Runtime store</span></span>

<span data-ttu-id="c702a-203">Die Hostingstartimplementierung wird im [Laufzeitspeicher](/dotnet/core/deploying/runtime-store) abgelegt.</span><span class="sxs-lookup"><span data-stu-id="c702a-203">The hosting startup implementation is placed in the [runtime store](/dotnet/core/deploying/runtime-store).</span></span> <span data-ttu-id="c702a-204">Ein Kompilierzeitverweis auf die Assembly wird von der erweiterten App nicht benötigt.</span><span class="sxs-lookup"><span data-stu-id="c702a-204">A compile-time reference to the assembly isn't required by the enhanced app.</span></span>

<span data-ttu-id="c702a-205">Nach der Erstellung des Hostingstarts wird mithilfe der Manifestprojektdatei und des Befehls [dotnet store](/dotnet/core/tools/dotnet-store) ein Laufzeitspeicher generiert.</span><span class="sxs-lookup"><span data-stu-id="c702a-205">After the hosting startup is built, a runtime store is generated using the manifest project file and the [dotnet store](/dotnet/core/tools/dotnet-store) command.</span></span>

```dotnetcli
dotnet store --manifest {MANIFEST FILE} --runtime {RUNTIME IDENTIFIER} --output {OUTPUT LOCATION} --skip-optimization
```

<span data-ttu-id="c702a-206">In der Beispiel-App (Projekt *RuntimeStore* ) wird der folgende Befehl verwendet:</span><span class="sxs-lookup"><span data-stu-id="c702a-206">In the sample app ( *RuntimeStore* project) the following command is used:</span></span>

```dotnetcli
dotnet store --manifest store.manifest.csproj --runtime win7-x64 --output ./deployment/store --skip-optimization
```

<span data-ttu-id="c702a-207">Damit die Runtime den Laufzeitspeicher ermitteln kann, wird der Speicherort des Laufzeitspeichers der Umgebungsvariablen `DOTNET_SHARED_STORE` hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="c702a-207">For the runtime to discover the runtime store, the runtime store's location is added to the `DOTNET_SHARED_STORE` environment variable.</span></span>

<span data-ttu-id="c702a-208">**Ändern und Ablegen der Abhängigkeitendatei des Hostingstarts**</span><span class="sxs-lookup"><span data-stu-id="c702a-208">**Modify and place the hosting startup's dependencies file**</span></span>

<span data-ttu-id="c702a-209">Um die Erweiterung ohne einen Paketverweis auf die Erweiterung zu aktivieren, geben Sie mit `additionalDeps` zusätzliche Abhängigkeiten zur Laufzeit an.</span><span class="sxs-lookup"><span data-stu-id="c702a-209">To activate the enhancement without a package reference to the enhancement, specify additional dependencies to the runtime with `additionalDeps`.</span></span> <span data-ttu-id="c702a-210">`additionalDeps` ermöglicht Ihnen Folgendes:</span><span class="sxs-lookup"><span data-stu-id="c702a-210">`additionalDeps` allows you to:</span></span>

* <span data-ttu-id="c702a-211">Erweitern des App-Bibliotheksdiagramms durch Bereitstellen einer Reihe zusätzlicher *.deps.json* -Dateien, die beim Start mit der App-eigenen *.deps.json* -Datei zusammengeführt werden.</span><span class="sxs-lookup"><span data-stu-id="c702a-211">Extend the app's library graph by providing a set of additional *.deps.json* files to merge with the app's own *.deps.json* file on startup.</span></span>
* <span data-ttu-id="c702a-212">Bereitstellen der Hostingstartassembly, sodass sie ermittelt und geladen werden kann</span><span class="sxs-lookup"><span data-stu-id="c702a-212">Make the hosting startup assembly discoverable and loadable.</span></span>

<span data-ttu-id="c702a-213">Folgender Ansatz wird zum Generieren der zusätzlichen Abhängigkeitendatei empfohlen:</span><span class="sxs-lookup"><span data-stu-id="c702a-213">The recommended approach for generating the additional dependencies file is to:</span></span>

 1. <span data-ttu-id="c702a-214">Führen Sie `dotnet publish` für die Manifestdatei des Laufzeitspeichers aus, auf die im vorherigen Abschnitt verwiesen wurde.</span><span class="sxs-lookup"><span data-stu-id="c702a-214">Execute `dotnet publish` on the runtime store manifest file referenced in the previous section.</span></span>
 1. <span data-ttu-id="c702a-215">Entfernen Sie den Manifestverweis aus den Bibliotheken und dem Abschnitt `runtime` der daraus resultierenden *.deps.json* -Datei.</span><span class="sxs-lookup"><span data-stu-id="c702a-215">Remove the manifest reference from libraries and the `runtime` section of the resulting *.deps.json* file.</span></span>

<span data-ttu-id="c702a-216">Im Beispielprojekt wird die Eigenschaft `store.manifest/1.0.0` aus den Abschnitten `targets` und `libraries` entfernt:</span><span class="sxs-lookup"><span data-stu-id="c702a-216">In the example project, the `store.manifest/1.0.0` property is removed from the `targets` and `libraries` section:</span></span>

```json
{
  "runtimeTarget": {
    "name": ".NETCoreApp,Version=v3.0",
    "signature": ""
  },
  "compilationOptions": {},
  "targets": {
    ".NETCoreApp,Version=v3.0": {
      "store.manifest/1.0.0": {
        "dependencies": {
          "StartupDiagnostics": "1.0.0"
        },
        "runtime": {
          "store.manifest.dll": {}
        }
      },
      "StartupDiagnostics/1.0.0": {
        "runtime": {
          "lib/netcoreapp3.0/StartupDiagnostics.dll": {
            "assemblyVersion": "1.0.0.0",
            "fileVersion": "1.0.0.0"
          }
        }
      }
    }
  },
  "libraries": {
    "store.manifest/1.0.0": {
      "type": "project",
      "serviceable": false,
      "sha512": ""
    },
    "StartupDiagnostics/1.0.0": {
      "type": "package",
      "serviceable": true,
      "sha512": "sha512-xrhzuNSyM5/f4ZswhooJ9dmIYLP64wMnqUJSyTKVDKDVj5T+qtzypl8JmM/aFJLLpYrf0FYpVWvGujd7/FfMEw==",
      "path": "startupdiagnostics/1.0.0",
      "hashPath": "startupdiagnostics.1.0.0.nupkg.sha512"
    }
  }
}
```

<span data-ttu-id="c702a-217">Legen Sie die *.deps.json* -Datei an folgendem Speicherort ab:</span><span class="sxs-lookup"><span data-stu-id="c702a-217">Place the *.deps.json* file into the following location:</span></span>

```
{ADDITIONAL DEPENDENCIES PATH}/shared/{SHARED FRAMEWORK NAME}/{SHARED FRAMEWORK VERSION}/{ENHANCEMENT ASSEMBLY NAME}.deps.json
```

* <span data-ttu-id="c702a-218">`{ADDITIONAL DEPENDENCIES PATH}`: Der Speicherort wurde zur Umgebungsvariable `DOTNET_ADDITIONAL_DEPS` hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="c702a-218">`{ADDITIONAL DEPENDENCIES PATH}`: Location added to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>
* <span data-ttu-id="c702a-219">`{SHARED FRAMEWORK NAME}`: Dies ist das freigegebene Framework, das für diese zusätzliche Abhängigkeitsdatei erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="c702a-219">`{SHARED FRAMEWORK NAME}`: Shared framework required for this additional dependencies file.</span></span>
* <span data-ttu-id="c702a-220">`{SHARED FRAMEWORK VERSION}`: Dies ist die Mindestversion des freigegebenen Frameworks.</span><span class="sxs-lookup"><span data-stu-id="c702a-220">`{SHARED FRAMEWORK VERSION}`: Minimum shared framework version.</span></span>
* <span data-ttu-id="c702a-221">`{ENHANCEMENT ASSEMBLY NAME}`: Dies ist der Name der Erweiterungsassembly.</span><span class="sxs-lookup"><span data-stu-id="c702a-221">`{ENHANCEMENT ASSEMBLY NAME}`: The enhancement's assembly name.</span></span>

<span data-ttu-id="c702a-222">In der Beispiel-App (Projekt *RuntimeStore* ) wird die zusätzliche Abhängigkeitendatei an folgendem Speicherort abgelegt:</span><span class="sxs-lookup"><span data-stu-id="c702a-222">In the sample app ( *RuntimeStore* project), the additional dependencies file is placed into the following location:</span></span>

```
deployment/additionalDeps/shared/Microsoft.AspNetCore.App/3.0.0/StartupDiagnostics.deps.json
```

<span data-ttu-id="c702a-223">Damit die Runtime den Speicherort des Laufzeitspeichers ermitteln kann, wir die zusätzliche Abhängigkeitendatei der Umgebungsvariablen `DOTNET_ADDITIONAL_DEPS` hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="c702a-223">For runtime to discover the runtime store location, the additional dependencies file location is added to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>

<span data-ttu-id="c702a-224">In der Beispiel-App (Projekt *RuntimeStore* ) wird das Erstellen des Laufzeitspeichers und das Generieren der zusätzlichen Abhängigkeitendatei durch ein [PowerShell](/powershell/scripting/powershell-scripting)-Skript durchgeführt.</span><span class="sxs-lookup"><span data-stu-id="c702a-224">In the sample app ( *RuntimeStore* project), building the runtime store and generating the additional dependencies file is accomplished using a [PowerShell](/powershell/scripting/powershell-scripting) script.</span></span>

<span data-ttu-id="c702a-225">Beispiele zum Festlegen von Umgebungsvariablen für verschiedene Betriebssysteme finden Sie unter [Use multiple environments (Verwenden mehrerer Umgebungen)](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="c702a-225">For examples of how to set environment variables for various operating systems, see [Use multiple environments](xref:fundamentals/environments).</span></span>

<span data-ttu-id="c702a-226">**Bereitstellung**</span><span class="sxs-lookup"><span data-stu-id="c702a-226">**Deployment**</span></span>

<span data-ttu-id="c702a-227">Um die Bereitstellung eines Hostingstarts in einer Umgebung mit mehreren Computern zu erleichtern, erstellt die Beispiel-App in der veröffentlichten Ausgabe einen *deployment* -Ordner, der Folgendes enthält:</span><span class="sxs-lookup"><span data-stu-id="c702a-227">To facilitate the deployment of a hosting startup in a multimachine environment, the sample app creates a *deployment* folder in published output that contains:</span></span>

* <span data-ttu-id="c702a-228">Der Laufzeitspeicher des Hostingstarts.</span><span class="sxs-lookup"><span data-stu-id="c702a-228">The hosting startup runtime store.</span></span>
* <span data-ttu-id="c702a-229">Die Abhängigkeitendatei des Hostingstarts.</span><span class="sxs-lookup"><span data-stu-id="c702a-229">The hosting startup dependencies file.</span></span>
* <span data-ttu-id="c702a-230">Ein PowerShell-Skript, das `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`, `DOTNET_SHARED_STORE` und `DOTNET_ADDITIONAL_DEPS` erstellt oder ändert, um die Aktivierung des Hostingstarts zu unterstützen.</span><span class="sxs-lookup"><span data-stu-id="c702a-230">A PowerShell script that creates or modifies the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`, `DOTNET_SHARED_STORE`, and `DOTNET_ADDITIONAL_DEPS` to support the activation of the hosting startup.</span></span> <span data-ttu-id="c702a-231">Führen Sie das Skript über eine PowerShell-Administratoreingabeaufforderung auf dem Bereitstellungssystem aus.</span><span class="sxs-lookup"><span data-stu-id="c702a-231">Run the script from an administrative PowerShell command prompt on the deployment system.</span></span>

### <a name="nuget-package"></a><span data-ttu-id="c702a-232">NuGet-Paket</span><span class="sxs-lookup"><span data-stu-id="c702a-232">NuGet package</span></span>

<span data-ttu-id="c702a-233">In einem NuGet-Paket kann eine Hostingstarterweiterung bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="c702a-233">A hosting startup enhancement can be provided in a NuGet package.</span></span> <span data-ttu-id="c702a-234">Das Paket enthält ein `HostingStartup`-Attribut.</span><span class="sxs-lookup"><span data-stu-id="c702a-234">The package has a `HostingStartup` attribute.</span></span> <span data-ttu-id="c702a-235">Die vom Paket bereitgestellten Hostingstarttypen werden für die App mit einem der folgenden Verfahren verfügbar gemacht:</span><span class="sxs-lookup"><span data-stu-id="c702a-235">The hosting startup types provided by the package are made available to the app using either of the following approaches:</span></span>

* <span data-ttu-id="c702a-236">Die Projektdatei der erweiterten App erstellt einen Paketverweis für den Hostingstart in der Projektdatei der App (Kompilierzeitverweis).</span><span class="sxs-lookup"><span data-stu-id="c702a-236">The enhanced app's project file makes a package reference for the hosting startup in the app's project file (a compile-time reference).</span></span> <span data-ttu-id="c702a-237">Wenn ein Kompilierzeitverweis eingerichtet ist, werden die Hostingstartassembly und alle dazu gehörenden Abhängigkeiten in die Abhängigkeitendatei der App ( *.deps.json* ) eingebunden.</span><span class="sxs-lookup"><span data-stu-id="c702a-237">With the compile-time reference in place, the hosting startup assembly and all of its dependencies are incorporated into the app's dependency file ( *.deps.json* ).</span></span> <span data-ttu-id="c702a-238">Dieses Verfahren gilt für ein in [nuget.org](https://www.nuget.org/) veröffentlichtes Hostingstartassembly-Paket.</span><span class="sxs-lookup"><span data-stu-id="c702a-238">This approach applies to a hosting startup assembly package published to [nuget.org](https://www.nuget.org/).</span></span>
* <span data-ttu-id="c702a-239">Die Abhängigkeitendatei des Hostingstarts wird für die erweiterte App wie im Abschnitt [Laufzeitspeicher](#runtime-store) beschrieben (ohne Kompilierzeitverweis) verfügbar gemacht.</span><span class="sxs-lookup"><span data-stu-id="c702a-239">The hosting startup's dependencies file is made available to the enhanced app as described in the [Runtime store](#runtime-store) section (without a compile-time reference).</span></span>

<span data-ttu-id="c702a-240">Weitere Informationen zu NuGet-Paketen und den Laufzeitspeicher finden Sie in den folgenden Themen:</span><span class="sxs-lookup"><span data-stu-id="c702a-240">For more information on NuGet packages and the runtime store, see the following topics:</span></span>

* [<span data-ttu-id="c702a-241">So erstellen Sie ein NuGet-Paket mit plattformübergreifenden Tools</span><span class="sxs-lookup"><span data-stu-id="c702a-241">How to Create a NuGet Package with Cross Platform Tools</span></span>](/dotnet/core/deploying/creating-nuget-packages)
* [<span data-ttu-id="c702a-242">Veröffentlichen von Paketen</span><span class="sxs-lookup"><span data-stu-id="c702a-242">Publishing packages</span></span>](/nuget/create-packages/publish-a-package)
* [<span data-ttu-id="c702a-243">Laufzeitpaketspeicher</span><span class="sxs-lookup"><span data-stu-id="c702a-243">Runtime package store</span></span>](/dotnet/core/deploying/runtime-store)

### <a name="project-bin-folder"></a><span data-ttu-id="c702a-244">Ordner „Bin“ des Projekts</span><span class="sxs-lookup"><span data-stu-id="c702a-244">Project bin folder</span></span>

<span data-ttu-id="c702a-245">Eine Hostingstarterweiterung kann durch eine mittels *bin* bereitgestellte Assembly in der erweiterten App bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="c702a-245">A hosting startup enhancement can be provided by a *bin* -deployed assembly in the enhanced app.</span></span> <span data-ttu-id="c702a-246">Die von der Assembly bereitgestellten Hostingstarttypen werden für die App mit einem der folgenden Verfahren verfügbar gemacht:</span><span class="sxs-lookup"><span data-stu-id="c702a-246">The hosting startup types provided by the assembly are made available to the app using one of the following approaches:</span></span>

* <span data-ttu-id="c702a-247">Die Projektdatei der erweiterten App enthält einen Assemblyverweis auf den Hostingstart (Kompilierzeitverweis).</span><span class="sxs-lookup"><span data-stu-id="c702a-247">The enhanced app's project file makes an assembly reference to the hosting startup (a compile-time reference).</span></span> <span data-ttu-id="c702a-248">Wenn ein Kompilierzeitverweis eingerichtet ist, werden die Hostingstartassembly und alle dazu gehörenden Abhängigkeiten in die Abhängigkeitendatei der App ( *.deps.json* ) eingebunden.</span><span class="sxs-lookup"><span data-stu-id="c702a-248">With the compile-time reference in place, the hosting startup assembly and all of its dependencies are incorporated into the app's dependency file ( *.deps.json* ).</span></span> <span data-ttu-id="c702a-249">Dieser Ansatz gilt, wenn das Bereitstellungsszenario erfordert, dass ein Kompilierzeitverweis auf die Assembly des Hostingstarts ( *DLL* -Datei) erstellt und die Assembly in eine der beiden Dateien verschoben wird:</span><span class="sxs-lookup"><span data-stu-id="c702a-249">This approach applies when the deployment scenario calls for making a compile-time reference to the hosting startup's assembly ( *.dll* file) and moving the assembly to either:</span></span>
  * <span data-ttu-id="c702a-250">Das verbrauchende Projekt.</span><span class="sxs-lookup"><span data-stu-id="c702a-250">The consuming project.</span></span>
  * <span data-ttu-id="c702a-251">Einen Speicherort, auf den das verbrauchende Projekt zugreifen kann.</span><span class="sxs-lookup"><span data-stu-id="c702a-251">A location accessible by the consuming project.</span></span>
* <span data-ttu-id="c702a-252">Die Abhängigkeitendatei des Hostingstarts wird für die erweiterte App wie im Abschnitt [Laufzeitspeicher](#runtime-store) beschrieben (ohne Kompilierzeitverweis) verfügbar gemacht.</span><span class="sxs-lookup"><span data-stu-id="c702a-252">The hosting startup's dependencies file is made available to the enhanced app as described in the [Runtime store](#runtime-store) section (without a compile-time reference).</span></span>
* <span data-ttu-id="c702a-253">Wenn das Ziel .NET Framework ist, kann die Assembly in den Standardladekontext geladen werden, was bei .NET Framework bedeutet, dass sich die Assembly an einem der folgenden Speicherorte befindet:</span><span class="sxs-lookup"><span data-stu-id="c702a-253">When targeting the .NET Framework, the assembly is loadable in the default load context, which on .NET Framework means that the assembly is located at either of the following locations:</span></span>
  * <span data-ttu-id="c702a-254">Anwendungsbasispfad: Dabei handelt es sich um den Ordner *bin* , in dem die ausführbare Datei ( *.exe* ) der App gespeichert wird.</span><span class="sxs-lookup"><span data-stu-id="c702a-254">Application base path: The *bin* folder where the app's executable ( *.exe* ) is located.</span></span>
  * <span data-ttu-id="c702a-255">Globaler Assemblycache (GAC): Der GAC speichert Assemblys, die mehrere .NET Framework-Apps gemeinsam nutzen.</span><span class="sxs-lookup"><span data-stu-id="c702a-255">Global Assembly Cache (GAC): The GAC stores assemblies that several .NET Framework apps share.</span></span> <span data-ttu-id="c702a-256">Weitere Informationen finden Sie unter [Vorgehensweise: Installieren einer Assembly in den globalen Assemblycache](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac) in der .NET Framework-Dokumentation.</span><span class="sxs-lookup"><span data-stu-id="c702a-256">For more information, see [How to: Install an assembly into the global assembly cache](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac) in the .NET Framework documentation.</span></span>

## <a name="sample-code"></a><span data-ttu-id="c702a-257">Beispielcode</span><span class="sxs-lookup"><span data-stu-id="c702a-257">Sample code</span></span>

<span data-ttu-id="c702a-258">Der [Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([Herunterladen](xref:index#how-to-download-a-sample)) zeigt Szenarios für die Hostingstartimplementierung:</span><span class="sxs-lookup"><span data-stu-id="c702a-258">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([how to download](xref:index#how-to-download-a-sample)) demonstrates hosting startup implementation scenarios:</span></span>

* <span data-ttu-id="c702a-259">Zwei Hostingstartassemblys (Klassenbibliotheken) legen zwei Schlüssel-Wert-Paare der Konfiguration im Arbeitsspeicher fest:</span><span class="sxs-lookup"><span data-stu-id="c702a-259">Two hosting startup assemblies (class libraries) set a pair of in-memory configuration key-value pairs each:</span></span>
  * <span data-ttu-id="c702a-260">NuGet-Paket ( *HostingStartupPackage* )</span><span class="sxs-lookup"><span data-stu-id="c702a-260">NuGet package ( *HostingStartupPackage* )</span></span>
  * <span data-ttu-id="c702a-261">Klassenbibliothek ( *HostingStartupLibrary* )</span><span class="sxs-lookup"><span data-stu-id="c702a-261">Class library ( *HostingStartupLibrary* )</span></span>
* <span data-ttu-id="c702a-262">Ein Hostingstart wird über eine im Laufzeitspeicher bereitgestellte Assembly ( *StartupDiagnostics* ) aktiviert.</span><span class="sxs-lookup"><span data-stu-id="c702a-262">A hosting startup is activated from a runtime store-deployed assembly ( *StartupDiagnostics* ).</span></span> <span data-ttu-id="c702a-263">Die Assembly fügt der App zum Start zwei Middlewares hinzu, die Diagnoseinformationen zu folgenden Komponenten bereitstellen:</span><span class="sxs-lookup"><span data-stu-id="c702a-263">The assembly adds two middlewares to the app at startup that provide diagnostic information on:</span></span>
  * <span data-ttu-id="c702a-264">Registrierte Dienste</span><span class="sxs-lookup"><span data-stu-id="c702a-264">Registered services</span></span>
  * <span data-ttu-id="c702a-265">Adresse (Schema, Host, Basispfad, Pfad, Abfragezeichenfolge)</span><span class="sxs-lookup"><span data-stu-id="c702a-265">Address (scheme, host, path base, path, query string)</span></span>
  * <span data-ttu-id="c702a-266">Verbindung (Remote-IP, Remoteport, lokale IP, lokaler Port, Clientzertifikat)</span><span class="sxs-lookup"><span data-stu-id="c702a-266">Connection (remote IP, remote port, local IP, local port, client certificate)</span></span>
  * <span data-ttu-id="c702a-267">Anforderungsheader</span><span class="sxs-lookup"><span data-stu-id="c702a-267">Request headers</span></span>
  * <span data-ttu-id="c702a-268">Umgebungsvariablen</span><span class="sxs-lookup"><span data-stu-id="c702a-268">Environment variables</span></span>

<span data-ttu-id="c702a-269">So führen Sie das Beispiel aus:</span><span class="sxs-lookup"><span data-stu-id="c702a-269">To run the sample:</span></span>

<span data-ttu-id="c702a-270">**Aktivierung über ein NuGet-Paket**</span><span class="sxs-lookup"><span data-stu-id="c702a-270">**Activation from a NuGet package**</span></span>

1. <span data-ttu-id="c702a-271">Kompilieren Sie das Paket *HostingStartupPackage* mit dem Befehl [dotnet pack](/dotnet/core/tools/dotnet-pack).</span><span class="sxs-lookup"><span data-stu-id="c702a-271">Compile the *HostingStartupPackage* package with the [dotnet pack](/dotnet/core/tools/dotnet-pack) command.</span></span>
1. <span data-ttu-id="c702a-272">Fügen Sie den Assemblynamen des Pakets *HostingStartupPackage* zur Umgebungsvariablen `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` hinzu.</span><span class="sxs-lookup"><span data-stu-id="c702a-272">Add the package's assembly name of the *HostingStartupPackage* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
1. <span data-ttu-id="c702a-273">Kompilieren Sie die App, und führen Sie diese aus.</span><span class="sxs-lookup"><span data-stu-id="c702a-273">Compile and run the app.</span></span> <span data-ttu-id="c702a-274">In der erweiterten App ist ein Paketverweis vorhanden (Kompilierzeitverweis).</span><span class="sxs-lookup"><span data-stu-id="c702a-274">A package reference is present in the enhanced app (a compile-time reference).</span></span> <span data-ttu-id="c702a-275">Eine `<PropertyGroup>` in der Projektdatei der App gibt die Ausgabe des Paketprojekts ( *../HostingStartupPackage/bin/Debug* ) als Paketquelle an.</span><span class="sxs-lookup"><span data-stu-id="c702a-275">A `<PropertyGroup>` in the app's project file specifies the package project's output ( *../HostingStartupPackage/bin/Debug* ) as a package source.</span></span> <span data-ttu-id="c702a-276">Dadurch kann die Anwendung das Paket verwenden, ohne das Paket in [nuget.org](https://www.nuget.org/) hochzuladen. Weitere Informationen finden Sie in den Hinweisen in der Projektdatei von HostingStartupApp.</span><span class="sxs-lookup"><span data-stu-id="c702a-276">This allows the app to use the package without uploading the package to [nuget.org](https://www.nuget.org/). For more information, see the notes in the HostingStartupApp's project file.</span></span>

   ```xml
   <PropertyGroup>
     <RestoreSources>$(RestoreSources);https://api.nuget.org/v3/index.json;../HostingStartupPackage/bin/Debug</RestoreSources>
   </PropertyGroup>
   ```

1. <span data-ttu-id="c702a-277">Die von der Indexseite gerenderten Schlüsselwerte der Dienstkonfiguration entsprechen den durch die `ServiceKeyInjection.Configure`-Methode des Pakets festgelegten Werten.</span><span class="sxs-lookup"><span data-stu-id="c702a-277">Observe that the service configuration key values rendered by the Index page match the values set by the package's `ServiceKeyInjection.Configure` method.</span></span>

<span data-ttu-id="c702a-278">Wenn Sie am Projekt *HostingStartupPackage* Änderungen vornehmen und das Projekt erneut kompilieren, bereinigen Sie die lokalen NuGet-Paketcaches, um sicherzustellen, dass die *HostingStartupApp* das aktualisierte Paket erhält und kein veraltetes Paket aus dem lokalen Cache.</span><span class="sxs-lookup"><span data-stu-id="c702a-278">If you make changes to the *HostingStartupPackage* project and recompile it, clear the local NuGet package caches to ensure that the *HostingStartupApp* receives the updated package and not a stale package from the local cache.</span></span> <span data-ttu-id="c702a-279">Um die lokalen NuGet-Caches zu bereinigen, führen Sie den folgenden [dotnet nuget locals](/dotnet/core/tools/dotnet-nuget-locals)-Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="c702a-279">To clear the local NuGet caches, execute the following [dotnet nuget locals](/dotnet/core/tools/dotnet-nuget-locals) command:</span></span>

```dotnetcli
dotnet nuget locals all --clear
```

<span data-ttu-id="c702a-280">**Aktivierung über eine Klassenbibliothek**</span><span class="sxs-lookup"><span data-stu-id="c702a-280">**Activation from a class library**</span></span>

1. <span data-ttu-id="c702a-281">Kompilieren Sie die Klassenbibliothek *HostingStartupLibrary* mit dem Befehl [dotnet build](/dotnet/core/tools/dotnet-build).</span><span class="sxs-lookup"><span data-stu-id="c702a-281">Compile the *HostingStartupLibrary* class library with the [dotnet build](/dotnet/core/tools/dotnet-build) command.</span></span>
1. <span data-ttu-id="c702a-282">Fügen Sie der Umgebungsvariablen `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` den Assemblynamen *HostingStartupLibrary* der Klassenbibliothek hinzu.</span><span class="sxs-lookup"><span data-stu-id="c702a-282">Add the class library's assembly name of *HostingStartupLibrary* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
1. <span data-ttu-id="c702a-283">Stellen Sie die Assembly der Klassenbibliothek mittels *bin* für die App bereit, indem Sie die Datei *HostingStartupLibrary.dll* aus der kompilierten Ausgabe der Klassenbibliothek in den Ordner *bin/Debug* der App kopieren.</span><span class="sxs-lookup"><span data-stu-id="c702a-283">*bin* -deploy the class library's assembly to the app by copying the *HostingStartupLibrary.dll* file from the class library's compiled output to the app's *bin/Debug* folder.</span></span>
1. <span data-ttu-id="c702a-284">Kompilieren Sie die App, und führen Sie diese aus.</span><span class="sxs-lookup"><span data-stu-id="c702a-284">Compile and run the app.</span></span> <span data-ttu-id="c702a-285">Eine `<ItemGroup>` in der Projektdatei der App verweist auf die Assembly der Klassenbibliothek ( *.\bin\Debug\netcoreapp3.0\HostingStartupLibrary.dll* ) – Kompilierzeitverweis.</span><span class="sxs-lookup"><span data-stu-id="c702a-285">An `<ItemGroup>` in the app's project file references the class library's assembly ( *.\bin\Debug\netcoreapp3.0\HostingStartupLibrary.dll* ) (a compile-time reference).</span></span> <span data-ttu-id="c702a-286">Weitere Informationen finden Sie in den Hinweisen in der Projektdatei von HostingStartupApp.</span><span class="sxs-lookup"><span data-stu-id="c702a-286">For more information, see the notes in the HostingStartupApp's project file.</span></span>

   ```xml
   <ItemGroup>
     <Reference Include=".\\bin\\Debug\\netcoreapp3.0\\HostingStartupLibrary.dll">
       <HintPath>.\bin\Debug\netcoreapp3.0\HostingStartupLibrary.dll</HintPath>
       <SpecificVersion>False</SpecificVersion> 
     </Reference>
   </ItemGroup>
   ```

1. <span data-ttu-id="c702a-287">Die von der Indexseite gerenderten Schlüsselwerte der Dienstkonfiguration entsprechen den durch die `ServiceKeyInjection.Configure`-Methode der Klassenbibliothek festgelegten Werten.</span><span class="sxs-lookup"><span data-stu-id="c702a-287">Observe that the service configuration key values rendered by the Index page match the values set by the class library's `ServiceKeyInjection.Configure` method.</span></span>

<span data-ttu-id="c702a-288">**Aktivierung über eine mittels Laufzeitspeicher bereitgestellten Assembly**</span><span class="sxs-lookup"><span data-stu-id="c702a-288">**Activation from a runtime store-deployed assembly**</span></span>

1. <span data-ttu-id="c702a-289">Im Projekt *StartupDiagnostics* wird [PowerShell](/powershell/scripting/powershell-scripting) verwendet, um die Datei *StartupDiagnostics.deps.json* zu bearbeiten.</span><span class="sxs-lookup"><span data-stu-id="c702a-289">The *StartupDiagnostics* project uses [PowerShell](/powershell/scripting/powershell-scripting) to modify its *StartupDiagnostics.deps.json* file.</span></span> <span data-ttu-id="c702a-290">PowerShell ist standardmäßig auf Windows-Betriebssystemen ab Windows 7 SP1 und Windows Server 2008 R2 SP1 installiert.</span><span class="sxs-lookup"><span data-stu-id="c702a-290">PowerShell is installed by default on Windows starting with Windows 7 SP1 and Windows Server 2008 R2 SP1.</span></span> <span data-ttu-id="c702a-291">Im Artikel [Installieren verschiedener Versionen von PowerShell](/powershell/scripting/install/installing-powershell) erfahren Sie, wie Sie PowerShell auf anderen Plattformen erwerben können.</span><span class="sxs-lookup"><span data-stu-id="c702a-291">To obtain PowerShell on other platforms, see [Installing various versions of PowerShell](/powershell/scripting/install/installing-powershell).</span></span>
1. <span data-ttu-id="c702a-292">Führen Sie das Skript *build.ps1* im Ordner *RuntimeStore* aus.</span><span class="sxs-lookup"><span data-stu-id="c702a-292">Execute the *build.ps1* script in the *RuntimeStore* folder.</span></span> <span data-ttu-id="c702a-293">Das Skript:</span><span class="sxs-lookup"><span data-stu-id="c702a-293">The script:</span></span>
   * <span data-ttu-id="c702a-294">Generiert das `StartupDiagnostics`-Paket im Ordner *obj\packages* .</span><span class="sxs-lookup"><span data-stu-id="c702a-294">Generates the `StartupDiagnostics` package in the *obj\packages* folder.</span></span>
   * <span data-ttu-id="c702a-295">Generiert den Laufzeitspeicher für `StartupDiagnostics` im Ordner *store*.</span><span class="sxs-lookup"><span data-stu-id="c702a-295">Generates the runtime store for `StartupDiagnostics` in the *store* folder.</span></span> <span data-ttu-id="c702a-296">Der `dotnet store`-Befehl im Skript verwendet den [Laufzeitbezeichner (RID)](/dotnet/core/rid-catalog) `win7-x64` für einen für Windows bereitgestellten Hostingstart.</span><span class="sxs-lookup"><span data-stu-id="c702a-296">The `dotnet store` command in the script uses the `win7-x64` [runtime identifier (RID)](/dotnet/core/rid-catalog) for a hosting startup deployed to Windows.</span></span> <span data-ttu-id="c702a-297">Wenn der Hostingstart für eine andere Laufzeit bereitgestellt wird, muss in Zeile 37 die entsprechende RID eingegeben werden.</span><span class="sxs-lookup"><span data-stu-id="c702a-297">When providing the hosting startup for a different runtime, substitute the correct RID on line 37 of the script.</span></span> <span data-ttu-id="c702a-298">Der Runtimespeicher für `StartupDiagnostics` würde später in den Runtimespeicher des Benutzers oder Systems auf dem Computer verschoben, auf dem die Assembly verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="c702a-298">The runtime store for `StartupDiagnostics` would later be moved to the user's or system's runtime store on the machine where the assembly will be consumed.</span></span> <span data-ttu-id="c702a-299">Der Installationsspeicherort für den Benutzerruntimespeicher für die `StartupDiagnostics`-Assembly ist *.dotnet/store/x64/netcoreapp3.0/startupdiagnostics/1.0.0/lib/netcoreapp3.0/StartupDiagnostics.dll*.</span><span class="sxs-lookup"><span data-stu-id="c702a-299">The user runtime store install location for the `StartupDiagnostics` assembly is *.dotnet/store/x64/netcoreapp3.0/startupdiagnostics/1.0.0/lib/netcoreapp3.0/StartupDiagnostics.dll*.</span></span>
   * <span data-ttu-id="c702a-300">Generiert die `additionalDeps` für `StartupDiagnostics` im Ordner *additionalDeps* .</span><span class="sxs-lookup"><span data-stu-id="c702a-300">Generates the `additionalDeps` for `StartupDiagnostics` in the *additionalDeps* folder.</span></span> <span data-ttu-id="c702a-301">Die zusätzlichen Abhängigkeiten würden später in die zusätzlichen Abhängigkeiten des Benutzers oder Systems verschoben.</span><span class="sxs-lookup"><span data-stu-id="c702a-301">The additional dependencies would later be moved to the user's or system's additional dependencies.</span></span> <span data-ttu-id="c702a-302">Der Installationsspeicherort der zusätzlichen Abhängigkeiten des Benutzers `StartupDiagnostics` ist *.dotnet/x64/additionalDeps/StartupDiagnostics/shared/Microsoft.NETCore.App/3.0.0/StartupDiagnostics.deps.json*.</span><span class="sxs-lookup"><span data-stu-id="c702a-302">The user `StartupDiagnostics` additional dependencies install location is *.dotnet/x64/additionalDeps/StartupDiagnostics/shared/Microsoft.NETCore.App/3.0.0/StartupDiagnostics.deps.json*.</span></span>
   * <span data-ttu-id="c702a-303">Legt die Datei *deploy.ps1* im Ordner *deployment* ab.</span><span class="sxs-lookup"><span data-stu-id="c702a-303">Places the *deploy.ps1* file in the *deployment* folder.</span></span>
1. <span data-ttu-id="c702a-304">Führen Sie das Skript *deploy.ps1* im Ordner *deployment* aus.</span><span class="sxs-lookup"><span data-stu-id="c702a-304">Run the *deploy.ps1* script in the *deployment* folder.</span></span> <span data-ttu-id="c702a-305">Das Skript fügt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="c702a-305">The script appends:</span></span>
   * <span data-ttu-id="c702a-306">`StartupDiagnostics` an die Umgebungsvariable `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.</span><span class="sxs-lookup"><span data-stu-id="c702a-306">`StartupDiagnostics` to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
   * <span data-ttu-id="c702a-307">Der Pfad der Hostingstartabhängigkeiten (im Ordner *deployment* des RuntimeStore-Projekts) zur `DOTNET_ADDITIONAL_DEPS`-Umgebungsvariablen.</span><span class="sxs-lookup"><span data-stu-id="c702a-307">The hosting startup dependencies path (in the RuntimeStore project's *deployment* folder) to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>
   * <span data-ttu-id="c702a-308">Der Pfad des Runtimespeichers (im Ordner *deployment* des RuntimeStore-Projekts) zur `DOTNET_SHARED_STORE`-Umgebungsvariablen.</span><span class="sxs-lookup"><span data-stu-id="c702a-308">The runtime store path (in the RuntimeStore project's *deployment* folder) to the `DOTNET_SHARED_STORE` environment variable.</span></span>
1. <span data-ttu-id="c702a-309">Führen Sie die Beispiel-App aus.</span><span class="sxs-lookup"><span data-stu-id="c702a-309">Run the sample app.</span></span>
1. <span data-ttu-id="c702a-310">Fordern Sie den Endpunkt `/services` an, um die registrierten Dienste der App anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="c702a-310">Request the `/services` endpoint to see the app's registered services.</span></span> <span data-ttu-id="c702a-311">Fordern Sie den Endpunkt `/diag` an, um Diagnoseinformationen anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="c702a-311">Request the `/diag` endpoint to see the diagnostic information.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="c702a-312">Mit einer <xref:Microsoft.AspNetCore.Hosting.IHostingStartup>-Implementierung (Hostingstart) werden Verbesserungen an einer App beim Start von einer externen Assembly aus vorgenommen.</span><span class="sxs-lookup"><span data-stu-id="c702a-312">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> (hosting startup) implementation adds enhancements to an app at startup from an external assembly.</span></span> <span data-ttu-id="c702a-313">Eine externe Bibliothek kann beispielsweise eine Hostingstartimplementierung verwenden, um zusätzliche Konfigurationsanbieter oder -dienste für eine App bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="c702a-313">For example, an external library can use a hosting startup implementation to provide additional configuration providers or services to an app.</span></span>

<span data-ttu-id="c702a-314">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="c702a-314">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="hostingstartup-attribute"></a><span data-ttu-id="c702a-315">Das Attribut HostingStartup</span><span class="sxs-lookup"><span data-stu-id="c702a-315">HostingStartup attribute</span></span>

<span data-ttu-id="c702a-316">Ein [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute)-Attribut gibt an, ob eine Hostingstartassembly vorhanden ist, die zur Laufzeit aktiviert werden kann.</span><span class="sxs-lookup"><span data-stu-id="c702a-316">A [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute indicates the presence of a hosting startup assembly to activate at runtime.</span></span>

<span data-ttu-id="c702a-317">Bei der Einstiegsassembly oder bei der Assembly, die die Klasse `Startup` enthält, wird automatisch geprüft, ob sie das Attribut `HostingStartup` enthält.</span><span class="sxs-lookup"><span data-stu-id="c702a-317">The entry assembly or the assembly containing the `Startup` class is automatically scanned for the `HostingStartup` attribute.</span></span> <span data-ttu-id="c702a-318">Die Liste der Assemblys für die Suche nach `HostingStartup`-Attributen wird zur Laufzeit aus der Konfiguration in [WebHostDefaults.HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey) geladen.</span><span class="sxs-lookup"><span data-stu-id="c702a-318">The list of assemblies to search for `HostingStartup` attributes is loaded at runtime from configuration in the [WebHostDefaults.HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey).</span></span> <span data-ttu-id="c702a-319">Die Liste der Assemblys, die aus der Ermittlung ausgeschlossen werden sollen, wird aus [WebHostDefaults.HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey) geladen.</span><span class="sxs-lookup"><span data-stu-id="c702a-319">The list of assemblies to exclude from discovery is loaded from the [WebHostDefaults.HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey).</span></span> <span data-ttu-id="c702a-320">Weitere Informationen finden Sie unter [Webhost: Hostingstartassemblys](xref:fundamentals/host/web-host#hosting-startup-assemblies)[Webhost: Auszuschließende Hostingstartassemblys](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies).</span><span class="sxs-lookup"><span data-stu-id="c702a-320">For more information, see [Web Host: Hosting Startup Assemblies](xref:fundamentals/host/web-host#hosting-startup-assemblies) and [Web Host: Hosting Startup Exclude Assemblies](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies).</span></span>

<span data-ttu-id="c702a-321">Im folgenden Beispiel lautet der Namespace der Hostingstartassembly `StartupEnhancement`.</span><span class="sxs-lookup"><span data-stu-id="c702a-321">In the following example, the namespace of the hosting startup assembly is `StartupEnhancement`.</span></span> <span data-ttu-id="c702a-322">`StartupEnhancementHostingStartup` ist die Klasse mit dem Hostingstartcode:</span><span class="sxs-lookup"><span data-stu-id="c702a-322">The class containing the hosting startup code is `StartupEnhancementHostingStartup`:</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet1)]

<span data-ttu-id="c702a-323">Das Attribut `HostingStartup` befindet sich in der Regel in der `IHostingStartup`-Implementierungsklassendatei der Hostingstartassembly.</span><span class="sxs-lookup"><span data-stu-id="c702a-323">The `HostingStartup` attribute is typically located in the hosting startup assembly's `IHostingStartup` implementation class file.</span></span>

## <a name="discover-loaded-hosting-startup-assemblies"></a><span data-ttu-id="c702a-324">Ermitteln geladener Hostingstartassemblys</span><span class="sxs-lookup"><span data-stu-id="c702a-324">Discover loaded hosting startup assemblies</span></span>

<span data-ttu-id="c702a-325">Zum Ermitteln von geladenen Hostingstartassemblys aktivieren Sie die Protokollierung und überprüfen die Protokolle der App.</span><span class="sxs-lookup"><span data-stu-id="c702a-325">To discover loaded hosting startup assemblies, enable logging and check the app's logs.</span></span> <span data-ttu-id="c702a-326">Fehler, die beim Laden von Assemblys auftreten, werden protokolliert.</span><span class="sxs-lookup"><span data-stu-id="c702a-326">Errors that occur when loading assemblies are logged.</span></span> <span data-ttu-id="c702a-327">Geladene Hostingstartassemblys werden auf der Debugebene protokolliert. Außerdem werden alle Fehler protokolliert.</span><span class="sxs-lookup"><span data-stu-id="c702a-327">Loaded hosting startup assemblies are logged at the Debug level, and all errors are logged.</span></span>

## <a name="disable-automatic-loading-of-hosting-startup-assemblies"></a><span data-ttu-id="c702a-328">Deaktivieren des automatischen Ladens von Hostingstartassemblys</span><span class="sxs-lookup"><span data-stu-id="c702a-328">Disable automatic loading of hosting startup assemblies</span></span>

<span data-ttu-id="c702a-329">Um das automatische Laden von Hostingstartassemblys zu deaktivieren, verwenden Sie einen der folgenden Ansätze:</span><span class="sxs-lookup"><span data-stu-id="c702a-329">To disable automatic loading of hosting startup assemblies, use one of the following approaches:</span></span>

* <span data-ttu-id="c702a-330">Um zu verhindern, dass alle Hostingstartassemblys geladen werden, legen Sie eine der folgenden Einstellungen auf `true` oder `1` fest:</span><span class="sxs-lookup"><span data-stu-id="c702a-330">To prevent all hosting startup assemblies from loading, set one of the following to `true` or `1`:</span></span>
  * <span data-ttu-id="c702a-331">Hostkonfigurationseinstellung [Verhindern des Hostingstarts](xref:fundamentals/host/web-host#prevent-hosting-startup).</span><span class="sxs-lookup"><span data-stu-id="c702a-331">[Prevent Hosting Startup](xref:fundamentals/host/web-host#prevent-hosting-startup) host configuration setting.</span></span>
  * <span data-ttu-id="c702a-332">Die Umgebungsvariable `ASPNETCORE_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="c702a-332">`ASPNETCORE_PREVENTHOSTINGSTARTUP` environment variable.</span></span>
* <span data-ttu-id="c702a-333">Um zu verhindern, dass bestimmte Hostingstartassemblys geladen werden, legen Sie eine der folgenden Einstellungen auf eine durch Semikolons getrennte Zeichenfolge mit Hostingstartassemblys fest, die beim Starten ausgeschlossen werden sollen:</span><span class="sxs-lookup"><span data-stu-id="c702a-333">To prevent specific hosting startup assemblies from loading, set one of the following to a semicolon-delimited string of hosting startup assemblies to exclude at startup:</span></span>
  * <span data-ttu-id="c702a-334">Hostkonfigurationseinstellung [Hostingstartausschlussassemblys](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies).</span><span class="sxs-lookup"><span data-stu-id="c702a-334">[Hosting Startup Exclude Assemblies](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies) host configuration setting.</span></span>
  * <span data-ttu-id="c702a-335">Die Umgebungsvariable `ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="c702a-335">`ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES` environment variable.</span></span>

<span data-ttu-id="c702a-336">Wenn sowohl die Konfigurationseinstellung für den Host als auch die Umgebungsvariable festgelegt werden, wird das Verhalten durch die Hosteinstellung gesteuert.</span><span class="sxs-lookup"><span data-stu-id="c702a-336">If both the host configuration setting and the environment variable are set, the host setting controls the behavior.</span></span>

<span data-ttu-id="c702a-337">Durch das Deaktivieren von Hostingstartassemblys mithilfe der Hosteinstellung oder der Umgebungsvariable wird die Assembly global deaktiviert. Einige Eigenschaften einer App können ebenfalls deaktiviert werden.</span><span class="sxs-lookup"><span data-stu-id="c702a-337">Disabling hosting startup assemblies using the host setting or environment variable disables the assembly globally and may disable several characteristics of an app.</span></span>

## <a name="project"></a><span data-ttu-id="c702a-338">Projekt</span><span class="sxs-lookup"><span data-stu-id="c702a-338">Project</span></span>

<span data-ttu-id="c702a-339">Erstellen Sie einen Hostingstart mit einem der folgenden Projekttypen:</span><span class="sxs-lookup"><span data-stu-id="c702a-339">Create a hosting startup with either of the following project types:</span></span>

* [<span data-ttu-id="c702a-340">Klassenbibliothek</span><span class="sxs-lookup"><span data-stu-id="c702a-340">Class library</span></span>](#class-library)
* [<span data-ttu-id="c702a-341">Konsolen-App ohne Einstiegspunkt</span><span class="sxs-lookup"><span data-stu-id="c702a-341">Console app without an entry point</span></span>](#console-app-without-an-entry-point)

### <a name="class-library"></a><span data-ttu-id="c702a-342">Klassenbibliothek</span><span class="sxs-lookup"><span data-stu-id="c702a-342">Class library</span></span>

<span data-ttu-id="c702a-343">In einer Klassenbibliothek kann eine Hostingstarterweiterung bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="c702a-343">A hosting startup enhancement can be provided in a class library.</span></span> <span data-ttu-id="c702a-344">Die Bibliothek enthält ein `HostingStartup`-Attribut.</span><span class="sxs-lookup"><span data-stu-id="c702a-344">The library contains a `HostingStartup` attribute.</span></span>

<span data-ttu-id="c702a-345">Der [Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) enthält die Razor Pages-App *HostingStartupApp* und die Klassenbibliothek *HostingStartupLibrary*.</span><span class="sxs-lookup"><span data-stu-id="c702a-345">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) includes a Razor Pages app, *HostingStartupApp* , and a class library, *HostingStartupLibrary*.</span></span> <span data-ttu-id="c702a-346">Die Klassenbibliothek:</span><span class="sxs-lookup"><span data-stu-id="c702a-346">The class library:</span></span>

* <span data-ttu-id="c702a-347">Enthält die Hostingstartklasse `ServiceKeyInjection`, die `IHostingStartup` implementiert.</span><span class="sxs-lookup"><span data-stu-id="c702a-347">Contains a hosting startup class, `ServiceKeyInjection`, which implements `IHostingStartup`.</span></span> <span data-ttu-id="c702a-348">`ServiceKeyInjection` fügt mithilfe des Anbieters der Konfiguration im Arbeitsspeicher ([AddInMemoryCollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)) der App-Konfiguration zwei Dienstzeichenfolgen hinzu.</span><span class="sxs-lookup"><span data-stu-id="c702a-348">`ServiceKeyInjection` adds a pair of service strings to the app's configuration using the in-memory configuration provider ([AddInMemoryCollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)).</span></span>
* <span data-ttu-id="c702a-349">Enthält ein `HostingStartup`-Attribut, das den Namespace und die Klasse des Hostingstarts angibt.</span><span class="sxs-lookup"><span data-stu-id="c702a-349">Includes a `HostingStartup` attribute that identifies the hosting startup's namespace and class.</span></span>

<span data-ttu-id="c702a-350">Die <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*>-Methode der `ServiceKeyInjection`-Klasse verwendet einen <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder>, um einer App Erweiterungen hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="c702a-350">The `ServiceKeyInjection` class's <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> method uses an <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> to add enhancements to an app.</span></span>

<span data-ttu-id="c702a-351">*HostingStartupLibrary/ServiceKeyInjection.cs* :</span><span class="sxs-lookup"><span data-stu-id="c702a-351">*HostingStartupLibrary/ServiceKeyInjection.cs* :</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupLibrary/ServiceKeyInjection.cs?name=snippet1)]

<span data-ttu-id="c702a-352">Die Indexseite der App liest und rendert die Konfigurationswerte für die beiden Schlüssel, die von der Hostingstartassembly der Klassenbibliothek festgelegt werden:</span><span class="sxs-lookup"><span data-stu-id="c702a-352">The app's Index page reads and renders the configuration values for the two keys set by the class library's hosting startup assembly:</span></span>

<span data-ttu-id="c702a-353">*HostingStartupApp/Pages/Index.cshtml.cs* :</span><span class="sxs-lookup"><span data-stu-id="c702a-353">*HostingStartupApp/Pages/Index.cshtml.cs* :</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=5-6,11-12)]

<span data-ttu-id="c702a-354">Der [Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) enthält darüber hinaus auch ein NuGet-Paketprojekt, das den separaten Hostingstart *HostingStartupPackage* bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="c702a-354">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) also includes a NuGet package project that provides a separate hosting startup, *HostingStartupPackage*.</span></span> <span data-ttu-id="c702a-355">Das Paket weist dieselben Merkmale wie die bereits beschriebene Klassenbibliothek auf.</span><span class="sxs-lookup"><span data-stu-id="c702a-355">The package has the same characteristics of the class library described earlier.</span></span> <span data-ttu-id="c702a-356">Das Paket:</span><span class="sxs-lookup"><span data-stu-id="c702a-356">The package:</span></span>

* <span data-ttu-id="c702a-357">Enthält die Hostingstartklasse `ServiceKeyInjection`, die `IHostingStartup` implementiert.</span><span class="sxs-lookup"><span data-stu-id="c702a-357">Contains a hosting startup class, `ServiceKeyInjection`, which implements `IHostingStartup`.</span></span> <span data-ttu-id="c702a-358">`ServiceKeyInjection` fügt der App-Konfiguration zwei Dienstzeichenfolgen hinzu.</span><span class="sxs-lookup"><span data-stu-id="c702a-358">`ServiceKeyInjection` adds a pair of service strings to the app's configuration.</span></span>
* <span data-ttu-id="c702a-359">Enthält ein `HostingStartup`-Attribut.</span><span class="sxs-lookup"><span data-stu-id="c702a-359">Includes a `HostingStartup` attribute.</span></span>

<span data-ttu-id="c702a-360">*HostingStartupPackage/ServiceKeyInjection.cs* :</span><span class="sxs-lookup"><span data-stu-id="c702a-360">*HostingStartupPackage/ServiceKeyInjection.cs* :</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupPackage/ServiceKeyInjection.cs?name=snippet1)]

<span data-ttu-id="c702a-361">Die Indexseite der App liest und rendert die Konfigurationswerte für die beiden Schlüssel, die von der Hostingstartassembly der Bibliothek festgelegt werden:</span><span class="sxs-lookup"><span data-stu-id="c702a-361">The app's Index page reads and renders the configuration values for the two keys set by the package's hosting startup assembly:</span></span>

<span data-ttu-id="c702a-362">*HostingStartupApp/Pages/Index.cshtml.cs* :</span><span class="sxs-lookup"><span data-stu-id="c702a-362">*HostingStartupApp/Pages/Index.cshtml.cs* :</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=7-8,13-14)]

### <a name="console-app-without-an-entry-point"></a><span data-ttu-id="c702a-363">Konsolen-App ohne Einstiegspunkt</span><span class="sxs-lookup"><span data-stu-id="c702a-363">Console app without an entry point</span></span>

<span data-ttu-id="c702a-364">*Dieses Verfahren ist nur für .NET Core-Apps, nicht jedoch für .NET Framework verfügbar.*</span><span class="sxs-lookup"><span data-stu-id="c702a-364">*This approach is only available for .NET Core apps, not .NET Framework.*</span></span>

<span data-ttu-id="c702a-365">Eine dynamische Hostingstarterweiterung, die zur Aktivierung keinen Kompilierungszeitverweis erfordert, kann in einer Konsolen-App ohne Einstiegspunkt bereitgestellt werden, die ein `HostingStartup`-Attribut enthält.</span><span class="sxs-lookup"><span data-stu-id="c702a-365">A dynamic hosting startup enhancement that doesn't require a compile-time reference for activation can be provided in a console app without an entry point that contains a `HostingStartup` attribute.</span></span> <span data-ttu-id="c702a-366">Durch die Veröffentlichung der Konsolen-App wird eine Hostingstartassembly erstellt, die über den Laufzeitspeicher genutzt werden kann.</span><span class="sxs-lookup"><span data-stu-id="c702a-366">Publishing the console app produces a hosting startup assembly that can be consumed from the runtime store.</span></span>

<span data-ttu-id="c702a-367">In diesem Prozess wird aus folgenden Gründen eine Konsolen-App ohne Einstiegspunkt verwendet:</span><span class="sxs-lookup"><span data-stu-id="c702a-367">A console app without an entry point is used in this process because:</span></span>

* <span data-ttu-id="c702a-368">Eine Abhängigkeitendatei ist erforderlich, um den Hostingstart in der Hostingstartassembly zu nutzen.</span><span class="sxs-lookup"><span data-stu-id="c702a-368">A dependencies file is required to consume the hosting startup in the hosting startup assembly.</span></span> <span data-ttu-id="c702a-369">Eine Abhängigkeitendatei ist eine ausführbare App-Ressource, die durch das Veröffentlichen einer App, nicht einer Bibliothek, erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="c702a-369">A dependencies file is a runnable app asset that's produced by publishing an app, not a library.</span></span>
* <span data-ttu-id="c702a-370">Eine Bibliothek kann dem [Laufzeitpaketspeicher](/dotnet/core/deploying/runtime-store), der ein ausführbares Projekt benötigt, das die freigegebene Laufzeit als Ziel verwendet, nicht direkt hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="c702a-370">A library can't be added directly to the [runtime package store](/dotnet/core/deploying/runtime-store), which requires a runnable project that targets the shared runtime.</span></span>

<span data-ttu-id="c702a-371">Bei der Erstellung eines dynamischen Hostingstarts geschieht Folgendes:</span><span class="sxs-lookup"><span data-stu-id="c702a-371">In the creation of a dynamic hosting startup:</span></span>

* <span data-ttu-id="c702a-372">Eine Hostingstartassembly wird über die Konsolen-App ohne Einstiegspunkt erstellt, für die Folgendes gilt:</span><span class="sxs-lookup"><span data-stu-id="c702a-372">A hosting startup assembly is created from the console app without an entry point that:</span></span>
  * <span data-ttu-id="c702a-373">Sie enthält eine Klasse mit der `IHostingStartup`-Implementierung.</span><span class="sxs-lookup"><span data-stu-id="c702a-373">Includes a class that contains the `IHostingStartup` implementation.</span></span>
  * <span data-ttu-id="c702a-374">Sie enthält ein [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute)-Attribut zum Identifizieren der `IHostingStartup`-Implementierungsklasse.</span><span class="sxs-lookup"><span data-stu-id="c702a-374">Includes a [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute to identify the `IHostingStartup` implementation class.</span></span>
* <span data-ttu-id="c702a-375">Die Konsolen-App wird veröffentlicht, um die Abhängigkeiten des Hostingstarts abzurufen.</span><span class="sxs-lookup"><span data-stu-id="c702a-375">The console app is published to obtain the hosting startup's dependencies.</span></span> <span data-ttu-id="c702a-376">Die Veröffentlichung der Konsolen-App hat unter anderem zur Folge, dass nicht verwendete Abhängigkeiten aus der Abhängigkeitendatei entfernt werden.</span><span class="sxs-lookup"><span data-stu-id="c702a-376">A consequence of publishing the console app is that unused dependencies are trimmed from the dependencies file.</span></span>
* <span data-ttu-id="c702a-377">Die Abhängigkeitendatei wird so geändert, dass der Laufzeitspeicherort der Hostingstartassembly festgelegt wird.</span><span class="sxs-lookup"><span data-stu-id="c702a-377">The dependencies file is modified to set the runtime location of the hosting startup assembly.</span></span>
* <span data-ttu-id="c702a-378">Die Hostingstartassembly und die dazu gehörende Abhängigkeitendatei werden im Laufzeitpaketspeicher abgelegt.</span><span class="sxs-lookup"><span data-stu-id="c702a-378">The hosting startup assembly and its dependencies file is placed into the runtime package store.</span></span> <span data-ttu-id="c702a-379">Damit die Hostingstartassembly und die entsprechende Abhängigkeitendatei erkannt werden, werden sie in einem Paar von Umgebungsvariablen aufgeführt.</span><span class="sxs-lookup"><span data-stu-id="c702a-379">To discover the hosting startup assembly and its dependencies file, they're listed in a pair of environment variables.</span></span>

<span data-ttu-id="c702a-380">Die Konsolen-App verweist auf das Paket [Microsoft.AspNetCore.Hosting.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/):</span><span class="sxs-lookup"><span data-stu-id="c702a-380">The console app references the [Microsoft.AspNetCore.Hosting.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/) package:</span></span>

[!code-xml[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.csproj)]

<span data-ttu-id="c702a-381">Ein [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute)-Attribut identifiziert eine Klasse bei der Erstellung von <xref:Microsoft.AspNetCore.Hosting.IWebHost> als eine Implementierung von `IHostingStartup` zum Laden und Ausführen.</span><span class="sxs-lookup"><span data-stu-id="c702a-381">A [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute identifies a class as an implementation of `IHostingStartup` for loading and execution when building the <xref:Microsoft.AspNetCore.Hosting.IWebHost>.</span></span> <span data-ttu-id="c702a-382">Im folgenden Beispiel ist der Namespace `StartupEnhancement` und die Klasse ist `StartupEnhancementHostingStartup`:</span><span class="sxs-lookup"><span data-stu-id="c702a-382">In the following example, the namespace is `StartupEnhancement`, and the class is `StartupEnhancementHostingStartup`:</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet1)]

<span data-ttu-id="c702a-383">Eine Klasse implementiert `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="c702a-383">A class implements `IHostingStartup`.</span></span> <span data-ttu-id="c702a-384">Die <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*>-Methode der Klasse verwendet einen <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder>, um einer App Erweiterungen hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="c702a-384">The class's <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> method uses an <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> to add enhancements to an app.</span></span> <span data-ttu-id="c702a-385">`IHostingStartup.Configure` wird in der Hoststartassembly von der Runtime vor `Startup.Configure` im Benutzercode aufgerufen. Dadurch kann Benutzercode jede Konfiguration der Hoststartassembly überschreiben.</span><span class="sxs-lookup"><span data-stu-id="c702a-385">`IHostingStartup.Configure` in the hosting startup assembly is called by the runtime before `Startup.Configure` in user code, which allows user code to overwrite any configuration provided by the hosting startup assembly.</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet2&highlight=3,5)]

<span data-ttu-id="c702a-386">Die Datei für die Abhängigkeiten ( *.deps.json* ) legt beim Erstellen eine `IHostingStartup`-Projekts den `runtime`-Speicherort der Assembly auf den *bin* -Ordner fest:</span><span class="sxs-lookup"><span data-stu-id="c702a-386">When building an `IHostingStartup` project, the dependencies file ( *.deps.json* ) sets the `runtime` location of the assembly to the *bin* folder:</span></span>

[!code-json[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement1.deps.json?range=2-13&highlight=8)]

<span data-ttu-id="c702a-387">Nur ein Teil der Datei wird angezeigt.</span><span class="sxs-lookup"><span data-stu-id="c702a-387">Only part of the file is shown.</span></span> <span data-ttu-id="c702a-388">`StartupEnhancement` ist der Name der Assembly im Beispiel.</span><span class="sxs-lookup"><span data-stu-id="c702a-388">The assembly name in the example is `StartupEnhancement`.</span></span>

## <a name="configuration-provided-by-the-hosting-startup"></a><span data-ttu-id="c702a-389">Beim Hostingstart bereitgestellte Konfiguration</span><span class="sxs-lookup"><span data-stu-id="c702a-389">Configuration provided by the hosting startup</span></span>

<span data-ttu-id="c702a-390">Es gibt zwei Ansätze zur Verarbeitung der Konfiguration, je nachdem, ob die Konfiguration des Hostingstarts oder die der App Vorrang haben soll:</span><span class="sxs-lookup"><span data-stu-id="c702a-390">There are two approaches to handling configuration depending on whether you want the hosting startup's configuration to take precedence or the app's configuration to take precedence:</span></span>

1. <span data-ttu-id="c702a-391">Bereitstellen der Konfiguration für die App mit <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> zum Laden der Konfiguration, nachdem die <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*>-Delegaten der App ausgeführt wurden.</span><span class="sxs-lookup"><span data-stu-id="c702a-391">Provide configuration to the app using <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> to load the configuration after the app's <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> delegates execute.</span></span> <span data-ttu-id="c702a-392">Bei diesem Ansatz hat die Hostingstartkonfiguration Vorrang vor der Konfiguration der App.</span><span class="sxs-lookup"><span data-stu-id="c702a-392">Hosting startup configuration takes priority over the app's configuration using this approach.</span></span>
1. <span data-ttu-id="c702a-393">Bereitstellen der Konfiguration für die App mit <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> zum Laden der Konfiguration, bevor die <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*>-Delegaten der App ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="c702a-393">Provide configuration to the app using <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> to load the configuration before the app's <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> delegates execute.</span></span> <span data-ttu-id="c702a-394">Die Konfigurationswerte der App haben bei diesem Ansatz Vorrang vor denen des Hostingstarts.</span><span class="sxs-lookup"><span data-stu-id="c702a-394">The app's configuration values take priority over those provided by the hosting startup using this approach.</span></span>

```csharp
public class ConfigurationInjection : IHostingStartup
{
    public void Configure(IWebHostBuilder builder)
    {
        Dictionary<string, string> dict;

        builder.ConfigureAppConfiguration(config =>
        {
            dict = new Dictionary<string, string>
            {
                {"ConfigurationKey1", 
                    "From IHostingStartup: Higher priority " +
                    "than the app's configuration."},
            };

            config.AddInMemoryCollection(dict);
        });

        dict = new Dictionary<string, string>
        {
            {"ConfigurationKey2", 
                "From IHostingStartup: Lower priority " +
                "than the app's configuration."},
        };

        var builtConfig = new ConfigurationBuilder()
            .AddInMemoryCollection(dict)
            .Build();

        builder.UseConfiguration(builtConfig);
    }
}
```

## <a name="specify-the-hosting-startup-assembly"></a><span data-ttu-id="c702a-395">Angeben der Hostingstartassembly</span><span class="sxs-lookup"><span data-stu-id="c702a-395">Specify the hosting startup assembly</span></span>

<span data-ttu-id="c702a-396">Geben Sie für einen von der Klassenbibliothek oder von der Konsolen-App bereitgestellten Hostingstart den Namen der Hostingstartassembly in der Umgebungsvariablen `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` an.</span><span class="sxs-lookup"><span data-stu-id="c702a-396">For either a class library- or console app-supplied hosting startup, specify the hosting startup assembly's name in the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span> <span data-ttu-id="c702a-397">Die Umgebungsvariable ist eine durch Semikolons getrennte Liste von Assemblys.</span><span class="sxs-lookup"><span data-stu-id="c702a-397">The environment variable is a semicolon-delimited list of assemblies.</span></span>

<span data-ttu-id="c702a-398">Nur in Hostingstartassemblys wird nach dem `HostingStartup`-Attribut gesucht.</span><span class="sxs-lookup"><span data-stu-id="c702a-398">Only hosting startup assemblies are scanned for the `HostingStartup` attribute.</span></span> <span data-ttu-id="c702a-399">Damit die Beispiel-App *HostingStartupApp* die weiter oben beschriebenen Hostingstarts erkennt, wird für die Umgebungsvariable der folgende Wert festgelegt:</span><span class="sxs-lookup"><span data-stu-id="c702a-399">For the sample app, *HostingStartupApp* , to discover the hosting startups described earlier, the environment variable is set to the following value:</span></span>

```
HostingStartupLibrary;HostingStartupPackage;StartupDiagnostics
```

<span data-ttu-id="c702a-400">Eine Hostingstartassembly kann auch mithilfe der Hostkonfigurationseinstellung [Hostingstartassemblys](xref:fundamentals/host/web-host#hosting-startup-assemblies) festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="c702a-400">A hosting startup assembly can also be set using the [Hosting Startup Assemblies](xref:fundamentals/host/web-host#hosting-startup-assemblies) host configuration setting.</span></span>

<span data-ttu-id="c702a-401">Wenn mehrere Hoststartassemblys vorhanden sind, werden ihre <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*>-Methoden in der Reihenfolge der aufgelisteten Assemblys ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="c702a-401">When multiple hosting startup assembles are present, their <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> methods are executed in the order that the assemblies are listed.</span></span>

## <a name="activation"></a><span data-ttu-id="c702a-402">Aktivierung</span><span class="sxs-lookup"><span data-stu-id="c702a-402">Activation</span></span>

<span data-ttu-id="c702a-403">Optionen für die Hostingstartaktivierung:</span><span class="sxs-lookup"><span data-stu-id="c702a-403">Options for hosting startup activation are:</span></span>

* <span data-ttu-id="c702a-404">[Laufzeitspeicher:](#runtime-store) Für die Aktivierung ist kein Kompilierzeitverweis erforderlich.</span><span class="sxs-lookup"><span data-stu-id="c702a-404">[Runtime store](#runtime-store): Activation doesn't require a compile-time reference for activation.</span></span> <span data-ttu-id="c702a-405">Die Beispiel-App legt die Hostingstartassembly und die Abhängigkeitendateien im Ordner *deployment* ab, um die Bereitstellung des Hostingstarts in einer Umgebung mit mehreren Computern zu erleichtern.</span><span class="sxs-lookup"><span data-stu-id="c702a-405">The sample app places the hosting startup assembly and dependencies files into a folder, *deployment* , to facilitate deployment of the hosting startup in a multimachine environment.</span></span> <span data-ttu-id="c702a-406">Der Ordner *deployment* enthält darüber hinaus auch ein PowerShell-Skript, das im Bereitstellungssystem Umgebungsvariablen erstellt oder ändert, um den Hostingstart zu ermöglichen.</span><span class="sxs-lookup"><span data-stu-id="c702a-406">The *deployment* folder also includes a PowerShell script that creates or modifies environment variables on the deployment system to enable the hosting startup.</span></span>
* <span data-ttu-id="c702a-407">Kompilierzeitverweis für die Aktivierung erforderlich</span><span class="sxs-lookup"><span data-stu-id="c702a-407">Compile-time reference required for activation</span></span>
  * [<span data-ttu-id="c702a-408">NuGet-Paket</span><span class="sxs-lookup"><span data-stu-id="c702a-408">NuGet package</span></span>](#nuget-package)
  * [<span data-ttu-id="c702a-409">Ordner „Bin“ des Projekts</span><span class="sxs-lookup"><span data-stu-id="c702a-409">Project bin folder</span></span>](#project-bin-folder)

### <a name="runtime-store"></a><span data-ttu-id="c702a-410">Laufzeitspeicher</span><span class="sxs-lookup"><span data-stu-id="c702a-410">Runtime store</span></span>

<span data-ttu-id="c702a-411">Die Hostingstartimplementierung wird im [Laufzeitspeicher](/dotnet/core/deploying/runtime-store) abgelegt.</span><span class="sxs-lookup"><span data-stu-id="c702a-411">The hosting startup implementation is placed in the [runtime store](/dotnet/core/deploying/runtime-store).</span></span> <span data-ttu-id="c702a-412">Ein Kompilierzeitverweis auf die Assembly wird von der erweiterten App nicht benötigt.</span><span class="sxs-lookup"><span data-stu-id="c702a-412">A compile-time reference to the assembly isn't required by the enhanced app.</span></span>

<span data-ttu-id="c702a-413">Nach der Erstellung des Hostingstarts wird mithilfe der Manifestprojektdatei und des Befehls [dotnet store](/dotnet/core/tools/dotnet-store) ein Laufzeitspeicher generiert.</span><span class="sxs-lookup"><span data-stu-id="c702a-413">After the hosting startup is built, a runtime store is generated using the manifest project file and the [dotnet store](/dotnet/core/tools/dotnet-store) command.</span></span>

```dotnetcli
dotnet store --manifest {MANIFEST FILE} --runtime {RUNTIME IDENTIFIER} --output {OUTPUT LOCATION} --skip-optimization
```

<span data-ttu-id="c702a-414">In der Beispiel-App (Projekt *RuntimeStore* ) wird der folgende Befehl verwendet:</span><span class="sxs-lookup"><span data-stu-id="c702a-414">In the sample app ( *RuntimeStore* project) the following command is used:</span></span>

```dotnetcli
dotnet store --manifest store.manifest.csproj --runtime win7-x64 --output ./deployment/store --skip-optimization
```

<span data-ttu-id="c702a-415">Damit die Runtime den Laufzeitspeicher ermitteln kann, wird der Speicherort des Laufzeitspeichers der Umgebungsvariablen `DOTNET_SHARED_STORE` hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="c702a-415">For the runtime to discover the runtime store, the runtime store's location is added to the `DOTNET_SHARED_STORE` environment variable.</span></span>

<span data-ttu-id="c702a-416">**Ändern und Ablegen der Abhängigkeitendatei des Hostingstarts**</span><span class="sxs-lookup"><span data-stu-id="c702a-416">**Modify and place the hosting startup's dependencies file**</span></span>

<span data-ttu-id="c702a-417">Um die Erweiterung ohne einen Paketverweis auf die Erweiterung zu aktivieren, geben Sie mit `additionalDeps` zusätzliche Abhängigkeiten zur Laufzeit an.</span><span class="sxs-lookup"><span data-stu-id="c702a-417">To activate the enhancement without a package reference to the enhancement, specify additional dependencies to the runtime with `additionalDeps`.</span></span> <span data-ttu-id="c702a-418">`additionalDeps` ermöglicht Ihnen Folgendes:</span><span class="sxs-lookup"><span data-stu-id="c702a-418">`additionalDeps` allows you to:</span></span>

* <span data-ttu-id="c702a-419">Erweitern des App-Bibliotheksdiagramms durch Bereitstellen einer Reihe zusätzlicher *.deps.json* -Dateien, die beim Start mit der App-eigenen *.deps.json* -Datei zusammengeführt werden.</span><span class="sxs-lookup"><span data-stu-id="c702a-419">Extend the app's library graph by providing a set of additional *.deps.json* files to merge with the app's own *.deps.json* file on startup.</span></span>
* <span data-ttu-id="c702a-420">Bereitstellen der Hostingstartassembly, sodass sie ermittelt und geladen werden kann</span><span class="sxs-lookup"><span data-stu-id="c702a-420">Make the hosting startup assembly discoverable and loadable.</span></span>

<span data-ttu-id="c702a-421">Folgender Ansatz wird zum Generieren der zusätzlichen Abhängigkeitendatei empfohlen:</span><span class="sxs-lookup"><span data-stu-id="c702a-421">The recommended approach for generating the additional dependencies file is to:</span></span>

 1. <span data-ttu-id="c702a-422">Führen Sie `dotnet publish` für die Manifestdatei des Laufzeitspeichers aus, auf die im vorherigen Abschnitt verwiesen wurde.</span><span class="sxs-lookup"><span data-stu-id="c702a-422">Execute `dotnet publish` on the runtime store manifest file referenced in the previous section.</span></span>
 1. <span data-ttu-id="c702a-423">Entfernen Sie den Manifestverweis aus den Bibliotheken und dem Abschnitt `runtime` der daraus resultierenden *.deps.json* -Datei.</span><span class="sxs-lookup"><span data-stu-id="c702a-423">Remove the manifest reference from libraries and the `runtime` section of the resulting *.deps.json* file.</span></span>

<span data-ttu-id="c702a-424">Im Beispielprojekt wird die Eigenschaft `store.manifest/1.0.0` aus den Abschnitten `targets` und `libraries` entfernt:</span><span class="sxs-lookup"><span data-stu-id="c702a-424">In the example project, the `store.manifest/1.0.0` property is removed from the `targets` and `libraries` section:</span></span>

```json
{
  "runtimeTarget": {
    "name": ".NETCoreApp,Version=v2.1",
    "signature": "4ea77c7b75ad1895ae1ea65e6ba2399010514f99"
  },
  "compilationOptions": {},
  "targets": {
    ".NETCoreApp,Version=v2.1": {
      "store.manifest/1.0.0": {
        "dependencies": {
          "StartupDiagnostics": "1.0.0"
        },
        "runtime": {
          "store.manifest.dll": {}
        }
      },
      "StartupDiagnostics/1.0.0": {
        "runtime": {
          "lib/netcoreapp2.1/StartupDiagnostics.dll": {
            "assemblyVersion": "1.0.0.0",
            "fileVersion": "1.0.0.0"
          }
        }
      }
    }
  },
  "libraries": {
    "store.manifest/1.0.0": {
      "type": "project",
      "serviceable": false,
      "sha512": ""
    },
    "StartupDiagnostics/1.0.0": {
      "type": "package",
      "serviceable": true,
      "sha512": "sha512-oiQr60vBQW7+nBTmgKLSldj06WNLRTdhOZpAdEbCuapoZ+M2DJH2uQbRLvFT8EGAAv4TAKzNtcztpx5YOgBXQQ==",
      "path": "startupdiagnostics/1.0.0",
      "hashPath": "startupdiagnostics.1.0.0.nupkg.sha512"
    }
  }
}
```

<span data-ttu-id="c702a-425">Legen Sie die *.deps.json* -Datei an folgendem Speicherort ab:</span><span class="sxs-lookup"><span data-stu-id="c702a-425">Place the *.deps.json* file into the following location:</span></span>

```
{ADDITIONAL DEPENDENCIES PATH}/shared/{SHARED FRAMEWORK NAME}/{SHARED FRAMEWORK VERSION}/{ENHANCEMENT ASSEMBLY NAME}.deps.json
```

* <span data-ttu-id="c702a-426">`{ADDITIONAL DEPENDENCIES PATH}`: Der Speicherort wurde zur Umgebungsvariable `DOTNET_ADDITIONAL_DEPS` hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="c702a-426">`{ADDITIONAL DEPENDENCIES PATH}`: Location added to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>
* <span data-ttu-id="c702a-427">`{SHARED FRAMEWORK NAME}`: Dies ist das freigegebene Framework, das für diese zusätzliche Abhängigkeitsdatei erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="c702a-427">`{SHARED FRAMEWORK NAME}`: Shared framework required for this additional dependencies file.</span></span>
* <span data-ttu-id="c702a-428">`{SHARED FRAMEWORK VERSION}`: Dies ist die Mindestversion des freigegebenen Frameworks.</span><span class="sxs-lookup"><span data-stu-id="c702a-428">`{SHARED FRAMEWORK VERSION}`: Minimum shared framework version.</span></span>
* <span data-ttu-id="c702a-429">`{ENHANCEMENT ASSEMBLY NAME}`: Dies ist der Name der Erweiterungsassembly.</span><span class="sxs-lookup"><span data-stu-id="c702a-429">`{ENHANCEMENT ASSEMBLY NAME}`: The enhancement's assembly name.</span></span>

<span data-ttu-id="c702a-430">In der Beispiel-App (Projekt *RuntimeStore* ) wird die zusätzliche Abhängigkeitendatei an folgendem Speicherort abgelegt:</span><span class="sxs-lookup"><span data-stu-id="c702a-430">In the sample app ( *RuntimeStore* project), the additional dependencies file is placed into the following location:</span></span>

```
deployment/additionalDeps/shared/Microsoft.AspNetCore.App/2.1.0/StartupDiagnostics.deps.json
```

<span data-ttu-id="c702a-431">Damit die Runtime den Speicherort des Laufzeitspeichers ermitteln kann, wir die zusätzliche Abhängigkeitendatei der Umgebungsvariablen `DOTNET_ADDITIONAL_DEPS` hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="c702a-431">For runtime to discover the runtime store location, the additional dependencies file location is added to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>

<span data-ttu-id="c702a-432">In der Beispiel-App (Projekt *RuntimeStore* ) wird das Erstellen des Laufzeitspeichers und das Generieren der zusätzlichen Abhängigkeitendatei durch ein [PowerShell](/powershell/scripting/powershell-scripting)-Skript durchgeführt.</span><span class="sxs-lookup"><span data-stu-id="c702a-432">In the sample app ( *RuntimeStore* project), building the runtime store and generating the additional dependencies file is accomplished using a [PowerShell](/powershell/scripting/powershell-scripting) script.</span></span>

<span data-ttu-id="c702a-433">Beispiele zum Festlegen von Umgebungsvariablen für verschiedene Betriebssysteme finden Sie unter [Use multiple environments (Verwenden mehrerer Umgebungen)](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="c702a-433">For examples of how to set environment variables for various operating systems, see [Use multiple environments](xref:fundamentals/environments).</span></span>

<span data-ttu-id="c702a-434">**Bereitstellung**</span><span class="sxs-lookup"><span data-stu-id="c702a-434">**Deployment**</span></span>

<span data-ttu-id="c702a-435">Um die Bereitstellung eines Hostingstarts in einer Umgebung mit mehreren Computern zu erleichtern, erstellt die Beispiel-App in der veröffentlichten Ausgabe einen *deployment* -Ordner, der Folgendes enthält:</span><span class="sxs-lookup"><span data-stu-id="c702a-435">To facilitate the deployment of a hosting startup in a multimachine environment, the sample app creates a *deployment* folder in published output that contains:</span></span>

* <span data-ttu-id="c702a-436">Der Laufzeitspeicher des Hostingstarts.</span><span class="sxs-lookup"><span data-stu-id="c702a-436">The hosting startup runtime store.</span></span>
* <span data-ttu-id="c702a-437">Die Abhängigkeitendatei des Hostingstarts.</span><span class="sxs-lookup"><span data-stu-id="c702a-437">The hosting startup dependencies file.</span></span>
* <span data-ttu-id="c702a-438">Ein PowerShell-Skript, das `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`, `DOTNET_SHARED_STORE` und `DOTNET_ADDITIONAL_DEPS` erstellt oder ändert, um die Aktivierung des Hostingstarts zu unterstützen.</span><span class="sxs-lookup"><span data-stu-id="c702a-438">A PowerShell script that creates or modifies the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`, `DOTNET_SHARED_STORE`, and `DOTNET_ADDITIONAL_DEPS` to support the activation of the hosting startup.</span></span> <span data-ttu-id="c702a-439">Führen Sie das Skript über eine PowerShell-Administratoreingabeaufforderung auf dem Bereitstellungssystem aus.</span><span class="sxs-lookup"><span data-stu-id="c702a-439">Run the script from an administrative PowerShell command prompt on the deployment system.</span></span>

### <a name="nuget-package"></a><span data-ttu-id="c702a-440">NuGet-Paket</span><span class="sxs-lookup"><span data-stu-id="c702a-440">NuGet package</span></span>

<span data-ttu-id="c702a-441">In einem NuGet-Paket kann eine Hostingstarterweiterung bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="c702a-441">A hosting startup enhancement can be provided in a NuGet package.</span></span> <span data-ttu-id="c702a-442">Das Paket enthält ein `HostingStartup`-Attribut.</span><span class="sxs-lookup"><span data-stu-id="c702a-442">The package has a `HostingStartup` attribute.</span></span> <span data-ttu-id="c702a-443">Die vom Paket bereitgestellten Hostingstarttypen werden für die App mit einem der folgenden Verfahren verfügbar gemacht:</span><span class="sxs-lookup"><span data-stu-id="c702a-443">The hosting startup types provided by the package are made available to the app using either of the following approaches:</span></span>

* <span data-ttu-id="c702a-444">Die Projektdatei der erweiterten App erstellt einen Paketverweis für den Hostingstart in der Projektdatei der App (Kompilierzeitverweis).</span><span class="sxs-lookup"><span data-stu-id="c702a-444">The enhanced app's project file makes a package reference for the hosting startup in the app's project file (a compile-time reference).</span></span> <span data-ttu-id="c702a-445">Wenn ein Kompilierzeitverweis eingerichtet ist, werden die Hostingstartassembly und alle dazu gehörenden Abhängigkeiten in die Abhängigkeitendatei der App ( *.deps.json* ) eingebunden.</span><span class="sxs-lookup"><span data-stu-id="c702a-445">With the compile-time reference in place, the hosting startup assembly and all of its dependencies are incorporated into the app's dependency file ( *.deps.json* ).</span></span> <span data-ttu-id="c702a-446">Dieses Verfahren gilt für ein in [nuget.org](https://www.nuget.org/) veröffentlichtes Hostingstartassembly-Paket.</span><span class="sxs-lookup"><span data-stu-id="c702a-446">This approach applies to a hosting startup assembly package published to [nuget.org](https://www.nuget.org/).</span></span>
* <span data-ttu-id="c702a-447">Die Abhängigkeitendatei des Hostingstarts wird für die erweiterte App wie im Abschnitt [Laufzeitspeicher](#runtime-store) beschrieben (ohne Kompilierzeitverweis) verfügbar gemacht.</span><span class="sxs-lookup"><span data-stu-id="c702a-447">The hosting startup's dependencies file is made available to the enhanced app as described in the [Runtime store](#runtime-store) section (without a compile-time reference).</span></span>

<span data-ttu-id="c702a-448">Weitere Informationen zu NuGet-Paketen und den Laufzeitspeicher finden Sie in den folgenden Themen:</span><span class="sxs-lookup"><span data-stu-id="c702a-448">For more information on NuGet packages and the runtime store, see the following topics:</span></span>

* [<span data-ttu-id="c702a-449">So erstellen Sie ein NuGet-Paket mit plattformübergreifenden Tools</span><span class="sxs-lookup"><span data-stu-id="c702a-449">How to Create a NuGet Package with Cross Platform Tools</span></span>](/dotnet/core/deploying/creating-nuget-packages)
* [<span data-ttu-id="c702a-450">Veröffentlichen von Paketen</span><span class="sxs-lookup"><span data-stu-id="c702a-450">Publishing packages</span></span>](/nuget/create-packages/publish-a-package)
* [<span data-ttu-id="c702a-451">Laufzeitpaketspeicher</span><span class="sxs-lookup"><span data-stu-id="c702a-451">Runtime package store</span></span>](/dotnet/core/deploying/runtime-store)

### <a name="project-bin-folder"></a><span data-ttu-id="c702a-452">Ordner „Bin“ des Projekts</span><span class="sxs-lookup"><span data-stu-id="c702a-452">Project bin folder</span></span>

<span data-ttu-id="c702a-453">Eine Hostingstarterweiterung kann durch eine mittels *bin* bereitgestellte Assembly in der erweiterten App bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="c702a-453">A hosting startup enhancement can be provided by a *bin* -deployed assembly in the enhanced app.</span></span> <span data-ttu-id="c702a-454">Die von der Assembly bereitgestellten Hostingstarttypen werden für die App mit einem der folgenden Verfahren verfügbar gemacht:</span><span class="sxs-lookup"><span data-stu-id="c702a-454">The hosting startup types provided by the assembly are made available to the app using one of the following approaches:</span></span>

* <span data-ttu-id="c702a-455">Die Projektdatei der erweiterten App enthält einen Assemblyverweis auf den Hostingstart (Kompilierzeitverweis).</span><span class="sxs-lookup"><span data-stu-id="c702a-455">The enhanced app's project file makes an assembly reference to the hosting startup (a compile-time reference).</span></span> <span data-ttu-id="c702a-456">Wenn ein Kompilierzeitverweis eingerichtet ist, werden die Hostingstartassembly und alle dazu gehörenden Abhängigkeiten in die Abhängigkeitendatei der App ( *.deps.json* ) eingebunden.</span><span class="sxs-lookup"><span data-stu-id="c702a-456">With the compile-time reference in place, the hosting startup assembly and all of its dependencies are incorporated into the app's dependency file ( *.deps.json* ).</span></span> <span data-ttu-id="c702a-457">Dieser Ansatz gilt, wenn das Bereitstellungsszenario erfordert, dass ein Kompilierzeitverweis auf die Assembly des Hostingstarts ( *DLL* -Datei) erstellt und die Assembly in eine der beiden Dateien verschoben wird:</span><span class="sxs-lookup"><span data-stu-id="c702a-457">This approach applies when the deployment scenario calls for making a compile-time reference to the hosting startup's assembly ( *.dll* file) and moving the assembly to either:</span></span>
  * <span data-ttu-id="c702a-458">Das verbrauchende Projekt.</span><span class="sxs-lookup"><span data-stu-id="c702a-458">The consuming project.</span></span>
  * <span data-ttu-id="c702a-459">Einen Speicherort, auf den das verbrauchende Projekt zugreifen kann.</span><span class="sxs-lookup"><span data-stu-id="c702a-459">A location accessible by the consuming project.</span></span>
* <span data-ttu-id="c702a-460">Die Abhängigkeitendatei des Hostingstarts wird für die erweiterte App wie im Abschnitt [Laufzeitspeicher](#runtime-store) beschrieben (ohne Kompilierzeitverweis) verfügbar gemacht.</span><span class="sxs-lookup"><span data-stu-id="c702a-460">The hosting startup's dependencies file is made available to the enhanced app as described in the [Runtime store](#runtime-store) section (without a compile-time reference).</span></span>
* <span data-ttu-id="c702a-461">Wenn das Ziel .NET Framework ist, kann die Assembly in den Standardladekontext geladen werden, was bei .NET Framework bedeutet, dass sich die Assembly an einem der folgenden Speicherorte befindet:</span><span class="sxs-lookup"><span data-stu-id="c702a-461">When targeting the .NET Framework, the assembly is loadable in the default load context, which on .NET Framework means that the assembly is located at either of the following locations:</span></span>
  * <span data-ttu-id="c702a-462">Anwendungsbasispfad: Dabei handelt es sich um den Ordner *bin* , in dem die ausführbare Datei ( *.exe* ) der App gespeichert wird.</span><span class="sxs-lookup"><span data-stu-id="c702a-462">Application base path: The *bin* folder where the app's executable ( *.exe* ) is located.</span></span>
  * <span data-ttu-id="c702a-463">Globaler Assemblycache (GAC): Der GAC speichert Assemblys, die mehrere .NET Framework-Apps gemeinsam nutzen.</span><span class="sxs-lookup"><span data-stu-id="c702a-463">Global Assembly Cache (GAC): The GAC stores assemblies that several .NET Framework apps share.</span></span> <span data-ttu-id="c702a-464">Weitere Informationen finden Sie unter [Vorgehensweise: Installieren einer Assembly in den globalen Assemblycache](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac) in der .NET Framework-Dokumentation.</span><span class="sxs-lookup"><span data-stu-id="c702a-464">For more information, see [How to: Install an assembly into the global assembly cache](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac) in the .NET Framework documentation.</span></span>

## <a name="sample-code"></a><span data-ttu-id="c702a-465">Beispielcode</span><span class="sxs-lookup"><span data-stu-id="c702a-465">Sample code</span></span>

<span data-ttu-id="c702a-466">Der [Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([Herunterladen](xref:index#how-to-download-a-sample)) zeigt Szenarios für die Hostingstartimplementierung:</span><span class="sxs-lookup"><span data-stu-id="c702a-466">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([how to download](xref:index#how-to-download-a-sample)) demonstrates hosting startup implementation scenarios:</span></span>

* <span data-ttu-id="c702a-467">Zwei Hostingstartassemblys (Klassenbibliotheken) legen zwei Schlüssel-Wert-Paare der Konfiguration im Arbeitsspeicher fest:</span><span class="sxs-lookup"><span data-stu-id="c702a-467">Two hosting startup assemblies (class libraries) set a pair of in-memory configuration key-value pairs each:</span></span>
  * <span data-ttu-id="c702a-468">NuGet-Paket ( *HostingStartupPackage* )</span><span class="sxs-lookup"><span data-stu-id="c702a-468">NuGet package ( *HostingStartupPackage* )</span></span>
  * <span data-ttu-id="c702a-469">Klassenbibliothek ( *HostingStartupLibrary* )</span><span class="sxs-lookup"><span data-stu-id="c702a-469">Class library ( *HostingStartupLibrary* )</span></span>
* <span data-ttu-id="c702a-470">Ein Hostingstart wird über eine im Laufzeitspeicher bereitgestellte Assembly ( *StartupDiagnostics* ) aktiviert.</span><span class="sxs-lookup"><span data-stu-id="c702a-470">A hosting startup is activated from a runtime store-deployed assembly ( *StartupDiagnostics* ).</span></span> <span data-ttu-id="c702a-471">Die Assembly fügt der App zum Start zwei Middlewares hinzu, die Diagnoseinformationen zu folgenden Komponenten bereitstellen:</span><span class="sxs-lookup"><span data-stu-id="c702a-471">The assembly adds two middlewares to the app at startup that provide diagnostic information on:</span></span>
  * <span data-ttu-id="c702a-472">Registrierte Dienste</span><span class="sxs-lookup"><span data-stu-id="c702a-472">Registered services</span></span>
  * <span data-ttu-id="c702a-473">Adresse (Schema, Host, Basispfad, Pfad, Abfragezeichenfolge)</span><span class="sxs-lookup"><span data-stu-id="c702a-473">Address (scheme, host, path base, path, query string)</span></span>
  * <span data-ttu-id="c702a-474">Verbindung (Remote-IP, Remoteport, lokale IP, lokaler Port, Clientzertifikat)</span><span class="sxs-lookup"><span data-stu-id="c702a-474">Connection (remote IP, remote port, local IP, local port, client certificate)</span></span>
  * <span data-ttu-id="c702a-475">Anforderungsheader</span><span class="sxs-lookup"><span data-stu-id="c702a-475">Request headers</span></span>
  * <span data-ttu-id="c702a-476">Umgebungsvariablen</span><span class="sxs-lookup"><span data-stu-id="c702a-476">Environment variables</span></span>

<span data-ttu-id="c702a-477">So führen Sie das Beispiel aus:</span><span class="sxs-lookup"><span data-stu-id="c702a-477">To run the sample:</span></span>

<span data-ttu-id="c702a-478">**Aktivierung über ein NuGet-Paket**</span><span class="sxs-lookup"><span data-stu-id="c702a-478">**Activation from a NuGet package**</span></span>

1. <span data-ttu-id="c702a-479">Kompilieren Sie das Paket *HostingStartupPackage* mit dem Befehl [dotnet pack](/dotnet/core/tools/dotnet-pack).</span><span class="sxs-lookup"><span data-stu-id="c702a-479">Compile the *HostingStartupPackage* package with the [dotnet pack](/dotnet/core/tools/dotnet-pack) command.</span></span>
1. <span data-ttu-id="c702a-480">Fügen Sie den Assemblynamen des Pakets *HostingStartupPackage* zur Umgebungsvariablen `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` hinzu.</span><span class="sxs-lookup"><span data-stu-id="c702a-480">Add the package's assembly name of the *HostingStartupPackage* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
1. <span data-ttu-id="c702a-481">Kompilieren Sie die App, und führen Sie diese aus.</span><span class="sxs-lookup"><span data-stu-id="c702a-481">Compile and run the app.</span></span> <span data-ttu-id="c702a-482">In der erweiterten App ist ein Paketverweis vorhanden (Kompilierzeitverweis).</span><span class="sxs-lookup"><span data-stu-id="c702a-482">A package reference is present in the enhanced app (a compile-time reference).</span></span> <span data-ttu-id="c702a-483">Eine `<PropertyGroup>` in der Projektdatei der App gibt die Ausgabe des Paketprojekts ( *../HostingStartupPackage/bin/Debug* ) als Paketquelle an.</span><span class="sxs-lookup"><span data-stu-id="c702a-483">A `<PropertyGroup>` in the app's project file specifies the package project's output ( *../HostingStartupPackage/bin/Debug* ) as a package source.</span></span> <span data-ttu-id="c702a-484">Dadurch kann die Anwendung das Paket verwenden, ohne das Paket in [nuget.org](https://www.nuget.org/) hochzuladen. Weitere Informationen finden Sie in den Hinweisen in der Projektdatei von HostingStartupApp.</span><span class="sxs-lookup"><span data-stu-id="c702a-484">This allows the app to use the package without uploading the package to [nuget.org](https://www.nuget.org/). For more information, see the notes in the HostingStartupApp's project file.</span></span>

   ```xml
   <PropertyGroup>
     <RestoreSources>$(RestoreSources);https://api.nuget.org/v3/index.json;../HostingStartupPackage/bin/Debug</RestoreSources>
   </PropertyGroup>
   ```

1. <span data-ttu-id="c702a-485">Die von der Indexseite gerenderten Schlüsselwerte der Dienstkonfiguration entsprechen den durch die `ServiceKeyInjection.Configure`-Methode des Pakets festgelegten Werten.</span><span class="sxs-lookup"><span data-stu-id="c702a-485">Observe that the service configuration key values rendered by the Index page match the values set by the package's `ServiceKeyInjection.Configure` method.</span></span>

<span data-ttu-id="c702a-486">Wenn Sie am Projekt *HostingStartupPackage* Änderungen vornehmen und das Projekt erneut kompilieren, bereinigen Sie die lokalen NuGet-Paketcaches, um sicherzustellen, dass die *HostingStartupApp* das aktualisierte Paket erhält und kein veraltetes Paket aus dem lokalen Cache.</span><span class="sxs-lookup"><span data-stu-id="c702a-486">If you make changes to the *HostingStartupPackage* project and recompile it, clear the local NuGet package caches to ensure that the *HostingStartupApp* receives the updated package and not a stale package from the local cache.</span></span> <span data-ttu-id="c702a-487">Um die lokalen NuGet-Caches zu bereinigen, führen Sie den folgenden [dotnet nuget locals](/dotnet/core/tools/dotnet-nuget-locals)-Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="c702a-487">To clear the local NuGet caches, execute the following [dotnet nuget locals](/dotnet/core/tools/dotnet-nuget-locals) command:</span></span>

```dotnetcli
dotnet nuget locals all --clear
```

<span data-ttu-id="c702a-488">**Aktivierung über eine Klassenbibliothek**</span><span class="sxs-lookup"><span data-stu-id="c702a-488">**Activation from a class library**</span></span>

1. <span data-ttu-id="c702a-489">Kompilieren Sie die Klassenbibliothek *HostingStartupLibrary* mit dem Befehl [dotnet build](/dotnet/core/tools/dotnet-build).</span><span class="sxs-lookup"><span data-stu-id="c702a-489">Compile the *HostingStartupLibrary* class library with the [dotnet build](/dotnet/core/tools/dotnet-build) command.</span></span>
1. <span data-ttu-id="c702a-490">Fügen Sie der Umgebungsvariablen `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` den Assemblynamen *HostingStartupLibrary* der Klassenbibliothek hinzu.</span><span class="sxs-lookup"><span data-stu-id="c702a-490">Add the class library's assembly name of *HostingStartupLibrary* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
1. <span data-ttu-id="c702a-491">Stellen Sie die Assembly der Klassenbibliothek mittels *bin* für die App bereit, indem Sie die Datei *HostingStartupLibrary.dll* aus der kompilierten Ausgabe der Klassenbibliothek in den Ordner *bin/Debug* der App kopieren.</span><span class="sxs-lookup"><span data-stu-id="c702a-491">*bin* -deploy the class library's assembly to the app by copying the *HostingStartupLibrary.dll* file from the class library's compiled output to the app's *bin/Debug* folder.</span></span>
1. <span data-ttu-id="c702a-492">Kompilieren Sie die App, und führen Sie diese aus.</span><span class="sxs-lookup"><span data-stu-id="c702a-492">Compile and run the app.</span></span> <span data-ttu-id="c702a-493">Eine `<ItemGroup>` in der Projektdatei der App verweist auf die Assembly der Klassenbibliothek ( *.\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll* ) (Kompilierzeitverweis).</span><span class="sxs-lookup"><span data-stu-id="c702a-493">An `<ItemGroup>` in the app's project file references the class library's assembly ( *.\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll* ) (a compile-time reference).</span></span> <span data-ttu-id="c702a-494">Weitere Informationen finden Sie in den Hinweisen in der Projektdatei von HostingStartupApp.</span><span class="sxs-lookup"><span data-stu-id="c702a-494">For more information, see the notes in the HostingStartupApp's project file.</span></span>

   ```xml
   <ItemGroup>
     <Reference Include=".\\bin\\Debug\\netcoreapp2.1\\HostingStartupLibrary.dll">
       <HintPath>.\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll</HintPath>
       <SpecificVersion>False</SpecificVersion>
     </Reference>
   </ItemGroup>
   ```

1. <span data-ttu-id="c702a-495">Die von der Indexseite gerenderten Schlüsselwerte der Dienstkonfiguration entsprechen den durch die `ServiceKeyInjection.Configure`-Methode der Klassenbibliothek festgelegten Werten.</span><span class="sxs-lookup"><span data-stu-id="c702a-495">Observe that the service configuration key values rendered by the Index page match the values set by the class library's `ServiceKeyInjection.Configure` method.</span></span>

<span data-ttu-id="c702a-496">**Aktivierung über eine mittels Laufzeitspeicher bereitgestellten Assembly**</span><span class="sxs-lookup"><span data-stu-id="c702a-496">**Activation from a runtime store-deployed assembly**</span></span>

1. <span data-ttu-id="c702a-497">Im Projekt *StartupDiagnostics* wird [PowerShell](/powershell/scripting/powershell-scripting) verwendet, um die Datei *StartupDiagnostics.deps.json* zu bearbeiten.</span><span class="sxs-lookup"><span data-stu-id="c702a-497">The *StartupDiagnostics* project uses [PowerShell](/powershell/scripting/powershell-scripting) to modify its *StartupDiagnostics.deps.json* file.</span></span> <span data-ttu-id="c702a-498">PowerShell ist standardmäßig auf Windows-Betriebssystemen ab Windows 7 SP1 und Windows Server 2008 R2 SP1 installiert.</span><span class="sxs-lookup"><span data-stu-id="c702a-498">PowerShell is installed by default on Windows starting with Windows 7 SP1 and Windows Server 2008 R2 SP1.</span></span> <span data-ttu-id="c702a-499">Im Artikel [Installieren verschiedener Versionen von PowerShell](/powershell/scripting/install/installing-powershell) erfahren Sie, wie Sie PowerShell auf anderen Plattformen erwerben können.</span><span class="sxs-lookup"><span data-stu-id="c702a-499">To obtain PowerShell on other platforms, see [Installing various versions of PowerShell](/powershell/scripting/install/installing-powershell).</span></span>
1. <span data-ttu-id="c702a-500">Führen Sie das Skript *build.ps1* im Ordner *RuntimeStore* aus.</span><span class="sxs-lookup"><span data-stu-id="c702a-500">Execute the *build.ps1* script in the *RuntimeStore* folder.</span></span> <span data-ttu-id="c702a-501">Das Skript:</span><span class="sxs-lookup"><span data-stu-id="c702a-501">The script:</span></span>
   * <span data-ttu-id="c702a-502">Generiert das `StartupDiagnostics`-Paket im Ordner *obj\packages* .</span><span class="sxs-lookup"><span data-stu-id="c702a-502">Generates the `StartupDiagnostics` package in the *obj\packages* folder.</span></span>
   * <span data-ttu-id="c702a-503">Generiert den Laufzeitspeicher für `StartupDiagnostics` im Ordner *store*.</span><span class="sxs-lookup"><span data-stu-id="c702a-503">Generates the runtime store for `StartupDiagnostics` in the *store* folder.</span></span> <span data-ttu-id="c702a-504">Der `dotnet store`-Befehl im Skript verwendet den [Laufzeitbezeichner (RID)](/dotnet/core/rid-catalog) `win7-x64` für einen für Windows bereitgestellten Hostingstart.</span><span class="sxs-lookup"><span data-stu-id="c702a-504">The `dotnet store` command in the script uses the `win7-x64` [runtime identifier (RID)](/dotnet/core/rid-catalog) for a hosting startup deployed to Windows.</span></span> <span data-ttu-id="c702a-505">Wenn der Hostingstart für eine andere Laufzeit bereitgestellt wird, muss in Zeile 37 die entsprechende RID eingegeben werden.</span><span class="sxs-lookup"><span data-stu-id="c702a-505">When providing the hosting startup for a different runtime, substitute the correct RID on line 37 of the script.</span></span> <span data-ttu-id="c702a-506">Der Runtimespeicher für `StartupDiagnostics` würde später in den Runtimespeicher des Benutzers oder Systems auf dem Computer verschoben, auf dem die Assembly verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="c702a-506">The runtime store for `StartupDiagnostics` would later be moved to the user's or system's runtime store on the machine where the assembly will be consumed.</span></span> <span data-ttu-id="c702a-507">Der Installationsspeicherort für den Benutzerruntimespeicher für die `StartupDiagnostics`-Assembly ist *.dotnet/store/x64/netcoreapp2.2/startupdiagnostics/1.0.0/lib/netcoreapp2.2/StartupDiagnostics.dll*.</span><span class="sxs-lookup"><span data-stu-id="c702a-507">The user runtime store install location for the `StartupDiagnostics` assembly is *.dotnet/store/x64/netcoreapp2.2/startupdiagnostics/1.0.0/lib/netcoreapp2.2/StartupDiagnostics.dll*.</span></span>
   * <span data-ttu-id="c702a-508">Generiert die `additionalDeps` für `StartupDiagnostics` im Ordner *additionalDeps* .</span><span class="sxs-lookup"><span data-stu-id="c702a-508">Generates the `additionalDeps` for `StartupDiagnostics` in the *additionalDeps* folder.</span></span> <span data-ttu-id="c702a-509">Die zusätzlichen Abhängigkeiten würden später in die zusätzlichen Abhängigkeiten des Benutzers oder Systems verschoben.</span><span class="sxs-lookup"><span data-stu-id="c702a-509">The additional dependencies would later be moved to the user's or system's additional dependencies.</span></span> <span data-ttu-id="c702a-510">Der Installationsspeicherort der zusätzlichen Abhängigkeiten des Benutzers `StartupDiagnostics` ist *.dotnet/x64/additionalDeps/StartupDiagnostics/shared/Microsoft.NETCore.App/2.2.0/StartupDiagnostics.deps.json*.</span><span class="sxs-lookup"><span data-stu-id="c702a-510">The user `StartupDiagnostics` additional dependencies install location is *.dotnet/x64/additionalDeps/StartupDiagnostics/shared/Microsoft.NETCore.App/2.2.0/StartupDiagnostics.deps.json*.</span></span>
   * <span data-ttu-id="c702a-511">Legt die Datei *deploy.ps1* im Ordner *deployment* ab.</span><span class="sxs-lookup"><span data-stu-id="c702a-511">Places the *deploy.ps1* file in the *deployment* folder.</span></span>
1. <span data-ttu-id="c702a-512">Führen Sie das Skript *deploy.ps1* im Ordner *deployment* aus.</span><span class="sxs-lookup"><span data-stu-id="c702a-512">Run the *deploy.ps1* script in the *deployment* folder.</span></span> <span data-ttu-id="c702a-513">Das Skript fügt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="c702a-513">The script appends:</span></span>
   * <span data-ttu-id="c702a-514">`StartupDiagnostics` an die Umgebungsvariable `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.</span><span class="sxs-lookup"><span data-stu-id="c702a-514">`StartupDiagnostics` to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
   * <span data-ttu-id="c702a-515">Der Pfad der Hostingstartabhängigkeiten (im Ordner *deployment* des RuntimeStore-Projekts) zur `DOTNET_ADDITIONAL_DEPS`-Umgebungsvariablen.</span><span class="sxs-lookup"><span data-stu-id="c702a-515">The hosting startup dependencies path (in the RuntimeStore project's *deployment* folder) to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>
   * <span data-ttu-id="c702a-516">Der Pfad des Runtimespeichers (im Ordner *deployment* des RuntimeStore-Projekts) zur `DOTNET_SHARED_STORE`-Umgebungsvariablen.</span><span class="sxs-lookup"><span data-stu-id="c702a-516">The runtime store path (in the RuntimeStore project's *deployment* folder) to the `DOTNET_SHARED_STORE` environment variable.</span></span>
1. <span data-ttu-id="c702a-517">Führen Sie die Beispiel-App aus.</span><span class="sxs-lookup"><span data-stu-id="c702a-517">Run the sample app.</span></span>
1. <span data-ttu-id="c702a-518">Fordern Sie den Endpunkt `/services` an, um die registrierten Dienste der App anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="c702a-518">Request the `/services` endpoint to see the app's registered services.</span></span> <span data-ttu-id="c702a-519">Fordern Sie den Endpunkt `/diag` an, um Diagnoseinformationen anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="c702a-519">Request the `/diag` endpoint to see the diagnostic information.</span></span>

::: moniker-end
