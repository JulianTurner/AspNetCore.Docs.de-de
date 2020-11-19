---
title: Erste Schritte mit ASP.NET Core MVC
author: rick-anderson
description: Hier finden Sie Informationen zum Einstieg in ASP.NET Core MVC.
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
uid: tutorials/first-mvc-app/start-mvc
ms.openlocfilehash: c96e7107c85bf36f55f6571c71c20d09bc94ddb3
ms.sourcegitcommit: fb208f907249cc7aab029afff941a0266c187050
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94688498"
---
# <a name="get-started-with-aspnet-core-mvc"></a><span data-ttu-id="4dc81-103">Erste Schritte mit ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="4dc81-103">Get started with ASP.NET Core MVC</span></span>

<span data-ttu-id="4dc81-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="4dc81-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="4dc81-105">In diesem Tutorial lernen Sie Grundlegendes zur Erstellung einer ASP.NET Core-MVC-Web-App.</span><span class="sxs-lookup"><span data-stu-id="4dc81-105">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="4dc81-106">Die App verwaltet eine Datenbank mit Filmtiteln.</span><span class="sxs-lookup"><span data-stu-id="4dc81-106">The app manages a database of movie titles.</span></span> <span data-ttu-id="4dc81-107">Sie lernen Folgendes:</span><span class="sxs-lookup"><span data-stu-id="4dc81-107">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="4dc81-108">Erstellen einer Web-App</span><span class="sxs-lookup"><span data-stu-id="4dc81-108">Create a web app.</span></span>
> * <span data-ttu-id="4dc81-109">Hinzufügen eines Modells und Erstellen eines Gerüsts für das Modell</span><span class="sxs-lookup"><span data-stu-id="4dc81-109">Add and scaffold a model.</span></span>
> * <span data-ttu-id="4dc81-110">Arbeiten mit einer Datenbank</span><span class="sxs-lookup"><span data-stu-id="4dc81-110">Work with a database.</span></span>
> * <span data-ttu-id="4dc81-111">Hinzufügen von Such- und Überprüfungsfunktionen</span><span class="sxs-lookup"><span data-stu-id="4dc81-111">Add search and validation.</span></span>

<span data-ttu-id="4dc81-112">Am Ende verfügen Sie über eine App, die Filmdaten verwalten und anzeigen kann.</span><span class="sxs-lookup"><span data-stu-id="4dc81-112">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="4dc81-113">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="4dc81-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4dc81-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4dc81-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="4dc81-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4dc81-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="4dc81-116">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="4dc81-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-5.0.md)]

---

## <a name="create-a-web-app"></a><span data-ttu-id="4dc81-117">Erstellen einer Web-App</span><span class="sxs-lookup"><span data-stu-id="4dc81-117">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4dc81-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4dc81-118">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="4dc81-119">Starten Sie Visual Studio, und wählen Sie **Neues Projekt erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="4dc81-119">Start Visual Studio and select **Create a new project**.</span></span>
1. <span data-ttu-id="4dc81-120">Wählen Sie im Dialogfeld **Neues Projekt** die Option **ASP.NET Core-Webanwendung** > **Weiter** aus.</span><span class="sxs-lookup"><span data-stu-id="4dc81-120">In the **Create a new project** dialog, select **ASP.NET Core Web Application** > **Next**.</span></span>
1. <span data-ttu-id="4dc81-121">Geben Sie `MvcMovie` im Dialogfeld **Neues Projekt konfigurieren** für **Projektname** ein.</span><span class="sxs-lookup"><span data-stu-id="4dc81-121">In the **Configure your new project** dialog, enter `MvcMovie` for **Project name**.</span></span> <span data-ttu-id="4dc81-122">Es ist wichtig, genau diesen Namen unter Berücksichtigung der Groß-/Kleinschreibung zu verwenden, sodass beim Kopieren von Code jeder `namespace` übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="4dc81-122">It's important to use this exact name including capitalization, so each `namespace` matches when code is copied.</span></span>
1. <span data-ttu-id="4dc81-123">Wählen Sie **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="4dc81-123">Select **Create**.</span></span>
1. <span data-ttu-id="4dc81-124">Wählen Sie im Dialogfeld **Neue ASP.NET Core-Webanwendung erstellen** Folgendes aus:</span><span class="sxs-lookup"><span data-stu-id="4dc81-124">In the **Create a new ASP.NET Core web application** dialog, select:</span></span>
    1. <span data-ttu-id="4dc81-125">In den Dropdownmenüs **.NET Core** und **ASP.NET Core 5.0**.</span><span class="sxs-lookup"><span data-stu-id="4dc81-125">**.NET Core** and **ASP.NET Core 5.0** in the dropdowns.</span></span>
    1. <span data-ttu-id="4dc81-126">**ASP.NET Core-Web-App (Model View Controller)** .</span><span class="sxs-lookup"><span data-stu-id="4dc81-126">**ASP.NET Core Web App (Model-View-Controller)**.</span></span>
    1. <span data-ttu-id="4dc81-127">**Erstellen**</span><span class="sxs-lookup"><span data-stu-id="4dc81-127">**Create**</span></span>

![<span data-ttu-id="4dc81-128">Erstellen einer neuen ASP.NET Core-Webanwendung</span><span class="sxs-lookup"><span data-stu-id="4dc81-128">Create a new ASP.NET Core web application</span></span> ](start-mvc/_static/mvcVS19v16.9.png)

<span data-ttu-id="4dc81-129">Alternative Ansätze zum Erstellen des Projekts finden Sie unter [Erstellen eines neuen Projekts in Visual Studio](/visualstudio/ide/create-new-project).</span><span class="sxs-lookup"><span data-stu-id="4dc81-129">For alternative approaches to create the project, see [Create a new project in Visual Studio](/visualstudio/ide/create-new-project).</span></span>

<span data-ttu-id="4dc81-130">Visual Studio verwendet die Standardvorlage für das MVC-Projekt, das Sie gerade erstellt haben.</span><span class="sxs-lookup"><span data-stu-id="4dc81-130">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="4dc81-131">Wenn Sie einen Projektnamen eingeben und einige Optionen festlegen, funktioniert Ihre App bereits.</span><span class="sxs-lookup"><span data-stu-id="4dc81-131">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="4dc81-132">Hierbei handelt es sich um ein grundlegendes Startprojekt.</span><span class="sxs-lookup"><span data-stu-id="4dc81-132">This is a basic starter project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="4dc81-133">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4dc81-133">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="4dc81-134">Das Tutorial setzt voraus, dass Sie mit Visual Studio Code vertraut sind.</span><span class="sxs-lookup"><span data-stu-id="4dc81-134">The tutorial assumes familiarity with VS Code.</span></span> <span data-ttu-id="4dc81-135">Weitere Informationen finden Sie unter [Erste Schritte mit VS Code](https://code.visualstudio.com/docs) und [Hilfe zu Visual Studio Code](#visual-studio-code-help).</span><span class="sxs-lookup"><span data-stu-id="4dc81-135">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="4dc81-136">Öffnen Sie das [integrierte Terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="4dc81-136">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="4dc81-137">Wechseln Sie mit `cd` zu einem Ordner, der das Projekt enthalten soll.</span><span class="sxs-lookup"><span data-stu-id="4dc81-137">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="4dc81-138">Führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="4dc81-138">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="4dc81-139">Es wird ein Dialogfeld mit folgender Meldung angezeigt: **Die erforderlichen Objekte zum Erstellen und Debuggen sind in "MvcMovie" nicht vorhanden. Sollen sie hinzugefügt werden?**</span><span class="sxs-lookup"><span data-stu-id="4dc81-139">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="4dc81-140">Wählen Sie **Ja** aus.</span><span class="sxs-lookup"><span data-stu-id="4dc81-140">Select **Yes**</span></span>

  * <span data-ttu-id="4dc81-141">`dotnet new mvc -o MvcMovie`: Erstellt ein neues ASP.NET Core MVC-Projekt im Ordner *MvcMovie*.</span><span class="sxs-lookup"><span data-stu-id="4dc81-141">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="4dc81-142">`code -r MvcMovie`: Lädt die Projektdatei *MvcMovie.csproj* in Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="4dc81-142">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="4dc81-143">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="4dc81-143">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="4dc81-144">Klicken Sie auf **Datei** > **Neue Projektmappe**.</span><span class="sxs-lookup"><span data-stu-id="4dc81-144">Select **File** > **New Solution**.</span></span>

  ![Neue Projektmappe in macOS](start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="4dc81-146">Navigieren Sie in Visual Studio für Mac-Versionen vor Version 8.6 zu **.NET Core** > **App** > **Webanwendung (Model-View-Controller)**  > **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="4dc81-146">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span> <span data-ttu-id="4dc81-147">Bei Version 8.6 oder höher klicken Sie auf **Web and Console** > **App** > **Webanwendung (Model-View-Controller)**  > **Weiter** (Web und Konsole).</span><span class="sxs-lookup"><span data-stu-id="4dc81-147">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

  ![Auswählen der macOS-Web-App-Vorlage](start-mvc/_static/web_app_template_vsmac.png)

* <span data-ttu-id="4dc81-149">Gehen Sie im Dialogfeld **Neue Webanwendung konfigurieren** folgendermaßen vor:</span><span class="sxs-lookup"><span data-stu-id="4dc81-149">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="4dc81-150">Vergewissern Sie sich, dass **Authentifizierung** auf **Keine Authentifizierung** festgelegt ist.</span><span class="sxs-lookup"><span data-stu-id="4dc81-150">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="4dc81-151">Wenn eine Option zum Auswählen eines **Zielframeworks** angezeigt wird, wählen Sie die neueste 5.x-Version aus.</span><span class="sxs-lookup"><span data-stu-id="4dc81-151">If presented an option to select a **Target Framework**, select the latest 5.x version.</span></span>

  <span data-ttu-id="4dc81-152">Klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="4dc81-152">Select **Next**.</span></span>

* <span data-ttu-id="4dc81-153">Nennen Sie das Projekt **MvcMovie**, und wählen Sie dann **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="4dc81-153">Name the project **MvcMovie**, and then select **Create**.</span></span>

  ![Benennen des macOS-Projekts](start-mvc/_static/MvcMovie.png)

---

### <a name="run-the-app"></a><span data-ttu-id="4dc81-155">Ausführen der App</span><span class="sxs-lookup"><span data-stu-id="4dc81-155">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4dc81-156">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4dc81-156">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="4dc81-157">Drücken Sie **STRG+F5**, um die App im Nicht-Debugmodus auszuführen.</span><span class="sxs-lookup"><span data-stu-id="4dc81-157">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="4dc81-158">Visual Studio startet [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) und führt die App aus.</span><span class="sxs-lookup"><span data-stu-id="4dc81-158">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="4dc81-159">Beachten Sie, dass die Adressleiste `localhost:port#` und nicht etwas wie `example.com` anzeigt.</span><span class="sxs-lookup"><span data-stu-id="4dc81-159">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="4dc81-160">Das liegt daran, dass es sich bei `localhost` um den Standard-Hostnamen für Ihren lokalen Computer handelt.</span><span class="sxs-lookup"><span data-stu-id="4dc81-160">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="4dc81-161">Wenn in Visual Studio ein Webprojekt erstellt wird, wird für den Webserver ein zufälliger Port verwendet.</span><span class="sxs-lookup"><span data-stu-id="4dc81-161">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="4dc81-162">Das Starten der App mit STRG+F5 (Nicht-Debugmodus) ermöglicht die Änderung des Codes, das Speichern der Datei, das Aktualisieren des Browsers und das Anzeigen von Codeänderungen.</span><span class="sxs-lookup"><span data-stu-id="4dc81-162">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="4dc81-163">Viele Entwickler bevorzugen den Nicht-Debugmodus, um die App schnell zu starten und Änderungen anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="4dc81-163">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="4dc81-164">Sie können die App über das Menüelement **Debuggen** im Debugmodus oder Nicht-Debugmodus starten:</span><span class="sxs-lookup"><span data-stu-id="4dc81-164">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![Menü „Debuggen“](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="4dc81-166">Sie können die App debuggen, indem Sie die Schaltfläche **IIS Express** auswählen.</span><span class="sxs-lookup"><span data-stu-id="4dc81-166">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

  <span data-ttu-id="4dc81-168">Auf der folgenden Abbildung sehen Sie die App:</span><span class="sxs-lookup"><span data-stu-id="4dc81-168">The following image shows the app:</span></span>

  ![Start- oder Indexseite](start-mvc/_static/home50-vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="4dc81-170">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4dc81-170">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="4dc81-171">Drücken Sie STRG+F5, um die Ausführung ohne den Debugger zu starten.</span><span class="sxs-lookup"><span data-stu-id="4dc81-171">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="4dc81-172">Visual Studio Code startet [Kestrel](xref:fundamentals/servers/kestrel) und einen Browser und navigiert zu `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="4dc81-172">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="4dc81-173">Die Adressleiste zeigt `localhost:port:5001` an, nicht `example.com`.</span><span class="sxs-lookup"><span data-stu-id="4dc81-173">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="4dc81-174">Das liegt daran, dass es sich bei `localhost` um den Standardhostnamen für den lokalen Computer handelt.</span><span class="sxs-lookup"><span data-stu-id="4dc81-174">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="4dc81-175">„Localhost“ dient nur Webanforderungen vom lokalen Computer.</span><span class="sxs-lookup"><span data-stu-id="4dc81-175">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="4dc81-176">Das Starten der App mit STRG+F5 (Nicht-Debugmodus) ermöglicht die Änderung des Codes, das Speichern der Datei, das Aktualisieren des Browsers und das Anzeigen von Codeänderungen.</span><span class="sxs-lookup"><span data-stu-id="4dc81-176">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="4dc81-177">Viele Entwickler bevorzugen den Nicht-Debugmodus, um die Seite zu aktualisieren und Änderungen anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="4dc81-177">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

  ![Start- oder Indexseite](start-mvc/_static/home50-port5001.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="4dc81-179">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="4dc81-179">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="4dc81-180">Wählen Sie **Ausführen** > **Ohne Debuggen starten** aus, um die App zu starten.</span><span class="sxs-lookup"><span data-stu-id="4dc81-180">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="4dc81-181">Visual Studio für Mac startet den [Kestrel](xref:fundamentals/servers/index#kestrel)-Server und einen Browser und navigiert zu `http://localhost:port`, wobei es sich bei *port* um eine zufällig ausgewählte Portnummer handelt.</span><span class="sxs-lookup"><span data-stu-id="4dc81-181">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="4dc81-182">Die Adressleiste zeigt `localhost:port#` an, nicht `example.com`.</span><span class="sxs-lookup"><span data-stu-id="4dc81-182">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="4dc81-183">Das liegt daran, dass es sich bei `localhost` um den Standard-Hostnamen für Ihren lokalen Computer handelt.</span><span class="sxs-lookup"><span data-stu-id="4dc81-183">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="4dc81-184">Wenn in Visual Studio ein Webprojekt erstellt wird, wird für den Webserver ein zufälliger Port verwendet.</span><span class="sxs-lookup"><span data-stu-id="4dc81-184">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="4dc81-185">Wenn Sie die App ausführen, wird eine andere Portnummer angezeigt.</span><span class="sxs-lookup"><span data-stu-id="4dc81-185">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="4dc81-186">Sie können die App über das Menü **Ausführen** im Debugmodus oder Nicht-Debugmodus starten.</span><span class="sxs-lookup"><span data-stu-id="4dc81-186">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

  <span data-ttu-id="4dc81-187">Auf der folgenden Abbildung sehen Sie die App:</span><span class="sxs-lookup"><span data-stu-id="4dc81-187">The following image shows the app:</span></span>

  ![Start- oder Indexseite](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="4dc81-189">Im nächsten Teil dieses Tutorials erfahren Sie mehr über MVC und beginnen mit dem Schreiben von Code.</span><span class="sxs-lookup"><span data-stu-id="4dc81-189">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="4dc81-190">Nächste</span><span class="sxs-lookup"><span data-stu-id="4dc81-190">Next</span></span>](adding-controller.md)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="4dc81-191">In diesem Tutorial lernen Sie Grundlegendes zur Erstellung einer ASP.NET Core-MVC-Web-App.</span><span class="sxs-lookup"><span data-stu-id="4dc81-191">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="4dc81-192">Die App verwaltet eine Datenbank mit Filmtiteln.</span><span class="sxs-lookup"><span data-stu-id="4dc81-192">The app manages a database of movie titles.</span></span> <span data-ttu-id="4dc81-193">Sie lernen Folgendes:</span><span class="sxs-lookup"><span data-stu-id="4dc81-193">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="4dc81-194">Erstellen einer Web-App</span><span class="sxs-lookup"><span data-stu-id="4dc81-194">Create a web app.</span></span>
> * <span data-ttu-id="4dc81-195">Hinzufügen eines Modells und Erstellen eines Gerüsts für das Modell</span><span class="sxs-lookup"><span data-stu-id="4dc81-195">Add and scaffold a model.</span></span>
> * <span data-ttu-id="4dc81-196">Arbeiten mit einer Datenbank</span><span class="sxs-lookup"><span data-stu-id="4dc81-196">Work with a database.</span></span>
> * <span data-ttu-id="4dc81-197">Hinzufügen von Such- und Überprüfungsfunktionen</span><span class="sxs-lookup"><span data-stu-id="4dc81-197">Add search and validation.</span></span>

<span data-ttu-id="4dc81-198">Am Ende verfügen Sie über eine App, die Filmdaten verwalten und anzeigen kann.</span><span class="sxs-lookup"><span data-stu-id="4dc81-198">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="4dc81-199">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="4dc81-199">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4dc81-200">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4dc81-200">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="4dc81-201">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4dc81-201">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="4dc81-202">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="4dc81-202">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-app"></a><span data-ttu-id="4dc81-203">Erstellen einer Web-App</span><span class="sxs-lookup"><span data-stu-id="4dc81-203">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4dc81-204">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4dc81-204">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4dc81-205">Wählen Sie in Visual Studio die Option **Neues Projekt erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="4dc81-205">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="4dc81-206">Wählen Sie **ASP.NET Core-Webanwendung** > **Weiter** aus.</span><span class="sxs-lookup"><span data-stu-id="4dc81-206">Select **ASP.NET Core Web Application** > **Next**.</span></span>

![neue ASP.NET Core-Webanwendung](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="4dc81-208">Geben Sie dem Projekt den Namen **MvcMovie**, und klicken Sie dann auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="4dc81-208">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="4dc81-209">Es ist wichtig, dem Projekt den Namen **MvcMovie** zu geben, damit beim Kopieren von Code der Namespace übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="4dc81-209">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![neue ASP.NET Core-Webanwendung](start-mvc/_static/config.png)

* <span data-ttu-id="4dc81-211">Wählen Sie **Webanwendung (Model View Controller)** aus.</span><span class="sxs-lookup"><span data-stu-id="4dc81-211">Select **Web Application(Model-View-Controller)**.</span></span> <span data-ttu-id="4dc81-212">Wählen Sie in den Dropdownfeldern **.NET Core** und **ASP.NET Core 3.1** und anschließend **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="4dc81-212">From the dropdown boxes, select **.NET Core** and **ASP.NET Core 3.1**, then select **Create**.</span></span>

![<span data-ttu-id="4dc81-213">Dialogfeld „Neues Projekt“, .NET Core im linken Bereich, ASP.NET Core-Web-App</span><span class="sxs-lookup"><span data-stu-id="4dc81-213">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project30.png)

<span data-ttu-id="4dc81-214">Visual Studio verwendet die Standardvorlage für das MVC-Projekt, das Sie gerade erstellt haben.</span><span class="sxs-lookup"><span data-stu-id="4dc81-214">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="4dc81-215">Wenn Sie einen Projektnamen eingeben und einige Optionen festlegen, funktioniert Ihre App bereits.</span><span class="sxs-lookup"><span data-stu-id="4dc81-215">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="4dc81-216">Hierbei handelt es sich um ein grundlegendes Startprojekt.</span><span class="sxs-lookup"><span data-stu-id="4dc81-216">This is a basic starter project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="4dc81-217">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4dc81-217">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="4dc81-218">Das Tutorial setzt voraus, dass Sie mit VS Code vertraut sind.</span><span class="sxs-lookup"><span data-stu-id="4dc81-218">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="4dc81-219">Weitere Informationen finden Sie unter [Erste Schritte mit VS Code](https://code.visualstudio.com/docs) und [Hilfe zu Visual Studio Code](#visual-studio-code-help).</span><span class="sxs-lookup"><span data-stu-id="4dc81-219">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="4dc81-220">Öffnen Sie das [integrierte Terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="4dc81-220">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="4dc81-221">Wechseln Sie mit `cd` zu einem Ordner, der das Projekt enthalten soll.</span><span class="sxs-lookup"><span data-stu-id="4dc81-221">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="4dc81-222">Führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="4dc81-222">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="4dc81-223">Es wird ein Dialogfeld mit folgender Meldung angezeigt: **Die erforderlichen Objekte zum Erstellen und Debuggen sind in "MvcMovie" nicht vorhanden. Sollen sie hinzugefügt werden?**</span><span class="sxs-lookup"><span data-stu-id="4dc81-223">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="4dc81-224">Wählen Sie **Ja** aus.</span><span class="sxs-lookup"><span data-stu-id="4dc81-224">Select **Yes**</span></span>

  * <span data-ttu-id="4dc81-225">`dotnet new mvc -o MvcMovie`: Erstellt ein neues ASP.NET Core MVC-Projekt im Ordner *MvcMovie*.</span><span class="sxs-lookup"><span data-stu-id="4dc81-225">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="4dc81-226">`code -r MvcMovie`: Lädt die Projektdatei *MvcMovie.csproj* in Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="4dc81-226">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="4dc81-227">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="4dc81-227">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="4dc81-228">Klicken Sie auf **Datei** > **Neue Projektmappe**.</span><span class="sxs-lookup"><span data-stu-id="4dc81-228">Select **File** > **New Solution**.</span></span>

  ![Neue Projektmappe in macOS](start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="4dc81-230">Navigieren Sie in Visual Studio für Mac-Versionen vor Version 8.6 zu **.NET Core** > **App** > **Webanwendung (Model-View-Controller)**  > **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="4dc81-230">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span> <span data-ttu-id="4dc81-231">Bei Version 8.6 oder höher klicken Sie auf **Web and Console** > **App** > **Webanwendung (Model-View-Controller)**  > **Weiter** (Web und Konsole).</span><span class="sxs-lookup"><span data-stu-id="4dc81-231">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

  ![Auswählen der macOS-Web-App-Vorlage](start-mvc/_static/web_app_template_vsmac.png)

* <span data-ttu-id="4dc81-233">Gehen Sie im Dialogfeld **Neue Webanwendung konfigurieren** folgendermaßen vor:</span><span class="sxs-lookup"><span data-stu-id="4dc81-233">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="4dc81-234">Vergewissern Sie sich, dass **Authentifizierung** auf **Keine Authentifizierung** festgelegt ist.</span><span class="sxs-lookup"><span data-stu-id="4dc81-234">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="4dc81-235">Wenn eine Option zum Auswählen eines **Zielframeworks** angezeigt wird, wählen Sie die neueste 3.x-Version aus.</span><span class="sxs-lookup"><span data-stu-id="4dc81-235">If presented an option to select a **Target Framework**, select the latest 3.x version.</span></span>

  <span data-ttu-id="4dc81-236">Klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="4dc81-236">Select **Next**.</span></span>

* <span data-ttu-id="4dc81-237">Nennen Sie das Projekt **MvcMovie**, und wählen Sie dann **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="4dc81-237">Name the project **MvcMovie**, and then select **Create**.</span></span>

  ![Benennen des macOS-Projekts](start-mvc/_static/MvcMovie.png)

---

### <a name="run-the-app"></a><span data-ttu-id="4dc81-239">Ausführen der App</span><span class="sxs-lookup"><span data-stu-id="4dc81-239">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4dc81-240">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4dc81-240">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="4dc81-241">Drücken Sie **STRG+F5**, um die App im Nicht-Debugmodus auszuführen.</span><span class="sxs-lookup"><span data-stu-id="4dc81-241">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="4dc81-242">Visual Studio startet [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) und führt die App aus.</span><span class="sxs-lookup"><span data-stu-id="4dc81-242">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="4dc81-243">Beachten Sie, dass die Adressleiste `localhost:port#` und nicht etwas wie `example.com` anzeigt.</span><span class="sxs-lookup"><span data-stu-id="4dc81-243">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="4dc81-244">Das liegt daran, dass es sich bei `localhost` um den Standard-Hostnamen für Ihren lokalen Computer handelt.</span><span class="sxs-lookup"><span data-stu-id="4dc81-244">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="4dc81-245">Wenn in Visual Studio ein Webprojekt erstellt wird, wird für den Webserver ein zufälliger Port verwendet.</span><span class="sxs-lookup"><span data-stu-id="4dc81-245">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="4dc81-246">Das Starten der App mit STRG+F5 (Nicht-Debugmodus) ermöglicht die Änderung des Codes, das Speichern der Datei, das Aktualisieren des Browsers und das Anzeigen von Codeänderungen.</span><span class="sxs-lookup"><span data-stu-id="4dc81-246">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="4dc81-247">Viele Entwickler bevorzugen den Nicht-Debugmodus, um die App schnell zu starten und Änderungen anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="4dc81-247">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="4dc81-248">Sie können die App über das Menüelement **Debuggen** im Debugmodus oder Nicht-Debugmodus starten:</span><span class="sxs-lookup"><span data-stu-id="4dc81-248">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![Menü „Debuggen“](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="4dc81-250">Sie können die App debuggen, indem Sie die Schaltfläche **IIS Express** auswählen.</span><span class="sxs-lookup"><span data-stu-id="4dc81-250">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

  <span data-ttu-id="4dc81-252">Auf der folgenden Abbildung sehen Sie die App:</span><span class="sxs-lookup"><span data-stu-id="4dc81-252">The following image shows the app:</span></span>

  ![Start- oder Indexseite](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="4dc81-254">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4dc81-254">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="4dc81-255">Drücken Sie STRG+F5, um die Ausführung ohne den Debugger zu starten.</span><span class="sxs-lookup"><span data-stu-id="4dc81-255">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="4dc81-256">Visual Studio Code startet [Kestrel](xref:fundamentals/servers/kestrel) und einen Browser und navigiert zu `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="4dc81-256">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="4dc81-257">Die Adressleiste zeigt `localhost:port:5001` an, nicht `example.com`.</span><span class="sxs-lookup"><span data-stu-id="4dc81-257">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="4dc81-258">Das liegt daran, dass es sich bei `localhost` um den Standardhostnamen für den lokalen Computer handelt.</span><span class="sxs-lookup"><span data-stu-id="4dc81-258">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="4dc81-259">„Localhost“ dient nur Webanforderungen vom lokalen Computer.</span><span class="sxs-lookup"><span data-stu-id="4dc81-259">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="4dc81-260">Das Starten der App mit STRG+F5 (Nicht-Debugmodus) ermöglicht die Änderung des Codes, das Speichern der Datei, das Aktualisieren des Browsers und das Anzeigen von Codeänderungen.</span><span class="sxs-lookup"><span data-stu-id="4dc81-260">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="4dc81-261">Viele Entwickler bevorzugen den Nicht-Debugmodus, um die Seite zu aktualisieren und Änderungen anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="4dc81-261">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

  ![Start- oder Indexseite](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="4dc81-263">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="4dc81-263">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="4dc81-264">Wählen Sie **Ausführen** > **Ohne Debuggen starten** aus, um die App zu starten.</span><span class="sxs-lookup"><span data-stu-id="4dc81-264">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="4dc81-265">Visual Studio für Mac startet den [Kestrel](xref:fundamentals/servers/index#kestrel)-Server und einen Browser und navigiert zu `http://localhost:port`, wobei es sich bei *port* um eine zufällig ausgewählte Portnummer handelt.</span><span class="sxs-lookup"><span data-stu-id="4dc81-265">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="4dc81-266">Die Adressleiste zeigt `localhost:port#` an, nicht `example.com`.</span><span class="sxs-lookup"><span data-stu-id="4dc81-266">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="4dc81-267">Das liegt daran, dass es sich bei `localhost` um den Standard-Hostnamen für Ihren lokalen Computer handelt.</span><span class="sxs-lookup"><span data-stu-id="4dc81-267">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="4dc81-268">Wenn in Visual Studio ein Webprojekt erstellt wird, wird für den Webserver ein zufälliger Port verwendet.</span><span class="sxs-lookup"><span data-stu-id="4dc81-268">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="4dc81-269">Wenn Sie die App ausführen, wird eine andere Portnummer angezeigt.</span><span class="sxs-lookup"><span data-stu-id="4dc81-269">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="4dc81-270">Sie können die App über das Menü **Ausführen** im Debugmodus oder Nicht-Debugmodus starten.</span><span class="sxs-lookup"><span data-stu-id="4dc81-270">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

  <span data-ttu-id="4dc81-271">Auf der folgenden Abbildung sehen Sie die App:</span><span class="sxs-lookup"><span data-stu-id="4dc81-271">The following image shows the app:</span></span>

  ![Start- oder Indexseite](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="4dc81-273">Im nächsten Teil dieses Tutorials erfahren Sie mehr über MVC und beginnen mit dem Schreiben von Code.</span><span class="sxs-lookup"><span data-stu-id="4dc81-273">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="4dc81-274">Nächste</span><span class="sxs-lookup"><span data-stu-id="4dc81-274">Next</span></span>](adding-controller.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="4dc81-275">In diesem Tutorial lernen Sie Grundlegendes zur Erstellung einer ASP.NET Core-MVC-Web-App.</span><span class="sxs-lookup"><span data-stu-id="4dc81-275">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="4dc81-276">Die App verwaltet eine Datenbank mit Filmtiteln.</span><span class="sxs-lookup"><span data-stu-id="4dc81-276">The app manages a database of movie titles.</span></span> <span data-ttu-id="4dc81-277">Sie lernen Folgendes:</span><span class="sxs-lookup"><span data-stu-id="4dc81-277">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="4dc81-278">Erstellen einer Web-App</span><span class="sxs-lookup"><span data-stu-id="4dc81-278">Create a web app.</span></span>
> * <span data-ttu-id="4dc81-279">Hinzufügen eines Modells und Erstellen eines Gerüsts für das Modell</span><span class="sxs-lookup"><span data-stu-id="4dc81-279">Add and scaffold a model.</span></span>
> * <span data-ttu-id="4dc81-280">Arbeiten mit einer Datenbank</span><span class="sxs-lookup"><span data-stu-id="4dc81-280">Work with a database.</span></span>
> * <span data-ttu-id="4dc81-281">Hinzufügen von Such- und Überprüfungsfunktionen</span><span class="sxs-lookup"><span data-stu-id="4dc81-281">Add search and validation.</span></span>

<span data-ttu-id="4dc81-282">Am Ende verfügen Sie über eine App, die Filmdaten verwalten und anzeigen kann.</span><span class="sxs-lookup"><span data-stu-id="4dc81-282">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="4dc81-283">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="4dc81-283">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4dc81-284">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4dc81-284">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="4dc81-285">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4dc81-285">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="4dc81-286">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="4dc81-286">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---
## <a name="create-a-web-app"></a><span data-ttu-id="4dc81-287">Erstellen einer Web-App</span><span class="sxs-lookup"><span data-stu-id="4dc81-287">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4dc81-288">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4dc81-288">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4dc81-289">Wählen Sie in Visual Studio die Option **Neues Projekt erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="4dc81-289">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="4dc81-290">Klicken Sie auf **ASP.NET Core-Webanwendung** und dann auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="4dc81-290">Select **ASP.NET Core Web Application** and then select **Next**.</span></span>

![neue ASP.NET Core-Webanwendung](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="4dc81-292">Geben Sie dem Projekt den Namen **MvcMovie**, und klicken Sie dann auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="4dc81-292">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="4dc81-293">Es ist wichtig, dem Projekt den Namen **MvcMovie** zu geben, damit beim Kopieren von Code der Namespace übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="4dc81-293">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![neue ASP.NET Core-Webanwendung](start-mvc/_static/config.png)


* <span data-ttu-id="4dc81-295">Klicken Sie auf **Webanwendung (Model View Controller)** und anschließend auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="4dc81-295">Select **Web Application(Model-View-Controller)**, and then select **Create**.</span></span>

![<span data-ttu-id="4dc81-296">Dialogfeld „Neues Projekt“, .NET Core im linken Bereich, ASP.NET Core-Web-App</span><span class="sxs-lookup"><span data-stu-id="4dc81-296">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project22-21.png)

<span data-ttu-id="4dc81-297">Visual Studio verwendet die Standardvorlage für das MVC-Projekt, das Sie gerade erstellt haben.</span><span class="sxs-lookup"><span data-stu-id="4dc81-297">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="4dc81-298">Wenn Sie einen Projektnamen eingeben und einige Optionen festlegen, funktioniert Ihre App bereits.</span><span class="sxs-lookup"><span data-stu-id="4dc81-298">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="4dc81-299">Dies ist ein grundlegendes Startprojekt und ein guter Einstieg.</span><span class="sxs-lookup"><span data-stu-id="4dc81-299">This is a basic starter project, and it's a good place to start.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="4dc81-300">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4dc81-300">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="4dc81-301">Das Tutorial setzt voraus, dass Sie mit VS Code vertraut sind.</span><span class="sxs-lookup"><span data-stu-id="4dc81-301">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="4dc81-302">Weitere Informationen finden Sie unter [Erste Schritte mit VS Code](https://code.visualstudio.com/docs) und [Hilfe zu Visual Studio Code](#visual-studio-code-help).</span><span class="sxs-lookup"><span data-stu-id="4dc81-302">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="4dc81-303">Öffnen Sie das [integrierte Terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="4dc81-303">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="4dc81-304">Wechseln Sie mit `cd` zu einem Ordner, der das Projekt enthalten soll.</span><span class="sxs-lookup"><span data-stu-id="4dc81-304">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="4dc81-305">Führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="4dc81-305">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="4dc81-306">Es wird ein Dialogfeld mit folgender Meldung angezeigt: **Die erforderlichen Objekte zum Erstellen und Debuggen sind in "MvcMovie" nicht vorhanden. Sollen sie hinzugefügt werden?**</span><span class="sxs-lookup"><span data-stu-id="4dc81-306">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="4dc81-307">Wählen Sie **Ja** aus.</span><span class="sxs-lookup"><span data-stu-id="4dc81-307">Select **Yes**</span></span>

  * <span data-ttu-id="4dc81-308">`dotnet new mvc -o MvcMovie`: Erstellt ein neues ASP.NET Core MVC-Projekt im Ordner *MvcMovie*.</span><span class="sxs-lookup"><span data-stu-id="4dc81-308">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="4dc81-309">`code -r MvcMovie`: Lädt die Projektdatei *MvcMovie.csproj* in Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="4dc81-309">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="4dc81-310">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="4dc81-310">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="4dc81-311">Klicken Sie auf **Datei** > **Neue Projektmappe**.</span><span class="sxs-lookup"><span data-stu-id="4dc81-311">Select **File** > **New Solution**.</span></span>

  ![Neue Projektmappe in macOS](./start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="4dc81-313">Navigieren Sie in Visual Studio für Mac-Versionen vor Version 8.6 zu **.NET Core** > **App** > **Webanwendung (Model-View-Controller)**  > **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="4dc81-313">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span> <span data-ttu-id="4dc81-314">Bei Version 8.6 oder höher klicken Sie auf **Web and Console** > **App** > **Webanwendung (Model-View-Controller)**  > **Weiter** (Web und Konsole).</span><span class="sxs-lookup"><span data-stu-id="4dc81-314">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

* <span data-ttu-id="4dc81-315">Gehen Sie im Dialogfeld **Neue Webanwendung konfigurieren** folgendermaßen vor:</span><span class="sxs-lookup"><span data-stu-id="4dc81-315">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="4dc81-316">Vergewissern Sie sich, dass **Authentifizierung** auf **Keine Authentifizierung** festgelegt ist.</span><span class="sxs-lookup"><span data-stu-id="4dc81-316">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="4dc81-317">Wenn eine Option zum Auswählen eines **Zielframeworks** angezeigt wird, wählen Sie die neueste 2.x-Version aus.</span><span class="sxs-lookup"><span data-stu-id="4dc81-317">If presented an option to select a **Target Framework**, select the latest 2.x version.</span></span>

  <span data-ttu-id="4dc81-318">Klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="4dc81-318">Select **Next**.</span></span>

* <span data-ttu-id="4dc81-319">Nennen Sie das Projekt **MvcMovie**, und wählen Sie dann **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="4dc81-319">Name the project **MvcMovie**, and then select **Create**.</span></span>

---

### <a name="run-the-app"></a><span data-ttu-id="4dc81-320">Ausführen der App</span><span class="sxs-lookup"><span data-stu-id="4dc81-320">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4dc81-321">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4dc81-321">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="4dc81-322">Drücken Sie **STRG+F5**, um die App im Nicht-Debugmodus auszuführen.</span><span class="sxs-lookup"><span data-stu-id="4dc81-322">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="4dc81-323">Visual Studio startet [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) und führt die App aus.</span><span class="sxs-lookup"><span data-stu-id="4dc81-323">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="4dc81-324">Beachten Sie, dass die Adressleiste `localhost:port#` und nicht etwas wie `example.com` anzeigt.</span><span class="sxs-lookup"><span data-stu-id="4dc81-324">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="4dc81-325">Das liegt daran, dass es sich bei `localhost` um den Standard-Hostnamen für Ihren lokalen Computer handelt.</span><span class="sxs-lookup"><span data-stu-id="4dc81-325">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="4dc81-326">Wenn in Visual Studio ein Webprojekt erstellt wird, wird für den Webserver ein zufälliger Port verwendet.</span><span class="sxs-lookup"><span data-stu-id="4dc81-326">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="4dc81-327">Das Starten der App mit STRG+F5 (Nicht-Debugmodus) ermöglicht die Änderung des Codes, das Speichern der Datei, das Aktualisieren des Browsers und das Anzeigen von Codeänderungen.</span><span class="sxs-lookup"><span data-stu-id="4dc81-327">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="4dc81-328">Viele Entwickler bevorzugen den Nicht-Debugmodus, um die App schnell zu starten und Änderungen anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="4dc81-328">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="4dc81-329">Sie können die App über das Menüelement **Debuggen** im Debugmodus oder Nicht-Debugmodus starten:</span><span class="sxs-lookup"><span data-stu-id="4dc81-329">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![Menü „Debuggen“](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="4dc81-331">Sie können die App debuggen, indem Sie die Schaltfläche **IIS Express** auswählen.</span><span class="sxs-lookup"><span data-stu-id="4dc81-331">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

* <span data-ttu-id="4dc81-333">Wählen Sie **Akzeptieren** aus, um der Nachverfolgung zuzustimmen.</span><span class="sxs-lookup"><span data-stu-id="4dc81-333">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="4dc81-334">Diese App verfolgt keine personenbezogenen Informationen nach.</span><span class="sxs-lookup"><span data-stu-id="4dc81-334">This app doesn't track personal information.</span></span> <span data-ttu-id="4dc81-335">Der generierte Vorlagencode enthält Objekte, die bei der Erfüllung der [Datenschutz-Grundverordnung (DSGVO)](xref:security/gdpr) als Unterstützung dienen sollen.</span><span class="sxs-lookup"><span data-stu-id="4dc81-335">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Start- oder Indexseite](start-mvc/_static/privacy.png)

  <span data-ttu-id="4dc81-337">Die folgende Abbildung zeigt die App, nachdem die Nachverfolgung akzeptiert wurde:</span><span class="sxs-lookup"><span data-stu-id="4dc81-337">The following image shows the app after accepting tracking:</span></span>

  ![Start- oder Indexseite](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="4dc81-339">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4dc81-339">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="4dc81-340">Drücken Sie STRG+F5, um die Ausführung ohne den Debugger zu starten.</span><span class="sxs-lookup"><span data-stu-id="4dc81-340">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="4dc81-341">Visual Studio Code startet [Kestrel](xref:fundamentals/servers/kestrel) und einen Browser und navigiert zu `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="4dc81-341">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="4dc81-342">Die Adressleiste zeigt `localhost:port:5001` an, nicht `example.com`.</span><span class="sxs-lookup"><span data-stu-id="4dc81-342">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="4dc81-343">Das liegt daran, dass es sich bei `localhost` um den Standardhostnamen für den lokalen Computer handelt.</span><span class="sxs-lookup"><span data-stu-id="4dc81-343">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="4dc81-344">„Localhost“ dient nur Webanforderungen vom lokalen Computer.</span><span class="sxs-lookup"><span data-stu-id="4dc81-344">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="4dc81-345">Das Starten der App mit STRG+F5 (Nicht-Debugmodus) ermöglicht die Änderung des Codes, das Speichern der Datei, das Aktualisieren des Browsers und das Anzeigen von Codeänderungen.</span><span class="sxs-lookup"><span data-stu-id="4dc81-345">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="4dc81-346">Viele Entwickler bevorzugen den Nicht-Debugmodus, um die Seite zu aktualisieren und Änderungen anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="4dc81-346">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

* <span data-ttu-id="4dc81-347">Wählen Sie **Akzeptieren** aus, um der Nachverfolgung zuzustimmen.</span><span class="sxs-lookup"><span data-stu-id="4dc81-347">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="4dc81-348">Diese App verfolgt keine personenbezogenen Informationen nach.</span><span class="sxs-lookup"><span data-stu-id="4dc81-348">This app doesn't track personal information.</span></span> <span data-ttu-id="4dc81-349">Der generierte Vorlagencode enthält Objekte, die bei der Erfüllung der [Datenschutz-Grundverordnung (DSGVO)](xref:security/gdpr) als Unterstützung dienen sollen.</span><span class="sxs-lookup"><span data-stu-id="4dc81-349">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Start- oder Indexseite](start-mvc/_static/privacy.png)

  <span data-ttu-id="4dc81-351">Die folgende Abbildung zeigt die App, nachdem die Nachverfolgung akzeptiert wurde:</span><span class="sxs-lookup"><span data-stu-id="4dc81-351">The following image shows the app after accepting tracking:</span></span>

  ![Start- oder Indexseite](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="4dc81-353">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="4dc81-353">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="4dc81-354">Wählen Sie **Ausführen** > **Ohne Debuggen starten** aus, um die App zu starten.</span><span class="sxs-lookup"><span data-stu-id="4dc81-354">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="4dc81-355">Visual Studio für Mac startet den [Kestrel](xref:fundamentals/servers/index#kestrel)-Server und einen Browser und navigiert zu `http://localhost:port`, wobei es sich bei *port* um eine zufällig ausgewählte Portnummer handelt.</span><span class="sxs-lookup"><span data-stu-id="4dc81-355">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="4dc81-356">Die Adressleiste zeigt `localhost:port#` an, nicht `example.com`.</span><span class="sxs-lookup"><span data-stu-id="4dc81-356">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="4dc81-357">Das liegt daran, dass es sich bei `localhost` um den Standard-Hostnamen für Ihren lokalen Computer handelt.</span><span class="sxs-lookup"><span data-stu-id="4dc81-357">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="4dc81-358">Wenn in Visual Studio ein Webprojekt erstellt wird, wird für den Webserver ein zufälliger Port verwendet.</span><span class="sxs-lookup"><span data-stu-id="4dc81-358">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="4dc81-359">Wenn Sie die App ausführen, wird eine andere Portnummer angezeigt.</span><span class="sxs-lookup"><span data-stu-id="4dc81-359">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="4dc81-360">Sie können die App über das Menü **Ausführen** im Debugmodus oder Nicht-Debugmodus starten.</span><span class="sxs-lookup"><span data-stu-id="4dc81-360">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

* <span data-ttu-id="4dc81-361">Wählen Sie **Akzeptieren** aus, um der Nachverfolgung zuzustimmen.</span><span class="sxs-lookup"><span data-stu-id="4dc81-361">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="4dc81-362">Diese App verfolgt keine personenbezogenen Informationen nach.</span><span class="sxs-lookup"><span data-stu-id="4dc81-362">This app doesn't track personal information.</span></span> <span data-ttu-id="4dc81-363">Der generierte Vorlagencode enthält Objekte, die bei der Erfüllung der [Datenschutz-Grundverordnung (DSGVO)](xref:security/gdpr) als Unterstützung dienen sollen.</span><span class="sxs-lookup"><span data-stu-id="4dc81-363">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Start- oder Indexseite](./start-mvc/_static/output_privacy_macos.png)

  <span data-ttu-id="4dc81-365">Die folgende Abbildung zeigt die App, nachdem die Nachverfolgung akzeptiert wurde:</span><span class="sxs-lookup"><span data-stu-id="4dc81-365">The following image shows the app after accepting tracking:</span></span>

  ![Start- oder Indexseite](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="4dc81-367">Im nächsten Teil dieses Tutorials erfahren Sie mehr über MVC und beginnen mit dem Schreiben von Code.</span><span class="sxs-lookup"><span data-stu-id="4dc81-367">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="4dc81-368">Nächste</span><span class="sxs-lookup"><span data-stu-id="4dc81-368">Next</span></span>](adding-controller.md)

::: moniker-end
