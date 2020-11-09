---
title: Erste Schritte mit ASP.NET Core MVC
author: rick-anderson
description: Hier finden Sie Informationen zum Einstieg in ASP.NET Core MVC.
ms.author: riande
ms.date: 10/16/2019
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
uid: tutorials/first-mvc-app/start-mvc
ms.openlocfilehash: cf17aaf8eff342c378536d4f635e09b936459bee
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93052902"
---
# <a name="get-started-with-aspnet-core-mvc"></a><span data-ttu-id="b2577-103">Erste Schritte mit ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="b2577-103">Get started with ASP.NET Core MVC</span></span>

<span data-ttu-id="b2577-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="b2577-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="b2577-105">In diesem Tutorial lernen Sie Grundlegendes zur Erstellung einer ASP.NET Core-MVC-Web-App.</span><span class="sxs-lookup"><span data-stu-id="b2577-105">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="b2577-106">Die App verwaltet eine Datenbank mit Filmtiteln.</span><span class="sxs-lookup"><span data-stu-id="b2577-106">The app manages a database of movie titles.</span></span> <span data-ttu-id="b2577-107">Sie lernen Folgendes:</span><span class="sxs-lookup"><span data-stu-id="b2577-107">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="b2577-108">Erstellen einer Web-App</span><span class="sxs-lookup"><span data-stu-id="b2577-108">Create a web app.</span></span>
> * <span data-ttu-id="b2577-109">Hinzufügen eines Modells und Erstellen eines Gerüsts für das Modell</span><span class="sxs-lookup"><span data-stu-id="b2577-109">Add and scaffold a model.</span></span>
> * <span data-ttu-id="b2577-110">Arbeiten mit einer Datenbank</span><span class="sxs-lookup"><span data-stu-id="b2577-110">Work with a database.</span></span>
> * <span data-ttu-id="b2577-111">Hinzufügen von Such- und Überprüfungsfunktionen</span><span class="sxs-lookup"><span data-stu-id="b2577-111">Add search and validation.</span></span>

<span data-ttu-id="b2577-112">Am Ende verfügen Sie über eine App, die Filmdaten verwalten und anzeigen kann.</span><span class="sxs-lookup"><span data-stu-id="b2577-112">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="b2577-113">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="b2577-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b2577-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b2577-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="b2577-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b2577-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="b2577-116">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="b2577-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-app"></a><span data-ttu-id="b2577-117">Erstellen einer Web-App</span><span class="sxs-lookup"><span data-stu-id="b2577-117">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b2577-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b2577-118">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b2577-119">Wählen Sie in Visual Studio die Option **Neues Projekt erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="b2577-119">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="b2577-120">Wählen Sie **ASP.NET Core-Webanwendung** > **Weiter** aus.</span><span class="sxs-lookup"><span data-stu-id="b2577-120">Select **ASP.NET Core Web Application** > **Next**.</span></span>

![neue ASP.NET Core-Webanwendung](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="b2577-122">Geben Sie dem Projekt den Namen **MvcMovie** , und klicken Sie dann auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="b2577-122">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="b2577-123">Es ist wichtig, dem Projekt den Namen **MvcMovie** zu geben, damit beim Kopieren von Code der Namespace übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="b2577-123">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![neue ASP.NET Core-Webanwendung](start-mvc/_static/config.png)

* <span data-ttu-id="b2577-125">Wählen Sie **Webanwendung (Model View Controller)** aus.</span><span class="sxs-lookup"><span data-stu-id="b2577-125">Select **Web Application(Model-View-Controller)**.</span></span> <span data-ttu-id="b2577-126">Wählen Sie in den Dropdownfeldern **.NET Core** und **ASP.NET Core 3.1** und anschließend **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="b2577-126">From the dropdown boxes, select **.NET Core** and **ASP.NET Core 3.1** , then select **Create**.</span></span>

![<span data-ttu-id="b2577-127">Dialogfeld „Neues Projekt“, .NET Core im linken Bereich, ASP.NET Core-Web-App</span><span class="sxs-lookup"><span data-stu-id="b2577-127">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project30.png)

<span data-ttu-id="b2577-128">Visual Studio verwendet die Standardvorlage für das MVC-Projekt, das Sie gerade erstellt haben.</span><span class="sxs-lookup"><span data-stu-id="b2577-128">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="b2577-129">Wenn Sie einen Projektnamen eingeben und einige Optionen festlegen, funktioniert Ihre App bereits.</span><span class="sxs-lookup"><span data-stu-id="b2577-129">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="b2577-130">Hierbei handelt es sich um ein grundlegendes Startprojekt.</span><span class="sxs-lookup"><span data-stu-id="b2577-130">This is a basic starter project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="b2577-131">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b2577-131">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="b2577-132">Das Tutorial setzt voraus, dass Sie mit VS Code vertraut sind.</span><span class="sxs-lookup"><span data-stu-id="b2577-132">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="b2577-133">Weitere Informationen finden Sie unter [Erste Schritte mit VS Code](https://code.visualstudio.com/docs) und [Hilfe zu Visual Studio Code](#visual-studio-code-help).</span><span class="sxs-lookup"><span data-stu-id="b2577-133">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="b2577-134">Öffnen Sie das [integrierte Terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="b2577-134">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="b2577-135">Wechseln Sie mit `cd` zu einem Ordner, der das Projekt enthalten soll.</span><span class="sxs-lookup"><span data-stu-id="b2577-135">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="b2577-136">Führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="b2577-136">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="b2577-137">Es wird ein Dialogfeld mit folgender Meldung angezeigt: **Die erforderlichen Objekte zum Erstellen und Debuggen sind in "MvcMovie" nicht vorhanden. Sollen sie hinzugefügt werden?**</span><span class="sxs-lookup"><span data-stu-id="b2577-137">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="b2577-138">Wählen Sie **Ja** aus.</span><span class="sxs-lookup"><span data-stu-id="b2577-138">Select **Yes**</span></span>

  * <span data-ttu-id="b2577-139">`dotnet new mvc -o MvcMovie`: Erstellt ein neues ASP.NET Core MVC-Projekt im Ordner *MvcMovie*.</span><span class="sxs-lookup"><span data-stu-id="b2577-139">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="b2577-140">`code -r MvcMovie`: Lädt die Projektdatei *MvcMovie.csproj* in Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="b2577-140">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="b2577-141">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="b2577-141">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="b2577-142">Klicken Sie auf **Datei** > **Neue Projektmappe**.</span><span class="sxs-lookup"><span data-stu-id="b2577-142">Select **File** > **New Solution**.</span></span>

  ![Neue Projektmappe in macOS](start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="b2577-144">Navigieren Sie in Visual Studio für Mac-Versionen vor Version 8.6 zu **.NET Core** > **App** > **Webanwendung (Model-View-Controller)**  > **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="b2577-144">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span> <span data-ttu-id="b2577-145">Bei Version 8.6 oder höher klicken Sie auf **Web and Console** > **App** > **Webanwendung (Model-View-Controller)**  > **Weiter** (Web und Konsole).</span><span class="sxs-lookup"><span data-stu-id="b2577-145">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

  ![Auswählen der macOS-Web-App-Vorlage](start-mvc/_static/web_app_template_vsmac.png)

* <span data-ttu-id="b2577-147">Gehen Sie im Dialogfeld **Neue Webanwendung konfigurieren** folgendermaßen vor:</span><span class="sxs-lookup"><span data-stu-id="b2577-147">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="b2577-148">Vergewissern Sie sich, dass **Authentifizierung** auf **Keine Authentifizierung** festgelegt ist.</span><span class="sxs-lookup"><span data-stu-id="b2577-148">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="b2577-149">Wenn eine Option zum Auswählen eines **Zielframeworks** angezeigt wird, wählen Sie die neueste 3.x-Version aus.</span><span class="sxs-lookup"><span data-stu-id="b2577-149">If presented an option to select a **Target Framework** , select the latest 3.x version.</span></span>

  <span data-ttu-id="b2577-150">Klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="b2577-150">Select **Next**.</span></span>

* <span data-ttu-id="b2577-151">Nennen Sie das Projekt **MvcMovie** , und wählen Sie dann **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="b2577-151">Name the project **MvcMovie** , and then select **Create**.</span></span>

  ![Benennen des macOS-Projekts](start-mvc/_static/MvcMovie.png)

---

### <a name="run-the-app"></a><span data-ttu-id="b2577-153">Ausführen der App</span><span class="sxs-lookup"><span data-stu-id="b2577-153">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b2577-154">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b2577-154">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b2577-155">Drücken Sie **STRG+F5** , um die App im Nicht-Debugmodus auszuführen.</span><span class="sxs-lookup"><span data-stu-id="b2577-155">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="b2577-156">Visual Studio startet [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) und führt die App aus.</span><span class="sxs-lookup"><span data-stu-id="b2577-156">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="b2577-157">Beachten Sie, dass die Adressleiste `localhost:port#` und nicht etwas wie `example.com` anzeigt.</span><span class="sxs-lookup"><span data-stu-id="b2577-157">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="b2577-158">Das liegt daran, dass es sich bei `localhost` um den Standard-Hostnamen für Ihren lokalen Computer handelt.</span><span class="sxs-lookup"><span data-stu-id="b2577-158">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="b2577-159">Wenn in Visual Studio ein Webprojekt erstellt wird, wird für den Webserver ein zufälliger Port verwendet.</span><span class="sxs-lookup"><span data-stu-id="b2577-159">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="b2577-160">Das Starten der App mit STRG+F5 (Nicht-Debugmodus) ermöglicht die Änderung des Codes, das Speichern der Datei, das Aktualisieren des Browsers und das Anzeigen von Codeänderungen.</span><span class="sxs-lookup"><span data-stu-id="b2577-160">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="b2577-161">Viele Entwickler bevorzugen den Nicht-Debugmodus, um die App schnell zu starten und Änderungen anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="b2577-161">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="b2577-162">Sie können die App über das Menüelement **Debuggen** im Debugmodus oder Nicht-Debugmodus starten:</span><span class="sxs-lookup"><span data-stu-id="b2577-162">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![Menü „Debuggen“](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="b2577-164">Sie können die App debuggen, indem Sie die Schaltfläche **IIS Express** auswählen.</span><span class="sxs-lookup"><span data-stu-id="b2577-164">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

  <span data-ttu-id="b2577-166">Auf der folgenden Abbildung sehen Sie die App:</span><span class="sxs-lookup"><span data-stu-id="b2577-166">The following image shows the app:</span></span>

  ![Start- oder Indexseite](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="b2577-168">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b2577-168">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="b2577-169">Drücken Sie STRG+F5, um die Ausführung ohne den Debugger zu starten.</span><span class="sxs-lookup"><span data-stu-id="b2577-169">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="b2577-170">Visual Studio Code startet [Kestrel](xref:fundamentals/servers/kestrel) und einen Browser und navigiert zu `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="b2577-170">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="b2577-171">Die Adressleiste zeigt `localhost:port:5001` an, nicht `example.com`.</span><span class="sxs-lookup"><span data-stu-id="b2577-171">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="b2577-172">Das liegt daran, dass es sich bei `localhost` um den Standardhostnamen für den lokalen Computer handelt.</span><span class="sxs-lookup"><span data-stu-id="b2577-172">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="b2577-173">„Localhost“ dient nur Webanforderungen vom lokalen Computer.</span><span class="sxs-lookup"><span data-stu-id="b2577-173">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="b2577-174">Das Starten der App mit STRG+F5 (Nicht-Debugmodus) ermöglicht die Änderung des Codes, das Speichern der Datei, das Aktualisieren des Browsers und das Anzeigen von Codeänderungen.</span><span class="sxs-lookup"><span data-stu-id="b2577-174">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="b2577-175">Viele Entwickler bevorzugen den Nicht-Debugmodus, um die Seite zu aktualisieren und Änderungen anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="b2577-175">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

  ![Start- oder Indexseite](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="b2577-177">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="b2577-177">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="b2577-178">Wählen Sie **Ausführen** > **Ohne Debuggen starten** aus, um die App zu starten.</span><span class="sxs-lookup"><span data-stu-id="b2577-178">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="b2577-179">Visual Studio für Mac startet den [Kestrel](xref:fundamentals/servers/index#kestrel)-Server und einen Browser und navigiert zu `http://localhost:port`, wobei es sich bei *port* um eine zufällig ausgewählte Portnummer handelt.</span><span class="sxs-lookup"><span data-stu-id="b2577-179">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="b2577-180">Die Adressleiste zeigt `localhost:port#` an, nicht `example.com`.</span><span class="sxs-lookup"><span data-stu-id="b2577-180">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="b2577-181">Das liegt daran, dass es sich bei `localhost` um den Standard-Hostnamen für Ihren lokalen Computer handelt.</span><span class="sxs-lookup"><span data-stu-id="b2577-181">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="b2577-182">Wenn in Visual Studio ein Webprojekt erstellt wird, wird für den Webserver ein zufälliger Port verwendet.</span><span class="sxs-lookup"><span data-stu-id="b2577-182">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="b2577-183">Wenn Sie die App ausführen, wird eine andere Portnummer angezeigt.</span><span class="sxs-lookup"><span data-stu-id="b2577-183">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="b2577-184">Sie können die App über das Menü **Ausführen** im Debugmodus oder Nicht-Debugmodus starten.</span><span class="sxs-lookup"><span data-stu-id="b2577-184">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

  <span data-ttu-id="b2577-185">Auf der folgenden Abbildung sehen Sie die App:</span><span class="sxs-lookup"><span data-stu-id="b2577-185">The following image shows the app:</span></span>

  ![Start- oder Indexseite](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="b2577-187">Im nächsten Teil dieses Tutorials erfahren Sie mehr über MVC und beginnen mit dem Schreiben von Code.</span><span class="sxs-lookup"><span data-stu-id="b2577-187">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="b2577-188">Nächste</span><span class="sxs-lookup"><span data-stu-id="b2577-188">Next</span></span>](adding-controller.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="b2577-189">In diesem Tutorial lernen Sie Grundlegendes zur Erstellung einer ASP.NET Core-MVC-Web-App.</span><span class="sxs-lookup"><span data-stu-id="b2577-189">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="b2577-190">Die App verwaltet eine Datenbank mit Filmtiteln.</span><span class="sxs-lookup"><span data-stu-id="b2577-190">The app manages a database of movie titles.</span></span> <span data-ttu-id="b2577-191">Sie lernen Folgendes:</span><span class="sxs-lookup"><span data-stu-id="b2577-191">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="b2577-192">Erstellen einer Web-App</span><span class="sxs-lookup"><span data-stu-id="b2577-192">Create a web app.</span></span>
> * <span data-ttu-id="b2577-193">Hinzufügen eines Modells und Erstellen eines Gerüsts für das Modell</span><span class="sxs-lookup"><span data-stu-id="b2577-193">Add and scaffold a model.</span></span>
> * <span data-ttu-id="b2577-194">Arbeiten mit einer Datenbank</span><span class="sxs-lookup"><span data-stu-id="b2577-194">Work with a database.</span></span>
> * <span data-ttu-id="b2577-195">Hinzufügen von Such- und Überprüfungsfunktionen</span><span class="sxs-lookup"><span data-stu-id="b2577-195">Add search and validation.</span></span>

<span data-ttu-id="b2577-196">Am Ende verfügen Sie über eine App, die Filmdaten verwalten und anzeigen kann.</span><span class="sxs-lookup"><span data-stu-id="b2577-196">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="b2577-197">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="b2577-197">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b2577-198">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b2577-198">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="b2577-199">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b2577-199">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="b2577-200">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="b2577-200">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---
## <a name="create-a-web-app"></a><span data-ttu-id="b2577-201">Erstellen einer Web-App</span><span class="sxs-lookup"><span data-stu-id="b2577-201">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b2577-202">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b2577-202">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b2577-203">Wählen Sie in Visual Studio die Option **Neues Projekt erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="b2577-203">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="b2577-204">Klicken Sie auf **ASP.NET Core-Webanwendung** und dann auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="b2577-204">Select **ASP.NET Core Web Application** and then select **Next**.</span></span>

![neue ASP.NET Core-Webanwendung](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="b2577-206">Geben Sie dem Projekt den Namen **MvcMovie** , und klicken Sie dann auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="b2577-206">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="b2577-207">Es ist wichtig, dem Projekt den Namen **MvcMovie** zu geben, damit beim Kopieren von Code der Namespace übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="b2577-207">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![neue ASP.NET Core-Webanwendung](start-mvc/_static/config.png)


* <span data-ttu-id="b2577-209">Klicken Sie auf **Webanwendung (Model View Controller)** und anschließend auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="b2577-209">Select **Web Application(Model-View-Controller)** , and then select **Create**.</span></span>

![<span data-ttu-id="b2577-210">Dialogfeld „Neues Projekt“, .NET Core im linken Bereich, ASP.NET Core-Web-App</span><span class="sxs-lookup"><span data-stu-id="b2577-210">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project22-21.png)

<span data-ttu-id="b2577-211">Visual Studio verwendet die Standardvorlage für das MVC-Projekt, das Sie gerade erstellt haben.</span><span class="sxs-lookup"><span data-stu-id="b2577-211">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="b2577-212">Wenn Sie einen Projektnamen eingeben und einige Optionen festlegen, funktioniert Ihre App bereits.</span><span class="sxs-lookup"><span data-stu-id="b2577-212">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="b2577-213">Dies ist ein grundlegendes Startprojekt und ein guter Einstieg.</span><span class="sxs-lookup"><span data-stu-id="b2577-213">This is a basic starter project, and it's a good place to start.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="b2577-214">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b2577-214">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="b2577-215">Das Tutorial setzt voraus, dass Sie mit VS Code vertraut sind.</span><span class="sxs-lookup"><span data-stu-id="b2577-215">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="b2577-216">Weitere Informationen finden Sie unter [Erste Schritte mit VS Code](https://code.visualstudio.com/docs) und [Hilfe zu Visual Studio Code](#visual-studio-code-help).</span><span class="sxs-lookup"><span data-stu-id="b2577-216">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="b2577-217">Öffnen Sie das [integrierte Terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="b2577-217">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="b2577-218">Wechseln Sie mit `cd` zu einem Ordner, der das Projekt enthalten soll.</span><span class="sxs-lookup"><span data-stu-id="b2577-218">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="b2577-219">Führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="b2577-219">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="b2577-220">Es wird ein Dialogfeld mit folgender Meldung angezeigt: **Die erforderlichen Objekte zum Erstellen und Debuggen sind in "MvcMovie" nicht vorhanden. Sollen sie hinzugefügt werden?**</span><span class="sxs-lookup"><span data-stu-id="b2577-220">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="b2577-221">Wählen Sie **Ja** aus.</span><span class="sxs-lookup"><span data-stu-id="b2577-221">Select **Yes**</span></span>

  * <span data-ttu-id="b2577-222">`dotnet new mvc -o MvcMovie`: Erstellt ein neues ASP.NET Core MVC-Projekt im Ordner *MvcMovie*.</span><span class="sxs-lookup"><span data-stu-id="b2577-222">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="b2577-223">`code -r MvcMovie`: Lädt die Projektdatei *MvcMovie.csproj* in Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="b2577-223">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="b2577-224">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="b2577-224">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="b2577-225">Klicken Sie auf **Datei** > **Neue Projektmappe**.</span><span class="sxs-lookup"><span data-stu-id="b2577-225">Select **File** > **New Solution**.</span></span>

  ![Neue Projektmappe in macOS](./start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="b2577-227">Navigieren Sie in Visual Studio für Mac-Versionen vor Version 8.6 zu **.NET Core** > **App** > **Webanwendung (Model-View-Controller)**  > **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="b2577-227">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span> <span data-ttu-id="b2577-228">Bei Version 8.6 oder höher klicken Sie auf **Web and Console** > **App** > **Webanwendung (Model-View-Controller)**  > **Weiter** (Web und Konsole).</span><span class="sxs-lookup"><span data-stu-id="b2577-228">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

* <span data-ttu-id="b2577-229">Gehen Sie im Dialogfeld **Neue Webanwendung konfigurieren** folgendermaßen vor:</span><span class="sxs-lookup"><span data-stu-id="b2577-229">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="b2577-230">Vergewissern Sie sich, dass **Authentifizierung** auf **Keine Authentifizierung** festgelegt ist.</span><span class="sxs-lookup"><span data-stu-id="b2577-230">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="b2577-231">Wenn eine Option zum Auswählen eines **Zielframeworks** angezeigt wird, wählen Sie die neueste 2.x-Version aus.</span><span class="sxs-lookup"><span data-stu-id="b2577-231">If presented an option to select a **Target Framework** , select the latest 2.x version.</span></span>

  <span data-ttu-id="b2577-232">Klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="b2577-232">Select **Next**.</span></span>

* <span data-ttu-id="b2577-233">Nennen Sie das Projekt **MvcMovie** , und wählen Sie dann **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="b2577-233">Name the project **MvcMovie** , and then select **Create**.</span></span>

---

### <a name="run-the-app"></a><span data-ttu-id="b2577-234">Ausführen der App</span><span class="sxs-lookup"><span data-stu-id="b2577-234">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b2577-235">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b2577-235">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b2577-236">Drücken Sie **STRG+F5** , um die App im Nicht-Debugmodus auszuführen.</span><span class="sxs-lookup"><span data-stu-id="b2577-236">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="b2577-237">Visual Studio startet [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) und führt die App aus.</span><span class="sxs-lookup"><span data-stu-id="b2577-237">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="b2577-238">Beachten Sie, dass die Adressleiste `localhost:port#` und nicht etwas wie `example.com` anzeigt.</span><span class="sxs-lookup"><span data-stu-id="b2577-238">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="b2577-239">Das liegt daran, dass es sich bei `localhost` um den Standard-Hostnamen für Ihren lokalen Computer handelt.</span><span class="sxs-lookup"><span data-stu-id="b2577-239">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="b2577-240">Wenn in Visual Studio ein Webprojekt erstellt wird, wird für den Webserver ein zufälliger Port verwendet.</span><span class="sxs-lookup"><span data-stu-id="b2577-240">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="b2577-241">Das Starten der App mit STRG+F5 (Nicht-Debugmodus) ermöglicht die Änderung des Codes, das Speichern der Datei, das Aktualisieren des Browsers und das Anzeigen von Codeänderungen.</span><span class="sxs-lookup"><span data-stu-id="b2577-241">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="b2577-242">Viele Entwickler bevorzugen den Nicht-Debugmodus, um die App schnell zu starten und Änderungen anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="b2577-242">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="b2577-243">Sie können die App über das Menüelement **Debuggen** im Debugmodus oder Nicht-Debugmodus starten:</span><span class="sxs-lookup"><span data-stu-id="b2577-243">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![Menü „Debuggen“](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="b2577-245">Sie können die App debuggen, indem Sie die Schaltfläche **IIS Express** auswählen.</span><span class="sxs-lookup"><span data-stu-id="b2577-245">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

* <span data-ttu-id="b2577-247">Wählen Sie **Akzeptieren** aus, um der Nachverfolgung zuzustimmen.</span><span class="sxs-lookup"><span data-stu-id="b2577-247">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="b2577-248">Diese App verfolgt keine personenbezogenen Informationen nach.</span><span class="sxs-lookup"><span data-stu-id="b2577-248">This app doesn't track personal information.</span></span> <span data-ttu-id="b2577-249">Der generierte Vorlagencode enthält Objekte, die bei der Erfüllung der [Datenschutz-Grundverordnung (DSGVO)](xref:security/gdpr) als Unterstützung dienen sollen.</span><span class="sxs-lookup"><span data-stu-id="b2577-249">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Start- oder Indexseite](start-mvc/_static/privacy.png)

  <span data-ttu-id="b2577-251">Die folgende Abbildung zeigt die App, nachdem die Nachverfolgung akzeptiert wurde:</span><span class="sxs-lookup"><span data-stu-id="b2577-251">The following image shows the app after accepting tracking:</span></span>

  ![Start- oder Indexseite](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="b2577-253">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b2577-253">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="b2577-254">Drücken Sie STRG+F5, um die Ausführung ohne den Debugger zu starten.</span><span class="sxs-lookup"><span data-stu-id="b2577-254">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="b2577-255">Visual Studio Code startet [Kestrel](xref:fundamentals/servers/kestrel) und einen Browser und navigiert zu `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="b2577-255">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="b2577-256">Die Adressleiste zeigt `localhost:port:5001` an, nicht `example.com`.</span><span class="sxs-lookup"><span data-stu-id="b2577-256">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="b2577-257">Das liegt daran, dass es sich bei `localhost` um den Standardhostnamen für den lokalen Computer handelt.</span><span class="sxs-lookup"><span data-stu-id="b2577-257">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="b2577-258">„Localhost“ dient nur Webanforderungen vom lokalen Computer.</span><span class="sxs-lookup"><span data-stu-id="b2577-258">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="b2577-259">Das Starten der App mit STRG+F5 (Nicht-Debugmodus) ermöglicht die Änderung des Codes, das Speichern der Datei, das Aktualisieren des Browsers und das Anzeigen von Codeänderungen.</span><span class="sxs-lookup"><span data-stu-id="b2577-259">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="b2577-260">Viele Entwickler bevorzugen den Nicht-Debugmodus, um die Seite zu aktualisieren und Änderungen anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="b2577-260">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

* <span data-ttu-id="b2577-261">Wählen Sie **Akzeptieren** aus, um der Nachverfolgung zuzustimmen.</span><span class="sxs-lookup"><span data-stu-id="b2577-261">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="b2577-262">Diese App verfolgt keine personenbezogenen Informationen nach.</span><span class="sxs-lookup"><span data-stu-id="b2577-262">This app doesn't track personal information.</span></span> <span data-ttu-id="b2577-263">Der generierte Vorlagencode enthält Objekte, die bei der Erfüllung der [Datenschutz-Grundverordnung (DSGVO)](xref:security/gdpr) als Unterstützung dienen sollen.</span><span class="sxs-lookup"><span data-stu-id="b2577-263">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Start- oder Indexseite](start-mvc/_static/privacy.png)

  <span data-ttu-id="b2577-265">Die folgende Abbildung zeigt die App, nachdem die Nachverfolgung akzeptiert wurde:</span><span class="sxs-lookup"><span data-stu-id="b2577-265">The following image shows the app after accepting tracking:</span></span>

  ![Start- oder Indexseite](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="b2577-267">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="b2577-267">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="b2577-268">Wählen Sie **Ausführen** > **Ohne Debuggen starten** aus, um die App zu starten.</span><span class="sxs-lookup"><span data-stu-id="b2577-268">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="b2577-269">Visual Studio für Mac startet den [Kestrel](xref:fundamentals/servers/index#kestrel)-Server und einen Browser und navigiert zu `http://localhost:port`, wobei es sich bei *port* um eine zufällig ausgewählte Portnummer handelt.</span><span class="sxs-lookup"><span data-stu-id="b2577-269">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="b2577-270">Die Adressleiste zeigt `localhost:port#` an, nicht `example.com`.</span><span class="sxs-lookup"><span data-stu-id="b2577-270">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="b2577-271">Das liegt daran, dass es sich bei `localhost` um den Standard-Hostnamen für Ihren lokalen Computer handelt.</span><span class="sxs-lookup"><span data-stu-id="b2577-271">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="b2577-272">Wenn in Visual Studio ein Webprojekt erstellt wird, wird für den Webserver ein zufälliger Port verwendet.</span><span class="sxs-lookup"><span data-stu-id="b2577-272">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="b2577-273">Wenn Sie die App ausführen, wird eine andere Portnummer angezeigt.</span><span class="sxs-lookup"><span data-stu-id="b2577-273">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="b2577-274">Sie können die App über das Menü **Ausführen** im Debugmodus oder Nicht-Debugmodus starten.</span><span class="sxs-lookup"><span data-stu-id="b2577-274">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

* <span data-ttu-id="b2577-275">Wählen Sie **Akzeptieren** aus, um der Nachverfolgung zuzustimmen.</span><span class="sxs-lookup"><span data-stu-id="b2577-275">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="b2577-276">Diese App verfolgt keine personenbezogenen Informationen nach.</span><span class="sxs-lookup"><span data-stu-id="b2577-276">This app doesn't track personal information.</span></span> <span data-ttu-id="b2577-277">Der generierte Vorlagencode enthält Objekte, die bei der Erfüllung der [Datenschutz-Grundverordnung (DSGVO)](xref:security/gdpr) als Unterstützung dienen sollen.</span><span class="sxs-lookup"><span data-stu-id="b2577-277">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Start- oder Indexseite](./start-mvc/_static/output_privacy_macos.png)

  <span data-ttu-id="b2577-279">Die folgende Abbildung zeigt die App, nachdem die Nachverfolgung akzeptiert wurde:</span><span class="sxs-lookup"><span data-stu-id="b2577-279">The following image shows the app after accepting tracking:</span></span>

  ![Start- oder Indexseite](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="b2577-281">Im nächsten Teil dieses Tutorials erfahren Sie mehr über MVC und beginnen mit dem Schreiben von Code.</span><span class="sxs-lookup"><span data-stu-id="b2577-281">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="b2577-282">Nächste</span><span class="sxs-lookup"><span data-stu-id="b2577-282">Next</span></span>](adding-controller.md)

::: moniker-end
