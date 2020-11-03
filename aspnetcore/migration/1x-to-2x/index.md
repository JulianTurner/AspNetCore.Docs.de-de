---
title: Migrieren von ASP.NET Core 1.x zu 2.0
author: scottaddie
description: In diesem Artikel werden die Voraussetzungen und üblichen Schritte zum Migrieren eines ASP.NET Core 1.x-Projekts zu ASP.NET Core 2.0 behandelt.
ms.author: scaddie
ms.custom: mvc
ms.date: 12/05/2019
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
uid: migration/1x-to-2x/index
ms.openlocfilehash: 6d67924d87cdbe72cb08c5305dfe45c5b22b31bc
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93057114"
---
# <a name="migrate-from-aspnet-core-1x-to-20"></a><span data-ttu-id="b2217-103">Migrieren von ASP.NET Core 1.x zu 2.0</span><span class="sxs-lookup"><span data-stu-id="b2217-103">Migrate from ASP.NET Core 1.x to 2.0</span></span>

<span data-ttu-id="b2217-104">Von [Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="b2217-104">By [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="b2217-105">In diesem Artikel aktualisieren wir exemplarisch ein vorhandenes ASP.NET Core 1.x-Projekt auf ASP.NET Core 2.0.</span><span class="sxs-lookup"><span data-stu-id="b2217-105">In this article, we walk you through updating an existing ASP.NET Core 1.x project to ASP.NET Core 2.0.</span></span> <span data-ttu-id="b2217-106">Durch Migrieren der Anwendung zu ASP.NET Core 2.0 kommen Sie in den Genuss [vieler neuer Funktionen und Leistungsverbesserungen](xref:aspnetcore-2.0).</span><span class="sxs-lookup"><span data-stu-id="b2217-106">Migrating your application to ASP.NET Core 2.0 enables you to take advantage of [many new features and performance improvements](xref:aspnetcore-2.0).</span></span>

<span data-ttu-id="b2217-107">Vorhandene ASP.NET Core 1.x-Anwendungen basieren auf versionsspezifischen Projektvorlagen.</span><span class="sxs-lookup"><span data-stu-id="b2217-107">Existing ASP.NET Core 1.x applications are based off of version-specific project templates.</span></span> <span data-ttu-id="b2217-108">Doch das ASP.NET Core-Framework entwickelt sich weiter. Gleiches gilt für die darin enthaltenen Projektvorlagen und den Startercode.</span><span class="sxs-lookup"><span data-stu-id="b2217-108">As the ASP.NET Core framework evolves, so do the project templates and the starter code contained within them.</span></span> <span data-ttu-id="b2217-109">Zusätzlich zum Aktualisieren des ASP.NET Core-Frameworks müssen Sie den Code für Ihre Anwendung aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="b2217-109">In addition to updating the ASP.NET Core framework, you need to update the code for your application.</span></span>

<a name="prerequisites"></a>

## <a name="prerequisites"></a><span data-ttu-id="b2217-110">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="b2217-110">Prerequisites</span></span>

<span data-ttu-id="b2217-111">Weitere Informationen finden Sie unter [Erste Schritte mit ASP.NET Core](xref:getting-started).</span><span class="sxs-lookup"><span data-stu-id="b2217-111">See [Get Started with ASP.NET Core](xref:getting-started).</span></span>

<a name="tfm"></a>

## <a name="update-target-framework-moniker-tfm"></a><span data-ttu-id="b2217-112">Aktualisieren des Zielframeworkmonikers (Target Framework Moniker, TFM)</span><span class="sxs-lookup"><span data-stu-id="b2217-112">Update Target Framework Moniker (TFM)</span></span>

<span data-ttu-id="b2217-113">Auf .NET Core ausgelegte Projekte müssen den [TFM](/dotnet/standard/frameworks) einer Version größer gleich .NET Core 2.0 verwenden.</span><span class="sxs-lookup"><span data-stu-id="b2217-113">Projects targeting .NET Core should use the [TFM](/dotnet/standard/frameworks) of a version greater than or equal to .NET Core 2.0.</span></span> <span data-ttu-id="b2217-114">Suchen Sie den Knoten `<TargetFramework>` in der *CSPROJ* -Datei, und ersetzen Sie dessen inneren Text durch `netcoreapp2.0`:</span><span class="sxs-lookup"><span data-stu-id="b2217-114">Search for the `<TargetFramework>` node in the *.csproj* file, and replace its inner text with `netcoreapp2.0`:</span></span>

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App.csproj?range=3)]

<span data-ttu-id="b2217-115">Auf .NET Framework ausgelegte Projekte müssen den TFM einer Version größer gleich .NET Framework 4.6.1 verwenden.</span><span class="sxs-lookup"><span data-stu-id="b2217-115">Projects targeting .NET Framework should use the TFM of a version greater than or equal to .NET Framework 4.6.1.</span></span> <span data-ttu-id="b2217-116">Suchen Sie den Knoten `<TargetFramework>` in der *CSPROJ* -Datei, und ersetzen Sie dessen inneren Text durch `net461`:</span><span class="sxs-lookup"><span data-stu-id="b2217-116">Search for the `<TargetFramework>` node in the *.csproj* file, and replace its inner text with `net461`:</span></span>

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App.csproj?range=4)]

> [!NOTE]
> <span data-ttu-id="b2217-117">.NET Core 2.0 bietet eine viel größere Oberfläche als .NET Core 1.x.</span><span class="sxs-lookup"><span data-stu-id="b2217-117">.NET Core 2.0 offers a much larger surface area than .NET Core 1.x.</span></span> <span data-ttu-id="b2217-118">Wenn Sie mit .NET Framework entwickeln, nur weil APIs in .NET Core 1.x fehlen, wird das Entwickeln mit .NET Core 2.0 wahrscheinlich funktionieren.</span><span class="sxs-lookup"><span data-stu-id="b2217-118">If you're targeting .NET Framework solely because of missing APIs in .NET Core 1.x, targeting .NET Core 2.0 is likely to work.</span></span>

<span data-ttu-id="b2217-119">Wenn die Projektdatei `<RuntimeFrameworkVersion>1.{sub-version}</RuntimeFrameworkVersion>` enthält, lesen Sie die Informationen zu [diesem GitHub-Problem](https://github.com/dotnet/AspNetCore/issues/3221#issuecomment-413094268).</span><span class="sxs-lookup"><span data-stu-id="b2217-119">If the project file contains `<RuntimeFrameworkVersion>1.{sub-version}</RuntimeFrameworkVersion>`, see [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/3221#issuecomment-413094268).</span></span>

<a name="global-json"></a>

## <a name="update-net-core-sdk-version-in-globaljson"></a><span data-ttu-id="b2217-120">Aktualisieren der .NET Core SDK-Version in „global.json“</span><span class="sxs-lookup"><span data-stu-id="b2217-120">Update .NET Core SDK version in global.json</span></span>

<span data-ttu-id="b2217-121">Wenn Ihre Projektmappe von einer Datei [global.json](/dotnet/core/tools/global-json) abhängig ist, um eine bestimmte .NET Core SDK-Version als Ziel anzugeben, aktualisieren Sie deren `version`-Eigenschaft so, dass die auf Ihrem Computer installierte Version 2.0 verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="b2217-121">If your solution relies upon a [global.json](/dotnet/core/tools/global-json) file to target a specific .NET Core SDK version, update its `version` property to use the 2.0 version installed on your machine:</span></span>

[!code-json[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/global.json?highlight=3)]

<a name="package-reference"></a>

## <a name="update-package-references"></a><span data-ttu-id="b2217-122">Aktualisieren von Paketverweisen</span><span class="sxs-lookup"><span data-stu-id="b2217-122">Update package references</span></span>

<span data-ttu-id="b2217-123">In der *CSPROJ* -Datei in einem Projekt der Version 1.x sind alle NuGet-Pakete aufgeführt, die vom Projekt verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="b2217-123">The *.csproj* file in a 1.x project lists each NuGet package used by the project.</span></span>

<span data-ttu-id="b2217-124">In einem ASP.NET Core 2.0-Projekt für .NET Core 2.0 ersetzt ein einzelner Verweis des Typs [metapackage](xref:fundamentals/metapackage) in der *CSPROJ* -Datei die Paketsammlung:</span><span class="sxs-lookup"><span data-stu-id="b2217-124">In an ASP.NET Core 2.0 project targeting .NET Core 2.0, a single [metapackage](xref:fundamentals/metapackage) reference in the *.csproj* file replaces the collection of packages:</span></span>

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App.csproj?range=8-10)]

<span data-ttu-id="b2217-125">Alle Funktionen von ASP.NET Core 2.0 und Entity Framework Core 2.0 sind in „metapackage“ enthalten.</span><span class="sxs-lookup"><span data-stu-id="b2217-125">All the features of ASP.NET Core 2.0 and Entity Framework Core 2.0 are included in the metapackage.</span></span>

<span data-ttu-id="b2217-126">ASP.NET Core 2.0-Projekte für .NET Framework müssen weiterhin auf einzelne NuGet-Pakete verweisen.</span><span class="sxs-lookup"><span data-stu-id="b2217-126">ASP.NET Core 2.0 projects targeting .NET Framework should continue to reference individual NuGet packages.</span></span> <span data-ttu-id="b2217-127">Aktualisieren Sie das `Version`-Attribut aller Knoten des Typs `<PackageReference />` auf 2.0.0.</span><span class="sxs-lookup"><span data-stu-id="b2217-127">Update the `Version` attribute of each `<PackageReference />` node to 2.0.0.</span></span>

<span data-ttu-id="b2217-128">Hier ist z.B. die Liste der `<PackageReference />`-Knoten in einem typischen ASP.NET Core 2.0-Projekt für .NET Framework:</span><span class="sxs-lookup"><span data-stu-id="b2217-128">For example, here's the list of `<PackageReference />` nodes used in a typical ASP.NET Core 2.0 project targeting .NET Framework:</span></span>

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App.csproj?range=9-22)]

<a name="dot-net-cli-tool-reference"></a>

## <a name="update-net-core-cli-tools"></a><span data-ttu-id="b2217-129">Aktualisieren von .NET Core CLI-Tools</span><span class="sxs-lookup"><span data-stu-id="b2217-129">Update .NET Core CLI tools</span></span>

<span data-ttu-id="b2217-130">Aktualisieren Sie in der *CSPROJ* -Datei das `Version`-Attribut jedes `<DotNetCliToolReference />`-Knotens auf 2.0.0.</span><span class="sxs-lookup"><span data-stu-id="b2217-130">In the *.csproj* file, update the `Version` attribute of each `<DotNetCliToolReference />` node to 2.0.0.</span></span>

<span data-ttu-id="b2217-131">Hier ist z.B. die Liste der CLI-Tools in einem typischen ASP.NET Core 2.0-Projekt für .NET Core 2.0:</span><span class="sxs-lookup"><span data-stu-id="b2217-131">For example, here's the list of CLI tools used in a typical ASP.NET Core 2.0 project targeting .NET Core 2.0:</span></span>

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App.csproj?range=12-16)]

<a name="package-target-fallback"></a>

## <a name="rename-package-target-fallback-property"></a><span data-ttu-id="b2217-132">Umbenennen der „TargetFallback“-Eigenschaft des Pakets</span><span class="sxs-lookup"><span data-stu-id="b2217-132">Rename Package Target Fallback property</span></span>

<span data-ttu-id="b2217-133">Die *CSPROJ* -Datei eines 1.x-Projekts hat einen Knoten des Typs `PackageTargetFallback` und eine Variable verwendet:</span><span class="sxs-lookup"><span data-stu-id="b2217-133">The *.csproj* file of a 1.x project used a `PackageTargetFallback` node and variable:</span></span>

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App.csproj?range=5)]

<span data-ttu-id="b2217-134">Benennen Sie den Knoten und die Variable in `AssetTargetFallback` um:</span><span class="sxs-lookup"><span data-stu-id="b2217-134">Rename both the node and variable to `AssetTargetFallback`:</span></span>

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App.csproj?range=4)]

<a name="program-cs"></a>

## <a name="update-main-method-in-programcs"></a><span data-ttu-id="b2217-135">Aktualisieren der „Main“-Methode in „Program.cs“</span><span class="sxs-lookup"><span data-stu-id="b2217-135">Update Main method in Program.cs</span></span>

<span data-ttu-id="b2217-136">In 1.x-Projekten sah die `Main`-Methode von *Program.cs* wie folgt aus:</span><span class="sxs-lookup"><span data-stu-id="b2217-136">In 1.x projects, the `Main` method of *Program.cs* looked like this:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Program.cs?name=snippet_ProgramCs&highlight=8-19)]

<span data-ttu-id="b2217-137">In 2.0-Projekten wurde die `Main`-Methode von *Program.cs* vereinfacht:</span><span class="sxs-lookup"><span data-stu-id="b2217-137">In 2.0 projects, the `Main` method of *Program.cs* has been simplified:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Program.cs?highlight=8-11)]

<span data-ttu-id="b2217-138">Das Übernehmen dieses neuen 2.0-Musters wird dringend empfohlen und ist für Produktfunktionen wie [Entity Framework Core-Migrationen (EF)](xref:data/ef-mvc/migrations) erforderlich.</span><span class="sxs-lookup"><span data-stu-id="b2217-138">The adoption of this new 2.0 pattern is highly recommended and is required for product features like [Entity Framework (EF) Core Migrations](xref:data/ef-mvc/migrations) to work.</span></span> <span data-ttu-id="b2217-139">Bei Ausführung von `Update-Database` im Paket-Manager-Konsolenfenster oder von `dotnet ef database update` an der Befehlszeile (für in ASP.NET Core 2.0 konvertierte Projekte) wird der folgende Fehler generiert:</span><span class="sxs-lookup"><span data-stu-id="b2217-139">For example, running `Update-Database` from the Package Manager Console window or `dotnet ef database update` from the command line (on projects converted to ASP.NET Core 2.0) generates the following error:</span></span>

```
Unable to create an object of type '<Context>'. Add an implementation of 'IDesignTimeDbContextFactory<Context>' to the project, or see https://go.microsoft.com/fwlink/?linkid=851728 for additional patterns supported at design time.
```

<a name="add-modify-configuration"></a>

## <a name="add-configuration-providers"></a><span data-ttu-id="b2217-140">Hinzufügen von Konfigurationsanbietern</span><span class="sxs-lookup"><span data-stu-id="b2217-140">Add configuration providers</span></span>

<span data-ttu-id="b2217-141">In 1.x-Projekten konnten Sie Konfigurationsanbieter einer App mit dem `Startup`-Konstruktor hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="b2217-141">In 1.x projects, adding configuration providers to an app was accomplished via the `Startup` constructor.</span></span> <span data-ttu-id="b2217-142">Dazu mussten Sie eine Instanz von `ConfigurationBuilder` erstellen, die betreffenden Anbieter laden (Umgebungsvariablen, App-Einstellungen usw.) und einen Member von `IConfigurationRoot` initialisieren.</span><span class="sxs-lookup"><span data-stu-id="b2217-142">The steps involved creating an instance of `ConfigurationBuilder`, loading applicable providers (environment variables, app settings, etc.), and initializing a member of `IConfigurationRoot`.</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Startup.cs?name=snippet_1xStartup)]

<span data-ttu-id="b2217-143">Im vorherigen Beispiel wurde der `Configuration`-Member mit Konfigurationseinstellen aus *:::no-loc(appsettings.json):::* geladen sowie aus jeder anderen *appsettings.\<EnvironmentName\>.jso* n-Datei, die mit der Eigenschaft `IHostingEnvironment.EnvironmentName` übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="b2217-143">The preceding example loads the `Configuration` member with configuration settings from *:::no-loc(appsettings.json):::* as well as any *appsettings.\<EnvironmentName\>.json* file matching the `IHostingEnvironment.EnvironmentName` property.</span></span> <span data-ttu-id="b2217-144">Diese Dateien befinden sich am gleichen Speicherort wie *startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="b2217-144">The location of these files is at the same path as *Startup.cs*.</span></span>

<span data-ttu-id="b2217-145">In 2.0-Projekten wird der Bausteinkonfigurationsknoten, der 1.x-Projekten eigen ist, im Hintergrund ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="b2217-145">In 2.0 projects, the boilerplate configuration code inherent to 1.x projects runs behind-the-scenes.</span></span> <span data-ttu-id="b2217-146">Umgebungsvariablen und App-Einstellungen werden beispielsweise beim Start geladen.</span><span class="sxs-lookup"><span data-stu-id="b2217-146">For example, environment variables and app settings are loaded at startup.</span></span> <span data-ttu-id="b2217-147">Der entsprechende Code *startup.cs* wird zur `IConfiguration`-Initialisierung mit der eingefügten Instanz reduziert:</span><span class="sxs-lookup"><span data-stu-id="b2217-147">The equivalent *Startup.cs* code is reduced to `IConfiguration` initialization with the injected instance:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/Startup.cs?name=snippet_2xStartup)]

<span data-ttu-id="b2217-148">Um die von `WebHostBuilder.CreateDefaultBuilder` hinzugefügten Standardanbieter zu entfernen, rufen Sie die `Clear`-Methode in der `IConfigurationBuilder.Sources`-Eigenschaft in `ConfigureAppConfiguration` auf.</span><span class="sxs-lookup"><span data-stu-id="b2217-148">To remove the default providers added by `WebHostBuilder.CreateDefaultBuilder`, invoke the `Clear` method on the `IConfigurationBuilder.Sources` property inside of `ConfigureAppConfiguration`.</span></span> <span data-ttu-id="b2217-149">Um Anbieter wieder hinzuzufügen, verwenden Sie die `ConfigureAppConfiguration`-Methode in *program.cs* :</span><span class="sxs-lookup"><span data-stu-id="b2217-149">To add providers back, utilize the `ConfigureAppConfiguration` method in *Program.cs* :</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/Program.cs?name=snippet_ProgramMainConfigProviders&highlight=9-14)]

<span data-ttu-id="b2217-150">Die von der `CreateDefaultBuilder`-Methode verwendete Konfiguration im vorherigen Codeausschnitt können Sie sich [hier](https://github.com/aspnet/MetaPackages/blob/rel/2.0.0/src/Microsoft.AspNetCore/WebHost.cs#L152) ansehen.</span><span class="sxs-lookup"><span data-stu-id="b2217-150">The configuration used by the `CreateDefaultBuilder` method in the preceding code snippet can be seen [here](https://github.com/aspnet/MetaPackages/blob/rel/2.0.0/src/Microsoft.AspNetCore/WebHost.cs#L152).</span></span>

<span data-ttu-id="b2217-151">Weitere Informationen finden Sie unter [Konfiguration in ASP.NET Core](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="b2217-151">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index).</span></span>

<a name="db-init-code"></a>

## <a name="move-database-initialization-code"></a><span data-ttu-id="b2217-152">Verschieben des Initialisierungscodes der Datenbank</span><span class="sxs-lookup"><span data-stu-id="b2217-152">Move database initialization code</span></span>

<span data-ttu-id="b2217-153">In 1.x-Projekten, die EF Core 1.x verwenden, bewirkt ein Befehl wie `dotnet ef migrations add` Folgendes:</span><span class="sxs-lookup"><span data-stu-id="b2217-153">In 1.x projects using EF Core 1.x, a command such as `dotnet ef migrations add` does the following:</span></span>

1. <span data-ttu-id="b2217-154">Instanziiert eine `Startup`-Instanz</span><span class="sxs-lookup"><span data-stu-id="b2217-154">Instantiates a `Startup` instance</span></span>
1. <span data-ttu-id="b2217-155">Ruft die `ConfigureServices`-Methode auf, um alle Dienste mit Abhängigkeitsinjektion (einschließlich `DbContext`-Typen zu registrieren</span><span class="sxs-lookup"><span data-stu-id="b2217-155">Invokes the `ConfigureServices` method to register all services with dependency injection (including `DbContext` types)</span></span>
1. <span data-ttu-id="b2217-156">Führt die erforderlichen Aufgaben aus</span><span class="sxs-lookup"><span data-stu-id="b2217-156">Performs its requisite tasks</span></span>

<span data-ttu-id="b2217-157">In 2.0-Projekten, die EF Core 2.0 verwenden, wird `Program.BuildWebHost` aufgerufen, um die Anwendungsdienste abzurufen.</span><span class="sxs-lookup"><span data-stu-id="b2217-157">In 2.0 projects using EF Core 2.0, `Program.BuildWebHost` is invoked to obtain the application services.</span></span> <span data-ttu-id="b2217-158">Im Gegensatz zu 1.x hat dies den zusätzlichen Nebeneffekt, dass `Startup.Configure` aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="b2217-158">Unlike 1.x, this has the additional side effect of invoking `Startup.Configure`.</span></span> <span data-ttu-id="b2217-159">Wenn Ihre 1.x-App den Datenbankinitialisierungscode in der `Configure`-Methode aufgerufen hat, können unerwartete Probleme auftreten.</span><span class="sxs-lookup"><span data-stu-id="b2217-159">If your 1.x app invoked database initialization code in its `Configure` method, unexpected problems can occur.</span></span> <span data-ttu-id="b2217-160">Wenn die Datenbank beispielsweise noch nicht existiert, wird der Seedingcode vor der Befehlsausführung der EF Core-Migration ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="b2217-160">For example, if the database doesn't yet exist, the seeding code runs before the EF Core Migrations command execution.</span></span> <span data-ttu-id="b2217-161">Dieses Problem führt dazu, dass ein `dotnet ef migrations list`-Befehl fehlschlägt, da die Datenbank noch nicht vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="b2217-161">This problem causes a `dotnet ef migrations list` command to fail if the database doesn't yet exist.</span></span>

<span data-ttu-id="b2217-162">Erwägen Sie den folgenden 1.x-Startcode der Initialisierung in der `Configure`-Methode von *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="b2217-162">Consider the following 1.x seed initialization code in the `Configure` method of *Startup.cs* :</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Startup.cs?name=snippet_ConfigureSeedData&highlight=8)]

<span data-ttu-id="b2217-163">Verschieben Sie in 2.0-Projekten den Aufruf `SeedData.Initialize` zur `Main`-Methode von *Program.cs* :</span><span class="sxs-lookup"><span data-stu-id="b2217-163">In 2.0 projects, move the `SeedData.Initialize` call to the `Main` method of *Program.cs* :</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Program2.cs?name=snippet_Main2Code&highlight=10)]

<span data-ttu-id="b2217-164">Ab 2.0 ist es keine gute Idee, etwas in `BuildWebHost` zu tun, außer den Webhost zu erstellen und zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="b2217-164">As of 2.0, it's bad practice to do anything in `BuildWebHost` except build and configure the web host.</span></span> <span data-ttu-id="b2217-165">Alles, was in der Anwendung ausgeführt werden soll, sollte außerhalb von `BuildWebHost` &mdash; behandelt werden, in der Regel in der `Main`-Methode von *Program.cs*.</span><span class="sxs-lookup"><span data-stu-id="b2217-165">Anything that's about running the application should be handled outside of `BuildWebHost` &mdash; typically in the `Main` method of *Program.cs*.</span></span>

<a name="view-compilation"></a>

## <a name="review-no-locrazor-view-compilation-setting"></a><span data-ttu-id="b2217-166">Überprüfen der Einstellungen für die Kompilierung der :::no-loc(Razor):::-Ansicht</span><span class="sxs-lookup"><span data-stu-id="b2217-166">Review :::no-loc(Razor)::: view compilation setting</span></span>

<span data-ttu-id="b2217-167">Eine schnellere Anwendungsstartzeit und kleinere veröffentlichte Pakete sind für Sie von höchster Wichtigkeit.</span><span class="sxs-lookup"><span data-stu-id="b2217-167">Faster application startup time and smaller published bundles are of utmost importance to you.</span></span> <span data-ttu-id="b2217-168">Aus diesen Gründen ist [:::no-loc(Razor):::-Ansichtskompilierung](xref:mvc/views/view-compilation) in ASP.NET Core 2.0 standardmäßig aktiviert.</span><span class="sxs-lookup"><span data-stu-id="b2217-168">For these reasons, [:::no-loc(Razor)::: view compilation](xref:mvc/views/view-compilation) is enabled by default in ASP.NET Core 2.0.</span></span>

<span data-ttu-id="b2217-169">Das Festlegen der `Mvc:::no-loc(Razor):::CompileOnPublish`-Eigenschaft auf „true“ ist nicht mehr erforderlich.</span><span class="sxs-lookup"><span data-stu-id="b2217-169">Setting the `Mvc:::no-loc(Razor):::CompileOnPublish` property to true is no longer required.</span></span> <span data-ttu-id="b2217-170">Außer wenn Sie die Ansichtskompilierung deaktivieren, kann die Eigenschaft aus der *CSPROJ* -Datei entfernt werden.</span><span class="sxs-lookup"><span data-stu-id="b2217-170">Unless you're disabling view compilation, the property may be removed from the *.csproj* file.</span></span>

<span data-ttu-id="b2217-171">Bei Entwicklung für .NET Framework müssen Sie weiter explizit auf das NuGet-Paket [Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.ViewCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.ViewCompilation) in Ihrer *.csproj* -Datei verweisen:</span><span class="sxs-lookup"><span data-stu-id="b2217-171">When targeting .NET Framework, you still need to explicitly reference the [Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.ViewCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.ViewCompilation) NuGet package in your *.csproj* file:</span></span>

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App.csproj?range=15)]

<a name="app-insights"></a>

## <a name="rely-on-application-insights-light-up-features"></a><span data-ttu-id="b2217-172">Arbeiten mit den Startfeatures von Application Insights</span><span class="sxs-lookup"><span data-stu-id="b2217-172">Rely on Application Insights "light-up" features</span></span>

<span data-ttu-id="b2217-173">Die mühelose Einrichtung der Instrumentierung der Anwendungsleistung ist wichtig.</span><span class="sxs-lookup"><span data-stu-id="b2217-173">Effortless setup of application performance instrumentation is important.</span></span> <span data-ttu-id="b2217-174">Hierfür können Sie nun mit den neuen Startfeatures von [Application Insights](/azure/application-insights/app-insights-overview) in den Visual Studio 2017-Tools arbeiten.</span><span class="sxs-lookup"><span data-stu-id="b2217-174">You can now rely on the new [Application Insights](/azure/application-insights/app-insights-overview) "light-up" features available in the Visual Studio 2017 tooling.</span></span>

<span data-ttu-id="b2217-175">Bei in Visual Studio 2017 erstellten ASP.NET Core 1.1-Projekten wurde Application Insights standardmäßig hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="b2217-175">ASP.NET Core 1.1 projects created in Visual Studio 2017 added Application Insights by default.</span></span> <span data-ttu-id="b2217-176">Wenn Sie das Application Insights SDK nicht direkt verwenden, gehen Sie außerhalb von *Program.cs* und *Startup.cs* folgendermaßen vor:</span><span class="sxs-lookup"><span data-stu-id="b2217-176">If you're not using the Application Insights SDK directly, outside of *Program.cs* and *Startup.cs* , follow these steps:</span></span>

1. <span data-ttu-id="b2217-177">Wenn .NET Core Ihr Ziel ist, entfernen Sie den folgenden `<PackageReference />`-Knoten aus der *CSPROJ* -Datei:</span><span class="sxs-lookup"><span data-stu-id="b2217-177">If targeting .NET Core, remove the following `<PackageReference />` node from the *.csproj* file:</span></span>

    [!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App.csproj?range=10)]

2. <span data-ttu-id="b2217-178">Wenn .NET Core Ihr Ziel ist, entfernen Sie den Aufruf der Erweiterungsmethode `UseApplicationInsights` aus *Program.cs* :</span><span class="sxs-lookup"><span data-stu-id="b2217-178">If targeting .NET Core, remove the `UseApplicationInsights` extension method invocation from *Program.cs* :</span></span>

    [!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Program.cs?name=snippet_ProgramCsMain&highlight=8)]

3. <span data-ttu-id="b2217-179">Entfernen Sie aus *_Layout.cshtml* den clientseitigen API-Aufruf von Application Insights.</span><span class="sxs-lookup"><span data-stu-id="b2217-179">Remove the Application Insights client-side API call from *_Layout.cshtml*.</span></span> <span data-ttu-id="b2217-180">Dieser umfasst die beiden folgenden Codezeilen:</span><span class="sxs-lookup"><span data-stu-id="b2217-180">It comprises the following two lines of code:</span></span>

    [!code-cshtml[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Views/Shared/_Layout.cshtml?range=1,19&dedent=4)]

<span data-ttu-id="b2217-181">Sie können das Application Insights SDK weiterhin direkt verwenden.</span><span class="sxs-lookup"><span data-stu-id="b2217-181">If you are using the Application Insights SDK directly, continue to do so.</span></span> <span data-ttu-id="b2217-182">[metapackage](xref:fundamentals/metapackage) der Version 2.0 enthält die neueste Version von Application Insights, weshalb ein Fehler zum Downgrade eines Pakets angezeigt wird, wenn Sie auf eine ältere Version verweisen.</span><span class="sxs-lookup"><span data-stu-id="b2217-182">The 2.0 [metapackage](xref:fundamentals/metapackage) includes the latest version of Application Insights, so a package downgrade error appears if you're referencing an older version.</span></span>

<a name="auth-and-identity"></a>

## <a name="adopt-authenticationno-locidentity-improvements"></a><span data-ttu-id="b2217-183">Übernehmen der Authentifizierung bzw. Verbesserungen an :::no-loc(Identity):::</span><span class="sxs-lookup"><span data-stu-id="b2217-183">Adopt authentication/:::no-loc(Identity)::: improvements</span></span>

<span data-ttu-id="b2217-184">ASP.NET Core 2.0 verfügt über ein neues Authentifizierungsmodell und bietet eine Reihe wichtiger Änderungen an :::no-loc(ASP.NET Core Identity):::.</span><span class="sxs-lookup"><span data-stu-id="b2217-184">ASP.NET Core 2.0 has a new authentication model and a number of significant changes to :::no-loc(ASP.NET Core Identity):::.</span></span> <span data-ttu-id="b2217-185">Wenn Sie Ihr Projekt mit aktivierter Option „Einzelne Benutzerkonten“ erstellt oder Authentifizierungs- oder Identitätsfunktionen manuell hinzugefügt haben, finden Sie unter [Migrieren von Authentifizierungs- und Identitätseinstellungen nach ASP.NET Core 2.0](xref:migration/1x-to-2x/identity-2x) weitere Informationen.</span><span class="sxs-lookup"><span data-stu-id="b2217-185">If you created your project with Individual User Accounts enabled, or if you have manually added authentication or :::no-loc(Identity):::, see [Migrate Authentication and :::no-loc(Identity)::: to ASP.NET Core 2.0](xref:migration/1x-to-2x/identity-2x).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b2217-186">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="b2217-186">Additional resources</span></span>

* [<span data-ttu-id="b2217-187">Wichtige Änderungen in ASP.NET Core 2.0</span><span class="sxs-lookup"><span data-stu-id="b2217-187">Breaking Changes in ASP.NET Core 2.0</span></span>](https://github.com/aspnet/announcements/issues?page=1&q=is%3Aissue+is%3Aopen+label%3A2.0.0+label%3A%22Breaking+change%22&utf8=%E2%9C%93)
