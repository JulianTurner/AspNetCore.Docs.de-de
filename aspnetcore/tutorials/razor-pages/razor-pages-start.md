---
title: 'Tutorial: Erste Schritte mit Razor Pages in ASP.NET Core'
author: rick-anderson
description: Dies ist das erste Tutorial einer Reihe, in der Sie Grundlegendes zur Erstellung einer ASP.NET Core-Web-App mit Razor Pages erfahren.
ms.author: riande
ms.date: 09/15/2020
no-loc:
- Index
- Create
- Delete
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
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: fa113a3e0a2a69fb4aa1318056dcfc6e261490f6
ms.sourcegitcommit: 8b867c4cb0c3b39bbc4d2d87815610d2ef858ae7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "96025024"
---
# <a name="tutorial-get-started-with-no-locrazor-pages-in-aspnet-core"></a><span data-ttu-id="d65ff-103">Tutorial: Erste Schritte mit Razor Pages in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d65ff-103">Tutorial: Get started with Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="d65ff-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="d65ff-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"
<span data-ttu-id="d65ff-105">Dies ist das erste Tutorial einer Reihe, in der Sie Grundlegendes zur Erstellung einer ASP.NET Core-Web-App mit Razor Pages erfahren.</span><span class="sxs-lookup"><span data-stu-id="d65ff-105">This is the first tutorial of a series that teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

<span data-ttu-id="d65ff-106">Eine erweiterte Einführung für Entwickler, die mit Controllern und Ansichten vertraut sind, finden Sie unter [Einführung in Razor Pages](xref:razor-pages/index).</span><span class="sxs-lookup"><span data-stu-id="d65ff-106">For a more advanced introduction aimed at developers who are familiar with controllers and views, see [Introduction to Razor Pages](xref:razor-pages/index).</span></span>

<span data-ttu-id="d65ff-107">Am Ende der Reihe verfügen Sie über eine App, mit der eine Filmdatenbank verwaltet werden kann.</span><span class="sxs-lookup"><span data-stu-id="d65ff-107">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

<span data-ttu-id="d65ff-108">[Zeigen Sie Beispielcode an, oder laden Sie diesen herunter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="d65ff-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="d65ff-109">In diesem Tutorial:</span><span class="sxs-lookup"><span data-stu-id="d65ff-109">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="d65ff-110">Create (erstellen) Sie eine Razor Pages-Web-App.</span><span class="sxs-lookup"><span data-stu-id="d65ff-110">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="d65ff-111">Führen Sie die App aus.</span><span class="sxs-lookup"><span data-stu-id="d65ff-111">Run the app.</span></span>
> * <span data-ttu-id="d65ff-112">Überprüfen Sie die Projektdateien.</span><span class="sxs-lookup"><span data-stu-id="d65ff-112">Examine the project files.</span></span>

<span data-ttu-id="d65ff-113">Am Ende dieses Tutorials verfügen Sie über eine funktionsfähige Razor Pages-Web-App, die der Sie in späteren Tutorials weiterentwickeln werden.</span><span class="sxs-lookup"><span data-stu-id="d65ff-113">At the end of this tutorial, you'll have a working Razor Pages web app that you'll enhance in later tutorials.</span></span>

![Seite „Home“ oder Index](razor-pages-start/_static/5/home5.png)

## <a name="prerequisites"></a><span data-ttu-id="d65ff-115">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="d65ff-115">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d65ff-116">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d65ff-116">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="d65ff-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d65ff-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d65ff-118">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="d65ff-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-5.0.md)]

---

## <a name="no-loccreate-a-no-locrazor-pages-web-app"></a><span data-ttu-id="d65ff-119">Create (Erstellen) einer Razor Pages-Web-App</span><span class="sxs-lookup"><span data-stu-id="d65ff-119">Create a Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d65ff-120">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d65ff-120">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="d65ff-121">Starten Sie Visual Studio, und wählen Sie **CreateNeues Projekt erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="d65ff-121">Start Visual Studio and select **Create a new project**.</span></span> <span data-ttu-id="d65ff-122">Weitere Informationen finden Sie unter[Create (Erstellen) eines neuen Projekts in Visual Studio](/visualstudio/ide/create-new-project).</span><span class="sxs-lookup"><span data-stu-id="d65ff-122">For more information, see [Create a new project in Visual Studio](/visualstudio/ide/create-new-project).</span></span>

   ![Create (Erstellen) eines neuen Projekts im Startfenster](razor-pages-start/_static/5/start-window-create-new-project.png)

1. <span data-ttu-id="d65ff-124">Wählen Sie im Dialogfeld **CreateNeues Projekt erstellen** die Option **ASP.NET Core-Webanwendung** aus, und klicken Sie dann auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="d65ff-124">In the **Create a new project** dialog, select **ASP.NET Core Web Application**, and then select **Next**.</span></span>

    ![neue ASP.NET Core-Webanwendung](razor-pages-start/_static/5/np.png)
    
1. <span data-ttu-id="d65ff-126">Geben Sie `RazorPagesMovie` im Dialogfeld **Neues Projekt konfigurieren** für **Projektname** ein.</span><span class="sxs-lookup"><span data-stu-id="d65ff-126">In the **Configure your new project** dialog, enter `RazorPagesMovie` for **Project name**.</span></span> <span data-ttu-id="d65ff-127">Es ist wichtig, dem Projekt den Namen *RazorPagesMovie* in übereinstimmender Groß-/Kleinschreibung zu geben, damit die Namespaces übereinstimmen, wenn Sie Beispielcode kopieren und einfügen.</span><span class="sxs-lookup"><span data-stu-id="d65ff-127">It's important to name the project *RazorPagesMovie*, including matching the capitalization, so the namespaces will match when you copy and paste example code.</span></span>

1. <span data-ttu-id="d65ff-128">Klicken Sie auf **Create** (Zur Kasse).</span><span class="sxs-lookup"><span data-stu-id="d65ff-128">Select **Create**.</span></span>

    ![neue ASP.NET Core-Webanwendung](razor-pages-start/_static/config.png)

1. <span data-ttu-id="d65ff-130">Wählen Sie im Dialogfeld **CreateNeue ASP.NET Core-Webanwendung erstellen** Folgendes aus:</span><span class="sxs-lookup"><span data-stu-id="d65ff-130">In the **Create a new ASP.NET Core web application** dialog, select:</span></span>
    1. <span data-ttu-id="d65ff-131">In den Dropdownmenüs **.NET Core** und **ASP.NET Core 5.0**.</span><span class="sxs-lookup"><span data-stu-id="d65ff-131">**.NET Core** and **ASP.NET Core 5.0** in the dropdowns.</span></span>
    1. <span data-ttu-id="d65ff-132">**Webanwendung**</span><span class="sxs-lookup"><span data-stu-id="d65ff-132">**Web Application**.</span></span>
    1. <span data-ttu-id="d65ff-133">**Create**.</span><span class="sxs-lookup"><span data-stu-id="d65ff-133">**Create**.</span></span>

     ![neue ASP.NET Core-Webanwendung](razor-pages-start/_static/5/npx.png)

    <span data-ttu-id="d65ff-135">Das folgende Startprojekt wird erstellt:</span><span class="sxs-lookup"><span data-stu-id="d65ff-135">The following starter project is created:</span></span>

    ![Projektmappen-Explorer](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="d65ff-137">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d65ff-137">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="d65ff-138">Öffnen Sie das [integrierte Terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="d65ff-138">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

1. <span data-ttu-id="d65ff-139">Wechseln Sie zum Verzeichnis (`cd`), das das Projekt enthalten soll.</span><span class="sxs-lookup"><span data-stu-id="d65ff-139">Change to the directory (`cd`) which will contain the project.</span></span>

1. <span data-ttu-id="d65ff-140">Führen Sie die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="d65ff-140">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapp -o RazorPagesMovie
   code -r RazorPagesMovie
   ```

   * <span data-ttu-id="d65ff-141">Der Befehl `dotnet new` erstellt ein neues Razor Pages-Projekt im Ordner *RazorPagesMovie*.</span><span class="sxs-lookup"><span data-stu-id="d65ff-141">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
   * <span data-ttu-id="d65ff-142">Der Befehl `code` öffnet den Ordner *RazorPagesMovie* in der aktuellen Instanz von Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="d65ff-142">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d65ff-143">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="d65ff-143">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="d65ff-144">Klicken Sie auf **Datei** > **Neue Projektmappe**.</span><span class="sxs-lookup"><span data-stu-id="d65ff-144">Select **File** > **New Solution**.</span></span>

    ![Neue Projektmappe in macOS](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

1. <span data-ttu-id="d65ff-146">Navigieren Sie in Visual Studio für Mac-Versionen vor Version 8.6 zu **.NET Core** > **App** > **Webanwendung** > **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="d65ff-146">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application** > **Next**.</span></span> <span data-ttu-id="d65ff-147">Bei Version 8.6 oder höher klicken Sie auf **Web and Console** > **App** > **Webanwendung** > **Weiter** (Web und Konsole).</span><span class="sxs-lookup"><span data-stu-id="d65ff-147">In version 8.6 or later, select **Web and Console** > **App** > **Web Application** > **Next**.</span></span>

    ![Auswählen der macOS-Web-App-Vorlage](razor-pages-start/_static/web_app_template_vsmac.png)

1. <span data-ttu-id="d65ff-149">Gehen Sie im Dialogfeld **Neue Webanwendung konfigurieren** folgendermaßen vor:</span><span class="sxs-lookup"><span data-stu-id="d65ff-149">In the **Configure the new Web Application** dialog:</span></span>

    1. <span data-ttu-id="d65ff-150">Vergewissern Sie sich, dass **Authentifizierung** auf **Keine Authentifizierung** festgelegt ist.</span><span class="sxs-lookup"><span data-stu-id="d65ff-150">Confirm that **Authentication** is set to **No Authentication**.</span></span>
    1. <span data-ttu-id="d65ff-151">Wenn eine Option zum Auswählen eines **Zielframeworks** angezeigt wird, wählen Sie die neueste .NET 5.x-Version aus.</span><span class="sxs-lookup"><span data-stu-id="d65ff-151">If presented an option to select a **Target Framework**, select the latest .NET 5.x version.</span></span>
    1. <span data-ttu-id="d65ff-152">Wählen Sie **Weiter** aus.</span><span class="sxs-lookup"><span data-stu-id="d65ff-152">Select **Next**.</span></span>

1. <span data-ttu-id="d65ff-153">Nennen Sie das Projekt *RazorPagesMovie*, und wählen Sie **Create** aus.</span><span class="sxs-lookup"><span data-stu-id="d65ff-153">Name the project *RazorPagesMovie* and select **Create**.</span></span>

    ![Benennen des macOS-Projekts](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="d65ff-155">Ausführen der App</span><span class="sxs-lookup"><span data-stu-id="d65ff-155">Run the app</span></span>

  [!INCLUDE[](~/includes/run-the-app.md)]

## <a name="examine-the-project-files"></a><span data-ttu-id="d65ff-156">Überprüfen der Projektdateien</span><span class="sxs-lookup"><span data-stu-id="d65ff-156">Examine the project files</span></span>

<span data-ttu-id="d65ff-157">Es folgt eine Übersicht über die Hauptprojektordner und -dateien, mit denen Sie in späteren Tutorials arbeiten werden.</span><span class="sxs-lookup"><span data-stu-id="d65ff-157">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="d65ff-158">Ordner „Seiten“</span><span class="sxs-lookup"><span data-stu-id="d65ff-158">Pages folder</span></span>

<span data-ttu-id="d65ff-159">Enthält Razor-Seiten und unterstützende Dateien.</span><span class="sxs-lookup"><span data-stu-id="d65ff-159">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="d65ff-160">Jede Razor-Seite besteht aus einem Dateienpaar:</span><span class="sxs-lookup"><span data-stu-id="d65ff-160">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="d65ff-161">Einer *.cshtml*-Datei mit HTML-Markup mit C#-Code in Razor-Syntax.</span><span class="sxs-lookup"><span data-stu-id="d65ff-161">A *.cshtml* file that has HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="d65ff-162">Einer *.cshtml.cs*-Datei mit C#-Code zum Verarbeiten von Seitenereignissen.</span><span class="sxs-lookup"><span data-stu-id="d65ff-162">A *.cshtml.cs* file that has C# code that handles page events.</span></span>

<span data-ttu-id="d65ff-163">Unterstützende Dateien haben Namen, die mit einem Unterstrich beginnen.</span><span class="sxs-lookup"><span data-stu-id="d65ff-163">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="d65ff-164">Zum Beispiel sind in der Datei *_Layout.cshtml* Benutzeroberflächenelemente konfiguriert, die für alle Seiten gelten.</span><span class="sxs-lookup"><span data-stu-id="d65ff-164">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="d65ff-165">Mit dieser Datei werden das Navigationsmenü oben auf der Seite und der Urheberrechtshinweis unten auf der Seite eingerichtet.</span><span class="sxs-lookup"><span data-stu-id="d65ff-165">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="d65ff-166">Weitere Informationen finden Sie unter <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="d65ff-166">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="d65ff-167">Ordner „wwwroot“</span><span class="sxs-lookup"><span data-stu-id="d65ff-167">wwwroot folder</span></span>

<span data-ttu-id="d65ff-168">Enthält statische Ressourcen, z. B. HTML-, JavaScript- und CSS-Dateien.</span><span class="sxs-lookup"><span data-stu-id="d65ff-168">Contains static assets, like HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="d65ff-169">Weitere Informationen finden Sie unter <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="d65ff-169">For more information, see <xref:fundamentals/static-files>.</span></span>

### appsettings.json

<span data-ttu-id="d65ff-170">Enthält Konfigurationsdaten, z. B. Verbindungszeichenfolgen.</span><span class="sxs-lookup"><span data-stu-id="d65ff-170">Contains configuration data, like connection strings.</span></span> <span data-ttu-id="d65ff-171">Weitere Informationen finden Sie unter <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="d65ff-171">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="d65ff-172">Program.cs</span><span class="sxs-lookup"><span data-stu-id="d65ff-172">Program.cs</span></span>

<span data-ttu-id="d65ff-173">Enthält den Einstiegspunkt für die App.</span><span class="sxs-lookup"><span data-stu-id="d65ff-173">Contains the entry point for the app.</span></span> <span data-ttu-id="d65ff-174">Weitere Informationen finden Sie unter <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="d65ff-174">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="d65ff-175">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="d65ff-175">Startup.cs</span></span>

<span data-ttu-id="d65ff-176">Diese Datei enthält Code, mit dem das App-Verhalten konfiguriert wird.</span><span class="sxs-lookup"><span data-stu-id="d65ff-176">Contains code that configures app behavior.</span></span> <span data-ttu-id="d65ff-177">Weitere Informationen finden Sie unter <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="d65ff-177">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="next-steps"></a><span data-ttu-id="d65ff-178">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="d65ff-178">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="d65ff-179">Weiter: Hinzufügen eines Modells</span><span class="sxs-lookup"><span data-stu-id="d65ff-179">Next: Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end

<!--::: moniker range=">= aspnetcore-5.0" -->

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="d65ff-180">Dies ist das erste Tutorial einer Reihe, in der Sie Grundlegendes zur Erstellung einer ASP.NET Core-Web-App mit Razor Pages erfahren.</span><span class="sxs-lookup"><span data-stu-id="d65ff-180">This is the first tutorial of a series that teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

<span data-ttu-id="d65ff-181">Eine erweiterte Einführung für Entwickler, die mit Controllern und Ansichten vertraut sind, finden Sie unter [Einführung in Razor Pages](xref:razor-pages/index).</span><span class="sxs-lookup"><span data-stu-id="d65ff-181">For a more advanced introduction aimed at developers who are familiar with controllers and views, see [Introduction to Razor Pages](xref:razor-pages/index).</span></span>

<span data-ttu-id="d65ff-182">Am Ende der Reihe verfügen Sie über eine App, mit der eine Filmdatenbank verwaltet werden kann.</span><span class="sxs-lookup"><span data-stu-id="d65ff-182">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

<span data-ttu-id="d65ff-183">[Zeigen Sie Beispielcode an, oder laden Sie diesen herunter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="d65ff-183">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="d65ff-184">In diesem Tutorial:</span><span class="sxs-lookup"><span data-stu-id="d65ff-184">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="d65ff-185">Create (erstellen) Sie eine Razor Pages-Web-App.</span><span class="sxs-lookup"><span data-stu-id="d65ff-185">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="d65ff-186">Führen Sie die App aus.</span><span class="sxs-lookup"><span data-stu-id="d65ff-186">Run the app.</span></span>
> * <span data-ttu-id="d65ff-187">Überprüfen Sie die Projektdateien.</span><span class="sxs-lookup"><span data-stu-id="d65ff-187">Examine the project files.</span></span>

<span data-ttu-id="d65ff-188">Am Ende dieses Tutorials verfügen Sie über eine funktionsfähige Razor Pages-Web-App, auf der Sie in späteren Tutorials aufbauen werden.</span><span class="sxs-lookup"><span data-stu-id="d65ff-188">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![Seite „Home“ oder Index](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="d65ff-190">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="d65ff-190">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d65ff-191">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d65ff-191">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="d65ff-192">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d65ff-192">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d65ff-193">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="d65ff-193">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="no-loccreate-a-no-locrazor-pages-web-app"></a><span data-ttu-id="d65ff-194">Create (Erstellen) einer Razor Pages-Web-App</span><span class="sxs-lookup"><span data-stu-id="d65ff-194">Create a Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d65ff-195">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d65ff-195">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="d65ff-196">Klicken Sie in Visual Studio im Menü **Datei** auf **Neu** > **Projekt**.</span><span class="sxs-lookup"><span data-stu-id="d65ff-196">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="d65ff-197">Create (Erstellen) Sie eine neue ASP.NET Core-Webanwendung, und klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="d65ff-197">Create a new ASP.NET Core Web Application and select **Next**.</span></span>
  <span data-ttu-id="d65ff-198">![neue ASP.NET Core-Webanwendung](razor-pages-start/_static/np_2.1.png)</span><span class="sxs-lookup"><span data-stu-id="d65ff-198">![new ASP.NET Core Web Application](razor-pages-start/_static/np_2.1.png)</span></span>
* <span data-ttu-id="d65ff-199">Nennen Sie das Projekt **RazorPagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="d65ff-199">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="d65ff-200">Es ist wichtig, den Namen *RazorPagesMovie* zu verwenden, damit die Namespaces übereinstimmen, wenn Sie Code kopieren und einfügen.</span><span class="sxs-lookup"><span data-stu-id="d65ff-200">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>
  <span data-ttu-id="d65ff-201">![neue ASP.NET Core-Webanwendung](razor-pages-start/_static/config.png)</span><span class="sxs-lookup"><span data-stu-id="d65ff-201">![new ASP.NET Core Web Application](razor-pages-start/_static/config.png)</span></span>

* <span data-ttu-id="d65ff-202">Wählen Sie in der Dropdownliste nacheinander **ASP.NET Core 3.1**, **Webanwendung** und **Create (Erstellen)** aus.</span><span class="sxs-lookup"><span data-stu-id="d65ff-202">Select **ASP.NET Core 3.1** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![neue ASP.NET Core-Webanwendung](razor-pages-start/_static/3/npx.png)

  <span data-ttu-id="d65ff-204">Das folgende Startprojekt wird erstellt:</span><span class="sxs-lookup"><span data-stu-id="d65ff-204">The following starter project is created:</span></span>

  ![Projektmappen-Explorer](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="d65ff-206">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d65ff-206">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="d65ff-207">Öffnen Sie das [integrierte Terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="d65ff-207">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="d65ff-208">Wechseln Sie zum Verzeichnis (`cd`), das das Projekt enthalten soll.</span><span class="sxs-lookup"><span data-stu-id="d65ff-208">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="d65ff-209">Führen Sie die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="d65ff-209">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="d65ff-210">Der Befehl `dotnet new` erstellt ein neues Razor Pages-Projekt im Ordner *RazorPagesMovie*.</span><span class="sxs-lookup"><span data-stu-id="d65ff-210">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="d65ff-211">Der Befehl `code` öffnet den Ordner *RazorPagesMovie* in der aktuellen Instanz von Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="d65ff-211">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="d65ff-212">Sobald das OmniSharp-Flammensymbol in der Statusleiste grün angezeigt wird, wird ein Dialogfeld mit folgender Meldung angezeigt: **Die erforderlichen Objekte zum Erstellen und Debuggen sind in "RazorPagesMovie" nicht vorhanden. Sollen sie hinzugefügt werden?**</span><span class="sxs-lookup"><span data-stu-id="d65ff-212">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="d65ff-213">Wählen Sie **Ja**.</span><span class="sxs-lookup"><span data-stu-id="d65ff-213">Select **Yes**.</span></span>

  <span data-ttu-id="d65ff-214">Ein *VSCODE*-Verzeichnis, das die Dateien *launch.json* und *tasks.json* enthält, wird dem Stammverzeichnis des Projekts hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="d65ff-214">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d65ff-215">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="d65ff-215">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="d65ff-216">Klicken Sie auf **Datei** > **Neue Projektmappe**.</span><span class="sxs-lookup"><span data-stu-id="d65ff-216">Select **File** > **New Solution**.</span></span>

  ![Neue Projektmappe in macOS](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="d65ff-218">Navigieren Sie in Visual Studio für Mac-Versionen vor Version 8.6 zu **.NET Core** > **App** > **Webanwendung** > **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="d65ff-218">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application** > **Next**.</span></span> <span data-ttu-id="d65ff-219">Bei Version 8.6 oder höher klicken Sie auf **Web and Console** > **App** > **Webanwendung** > **Weiter** (Web und Konsole).</span><span class="sxs-lookup"><span data-stu-id="d65ff-219">In version 8.6 or later, select **Web and Console** > **App** > **Web Application** > **Next**.</span></span>

  ![Auswählen der macOS-Web-App-Vorlage](razor-pages-start/_static/web_app_template_vsmac.png)

* <span data-ttu-id="d65ff-221">Gehen Sie im Dialogfeld **Neue Webanwendung konfigurieren** folgendermaßen vor:</span><span class="sxs-lookup"><span data-stu-id="d65ff-221">In the **Configure the new Web Application** dialog:</span></span>

  * <span data-ttu-id="d65ff-222">Vergewissern Sie sich, dass **Authentifizierung** auf **Keine Authentifizierung** festgelegt ist.</span><span class="sxs-lookup"><span data-stu-id="d65ff-222">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="d65ff-223">Wenn eine Option zum Auswählen eines **Zielframeworks** angezeigt wird, wählen Sie die neueste 3.x-Version aus.</span><span class="sxs-lookup"><span data-stu-id="d65ff-223">If presented an option to select a **Target Framework**, select the latest 3.x version.</span></span>

  <span data-ttu-id="d65ff-224">Klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="d65ff-224">Select **Next**.</span></span>

* <span data-ttu-id="d65ff-225">Nennen Sie das Projekt **RazorPagesMovie**, und klicken Sie auf **Create (Erstellen)** .</span><span class="sxs-lookup"><span data-stu-id="d65ff-225">Name the project **RazorPagesMovie**, and then select **Create**.</span></span>

  ![Benennen des macOS-Projekts](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="d65ff-227">Ausführen der App</span><span class="sxs-lookup"><span data-stu-id="d65ff-227">Run the app</span></span>

  [!INCLUDE[](~/includes/run-the-app.md)]

## <a name="examine-the-project-files"></a><span data-ttu-id="d65ff-228">Überprüfen der Projektdateien</span><span class="sxs-lookup"><span data-stu-id="d65ff-228">Examine the project files</span></span>

<span data-ttu-id="d65ff-229">Es folgt eine Übersicht über die Hauptprojektordner und -dateien, mit denen Sie in späteren Tutorials arbeiten werden.</span><span class="sxs-lookup"><span data-stu-id="d65ff-229">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="d65ff-230">Ordner „Seiten“</span><span class="sxs-lookup"><span data-stu-id="d65ff-230">Pages folder</span></span>

<span data-ttu-id="d65ff-231">Enthält Razor-Seiten und unterstützende Dateien.</span><span class="sxs-lookup"><span data-stu-id="d65ff-231">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="d65ff-232">Jede Razor-Seite besteht aus einem Dateienpaar:</span><span class="sxs-lookup"><span data-stu-id="d65ff-232">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="d65ff-233">Einer *.cshtml*-Datei mit HTML-Markup mit C#-Code in Razor-Syntax.</span><span class="sxs-lookup"><span data-stu-id="d65ff-233">A *.cshtml* file that has HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="d65ff-234">Einer *.cshtml.cs*-Datei mit C#-Code zum Verarbeiten von Seitenereignissen.</span><span class="sxs-lookup"><span data-stu-id="d65ff-234">A *.cshtml.cs* file that has C# code that handles page events.</span></span>

<span data-ttu-id="d65ff-235">Unterstützende Dateien haben Namen, die mit einem Unterstrich beginnen.</span><span class="sxs-lookup"><span data-stu-id="d65ff-235">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="d65ff-236">Zum Beispiel sind in der Datei *_Layout.cshtml* Benutzeroberflächenelemente konfiguriert, die für alle Seiten gelten.</span><span class="sxs-lookup"><span data-stu-id="d65ff-236">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="d65ff-237">Mit dieser Datei werden das Navigationsmenü oben auf der Seite und der Urheberrechtshinweis unten auf der Seite eingerichtet.</span><span class="sxs-lookup"><span data-stu-id="d65ff-237">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="d65ff-238">Weitere Informationen finden Sie unter <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="d65ff-238">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="d65ff-239">Ordner „wwwroot“</span><span class="sxs-lookup"><span data-stu-id="d65ff-239">wwwroot folder</span></span>

<span data-ttu-id="d65ff-240">Enthält statische Dateien, z. B. HTML-, JavaScript- und CSS-Dateien.</span><span class="sxs-lookup"><span data-stu-id="d65ff-240">Contains static files, like HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="d65ff-241">Weitere Informationen finden Sie unter <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="d65ff-241">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="d65ff-242">appsettings.json</span><span class="sxs-lookup"><span data-stu-id="d65ff-242">appSettings.json</span></span>

<span data-ttu-id="d65ff-243">Enthält Konfigurationsdaten, z. B. Verbindungszeichenfolgen.</span><span class="sxs-lookup"><span data-stu-id="d65ff-243">Contains configuration data, like connection strings.</span></span> <span data-ttu-id="d65ff-244">Weitere Informationen finden Sie unter <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="d65ff-244">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="d65ff-245">Program.cs</span><span class="sxs-lookup"><span data-stu-id="d65ff-245">Program.cs</span></span>

<span data-ttu-id="d65ff-246">Enthält den Einstiegspunkt für das Programm.</span><span class="sxs-lookup"><span data-stu-id="d65ff-246">Contains the entry point for the program.</span></span> <span data-ttu-id="d65ff-247">Weitere Informationen finden Sie unter <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="d65ff-247">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="d65ff-248">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="d65ff-248">Startup.cs</span></span>

<span data-ttu-id="d65ff-249">Diese Datei enthält Code, mit dem das App-Verhalten konfiguriert wird.</span><span class="sxs-lookup"><span data-stu-id="d65ff-249">Contains code that configures app behavior.</span></span> <span data-ttu-id="d65ff-250">Weitere Informationen finden Sie unter <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="d65ff-250">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="next-steps"></a><span data-ttu-id="d65ff-251">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="d65ff-251">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="d65ff-252">Weiter: Hinzufügen eines Modells</span><span class="sxs-lookup"><span data-stu-id="d65ff-252">Next: Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end

<!--::: moniker range=">= aspnetcore-3.0" -->

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="d65ff-253">Dies ist das erste Tutorial aus einer Reihe.</span><span class="sxs-lookup"><span data-stu-id="d65ff-253">This is the first tutorial of a series.</span></span> <span data-ttu-id="d65ff-254">[In der Reihe](xref:tutorials/razor-pages/index) lernen Sie die Grundlagen zum Erstellen einer Razor Pages-Web-App mit ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d65ff-254">[The series](xref:tutorials/razor-pages/index) teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

<span data-ttu-id="d65ff-255">Eine erweiterte Einführung für Entwickler, die mit Controllern und Ansichten vertraut sind, finden Sie unter [Einführung in Razor Pages](xref:razor-pages/index).</span><span class="sxs-lookup"><span data-stu-id="d65ff-255">For a more advanced introduction aimed at developers who are familiar with controllers and views, see [Introduction to Razor Pages](xref:razor-pages/index).</span></span>

<span data-ttu-id="d65ff-256">Am Ende der Reihe verfügen Sie über eine App, mit der eine Filmdatenbank verwaltet werden kann.</span><span class="sxs-lookup"><span data-stu-id="d65ff-256">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

<span data-ttu-id="d65ff-257">[Zeigen Sie Beispielcode an, oder laden Sie diesen herunter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="d65ff-257">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="d65ff-258">In diesem Tutorial:</span><span class="sxs-lookup"><span data-stu-id="d65ff-258">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="d65ff-259">Create (erstellen) Sie eine Razor Pages-Web-App.</span><span class="sxs-lookup"><span data-stu-id="d65ff-259">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="d65ff-260">Führen Sie die App aus.</span><span class="sxs-lookup"><span data-stu-id="d65ff-260">Run the app.</span></span>
> * <span data-ttu-id="d65ff-261">Überprüfen Sie die Projektdateien.</span><span class="sxs-lookup"><span data-stu-id="d65ff-261">Examine the project files.</span></span>

<span data-ttu-id="d65ff-262">Am Ende dieses Tutorials verfügen Sie über eine funktionsfähige Razor Pages-Web-App, auf der Sie in späteren Tutorials aufbauen werden.</span><span class="sxs-lookup"><span data-stu-id="d65ff-262">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![Seite „Home“ oder Index](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="d65ff-264">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="d65ff-264">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d65ff-265">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d65ff-265">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="d65ff-266">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d65ff-266">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d65ff-267">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="d65ff-267">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="no-loccreate-a-no-locrazor-pages-web-app"></a><span data-ttu-id="d65ff-268">Create (Erstellen) einer Razor Pages-Web-App</span><span class="sxs-lookup"><span data-stu-id="d65ff-268">Create a Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d65ff-269">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d65ff-269">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="d65ff-270">Klicken Sie in Visual Studio im Menü **Datei** auf **Neu** > **Projekt**.</span><span class="sxs-lookup"><span data-stu-id="d65ff-270">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>

* <span data-ttu-id="d65ff-271">Create (Erstellen) Sie eine neue ASP.NET Core-Webanwendung, und klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="d65ff-271">Create a new ASP.NET Core Web Application and select **Next**.</span></span>

  ![neue ASP.NET Core-Webanwendung](razor-pages-start/_static/np_2.1.png)

* <span data-ttu-id="d65ff-273">Nennen Sie das Projekt **RazorPagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="d65ff-273">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="d65ff-274">Es ist wichtig, den Namen *RazorPagesMovie* zu verwenden, damit die Namespaces übereinstimmen, wenn Sie Code kopieren und einfügen.</span><span class="sxs-lookup"><span data-stu-id="d65ff-274">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>

  ![neue ASP.NET Core-Webanwendung](razor-pages-start/_static/config.png)

* <span data-ttu-id="d65ff-276">Wählen Sie in der Dropdownliste nacheinander **ASP.NET Core 2.2**, **Webanwendung** und **Create (Erstellen)** aus.</span><span class="sxs-lookup"><span data-stu-id="d65ff-276">Select **ASP.NET Core 2.2** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![neue ASP.NET Core-Webanwendung](razor-pages-start/_static/np_2_2.2.png)

  <span data-ttu-id="d65ff-278">Das folgende Startprojekt wird erstellt:</span><span class="sxs-lookup"><span data-stu-id="d65ff-278">The following starter project is created:</span></span>

  ![Projektmappen-Explorer](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="d65ff-280">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d65ff-280">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="d65ff-281">Öffnen Sie das [integrierte Terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="d65ff-281">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="d65ff-282">Wechseln Sie zum Verzeichnis (`cd`), das das Projekt enthalten soll.</span><span class="sxs-lookup"><span data-stu-id="d65ff-282">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="d65ff-283">Führen Sie die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="d65ff-283">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="d65ff-284">Der Befehl `dotnet new` erstellt ein neues Razor Pages-Projekt im Ordner *RazorPagesMovie*.</span><span class="sxs-lookup"><span data-stu-id="d65ff-284">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="d65ff-285">Der Befehl `code` öffnet den Ordner *RazorPagesMovie* in der aktuellen Instanz von Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="d65ff-285">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="d65ff-286">Sobald das OmniSharp-Flammensymbol in der Statusleiste grün angezeigt wird, wird ein Dialogfeld mit folgender Meldung angezeigt: **Die erforderlichen Objekte zum Erstellen und Debuggen sind in "RazorPagesMovie" nicht vorhanden. Sollen sie hinzugefügt werden?**</span><span class="sxs-lookup"><span data-stu-id="d65ff-286">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="d65ff-287">Wählen Sie **Ja**.</span><span class="sxs-lookup"><span data-stu-id="d65ff-287">Select **Yes**.</span></span>

  <span data-ttu-id="d65ff-288">Ein *VSCODE*-Verzeichnis, das die Dateien *launch.json* und *tasks.json* enthält, wird dem Stammverzeichnis des Projekts hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="d65ff-288">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d65ff-289">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="d65ff-289">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="d65ff-290">Klicken Sie auf **Datei** > **Neue Projektmappe**.</span><span class="sxs-lookup"><span data-stu-id="d65ff-290">Select **File** > **New Solution**.</span></span>

![Neue Projektmappe in macOS](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="d65ff-292">Navigieren Sie in Visual Studio für Mac-Versionen vor Version 8.6 zu **.NET Core** > **App** > **Webanwendung** > **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="d65ff-292">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application** > **Next**.</span></span> <span data-ttu-id="d65ff-293">Bei Version 8.6 oder höher klicken Sie auf **Web and Console** > **App** > **Webanwendung** > **Weiter** (Web und Konsole).</span><span class="sxs-lookup"><span data-stu-id="d65ff-293">In version 8.6 or later, select **Web and Console** > **App** > **Web Application** > **Next**.</span></span>

* <span data-ttu-id="d65ff-294">Gehen Sie im Dialogfeld **Neue Webanwendung konfigurieren** folgendermaßen vor:</span><span class="sxs-lookup"><span data-stu-id="d65ff-294">In the **Configure the new Web Application** dialog:</span></span>

  * <span data-ttu-id="d65ff-295">Vergewissern Sie sich, dass **Authentifizierung** auf **Keine Authentifizierung** festgelegt ist.</span><span class="sxs-lookup"><span data-stu-id="d65ff-295">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="d65ff-296">Wenn eine Option zum Auswählen eines **Zielframeworks** angezeigt wird, wählen Sie die neueste 2.x-Version aus.</span><span class="sxs-lookup"><span data-stu-id="d65ff-296">If presented an option to select a **Target Framework**, select the latest 2.x version.</span></span>

  <span data-ttu-id="d65ff-297">Klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="d65ff-297">Select **Next**.</span></span>

* <span data-ttu-id="d65ff-298">Nennen Sie das Projekt **RazorPagesMovie**, und klicken Sie auf **Create (Erstellen)** .</span><span class="sxs-lookup"><span data-stu-id="d65ff-298">Name the project **RazorPagesMovie**, and then select **Create**.</span></span>

  ![nameproj](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="d65ff-300">Ausführen der App</span><span class="sxs-lookup"><span data-stu-id="d65ff-300">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d65ff-301">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d65ff-301">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="d65ff-302">Drücken Sie STRG+F5, um die Ausführung ohne den Debugger zu starten.</span><span class="sxs-lookup"><span data-stu-id="d65ff-302">Press Ctrl+F5 to run without the debugger.</span></span>

  <span data-ttu-id="d65ff-303">Das Starten der App mit <kbd>STRG+F5</kbd> (Nicht-Debugmodus) ermöglicht die Änderung des Codes, das Speichern der Datei, das Aktualisieren des Browsers und das Anzeigen von Codeänderungen.</span><span class="sxs-lookup"><span data-stu-id="d65ff-303">Launching the app with <kbd>Ctrl+F5</kbd> (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="d65ff-304">Viele Entwickler bevorzugen den Nicht-Debugmodus, um die App schnell zu starten und Änderungen anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="d65ff-304">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="d65ff-305">Visual Studio startet [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) und führt die App aus.</span><span class="sxs-lookup"><span data-stu-id="d65ff-305">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="d65ff-306">Die Adressleiste zeigt `localhost:port#` an, nicht `example.com`.</span><span class="sxs-lookup"><span data-stu-id="d65ff-306">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="d65ff-307">Das liegt daran, dass es sich bei `localhost` um den Standardhostnamen für den lokalen Computer handelt.</span><span class="sxs-lookup"><span data-stu-id="d65ff-307">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="d65ff-308">„Localhost“ dient nur Webanforderungen vom lokalen Computer.</span><span class="sxs-lookup"><span data-stu-id="d65ff-308">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="d65ff-309">Wenn in Visual Studio ein Webprojekt erstellt wird, wird für den Webserver ein zufälliger Port verwendet.</span><span class="sxs-lookup"><span data-stu-id="d65ff-309">When Visual Studio creates a web project, a random port is used for the web server.</span></span>

* <span data-ttu-id="d65ff-310">Wählen Sie auf der Homepage der App **Akzeptieren** aus, um der Nachverfolgung zuzustimmen.</span><span class="sxs-lookup"><span data-stu-id="d65ff-310">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="d65ff-311">Diese App verfolgt keine persönlichen Informationen nach, aber die Projektvorlage enthält das Einverständniserklärungsfeature für den Fall, dass die App die [allgemeine Datenschutz-Grundverordnung (DSGVO)](xref:security/gdpr) der Europäischen Union erfüllen muss.</span><span class="sxs-lookup"><span data-stu-id="d65ff-311">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Seite „Home“ oder Index](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="d65ff-313">Die folgende Abbildung zeigt die App, nachdem Sie die Zustimmung zur Nachverfolgung gegeben haben:</span><span class="sxs-lookup"><span data-stu-id="d65ff-313">The following image shows the app after consent to tracking is provided:</span></span>

  ![Seite „Home“ oder Index](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-code"></a>[<span data-ttu-id="d65ff-315">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d65ff-315">Visual Studio Code</span></span>](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* <span data-ttu-id="d65ff-316">Drücken Sie <kbd>STRG+F5</kbd>, um die Ausführung ohne Debugger zu starten.</span><span class="sxs-lookup"><span data-stu-id="d65ff-316">Press <kbd>Ctrl+F5</kbd> to run without the debugger.</span></span>

  <span data-ttu-id="d65ff-317">Das Starten der App mit <kbd>STRG+F5</kbd> (Nicht-Debugmodus) ermöglicht die Änderung des Codes, das Speichern der Datei, das Aktualisieren des Browsers und das Anzeigen von Codeänderungen.</span><span class="sxs-lookup"><span data-stu-id="d65ff-317">Launching the app with <kbd>Ctrl+F5</kbd> (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="d65ff-318">Viele Entwickler bevorzugen den Nicht-Debugmodus, um die App schnell zu starten und Änderungen anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="d65ff-318">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>

  <span data-ttu-id="d65ff-319">Visual Studio Code startet [Kestrel](xref:fundamentals/servers/kestrel) und einen Browser und navigiert zu `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="d65ff-319">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and goes to `http://localhost:5001`.</span></span> <span data-ttu-id="d65ff-320">Die Adressleiste zeigt `localhost:port#` an, nicht `example.com`.</span><span class="sxs-lookup"><span data-stu-id="d65ff-320">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="d65ff-321">Das liegt daran, dass es sich bei `localhost` um den Standardhostnamen für den lokalen Computer handelt.</span><span class="sxs-lookup"><span data-stu-id="d65ff-321">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="d65ff-322">„Localhost“ dient nur Webanforderungen vom lokalen Computer.</span><span class="sxs-lookup"><span data-stu-id="d65ff-322">Localhost only serves web requests from the local computer.</span></span>

* <span data-ttu-id="d65ff-323">Wählen Sie auf der Homepage der App **Akzeptieren** aus, um der Nachverfolgung zuzustimmen.</span><span class="sxs-lookup"><span data-stu-id="d65ff-323">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="d65ff-324">Diese App verfolgt keine persönlichen Informationen nach, aber die Projektvorlage enthält das Einverständniserklärungsfeature für den Fall, dass die App die [allgemeine Datenschutz-Grundverordnung (DSGVO)](xref:security/gdpr) der Europäischen Union erfüllen muss.</span><span class="sxs-lookup"><span data-stu-id="d65ff-324">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Seite „Home“ oder Index](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="d65ff-326">Die folgende Abbildung zeigt die App, nachdem Sie das Einverständnis zur Nachverfolgung gegeben haben:</span><span class="sxs-lookup"><span data-stu-id="d65ff-326">The following image shows the app after you give consent to tracking:</span></span>

  ![Seite „Home“ oder Index](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d65ff-328">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="d65ff-328">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="d65ff-329">Drücken Sie **CMD+OPT+F5** für eine Ausführung ohne Debugger.</span><span class="sxs-lookup"><span data-stu-id="d65ff-329">Press **Cmd-Opt-F5** to run without the debugger.</span></span>

  <span data-ttu-id="d65ff-330">Das Starten der App mit <kbd>Cmd+Opt+F5</kbd> (Nicht-Debugmodus) ermöglicht die Änderung des Codes, das Speichern der Datei, das Aktualisieren des Browsers und das Anzeigen von Codeänderungen.</span><span class="sxs-lookup"><span data-stu-id="d65ff-330">Launching the app with <kbd>Cmd+Opt+F5</kbd> (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="d65ff-331">Viele Entwickler bevorzugen den Nicht-Debugmodus, um die App schnell zu starten und Änderungen anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="d65ff-331">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>

  <span data-ttu-id="d65ff-332">Visual Studio startet dann [Kestrel](xref:fundamentals/servers/kestrel) und einen Browser und navigiert zu `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="d65ff-332">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and goes to `http://localhost:5001`.</span></span>

* <span data-ttu-id="d65ff-333">Wählen Sie auf der Homepage der App **Akzeptieren** aus, um der Nachverfolgung zuzustimmen.</span><span class="sxs-lookup"><span data-stu-id="d65ff-333">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="d65ff-334">Diese App verfolgt keine persönlichen Informationen nach, aber die Projektvorlage enthält das Einverständniserklärungsfeature für den Fall, dass die App die [allgemeine Datenschutz-Grundverordnung (DSGVO)](xref:security/gdpr) der Europäischen Union erfüllen muss.</span><span class="sxs-lookup"><span data-stu-id="d65ff-334">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Seite „Home“ oder Index](razor-pages-start/_static/homeGDPR2.2_safari.png)

  <span data-ttu-id="d65ff-336">Die folgende Abbildung zeigt die App, nachdem Sie die Zustimmung zur Nachverfolgung gegeben haben:</span><span class="sxs-lookup"><span data-stu-id="d65ff-336">The following image shows the app after consent to tracking is provided:</span></span>

  ![Seite „Home“ oder Index](razor-pages-start/_static/home2.2_safari.png)

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a><span data-ttu-id="d65ff-338">Überprüfen der Projektdateien</span><span class="sxs-lookup"><span data-stu-id="d65ff-338">Examine the project files</span></span>

<span data-ttu-id="d65ff-339">Es folgt eine Übersicht über die Hauptprojektordner und -dateien, mit denen Sie in späteren Tutorials arbeiten werden.</span><span class="sxs-lookup"><span data-stu-id="d65ff-339">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="d65ff-340">Ordner „Seiten“</span><span class="sxs-lookup"><span data-stu-id="d65ff-340">Pages folder</span></span>

<span data-ttu-id="d65ff-341">Enthält Razor-Seiten und unterstützende Dateien.</span><span class="sxs-lookup"><span data-stu-id="d65ff-341">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="d65ff-342">Jede Razor-Seite besteht aus einem Dateienpaar:</span><span class="sxs-lookup"><span data-stu-id="d65ff-342">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="d65ff-343">Einer *.cshtml*-Datei mit HTML-Markup mit C#-Code in Razor-Syntax.</span><span class="sxs-lookup"><span data-stu-id="d65ff-343">A *.cshtml* file that has HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="d65ff-344">Einer *.cshtml.cs*-Datei mit C#-Code zum Verarbeiten von Seitenereignissen.</span><span class="sxs-lookup"><span data-stu-id="d65ff-344">A *.cshtml.cs* file that has C# code that handles page events.</span></span>

<span data-ttu-id="d65ff-345">Unterstützende Dateien haben Namen, die mit einem Unterstrich beginnen.</span><span class="sxs-lookup"><span data-stu-id="d65ff-345">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="d65ff-346">Zum Beispiel sind in der Datei *_Layout.cshtml* Benutzeroberflächenelemente konfiguriert, die für alle Seiten gelten.</span><span class="sxs-lookup"><span data-stu-id="d65ff-346">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="d65ff-347">Mit dieser Datei werden das Navigationsmenü oben auf der Seite und der Urheberrechtshinweis unten auf der Seite eingerichtet.</span><span class="sxs-lookup"><span data-stu-id="d65ff-347">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="d65ff-348">Weitere Informationen finden Sie unter <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="d65ff-348">For more information, see <xref:mvc/views/layout>.</span></span>

<span data-ttu-id="d65ff-349">Razor-Seiten werden von `PageModel` abgeleitet.</span><span class="sxs-lookup"><span data-stu-id="d65ff-349">Razor Pages are derived from `PageModel`.</span></span> <span data-ttu-id="d65ff-350">Gemäß der Konvention wird die von `PageModel` abgeleitete Klasse `<PageName>Model` genannt.</span><span class="sxs-lookup"><span data-stu-id="d65ff-350">By convention, the `PageModel`-derived class is named `<PageName>Model`.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="d65ff-351">Ordner „wwwroot“</span><span class="sxs-lookup"><span data-stu-id="d65ff-351">wwwroot folder</span></span>

<span data-ttu-id="d65ff-352">Enthält statische Dateien, z. B. HTML-, JavaScript- und CSS-Dateien.</span><span class="sxs-lookup"><span data-stu-id="d65ff-352">Contains static files, like HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="d65ff-353">Weitere Informationen finden Sie unter <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="d65ff-353">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="d65ff-354">appsettings.json</span><span class="sxs-lookup"><span data-stu-id="d65ff-354">appSettings.json</span></span>

<span data-ttu-id="d65ff-355">Enthält Konfigurationsdaten, z. B. Verbindungszeichenfolgen.</span><span class="sxs-lookup"><span data-stu-id="d65ff-355">Contains configuration data, like connection strings.</span></span> <span data-ttu-id="d65ff-356">Weitere Informationen finden Sie unter <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="d65ff-356">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="d65ff-357">Program.cs</span><span class="sxs-lookup"><span data-stu-id="d65ff-357">Program.cs</span></span>

<span data-ttu-id="d65ff-358">Enthält den Einstiegspunkt für das Programm.</span><span class="sxs-lookup"><span data-stu-id="d65ff-358">Contains the entry point for the program.</span></span> <span data-ttu-id="d65ff-359">Weitere Informationen finden Sie unter <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="d65ff-359">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="d65ff-360">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="d65ff-360">Startup.cs</span></span>

<span data-ttu-id="d65ff-361">Enthält Code, mit dem das App-Verhalten konfiguriert wird, beispielsweise, ob die App Zustimmung für cookies erfordert.</span><span class="sxs-lookup"><span data-stu-id="d65ff-361">Contains code that configures app behavior, such as whether it requires consent for cookies.</span></span> <span data-ttu-id="d65ff-362">Weitere Informationen finden Sie unter <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="d65ff-362">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d65ff-363">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="d65ff-363">Additional resources</span></span>

* [<span data-ttu-id="d65ff-364">Dieses Tutorial auf YouTube</span><span class="sxs-lookup"><span data-stu-id="d65ff-364">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=F0SP7Ry4flQ&feature=youtu.be)

## <a name="next-steps"></a><span data-ttu-id="d65ff-365">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="d65ff-365">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="d65ff-366">Weiter: Hinzufügen eines Modells</span><span class="sxs-lookup"><span data-stu-id="d65ff-366">Next: Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end
