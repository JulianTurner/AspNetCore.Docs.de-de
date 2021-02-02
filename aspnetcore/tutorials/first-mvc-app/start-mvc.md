---
title: Erste Schritte mit ASP.NET Core MVC
author: rick-anderson
description: Hier finden Sie Informationen zum Einstieg in ASP.NET Core MVC.
ms.author: riande
ms.date: 01/20/2021
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
ms.custom: contperf-fy21q3
ms.openlocfilehash: aaf930eee351ed757be60f648bce88b182d52799
ms.sourcegitcommit: da5a5bed5718a9f8db59356ef8890b4b60ced6e9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/22/2021
ms.locfileid: "98710772"
---
# <a name="get-started-with-aspnet-core-mvc"></a><span data-ttu-id="d0fb7-103">Erste Schritte mit ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="d0fb7-103">Get started with ASP.NET Core MVC</span></span>

<span data-ttu-id="d0fb7-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="d0fb7-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="d0fb7-105">Dies ist das erste Tutorial einer Tutorialreihe, die Informationen zur ASP.NET Core MVC-Webentwicklung mit Controllern und Ansichten vermittelt.</span><span class="sxs-lookup"><span data-stu-id="d0fb7-105">This is the first tutorial of a series that teaches ASP.NET Core MVC web development with controllers and views.</span></span>

<span data-ttu-id="d0fb7-106">Nach Abschluss der Reihe verfügen Sie über eine App, mit der Filmdaten verwaltet und angezeigt werden können.</span><span class="sxs-lookup"><span data-stu-id="d0fb7-106">At the end of the series, you'll have an app that manages and displays movie data.</span></span> <span data-ttu-id="d0fb7-107">Sie lernen Folgendes:</span><span class="sxs-lookup"><span data-stu-id="d0fb7-107">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="d0fb7-108">Erstellen einer Web-App</span><span class="sxs-lookup"><span data-stu-id="d0fb7-108">Create a web app.</span></span>
> * <span data-ttu-id="d0fb7-109">Hinzufügen eines Modells und Erstellen eines Gerüsts für das Modell</span><span class="sxs-lookup"><span data-stu-id="d0fb7-109">Add and scaffold a model.</span></span>
> * <span data-ttu-id="d0fb7-110">Arbeiten mit einer Datenbank</span><span class="sxs-lookup"><span data-stu-id="d0fb7-110">Work with a database.</span></span>
> * <span data-ttu-id="d0fb7-111">Hinzufügen von Such- und Überprüfungsfunktionen</span><span class="sxs-lookup"><span data-stu-id="d0fb7-111">Add search and validation.</span></span>

<span data-ttu-id="d0fb7-112">[Zeigen Sie Beispielcode an, oder laden Sie diesen herunter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mvc-app/start-mvc/sample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="d0fb7-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mvc-app/start-mvc/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="d0fb7-113">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="d0fb7-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d0fb7-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d0fb7-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="d0fb7-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d0fb7-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d0fb7-116">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="d0fb7-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-5.0.md)]

---

## <a name="create-a-web-app"></a><span data-ttu-id="d0fb7-117">Erstellen einer Web-App</span><span class="sxs-lookup"><span data-stu-id="d0fb7-117">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d0fb7-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d0fb7-118">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="d0fb7-119">Starten Sie Visual Studio, und wählen Sie **Neues Projekt erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="d0fb7-119">Start Visual Studio and select **Create a new project**.</span></span>
* <span data-ttu-id="d0fb7-120">Wählen Sie im Dialogfeld **Neues Projekt** die Option **ASP.NET Core-Webanwendung** > **Weiter** aus.</span><span class="sxs-lookup"><span data-stu-id="d0fb7-120">In the **Create a new project** dialog, select **ASP.NET Core Web Application** > **Next**.</span></span>
* <span data-ttu-id="d0fb7-121">Geben Sie `MvcMovie` im Dialogfeld **Neues Projekt konfigurieren** für **Projektname** ein.</span><span class="sxs-lookup"><span data-stu-id="d0fb7-121">In the **Configure your new project** dialog, enter `MvcMovie` for **Project name**.</span></span> <span data-ttu-id="d0fb7-122">Es ist wichtig, *MvcMovie* als Projektnamen anzugeben.</span><span class="sxs-lookup"><span data-stu-id="d0fb7-122">It's important to name the project *MvcMovie*.</span></span> <span data-ttu-id="d0fb7-123">Die Groß- und Kleinschreibung muss beim Kopieren von Code bei jedem `namespace` übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="d0fb7-123">Capitalization needs to match each `namespace` matches when code is copied.</span></span>
* <span data-ttu-id="d0fb7-124">Klicken Sie auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="d0fb7-124">Select **Create**.</span></span>
* <span data-ttu-id="d0fb7-125">Wählen Sie im Dialogfeld **Neue ASP.NET Core-Webanwendung erstellen** Folgendes aus:</span><span class="sxs-lookup"><span data-stu-id="d0fb7-125">In the **Create a new ASP.NET Core web application** dialog, select:</span></span>
  * <span data-ttu-id="d0fb7-126">In den Dropdownmenüs **.NET Core** und **ASP.NET Core 5.0**.</span><span class="sxs-lookup"><span data-stu-id="d0fb7-126">**.NET Core** and **ASP.NET Core 5.0** in the dropdowns.</span></span>
  * <span data-ttu-id="d0fb7-127">**ASP.NET Core-Web-App (Model View Controller)** .</span><span class="sxs-lookup"><span data-stu-id="d0fb7-127">**ASP.NET Core Web App (Model-View-Controller)**.</span></span>
  * <span data-ttu-id="d0fb7-128">**Erstellen**:</span><span class="sxs-lookup"><span data-stu-id="d0fb7-128">**Create**.</span></span>

![<span data-ttu-id="d0fb7-129">Erstellen einer neuen ASP.NET Core-Webanwendung</span><span class="sxs-lookup"><span data-stu-id="d0fb7-129">Create a new ASP.NET Core web application</span></span> ](start-mvc/_static/mvcVS19v16.9.png)

<span data-ttu-id="d0fb7-130">Alternative Ansätze zum Erstellen des Projekts finden Sie unter [Erstellen eines neuen Projekts in Visual Studio](/visualstudio/ide/create-new-project).</span><span class="sxs-lookup"><span data-stu-id="d0fb7-130">For alternative approaches to create the project, see [Create a new project in Visual Studio](/visualstudio/ide/create-new-project).</span></span>

<span data-ttu-id="d0fb7-131">Visual Studio hat die Standardprojektvorlage für das erstellte MVC-Projekt verwendet.</span><span class="sxs-lookup"><span data-stu-id="d0fb7-131">Visual Studio used the default project template for the created MVC project.</span></span> <span data-ttu-id="d0fb7-132">Für das erstellte Projekt gilt:</span><span class="sxs-lookup"><span data-stu-id="d0fb7-132">The created project:</span></span>

* <span data-ttu-id="d0fb7-133">Es handelt sich um eine funktionierende App.</span><span class="sxs-lookup"><span data-stu-id="d0fb7-133">Is a working app.</span></span>
* <span data-ttu-id="d0fb7-134">Es handelt sich um ein einfaches Startprojekt.</span><span class="sxs-lookup"><span data-stu-id="d0fb7-134">Is a basic starter project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="d0fb7-135">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d0fb7-135">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="d0fb7-136">Das Tutorial setzt voraus, dass Sie mit Visual Studio Code vertraut sind.</span><span class="sxs-lookup"><span data-stu-id="d0fb7-136">The tutorial assumes familiarity with VS Code.</span></span> <span data-ttu-id="d0fb7-137">Weitere Informationen finden Sie unter [Erste Schritte mit VS Code](https://code.visualstudio.com/docs) und [Hilfe zu Visual Studio Code](#visual-studio-code-help).</span><span class="sxs-lookup"><span data-stu-id="d0fb7-137">For more information, see [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help).</span></span>

* <span data-ttu-id="d0fb7-138">Öffnen Sie das [integrierte Terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="d0fb7-138">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="d0fb7-139">Wechseln Sie zu dem Verzeichnis (`cd`), in dem Projekt enthalten sein soll.</span><span class="sxs-lookup"><span data-stu-id="d0fb7-139">Change to the directory (`cd`) that will contain the project.</span></span>
* <span data-ttu-id="d0fb7-140">Führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="d0fb7-140">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="d0fb7-141">Möglicherweise wird ein Dialogfeld mit folgender Meldung angezeigt: **Die erforderlichen Ressourcen zum Erstellen und Debuggen sind in „MvcMovie“ nicht vorhanden. Hinzufügen?** . Klicken Sie in diesem Fall auf **Ja**.</span><span class="sxs-lookup"><span data-stu-id="d0fb7-141">If a dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**, select **Yes**</span></span>

  * <span data-ttu-id="d0fb7-142">`dotnet new mvc -o MvcMovie`: Erstellt ein neues ASP.NET Core MVC-Projekt im Ordner *MvcMovie*.</span><span class="sxs-lookup"><span data-stu-id="d0fb7-142">`dotnet new mvc -o MvcMovie`: Creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="d0fb7-143">`code -r MvcMovie`: Lädt die Projektdatei *MvcMovie.csproj* in Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="d0fb7-143">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d0fb7-144">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="d0fb7-144">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="d0fb7-145">Klicken Sie auf **Datei** > **Neue Projektmappe**.</span><span class="sxs-lookup"><span data-stu-id="d0fb7-145">Select **File** > **New Solution**.</span></span>

  ![Neue Projektmappe in macOS](start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="d0fb7-147">Navigieren Sie in Visual Studio für Mac-Versionen vor Version 8.6 zu **.NET Core** > **App** > **Webanwendung (Model-View-Controller)**  > **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="d0fb7-147">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span> <span data-ttu-id="d0fb7-148">Bei Version 8.6 oder höher klicken Sie auf **Web and Console** > **App** > **Webanwendung (Model-View-Controller)**  > **Weiter** (Web und Konsole).</span><span class="sxs-lookup"><span data-stu-id="d0fb7-148">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

  ![Auswählen der macOS-Web-App-Vorlage](start-mvc/_static/web_app_template_vsmac.png)

* <span data-ttu-id="d0fb7-150">Gehen Sie im Dialogfeld **Neue Webanwendung konfigurieren** folgendermaßen vor:</span><span class="sxs-lookup"><span data-stu-id="d0fb7-150">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="d0fb7-151">Vergewissern Sie sich, dass **Authentifizierung** auf **Keine Authentifizierung** festgelegt ist.</span><span class="sxs-lookup"><span data-stu-id="d0fb7-151">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="d0fb7-152">Wenn eine Option zum Auswählen eines **Zielframeworks** angezeigt wird, wählen Sie die neueste 5.x-Version aus.</span><span class="sxs-lookup"><span data-stu-id="d0fb7-152">If an option to select a **Target Framework** is presented, select the latest 5.x version.</span></span>
  * <span data-ttu-id="d0fb7-153">Klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="d0fb7-153">Select **Next**.</span></span>

* <span data-ttu-id="d0fb7-154">Nennen Sie das Projekt **MvcMovie**, und wählen Sie dann **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="d0fb7-154">Name the project **MvcMovie**, and then select **Create**.</span></span>

  ![Benennen des macOS-Projekts](start-mvc/_static/MvcMovie.png)

---

### <a name="run-the-app"></a><span data-ttu-id="d0fb7-156">Ausführen der App</span><span class="sxs-lookup"><span data-stu-id="d0fb7-156">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d0fb7-157">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d0fb7-157">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="d0fb7-158">Drücken Sie STRG+F5, um die App ohne den Debugger auszuführen.</span><span class="sxs-lookup"><span data-stu-id="d0fb7-158">Select Ctrl+F5 to run the app without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="d0fb7-159">Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="d0fb7-159">Visual Studio:</span></span>

  * <span data-ttu-id="d0fb7-160">Startet [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview).</span><span class="sxs-lookup"><span data-stu-id="d0fb7-160">Starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview).</span></span>
  * <span data-ttu-id="d0fb7-161">Führt die App aus.</span><span class="sxs-lookup"><span data-stu-id="d0fb7-161">Runs the app.</span></span>

  <span data-ttu-id="d0fb7-162">Die Adressleiste zeigt `localhost:port#` an, nicht `example.com`.</span><span class="sxs-lookup"><span data-stu-id="d0fb7-162">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="d0fb7-163">Der Standardhostname für Ihren lokalen Computer lautet `localhost`.</span><span class="sxs-lookup"><span data-stu-id="d0fb7-163">The standard hostname for your local computer is `localhost`.</span></span> <span data-ttu-id="d0fb7-164">Wenn Visual Studio ein Webprojekt erstellt, wird ein zufällig ausgewählter Port für den Webserver verwendet.</span><span class="sxs-lookup"><span data-stu-id="d0fb7-164">When Visual Studio creates a web project, a random port is used for the web server.</span></span>

<span data-ttu-id="d0fb7-165">Das Starten der App ohne Debuggen über STRG+F5 ermöglicht Ihnen Folgendes:</span><span class="sxs-lookup"><span data-stu-id="d0fb7-165">Launching the app without debugging by selecting Ctrl+F5 allows you to:</span></span>

* <span data-ttu-id="d0fb7-166">Nehmen Sie Änderungen am Code vor.</span><span class="sxs-lookup"><span data-stu-id="d0fb7-166">Make code changes.</span></span>
* <span data-ttu-id="d0fb7-167">Speichern Sie die Datei.</span><span class="sxs-lookup"><span data-stu-id="d0fb7-167">Save the file.</span></span>
* <span data-ttu-id="d0fb7-168">Aktualisieren Sie den Browser, und sehen Sie sich die Codeänderungen an.</span><span class="sxs-lookup"><span data-stu-id="d0fb7-168">Quickly refresh the browser and see the code changes.</span></span>

<span data-ttu-id="d0fb7-169">Sie können die App über das Menüelement **Debuggen** im Debugmodus oder Nicht-Debugmodus starten:</span><span class="sxs-lookup"><span data-stu-id="d0fb7-169">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

![Menü „Debuggen“](start-mvc/_static/debug_menu50.png)

<span data-ttu-id="d0fb7-171">Sie können die App debuggen, indem Sie die Schaltfläche **IIS Express** auswählen.</span><span class="sxs-lookup"><span data-stu-id="d0fb7-171">You can debug the app by selecting the **IIS Express** button</span></span>

![IIS Express](start-mvc/_static/iis_express50.png)

<span data-ttu-id="d0fb7-173">Auf der folgenden Abbildung sehen Sie die App:</span><span class="sxs-lookup"><span data-stu-id="d0fb7-173">The following image shows the app:</span></span>

![Start- oder Indexseite](start-mvc/_static/home50-vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="d0fb7-175">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d0fb7-175">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="d0fb7-176">Drücken Sie STRG+F5, um die App ohne den Debugger auszuführen.</span><span class="sxs-lookup"><span data-stu-id="d0fb7-176">Select Ctrl+F5 to run without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="d0fb7-177">Visual Studio Code:</span><span class="sxs-lookup"><span data-stu-id="d0fb7-177">Visual Studio Code:</span></span>

  * <span data-ttu-id="d0fb7-178">Startet [Kestrel](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="d0fb7-178">Starts [Kestrel](xref:fundamentals/servers/kestrel)</span></span>
  * <span data-ttu-id="d0fb7-179">Startet einen Browser.</span><span class="sxs-lookup"><span data-stu-id="d0fb7-179">Launches a browser.</span></span>
  * <span data-ttu-id="d0fb7-180">Navigiert zu `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="d0fb7-180">Navigates to `https://localhost:5001`.</span></span>

  <span data-ttu-id="d0fb7-181">Die Adressleiste zeigt `localhost:port:5001` an, nicht `example.com`.</span><span class="sxs-lookup"><span data-stu-id="d0fb7-181">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="d0fb7-182">Der Standardhostname für Ihren lokalen Computer lautet `localhost`.</span><span class="sxs-lookup"><span data-stu-id="d0fb7-182">The standard hostname for your local computer is `localhost`.</span></span> <span data-ttu-id="d0fb7-183">„Localhost“ dient nur Webanforderungen vom lokalen Computer.</span><span class="sxs-lookup"><span data-stu-id="d0fb7-183">Localhost only serves web requests from the local computer.</span></span>

<span data-ttu-id="d0fb7-184">Das Starten der App ohne Debuggen über STRG+F5 ermöglicht Ihnen Folgendes:</span><span class="sxs-lookup"><span data-stu-id="d0fb7-184">Launching the app without debugging by selecting Ctrl+F5 allows you to:</span></span>

* <span data-ttu-id="d0fb7-185">Nehmen Sie Änderungen am Code vor.</span><span class="sxs-lookup"><span data-stu-id="d0fb7-185">Make code changes.</span></span>
* <span data-ttu-id="d0fb7-186">Speichern Sie die Datei.</span><span class="sxs-lookup"><span data-stu-id="d0fb7-186">Save the file.</span></span>
* <span data-ttu-id="d0fb7-187">Aktualisieren Sie den Browser, und sehen Sie sich die Codeänderungen an.</span><span class="sxs-lookup"><span data-stu-id="d0fb7-187">Quickly refresh the browser and see the code changes.</span></span>

  ![Start- oder Indexseite](start-mvc/_static/home50-port5001.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d0fb7-189">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="d0fb7-189">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="d0fb7-190">Wählen Sie **Ausführen** > **Ohne Debuggen starten** auf, um die App zu starten.</span><span class="sxs-lookup"><span data-stu-id="d0fb7-190">Select **Run** > **Start Without Debugging** to launch the app.</span></span>

  <span data-ttu-id="d0fb7-191">Visual Studio für Mac:</span><span class="sxs-lookup"><span data-stu-id="d0fb7-191">Visual Studio for Mac:</span></span>

  * <span data-ttu-id="d0fb7-192">Startet den [Kestrel](xref:fundamentals/servers/index#kestrel)-Server.</span><span class="sxs-lookup"><span data-stu-id="d0fb7-192">Starts [Kestrel](xref:fundamentals/servers/index#kestrel) server.</span></span>
  * <span data-ttu-id="d0fb7-193">Startet einen Browser.</span><span class="sxs-lookup"><span data-stu-id="d0fb7-193">Launches a browser.</span></span>
  * <span data-ttu-id="d0fb7-194">Navigiert zu `http://localhost:port`, wobei es sich bei *port* um eine zufällig ausgewählte Portnummer handelt.</span><span class="sxs-lookup"><span data-stu-id="d0fb7-194">Navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

  [!INCLUDE[](~/includes/trustCertMac.md)]

  <span data-ttu-id="d0fb7-195">Die Adressleiste zeigt `localhost:port#` an, nicht `example.com`.</span><span class="sxs-lookup"><span data-stu-id="d0fb7-195">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="d0fb7-196">Der Standardhostname für Ihren lokalen Computer lautet `localhost`.</span><span class="sxs-lookup"><span data-stu-id="d0fb7-196">The standard hostname for your local computer is `localhost`.</span></span> <span data-ttu-id="d0fb7-197">Wenn Visual Studio ein Webprojekt erstellt, wird ein zufällig ausgewählter Port für den Webserver verwendet.</span><span class="sxs-lookup"><span data-stu-id="d0fb7-197">When Visual Studio creates a web project, a random port is used for the web server.</span></span>

<span data-ttu-id="d0fb7-198">Sie können die App über das Menü **Ausführen** im Debugmodus oder Nicht-Debugmodus starten.</span><span class="sxs-lookup"><span data-stu-id="d0fb7-198">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

<span data-ttu-id="d0fb7-199">Auf der folgenden Abbildung sehen Sie die App:</span><span class="sxs-lookup"><span data-stu-id="d0fb7-199">The following image shows the app:</span></span>

![Start- oder Indexseite](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="d0fb7-201">Im nächsten Teil dieses Tutorials erfahren Sie mehr über MVC und beginnen mit dem Schreiben von Code.</span><span class="sxs-lookup"><span data-stu-id="d0fb7-201">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="d0fb7-202">Weiter: Hinzufügen eines Controllers</span><span class="sxs-lookup"><span data-stu-id="d0fb7-202">Next: Add a controller</span></span>](adding-controller.md)

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="d0fb7-203">Dies ist das erste Tutorial einer Tutorialreihe, die Informationen zur ASP.NET Core MVC-Webentwicklung mit Controllern und Ansichten vermittelt.</span><span class="sxs-lookup"><span data-stu-id="d0fb7-203">This is the first tutorial of a series that teaches ASP.NET Core MVC web development with controllers and views.</span></span>

<span data-ttu-id="d0fb7-204">Nach Abschluss der Reihe verfügen Sie über eine App, mit der Filmdaten verwaltet und angezeigt werden können.</span><span class="sxs-lookup"><span data-stu-id="d0fb7-204">At the end of the series, you'll have an app that manages and displays movie data.</span></span> <span data-ttu-id="d0fb7-205">Sie lernen Folgendes:</span><span class="sxs-lookup"><span data-stu-id="d0fb7-205">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="d0fb7-206">Erstellen einer Web-App</span><span class="sxs-lookup"><span data-stu-id="d0fb7-206">Create a web app.</span></span>
> * <span data-ttu-id="d0fb7-207">Hinzufügen eines Modells und Erstellen eines Gerüsts für das Modell</span><span class="sxs-lookup"><span data-stu-id="d0fb7-207">Add and scaffold a model.</span></span>
> * <span data-ttu-id="d0fb7-208">Arbeiten mit einer Datenbank</span><span class="sxs-lookup"><span data-stu-id="d0fb7-208">Work with a database.</span></span>
> * <span data-ttu-id="d0fb7-209">Hinzufügen von Such- und Überprüfungsfunktionen</span><span class="sxs-lookup"><span data-stu-id="d0fb7-209">Add search and validation.</span></span>

<span data-ttu-id="d0fb7-210">[Zeigen Sie Beispielcode an, oder laden Sie diesen herunter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mvc-app/start-mvc/sample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="d0fb7-210">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mvc-app/start-mvc/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="d0fb7-211">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="d0fb7-211">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d0fb7-212">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d0fb7-212">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="d0fb7-213">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d0fb7-213">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d0fb7-214">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="d0fb7-214">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-app"></a><span data-ttu-id="d0fb7-215">Erstellen einer Web-App</span><span class="sxs-lookup"><span data-stu-id="d0fb7-215">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d0fb7-216">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d0fb7-216">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="d0fb7-217">Wählen Sie in Visual Studio die Option **Neues Projekt erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="d0fb7-217">From the Visual Studio, select **Create a new project**.</span></span>

* <span data-ttu-id="d0fb7-218">Wählen Sie **ASP.NET Core-Webanwendung** > **Weiter** aus.</span><span class="sxs-lookup"><span data-stu-id="d0fb7-218">Select **ASP.NET Core Web Application** > **Next**.</span></span>

  ![Erstellen eines neuen Projekts für eine ASP.NET Core-Webanwendung](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="d0fb7-220">Geben Sie dem Projekt den Namen **MvcMovie**, und klicken Sie dann auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="d0fb7-220">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="d0fb7-221">Es ist wichtig, dem Projekt den Namen **MvcMovie** zu geben, damit beim Kopieren von Code der Namespace übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="d0fb7-221">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![Neues Projekt konfigurieren](start-mvc/_static/config.png)

* <span data-ttu-id="d0fb7-223">Wählen Sie **Webanwendung (Model View Controller)** aus.</span><span class="sxs-lookup"><span data-stu-id="d0fb7-223">Select **Web Application(Model-View-Controller)**.</span></span> <span data-ttu-id="d0fb7-224">Wählen Sie in den Dropdownfeldern **.NET Core** und **ASP.NET Core 3.1** und anschließend **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="d0fb7-224">From the dropdown boxes, select **.NET Core** and **ASP.NET Core 3.1**, then select **Create**.</span></span>

  ![<span data-ttu-id="d0fb7-225">Dialogfeld „Neues Projekt“, .NET Core im linken Bereich, ASP.NET Core-Web-App</span><span class="sxs-lookup"><span data-stu-id="d0fb7-225">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project30.png)

<span data-ttu-id="d0fb7-226">Visual Studio hat die Standardprojektvorlage für das erstellte MVC-Projekt verwendet.</span><span class="sxs-lookup"><span data-stu-id="d0fb7-226">Visual Studio used the default project template for the created MVC project.</span></span> <span data-ttu-id="d0fb7-227">Für das erstellte Projekt gilt:</span><span class="sxs-lookup"><span data-stu-id="d0fb7-227">The created project:</span></span>

* <span data-ttu-id="d0fb7-228">Es handelt sich um eine funktionierende App.</span><span class="sxs-lookup"><span data-stu-id="d0fb7-228">Is a working app.</span></span>
* <span data-ttu-id="d0fb7-229">Es handelt sich um ein einfaches Startprojekt.</span><span class="sxs-lookup"><span data-stu-id="d0fb7-229">Is a basic starter project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="d0fb7-230">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d0fb7-230">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="d0fb7-231">Das Tutorial setzt voraus, dass Sie mit Visual Studio Code vertraut sind.</span><span class="sxs-lookup"><span data-stu-id="d0fb7-231">The tutorial assumes familiarity with VS Code.</span></span> <span data-ttu-id="d0fb7-232">Weitere Informationen finden Sie unter [Erste Schritte mit VS Code](https://code.visualstudio.com/docs) und [Hilfe zu Visual Studio Code](#visual-studio-code-help).</span><span class="sxs-lookup"><span data-stu-id="d0fb7-232">For more information, see [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help).</span></span>

* <span data-ttu-id="d0fb7-233">Öffnen Sie das [integrierte Terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="d0fb7-233">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="d0fb7-234">Wechseln Sie mit `cd` zu einem Ordner, der das Projekt enthalten soll.</span><span class="sxs-lookup"><span data-stu-id="d0fb7-234">Change directories (`cd`) to a folder that will contain the project.</span></span>
* <span data-ttu-id="d0fb7-235">Führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="d0fb7-235">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="d0fb7-236">Es wird ein Dialogfeld mit folgender Meldung angezeigt: **Die erforderlichen Objekte zum Erstellen und Debuggen sind in "MvcMovie" nicht vorhanden. Hinzufügen?** . Klicken Sie in diesem Fall auf **Ja**.</span><span class="sxs-lookup"><span data-stu-id="d0fb7-236">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**, select **Yes**.</span></span>

  * <span data-ttu-id="d0fb7-237">`dotnet new mvc -o MvcMovie`: Erstellt ein neues ASP.NET Core MVC-Projekt im Ordner *MvcMovie*.</span><span class="sxs-lookup"><span data-stu-id="d0fb7-237">`dotnet new mvc -o MvcMovie`: Creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="d0fb7-238">`code -r MvcMovie`: Lädt die Projektdatei *MvcMovie.csproj* in Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="d0fb7-238">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d0fb7-239">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="d0fb7-239">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="d0fb7-240">Klicken Sie auf **Datei** > **Neue Projektmappe**.</span><span class="sxs-lookup"><span data-stu-id="d0fb7-240">Select **File** > **New Solution**.</span></span>

  ![Neue Projektmappe in macOS](start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="d0fb7-242">Navigieren Sie in Visual Studio für Mac-Versionen vor Version 8.6 zu **.NET Core** > **App** > **Webanwendung (Model-View-Controller)**  > **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="d0fb7-242">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span> <span data-ttu-id="d0fb7-243">Bei Version 8.6 oder höher klicken Sie auf **Web and Console** > **App** > **Webanwendung (Model-View-Controller)**  > **Weiter** (Web und Konsole).</span><span class="sxs-lookup"><span data-stu-id="d0fb7-243">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

  ![Auswählen der macOS-Web-App-Vorlage](start-mvc/_static/web_app_template_vsmac.png)

* <span data-ttu-id="d0fb7-245">Gehen Sie im Dialogfeld **Neue Webanwendung konfigurieren** folgendermaßen vor:</span><span class="sxs-lookup"><span data-stu-id="d0fb7-245">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="d0fb7-246">Vergewissern Sie sich, dass **Authentifizierung** auf **Keine Authentifizierung** festgelegt ist.</span><span class="sxs-lookup"><span data-stu-id="d0fb7-246">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="d0fb7-247">Wenn eine Option zum Auswählen eines **Zielframeworks** angezeigt wird, wählen Sie die neueste 3.x-Version aus.</span><span class="sxs-lookup"><span data-stu-id="d0fb7-247">If an option to select a **Target Framework** is presented, select the latest 3.x version.</span></span>
  * <span data-ttu-id="d0fb7-248">Klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="d0fb7-248">Select **Next**.</span></span>

* <span data-ttu-id="d0fb7-249">Nennen Sie das Projekt **MvcMovie**, und wählen Sie dann **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="d0fb7-249">Name the project **MvcMovie**, and then select **Create**.</span></span>

  ![Benennen des macOS-Projekts](start-mvc/_static/MvcMovie.png)

---

### <a name="run-the-app"></a><span data-ttu-id="d0fb7-251">Ausführen der App</span><span class="sxs-lookup"><span data-stu-id="d0fb7-251">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d0fb7-252">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d0fb7-252">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="d0fb7-253">Drücken Sie STRG+F5, um die App ohne Debuggen auszuführen.</span><span class="sxs-lookup"><span data-stu-id="d0fb7-253">Select Ctrl+F5 to run the app without debugging.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="d0fb7-254">Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="d0fb7-254">Visual Studio:</span></span>

  * <span data-ttu-id="d0fb7-255">Startet [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview).</span><span class="sxs-lookup"><span data-stu-id="d0fb7-255">Starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview).</span></span>
  * <span data-ttu-id="d0fb7-256">Führt die App aus.</span><span class="sxs-lookup"><span data-stu-id="d0fb7-256">Runs the app.</span></span>

  <span data-ttu-id="d0fb7-257">Die Adressleiste zeigt `localhost:port#` an, nicht `example.com`.</span><span class="sxs-lookup"><span data-stu-id="d0fb7-257">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="d0fb7-258">Der Standardhostname für Ihren lokalen Computer lautet `localhost`.</span><span class="sxs-lookup"><span data-stu-id="d0fb7-258">The standard hostname for your local computer is `localhost`.</span></span> <span data-ttu-id="d0fb7-259">Wenn Visual Studio ein Webprojekt erstellt, wird ein zufällig ausgewählter Port für den Webserver verwendet.</span><span class="sxs-lookup"><span data-stu-id="d0fb7-259">When Visual Studio creates a web project, a random port is used for the web server.</span></span>

<span data-ttu-id="d0fb7-260">Das Starten der App ohne Debuggen über STRG+F5 ermöglicht Ihnen Folgendes:</span><span class="sxs-lookup"><span data-stu-id="d0fb7-260">Launching the app without debugging by selecting Ctrl+F5 allows you to:</span></span>

* <span data-ttu-id="d0fb7-261">Nehmen Sie Änderungen am Code vor.</span><span class="sxs-lookup"><span data-stu-id="d0fb7-261">Make code changes.</span></span>
* <span data-ttu-id="d0fb7-262">Speichern Sie die Datei.</span><span class="sxs-lookup"><span data-stu-id="d0fb7-262">Save the file.</span></span>
* <span data-ttu-id="d0fb7-263">Aktualisieren Sie den Browser, und sehen Sie sich die Codeänderungen an.</span><span class="sxs-lookup"><span data-stu-id="d0fb7-263">Quickly refresh the browser and see the code changes.</span></span>

<span data-ttu-id="d0fb7-264">Sie können die App über das Menüelement **Debuggen** im Debugmodus oder Nicht-Debugmodus starten:</span><span class="sxs-lookup"><span data-stu-id="d0fb7-264">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

![Menü „Debuggen“](start-mvc/_static/debug_menu.png)

<span data-ttu-id="d0fb7-266">Sie können die App debuggen, indem Sie die Schaltfläche **IIS Express** auswählen.</span><span class="sxs-lookup"><span data-stu-id="d0fb7-266">You can debug the app by selecting the **IIS Express** button</span></span>

![IIS Express](start-mvc/_static/iis_express.png)

<span data-ttu-id="d0fb7-268">Auf der folgenden Abbildung sehen Sie die App:</span><span class="sxs-lookup"><span data-stu-id="d0fb7-268">The following image shows the app:</span></span>

![Start- oder Indexseite](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="d0fb7-270">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d0fb7-270">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="d0fb7-271">Drücken Sie STRG+F5, um die App ohne Debuggen auszuführen.</span><span class="sxs-lookup"><span data-stu-id="d0fb7-271">Select Ctrl+F5 to run the app without debugging.</span></span>

  [!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="d0fb7-272">Visual Studio Code:</span><span class="sxs-lookup"><span data-stu-id="d0fb7-272">Visual Studio Code:</span></span>

  * <span data-ttu-id="d0fb7-273">Startet [Kestrel](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="d0fb7-273">Starts [Kestrel](xref:fundamentals/servers/kestrel)</span></span>
  * <span data-ttu-id="d0fb7-274">Startet einen Browser.</span><span class="sxs-lookup"><span data-stu-id="d0fb7-274">Launches a browser.</span></span>
  * <span data-ttu-id="d0fb7-275">Navigiert zu `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="d0fb7-275">Navigates to `https://localhost:5001`.</span></span>

  <span data-ttu-id="d0fb7-276">Die Adressleiste zeigt `localhost:port:5001` an, nicht `example.com`.</span><span class="sxs-lookup"><span data-stu-id="d0fb7-276">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="d0fb7-277">Der Standardhostname für Ihren lokalen Computer lautet `localhost`.</span><span class="sxs-lookup"><span data-stu-id="d0fb7-277">The standard hostname for your local computer is `localhost`.</span></span> <span data-ttu-id="d0fb7-278">„Localhost“ dient nur Webanforderungen vom lokalen Computer.</span><span class="sxs-lookup"><span data-stu-id="d0fb7-278">Localhost only serves web requests from the local computer.</span></span>

<span data-ttu-id="d0fb7-279">Das Starten der App ohne Debuggen über STRG+F5 ermöglicht Ihnen Folgendes:</span><span class="sxs-lookup"><span data-stu-id="d0fb7-279">Launching the app without debugging by selecting Ctrl+F5 allows you to:</span></span>

* <span data-ttu-id="d0fb7-280">Nehmen Sie Änderungen am Code vor.</span><span class="sxs-lookup"><span data-stu-id="d0fb7-280">Make code changes.</span></span>
* <span data-ttu-id="d0fb7-281">Speichern Sie die Datei.</span><span class="sxs-lookup"><span data-stu-id="d0fb7-281">Save the file.</span></span>
* <span data-ttu-id="d0fb7-282">Aktualisieren Sie den Browser, und sehen Sie sich die Codeänderungen an.</span><span class="sxs-lookup"><span data-stu-id="d0fb7-282">Quickly refresh the browser and see the code changes.</span></span>

  ![Start- oder Indexseite](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d0fb7-284">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="d0fb7-284">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="d0fb7-285">Wählen Sie **Ausführen** > **Ohne Debuggen starten** auf, um die App zu starten.</span><span class="sxs-lookup"><span data-stu-id="d0fb7-285">Select **Run** > **Start Without Debugging** to launch the app.</span></span>

  <span data-ttu-id="d0fb7-286">Visual Studio für Mac startet den [Kestrel](xref:fundamentals/servers/index#kestrel)-Server und einen Browser und navigiert zu `http://localhost:port`, wobei es sich bei *port* um eine zufällig ausgewählte Portnummer handelt.</span><span class="sxs-lookup"><span data-stu-id="d0fb7-286">Visual Studio for Mac: starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

<span data-ttu-id="d0fb7-287">Die Adressleiste zeigt `localhost:port#` an, nicht `example.com`.</span><span class="sxs-lookup"><span data-stu-id="d0fb7-287">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="d0fb7-288">Der Standardhostname für Ihren lokalen Computer lautet `localhost`.</span><span class="sxs-lookup"><span data-stu-id="d0fb7-288">The standard hostname for your local computer is `localhost`.</span></span> <span data-ttu-id="d0fb7-289">Wenn in Visual Studio ein Webprojekt erstellt wird, wird für den Webserver ein zufälliger Port verwendet.</span><span class="sxs-lookup"><span data-stu-id="d0fb7-289">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="d0fb7-290">Wenn Sie die App ausführen, wird eine andere Portnummer angezeigt.</span><span class="sxs-lookup"><span data-stu-id="d0fb7-290">When you run the app, you'll see a different port number.</span></span>

<span data-ttu-id="d0fb7-291">Sie können die App über das Menü **Ausführen** im Debugmodus oder Nicht-Debugmodus starten.</span><span class="sxs-lookup"><span data-stu-id="d0fb7-291">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

<span data-ttu-id="d0fb7-292">Auf der folgenden Abbildung sehen Sie die App:</span><span class="sxs-lookup"><span data-stu-id="d0fb7-292">The following image shows the app:</span></span>

![Start- oder Indexseite](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="d0fb7-294">Im nächsten Teil dieses Tutorials erfahren Sie mehr über MVC und beginnen mit dem Schreiben von Code.</span><span class="sxs-lookup"><span data-stu-id="d0fb7-294">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="d0fb7-295">Nächste</span><span class="sxs-lookup"><span data-stu-id="d0fb7-295">Next</span></span>](adding-controller.md)

::: moniker-end
