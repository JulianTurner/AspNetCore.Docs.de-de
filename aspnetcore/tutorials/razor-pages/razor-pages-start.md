---
title: 'Tutorial: Erste Schritte mit Razor Pages in ASP.NET Core'
author: rick-anderson
description: 'Diese Reihe von Tutorials zeigt, wie Sie Razor Pages in ASP.NET Core verwenden können. Erfahren Sie, wie Sie ein Modell erstellen, Code für Razor-Seiten generieren, Entity Framework Core und SQL Server für den Datenzugriff verwenden, Suchfunktionen hinzufügen, Eingabeüberprüfung hinzufügen und Migrationen verwenden, um das Modell zu aktualisieren.'
ms.author: riande
ms.date: 11/12/2019
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
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: ab890b956b1242f183054b7ab4575a59072b4f50
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060234"
---
# <a name="tutorial-get-started-with-no-locrazor-pages-in-aspnet-core"></a><span data-ttu-id="d25b0-104">Tutorial: Erste Schritte mit Razor Pages in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d25b0-104">Tutorial: Get started with Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="d25b0-105">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="d25b0-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"
<span data-ttu-id="d25b0-106">Dies ist das erste Tutorial einer Reihe, in der Sie Grundlegendes zur Erstellung einer ASP.NET Core-Web-App mit Razor Pages erfahren.</span><span class="sxs-lookup"><span data-stu-id="d25b0-106">This is the first tutorial of a series that teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="d25b0-107">Am Ende der Reihe verfügen Sie über eine App, mit der eine Filmdatenbank verwaltet werden kann.</span><span class="sxs-lookup"><span data-stu-id="d25b0-107">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="d25b0-108">In diesem Tutorial:</span><span class="sxs-lookup"><span data-stu-id="d25b0-108">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="d25b0-109">Sie erstellen eine Razor Pages-Web-App.</span><span class="sxs-lookup"><span data-stu-id="d25b0-109">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="d25b0-110">Führen Sie die App aus.</span><span class="sxs-lookup"><span data-stu-id="d25b0-110">Run the app.</span></span>
> * <span data-ttu-id="d25b0-111">Überprüfen Sie die Projektdateien.</span><span class="sxs-lookup"><span data-stu-id="d25b0-111">Examine the project files.</span></span>

<span data-ttu-id="d25b0-112">Am Ende dieses Tutorials verfügen Sie über eine funktionsfähige Razor Pages-Web-App, auf der Sie in späteren Tutorials aufbauen werden.</span><span class="sxs-lookup"><span data-stu-id="d25b0-112">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![Start- oder Indexseite](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="d25b0-114">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="d25b0-114">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d25b0-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d25b0-115">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="d25b0-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d25b0-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d25b0-117">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="d25b0-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-no-locrazor-pages-web-app"></a><span data-ttu-id="d25b0-118">Erstellen einer Razor Pages-Web-App</span><span class="sxs-lookup"><span data-stu-id="d25b0-118">Create a Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d25b0-119">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d25b0-119">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="d25b0-120">Klicken Sie in Visual Studio im Menü **Datei** auf **Neu** > **Projekt**.</span><span class="sxs-lookup"><span data-stu-id="d25b0-120">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="d25b0-121">Erstellen Sie eine neue ASP.NET Core-Webanwendung, und klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="d25b0-121">Create a new ASP.NET Core Web Application and select **Next**.</span></span>
  <span data-ttu-id="d25b0-122">![neue ASP.NET Core-Webanwendung](razor-pages-start/_static/np_2.1.png)</span><span class="sxs-lookup"><span data-stu-id="d25b0-122">![new ASP.NET Core Web Application](razor-pages-start/_static/np_2.1.png)</span></span>
* <span data-ttu-id="d25b0-123">Nennen Sie das Projekt **RazorPagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="d25b0-123">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="d25b0-124">Es ist wichtig, den Namen *RazorPagesMovie* zu verwenden, damit die Namespaces übereinstimmen, wenn Sie Code kopieren und einfügen.</span><span class="sxs-lookup"><span data-stu-id="d25b0-124">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>
  <span data-ttu-id="d25b0-125">![neue ASP.NET Core-Webanwendung](razor-pages-start/_static/config.png)</span><span class="sxs-lookup"><span data-stu-id="d25b0-125">![new ASP.NET Core Web Application](razor-pages-start/_static/config.png)</span></span>

* <span data-ttu-id="d25b0-126">Klicken Sie in der Dropdownliste nacheinander auf **ASP.NET Core 3.1** , **Webanwendung** und **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="d25b0-126">Select **ASP.NET Core 3.1** in the dropdown, **Web Application** , and then select **Create**.</span></span>

![neue ASP.NET Core-Webanwendung](razor-pages-start/_static/3/npx.png)

  <span data-ttu-id="d25b0-128">Das folgende Startprojekt wird erstellt:</span><span class="sxs-lookup"><span data-stu-id="d25b0-128">The following starter project is created:</span></span>

  ![Projektmappen-Explorer](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="d25b0-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d25b0-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="d25b0-131">Öffnen Sie das [integrierte Terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="d25b0-131">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="d25b0-132">Wechseln Sie zum Verzeichnis (`cd`), das das Projekt enthalten soll.</span><span class="sxs-lookup"><span data-stu-id="d25b0-132">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="d25b0-133">Führen Sie die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="d25b0-133">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="d25b0-134">Der Befehl `dotnet new` erstellt ein neues Razor Pages-Projekt im Ordner *RazorPagesMovie*.</span><span class="sxs-lookup"><span data-stu-id="d25b0-134">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="d25b0-135">Der Befehl `code` öffnet den Ordner *RazorPagesMovie* in der aktuellen Instanz von Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="d25b0-135">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="d25b0-136">Sobald das OmniSharp-Flammensymbol in der Statusleiste grün angezeigt wird, wird ein Dialogfeld mit folgender Meldung angezeigt: **Die erforderlichen Objekte zum Erstellen und Debuggen sind in "RazorPagesMovie" nicht vorhanden. Sollen sie hinzugefügt werden?**</span><span class="sxs-lookup"><span data-stu-id="d25b0-136">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="d25b0-137">Wählen Sie **Ja**.</span><span class="sxs-lookup"><span data-stu-id="d25b0-137">Select **Yes**.</span></span>

  <span data-ttu-id="d25b0-138">Ein *VSCODE* -Verzeichnis, das die Dateien *launch.json* und *tasks.json* enthält, wird dem Stammverzeichnis des Projekts hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="d25b0-138">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d25b0-139">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="d25b0-139">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="d25b0-140">Klicken Sie auf **Datei** > **Neue Projektmappe**.</span><span class="sxs-lookup"><span data-stu-id="d25b0-140">Select **File** > **New Solution**.</span></span>

  ![Neue Projektmappe in macOS](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="d25b0-142">Navigieren Sie in Visual Studio für Mac-Versionen vor Version 8.6 zu **.NET Core** > **App** > **Webanwendung** > **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="d25b0-142">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application** > **Next**.</span></span> <span data-ttu-id="d25b0-143">Bei Version 8.6 oder höher klicken Sie auf **Web and Console** > **App** > **Webanwendung** > **Weiter** (Web und Konsole).</span><span class="sxs-lookup"><span data-stu-id="d25b0-143">In version 8.6 or later, select **Web and Console** > **App** > **Web Application** > **Next**.</span></span>

  ![Auswählen der macOS-Web-App-Vorlage](razor-pages-start/_static/web_app_template_vsmac.png)

* <span data-ttu-id="d25b0-145">Gehen Sie im Dialogfeld **Neue Webanwendung konfigurieren** folgendermaßen vor:</span><span class="sxs-lookup"><span data-stu-id="d25b0-145">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="d25b0-146">Vergewissern Sie sich, dass **Authentifizierung** auf **Keine Authentifizierung** festgelegt ist.</span><span class="sxs-lookup"><span data-stu-id="d25b0-146">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="d25b0-147">Wenn eine Option zum Auswählen eines **Zielframeworks** angezeigt wird, wählen Sie die neueste 3.x-Version aus.</span><span class="sxs-lookup"><span data-stu-id="d25b0-147">If presented an option to select a **Target Framework** , select the latest 3.x version.</span></span>

  <span data-ttu-id="d25b0-148">Klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="d25b0-148">Select **Next**.</span></span>

* <span data-ttu-id="d25b0-149">Nennen Sie das Projekt **RazorPagesMovie** , und klicken Sie auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="d25b0-149">Name the project **RazorPagesMovie** , and then select **Create**.</span></span>

  ![Benennen des macOS-Projekts](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="d25b0-151">Ausführen der App</span><span class="sxs-lookup"><span data-stu-id="d25b0-151">Run the app</span></span>

  [!INCLUDE[](~/includes/run-the-app.md)]

## <a name="examine-the-project-files"></a><span data-ttu-id="d25b0-152">Überprüfen der Projektdateien</span><span class="sxs-lookup"><span data-stu-id="d25b0-152">Examine the project files</span></span>

<span data-ttu-id="d25b0-153">Es folgt eine Übersicht über die Hauptprojektordner und -dateien, mit denen Sie in späteren Tutorials arbeiten werden.</span><span class="sxs-lookup"><span data-stu-id="d25b0-153">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="d25b0-154">Ordner „Seiten“</span><span class="sxs-lookup"><span data-stu-id="d25b0-154">Pages folder</span></span>

<span data-ttu-id="d25b0-155">Enthält Razor-Seiten und unterstützende Dateien.</span><span class="sxs-lookup"><span data-stu-id="d25b0-155">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="d25b0-156">Jede Razor-Seite besteht aus einem Dateienpaar:</span><span class="sxs-lookup"><span data-stu-id="d25b0-156">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="d25b0-157">Eine *.cshtml* -Datei, die HTML-Markup mit C#-Code in Razor-Syntax enthält</span><span class="sxs-lookup"><span data-stu-id="d25b0-157">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="d25b0-158">Eine *.cshtml.cs* -Datei mit C# Code, in dem Seitenereignisse verarbeitet werden</span><span class="sxs-lookup"><span data-stu-id="d25b0-158">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="d25b0-159">Unterstützende Dateien haben Namen, die mit einem Unterstrich beginnen.</span><span class="sxs-lookup"><span data-stu-id="d25b0-159">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="d25b0-160">Zum Beispiel sind in der Datei *_Layout.cshtml* Benutzeroberflächenelemente konfiguriert, die für alle Seiten gelten.</span><span class="sxs-lookup"><span data-stu-id="d25b0-160">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="d25b0-161">Mit dieser Datei werden das Navigationsmenü oben auf der Seite und der Urheberrechtshinweis unten auf der Seite eingerichtet.</span><span class="sxs-lookup"><span data-stu-id="d25b0-161">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="d25b0-162">Weitere Informationen finden Sie unter <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="d25b0-162">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="d25b0-163">Ordner „wwwroot“</span><span class="sxs-lookup"><span data-stu-id="d25b0-163">wwwroot folder</span></span>

<span data-ttu-id="d25b0-164">Enthält statische Dateien, z. B. HTML-Dateien, JavaScript-Dateien und CSS-Dateien.</span><span class="sxs-lookup"><span data-stu-id="d25b0-164">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="d25b0-165">Weitere Informationen finden Sie unter <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="d25b0-165">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="d25b0-166">appsettings.json</span><span class="sxs-lookup"><span data-stu-id="d25b0-166">appSettings.json</span></span>

<span data-ttu-id="d25b0-167">Enthält Konfigurationsdaten, z. B. Verbindungszeichenfolgen.</span><span class="sxs-lookup"><span data-stu-id="d25b0-167">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="d25b0-168">Weitere Informationen finden Sie unter <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="d25b0-168">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="d25b0-169">Program.cs</span><span class="sxs-lookup"><span data-stu-id="d25b0-169">Program.cs</span></span>

<span data-ttu-id="d25b0-170">Enthält den Einstiegspunkt für das Programm.</span><span class="sxs-lookup"><span data-stu-id="d25b0-170">Contains the entry point for the program.</span></span> <span data-ttu-id="d25b0-171">Weitere Informationen finden Sie unter <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="d25b0-171">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="d25b0-172">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="d25b0-172">Startup.cs</span></span>

<span data-ttu-id="d25b0-173">Diese Datei enthält Code, mit dem das App-Verhalten konfiguriert wird.</span><span class="sxs-lookup"><span data-stu-id="d25b0-173">Contains code that configures app behavior.</span></span> <span data-ttu-id="d25b0-174">Weitere Informationen finden Sie unter <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="d25b0-174">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="next-steps"></a><span data-ttu-id="d25b0-175">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="d25b0-175">Next steps</span></span>

<span data-ttu-id="d25b0-176">Wechseln Sie zum nächsten Tutorial in der Reihe:</span><span class="sxs-lookup"><span data-stu-id="d25b0-176">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="d25b0-177">Hinzufügen eines Modells</span><span class="sxs-lookup"><span data-stu-id="d25b0-177">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end

<!--::: moniker range=">= aspnetcore-3.0" -->

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="d25b0-178">Dies ist das erste Tutorial aus einer Reihe.</span><span class="sxs-lookup"><span data-stu-id="d25b0-178">This is the first tutorial of a series.</span></span> <span data-ttu-id="d25b0-179">[In der Reihe](xref:tutorials/razor-pages/index) lernen Sie die Grundlagen zum Erstellen einer Razor Pages-Web-App mit ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d25b0-179">[The series](xref:tutorials/razor-pages/index) teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="d25b0-180">Am Ende der Reihe verfügen Sie über eine App, mit der eine Filmdatenbank verwaltet werden kann.</span><span class="sxs-lookup"><span data-stu-id="d25b0-180">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="d25b0-181">In diesem Tutorial:</span><span class="sxs-lookup"><span data-stu-id="d25b0-181">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="d25b0-182">Sie erstellen eine Razor Pages-Web-App.</span><span class="sxs-lookup"><span data-stu-id="d25b0-182">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="d25b0-183">Führen Sie die App aus.</span><span class="sxs-lookup"><span data-stu-id="d25b0-183">Run the app.</span></span>
> * <span data-ttu-id="d25b0-184">Überprüfen Sie die Projektdateien.</span><span class="sxs-lookup"><span data-stu-id="d25b0-184">Examine the project files.</span></span>

<span data-ttu-id="d25b0-185">Am Ende dieses Tutorials verfügen Sie über eine funktionsfähige Razor Pages-Web-App, auf der Sie in späteren Tutorials aufbauen werden.</span><span class="sxs-lookup"><span data-stu-id="d25b0-185">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![Start- oder Indexseite](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="d25b0-187">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="d25b0-187">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d25b0-188">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d25b0-188">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="d25b0-189">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d25b0-189">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d25b0-190">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="d25b0-190">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-no-locrazor-pages-web-app"></a><span data-ttu-id="d25b0-191">Erstellen einer Razor Pages-Web-App</span><span class="sxs-lookup"><span data-stu-id="d25b0-191">Create a Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d25b0-192">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d25b0-192">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="d25b0-193">Klicken Sie in Visual Studio im Menü **Datei** auf **Neu** > **Projekt**.</span><span class="sxs-lookup"><span data-stu-id="d25b0-193">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>

* <span data-ttu-id="d25b0-194">Erstellen Sie eine neue ASP.NET Core-Webanwendung, und klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="d25b0-194">Create a new ASP.NET Core Web Application and select **Next**.</span></span>

  ![neue ASP.NET Core-Webanwendung](razor-pages-start/_static/np_2.1.png)

* <span data-ttu-id="d25b0-196">Nennen Sie das Projekt **RazorPagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="d25b0-196">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="d25b0-197">Es ist wichtig, den Namen *RazorPagesMovie* zu verwenden, damit die Namespaces übereinstimmen, wenn Sie Code kopieren und einfügen.</span><span class="sxs-lookup"><span data-stu-id="d25b0-197">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>

  ![neue ASP.NET Core-Webanwendung](razor-pages-start/_static/config.png)

* <span data-ttu-id="d25b0-199">Klicken Sie in der Dropdownliste auf **ASP.NET Core 2.2** , auf **Webanwendung** und anschließend auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="d25b0-199">Select **ASP.NET Core 2.2** in the dropdown, **Web Application** , and then select **Create**.</span></span>

![neue ASP.NET Core-Webanwendung](razor-pages-start/_static/np_2_2.2.png)

  <span data-ttu-id="d25b0-201">Das folgende Startprojekt wird erstellt:</span><span class="sxs-lookup"><span data-stu-id="d25b0-201">The following starter project is created:</span></span>

  ![Projektmappen-Explorer](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="d25b0-203">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d25b0-203">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="d25b0-204">Öffnen Sie das [integrierte Terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="d25b0-204">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="d25b0-205">Wechseln Sie zum Verzeichnis (`cd`), das das Projekt enthalten soll.</span><span class="sxs-lookup"><span data-stu-id="d25b0-205">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="d25b0-206">Führen Sie die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="d25b0-206">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="d25b0-207">Der Befehl `dotnet new` erstellt ein neues Razor Pages-Projekt im Ordner *RazorPagesMovie*.</span><span class="sxs-lookup"><span data-stu-id="d25b0-207">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="d25b0-208">Der Befehl `code` öffnet den Ordner *RazorPagesMovie* in der aktuellen Instanz von Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="d25b0-208">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="d25b0-209">Sobald das OmniSharp-Flammensymbol in der Statusleiste grün angezeigt wird, wird ein Dialogfeld mit folgender Meldung angezeigt: **Die erforderlichen Objekte zum Erstellen und Debuggen sind in "RazorPagesMovie" nicht vorhanden. Sollen sie hinzugefügt werden?**</span><span class="sxs-lookup"><span data-stu-id="d25b0-209">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="d25b0-210">Wählen Sie **Ja**.</span><span class="sxs-lookup"><span data-stu-id="d25b0-210">Select **Yes**.</span></span>

  <span data-ttu-id="d25b0-211">Ein *VSCODE* -Verzeichnis, das die Dateien *launch.json* und *tasks.json* enthält, wird dem Stammverzeichnis des Projekts hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="d25b0-211">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d25b0-212">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="d25b0-212">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="d25b0-213">Klicken Sie auf **Datei** > **Neue Projektmappe**.</span><span class="sxs-lookup"><span data-stu-id="d25b0-213">Select **File** > **New Solution**.</span></span>

![Neue Projektmappe in macOS](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="d25b0-215">Navigieren Sie in Visual Studio für Mac-Versionen vor Version 8.6 zu **.NET Core** > **App** > **Webanwendung** > **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="d25b0-215">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application** > **Next**.</span></span> <span data-ttu-id="d25b0-216">Bei Version 8.6 oder höher klicken Sie auf **Web and Console** > **App** > **Webanwendung** > **Weiter** (Web und Konsole).</span><span class="sxs-lookup"><span data-stu-id="d25b0-216">In version 8.6 or later, select **Web and Console** > **App** > **Web Application** > **Next**.</span></span>

* <span data-ttu-id="d25b0-217">Gehen Sie im Dialogfeld **Neue Webanwendung konfigurieren** folgendermaßen vor:</span><span class="sxs-lookup"><span data-stu-id="d25b0-217">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="d25b0-218">Vergewissern Sie sich, dass **Authentifizierung** auf **Keine Authentifizierung** festgelegt ist.</span><span class="sxs-lookup"><span data-stu-id="d25b0-218">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="d25b0-219">Wenn eine Option zum Auswählen eines **Zielframeworks** angezeigt wird, wählen Sie die neueste 2.x-Version aus.</span><span class="sxs-lookup"><span data-stu-id="d25b0-219">If presented an option to select a **Target Framework** , select the latest 2.x version.</span></span>

  <span data-ttu-id="d25b0-220">Klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="d25b0-220">Select **Next**.</span></span>

* <span data-ttu-id="d25b0-221">Nennen Sie das Projekt **RazorPagesMovie** , und klicken Sie auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="d25b0-221">Name the project **RazorPagesMovie** , and then select **Create**.</span></span>

  ![nameproj](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="d25b0-223">Ausführen der App</span><span class="sxs-lookup"><span data-stu-id="d25b0-223">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d25b0-224">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d25b0-224">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="d25b0-225">Drücken Sie STRG+F5, um die Ausführung ohne den Debugger zu starten.</span><span class="sxs-lookup"><span data-stu-id="d25b0-225">Press Ctrl+F5 to run without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="d25b0-226">Visual Studio startet [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) und führt die App aus.</span><span class="sxs-lookup"><span data-stu-id="d25b0-226">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="d25b0-227">Die Adressleiste zeigt `localhost:port#` an, nicht `example.com`.</span><span class="sxs-lookup"><span data-stu-id="d25b0-227">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="d25b0-228">Das liegt daran, dass es sich bei `localhost` um den Standardhostnamen für den lokalen Computer handelt.</span><span class="sxs-lookup"><span data-stu-id="d25b0-228">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="d25b0-229">„Localhost“ dient nur Webanforderungen vom lokalen Computer.</span><span class="sxs-lookup"><span data-stu-id="d25b0-229">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="d25b0-230">Wenn in Visual Studio ein Webprojekt erstellt wird, wird für den Webserver ein zufälliger Port verwendet.</span><span class="sxs-lookup"><span data-stu-id="d25b0-230">When Visual Studio creates a web project, a random port is used for the web server.</span></span>

* <span data-ttu-id="d25b0-231">Wählen Sie auf der Homepage der App **Akzeptieren** aus, um der Nachverfolgung zuzustimmen.</span><span class="sxs-lookup"><span data-stu-id="d25b0-231">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="d25b0-232">Diese App verfolgt keine persönlichen Informationen nach, aber die Projektvorlage enthält das Einverständniserklärungsfeature für den Fall, dass die App die [allgemeine Datenschutz-Grundverordnung (DSGVO)](xref:security/gdpr) der Europäischen Union erfüllen muss.</span><span class="sxs-lookup"><span data-stu-id="d25b0-232">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Start- oder Indexseite](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="d25b0-234">Die folgende Abbildung zeigt die App, nachdem Sie das Einverständnis zur Nachverfolgung gegeben haben:</span><span class="sxs-lookup"><span data-stu-id="d25b0-234">The following image shows the app after you give consent to tracking:</span></span>

  ![Start- oder Indexseite](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-code"></a>[<span data-ttu-id="d25b0-236">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d25b0-236">Visual Studio Code</span></span>](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* <span data-ttu-id="d25b0-237">Drücken Sie **STRG+F5** , um die Ausführung ohne den Debugger zu starten.</span><span class="sxs-lookup"><span data-stu-id="d25b0-237">Press **Ctrl-F5** to run without the debugger.</span></span>

  <span data-ttu-id="d25b0-238">Visual Studio Code startet [Kestrel](xref:fundamentals/servers/kestrel) und einen Browser und navigiert zu `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="d25b0-238">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span> <span data-ttu-id="d25b0-239">Die Adressleiste zeigt `localhost:port#` an, nicht `example.com`.</span><span class="sxs-lookup"><span data-stu-id="d25b0-239">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="d25b0-240">Das liegt daran, dass es sich bei `localhost` um den Standardhostnamen für den lokalen Computer handelt.</span><span class="sxs-lookup"><span data-stu-id="d25b0-240">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="d25b0-241">„Localhost“ dient nur Webanforderungen vom lokalen Computer.</span><span class="sxs-lookup"><span data-stu-id="d25b0-241">Localhost only serves web requests from the local computer.</span></span>

* <span data-ttu-id="d25b0-242">Wählen Sie auf der Homepage der App **Akzeptieren** aus, um der Nachverfolgung zuzustimmen.</span><span class="sxs-lookup"><span data-stu-id="d25b0-242">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="d25b0-243">Diese App verfolgt keine persönlichen Informationen nach, aber die Projektvorlage enthält das Einverständniserklärungsfeature für den Fall, dass die App die [allgemeine Datenschutz-Grundverordnung (DSGVO)](xref:security/gdpr) der Europäischen Union erfüllen muss.</span><span class="sxs-lookup"><span data-stu-id="d25b0-243">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Start- oder Indexseite](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="d25b0-245">Die folgende Abbildung zeigt die App, nachdem Sie das Einverständnis zur Nachverfolgung gegeben haben:</span><span class="sxs-lookup"><span data-stu-id="d25b0-245">The following image shows the app after you give consent to tracking:</span></span>

  ![Start- oder Indexseite](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d25b0-247">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="d25b0-247">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="d25b0-248">Drücken Sie **CMD+OPT+F5** für eine Ausführung ohne Debugger.</span><span class="sxs-lookup"><span data-stu-id="d25b0-248">Press **Cmd-Opt-F5** to run without the debugger.</span></span>

  <span data-ttu-id="d25b0-249">Visual Studio startet dann [Kestrel](xref:fundamentals/servers/kestrel) und einen Browser und navigiert zu `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="d25b0-249">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span>

* <span data-ttu-id="d25b0-250">Wählen Sie auf der Homepage der App **Akzeptieren** aus, um der Nachverfolgung zuzustimmen.</span><span class="sxs-lookup"><span data-stu-id="d25b0-250">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="d25b0-251">Diese App verfolgt keine persönlichen Informationen nach, aber die Projektvorlage enthält das Einverständniserklärungsfeature für den Fall, dass die App die [allgemeine Datenschutz-Grundverordnung (DSGVO)](xref:security/gdpr) der Europäischen Union erfüllen muss.</span><span class="sxs-lookup"><span data-stu-id="d25b0-251">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Start- oder Indexseite](razor-pages-start/_static/homeGDPR2.2_safari.png)

  <span data-ttu-id="d25b0-253">Die folgende Abbildung zeigt die App, nachdem Sie das Einverständnis zur Nachverfolgung gegeben haben:</span><span class="sxs-lookup"><span data-stu-id="d25b0-253">The following image shows the app after you give consent to tracking:</span></span>

  ![Start- oder Indexseite](razor-pages-start/_static/home2.2_safari.png)

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a><span data-ttu-id="d25b0-255">Überprüfen der Projektdateien</span><span class="sxs-lookup"><span data-stu-id="d25b0-255">Examine the project files</span></span>

<span data-ttu-id="d25b0-256">Es folgt eine Übersicht über die Hauptprojektordner und -dateien, mit denen Sie in späteren Tutorials arbeiten werden.</span><span class="sxs-lookup"><span data-stu-id="d25b0-256">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="d25b0-257">Ordner „Seiten“</span><span class="sxs-lookup"><span data-stu-id="d25b0-257">Pages folder</span></span>

<span data-ttu-id="d25b0-258">Enthält Razor-Seiten und unterstützende Dateien.</span><span class="sxs-lookup"><span data-stu-id="d25b0-258">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="d25b0-259">Jede Razor-Seite besteht aus einem Dateienpaar:</span><span class="sxs-lookup"><span data-stu-id="d25b0-259">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="d25b0-260">Eine *.cshtml* -Datei, die HTML-Markup mit C#-Code in Razor-Syntax enthält</span><span class="sxs-lookup"><span data-stu-id="d25b0-260">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="d25b0-261">Eine *.cshtml.cs* -Datei mit C# Code, in dem Seitenereignisse verarbeitet werden</span><span class="sxs-lookup"><span data-stu-id="d25b0-261">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="d25b0-262">Unterstützende Dateien haben Namen, die mit einem Unterstrich beginnen.</span><span class="sxs-lookup"><span data-stu-id="d25b0-262">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="d25b0-263">Zum Beispiel sind in der Datei *_Layout.cshtml* Benutzeroberflächenelemente konfiguriert, die für alle Seiten gelten.</span><span class="sxs-lookup"><span data-stu-id="d25b0-263">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="d25b0-264">Mit dieser Datei werden das Navigationsmenü oben auf der Seite und der Urheberrechtshinweis unten auf der Seite eingerichtet.</span><span class="sxs-lookup"><span data-stu-id="d25b0-264">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="d25b0-265">Weitere Informationen finden Sie unter <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="d25b0-265">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="d25b0-266">Ordner „wwwroot“</span><span class="sxs-lookup"><span data-stu-id="d25b0-266">wwwroot folder</span></span>

<span data-ttu-id="d25b0-267">Enthält statische Dateien, z. B. HTML-Dateien, JavaScript-Dateien und CSS-Dateien.</span><span class="sxs-lookup"><span data-stu-id="d25b0-267">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="d25b0-268">Weitere Informationen finden Sie unter <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="d25b0-268">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="d25b0-269">appsettings.json</span><span class="sxs-lookup"><span data-stu-id="d25b0-269">appSettings.json</span></span>

<span data-ttu-id="d25b0-270">Enthält Konfigurationsdaten, z. B. Verbindungszeichenfolgen.</span><span class="sxs-lookup"><span data-stu-id="d25b0-270">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="d25b0-271">Weitere Informationen finden Sie unter <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="d25b0-271">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="d25b0-272">Program.cs</span><span class="sxs-lookup"><span data-stu-id="d25b0-272">Program.cs</span></span>

<span data-ttu-id="d25b0-273">Enthält den Einstiegspunkt für das Programm.</span><span class="sxs-lookup"><span data-stu-id="d25b0-273">Contains the entry point for the program.</span></span> <span data-ttu-id="d25b0-274">Weitere Informationen finden Sie unter <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="d25b0-274">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="d25b0-275">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="d25b0-275">Startup.cs</span></span>

<span data-ttu-id="d25b0-276">Enthält Code, mit dem das App-Verhalten konfiguriert wird, beispielsweise, ob die App Zustimmung für cookies erfordert.</span><span class="sxs-lookup"><span data-stu-id="d25b0-276">Contains code that configures app behavior, such as whether it requires consent for cookies.</span></span> <span data-ttu-id="d25b0-277">Weitere Informationen finden Sie unter <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="d25b0-277">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d25b0-278">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="d25b0-278">Additional resources</span></span>

* [<span data-ttu-id="d25b0-279">Dieses Tutorials auf YouTube</span><span class="sxs-lookup"><span data-stu-id="d25b0-279">Youtube version of this tutorial</span></span>](https://www.youtube.com/watch?v=F0SP7Ry4flQ&feature=youtu.be)

## <a name="next-steps"></a><span data-ttu-id="d25b0-280">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="d25b0-280">Next steps</span></span>

<span data-ttu-id="d25b0-281">Wechseln Sie zum nächsten Tutorial in der Reihe:</span><span class="sxs-lookup"><span data-stu-id="d25b0-281">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="d25b0-282">Hinzufügen eines Modells</span><span class="sxs-lookup"><span data-stu-id="d25b0-282">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end
