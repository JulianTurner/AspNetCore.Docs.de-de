---
title: Befehl „dotnet aspnet-codegenerator“
author: rick-anderson
description: Der Befehl „dotnet aspnet-codegenerator“ erstellt das Gerüst für ASP.NET Core-Projekte.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 11/16/2020
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
uid: fundamentals/tools/dotnet-aspnet-codegenerator
ms.openlocfilehash: 8844b0014cac58f414d79df4c64bc0efac75bfe1
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/04/2021
ms.locfileid: "94920702"
---
# <a name="dotnet-aspnet-codegenerator"></a><span data-ttu-id="37170-103">dotnet aspnet-codegenerator</span><span class="sxs-lookup"><span data-stu-id="37170-103">dotnet aspnet-codegenerator</span></span>

<span data-ttu-id="37170-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="37170-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="37170-105">`dotnet aspnet-codegenerator`: Führt die ASP.NET Core-Gerüstbauengine aus.</span><span class="sxs-lookup"><span data-stu-id="37170-105">`dotnet aspnet-codegenerator` - Runs the ASP.NET Core scaffolding engine.</span></span> <span data-ttu-id="37170-106">`dotnet aspnet-codegenerator` wird nur für den Gerüstbau über die Befehlszeile benötigt, nicht zum Gerüstbau mit Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="37170-106">`dotnet aspnet-codegenerator` is only required to scaffold from the command line, it's not needed to use scaffolding with Visual Studio.</span></span>

## <a name="install-and-update-aspnet-codegenerator"></a><span data-ttu-id="37170-107">Installieren und Aktualisieren von aspnet-codegenerator</span><span class="sxs-lookup"><span data-stu-id="37170-107">Install and update aspnet-codegenerator</span></span>

<span data-ttu-id="37170-108">Installieren Sie das [.NET SDK](https://dotnet.microsoft.com/download).</span><span class="sxs-lookup"><span data-stu-id="37170-108">Install the [.NET SDK](https://dotnet.microsoft.com/download).</span></span>

<span data-ttu-id="37170-109">`dotnet-aspnet-codegenerator` ist ein [globales Tool](/dotnet/core/tools/global-tools), das installiert werden muss.</span><span class="sxs-lookup"><span data-stu-id="37170-109">`dotnet-aspnet-codegenerator` is a [global tool](/dotnet/core/tools/global-tools) that must be installed.</span></span> <span data-ttu-id="37170-110">Über den folgenden Befehl wird die neueste stabile Version des `dotnet-aspnet-codegenerator`-Tools installiert:</span><span class="sxs-lookup"><span data-stu-id="37170-110">The following command installs the latest stable version of the `dotnet-aspnet-codegenerator` tool:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="37170-111">Mit dem folgenden Befehl wird `dotnet-aspnet-codegenerator` auf die neueste stabile Version aktualisiert, die in den installierten .NET Core SDKs verfügbar ist:</span><span class="sxs-lookup"><span data-stu-id="37170-111">The following command updates `dotnet-aspnet-codegenerator` to the latest stable version available from the installed .NET Core SDKs:</span></span>

```dotnetcli
dotnet tool update -g dotnet-aspnet-codegenerator
```

## <a name="uninstall-aspnet-codegenerator"></a><span data-ttu-id="37170-112">Deinstallieren von aspnet-codegenerator</span><span class="sxs-lookup"><span data-stu-id="37170-112">Uninstall aspnet-codegenerator</span></span>

<span data-ttu-id="37170-113">Es kann erforderlich sein, `aspnet-codegenerator` zu deinstallieren, um Probleme zu beheben.</span><span class="sxs-lookup"><span data-stu-id="37170-113">It may be necessary to uninstall the `aspnet-codegenerator` to resolve problems.</span></span> <span data-ttu-id="37170-114">Wenn Sie z. B. eine Vorschauversion von `aspnet-codegenerator`installiert haben, deinstallieren Sie diese, bevor Sie die veröffentlichte Version installieren.</span><span class="sxs-lookup"><span data-stu-id="37170-114">For example, if you installed a preview version of `aspnet-codegenerator`, uninstall it before installing the released version.</span></span>

<span data-ttu-id="37170-115">Über den folgenden Befehl wird die neueste stabile Version des `dotnet-aspnet-codegenerator`-Tools installiert:</span><span class="sxs-lookup"><span data-stu-id="37170-115">The following commands uninstall the `dotnet-aspnet-codegenerator` tool and installs the latest stable version:</span></span>

```dotnetcli
dotnet tool uninstall -g dotnet-aspnet-codegenerator
dotnet tool install -g dotnet-aspnet-codegenerator
```

## <a name="synopsis"></a><span data-ttu-id="37170-116">Übersicht</span><span class="sxs-lookup"><span data-stu-id="37170-116">Synopsis</span></span>

```
dotnet aspnet-codegenerator [arguments] [-p|--project] [-n|--nuget-package-dir] [-c|--configuration] [-tfm|--target-framework] [-b|--build-base-path] [--no-build] 
dotnet aspnet-codegenerator [-h|--help]
```

## <a name="description"></a><span data-ttu-id="37170-117">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="37170-117">Description</span></span>

<span data-ttu-id="37170-118">Der globale Befehl `dotnet aspnet-codegenerator` führt den ASP.NET Code-Codegenerator und die Gerüstbauengine aus.</span><span class="sxs-lookup"><span data-stu-id="37170-118">The `dotnet aspnet-codegenerator` global command runs the ASP.NET Core code generator and scaffolding engine.</span></span>

## <a name="arguments"></a><span data-ttu-id="37170-119">Argumente</span><span class="sxs-lookup"><span data-stu-id="37170-119">Arguments</span></span>

`generator`

<span data-ttu-id="37170-120">Der auszuführende Codegenerator.</span><span class="sxs-lookup"><span data-stu-id="37170-120">The code generator to run.</span></span> <span data-ttu-id="37170-121">Folgende Generatoren sind verfügbar:</span><span class="sxs-lookup"><span data-stu-id="37170-121">The following generators are available:</span></span>

| <span data-ttu-id="37170-122">Generator</span><span class="sxs-lookup"><span data-stu-id="37170-122">Generator</span></span>  | <span data-ttu-id="37170-123">Vorgang</span><span class="sxs-lookup"><span data-stu-id="37170-123">Operation</span></span>                                                            |
| ---------- | -------------------------------------------------------------------- |
| <span data-ttu-id="37170-124">area</span><span class="sxs-lookup"><span data-stu-id="37170-124">area</span></span>       | [<span data-ttu-id="37170-125">Gerüstbau für einen Bereich</span><span class="sxs-lookup"><span data-stu-id="37170-125">Scaffolds an Area</span></span>](xref:mvc/controllers/areas)                      |
| <span data-ttu-id="37170-126">Controller</span><span class="sxs-lookup"><span data-stu-id="37170-126">controller</span></span> | [<span data-ttu-id="37170-127">Gerüstbau für einen Controller</span><span class="sxs-lookup"><span data-stu-id="37170-127">Scaffolds a controller</span></span>](xref:tutorials/first-mvc-app/adding-model)  |
| <span data-ttu-id="37170-128">Identität</span><span class="sxs-lookup"><span data-stu-id="37170-128">identity</span></span>   | [<span data-ttu-id="37170-129">Gerüstbau Identity</span><span class="sxs-lookup"><span data-stu-id="37170-129">Scaffolds Identity</span></span>](xref:security/authentication/scaffold-identity) |
| <span data-ttu-id="37170-130">razorpage</span><span class="sxs-lookup"><span data-stu-id="37170-130">razorpage</span></span>  | [<span data-ttu-id="37170-131">Gerüstbau für Razor Pages</span><span class="sxs-lookup"><span data-stu-id="37170-131">Scaffolds Razor Pages</span></span>](xref:tutorials/razor-pages/model)            |
| <span data-ttu-id="37170-132">Sicht</span><span class="sxs-lookup"><span data-stu-id="37170-132">view</span></span>       | [<span data-ttu-id="37170-133">Gerüstbau für eine Ansicht</span><span class="sxs-lookup"><span data-stu-id="37170-133">Scaffolds a view</span></span>](xref:mvc/views/overview)                          |

## <a name="options"></a><span data-ttu-id="37170-134">Optionen</span><span class="sxs-lookup"><span data-stu-id="37170-134">Options</span></span>

`-n|--nuget-package-dir`

<span data-ttu-id="37170-135">Gibt das NuGet-Paketverzeichnis an.</span><span class="sxs-lookup"><span data-stu-id="37170-135">Specifies the NuGet package directory.</span></span>

`-c|--configuration {Debug|Release}`

<span data-ttu-id="37170-136">Legt die Buildkonfiguration fest.</span><span class="sxs-lookup"><span data-stu-id="37170-136">Defines the build configuration.</span></span> <span data-ttu-id="37170-137">Der Standardwert ist `Debug`.</span><span class="sxs-lookup"><span data-stu-id="37170-137">The default value is `Debug`.</span></span>

`-tfm|--target-framework`

<span data-ttu-id="37170-138">Das zu verwendende [Zielframework](/dotnet/standard/frameworks).</span><span class="sxs-lookup"><span data-stu-id="37170-138">Target [Framework](/dotnet/standard/frameworks) to use.</span></span> <span data-ttu-id="37170-139">Beispielsweise `net46`.</span><span class="sxs-lookup"><span data-stu-id="37170-139">For example, `net46`.</span></span>

`-b|--build-base-path`

<span data-ttu-id="37170-140">Der Basispfad für den Build.</span><span class="sxs-lookup"><span data-stu-id="37170-140">The build base path.</span></span>

`-h|--help`

<span data-ttu-id="37170-141">Druckt eine kurze Hilfe für den Befehl.</span><span class="sxs-lookup"><span data-stu-id="37170-141">Prints out a short help for the command.</span></span>

`--no-build`

<span data-ttu-id="37170-142">Erstellt das Projekt nicht vor der Ausführung.</span><span class="sxs-lookup"><span data-stu-id="37170-142">Doesn't build the project before running.</span></span> <span data-ttu-id="37170-143">Zudem wird das Flag `--no-restore` implizit festgelegt.</span><span class="sxs-lookup"><span data-stu-id="37170-143">It also implicitly sets the `--no-restore` flag.</span></span>

`-p|--project <PATH>`

<span data-ttu-id="37170-144">Gibt den Pfad der auszuführenden Projektdatei an (Ordnername oder vollständiger Pfad).</span><span class="sxs-lookup"><span data-stu-id="37170-144">Specifies the path of the project file to run (folder name or full path).</span></span> <span data-ttu-id="37170-145">Wenn nicht angegeben, wird standardmäßig das aktuelle Verzeichnis gewählt.</span><span class="sxs-lookup"><span data-stu-id="37170-145">If not specified, it defaults to the current directory.</span></span>

## <a name="generator-options"></a><span data-ttu-id="37170-146">Generatoroptionen</span><span class="sxs-lookup"><span data-stu-id="37170-146">Generator options</span></span>

<span data-ttu-id="37170-147">In den folgenden Abschnitte werden die verfügbaren Optionen für die unterstützten Generatoren vorgestellt:</span><span class="sxs-lookup"><span data-stu-id="37170-147">The following sections detail the options available for the supported generators:</span></span>

* <span data-ttu-id="37170-148">Bereich</span><span class="sxs-lookup"><span data-stu-id="37170-148">Area</span></span>
* <span data-ttu-id="37170-149">Controller</span><span class="sxs-lookup"><span data-stu-id="37170-149">Controller</span></span>
* Identity  
* <span data-ttu-id="37170-150">Razorpage</span><span class="sxs-lookup"><span data-stu-id="37170-150">Razorpage</span></span>
* <span data-ttu-id="37170-151">Ansicht</span><span class="sxs-lookup"><span data-stu-id="37170-151">View</span></span>

<a name="area"></a>

### <a name="area-options"></a><span data-ttu-id="37170-152">Bereichsoptionen</span><span class="sxs-lookup"><span data-stu-id="37170-152">Area options</span></span>

<span data-ttu-id="37170-153">Dieses Tool ist für ASP.NET Core-Webprojekte mit Controllern und Ansichten vorgesehen.</span><span class="sxs-lookup"><span data-stu-id="37170-153">This tool is intended for ASP.NET Core web projects with controllers and views.</span></span> <span data-ttu-id="37170-154">Es ist nicht für Razor Pages-Apps gedacht.</span><span class="sxs-lookup"><span data-stu-id="37170-154">It's not intended for Razor Pages apps.</span></span>

<span data-ttu-id="37170-155">Verwendung: `dotnet aspnet-codegenerator area AreaNameToGenerate`</span><span class="sxs-lookup"><span data-stu-id="37170-155">Usage: `dotnet aspnet-codegenerator area AreaNameToGenerate`</span></span>

<span data-ttu-id="37170-156">Der oben gezeigte Befehl generiert die folgenden Ordner:</span><span class="sxs-lookup"><span data-stu-id="37170-156">The preceding command generates the following folders:</span></span>

* <span data-ttu-id="37170-157">*Bereiche*</span><span class="sxs-lookup"><span data-stu-id="37170-157">*Areas*</span></span>
  * <span data-ttu-id="37170-158">*AreaNameToGenerate*</span><span class="sxs-lookup"><span data-stu-id="37170-158">*AreaNameToGenerate*</span></span>
    * <span data-ttu-id="37170-159">*Controller*</span><span class="sxs-lookup"><span data-stu-id="37170-159">*Controllers*</span></span>
    * <span data-ttu-id="37170-160">*Data*</span><span class="sxs-lookup"><span data-stu-id="37170-160">*Data*</span></span>
    * <span data-ttu-id="37170-161">*Models*</span><span class="sxs-lookup"><span data-stu-id="37170-161">*Models*</span></span>
    * <span data-ttu-id="37170-162">*Ansichten*</span><span class="sxs-lookup"><span data-stu-id="37170-162">*Views*</span></span>

<a name="ctl"></a>

### <a name="controller-options"></a><span data-ttu-id="37170-163">Controlleroptionen</span><span class="sxs-lookup"><span data-stu-id="37170-163">Controller options</span></span>

<span data-ttu-id="37170-164">Die folgende Tabelle zeigt die Optionen für `aspnet-codegenerator` `controller` und `razorpage`:</span><span class="sxs-lookup"><span data-stu-id="37170-164">The following table lists options for  `aspnet-codegenerator` `controller` and `razorpage`:</span></span>

[!INCLUDE [aspnet-codegenerator-args-md.md](~/includes/aspnet-codegenerator-args-md.md)]

<span data-ttu-id="37170-165">Die folgende Tabelle zeigt die Optionen für `aspnet-codegenerator controller`:</span><span class="sxs-lookup"><span data-stu-id="37170-165">The following table lists options unique to  `aspnet-codegenerator controller`:</span></span>

| <span data-ttu-id="37170-166">Option</span><span class="sxs-lookup"><span data-stu-id="37170-166">Option</span></span>                         | <span data-ttu-id="37170-167">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="37170-167">Description</span></span>                                                                                               |
| ------------------------------ | --------------------------------------------------------------------------------------------------------- |
| <span data-ttu-id="37170-168">--controllerName oder -name</span><span class="sxs-lookup"><span data-stu-id="37170-168">--controllerName or -name</span></span>      | <span data-ttu-id="37170-169">Der Name des Controllers.</span><span class="sxs-lookup"><span data-stu-id="37170-169">Name of the controller.</span></span>                                                                                   |
| <span data-ttu-id="37170-170">--useAsyncActions oder -async</span><span class="sxs-lookup"><span data-stu-id="37170-170">--useAsyncActions or -async</span></span>    | <span data-ttu-id="37170-171">Generiert asynchrone Controlleraktionen.</span><span class="sxs-lookup"><span data-stu-id="37170-171">Generate async controller actions.</span></span>                                                                        |
| <span data-ttu-id="37170-172">--noViews oder -nv</span><span class="sxs-lookup"><span data-stu-id="37170-172">--noViews or -nv</span></span>               | <span data-ttu-id="37170-173">Generiert **keine** Ansichten.</span><span class="sxs-lookup"><span data-stu-id="37170-173">Generate **no** views.</span></span>                                                                                    |
| <span data-ttu-id="37170-174">--restWithNoViews oder -api</span><span class="sxs-lookup"><span data-stu-id="37170-174">--restWithNoViews or -api</span></span>      | <span data-ttu-id="37170-175">Generiert einen Controller mit einer API im REST-Stil.</span><span class="sxs-lookup"><span data-stu-id="37170-175">Generate a Controller with REST style API.</span></span> <span data-ttu-id="37170-176">`noViews` wird vorausgesetzt, und ansichtsbezogene Optionen werden ignoriert.</span><span class="sxs-lookup"><span data-stu-id="37170-176">`noViews` is assumed and any view related options are ignored.</span></span> |
| <span data-ttu-id="37170-177">--readWriteActions oder -actions</span><span class="sxs-lookup"><span data-stu-id="37170-177">--readWriteActions or -actions</span></span> | <span data-ttu-id="37170-178">Generiert einen Controller mit Lese-/Schreibaktionen ohne Modell.</span><span class="sxs-lookup"><span data-stu-id="37170-178">Generate controller with read/write actions without a model.</span></span>                                              |

<span data-ttu-id="37170-179">Verwenden Sie den Schalter `-h`, um Hilfe zum `aspnet-codegenerator controller`-Befehl zu erhalten:</span><span class="sxs-lookup"><span data-stu-id="37170-179">Use the `-h` switch for help on the `aspnet-codegenerator controller` command:</span></span>

```dotnetcli
dotnet aspnet-codegenerator controller -h
```

<span data-ttu-id="37170-180">Unter [Erstellen des Gerüsts für das Filmmodell](xref:tutorials/first-mvc-app/adding-model) finden Sie ein Beispiel für `dotnet aspnet-codegenerator controller`.</span><span class="sxs-lookup"><span data-stu-id="37170-180">See [Scaffold the movie model](xref:tutorials/first-mvc-app/adding-model) for an example of `dotnet aspnet-codegenerator controller`.</span></span>

### <a name="no-locrazorpage"></a><span data-ttu-id="37170-181">Razorpage</span><span class="sxs-lookup"><span data-stu-id="37170-181">Razorpage</span></span>

<a name="rp"></a>

<span data-ttu-id="37170-182">Für Razor Pages können jeweils einzelne Gerüste erstellt werden, indem der Name der neuen Seite und der zu verwendenden Vorlage angegeben wird.</span><span class="sxs-lookup"><span data-stu-id="37170-182">Razor Pages can be individually scaffolded by specifying the name of the new page and the template to use.</span></span> <span data-ttu-id="37170-183">Folgende Vorlagen werden unterstützt:</span><span class="sxs-lookup"><span data-stu-id="37170-183">The supported templates are:</span></span>

* `Empty`
* `Create`
* `Edit`
* `Delete`
* `Details`
* `List`

<span data-ttu-id="37170-184">Beispielsweise verwendet der folgende Befehl die Bearbeitungsvorlage, um *MyEdit.cshtml* und *MyEdit.cshtml.cs* zu generieren:</span><span class="sxs-lookup"><span data-stu-id="37170-184">For example, the following command uses the Edit template to generate *MyEdit.cshtml* and *MyEdit.cshtml.cs*:</span></span>

```dotnetcli
dotnet aspnet-codegenerator razorpage MyEdit Edit -m Movie -dc RazorPagesMovieContext -outDir Pages/Movies
```

<span data-ttu-id="37170-185">Typischerweise werden die Vorlage und der Name der generierten Datei nicht angegeben, und es werden die folgenden Vorlagen erstellt:</span><span class="sxs-lookup"><span data-stu-id="37170-185">Typically, the template and generated file name is not specified, and the following templates are created:</span></span>

* `Create`
* `Edit`
* `Delete`
* `Details`
* `List`

<span data-ttu-id="37170-186">Die folgende Tabelle zeigt die Optionen für `aspnet-codegenerator` `razorpage` und `controller`:</span><span class="sxs-lookup"><span data-stu-id="37170-186">The following table lists options for  `aspnet-codegenerator` `razorpage` and `controller`:</span></span>

[!INCLUDE [aspnet-codegenerator-args-md.md](~/includes/aspnet-codegenerator-args-md.md)]

<span data-ttu-id="37170-187">Die folgende Tabelle zeigt die Optionen für `aspnet-codegenerator razorpage`:</span><span class="sxs-lookup"><span data-stu-id="37170-187">The following table lists options unique to  `aspnet-codegenerator razorpage`:</span></span>

| <span data-ttu-id="37170-188">Option</span><span class="sxs-lookup"><span data-stu-id="37170-188">Option</span></span>                        | <span data-ttu-id="37170-189">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="37170-189">Description</span></span>                                                                           |
| ----------------------------- | ------------------------------------------------------------------------------------- |
| <span data-ttu-id="37170-190">--namespaceName oder -namespace</span><span class="sxs-lookup"><span data-stu-id="37170-190">--namespaceName or -namespace</span></span> | <span data-ttu-id="37170-191">Der Name des Namespace, der für das generierte PageModel verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="37170-191">The name of the namespace to use for the generated PageModel</span></span>                          |
| <span data-ttu-id="37170-192">--partialView oder -partial</span><span class="sxs-lookup"><span data-stu-id="37170-192">--partialView or -partial</span></span>     | <span data-ttu-id="37170-193">Generiert eine Teilansicht.</span><span class="sxs-lookup"><span data-stu-id="37170-193">Generate a partial view.</span></span> <span data-ttu-id="37170-194">Die Layoutoptionen -l und -udl werden ignoriert, wenn diese Option angegeben wird.</span><span class="sxs-lookup"><span data-stu-id="37170-194">Layout options -l and -udl are ignored if this is specified.</span></span> |
| <span data-ttu-id="37170-195">--noPageModel oder -npm</span><span class="sxs-lookup"><span data-stu-id="37170-195">--noPageModel or -npm</span></span>         | <span data-ttu-id="37170-196">Schalter, um keine PageModel-Klasse für eine leere Vorlage zu generieren.</span><span class="sxs-lookup"><span data-stu-id="37170-196">Switch to not generate a PageModel class for Empty template</span></span>                           |

<span data-ttu-id="37170-197">Verwenden Sie den Schalter `-h`, um Hilfe zum `aspnet-codegenerator razorpage`-Befehl zu erhalten:</span><span class="sxs-lookup"><span data-stu-id="37170-197">Use the `-h` switch for help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet aspnet-codegenerator razorpage -h
```

<span data-ttu-id="37170-198">Unter [Erstellen des Gerüsts für das Filmmodell](xref:tutorials/razor-pages/model) finden Sie ein Beispiel für `dotnet aspnet-codegenerator razorpage`.</span><span class="sxs-lookup"><span data-stu-id="37170-198">See [Scaffold the movie model](xref:tutorials/razor-pages/model) for an example of `dotnet aspnet-codegenerator razorpage`.</span></span>

### Identity

<span data-ttu-id="37170-199">Siehe [Gerüst Identity](xref:security/authentication/scaffold-identity)</span><span class="sxs-lookup"><span data-stu-id="37170-199">See [Scaffold Identity](xref:security/authentication/scaffold-identity)</span></span>
