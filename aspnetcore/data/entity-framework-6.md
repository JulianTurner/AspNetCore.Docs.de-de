---
title: ASP.NET Core und Entity Framework 6
author: rick-anderson
description: Entity Framework 6.3 und höher funktioniert mit ASP.Net Core 3.1 und höher.
ms.author: riande
ms.custom: mvc
ms.date: 7/14/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: data/entity-framework-6
ms.openlocfilehash: 086418c161677f585b08ed360555c93d8575e701
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059454"
---
# <a name="aspnet-core-and-entity-framework-6"></a><span data-ttu-id="cd1e4-103">ASP.NET Core und Entity Framework 6</span><span class="sxs-lookup"><span data-stu-id="cd1e4-103">ASP.NET Core and Entity Framework 6</span></span>
::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="cd1e4-104">Von [Patrick Goode](https://github.com/attrib75)</span><span class="sxs-lookup"><span data-stu-id="cd1e4-104">By [Patrick Goode](https://github.com/attrib75)</span></span>

## <a name="using-entity-framework-6-with-aspnet-core"></a><span data-ttu-id="cd1e4-105">Verwenden von Entity Framework 6 mit ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="cd1e4-105">Using Entity Framework 6 with ASP.NET Core</span></span>

<span data-ttu-id="cd1e4-106">[Entity Framework Core](/ef/) sollte für neue Entwicklungsvorhaben verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-106">[Entity Framework Core](/ef/) should be used for new development.</span></span> <span data-ttu-id="cd1e4-107">Das [Downloadbeispiel](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/entity-framework-6/3.xsample) verwendet [Entity Framework 6 (EF6)](/ef/ef6), das zum Migrieren von vorhandenen Apps zu ASP.NET Core verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-107">The [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/entity-framework-6/3.xsample) uses [Entity Framework 6 (EF6)](/ef/ef6), which can be used to migrate exiting apps to ASP.NET Core.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="cd1e4-108">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="cd1e4-108">Additional resources</span></span>

* [<span data-ttu-id="cd1e4-109">Entity Framework – Code-Based Configuration (Codebasierte Konfiguration von Entity Framework)</span><span class="sxs-lookup"><span data-stu-id="cd1e4-109">Entity Framework - Code-Based Configuration</span></span>](/ef/ef6/fundamentals/configuring/code-based)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="cd1e4-110">Von [Paweł Grudzień](https://github.com/pgrudzien12), [Damien Pontifex](https://github.com/DamienPontifex) und [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="cd1e4-110">By [Paweł Grudzień](https://github.com/pgrudzien12), [Damien Pontifex](https://github.com/DamienPontifex), and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="cd1e4-111">Dieser Artikel veranschaulicht die Verwendung von Entity Framework 6 in einer ASP.NET Core-Anwendung.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-111">This article shows how to use Entity Framework 6 in an ASP.NET Core application.</span></span>    

## <a name="overview"></a><span data-ttu-id="cd1e4-112">Übersicht</span><span class="sxs-lookup"><span data-stu-id="cd1e4-112">Overview</span></span> 

<span data-ttu-id="cd1e4-113">Damit Sie Entity Framework 6 verwenden können, muss Ihr Projekt mit .NET Framework kompiliert werden, da .NET Core von Entity Framework 6 nicht unterstützt wird.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-113">To use Entity Framework 6, your project has to compile against .NET Framework, as Entity Framework 6 doesn't support .NET Core.</span></span> <span data-ttu-id="cd1e4-114">Wenn Sie plattformübergreifende Features benötigen, müssen Sie ein Upgrade auf [Entity Framework Core](/ef/) durchführen.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-114">If you need cross-platform features you will need to upgrade to [Entity Framework Core](/ef/).</span></span>  

<span data-ttu-id="cd1e4-115">Die empfohlene Methode zum Verwenden von Entity Framework 6 in einer ASP.NET Core-Anwendung besteht darin, den Kontext von Entity Framework 6 und die Modellklassen in einem Klassenbibliotheksprojekt einzufügen, das das .NET-Framework anzielt.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-115">The recommended way to use Entity Framework 6 in an ASP.NET Core application is to put the EF6 context and model classes in a class library project that targets .NET Framework.</span></span> <span data-ttu-id="cd1e4-116">Fügen Sie einen Verweis vom ASP.NET Core-Projekt zur Klassenbibliothek hinzu.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-116">Add a reference to the class library from the ASP.NET Core project.</span></span> <span data-ttu-id="cd1e4-117">Weitere Informationen finden Sie im Beispiel [Visual Studio-Projektmappe mit Entity Framework 6 und ASP.NET Core-Projekten](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/entity-framework-6/sample/).</span><span class="sxs-lookup"><span data-stu-id="cd1e4-117">See the sample [Visual Studio solution with EF6 and ASP.NET Core projects](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/entity-framework-6/sample/).</span></span>  

<span data-ttu-id="cd1e4-118">Sie können den Kontext von Entity Framework 6 nicht in ASP.NET Core-Projekte einfügen, da diese nicht alle Funktionalitäten unterstützen, die für Entity Framework 6-Befehle (wie *Enable-Migrations* ) erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-118">You can't put an EF6 context in an ASP.NET Core project because .NET Core projects don't support all of the functionality that EF6 commands such as *Enable-Migrations* require.</span></span>    

<span data-ttu-id="cd1e4-119">Unabhängig vom Projekttyp, in dem Sie den Kontext von Entity Framework 6 platzieren, funktionieren nur Entity Framework 6-Befehlszeilentools mit diesem Kontext.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-119">Regardless of project type in which you locate your EF6 context, only EF6 command-line tools work with an EF6 context.</span></span> <span data-ttu-id="cd1e4-120">`Scaffold-DbContext` ist beispielsweise nur in Entity Framework Core verfügbar.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-120">For example, `Scaffold-DbContext` is only available in Entity Framework Core.</span></span> <span data-ttu-id="cd1e4-121">Wenn Sie bei einer Datenbank das Reverse Engineering (Zurückentwicklung) in ein Entity Framework 6-Modell durchführen müssen, finden Sie weitere Informationen unter <https://docs.microsoft.com/ef/ef6/modeling/code-first/workflows/existing-database>.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-121">If you need to do reverse engineering of a database into an EF6 model, see <https://docs.microsoft.com/ef/ef6/modeling/code-first/workflows/existing-database>.</span></span>    

## <a name="reference-full-framework-and-ef6-in-the-aspnet-core-project"></a><span data-ttu-id="cd1e4-122">Verweisen auf das vollständige Framework und auf Entity Framework 6 in einem ASP.NET Core-Projekt</span><span class="sxs-lookup"><span data-stu-id="cd1e4-122">Reference full framework and EF6 in the ASP.NET Core project</span></span> 

<span data-ttu-id="cd1e4-123">Das ASP.NET Core-Projekt muss .NET-Framework anzielen und auf das Entity Framework 6 verweisen.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-123">Your ASP.NET Core project needs to target .NET Framework and reference EF6.</span></span> <span data-ttu-id="cd1e4-124">Die *CSPROJ* -Datei Ihres ASP.NET Core-Projekts ähnelt beispielsweise folgendem Beispiel (nur die relevanten Teile der Datei werden dargestellt).</span><span class="sxs-lookup"><span data-stu-id="cd1e4-124">For example, the *.csproj* file of your ASP.NET Core project will look similar to the following example (only relevant parts of the file are shown).</span></span>    

[!code-xml[](entity-framework-6/sample/MVCCore/MVCCore.csproj?range=3-9&highlight=2)]   

<span data-ttu-id="cd1e4-125">Verwenden Sie die Vorlage **ASP.NET Core-Webanwendung (.NET Framework)** , wenn Sie ein neues Projekt erstellen.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-125">When creating a new project, use the **ASP.NET Core Web Application (.NET Framework)** template.</span></span>    

## <a name="handle-connection-strings"></a><span data-ttu-id="cd1e4-126">Verarbeiten von Verbindungszeichenfolgen</span><span class="sxs-lookup"><span data-stu-id="cd1e4-126">Handle connection strings</span></span>    

<span data-ttu-id="cd1e4-127">Die Entity Framework 6-Befehlszeilentools, die Sie im Entity Framework 6-Klassenbibliotheksprojekt verwenden, erfordern einen Standardkonstruktor, um den Kontext zu instanziieren.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-127">The EF6 command-line tools that you'll use in the EF6 class library project require a default constructor so they can instantiate the context.</span></span> <span data-ttu-id="cd1e4-128">Wenn Sie die Verbindungszeichenfolge angeben möchten, die im ASP.NET Core-Projekt verwendet werden soll, muss der Kontextkonstruktor über einen Parameter verfügen, der das Übergeben der Verbindungszeichenfolge ermöglicht.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-128">But you'll probably want to specify the connection string to use in the ASP.NET Core project, in which case your context constructor must have a parameter that lets you pass in the connection string.</span></span> <span data-ttu-id="cd1e4-129">Hier finden Sie ein Beispiel dafür.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-129">Here's an example.</span></span>   

[!code-csharp[](entity-framework-6/sample/EF6/SchoolContext.cs?name=snippet_Constructor)]   

<span data-ttu-id="cd1e4-130">Da in Ihrem Entity Framework 6-Kontext kein Konstruktor ohne Parameter vorhanden ist, muss Ihr Entity Framework 6-Projekt eine Implementierung von <https://docs.microsoft.com/dotnet/api/system.data.entity.infrastructure.idbcontextfactory-1?view=entity-framework-6.2.0> bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-130">Since your EF6 context doesn't have a parameterless constructor, your EF6 project has to provide an implementation of <https://docs.microsoft.com/dotnet/api/system.data.entity.infrastructure.idbcontextfactory-1?view=entity-framework-6.2.0>.</span></span> <span data-ttu-id="cd1e4-131">Das Entity Framework 6-Befehlszeilentool sucht und verwendet diese Implementierung, damit der Kontext instanziiert werden kann.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-131">The EF6 command-line tools will find and use that implementation so they can instantiate the context.</span></span> <span data-ttu-id="cd1e4-132">Hier finden Sie ein Beispiel dafür.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-132">Here's an example.</span></span>   

[!code-csharp[](entity-framework-6/sample/EF6/SchoolContextFactory.cs?name=snippet_IDbContextFactory)]  

<span data-ttu-id="cd1e4-133">In diesem Beispielcode übergibt die `IDbContextFactory`-Implementierung eine hart codierte Verbindungszeichenfolge.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-133">In this sample code, the `IDbContextFactory` implementation passes in a hard-coded connection string.</span></span> <span data-ttu-id="cd1e4-134">Dabei handelt es sich um die Verbindungszeichenfolge, die von den Befehlszeilentools verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-134">This is the connection string that the command-line tools will use.</span></span> <span data-ttu-id="cd1e4-135">Sie sollten eine Strategie implementieren, um sicherzustellen, dass die Klassenbibliothek die gleiche Verbindungszeichenfolge wie die aufrufende Anwendung verwendet.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-135">You'll want to implement a strategy to ensure that the class library uses the same connection string that the calling application uses.</span></span> <span data-ttu-id="cd1e4-136">Sie könnten den Wert in beiden Projekten beispielsweise aus einer Umgebungsvariable abrufen.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-136">For example, you could get the value from an environment variable in both projects.</span></span>   

## <a name="set-up-dependency-injection-in-the-aspnet-core-project"></a><span data-ttu-id="cd1e4-137">Einrichten von Dependency Injection im ASP.NET Core-Projekt</span><span class="sxs-lookup"><span data-stu-id="cd1e4-137">Set up dependency injection in the ASP.NET Core project</span></span>  

<span data-ttu-id="cd1e4-138">Richten Sie den Entity Framework 6-Kontext in der Datei *Startup.cs* des Core-Projekts für Dependency Injection (DI) in `ConfigureServices` ein.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-138">In the Core project's *Startup.cs* file, set up the EF6 context for dependency injection (DI) in `ConfigureServices`.</span></span> <span data-ttu-id="cd1e4-139">Die Lebensdauer der Entity Framework-Kontextobjekte sollte auf den Zeitraum vor der Anforderung begrenzt werden.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-139">EF context objects should be scoped for a per-request lifetime.</span></span>   

[!code-csharp[](entity-framework-6/sample/MVCCore/Startup.cs?name=snippet_ConfigureServices&highlight=5)]   

<span data-ttu-id="cd1e4-140">Sie können dann mithilfe von DI eine Instanz des Kontext in Ihre Controller abrufen.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-140">You can then get an instance of the context in your controllers by using DI.</span></span> <span data-ttu-id="cd1e4-141">Der Code ähnelt dem, den Sie für einen Entity Framework Core-Kontext verwenden würden:</span><span class="sxs-lookup"><span data-stu-id="cd1e4-141">The code is similar to what you'd write for an EF Core context:</span></span>    

[!code-csharp[](entity-framework-6/sample/MVCCore/Controllers/StudentsController.cs?name=snippet_ContextInController)]  

## <a name="sample-application"></a><span data-ttu-id="cd1e4-142">Beispielanwendung</span><span class="sxs-lookup"><span data-stu-id="cd1e4-142">Sample application</span></span>   

<span data-ttu-id="cd1e4-143">Eine funktionierende Beispielanwendung finden Sie in der [Visual Studio-Beispielprojektmappe](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/entity-framework-6/sample/), die in diesem Artikel verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-143">For a working sample application, see the [sample Visual Studio solution](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/entity-framework-6/sample/) that accompanies this article.</span></span>  

<span data-ttu-id="cd1e4-144">Dieses Beispiel kann von Grund auf neu erstellt werden, indem Sie folgende Schritte in Visual Studio befolgen:</span><span class="sxs-lookup"><span data-stu-id="cd1e4-144">This sample can be created from scratch by the following steps in Visual Studio:</span></span>    

* <span data-ttu-id="cd1e4-145">Erstellen Sie eine Projektmappe.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-145">Create a solution.</span></span>    

* <span data-ttu-id="cd1e4-146">**Hinzufügen** > **Neues Projekt** > **Web** > **ASP.NET Core-Webanwendung**</span><span class="sxs-lookup"><span data-stu-id="cd1e4-146">**Add** > **New Project** > **Web** > **ASP.NET Core Web Application**</span></span>    
  * <span data-ttu-id="cd1e4-147">Wählen Sie im Dialogfeld zur Auswahl der Projektvorlage „API und .NET Framework“ in der Dropdown-Liste aus.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-147">In project template selection dialog, select API and .NET Framework in dropdown</span></span> 

* <span data-ttu-id="cd1e4-148">**Hinzufügen** > **Neues Projekt** > **Windows-Desktop** > **Klassenbibliothek (.NET Framework)**</span><span class="sxs-lookup"><span data-stu-id="cd1e4-148">**Add** > **New Project** > **Windows Desktop** > **Class Library (.NET Framework)**</span></span>  

* <span data-ttu-id="cd1e4-149">Führen Sie in der **Paket-Manager-Konsole** für beide Projekte den Befehl `Install-Package Entityframework` aus.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-149">In **Package Manager Console** (PMC) for both projects, run the command `Install-Package Entityframework`.</span></span>    

* <span data-ttu-id="cd1e4-150">Erstellen Sie Datenmodellklassen, eine Kontextklasse und eine Implementierung von `IDbContextFactory` im Klassenbibliotheksprojekt.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-150">In the class library project, create data model classes and a context class, and an implementation of `IDbContextFactory`.</span></span>    

* <span data-ttu-id="cd1e4-151">Führen Sie in der Paket-Manager-Konsole des Klassenbibliotheksprojekts die Befehle `Enable-Migrations` und `Add-Migration Initial` aus.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-151">In PMC for the class library project, run the commands `Enable-Migrations` and `Add-Migration Initial`.</span></span> <span data-ttu-id="cd1e4-152">Wenn Sie das ASP.NET Core-Projekt als Startprojekt festgelegt haben, fügen Sie `-StartupProjectName EF6` zu diesen Befehlen hinzu.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-152">If you have set the ASP.NET Core project as the startup project, add `-StartupProjectName EF6` to these commands.</span></span> 

* <span data-ttu-id="cd1e4-153">Fügen Sie im Core-Projekt einen Projektverweis zum Klassenbibliotheksprojekt hinzu.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-153">In the Core project, add a project reference to the class library project.</span></span>    

* <span data-ttu-id="cd1e4-154">Registrieren Sie im Core-Projekt in der Datei *Startup.cs* den Kontext für DI.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-154">In the Core project, in *Startup.cs* , register the context for DI.</span></span>    

* <span data-ttu-id="cd1e4-155">Fügen Sie im Core-Projekt in *appsettings.json* die Verbindungszeichenfolge hinzu.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-155">In the Core project, in *appsettings.json* , add the connection string.</span></span>  

* <span data-ttu-id="cd1e4-156">Fügen Sie im Core-Projekt einen Controller und Ansichten hinzu, um zu überprüfen, dass Daten gelesen und geschrieben werden können.</span><span class="sxs-lookup"><span data-stu-id="cd1e4-156">In the Core project, add a controller and view(s) to verify that you can read and write data.</span></span> <span data-ttu-id="cd1e4-157">(Beachten Sie, dass der ASP.NET Core MVC-Gerüstbau nicht mit dem Entity Framework 6-Kontext funktioniert, auf den durch die Klassenbibliothek verwiesen wird.)</span><span class="sxs-lookup"><span data-stu-id="cd1e4-157">(Note that ASP.NET Core MVC scaffolding won't work with the EF6 context referenced from the class library.)</span></span>

::: moniker-end
