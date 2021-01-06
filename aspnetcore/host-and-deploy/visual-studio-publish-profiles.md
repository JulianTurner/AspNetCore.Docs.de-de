---
title: Visual Studio-Veröffentlichungsprofile (PUBXML) für die Bereitstellung von ASP.NET Core-Apps
author: rick-anderson
description: Informationen zum Erstellen von Veröffentlichungsprofilen in Visual Studio und zu deren Verwendung zum Verwalten von Bereitstellungen von ASP.NET Core-Apps an verschiedene Ziele.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/28/2020
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
uid: host-and-deploy/visual-studio-publish-profiles
ms.openlocfilehash: eae4a19042efded03f10e9ebd17122232f0323eb
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/04/2021
ms.locfileid: "97854638"
---
# <a name="visual-studio-publish-profiles-pubxml-for-aspnet-core-app-deployment"></a><span data-ttu-id="c4176-103">Visual Studio-Veröffentlichungsprofile (PUBXML) für die Bereitstellung von ASP.NET Core-Apps</span><span class="sxs-lookup"><span data-stu-id="c4176-103">Visual Studio publish profiles (.pubxml) for ASP.NET Core app deployment</span></span>

<span data-ttu-id="c4176-104">Von [Sayed Ibrahim Hashimi](https://github.com/sayedihashimi) und [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="c4176-104">By [Sayed Ibrahim Hashimi](https://github.com/sayedihashimi) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="c4176-105">Dieser Artikel konzertiert sich auf die Verwendung von Visual Studio 2019 oder höher zum Erstellen und Verwenden von Veröffentlichungsprofilen.</span><span class="sxs-lookup"><span data-stu-id="c4176-105">This document focuses on using Visual Studio 2019 or later to create and use publish profiles.</span></span> <span data-ttu-id="c4176-106">Die Veröffentlichungsprofile, die mit Visual Studio erstellt werden, können mit MSBuild und Visual Studio verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="c4176-106">The publish profiles created with Visual Studio can be used with MSBuild and Visual Studio.</span></span> <span data-ttu-id="c4176-107">Anweisungen zum Veröffentlichen in Azure finden Sie unter <xref:tutorials/publish-to-azure-webapp-using-vs>.</span><span class="sxs-lookup"><span data-stu-id="c4176-107">For instructions on publishing to Azure, see <xref:tutorials/publish-to-azure-webapp-using-vs>.</span></span>

<span data-ttu-id="c4176-108">Der Befehl `dotnet new mvc` generiert eine Projektdatei, die das folgende [\<Project>-Element](/visualstudio/msbuild/project-element-msbuild) auf Stammebene enthält:</span><span class="sxs-lookup"><span data-stu-id="c4176-108">The `dotnet new mvc` command produces a project file containing the following root-level [\<Project> element](/visualstudio/msbuild/project-element-msbuild):</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
    <!-- omitted for brevity -->
</Project>
```

<span data-ttu-id="c4176-109">Das `Sdk`-Attribut des vorhergehenden `<Project>`-Elements importiert die [Eigenschaften](/visualstudio/msbuild/msbuild-properties) und [Ziele](/visualstudio/msbuild/msbuild-targets) von MSBuild aus *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.reps* bzw. *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk*.</span><span class="sxs-lookup"><span data-stu-id="c4176-109">The preceding `<Project>` element's `Sdk` attribute imports the MSBuild [properties](/visualstudio/msbuild/msbuild-properties) and [targets](/visualstudio/msbuild/msbuild-targets) from *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.props* and *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.targets*, respectively.</span></span> <span data-ttu-id="c4176-110">Der Standardspeicherort für `$(MSBuildSDKsPath)` (mit Visual Studio 2019 Enterprise) ist der Ordner *%programfiles(x86)%\Microsoft Visual Studio\2019\Enterprise\MSBuild\Sdks*.</span><span class="sxs-lookup"><span data-stu-id="c4176-110">The default location for `$(MSBuildSDKsPath)` (with Visual Studio 2019 Enterprise) is the *%programfiles(x86)%\Microsoft Visual Studio\2019\Enterprise\MSBuild\Sdks* folder.</span></span>

<span data-ttu-id="c4176-111">`Microsoft.NET.Sdk.Web` ([Web-SDK](xref:razor-pages/web-sdk)) hängt von anderen SDKs ab, einschließlich `Microsoft.NET.Sdk` ([.NET Core SDK](/dotnet/core/project-sdk/msbuild-props)) und `Microsoft.NET.Sdk.Razor` ([Razor SDK](xref:razor-pages/sdk)).</span><span class="sxs-lookup"><span data-stu-id="c4176-111">`Microsoft.NET.Sdk.Web` ([Web SDK](xref:razor-pages/web-sdk)) depends on other SDKs, including `Microsoft.NET.Sdk` ([.NET Core SDK](/dotnet/core/project-sdk/msbuild-props)) and `Microsoft.NET.Sdk.Razor` ([Razor SDK](xref:razor-pages/sdk)).</span></span> <span data-ttu-id="c4176-112">Die MSBuild-Eigenschaften und -Ziele, die jedem abhängigen SDK zugeordnet sind, werden importiert.</span><span class="sxs-lookup"><span data-stu-id="c4176-112">The MSBuild properties and targets associated with each dependent SDK are imported.</span></span> <span data-ttu-id="c4176-113">Veröffentlichungsziele importieren basierend auf der verwendeten Veröffentlichungsmethode die entsprechende Gruppe von Zielen.</span><span class="sxs-lookup"><span data-stu-id="c4176-113">Publish targets import the appropriate set of targets based on the publish method used.</span></span>

<span data-ttu-id="c4176-114">Wenn in MSBuild oder Visual Studio ein Projekt geladen wird, werden die folgenden Aktionen auf hoher Ebene ausgeführt:</span><span class="sxs-lookup"><span data-stu-id="c4176-114">When MSBuild or Visual Studio loads a project, the following high-level actions occur:</span></span>

* <span data-ttu-id="c4176-115">Erstellen des Projekts</span><span class="sxs-lookup"><span data-stu-id="c4176-115">Build project</span></span>
* <span data-ttu-id="c4176-116">Berechnen der zu veröffentlichenden Dateien</span><span class="sxs-lookup"><span data-stu-id="c4176-116">Compute files to publish</span></span>
* <span data-ttu-id="c4176-117">Veröffentlichen der Dateien auf dem Ziel</span><span class="sxs-lookup"><span data-stu-id="c4176-117">Publish files to destination</span></span>

## <a name="compute-project-items"></a><span data-ttu-id="c4176-118">Berechnen der Projektelemente</span><span class="sxs-lookup"><span data-stu-id="c4176-118">Compute project items</span></span>

<span data-ttu-id="c4176-119">Wenn das Projekt geladen ist, werden die [MSBuild-Projektelemente](/visualstudio/msbuild/common-msbuild-project-items) (Dateien) berechnet.</span><span class="sxs-lookup"><span data-stu-id="c4176-119">When the project is loaded, the [MSBuild project items](/visualstudio/msbuild/common-msbuild-project-items) (files) are computed.</span></span> <span data-ttu-id="c4176-120">Der Elementtyp bestimmt, wie die Datei verarbeitet wird.</span><span class="sxs-lookup"><span data-stu-id="c4176-120">The item type determines how the file is processed.</span></span> <span data-ttu-id="c4176-121">Standardmäßig sind *CS*-Dateien in der `Compile`-Elementliste enthalten.</span><span class="sxs-lookup"><span data-stu-id="c4176-121">By default, *.cs* files are included in the `Compile` item list.</span></span> <span data-ttu-id="c4176-122">Dateien in der `Compile`-Elementliste werden kompiliert.</span><span class="sxs-lookup"><span data-stu-id="c4176-122">Files in the `Compile` item list are compiled.</span></span>

<span data-ttu-id="c4176-123">Die `Content`-Elementliste enthält Dateien, die zusätzlich zu den Buildausgaben veröffentlicht werden.</span><span class="sxs-lookup"><span data-stu-id="c4176-123">The `Content` item list contains files that are published in addition to the build outputs.</span></span> <span data-ttu-id="c4176-124">Standardmäßig sind Dateien, die mit den Mustern `wwwroot\**`, `**\*.config` und `**\*.json` übereinstimmen, in der Elementliste `Content` enthalten.</span><span class="sxs-lookup"><span data-stu-id="c4176-124">By default, files matching the patterns `wwwroot\**`, `**\*.config`, and `**\*.json` are included in the `Content` item list.</span></span> <span data-ttu-id="c4176-125">Das `wwwroot\**` [Globmuster](https://gruntjs.com/configuring-tasks#globbing-patterns) stimmt z. B. mit allen Dateien im *wwwroot*-Ordner und in dessen Unterordnern überein.</span><span class="sxs-lookup"><span data-stu-id="c4176-125">For example, the `wwwroot\**` [globbing pattern](https://gruntjs.com/configuring-tasks#globbing-patterns) matches all files in the *wwwroot* folder and its subfolders.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c4176-126">Das [Web-SDK](xref:razor-pages/web-sdk) importiert das [Razor SDK](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="c4176-126">The [Web SDK](xref:razor-pages/web-sdk) imports the [Razor SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="c4176-127">Demzufolge sind Dateien, die mit den Mustern `**\*.cshtml` und `**\*.razor` übereinstimmen, in der Elementliste `Content` enthalten.</span><span class="sxs-lookup"><span data-stu-id="c4176-127">As a result, files matching the patterns `**\*.cshtml` and `**\*.razor` are also included in the `Content` item list.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

<span data-ttu-id="c4176-128">Das [Web-SDK](xref:razor-pages/web-sdk) importiert das [Razor SDK](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="c4176-128">The [Web SDK](xref:razor-pages/web-sdk) imports the [Razor SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="c4176-129">Demzufolge sind Dateien, die mit dem Muster `**\*.cshtml` übereinstimmen, in der Elementliste `Content` enthalten.</span><span class="sxs-lookup"><span data-stu-id="c4176-129">As a result, files matching the `**\*.cshtml` pattern are also included in the `Content` item list.</span></span>

::: moniker-end

<span data-ttu-id="c4176-130">Um eine Datei explizit der Veröffentlichungsliste hinzuzufügen, fügen Sie die Datei, wie im Abschnitt [Includedateien](#include-files) gezeigt, direkt der *CSPROJ*-Datei hinzu.</span><span class="sxs-lookup"><span data-stu-id="c4176-130">To explicitly add a file to the publish list, add the file directly in the *.csproj* file as shown in the [Include Files](#include-files) section.</span></span>

<span data-ttu-id="c4176-131">Wenn Sie auf die Schaltfläche **Veröffentlichen** in Visual Studio klicken oder aus der Befehlszeile veröffentlichen:</span><span class="sxs-lookup"><span data-stu-id="c4176-131">When selecting the **Publish** button in Visual Studio or when publishing from the command line:</span></span>

* <span data-ttu-id="c4176-132">Die Eigenschaften und Elemente werden berechnet (die Dateien, die für den Build benötigt werden).</span><span class="sxs-lookup"><span data-stu-id="c4176-132">The properties/items are computed (the files that are needed to build).</span></span>
* <span data-ttu-id="c4176-133">**Nur Visual Studio**: NuGet-Pakete werden wiederhergestellt.</span><span class="sxs-lookup"><span data-stu-id="c4176-133">**Visual Studio only**: NuGet packages are restored.</span></span> <span data-ttu-id="c4176-134">(Die Wiederherstellung muss explizit vom Benutzer auf der CLI durchgeführt werden.)</span><span class="sxs-lookup"><span data-stu-id="c4176-134">(Restore needs to be explicit by the user on the CLI.)</span></span>
* <span data-ttu-id="c4176-135">Das Projekt wird erstellt.</span><span class="sxs-lookup"><span data-stu-id="c4176-135">The project builds.</span></span>
* <span data-ttu-id="c4176-136">Die zu veröffentlichenden Elemente werden berechnet (die Dateien, die für die Veröffentlichung benötigt werden).</span><span class="sxs-lookup"><span data-stu-id="c4176-136">The publish items are computed (the files that are needed to publish).</span></span>
* <span data-ttu-id="c4176-137">Das Projekt wird veröffentlicht (die berechneten Dateien werden in das Veröffentlichungsziel kopiert).</span><span class="sxs-lookup"><span data-stu-id="c4176-137">The project is published (the computed files are copied to the publish destination).</span></span>

<span data-ttu-id="c4176-138">Wenn ein ASP.NET Core-Projekt auf `Microsoft.NET.Sdk.Web` in der Projektdatei verweist, wird eine *app_offline.htm*-Datei im Stammverzeichnis des Verzeichnisses der Web-App abgelegt.</span><span class="sxs-lookup"><span data-stu-id="c4176-138">When an ASP.NET Core project references `Microsoft.NET.Sdk.Web` in the project file, an *app_offline.htm* file is placed at the root of the web app directory.</span></span> <span data-ttu-id="c4176-139">Wenn die Datei vorhanden ist, fährt das ASP.NET Core Module die App ordnungsgemäß herunter und verarbeitet die Datei *app_offline.htm* während der Bereitstellung.</span><span class="sxs-lookup"><span data-stu-id="c4176-139">When the file is present, the ASP.NET Core Module gracefully shuts down the app and serves the *app_offline.htm* file during the deployment.</span></span> <span data-ttu-id="c4176-140">Weitere Informationen finden Sie unter [Konfigurationsreferenz für das ASP.NET Core-Modul](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span><span class="sxs-lookup"><span data-stu-id="c4176-140">For more information, see the [ASP.NET Core Module configuration reference](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span></span>

## <a name="basic-command-line-publishing"></a><span data-ttu-id="c4176-141">Grundlegendes zur Veröffentlichung über die Befehlszeile</span><span class="sxs-lookup"><span data-stu-id="c4176-141">Basic command-line publishing</span></span>

<span data-ttu-id="c4176-142">Die Veröffentlichung über die Befehlszeile funktioniert auf allen von .NET Core unterstützten Plattformen und setzt Visual Studio nicht voraus.</span><span class="sxs-lookup"><span data-stu-id="c4176-142">Command-line publishing works on all .NET Core-supported platforms and doesn't require Visual Studio.</span></span> <span data-ttu-id="c4176-143">In den folgenden Beispielen wird der Befehl [dotnet publish](/dotnet/core/tools/dotnet-publish) der .NET Core-CLI im Projektverzeichnis ausgeführt (das die Datei *CSPROJ* enthält).</span><span class="sxs-lookup"><span data-stu-id="c4176-143">In the following examples, the .NET Core CLI's [dotnet publish](/dotnet/core/tools/dotnet-publish) command is run from the project directory (which contains the *.csproj* file).</span></span> <span data-ttu-id="c4176-144">Wenn der Projektordner nicht das aktuelle Arbeitsverzeichnis ist, übergeben Sie den Projektdateipfad explizit.</span><span class="sxs-lookup"><span data-stu-id="c4176-144">If the project folder isn't the current working directory, explicitly pass in the project file path.</span></span> <span data-ttu-id="c4176-145">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="c4176-145">For example:</span></span>

```dotnetcli
dotnet publish C:\Webs\Web1
```

<span data-ttu-id="c4176-146">Führen Sie die folgenden Befehle zum Erstellen und Veröffentlichen eine Web-App aus:</span><span class="sxs-lookup"><span data-stu-id="c4176-146">Run the following commands to create and publish a web app:</span></span>

```dotnetcli
dotnet new mvc
dotnet publish
```

<span data-ttu-id="c4176-147">Der Befehl `dotnet publish` erzeugt eine Abwandlung der folgenden Ausgabe:</span><span class="sxs-lookup"><span data-stu-id="c4176-147">The `dotnet publish` command produces a variation of the following output:</span></span>

```console
C:\Webs\Web1>dotnet publish
Microsoft (R) Build Engine version {VERSION} for .NET Core
Copyright (C) Microsoft Corporation. All rights reserved.

  Restore completed in 36.81 ms for C:\Webs\Web1\Web1.csproj.
  Web1 -> C:\Webs\Web1\bin\Debug\{TARGET FRAMEWORK MONIKER}\Web1.dll
  Web1 -> C:\Webs\Web1\bin\Debug\{TARGET FRAMEWORK MONIKER}\Web1.Views.dll
  Web1 -> C:\Webs\Web1\bin\Debug\{TARGET FRAMEWORK MONIKER}\publish\
```

<span data-ttu-id="c4176-148">Das Standardformat des Veröffentlichungsordners ist *bin\Debug\\{ZIELFRAMEWORKMONIKER}\publish\\* .</span><span class="sxs-lookup"><span data-stu-id="c4176-148">The default publish folder format is *bin\Debug\\{TARGET FRAMEWORK MONIKER}\publish\\*.</span></span> <span data-ttu-id="c4176-149">Dies ist dann z. B *bin\Debug\netcoreapp2.2\publish\\* .</span><span class="sxs-lookup"><span data-stu-id="c4176-149">For example, *bin\Debug\netcoreapp2.2\publish\\*.</span></span>

<span data-ttu-id="c4176-150">Der folgende Befehl gibt einen `Release`-Build und das Veröffentlichungsverzeichnis an:</span><span class="sxs-lookup"><span data-stu-id="c4176-150">The following command specifies a `Release` build and the publishing directory:</span></span>

```dotnetcli
dotnet publish -c Release -o C:\MyWebs\test
```

<span data-ttu-id="c4176-151">Der Befehl `dotnet publish` ruft MSBuild auf, welches das `Publish`-Ziel aufruft.</span><span class="sxs-lookup"><span data-stu-id="c4176-151">The `dotnet publish` command calls MSBuild, which invokes the `Publish` target.</span></span> <span data-ttu-id="c4176-152">Alle Parameter, die an `dotnet publish` übergeben werden, werden an MSBuild übergeben.</span><span class="sxs-lookup"><span data-stu-id="c4176-152">Any parameters passed to `dotnet publish` are passed to MSBuild.</span></span> <span data-ttu-id="c4176-153">Die Parameter `-c` und `-o` verweisen auf die MSBuild-Eigenschaften `Configuration` bzw. `OutputPath`.</span><span class="sxs-lookup"><span data-stu-id="c4176-153">The `-c` and `-o` parameters map to MSBuild's `Configuration` and `OutputPath` properties, respectively.</span></span>

<span data-ttu-id="c4176-154">MSBuild-Eigenschaften können mithilfe eines der folgenden Formate übergeben werden:</span><span class="sxs-lookup"><span data-stu-id="c4176-154">MSBuild properties can be passed using either of the following formats:</span></span>

* `-p:<NAME>=<VALUE>`
* `/p:<NAME>=<VALUE>`

<span data-ttu-id="c4176-155">Der folgende Befehl veröffentlicht z. B. einen `Release`-Build in einer Netzwerkfreigabe.</span><span class="sxs-lookup"><span data-stu-id="c4176-155">For example, the following command publishes a `Release` build to a network share.</span></span> <span data-ttu-id="c4176-156">Die Netzwerkfreigabe wird durch führende Schrägstriche ( *//r8/* ) angegeben und funktioniert auf allen Plattformen, die von .NET Core unterstützt werden.</span><span class="sxs-lookup"><span data-stu-id="c4176-156">The network share is specified with forward slashes (*//r8/*) and works on all .NET Core supported platforms.</span></span>

```dotnetcli
dotnet publish -c Release /p:PublishDir=//r8/release/AdminWeb
```

<span data-ttu-id="c4176-157">Versichern Sie sich, dass die veröffentlichte App für die Bereitstellung nicht ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="c4176-157">Confirm that the published app for deployment isn't running.</span></span> <span data-ttu-id="c4176-158">Die Dateien im Ordner *publish* sind gesperrt, wenn die App ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="c4176-158">Files in the *publish* folder are locked when the app is running.</span></span> <span data-ttu-id="c4176-159">Die Bereitstellung kann nicht erfolgen, da die gesperrten Dateien nicht kopiert werden können.</span><span class="sxs-lookup"><span data-stu-id="c4176-159">Deployment can't occur because locked files can't be copied.</span></span>

## <a name="publish-profiles"></a><span data-ttu-id="c4176-160">Veröffentlichungsprofile</span><span class="sxs-lookup"><span data-stu-id="c4176-160">Publish profiles</span></span>

<span data-ttu-id="c4176-161">In diesem Abschnitt wird Visual Studio 2019 oder höher verwendet, um ein Veröffentlichungsprofil zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="c4176-161">This section uses Visual Studio 2019 or later to create a publishing profile.</span></span> <span data-ttu-id="c4176-162">Nachdem das Profil erstellt ist, ist Veröffentlichung aus Visual Studio oder über die Befehlszeile möglich.</span><span class="sxs-lookup"><span data-stu-id="c4176-162">Once the profile is created, publishing from Visual Studio or the command line is available.</span></span> <span data-ttu-id="c4176-163">Veröffentlichungsprofile können den Veröffentlichungsprozess vereinfachen, und eine beliebige Anzahl von Profilen kann vorhanden sein.</span><span class="sxs-lookup"><span data-stu-id="c4176-163">Publish profiles can simplify the publishing process, and any number of profiles can exist.</span></span>

<span data-ttu-id="c4176-164">Erstellen Sie ein Veröffentlichungsprofil in Visual Studio durch Auswahl eines der folgenden Pfade:</span><span class="sxs-lookup"><span data-stu-id="c4176-164">Create a publish profile in Visual Studio by choosing one of the following paths:</span></span>

* <span data-ttu-id="c4176-165">Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt, und wählen Sie **Veröffentlichen** aus.</span><span class="sxs-lookup"><span data-stu-id="c4176-165">Right-click the project in **Solution Explorer** and select **Publish**.</span></span>
* <span data-ttu-id="c4176-166">Wählen Sie **{PROJEKTNAME} veröffentlichen** im Menü **Build** aus.</span><span class="sxs-lookup"><span data-stu-id="c4176-166">Select **Publish {PROJECT NAME}** from the **Build** menu.</span></span>

<span data-ttu-id="c4176-167">Die Registerkarte **Veröffentlichen** der Seite für App-Funktionen wird angezeigt.</span><span class="sxs-lookup"><span data-stu-id="c4176-167">The **Publish** tab of the app capabilities page is displayed.</span></span> <span data-ttu-id="c4176-168">Wenn das Projekt kein Veröffentlichungsprofil enthält, wird die Seite **Veröffentlichungsziel auswählen** angezeigt.</span><span class="sxs-lookup"><span data-stu-id="c4176-168">If the project lacks a publish profile, the **Pick a publish target** page is displayed.</span></span> <span data-ttu-id="c4176-169">Sie können aus den folgenden Veröffentlichungszielen wählen:</span><span class="sxs-lookup"><span data-stu-id="c4176-169">You're asked to select one of the following publish targets:</span></span>

* <span data-ttu-id="c4176-170">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="c4176-170">Azure App Service</span></span>
* <span data-ttu-id="c4176-171">Azure App Service für Linux</span><span class="sxs-lookup"><span data-stu-id="c4176-171">Azure App Service on Linux</span></span>
* <span data-ttu-id="c4176-172">Azure Virtual Machines</span><span class="sxs-lookup"><span data-stu-id="c4176-172">Azure Virtual Machines</span></span>
* <span data-ttu-id="c4176-173">Ordner</span><span class="sxs-lookup"><span data-stu-id="c4176-173">Folder</span></span>
* <span data-ttu-id="c4176-174">IIS, FTP, Web Deploy (für einen beliebigen Webserver)</span><span class="sxs-lookup"><span data-stu-id="c4176-174">IIS, FTP, Web Deploy (for any web server)</span></span>
* <span data-ttu-id="c4176-175">Profil importieren</span><span class="sxs-lookup"><span data-stu-id="c4176-175">Import Profile</span></span>

<span data-ttu-id="c4176-176">Unter [Übersicht über die Bereitstellung in Visual Studio](/visualstudio/ide/not-in-toc/web-publish-options) erfahren Sie, wie Sie das ideale Veröffentlichungsziel bestimmen.</span><span class="sxs-lookup"><span data-stu-id="c4176-176">To determine the most appropriate publish target, see [What publishing options are right for me](/visualstudio/ide/not-in-toc/web-publish-options).</span></span>

<span data-ttu-id="c4176-177">Wenn **Ordner** als Veröffentlichungsziel ausgewählt ist, geben Sie einen Ordnerpfad ein, um die veröffentlichten Objekte zu speichern.</span><span class="sxs-lookup"><span data-stu-id="c4176-177">When the **Folder** publish target is selected, specify a folder path to store the published assets.</span></span> <span data-ttu-id="c4176-178">Der Standardordnerpfad ist *bin\\{PROJEKTKONFIGURATION}\\{ZIELFRAMEWORKMONIKER}\publish\\* .</span><span class="sxs-lookup"><span data-stu-id="c4176-178">The default folder path is *bin\\{PROJECT CONFIGURATION}\\{TARGET FRAMEWORK MONIKER}\publish\\*.</span></span> <span data-ttu-id="c4176-179">Dies ist dann z. B. *bin\Release\netcoreapp2.2\publish\\* .</span><span class="sxs-lookup"><span data-stu-id="c4176-179">For example, *bin\Release\netcoreapp2.2\publish\\*.</span></span> <span data-ttu-id="c4176-180">Klicken Sie auf die Schaltfläche **Profil erstellen**, um den Vorgang abzuschließen.</span><span class="sxs-lookup"><span data-stu-id="c4176-180">Select the **Create Profile** button to finish.</span></span>

<span data-ttu-id="c4176-181">Sobald ein Veröffentlichungsprofil erstellt wurde, ändert sich der Inhalt der Registerkarte **Veröffentlichen**.</span><span class="sxs-lookup"><span data-stu-id="c4176-181">Once a publish profile is created, the **Publish** tab's content changes.</span></span> <span data-ttu-id="c4176-182">Das neu erstellte Profil wird in einer Dropdownliste angezeigt.</span><span class="sxs-lookup"><span data-stu-id="c4176-182">The newly created profile appears in a drop-down list.</span></span> <span data-ttu-id="c4176-183">Klicken Sie unter der Dropdownliste auf **Neues Profil erstellen**, um ein weiteres neues Profil zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="c4176-183">Below the drop-down list, select **Create new profile** to create another new profile.</span></span>

<span data-ttu-id="c4176-184">Das Visual Studio-Veröffentlichungstool erstellt die MSBuild-Datei *Properties/PublishProfiles/{PROFILNAME}.pubxml*, in der das Veröffentlichungsprofil beschrieben wird.</span><span class="sxs-lookup"><span data-stu-id="c4176-184">Visual Studio's publish tool produces a *Properties/PublishProfiles/{PROFILE NAME}.pubxml* MSBuild file describing the publish profile.</span></span> <span data-ttu-id="c4176-185">Die *PUBXML*-Datei:</span><span class="sxs-lookup"><span data-stu-id="c4176-185">The *.pubxml* file:</span></span>

* <span data-ttu-id="c4176-186">enthält Veröffentlichungskonfigurationseinstellungen und wird vom Veröffentlichungsprozess verwendet.</span><span class="sxs-lookup"><span data-stu-id="c4176-186">Contains publish configuration settings and is consumed by the publishing process.</span></span>
* <span data-ttu-id="c4176-187">kann geändert werden, um den Build- und Veröffentlichungsprozess anzupassen.</span><span class="sxs-lookup"><span data-stu-id="c4176-187">Can be modified to customize the build and publish process.</span></span>

<span data-ttu-id="c4176-188">Beim Veröffentlichen in einem Azure-Ziel enthält die *PUBXML*-Datei Ihre Azure-Abonnement-ID.</span><span class="sxs-lookup"><span data-stu-id="c4176-188">When publishing to an Azure target, the *.pubxml* file contains your Azure subscription identifier.</span></span> <span data-ttu-id="c4176-189">Bei diesem Zieltyp wird vom Hinzufügen der Datei zur Quellkontrolle abgeraten.</span><span class="sxs-lookup"><span data-stu-id="c4176-189">With that target type, adding this file to source control is discouraged.</span></span> <span data-ttu-id="c4176-190">Beim Veröffentlichen auf einem Nicht-Azure-Ziel kann die *PUBXML*-Datei eingecheckt werden.</span><span class="sxs-lookup"><span data-stu-id="c4176-190">When publishing to a non-Azure target, it's safe to check in the *.pubxml* file.</span></span>

<span data-ttu-id="c4176-191">Vertrauliche Informationen (z.B. das Kennwort für die Veröffentlichung) werden pro Benutzer/Computer verschlüsselt.</span><span class="sxs-lookup"><span data-stu-id="c4176-191">Sensitive information (like the publish password) is encrypted on a per user/machine level.</span></span> <span data-ttu-id="c4176-192">Sie werden in der Datei *Properties/PublishProfiles/{PROFILNAME}.pubxml.user* gespeichert.</span><span class="sxs-lookup"><span data-stu-id="c4176-192">It's stored in the *Properties/PublishProfiles/{PROFILE NAME}.pubxml.user* file.</span></span> <span data-ttu-id="c4176-193">Da diese Datei vertrauliche Informationen enthalten kann, sollte sie nicht in die Quellverwaltung eingecheckt werden.</span><span class="sxs-lookup"><span data-stu-id="c4176-193">Because this file can store sensitive information, it shouldn't be checked into source control.</span></span>

<span data-ttu-id="c4176-194">Mehr Informationen zum Veröffentlichen einer ASP.NET Core-Web-App finden Sie unter <xref:host-and-deploy/index>.</span><span class="sxs-lookup"><span data-stu-id="c4176-194">For an overview of how to publish an ASP.NET Core web app, see <xref:host-and-deploy/index>.</span></span> <span data-ttu-id="c4176-195">Die MSBuild-Tasks und -Ziele, die zum Veröffentlichen einer ASP.NET Core-Web-App erforderlich sind, sind als Open-Source-Code im Repository [dotnet/websdk](https://github.com/dotnet/websdk) verfügbar.</span><span class="sxs-lookup"><span data-stu-id="c4176-195">The MSBuild tasks and targets necessary to publish an ASP.NET Core web app are open-source in the [dotnet/websdk repository](https://github.com/dotnet/websdk).</span></span>

<span data-ttu-id="c4176-196">Die folgenden Befehle können Ordner, MSDeploy und [Kudu](https://github.com/projectkudu/kudu/wiki)-Veröffentlichungsprofile verwenden.</span><span class="sxs-lookup"><span data-stu-id="c4176-196">The following commands can use folder, MSDeploy, and [Kudu](https://github.com/projectkudu/kudu/wiki) publish profiles.</span></span> <span data-ttu-id="c4176-197">Da MSDeploy nicht plattformübergreifend unterstützt wird, werden die folgenden MSDeploy-Optionen nur unter Windows unterstützt:</span><span class="sxs-lookup"><span data-stu-id="c4176-197">Because MSDeploy lacks cross-platform support, the following MSDeploy options are supported only on Windows.</span></span>

<span data-ttu-id="c4176-198">**Ordner (funktioniert plattformübergreifend):**</span><span class="sxs-lookup"><span data-stu-id="c4176-198">**Folder (works cross-platform):**</span></span>

```dotnetcli
dotnet publish WebApplication.csproj /p:PublishProfile=<FolderProfileName>
```

```dotnetcli
dotnet build WebApplication.csproj /p:DeployOnBuild=true /p:PublishProfile=<FolderProfileName>
```

<span data-ttu-id="c4176-199">**MSDeploy:**</span><span class="sxs-lookup"><span data-stu-id="c4176-199">**MSDeploy:**</span></span>

```dotnetcli
dotnet publish WebApplication.csproj /p:PublishProfile=<MsDeployProfileName> /p:Password=<DeploymentPassword>
```

```dotnetcli
dotnet build WebApplication.csproj /p:DeployOnBuild=true /p:PublishProfile=<MsDeployProfileName> /p:Password=<DeploymentPassword>
```

<span data-ttu-id="c4176-200">**MSDeploy-Paket:**</span><span class="sxs-lookup"><span data-stu-id="c4176-200">**MSDeploy package:**</span></span>

```dotnetcli
dotnet publish WebApplication.csproj /p:PublishProfile=<MsDeployPackageProfileName>
```

```dotnetcli
dotnet build WebApplication.csproj /p:DeployOnBuild=true /p:PublishProfile=<MsDeployPackageProfileName>
```

<span data-ttu-id="c4176-201">In den vorherigen Beispielen:</span><span class="sxs-lookup"><span data-stu-id="c4176-201">In the preceding examples:</span></span>

* <span data-ttu-id="c4176-202">`dotnet publish` und `dotnet build` unterstützen Kudu-APIs zur Veröffentlichung in Azure von jeder Plattform aus.</span><span class="sxs-lookup"><span data-stu-id="c4176-202">`dotnet publish` and `dotnet build` support Kudu APIs to publish to Azure from any platform.</span></span> <span data-ttu-id="c4176-203">Die Visual Studio-Veröffentlichung unterstützt die Kudu-APIs, wird für die plattformübergreifende Veröffentlichung in Azure aber vom WebSDK unterstützt.</span><span class="sxs-lookup"><span data-stu-id="c4176-203">Visual Studio publish supports the Kudu APIs, but it's supported by WebSDK for cross-platform publish to Azure.</span></span>
* <span data-ttu-id="c4176-204">Übergeben Sie `DeployOnBuild` nicht an den `dotnet publish`-Befehl.</span><span class="sxs-lookup"><span data-stu-id="c4176-204">Don't pass `DeployOnBuild` to the `dotnet publish` command.</span></span>

<span data-ttu-id="c4176-205">Weitere Informationen finden Sie unter [Microsoft.NET.Sdk.Publish](https://github.com/dotnet/websdk#microsoftnetsdkpublish).</span><span class="sxs-lookup"><span data-stu-id="c4176-205">For more information, see [Microsoft.NET.Sdk.Publish](https://github.com/dotnet/websdk#microsoftnetsdkpublish).</span></span>

<span data-ttu-id="c4176-206">Fügen Sie dem Projektordner *Properties/PublishProfiles* ein Veröffentlichungsprofil mit folgendem Inhalt hinzu:</span><span class="sxs-lookup"><span data-stu-id="c4176-206">Add a publish profile to the project's *Properties/PublishProfiles* folder with the following content:</span></span>

```xml
<Project>
  <PropertyGroup>
    <PublishProtocol>Kudu</PublishProtocol>
    <PublishSiteName>nodewebapp</PublishSiteName>
    <UserName>username</UserName>
    <Password>password</Password>
  </PropertyGroup>
</Project>
```

## <a name="folder-publish-example"></a><span data-ttu-id="c4176-207">Beispiel für Ordnerveröffentlichung</span><span class="sxs-lookup"><span data-stu-id="c4176-207">Folder publish example</span></span>

<span data-ttu-id="c4176-208">Verwenden Sie einen der folgenden Befehle, wenn Sie mit einem Profil namens *FolderProfile* veröffentlichen:</span><span class="sxs-lookup"><span data-stu-id="c4176-208">When publishing with a profile named *FolderProfile*, use any of the following commands:</span></span>

```dotnetcli
dotnet publish /p:Configuration=Release /p:PublishProfile=FolderProfile
```

```dotnetcli
dotnet build /p:DeployOnBuild=true /p:PublishProfile=FolderProfile
```

```bash
msbuild /p:DeployOnBuild=true /p:PublishProfile=FolderProfile
```

<span data-ttu-id="c4176-209">Der .NET Core-CLI-Befehl [dotnet build](/dotnet/core/tools/dotnet-build) ruft `msbuild` auf, um den Build- und Veröffentlichungsprozess auszuführen.</span><span class="sxs-lookup"><span data-stu-id="c4176-209">The .NET Core CLI's [dotnet build](/dotnet/core/tools/dotnet-build) command calls `msbuild` to run the build and publish process.</span></span> <span data-ttu-id="c4176-210">Die Befehle `dotnet build` und `msbuild` sind bei der Übergabe eines Ordnerprofils gleichwertig.</span><span class="sxs-lookup"><span data-stu-id="c4176-210">The `dotnet build` and `msbuild` commands are equivalent when passing in a folder profile.</span></span> <span data-ttu-id="c4176-211">Wenn Sie `msbuild` direkt in Windows aufrufen, wird die .NET Framework-Version von MSBuild verwendet.</span><span class="sxs-lookup"><span data-stu-id="c4176-211">When calling `msbuild` directly on Windows, the .NET Framework version of MSBuild is used.</span></span> <span data-ttu-id="c4176-212">Wenn Sie `dotnet build` in einem Profil ohne Ordner aufrufen:</span><span class="sxs-lookup"><span data-stu-id="c4176-212">Calling `dotnet build` on a non-folder profile:</span></span>

* <span data-ttu-id="c4176-213">wird das `msbuild`-Objekt aufgerufen, das MSDeploy verwendet.</span><span class="sxs-lookup"><span data-stu-id="c4176-213">Invokes `msbuild`, which uses MSDeploy.</span></span>
* <span data-ttu-id="c4176-214">führt dies zu einem Fehler (auch unter Windows).</span><span class="sxs-lookup"><span data-stu-id="c4176-214">Results in a failure (even when running on Windows).</span></span> <span data-ttu-id="c4176-215">Rufen Sie `msbuild` direkt auf, um eine Veröffentlichung mit einem Profil ohne Ordner durchzuführen.</span><span class="sxs-lookup"><span data-stu-id="c4176-215">To publish with a non-folder profile, call `msbuild` directly.</span></span>

<span data-ttu-id="c4176-216">Der folgende Ordner „Veröffentlichungsprofil“ wurde mit Visual Studio erstellt und veröffentlicht in eine Netzwerkfreigabe.</span><span class="sxs-lookup"><span data-stu-id="c4176-216">The following folder publish profile was created with Visual Studio and publishes to a network share:</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<!--
This file is used by the publish/package process of your Web project.
You can customize the behavior of this process by editing this 
MSBuild file.
-->
<Project ToolsVersion="4.0" xmlns="http://schemas.microsoft.com/developer/msbuild/2003">
  <PropertyGroup>
    <WebPublishMethod>FileSystem</WebPublishMethod>
    <PublishProvider>FileSystem</PublishProvider>
    <LastUsedBuildConfiguration>Release</LastUsedBuildConfiguration>
    <LastUsedPlatform>Any CPU</LastUsedPlatform>
    <SiteUrlToLaunchAfterPublish />
    <LaunchSiteAfterPublish>True</LaunchSiteAfterPublish>
    <ExcludeApp_Data>False</ExcludeApp_Data>
    <PublishFramework>netcoreapp1.1</PublishFramework>
    <ProjectGuid>c30c453c-312e-40c4-aec9-394a145dee0b</ProjectGuid>
    <publishUrl>\\r8\Release\AdminWeb</publishUrl>
    <DeleteExistingFiles>False</DeleteExistingFiles>
  </PropertyGroup>
</Project>
```

<span data-ttu-id="c4176-217">Im vorherigen Beispiel:</span><span class="sxs-lookup"><span data-stu-id="c4176-217">In the preceding example:</span></span>

* <span data-ttu-id="c4176-218">Die Eigenschaft `<ExcludeApp_Data>` ist nur vorhanden, um die XML-Schemaanforderung zu erfüllen.</span><span class="sxs-lookup"><span data-stu-id="c4176-218">The `<ExcludeApp_Data>` property is present merely to satisfy an XML schema requirement.</span></span> <span data-ttu-id="c4176-219">Die Eigenschaft `<ExcludeApp_Data>` wirkt sich nicht auf den Veröffentlichungsprozess aus, auch wenn es den Ordner *App_Data* am Projektstamm gibt.</span><span class="sxs-lookup"><span data-stu-id="c4176-219">The `<ExcludeApp_Data>` property has no effect on the publish process, even if there's an *App_Data* folder in the project root.</span></span> <span data-ttu-id="c4176-220">Der Ordner *App_Data* wird genauso wie in ASP.NET 4.x-Projekten behandelt.</span><span class="sxs-lookup"><span data-stu-id="c4176-220">The *App_Data* folder doesn't receive special treatment as it does in ASP.NET 4.x projects.</span></span>
* <span data-ttu-id="c4176-221">Die `<LastUsedBuildConfiguration>`-Eigenschaft ist auf `Release` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="c4176-221">The `<LastUsedBuildConfiguration>` property is set to `Release`.</span></span> <span data-ttu-id="c4176-222">Bei der Veröffentlichung von Visual Studio wird der `<LastUsedBuildConfiguration>`-Wert mithilfe des Werts festgelegt, der beim Starten des Veröffentlichungsprozesses vorliegt.</span><span class="sxs-lookup"><span data-stu-id="c4176-222">When publishing from Visual Studio, the value of `<LastUsedBuildConfiguration>` is set using the value when the publish process is started.</span></span> <span data-ttu-id="c4176-223">`<LastUsedBuildConfiguration>` ist ein Sonderfall und sollte nicht in einer importierten MSBuild-Datei überschrieben werden.</span><span class="sxs-lookup"><span data-stu-id="c4176-223">`<LastUsedBuildConfiguration>` is special and shouldn't be overridden in an imported MSBuild file.</span></span> <span data-ttu-id="c4176-224">Diese Eigenschaft kann aber über die Befehlszeile mit einer der folgenden Vorgehensweisen überschrieben werden.</span><span class="sxs-lookup"><span data-stu-id="c4176-224">This property can, however, be overridden from the command line using one of the following approaches.</span></span>
  * <span data-ttu-id="c4176-225">Mit der .NET Core-CLI:</span><span class="sxs-lookup"><span data-stu-id="c4176-225">Using the .NET Core CLI:</span></span>

    ```dotnetcli
    dotnet publish /p:Configuration=Release /p:PublishProfile=FolderProfile
    ```

    ```dotnetcli
    dotnet build -c Release /p:DeployOnBuild=true /p:PublishProfile=FolderProfile
    ```

  * <span data-ttu-id="c4176-226">Verwenden von MSBuild:</span><span class="sxs-lookup"><span data-stu-id="c4176-226">Using MSBuild:</span></span>

    ```bash
    msbuild /p:Configuration=Release /p:DeployOnBuild=true /p:PublishProfile=FolderProfile
    ```

  <span data-ttu-id="c4176-227">Weitere Informationen finden Sie unter [MSBuild: how to set the configuration property (MSBuild: Festlegen der Konfigurationseigenschaft)](http://sedodream.com/2012/10/27/MSBuildHowToSetTheConfigurationProperty.aspx).</span><span class="sxs-lookup"><span data-stu-id="c4176-227">For more information, see [MSBuild: how to set the configuration property](http://sedodream.com/2012/10/27/MSBuildHowToSetTheConfigurationProperty.aspx).</span></span>

## <a name="publish-to-an-msdeploy-endpoint-from-the-command-line"></a><span data-ttu-id="c4176-228">Veröffentlichen auf einen MSDeploy-Endpunkt von der Befehlszeile</span><span class="sxs-lookup"><span data-stu-id="c4176-228">Publish to an MSDeploy endpoint from the command line</span></span>

<span data-ttu-id="c4176-229">Im folgenden Beispiel wird von Visual Studio eine ASP.NET Core-Web-App namens *AzureWebApp* erstellt.</span><span class="sxs-lookup"><span data-stu-id="c4176-229">The following example uses an ASP.NET Core web app created by Visual Studio named *AzureWebApp*.</span></span> <span data-ttu-id="c4176-230">Ein Azure Apps-Veröffentlichungsprofil wird mit Visual Studio hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="c4176-230">An Azure Apps publish profile is added with Visual Studio.</span></span> <span data-ttu-id="c4176-231">Weitere Informationen zum Erstellen eines Profils finden Sie unter im Abschnitt [Veröffentlichungsprofile](#publish-profiles).</span><span class="sxs-lookup"><span data-stu-id="c4176-231">For more information on how to create a profile, see the [Publish profiles](#publish-profiles) section.</span></span>

<span data-ttu-id="c4176-232">Um eine App mit einem Veröffentlichungsprofil bereitzustellen, führen Sie den Befehl `msbuild` an einer **Developer-Eingabeaufforderung** von Visual Studio aus.</span><span class="sxs-lookup"><span data-stu-id="c4176-232">To deploy the app using a publish profile, execute the `msbuild` command from a Visual Studio **Developer Command Prompt**.</span></span> <span data-ttu-id="c4176-233">Die Eingabeaufforderung finden Sie im Ordner *Visual Studio* im **Start**-Menü auf der Windows-Taskleiste.</span><span class="sxs-lookup"><span data-stu-id="c4176-233">The command prompt is available in the *Visual Studio* folder of the **Start** menu on the Windows taskbar.</span></span> <span data-ttu-id="c4176-234">Für einfacheren Zugriff können Sie die Eingabeaufforderung zum Menü **Tools** in Visual Studio hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="c4176-234">For easier access, you can add the command prompt to the **Tools** menu in Visual Studio.</span></span> <span data-ttu-id="c4176-235">Weitere Informationen finden Sie unter [Developer-Eingabeaufforderung für Visual Studio](/dotnet/framework/tools/developer-command-prompt-for-vs#run-the-command-prompt-from-inside-visual-studio).</span><span class="sxs-lookup"><span data-stu-id="c4176-235">For more information, see [Developer Command Prompt for Visual Studio](/dotnet/framework/tools/developer-command-prompt-for-vs#run-the-command-prompt-from-inside-visual-studio).</span></span>

<span data-ttu-id="c4176-236">MSBuild hat die folgende Befehlssyntax:</span><span class="sxs-lookup"><span data-stu-id="c4176-236">MSBuild uses the following command syntax:</span></span>

```bash
msbuild {PATH} 
    /p:DeployOnBuild=true 
    /p:PublishProfile={PROFILE} 
    /p:Username={USERNAME} 
    /p:Password={PASSWORD}
```

* <span data-ttu-id="c4176-237">`{PATH}`: Der Pfad zur Projektdatei der App.</span><span class="sxs-lookup"><span data-stu-id="c4176-237">`{PATH}`: Path to the app's project file.</span></span>
* <span data-ttu-id="c4176-238">`{PROFILE}`: Der Name des Veröffentlichungsprofils.</span><span class="sxs-lookup"><span data-stu-id="c4176-238">`{PROFILE}`: Name of the publish profile.</span></span>
* <span data-ttu-id="c4176-239">`{USERNAME}`: Der MSDeploy-Benutzername.</span><span class="sxs-lookup"><span data-stu-id="c4176-239">`{USERNAME}`: MSDeploy username.</span></span> <span data-ttu-id="c4176-240">`{USERNAME}` befindet sich im Veröffentlichungsprofil.</span><span class="sxs-lookup"><span data-stu-id="c4176-240">The `{USERNAME}` can be found in the publish profile.</span></span>
* <span data-ttu-id="c4176-241">`{PASSWORD}`: Das MSDeploy-Kennwort.</span><span class="sxs-lookup"><span data-stu-id="c4176-241">`{PASSWORD}`: MSDeploy password.</span></span> <span data-ttu-id="c4176-242">`{PASSWORD}` befindet sich in der Datei *{PROFILE}.PublishSettings*.</span><span class="sxs-lookup"><span data-stu-id="c4176-242">Obtain the `{PASSWORD}` from the *{PROFILE}.PublishSettings* file.</span></span> <span data-ttu-id="c4176-243">Laden Sie die Datei *.PublishSettings* von einem der folgenden Speicherorte herunter:</span><span class="sxs-lookup"><span data-stu-id="c4176-243">Download the *.PublishSettings* file from either:</span></span>
  * <span data-ttu-id="c4176-244">**Projektmappen-Explorer:** Wählen Sie **Ansicht** > **Cloud-Explorer** aus.</span><span class="sxs-lookup"><span data-stu-id="c4176-244">**Solution Explorer**: Select **View** > **Cloud Explorer**.</span></span> <span data-ttu-id="c4176-245">Stellen Sie eine Verbindung mit Ihrem Azure-Abonnement her.</span><span class="sxs-lookup"><span data-stu-id="c4176-245">Connect with your Azure subscription.</span></span> <span data-ttu-id="c4176-246">Öffnen Sie **App Services**.</span><span class="sxs-lookup"><span data-stu-id="c4176-246">Open **App Services**.</span></span> <span data-ttu-id="c4176-247">Klicken Sie mit der rechten Maustaste auf die App.</span><span class="sxs-lookup"><span data-stu-id="c4176-247">Right-click the app.</span></span> <span data-ttu-id="c4176-248">Wählen Sie **Veröffentlichungsprofil herunterladen** aus.</span><span class="sxs-lookup"><span data-stu-id="c4176-248">Select **Download Publish Profile**.</span></span>
  * <span data-ttu-id="c4176-249">Azure-Portal: Wählen Sie **Veröffentlichungsprofil abrufen** im Bereich **Übersicht** der Web-App aus.</span><span class="sxs-lookup"><span data-stu-id="c4176-249">Azure portal: Select **Get publish profile** in the web app's **Overview** panel.</span></span>

<span data-ttu-id="c4176-250">Im folgenden Beispiel wird ein Veröffentlichungsprofil namens *AzureWebApp – Web Deploy* verwendet:</span><span class="sxs-lookup"><span data-stu-id="c4176-250">The following example uses a publish profile named *AzureWebApp - Web Deploy*:</span></span>

```bash
msbuild "AzureWebApp.csproj" 
    /p:DeployOnBuild=true 
    /p:PublishProfile="AzureWebApp - Web Deploy" 
    /p:Username="$AzureWebApp" 
    /p:Password=".........."
```

<span data-ttu-id="c4176-251">Ein Veröffentlichungsprofil kann auch mit dem .NET Core-CLI-Befehl [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) über eine Windows-Befehlsshell verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="c4176-251">A publish profile can also be used with the .NET Core CLI's [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) command from a Windows command shell:</span></span>

```dotnetcli
dotnet msbuild "AzureWebApp.csproj"
    /p:DeployOnBuild=true 
    /p:PublishProfile="AzureWebApp - Web Deploy" 
    /p:Username="$AzureWebApp" 
    /p:Password=".........."
```

> [!IMPORTANT]
> <span data-ttu-id="c4176-252">Der Befehl `dotnet msbuild` ist plattformübergreifend verfügbar und kann ASP.NET Core-Apps unter macOS und Linux kompilieren.</span><span class="sxs-lookup"><span data-stu-id="c4176-252">The `dotnet msbuild` command is a cross-platform command and can compile ASP.NET Core apps on macOS and Linux.</span></span> <span data-ttu-id="c4176-253">Allerdings kann MSBuild unter macOS und Linux keine Apps in Azure oder an einem anderen MSDeploy-Endpunkt bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="c4176-253">However, MSBuild on macOS and Linux isn't capable of deploying an app to Azure or other MSDeploy endpoints.</span></span>

## <a name="set-the-environment"></a><span data-ttu-id="c4176-254">Festlegen der Umgebung</span><span class="sxs-lookup"><span data-stu-id="c4176-254">Set the environment</span></span>

<span data-ttu-id="c4176-255">Beziehen Sie die `<EnvironmentName>`-Eigenschaft in das Veröffentlichungsprofil (*PUBXML*) oder die Projektdatei ein, um die [Umgebung](xref:fundamentals/environments) der App festzulegen:</span><span class="sxs-lookup"><span data-stu-id="c4176-255">Include the `<EnvironmentName>` property in the publish profile (*.pubxml*) or project file to set the app's [environment](xref:fundamentals/environments):</span></span>

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

<span data-ttu-id="c4176-256">Wenn Sie *web.config*-Transformationen benötigen (z.B. Umgebungsvariablen basierend auf der Konfiguration, dem Profil oder der Umgebung), finden Sie weitere Informationen unter <xref:host-and-deploy/iis/transform-webconfig>.</span><span class="sxs-lookup"><span data-stu-id="c4176-256">If you require *web.config* transformations (for example, setting environment variables based on the configuration, profile, or environment), see <xref:host-and-deploy/iis/transform-webconfig>.</span></span>

## <a name="exclude-files"></a><span data-ttu-id="c4176-257">Dateien ausschließen</span><span class="sxs-lookup"><span data-stu-id="c4176-257">Exclude files</span></span>

<span data-ttu-id="c4176-258">Wenn Sie ASP.NET Core-Web-Apps veröffentlichen, sind die folgenden Ressourcen enthalten:</span><span class="sxs-lookup"><span data-stu-id="c4176-258">When publishing ASP.NET Core web apps, the following assets are included:</span></span>

* <span data-ttu-id="c4176-259">Build-Artefakte</span><span class="sxs-lookup"><span data-stu-id="c4176-259">Build artifacts</span></span>
* <span data-ttu-id="c4176-260">Ordner und Dateien, die den folgenden Globmustern entsprechen:</span><span class="sxs-lookup"><span data-stu-id="c4176-260">Folders and files matching the following globbing patterns:</span></span>
  * <span data-ttu-id="c4176-261">`**\*.config` (Beispiel: *web.config*)</span><span class="sxs-lookup"><span data-stu-id="c4176-261">`**\*.config` (for example, *web.config*)</span></span>
  * <span data-ttu-id="c4176-262">`**\*.json` (Beispiel *appsettings.json* )</span><span class="sxs-lookup"><span data-stu-id="c4176-262">`**\*.json` (for example, *appsettings.json*)</span></span>
  * `wwwroot\**`

<span data-ttu-id="c4176-263">MSBuild unterstützt [Globmuster](https://gruntjs.com/configuring-tasks#globbing-patterns).</span><span class="sxs-lookup"><span data-stu-id="c4176-263">MSBuild supports [globbing patterns](https://gruntjs.com/configuring-tasks#globbing-patterns).</span></span> <span data-ttu-id="c4176-264">Das folgende `<Content>`-Element unterdrückt beispielsweise das Kopieren von Textdateien (*TXT*) aus dem Ordner *wwwroot/content* und allen seinen Unterordnern:</span><span class="sxs-lookup"><span data-stu-id="c4176-264">For example, the following `<Content>` element suppresses the copying of text (*.txt*) files in the *wwwroot\content* folder and its subfolders:</span></span>

```xml
<ItemGroup>
  <Content Update="wwwroot/content/**/*.txt" CopyToPublishDirectory="Never" />
</ItemGroup>
```

<span data-ttu-id="c4176-265">Das vorstehende Markup kann einem Veröffentlichungsprofil oder der *CSPROJ*-Datei hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="c4176-265">The preceding markup can be added to a publish profile or the *.csproj* file.</span></span> <span data-ttu-id="c4176-266">Wenn es zu der *CSPROJ*-Datei hinzugefügt wird, wird die Regel zu allen Veröffentlichungsprofilen im Projekt hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="c4176-266">When added to the *.csproj* file, the rule is added to all publish profiles in the project.</span></span>

<span data-ttu-id="c4176-267">Das folgende `<MsDeploySkipRules>`-Element schließt alle Dateien aus dem Ordner *wwwroot\content* aus:</span><span class="sxs-lookup"><span data-stu-id="c4176-267">The following `<MsDeploySkipRules>` element excludes all files from the *wwwroot\content* folder:</span></span>

```xml
<ItemGroup>
  <MsDeploySkipRules Include="CustomSkipFolder">
    <ObjectName>dirPath</ObjectName>
    <AbsolutePath>wwwroot\\content</AbsolutePath>
  </MsDeploySkipRules>
</ItemGroup>
```

<span data-ttu-id="c4176-268">`<MsDeploySkipRules>` löscht die *Skip*-Ziele von der Bereitstellungsseite nicht.</span><span class="sxs-lookup"><span data-stu-id="c4176-268">`<MsDeploySkipRules>` won't delete the *skip* targets from the deployment site.</span></span> <span data-ttu-id="c4176-269">`<Content>`-Zieldateien und -ordner werden von der Bereitstellungsseite gelöscht.</span><span class="sxs-lookup"><span data-stu-id="c4176-269">`<Content>` targeted files and folders are deleted from the deployment site.</span></span> <span data-ttu-id="c4176-270">Nehmen wir beispielsweise an, dass eine bereitgestellte Web-App folgende Dateien enthält:</span><span class="sxs-lookup"><span data-stu-id="c4176-270">For example, suppose a deployed web app had the following files:</span></span>

* <span data-ttu-id="c4176-271">*Views/Home/About1.cshtml*</span><span class="sxs-lookup"><span data-stu-id="c4176-271">*Views/Home/About1.cshtml*</span></span>
* <span data-ttu-id="c4176-272">*Views/Home/About2.cshtml*</span><span class="sxs-lookup"><span data-stu-id="c4176-272">*Views/Home/About2.cshtml*</span></span>
* <span data-ttu-id="c4176-273">*Views/Home/About3.cshtml*</span><span class="sxs-lookup"><span data-stu-id="c4176-273">*Views/Home/About3.cshtml*</span></span>

<span data-ttu-id="c4176-274">Wenn folgende `<MsDeploySkipRules>`-Elemente hinzugefügt werden, würden diese Dateien nicht von der Bereitstellungsseite gelöscht werden.</span><span class="sxs-lookup"><span data-stu-id="c4176-274">If the following `<MsDeploySkipRules>` elements are added, those files wouldn't be deleted on the deployment site.</span></span>

```xml
<ItemGroup>
  <MsDeploySkipRules Include="CustomSkipFile">
    <ObjectName>filePath</ObjectName>
    <AbsolutePath>Views\\Home\\About1.cshtml</AbsolutePath>
  </MsDeploySkipRules>

  <MsDeploySkipRules Include="CustomSkipFile">
    <ObjectName>filePath</ObjectName>
    <AbsolutePath>Views\\Home\\About2.cshtml</AbsolutePath>
  </MsDeploySkipRules>

  <MsDeploySkipRules Include="CustomSkipFile">
    <ObjectName>filePath</ObjectName>
    <AbsolutePath>Views\\Home\\About3.cshtml</AbsolutePath>
  </MsDeploySkipRules>
</ItemGroup>
```

<span data-ttu-id="c4176-275">Die vorangehenden `<MsDeploySkipRules>`-Elemente verhindern, dass *übersprungene* Dateien bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="c4176-275">The preceding `<MsDeploySkipRules>` elements prevent the *skipped* files from being deployed.</span></span> <span data-ttu-id="c4176-276">Diese Dateien werden nach ihrer Bereitstellung nicht gelöscht.</span><span class="sxs-lookup"><span data-stu-id="c4176-276">It won't delete those files once they're deployed.</span></span>

<span data-ttu-id="c4176-277">Das folgende `<Content>`-Element löscht die Zieldateien auf der Bereitstellungsseite:</span><span class="sxs-lookup"><span data-stu-id="c4176-277">The following `<Content>` element deletes the targeted files at the deployment site:</span></span>

```xml
<ItemGroup>
  <Content Update="Views/Home/About?.cshtml" CopyToPublishDirectory="Never" />
</ItemGroup>
```

<span data-ttu-id="c4176-278">Die Bereitstellung über die Befehlszeile mit dem vorangehenden `<Content>`-Element gibt eine Abwandlung der folgenden Ausgabe aus:</span><span class="sxs-lookup"><span data-stu-id="c4176-278">Using command-line deployment with the preceding `<Content>` element yields a variation of the following output:</span></span>

```console
MSDeployPublish:
  Starting Web deployment task from source: manifest(C:\Webs\Web1\obj\Release\{TARGET FRAMEWORK MONIKER}\PubTmp\Web1.SourceManifest.
  xml) to Destination: auto().
  Deleting file (Web11112\Views\Home\About1.cshtml).
  Deleting file (Web11112\Views\Home\About2.cshtml).
  Deleting file (Web11112\Views\Home\About3.cshtml).
  Updating file (Web11112\web.config).
  Updating file (Web11112\Web1.deps.json).
  Updating file (Web11112\Web1.dll).
  Updating file (Web11112\Web1.pdb).
  Updating file (Web11112\Web1.runtimeconfig.json).
  Successfully executed Web deployment task.
  Publish Succeeded.
Done Building Project "C:\Webs\Web1\Web1.csproj" (default targets).
```

## <a name="include-files"></a><span data-ttu-id="c4176-279">Includedateien</span><span class="sxs-lookup"><span data-stu-id="c4176-279">Include files</span></span>

<span data-ttu-id="c4176-280">Die folgenden Abschnitte beschreiben verschiedene Ansätze für das Einbeziehen von Dateien beim Veröffentlichen.</span><span class="sxs-lookup"><span data-stu-id="c4176-280">The following sections outline different approaches for file inclusion at publish time.</span></span> <span data-ttu-id="c4176-281">Im Abschnitt [Allgemeines Einbeziehen von Dateien](#general-file-inclusion) wird das Element `DotNetPublishFiles` verwendet, das von einer Veröffentlichungszieledatei im [Web-SDK](xref:razor-pages/web-sdk) bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="c4176-281">The [General file inclusion](#general-file-inclusion) section uses the `DotNetPublishFiles` item, which is provided by a publish targets file in the [Web SDK](xref:razor-pages/web-sdk).</span></span> <span data-ttu-id="c4176-282">Im Abschnitt [Selektives Einbeziehen von Dateien](#selective-file-inclusion) wird das Element `ResolvedFileToPublish` verwendet, das von einer Veröffentlichungszieledatei im [.NET Core SDK](/dotnet/core/project-sdk/msbuild-props) bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="c4176-282">The [Selective file inclusion](#selective-file-inclusion) section uses the `ResolvedFileToPublish` item, which is provided by a publish targets file in the [.NET Core SDK](/dotnet/core/project-sdk/msbuild-props).</span></span> <span data-ttu-id="c4176-283">Weil das Web-SDK vom .NET Core SDK abhängig ist, können beide Elemente in einem ASP.NET Core-Projekt verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="c4176-283">Because the Web SDK depends on the .NET Core SDK, either item can be used in an ASP.NET Core project.</span></span>

### <a name="general-file-inclusion"></a><span data-ttu-id="c4176-284">Allgemeines Einbeziehen von Dateien</span><span class="sxs-lookup"><span data-stu-id="c4176-284">General file inclusion</span></span>

<span data-ttu-id="c4176-285">Das Element `<ItemGroup>` im folgenden Beispiel veranschaulicht das Kopieren eines Ordners, der sich außerhalb des Projektverzeichnisses befindet, in einen Ordner der veröffentlichten Website.</span><span class="sxs-lookup"><span data-stu-id="c4176-285">The following example's `<ItemGroup>` element demonstrates copying a folder located outside of the project directory to a folder of the published site.</span></span> <span data-ttu-id="c4176-286">Alle Dateien, die dem `<ItemGroup>`-Element im folgenden Markup-Ausschnitt hinzugefügt werden, werden standardmäßig einbezogen.</span><span class="sxs-lookup"><span data-stu-id="c4176-286">Any files added to the following markup's `<ItemGroup>` are included by default.</span></span>

```xml
<ItemGroup>
  <_CustomFiles Include="$(MSBuildProjectDirectory)/../images/**/*" />
  <DotNetPublishFiles Include="@(_CustomFiles)">
    <DestinationRelativePath>wwwroot/images/%(RecursiveDir)%(Filename)%(Extension)</DestinationRelativePath>
  </DotNetPublishFiles>
</ItemGroup>
```

<span data-ttu-id="c4176-287">Das obenstehende Markup:</span><span class="sxs-lookup"><span data-stu-id="c4176-287">The preceding markup:</span></span>

* <span data-ttu-id="c4176-288">Kann der *CSPROJ*-Datei oder dem Veröffentlichungsprofil hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="c4176-288">Can be added to the *.csproj* file or the publish profile.</span></span> <span data-ttu-id="c4176-289">Wenn es der *CSPROJ*-Datei hinzugefügt wird, wird es in jedes Veröffentlichungsprofil im Projekt eingeschlossen.</span><span class="sxs-lookup"><span data-stu-id="c4176-289">If it's added to the *.csproj* file, it's included in each publish profile in the project.</span></span>
* <span data-ttu-id="c4176-290">Deklariert ein `_CustomFiles`-Element, um Dateien zu speichern, die mit dem Globmuster des `Include`-Attributs übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="c4176-290">Declares a `_CustomFiles` item to store files matching the `Include` attribute's globbing pattern.</span></span> <span data-ttu-id="c4176-291">Der Ordner *images*, auf den im Muster verwiesen wird, befindet sich außerhalb des Projektverzeichnisses.</span><span class="sxs-lookup"><span data-stu-id="c4176-291">The *images* folder referenced in the pattern is located outside of the project directory.</span></span> <span data-ttu-id="c4176-292">Die [reservierte Eigenschaft](/visualstudio/msbuild/msbuild-reserved-and-well-known-properties)`$(MSBuildProjectDirectory)` löst den absoluten Pfad der Projektdatei auf.</span><span class="sxs-lookup"><span data-stu-id="c4176-292">A [reserved property](/visualstudio/msbuild/msbuild-reserved-and-well-known-properties), named `$(MSBuildProjectDirectory)`, resolves to the project file's absolute path.</span></span>
* <span data-ttu-id="c4176-293">Stellt eine Liste der zu verarbeitenden `DotNetPublishFiles`-Elemente bereit.</span><span class="sxs-lookup"><span data-stu-id="c4176-293">Provides a list of files to the `DotNetPublishFiles` item.</span></span> <span data-ttu-id="c4176-294">Das `<DestinationRelativePath>`-Element des Elements ist standardmäßig leer.</span><span class="sxs-lookup"><span data-stu-id="c4176-294">By default, the item's `<DestinationRelativePath>` element is empty.</span></span> <span data-ttu-id="c4176-295">Der Standardwert wird im Markup überschrieben und verwendet [bekannte Elementmetadaten](/visualstudio/msbuild/msbuild-well-known-item-metadata) wie z. B. `%(RecursiveDir)`.</span><span class="sxs-lookup"><span data-stu-id="c4176-295">The default value is overridden in the markup and uses [well-known item metadata](/visualstudio/msbuild/msbuild-well-known-item-metadata) such as `%(RecursiveDir)`.</span></span> <span data-ttu-id="c4176-296">Der innere Text stellt den Ordner *wwwroot/images* der veröffentlichten Website dar.</span><span class="sxs-lookup"><span data-stu-id="c4176-296">The inner text represents the *wwwroot/images* folder of the published site.</span></span>

### <a name="selective-file-inclusion"></a><span data-ttu-id="c4176-297">Selektives Einbeziehen von Dateien</span><span class="sxs-lookup"><span data-stu-id="c4176-297">Selective file inclusion</span></span>

<span data-ttu-id="c4176-298">Der markierte Markup-Code im folgenden Beispiel veranschaulicht Folgendes:</span><span class="sxs-lookup"><span data-stu-id="c4176-298">The highlighted markup in the following example demonstrates:</span></span>

* <span data-ttu-id="c4176-299">Das Kopieren einer Datei, die sich außerhalb eines Projekts befindet, in den *wwwroot*-Ordner der veröffentlichten Website.</span><span class="sxs-lookup"><span data-stu-id="c4176-299">Copying a file located outside of the project into the published site's *wwwroot* folder.</span></span> <span data-ttu-id="c4176-300">Der Dateiname *ReadMe2.md* wird beibehalten.</span><span class="sxs-lookup"><span data-stu-id="c4176-300">The file name of *ReadMe2.md* is maintained.</span></span>
* <span data-ttu-id="c4176-301">Ausschließen des Ordners *wwwroot\Content*.</span><span class="sxs-lookup"><span data-stu-id="c4176-301">Excluding the *wwwroot\Content* folder.</span></span>
* <span data-ttu-id="c4176-302">Ausschließen von *Views\Home\About2.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="c4176-302">Excluding *Views\Home\About2.cshtml*.</span></span>

[!code-xml[](visual-studio-publish-profiles/samples/Web1.pubxml?highlight=18-23)]

<span data-ttu-id="c4176-303">Im vorherigen Beispiel wird das Element `ResolvedFileToPublish` verwendet, dessen Standardverhalten darin besteht, die im `Include`-Attribut bereitgestellten Dateien in die veröffentlichte Website zu kopieren.</span><span class="sxs-lookup"><span data-stu-id="c4176-303">The preceding example uses the `ResolvedFileToPublish` item, whose default behavior is to always copy the files provided in the `Include` attribute to the published site.</span></span> <span data-ttu-id="c4176-304">Sie können das Standardverhalten überschreiben, indem Sie ein untergeordnetes `<CopyToPublishDirectory>`-Element mit dem inneren Text `Never` oder `PreserveNewest` einbeziehen.</span><span class="sxs-lookup"><span data-stu-id="c4176-304">Override the default behavior by including a `<CopyToPublishDirectory>` child element with inner text of either `Never` or `PreserveNewest`.</span></span> <span data-ttu-id="c4176-305">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="c4176-305">For example:</span></span>

```xml
<ResolvedFileToPublish Include="..\ReadMe2.md">
  <RelativePath>wwwroot\ReadMe2.md</RelativePath>
  <CopyToPublishDirectory>PreserveNewest</CopyToPublishDirectory>
</ResolvedFileToPublish>
```

<span data-ttu-id="c4176-306">Weitere Bereitstellungsbeispiele finden Sie in der [README-Datei des Web SDK](https://github.com/dotnet/sdk/tree/master/src/WebSdk).</span><span class="sxs-lookup"><span data-stu-id="c4176-306">For more deployment samples, see the [Web SDK README file](https://github.com/dotnet/sdk/tree/master/src/WebSdk).</span></span>

## <a name="run-a-target-before-or-after-publishing"></a><span data-ttu-id="c4176-307">Führen Sie ein Ziel vor oder nach der Veröffentlichung aus.</span><span class="sxs-lookup"><span data-stu-id="c4176-307">Run a target before or after publishing</span></span>

<span data-ttu-id="c4176-308">Die integrierten Ziele `BeforePublish` und `AfterPublish` führen ein Ziel vor oder nach dem Veröffentlichungsziel aus.</span><span class="sxs-lookup"><span data-stu-id="c4176-308">The built-in `BeforePublish` and `AfterPublish` targets execute a target before or after the publish target.</span></span> <span data-ttu-id="c4176-309">Fügen Sie die folgenden Elemente dem Veröffentlichungsprofil hinzu, um Konsolenmeldungen vor und nach der Veröffentlichung zu protokollieren:</span><span class="sxs-lookup"><span data-stu-id="c4176-309">Add the following elements to the publish profile to log console messages both before and after publishing:</span></span>

```xml
<Target Name="CustomActionsBeforePublish" BeforeTargets="BeforePublish">
    <Message Text="Inside BeforePublish" Importance="high" />
  </Target>
  <Target Name="CustomActionsAfterPublish" AfterTargets="AfterPublish">
    <Message Text="Inside AfterPublish" Importance="high" />
</Target>
```

## <a name="publish-to-a-server-using-an-untrusted-certificate"></a><span data-ttu-id="c4176-310">Veröffentlichen auf einem Server unter Verwendung eines nicht vertrauenswürdigen Zertifikats</span><span class="sxs-lookup"><span data-stu-id="c4176-310">Publish to a server using an untrusted certificate</span></span>

<span data-ttu-id="c4176-311">Fügen Sie die Eigenschaft `<AllowUntrustedCertificate>` mit dem Wert `True` auf dem Veröffentlichungsprofil hinzu:</span><span class="sxs-lookup"><span data-stu-id="c4176-311">Add the `<AllowUntrustedCertificate>` property with a value of `True` to the publish profile:</span></span>

```xml
<PropertyGroup>
  <AllowUntrustedCertificate>True</AllowUntrustedCertificate>
</PropertyGroup>
```

## <a name="the-kudu-service"></a><span data-ttu-id="c4176-312">Der Kudu-Dienst</span><span class="sxs-lookup"><span data-stu-id="c4176-312">The Kudu service</span></span>

<span data-ttu-id="c4176-313">Verwenden Sie zum Anzeigen der Dateien in einer Web-App-Bereitstellung von Azure App Service den [Kudu-Dienst](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service).</span><span class="sxs-lookup"><span data-stu-id="c4176-313">To view the files in an Azure App Service web app deployment, use the [Kudu service](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service).</span></span> <span data-ttu-id="c4176-314">Fügen Sie das Token `scm` an den Namen Ihrer Web-App an.</span><span class="sxs-lookup"><span data-stu-id="c4176-314">Append the `scm` token to the web app name.</span></span> <span data-ttu-id="c4176-315">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="c4176-315">For example:</span></span>

| <span data-ttu-id="c4176-316">URL</span><span class="sxs-lookup"><span data-stu-id="c4176-316">URL</span></span>                                    | <span data-ttu-id="c4176-317">Ergebnis</span><span class="sxs-lookup"><span data-stu-id="c4176-317">Result</span></span>       |
| -------------------------------------- | ------------ |
| `http://mysite.azurewebsites.net/`     | <span data-ttu-id="c4176-318">Webanwendung</span><span class="sxs-lookup"><span data-stu-id="c4176-318">Web App</span></span>      |
| `http://mysite.scm.azurewebsites.net/` | <span data-ttu-id="c4176-319">Der Kudu-Dienst</span><span class="sxs-lookup"><span data-stu-id="c4176-319">Kudu service</span></span> |

<span data-ttu-id="c4176-320">Wählen Sie das Menüelement [Debugging-Konsole](https://github.com/projectkudu/kudu/wiki/Kudu-console), aus, um Dateien anzuzeigen, zu bearbeiten, zu löschen oder hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="c4176-320">Select the [Debug Console](https://github.com/projectkudu/kudu/wiki/Kudu-console) menu item to view, edit, delete, or add files.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c4176-321">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="c4176-321">Additional resources</span></span>

* <span data-ttu-id="c4176-322">[Web Deploy](https://www.iis.net/downloads/microsoft/web-deploy) (MSDeploy) vereinfacht die Bereitstellung von Web-Apps und Websites auf IIS-Servern.</span><span class="sxs-lookup"><span data-stu-id="c4176-322">[Web Deploy](https://www.iis.net/downloads/microsoft/web-deploy) (MSDeploy) simplifies deployment of web apps and websites to IIS servers.</span></span>
* <span data-ttu-id="c4176-323">[GitHub-Repository für Web SDK](https://github.com/dotnet/websdk/issues): Probleme melden und Features für die Bereitstellung anfordern.</span><span class="sxs-lookup"><span data-stu-id="c4176-323">[Web SDK GitHub repository](https://github.com/dotnet/websdk/issues): File issues and request features for deployment.</span></span>
* [<span data-ttu-id="c4176-324">Veröffentlichen einer ASP.NET-Web-App auf einer Azure-VM aus Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c4176-324">Publish an ASP.NET Web App to an Azure VM from Visual Studio</span></span>](/azure/virtual-machines/windows/publish-web-app-from-visual-studio)
* <xref:host-and-deploy/iis/transform-webconfig>
