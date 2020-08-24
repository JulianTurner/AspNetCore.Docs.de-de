---
title: 'Tutorial: Erstellen einer Web-API mit ASP.NET Core'
author: rick-anderson
description: Informationen zum Erstellen einer Web-API mit ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 08/13/2020
no-loc:
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
uid: tutorials/first-web-api
ms.openlocfilehash: ce0dfdf1ce88b55790d33918a2d20bc19a09b288
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88626895"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a><span data-ttu-id="8829e-103">Tutorial: Erstellen einer Web-API mit ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8829e-103">Tutorial: Create a web API with ASP.NET Core</span></span>

<span data-ttu-id="8829e-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5) und [Mike Wasson](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="8829e-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="8829e-105">In diesem Tutorial lernen Sie die Grundlagen der Erstellung einer Web-API mit ASP.NET Core kennen.</span><span class="sxs-lookup"><span data-stu-id="8829e-105">This tutorial teaches the basics of building a web API with ASP.NET Core.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8829e-106">In diesem Tutorial lernen Sie, wie die folgenden Aufgaben ausgeführt werden:</span><span class="sxs-lookup"><span data-stu-id="8829e-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="8829e-107">Erstellen eines Web-API-Projekts</span><span class="sxs-lookup"><span data-stu-id="8829e-107">Create a web API project.</span></span>
> * <span data-ttu-id="8829e-108">Hinzufügen einer Modellklasse und eines Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="8829e-108">Add a model class and a database context.</span></span>
> * <span data-ttu-id="8829e-109">Erstellen eines Controllergerüsts mit CRUD-Methoden</span><span class="sxs-lookup"><span data-stu-id="8829e-109">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="8829e-110">Konfigurieren des Routings, der URL-Pfade und der Rückgabewerte</span><span class="sxs-lookup"><span data-stu-id="8829e-110">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="8829e-111">Aufrufen der Web-API mit Postman</span><span class="sxs-lookup"><span data-stu-id="8829e-111">Call the web API with Postman.</span></span>

<span data-ttu-id="8829e-112">Abschließend steht Ihnen eine Web-API zur Verfügung, die in einer relationalen Datenbank gespeicherte To-do-Elemente verwalten kann.</span><span class="sxs-lookup"><span data-stu-id="8829e-112">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="8829e-113">Übersicht</span><span class="sxs-lookup"><span data-stu-id="8829e-113">Overview</span></span>

<span data-ttu-id="8829e-114">In diesem Tutorial wird die folgende API erstellt:</span><span class="sxs-lookup"><span data-stu-id="8829e-114">This tutorial creates the following API:</span></span>

|<span data-ttu-id="8829e-115">API</span><span class="sxs-lookup"><span data-stu-id="8829e-115">API</span></span> | <span data-ttu-id="8829e-116">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="8829e-116">Description</span></span> | <span data-ttu-id="8829e-117">Anforderungstext</span><span class="sxs-lookup"><span data-stu-id="8829e-117">Request body</span></span> | <span data-ttu-id="8829e-118">Antworttext</span><span class="sxs-lookup"><span data-stu-id="8829e-118">Response body</span></span> |
|--- | ---- | ---- | ---- |
|`GET /api/TodoItems` | <span data-ttu-id="8829e-119">Alle To-do-Elemente abrufen</span><span class="sxs-lookup"><span data-stu-id="8829e-119">Get all to-do items</span></span> | <span data-ttu-id="8829e-120">Keine</span><span class="sxs-lookup"><span data-stu-id="8829e-120">None</span></span> | <span data-ttu-id="8829e-121">Array von To-do-Elementen</span><span class="sxs-lookup"><span data-stu-id="8829e-121">Array of to-do items</span></span>|
|`GET /api/TodoItems/{id}` | <span data-ttu-id="8829e-122">Ein Element nach ID abrufen</span><span class="sxs-lookup"><span data-stu-id="8829e-122">Get an item by ID</span></span> | <span data-ttu-id="8829e-123">Keine</span><span class="sxs-lookup"><span data-stu-id="8829e-123">None</span></span> | <span data-ttu-id="8829e-124">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="8829e-124">To-do item</span></span>|
|`POST /api/TodoItems` | <span data-ttu-id="8829e-125">Neues Element hinzufügen</span><span class="sxs-lookup"><span data-stu-id="8829e-125">Add a new item</span></span> | <span data-ttu-id="8829e-126">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="8829e-126">To-do item</span></span> | <span data-ttu-id="8829e-127">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="8829e-127">To-do item</span></span> |
|`PUT /api/TodoItems/{id}` | <span data-ttu-id="8829e-128">Vorhandenes Element aktualisieren &nbsp;</span><span class="sxs-lookup"><span data-stu-id="8829e-128">Update an existing item &nbsp;</span></span> | <span data-ttu-id="8829e-129">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="8829e-129">To-do item</span></span> | <span data-ttu-id="8829e-130">Keine</span><span class="sxs-lookup"><span data-stu-id="8829e-130">None</span></span> |
|<span data-ttu-id="8829e-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="8829e-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span></span> | <span data-ttu-id="8829e-132">Löschen eines Elements &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="8829e-132">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="8829e-133">Keine</span><span class="sxs-lookup"><span data-stu-id="8829e-133">None</span></span> | <span data-ttu-id="8829e-134">Keine</span><span class="sxs-lookup"><span data-stu-id="8829e-134">None</span></span>|

<span data-ttu-id="8829e-135">Das folgende Diagramm zeigt den Entwurf der App.</span><span class="sxs-lookup"><span data-stu-id="8829e-135">The following diagram shows the design of the app.</span></span>

![Der Client ist das Feld ganz links.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="8829e-141">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="8829e-141">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8829e-142">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8829e-142">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="8829e-143">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8829e-143">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="8829e-144">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="8829e-144">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="8829e-145">Erstellen eines Webprojekts</span><span class="sxs-lookup"><span data-stu-id="8829e-145">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8829e-146">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8829e-146">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="8829e-147">Klicken Sie im Menü **Datei** auf **Neu** > **Projekt**.</span><span class="sxs-lookup"><span data-stu-id="8829e-147">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="8829e-148">Wählen Sie die Vorlage **ASP.NET Core-Webanwendung** aus, und klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="8829e-148">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="8829e-149">Geben Sie dem Projekt den Namen *TodoApi*, und klicken Sie auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="8829e-149">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="8829e-150">Vergewissern Sie sich, dass im Dialogfeld **Neue ASP.NET Core-Webanwendung erstellen** die Optionen **.NET Core** und **ASP.NET Core 3.1** ausgewählt sind.</span><span class="sxs-lookup"><span data-stu-id="8829e-150">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.1** are selected.</span></span> <span data-ttu-id="8829e-151">Wählen Sie die Vorlage **API** aus, und klicken Sie auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="8829e-151">Select the **API** template and click **Create**.</span></span>

![VS-Dialogfeld „Neues Projekt“](first-web-api/_static/vs3.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="8829e-153">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8829e-153">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="8829e-154">Öffnen Sie das [integrierte Terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="8829e-154">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="8829e-155">Wechseln Sie mit dem Befehl `cd`' zu dem Ordner, der den Projektordner enthalten soll.</span><span class="sxs-lookup"><span data-stu-id="8829e-155">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="8829e-156">Führen Sie die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="8829e-156">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="8829e-157">Wenn Sie in einem Dialogfeld angeben müssen, ob Sie dem Projekt die erforderlichen Elemente hinzufügen möchten, wählen Sie **Ja** aus.</span><span class="sxs-lookup"><span data-stu-id="8829e-157">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="8829e-158">Die obenstehenden Befehle haben folgende Konsequenzen:</span><span class="sxs-lookup"><span data-stu-id="8829e-158">The preceding commands:</span></span>

  * <span data-ttu-id="8829e-159">Ein neues Web-API-Projekt wird erstellt und in Visual Studio Code geöffnet.</span><span class="sxs-lookup"><span data-stu-id="8829e-159">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="8829e-160">Die NuGet-Pakete, die im nächsten Abschnitt erforderlich sind, werden hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="8829e-160">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="8829e-161">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="8829e-161">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="8829e-162">Klicken Sie auf **Datei** > **Neue Projektmappe**.</span><span class="sxs-lookup"><span data-stu-id="8829e-162">Select **File** > **New Solution**.</span></span>

  ![Neue Projektmappe in macOS](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="8829e-164">Navigieren Sie in Visual Studio für Mac-Versionen vor Version 8.6 zu **.NET Core** > **App** > **API** > **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="8829e-164">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="8829e-165">Bei Version 8.6 oder höher klicken Sie auf **Web and Console** > **App** > **API** > **Weiter** (Web und Konsole).</span><span class="sxs-lookup"><span data-stu-id="8829e-165">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>

  ![Auswählen von macOS-API-Vorlagen](first-web-api-mac/_static/api_template.png)

* <span data-ttu-id="8829e-167">Wählen Sie im Dialogfeld **Neue ASP.NET Core-Web-API konfigurieren** die neueste .NET Core 3.x-Version als **Zielframework** aus.</span><span class="sxs-lookup"><span data-stu-id="8829e-167">In the **Configure the new ASP.NET Core Web API** dialog, select the latest .NET Core 3.x **Target Framework**.</span></span> <span data-ttu-id="8829e-168">Klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="8829e-168">Select **Next**.</span></span>

* <span data-ttu-id="8829e-169">Geben Sie für **Projektname** *TodoApi* ein, und wählen Sie dann **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="8829e-169">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![Dialogfeld „Konfiguration“](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="8829e-171">Öffnen Sie im Projektordner ein Befehlsterminal, und führen Sie die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="8829e-171">Open a command terminal in the project folder and run the following commands:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-api"></a><span data-ttu-id="8829e-172">Testen der API</span><span class="sxs-lookup"><span data-stu-id="8829e-172">Test the API</span></span>

<span data-ttu-id="8829e-173">Die Projektvorlage erstellt eine `WeatherForecast`-API.</span><span class="sxs-lookup"><span data-stu-id="8829e-173">The project template creates a `WeatherForecast` API.</span></span> <span data-ttu-id="8829e-174">Rufen Sie in einem Browser die `Get`-Methode zum Testen der App auf.</span><span class="sxs-lookup"><span data-stu-id="8829e-174">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8829e-175">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8829e-175">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="8829e-176">Drücken Sie STRG+F5, um die App auszuführen.</span><span class="sxs-lookup"><span data-stu-id="8829e-176">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="8829e-177">Visual Studio startet einen Browser und navigiert zu `https://localhost:<port>/WeatherForecast`, wobei es sich bei `<port>` um eine zufällig ausgewählte Portnummer handelt.</span><span class="sxs-lookup"><span data-stu-id="8829e-177">Visual Studio launches a browser and navigates to `https://localhost:<port>/WeatherForecast`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="8829e-178">Wenn Sie in einem Dialogfeld angeben müssen, ob Sie dem IIS Express-Zertifikat vertrauen, wählen Sie **Ja** aus.</span><span class="sxs-lookup"><span data-stu-id="8829e-178">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="8829e-179">Wählen Sie im folgenden Dialogfeld **Sicherheitswarnung** die Option **Ja** aus.</span><span class="sxs-lookup"><span data-stu-id="8829e-179">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="8829e-180">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8829e-180">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="8829e-181">Drücken Sie STRG+F5, um die App auszuführen.</span><span class="sxs-lookup"><span data-stu-id="8829e-181">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="8829e-182">Navigieren Sie in einem Browser zur folgenden URL: `https://localhost:5001/WeatherForecast`.</span><span class="sxs-lookup"><span data-stu-id="8829e-182">In a browser, go to following URL: `https://localhost:5001/WeatherForecast`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="8829e-183">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="8829e-183">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="8829e-184">Wählen Sie **Ausführen** > **Debugging starten** aus, um die App zu starten.</span><span class="sxs-lookup"><span data-stu-id="8829e-184">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="8829e-185">Visual Studio für Mac startet einen Browser und navigiert zu `https://localhost:<port>`, wobei es sich bei `<port>` um eine zufällig ausgewählte Portnummer handelt.</span><span class="sxs-lookup"><span data-stu-id="8829e-185">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="8829e-186">Der Fehler „HTTP 404: Nicht gefunden“ wird zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="8829e-186">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="8829e-187">Fügen Sie `/WeatherForecast` an die URL an, d. h., ändern Sie sie in `https://localhost:<port>/WeatherForecast`.</span><span class="sxs-lookup"><span data-stu-id="8829e-187">Append `/WeatherForecast` to the URL (change the URL to `https://localhost:<port>/WeatherForecast`).</span></span>

---

<span data-ttu-id="8829e-188">Der zurückgegebene JSON-Code sieht in etwa wie folgt aus:</span><span class="sxs-lookup"><span data-stu-id="8829e-188">JSON similar to the following is returned:</span></span>

```json
[
    {
        "date": "2019-07-16T19:04:05.7257911-06:00",
        "temperatureC": 52,
        "temperatureF": 125,
        "summary": "Mild"
    },
    {
        "date": "2019-07-17T19:04:05.7258461-06:00",
        "temperatureC": 36,
        "temperatureF": 96,
        "summary": "Warm"
    },
    {
        "date": "2019-07-18T19:04:05.7258467-06:00",
        "temperatureC": 39,
        "temperatureF": 102,
        "summary": "Cool"
    },
    {
        "date": "2019-07-19T19:04:05.7258471-06:00",
        "temperatureC": 10,
        "temperatureF": 49,
        "summary": "Bracing"
    },
    {
        "date": "2019-07-20T19:04:05.7258474-06:00",
        "temperatureC": -1,
        "temperatureF": 31,
        "summary": "Chilly"
    }
]
```

## <a name="add-a-model-class"></a><span data-ttu-id="8829e-189">Hinzufügen einer Modellklasse</span><span class="sxs-lookup"><span data-stu-id="8829e-189">Add a model class</span></span>

<span data-ttu-id="8829e-190">Ein *Modell* ist eine Gruppe von Klassen, die die Daten darstellen, die die App verwaltet.</span><span class="sxs-lookup"><span data-stu-id="8829e-190">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="8829e-191">Das Modell für diese App ist eine einzelne `TodoItem`-Klasse.</span><span class="sxs-lookup"><span data-stu-id="8829e-191">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8829e-192">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8829e-192">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="8829e-193">Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt.</span><span class="sxs-lookup"><span data-stu-id="8829e-193">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="8829e-194">Klicken Sie auf **Hinzufügen** > **Neuer Ordner**.</span><span class="sxs-lookup"><span data-stu-id="8829e-194">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="8829e-195">Geben Sie dem Ordner den Namen *Modelle*.</span><span class="sxs-lookup"><span data-stu-id="8829e-195">Name the folder *Models*.</span></span>

* <span data-ttu-id="8829e-196">Klicken Sie mit der rechten Maustaste auf den Ordner *Modelle*, und wählen Sie **Hinzufügen** > **Klasse** aus.</span><span class="sxs-lookup"><span data-stu-id="8829e-196">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="8829e-197">Nennen Sie die Klasse *TodoItem*, und wählen Sie **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="8829e-197">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="8829e-198">Ersetzen Sie den Vorlagencode durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="8829e-198">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="8829e-199">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8829e-199">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="8829e-200">Fügen Sie einen Ordner namens *Modelle* hinzu.</span><span class="sxs-lookup"><span data-stu-id="8829e-200">Add a folder named *Models*.</span></span>

* <span data-ttu-id="8829e-201">Fügen Sie dem Ordner *Modelle* mit dem folgenden Code eine `TodoItem`-Klasse hinzu:</span><span class="sxs-lookup"><span data-stu-id="8829e-201">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="8829e-202">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="8829e-202">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="8829e-203">Klicken Sie mit der rechten Maustaste auf das Projekt.</span><span class="sxs-lookup"><span data-stu-id="8829e-203">Right-click the project.</span></span> <span data-ttu-id="8829e-204">Klicken Sie auf **Hinzufügen** > **Neuer Ordner**.</span><span class="sxs-lookup"><span data-stu-id="8829e-204">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="8829e-205">Geben Sie dem Ordner den Namen *Modelle*.</span><span class="sxs-lookup"><span data-stu-id="8829e-205">Name the folder *Models*.</span></span>

  ![Neuer Ordner](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="8829e-207">Klicken Sie mit der rechten Maustaste auf den Ordner *Modelle*, und wählen Sie **Hinzufügen** > **Neue Datei** > **Allgemein** > **Leere Klasse** aus.</span><span class="sxs-lookup"><span data-stu-id="8829e-207">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="8829e-208">Nennen Sie die Klasse *TodoItem*, und klicken Sie dann auf **Neu**.</span><span class="sxs-lookup"><span data-stu-id="8829e-208">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="8829e-209">Ersetzen Sie den Vorlagencode durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="8829e-209">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs?name=snippet)]

<span data-ttu-id="8829e-210">Die `Id`-Eigenschaft fungiert als eindeutiger Schlüssel in einer relationalen Datenbank.</span><span class="sxs-lookup"><span data-stu-id="8829e-210">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="8829e-211">Modellklassen können überall im Projekt platziert werden, doch gemäß der Konvention wird der Ordner *Modelle* verwendet.</span><span class="sxs-lookup"><span data-stu-id="8829e-211">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="8829e-212">Hinzufügen eines Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="8829e-212">Add a database context</span></span>

<span data-ttu-id="8829e-213">Der *Datenbankkontext* ist die Hauptklasse, die die Entity Framework-Funktionen für ein Datenmodell koordiniert.</span><span class="sxs-lookup"><span data-stu-id="8829e-213">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="8829e-214">Diese Klasse wird durch Ableiten von der `Microsoft.EntityFrameworkCore.DbContext`-Klasse erstellt.</span><span class="sxs-lookup"><span data-stu-id="8829e-214">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8829e-215">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8829e-215">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-nuget-packages"></a><span data-ttu-id="8829e-216">Hinzufügen von NuGet-Paketen</span><span class="sxs-lookup"><span data-stu-id="8829e-216">Add NuGet packages</span></span>

* <span data-ttu-id="8829e-217">Klicken Sie im Menü **Extras** auf **NuGet-Paket-Manager > NuGet-Pakete für Projektmappe verwalten...** .</span><span class="sxs-lookup"><span data-stu-id="8829e-217">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="8829e-218">Klicken Sie auf die Registerkarte **Durchsuchen**, und geben Sie dann **Microsoft.EntityFrameworkCore.SqlServer** in das Suchfeld ein.</span><span class="sxs-lookup"><span data-stu-id="8829e-218">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.SqlServer** in the search box.</span></span>
* <span data-ttu-id="8829e-219">Wählen Sie im linken Bereich **Microsoft.EntityFrameworkCore.SqlServer** aus.</span><span class="sxs-lookup"><span data-stu-id="8829e-219">Select **Microsoft.EntityFrameworkCore.SqlServer** in the left pane.</span></span>
* <span data-ttu-id="8829e-220">Aktivieren Sie das Kontrollkästchen **Projekt** im rechten Bereich, und klicken Sie dann auf **Installieren**.</span><span class="sxs-lookup"><span data-stu-id="8829e-220">Select the **Project** check box in the right pane and then select **Install**.</span></span>
* <span data-ttu-id="8829e-221">Verwenden Sie die Anweisungen oben zum Hinzuzufügen des **Microsoft.EntityFrameworkCore.InMemory**-NuGet-Pakets.</span><span class="sxs-lookup"><span data-stu-id="8829e-221">Use the preceding instructions to add the **Microsoft.EntityFrameworkCore.InMemory** NuGet package.</span></span>

![NuGet-Paket-Manager](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="8829e-223">Hinzufügen des TodoContext-Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="8829e-223">Add the TodoContext database context</span></span>

* <span data-ttu-id="8829e-224">Klicken Sie mit der rechten Maustaste auf den Ordner *Modelle*, und wählen Sie **Hinzufügen** > **Klasse** aus.</span><span class="sxs-lookup"><span data-stu-id="8829e-224">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="8829e-225">Nennen Sie die Klasse *TodoContext*, und klicken Sie auf **Hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="8829e-225">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="8829e-226">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="8829e-226">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="8829e-227">Fügen Sie eine `TodoContext`-Klasse zum Ordner *Modelle* hinzu.</span><span class="sxs-lookup"><span data-stu-id="8829e-227">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="8829e-228">Geben Sie den folgenden Code ein:</span><span class="sxs-lookup"><span data-stu-id="8829e-228">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="8829e-229">Registrieren des Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="8829e-229">Register the database context</span></span>

<span data-ttu-id="8829e-230">In ASP.NET Core müssen Dienste wie der Datenbankkontext mit dem Container [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) registriert werden.</span><span class="sxs-lookup"><span data-stu-id="8829e-230">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="8829e-231">Der Container stellt den Dienst für Controller bereit.</span><span class="sxs-lookup"><span data-stu-id="8829e-231">The container provides the service to controllers.</span></span>

<span data-ttu-id="8829e-232">Aktualisieren Sie *Startup.cs* mit dem folgenden hervorgehobenen Code:</span><span class="sxs-lookup"><span data-stu-id="8829e-232">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="8829e-233">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="8829e-233">The preceding code:</span></span>

* <span data-ttu-id="8829e-234">Entfernt nicht benötigte `using`-Deklarationen</span><span class="sxs-lookup"><span data-stu-id="8829e-234">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="8829e-235">Fügt dem Abhängigkeitscontainer den Datenbankkontext hinzu</span><span class="sxs-lookup"><span data-stu-id="8829e-235">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="8829e-236">Gibt an, dass der Datenbankkontext eine In-Memory Database verwendet</span><span class="sxs-lookup"><span data-stu-id="8829e-236">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="8829e-237">Erstellen eines Controllergerüsts</span><span class="sxs-lookup"><span data-stu-id="8829e-237">Scaffold a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8829e-238">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8829e-238">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="8829e-239">Klicken Sie mit der rechten Maustaste auf den Ordner *Controller*.</span><span class="sxs-lookup"><span data-stu-id="8829e-239">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="8829e-240">Wählen Sie **Hinzufügen** > **Neues Gerüstelement** aus.</span><span class="sxs-lookup"><span data-stu-id="8829e-240">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="8829e-241">Klicken Sie auf **API-Controller mit Aktionen unter Verwendung von Entity Framework** und dann auf **Hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="8829e-241">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="8829e-242">Führen Sie im Dialogfeld **API-Controller mit Aktionen unter Verwendung von Entity Framework** folgende Schritte aus:</span><span class="sxs-lookup"><span data-stu-id="8829e-242">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="8829e-243">Wählen Sie in der **Modellklasse** das Element **TodoItem (TodoApi.Models)** aus.</span><span class="sxs-lookup"><span data-stu-id="8829e-243">Select **TodoItem (TodoApi.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="8829e-244">Wählen Sie in der **Datenkontextklasse** das Element **TodoContext (TodoApi.Models)** aus.</span><span class="sxs-lookup"><span data-stu-id="8829e-244">Select **TodoContext (TodoApi.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="8829e-245">Wählen Sie **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="8829e-245">Select **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="8829e-246">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="8829e-246">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="8829e-247">Führen Sie die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="8829e-247">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet tool update -g Dotnet-aspnet-codegenerator
dotnet-aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="8829e-248">Die obenstehenden Befehle haben folgende Konsequenzen:</span><span class="sxs-lookup"><span data-stu-id="8829e-248">The preceding commands:</span></span>

* <span data-ttu-id="8829e-249">Die NuGet-Pakete für den Gerüstbau werden hinzufügt.</span><span class="sxs-lookup"><span data-stu-id="8829e-249">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="8829e-250">Die Gerüstbau-Engine (`dotnet-aspnet-codegenerator`) wird installiert.</span><span class="sxs-lookup"><span data-stu-id="8829e-250">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="8829e-251">Das Gerüst für `TodoItemsController` wird erstellt.</span><span class="sxs-lookup"><span data-stu-id="8829e-251">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="8829e-252">Der generierte Code hat folgende Auswirkungen:</span><span class="sxs-lookup"><span data-stu-id="8829e-252">The generated code:</span></span>

* <span data-ttu-id="8829e-253">Markiert die Klasse mit dem [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute)-Attribut.</span><span class="sxs-lookup"><span data-stu-id="8829e-253">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="8829e-254">Dieses Attribut gibt an, dass der Controller auf Web-API-Anforderungen reagiert.</span><span class="sxs-lookup"><span data-stu-id="8829e-254">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="8829e-255">Weitere Informationen zu bestimmten Verhaltensweisen, die das Attribut ermöglicht, finden Sie unter <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="8829e-255">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="8829e-256">Verwendet die Abhängigkeitsinjektion, um den Datenbankkontext (`TodoContext`) dem Controller hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="8829e-256">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="8829e-257">Der Datenbankkontext wird in den einzelnen [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete)-Methoden im Controller verwendet.</span><span class="sxs-lookup"><span data-stu-id="8829e-257">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<span data-ttu-id="8829e-258">Die ASP.NET Core-Vorlagen für:</span><span class="sxs-lookup"><span data-stu-id="8829e-258">The ASP.NET Core templates for:</span></span>

* <span data-ttu-id="8829e-259">Controller mit Ansichten enthalten `[action]` in der Routenvorlage.</span><span class="sxs-lookup"><span data-stu-id="8829e-259">Controllers with views include `[action]` in the route template.</span></span>
* <span data-ttu-id="8829e-260">API-Controller enthalten keine `[action]` in der Routenvorlage.</span><span class="sxs-lookup"><span data-stu-id="8829e-260">API controllers don't include `[action]` in the route template.</span></span>

<span data-ttu-id="8829e-261">Wenn sich das `[action]`-Token nicht in der Routenvorlage befindet, wird der [action](xref:mvc/controllers/routing#action)-Name von der Route ausgeschlossen.</span><span class="sxs-lookup"><span data-stu-id="8829e-261">When the `[action]` token isn't in the route template, the [action](xref:mvc/controllers/routing#action) name is excluded from the route.</span></span> <span data-ttu-id="8829e-262">Dies bedeutet, dass der zugehörige Methodenname der Aktion nicht in der übereinstimmenden Route verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="8829e-262">That is, the action's associated method name isn't used in the matching route.</span></span>

## <a name="examine-the-posttodoitem-create-method"></a><span data-ttu-id="8829e-263">Überblick über die PostTodoItem-Erstellungsmethode</span><span class="sxs-lookup"><span data-stu-id="8829e-263">Examine the PostTodoItem create method</span></span>

<span data-ttu-id="8829e-264">Ersetzen Sie die Rückgabeanweisung in `PostTodoItem` durch eine Anweisung mit dem [nameof](/dotnet/csharp/language-reference/operators/nameof)-Operator:</span><span class="sxs-lookup"><span data-stu-id="8829e-264">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="8829e-265">Der oben stehende Code ist eine HTTP POST-Methode, wie durch das [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute)-Attribut angegeben.</span><span class="sxs-lookup"><span data-stu-id="8829e-265">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="8829e-266">Die Methode ruft den Wert der Aufgabe aus dem Text der HTTP-Anforderung ab.</span><span class="sxs-lookup"><span data-stu-id="8829e-266">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="8829e-267">Weitere Informationen finden Sie unter [Attributrouting mit Http[Verb]-Attributen](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="8829e-267">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="8829e-268">Die <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*>-Methode:</span><span class="sxs-lookup"><span data-stu-id="8829e-268">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="8829e-269">Gibt den HTTP-Statuscode 201 zurück, wenn der Vorgang erfolgreich ist.</span><span class="sxs-lookup"><span data-stu-id="8829e-269">Returns an HTTP 201 status code if successful.</span></span> <span data-ttu-id="8829e-270">HTTP 201 ist die Standardantwort für eine HTTP POST-Methode, die eine neue Ressource auf dem Server erstellt.</span><span class="sxs-lookup"><span data-stu-id="8829e-270">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="8829e-271">Fügt der Antwort einen [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location)-Header hinzu.</span><span class="sxs-lookup"><span data-stu-id="8829e-271">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="8829e-272">Der `Location`-Header legt den [URI](https://developer.mozilla.org/docs/Glossary/URI) des neu erstellten To-do-Elements fest.</span><span class="sxs-lookup"><span data-stu-id="8829e-272">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="8829e-273">Weitere Informationen finden Sie unter [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="8829e-273">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="8829e-274">Verweist auf die `GetTodoItem`-Aktion zum Erstellen des URIs des `Location`-Headers.</span><span class="sxs-lookup"><span data-stu-id="8829e-274">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="8829e-275">Das `nameof`-Schlüsselwort von C# wird verwendet, um eine Hartcodierung des Aktionsnamens im `CreatedAtAction`-Aufruf zu vermeiden.</span><span class="sxs-lookup"><span data-stu-id="8829e-275">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="8829e-276">Installieren von Postman</span><span class="sxs-lookup"><span data-stu-id="8829e-276">Install Postman</span></span>

<span data-ttu-id="8829e-277">Dieses Tutorial verwendet Postman zum Testen der Web-API.</span><span class="sxs-lookup"><span data-stu-id="8829e-277">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="8829e-278">Installieren Sie [Postman](https://www.getpostman.com/downloads/).</span><span class="sxs-lookup"><span data-stu-id="8829e-278">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="8829e-279">Starten Sie die Web-App.</span><span class="sxs-lookup"><span data-stu-id="8829e-279">Start the web app.</span></span>
* <span data-ttu-id="8829e-280">Starten Sie Postman.</span><span class="sxs-lookup"><span data-stu-id="8829e-280">Start Postman.</span></span>
* <span data-ttu-id="8829e-281">Deaktivieren Sie **SSL certificate verification** (Verifizierung des SSL-Zertifikats).</span><span class="sxs-lookup"><span data-stu-id="8829e-281">Disable **SSL certificate verification**</span></span>
  * <span data-ttu-id="8829e-282">Deaktivieren Sie auf der Registerkarte **General** (Allgemein) unter File > Settings (**Datei** > **Einstellungen**) **SSL certificate verification** (Verifizierung des SSL-Zertifikats).</span><span class="sxs-lookup"><span data-stu-id="8829e-282">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="8829e-283">Aktivieren Sie die Verifizierung des SSL-Zertifikats wieder, nachdem Sie den Controller getestet haben.</span><span class="sxs-lookup"><span data-stu-id="8829e-283">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="8829e-284">Testen von PostTodoItem mit Postman</span><span class="sxs-lookup"><span data-stu-id="8829e-284">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="8829e-285">Erstellen Sie eine neue Anforderung.</span><span class="sxs-lookup"><span data-stu-id="8829e-285">Create a new request.</span></span>
* <span data-ttu-id="8829e-286">Legen Sie als HTTP-Methode `POST` fest.</span><span class="sxs-lookup"><span data-stu-id="8829e-286">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="8829e-287">Legen Sie den URI auf `https://localhost:<port>/api/TodoItems` fest.</span><span class="sxs-lookup"><span data-stu-id="8829e-287">Set the URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="8829e-288">Beispiel: `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="8829e-288">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="8829e-289">Wählen Sie die Registerkarte **Body** (Text) aus.</span><span class="sxs-lookup"><span data-stu-id="8829e-289">Select the **Body** tab.</span></span>
* <span data-ttu-id="8829e-290">Wählen Sie das Optionsfeld **raw** (Unformatiert) aus.</span><span class="sxs-lookup"><span data-stu-id="8829e-290">Select the **raw** radio button.</span></span>
* <span data-ttu-id="8829e-291">Legen Sie den Typ auf **JSON (application/json)** fest.</span><span class="sxs-lookup"><span data-stu-id="8829e-291">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="8829e-292">Geben Sie für die Aufgabe den Anforderungstext im JSON-Format ein:</span><span class="sxs-lookup"><span data-stu-id="8829e-292">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="8829e-293">Wählen Sie **Send** (Senden) aus.</span><span class="sxs-lookup"><span data-stu-id="8829e-293">Select **Send**.</span></span>

  ![Postman mit erstellter Anforderung](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri-with-postman"></a><span data-ttu-id="8829e-295">Testen des Adressheader-URIs mit Postman</span><span class="sxs-lookup"><span data-stu-id="8829e-295">Test the location header URI with Postman</span></span>

* <span data-ttu-id="8829e-296">Wählen Sie auf der Registerkarte **Header** (Header) den Bereich **Response** (Antwort) aus.</span><span class="sxs-lookup"><span data-stu-id="8829e-296">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="8829e-297">Kopieren Sie den den Headerwert von **Location** (Speicherort):</span><span class="sxs-lookup"><span data-stu-id="8829e-297">Copy the **Location** header value:</span></span>

  ![Registerkarte „Header“ in der Postman-Konsole](first-web-api/_static/3/create.png)

* <span data-ttu-id="8829e-299">Legen Sie als HTTP-Methode `GET` fest.</span><span class="sxs-lookup"><span data-stu-id="8829e-299">Set the HTTP method to `GET`.</span></span>
* <span data-ttu-id="8829e-300">Legen Sie den URI auf `https://localhost:<port>/api/TodoItems/1` fest.</span><span class="sxs-lookup"><span data-stu-id="8829e-300">Set the URI to `https://localhost:<port>/api/TodoItems/1`.</span></span> <span data-ttu-id="8829e-301">Beispiel: `https://localhost:5001/api/TodoItems/1`.</span><span class="sxs-lookup"><span data-stu-id="8829e-301">For example, `https://localhost:5001/api/TodoItems/1`.</span></span>
* <span data-ttu-id="8829e-302">Wählen Sie **Send** (Senden) aus.</span><span class="sxs-lookup"><span data-stu-id="8829e-302">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="8829e-303">Überblick über die GET-Methoden</span><span class="sxs-lookup"><span data-stu-id="8829e-303">Examine the GET methods</span></span>

<span data-ttu-id="8829e-304">Diese Methoden implementieren zwei GET-Endpunkte:</span><span class="sxs-lookup"><span data-stu-id="8829e-304">These methods implement two GET endpoints:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="8829e-305">Testen Sie die App, indem Sie die beiden Endpunkte in einem Browser oder über Postman aufrufen.</span><span class="sxs-lookup"><span data-stu-id="8829e-305">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="8829e-306">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="8829e-306">For example:</span></span>

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

<span data-ttu-id="8829e-307">Durch einen Aufruf von `GetTodoItems` wird eine Antwort gesendet, die der folgenden ähnelt:</span><span class="sxs-lookup"><span data-stu-id="8829e-307">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="8829e-308">Testen von GET mit Postman</span><span class="sxs-lookup"><span data-stu-id="8829e-308">Test Get with Postman</span></span>

* <span data-ttu-id="8829e-309">Erstellen Sie eine neue Anforderung.</span><span class="sxs-lookup"><span data-stu-id="8829e-309">Create a new request.</span></span>
* <span data-ttu-id="8829e-310">Legen Sie die HTTP-Methode auf **GET** fest.</span><span class="sxs-lookup"><span data-stu-id="8829e-310">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="8829e-311">Legen Sie den Anforderungs-URI auf `https://localhost:<port>/api/TodoItems` fest.</span><span class="sxs-lookup"><span data-stu-id="8829e-311">Set the request URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="8829e-312">Beispielsweise `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="8829e-312">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="8829e-313">Wählen Sie in Postman **Two pane view** (Ansicht mit zwei Bereichen) aus.</span><span class="sxs-lookup"><span data-stu-id="8829e-313">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="8829e-314">Wählen Sie **Send** (Senden) aus.</span><span class="sxs-lookup"><span data-stu-id="8829e-314">Select **Send**.</span></span>

<span data-ttu-id="8829e-315">Diese App verwendet eine In-Memory-Datenbank.</span><span class="sxs-lookup"><span data-stu-id="8829e-315">This app uses an in-memory database.</span></span> <span data-ttu-id="8829e-316">Wenn die App angehalten und dann wieder gestartet wird, werden durch die vorherige GET-Anforderung keine Daten zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="8829e-316">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="8829e-317">Wenn keine Daten zurückgegeben werden, senden Sie mit [POST](#post) Daten an die App.</span><span class="sxs-lookup"><span data-stu-id="8829e-317">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="8829e-318">Routing und URL-Pfade</span><span class="sxs-lookup"><span data-stu-id="8829e-318">Routing and URL paths</span></span>

<span data-ttu-id="8829e-319">Das Attribut [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) gibt eine Methode an, die auf eine HTTP GET-Anforderung antwortet.</span><span class="sxs-lookup"><span data-stu-id="8829e-319">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="8829e-320">Der URL-Pfad für jede Methode wird wie folgt erstellt:</span><span class="sxs-lookup"><span data-stu-id="8829e-320">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="8829e-321">Beginnen Sie mit der Vorlagenzeichenfolge im `Route`-Attribut des Controllers:</span><span class="sxs-lookup"><span data-stu-id="8829e-321">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="8829e-322">Ersetzen Sie `[controller]` durch den Namen des Controllers, bei dem es sich standardmäßig um den Namen der Controller-Klasse ohne das Suffix „Controller“ handelt.</span><span class="sxs-lookup"><span data-stu-id="8829e-322">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="8829e-323">In diesem Beispiel ist der Klassenname des Controllers „**TodoItems**Controller“. Der Controllername lautet also „TodoItems“.</span><span class="sxs-lookup"><span data-stu-id="8829e-323">For this sample, the controller class name is **TodoItems**Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="8829e-324">Beim ASP.NET Core-[Routing](xref:mvc/controllers/routing) wird die Groß-/Kleinschreibung nicht beachtet.</span><span class="sxs-lookup"><span data-stu-id="8829e-324">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="8829e-325">Wenn das `[HttpGet]`-Attribut eine Routenvorlage (z. B. `[HttpGet("products")]`) hat, fügen Sie diese an den Pfad an.</span><span class="sxs-lookup"><span data-stu-id="8829e-325">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="8829e-326">In diesem Beispiel wird keine Vorlage verwendet.</span><span class="sxs-lookup"><span data-stu-id="8829e-326">This sample doesn't use a template.</span></span> <span data-ttu-id="8829e-327">Weitere Informationen finden Sie unter [Attributrouting mit Http[Verb]-Attributen](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="8829e-327">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="8829e-328">In der folgenden `GetTodoItem`-Methode ist `"{id}"` eine Platzhaltervariable für den eindeutigen Bezeichner des To-do-Elements.</span><span class="sxs-lookup"><span data-stu-id="8829e-328">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="8829e-329">Wenn `GetTodoItem` aufgerufen wird, wird der Wert von `"{id}"` in der URL der Methode in ihrem Parameter `id` bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="8829e-329">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="8829e-330">Rückgabewerte</span><span class="sxs-lookup"><span data-stu-id="8829e-330">Return values</span></span>

<span data-ttu-id="8829e-331">Der Rückgabetyp der Methoden `GetTodoItems` und `GetTodoItem` ist der [ActionResult\<T>-Typ](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="8829e-331">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="8829e-332">ASP.NET Core serialisiert automatisch das Objekt in [JSON](https://www.json.org/) und schreibt den JSON-Code in den Text der Antwortnachricht.</span><span class="sxs-lookup"><span data-stu-id="8829e-332">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="8829e-333">Der Antwortcode für diesen Rückgabetyp ist 200, vorausgesetzt, es gibt keine Ausnahmefehler.</span><span class="sxs-lookup"><span data-stu-id="8829e-333">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="8829e-334">Nicht behandelte Ausnahmen werden in 5xx-Fehler übersetzt.</span><span class="sxs-lookup"><span data-stu-id="8829e-334">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="8829e-335">`ActionResult`-Rückgabetypen können eine Vielzahl von HTTP-Statuscodes darstellen.</span><span class="sxs-lookup"><span data-stu-id="8829e-335">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="8829e-336">Beispielsweise kann `GetTodoItem` zwei verschiedene Statuswerte zurückgeben:</span><span class="sxs-lookup"><span data-stu-id="8829e-336">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="8829e-337">Wenn kein Element mit der angeforderten ID übereinstimmt, gibt die Methode einen 404-Fehlercode <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> zurück.</span><span class="sxs-lookup"><span data-stu-id="8829e-337">If no item matches the requested ID, the method returns a 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="8829e-338">Andernfalls gibt die Methode 200 mit einem JSON-Antworttext zurück.</span><span class="sxs-lookup"><span data-stu-id="8829e-338">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="8829e-339">Die Rückgabe von `item` löst eine HTTP 200-Antwort aus.</span><span class="sxs-lookup"><span data-stu-id="8829e-339">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="8829e-340">PutTodoItem-Methode</span><span class="sxs-lookup"><span data-stu-id="8829e-340">The PutTodoItem method</span></span>

<span data-ttu-id="8829e-341">Untersuchen Sie die `PutTodoItem`-Methode.</span><span class="sxs-lookup"><span data-stu-id="8829e-341">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="8829e-342">`PutTodoItem` ähnelt `PostTodoItem`, verwendet allerdings HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="8829e-342">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="8829e-343">Die Antwort ist [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="8829e-343">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="8829e-344">Gemäß der HTTP-Spezifikation erfordert eine PUT-Anforderung, dass der Client die gesamte aktualisierte Entität (nicht nur die Änderungen) sendet.</span><span class="sxs-lookup"><span data-stu-id="8829e-344">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="8829e-345">Verwenden Sie [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute), um Teilupdates zu unterstützen.</span><span class="sxs-lookup"><span data-stu-id="8829e-345">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="8829e-346">Wenn beim Aufrufen von `PutTodoItem` ein Fehler zurückgegeben wird, rufen Sie `GET` auf, um sicherzustellen, dass die Datenbank ein Element enthält.</span><span class="sxs-lookup"><span data-stu-id="8829e-346">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="8829e-347">Testen der PutTodoItem-Methode</span><span class="sxs-lookup"><span data-stu-id="8829e-347">Test the PutTodoItem method</span></span>

<span data-ttu-id="8829e-348">In diesem Beispiel wird eine In-Memory-Datenbank verwendet, die jedes Mal initialisiert werden muss, wenn die App gestartet wird.</span><span class="sxs-lookup"><span data-stu-id="8829e-348">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="8829e-349">Es muss ein Element in der Datenbank vorhanden sein, bevor Sie einen PUT-Aufruf durchführen.</span><span class="sxs-lookup"><span data-stu-id="8829e-349">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="8829e-350">Rufen Sie vor einem PUT-Aufruf GET auf, um sicherzustellen, dass ein Element in der Datenbank vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="8829e-350">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="8829e-351">Aktualisieren Sie das To-do-Element, das über den ID-Wert 1 verfügt, und legen Sie als Namen „feed fish“ fest:</span><span class="sxs-lookup"><span data-stu-id="8829e-351">Update the to-do item that has ID = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="8829e-352">Die folgende Abbildung zeigt das Postman-Update:</span><span class="sxs-lookup"><span data-stu-id="8829e-352">The following image shows the Postman update:</span></span>

![Postman-Konsole mit der Antwort „204 (Kein Inhalt)“](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="8829e-354">DeleteTodoItem-Methode</span><span class="sxs-lookup"><span data-stu-id="8829e-354">The DeleteTodoItem method</span></span>

<span data-ttu-id="8829e-355">Untersuchen Sie die `DeleteTodoItem`-Methode.</span><span class="sxs-lookup"><span data-stu-id="8829e-355">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="8829e-356">Testen der DeleteTodoItem-Methode</span><span class="sxs-lookup"><span data-stu-id="8829e-356">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="8829e-357">So löschen Sie mit Postman eine Aufgabe</span><span class="sxs-lookup"><span data-stu-id="8829e-357">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="8829e-358">Legen Sie die Methode auf `DELETE` fest.</span><span class="sxs-lookup"><span data-stu-id="8829e-358">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="8829e-359">Legen Sie den URI des zu löschenden Objekts fest, z. B. `https://localhost:5001/api/TodoItems/1`.</span><span class="sxs-lookup"><span data-stu-id="8829e-359">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="8829e-360">Wählen Sie **Send** (Senden) aus.</span><span class="sxs-lookup"><span data-stu-id="8829e-360">Select **Send**.</span></span>

<a name="over-post"></a>

## <a name="prevent-over-posting"></a><span data-ttu-id="8829e-361">Vermeiden von Overposting</span><span class="sxs-lookup"><span data-stu-id="8829e-361">Prevent over-posting</span></span>

<span data-ttu-id="8829e-362">Derzeit macht die Beispiel-App das gesamte `TodoItem`-Objekt verfügbar.</span><span class="sxs-lookup"><span data-stu-id="8829e-362">Currently the sample app exposes the entire `TodoItem` object.</span></span> <span data-ttu-id="8829e-363">In den Produktions-Apps sind die Daten, die eingegeben und mithilfe einer Teilmenge des Modells zurückgegeben werden, in der Regel eingeschränkt.</span><span class="sxs-lookup"><span data-stu-id="8829e-363">Production apps typically limit the data that's input and returned using a subset of the model.</span></span> <span data-ttu-id="8829e-364">Hierfür gibt es mehrere Gründe, wobei die Sicherheit einer der Hauptgründe ist.</span><span class="sxs-lookup"><span data-stu-id="8829e-364">There are multiple reasons behind this and security is a major one.</span></span> <span data-ttu-id="8829e-365">Die Teilmenge eines Modells wird üblicherweise als Datenübertragungsobjekt (DTO, Data Transfer Object), Eingabemodell oder Anzeigemodell bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="8829e-365">The subset of a model is usually referred to as a Data Transfer Object (DTO), input model, or view model.</span></span> <span data-ttu-id="8829e-366">In diesem Artikel wird das **DTO** verwendet.</span><span class="sxs-lookup"><span data-stu-id="8829e-366">**DTO** is used in this article.</span></span>

<span data-ttu-id="8829e-367">Ein DTO kann für Folgendes verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="8829e-367">A DTO may be used to:</span></span>

* <span data-ttu-id="8829e-368">Vermeiden Sie Overposting.</span><span class="sxs-lookup"><span data-stu-id="8829e-368">Prevent over-posting.</span></span>
* <span data-ttu-id="8829e-369">Ausblenden von Eigenschaften, die Clients nicht anzeigen sollen</span><span class="sxs-lookup"><span data-stu-id="8829e-369">Hide properties that clients are not supposed to view.</span></span>
* <span data-ttu-id="8829e-370">Auslassen einiger Eigenschaften, um die Nutzlast zu verringern</span><span class="sxs-lookup"><span data-stu-id="8829e-370">Omit some properties in order to reduce payload size.</span></span>
* <span data-ttu-id="8829e-371">Vereinfachen von Objektgraphen, die geschachtelte Objekte enthalten</span><span class="sxs-lookup"><span data-stu-id="8829e-371">Flatten object graphs that contain nested objects.</span></span> <span data-ttu-id="8829e-372">Vereinfachte Objektgraphen können für Clients zweckmäßiger sein.</span><span class="sxs-lookup"><span data-stu-id="8829e-372">Flattened object graphs can be more convenient for clients.</span></span>

<span data-ttu-id="8829e-373">Um den DTO-Ansatz zu veranschaulichen, aktualisieren Sie die `TodoItem`-Klasse, sodass sie ein geheimes Feld einschließt:</span><span class="sxs-lookup"><span data-stu-id="8829e-373">To demonstrate the DTO approach, update the `TodoItem` class to include a secret field:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItem.cs?name=snippet&highlight=6)]

<span data-ttu-id="8829e-374">Das geheime Feld muss in dieser App ausgeblendet werden, eine administrative App kann es jedoch verfügbar machen.</span><span class="sxs-lookup"><span data-stu-id="8829e-374">The secret field needs to be hidden from this app, but an administrative app could choose to expose it.</span></span>

<span data-ttu-id="8829e-375">Vergewissern Sie sich, dass Sie das geheime Feld veröffentlichen und abrufen können.</span><span class="sxs-lookup"><span data-stu-id="8829e-375">Verify you can post and get the secret field.</span></span>

<span data-ttu-id="8829e-376">Erstellen Sie ein DTO-Modell:</span><span class="sxs-lookup"><span data-stu-id="8829e-376">Create a DTO model:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItemDTO.cs?name=snippet)]

<span data-ttu-id="8829e-377">Aktualisieren Sie `TodoItemsController`, sodass `TodoItemDTO` verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="8829e-377">Update the `TodoItemsController` to use `TodoItemDTO`:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

<span data-ttu-id="8829e-378">Vergewissern Sie sich, dass Sie das geheime Feld weder veröffentlichen noch abrufen können.</span><span class="sxs-lookup"><span data-stu-id="8829e-378">Verify you can't post or get the secret field.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="8829e-379">Aufrufen der Web-API mit JavaScript</span><span class="sxs-lookup"><span data-stu-id="8829e-379">Call the web API with JavaScript</span></span>

<span data-ttu-id="8829e-380">Mehr dazu finden Sie im [-Tutorial: Aufrufen einer ASP.NET Core-Web-API mit JavaScript](xref:tutorials/web-api-javascript).</span><span class="sxs-lookup"><span data-stu-id="8829e-380">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="8829e-381">In diesem Tutorial lernen Sie, wie die folgenden Aufgaben ausgeführt werden:</span><span class="sxs-lookup"><span data-stu-id="8829e-381">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="8829e-382">Erstellen eines Web-API-Projekts</span><span class="sxs-lookup"><span data-stu-id="8829e-382">Create a web API project.</span></span>
> * <span data-ttu-id="8829e-383">Hinzufügen einer Modellklasse und eines Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="8829e-383">Add a model class and a database context.</span></span>
> * <span data-ttu-id="8829e-384">Hinzufügen eines Controllers</span><span class="sxs-lookup"><span data-stu-id="8829e-384">Add a controller.</span></span>
> * <span data-ttu-id="8829e-385">Hinzufügen von CRUD-Methoden</span><span class="sxs-lookup"><span data-stu-id="8829e-385">Add CRUD methods.</span></span>
> * <span data-ttu-id="8829e-386">Konfigurieren von Routing und URL-Pfaden</span><span class="sxs-lookup"><span data-stu-id="8829e-386">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="8829e-387">Angeben von Rückgabewerten</span><span class="sxs-lookup"><span data-stu-id="8829e-387">Specify return values.</span></span>
> * <span data-ttu-id="8829e-388">Aufrufen der Web-API mit Postman</span><span class="sxs-lookup"><span data-stu-id="8829e-388">Call the web API with Postman.</span></span>
> * <span data-ttu-id="8829e-389">Aufrufen der Web-API mit JavaScript</span><span class="sxs-lookup"><span data-stu-id="8829e-389">Call the web API with JavaScript.</span></span>

<span data-ttu-id="8829e-390">Am Ende haben Sie eine Web-API, die in einer relationalen Datenbank gespeicherte „Aufgaben“ verwalten kann.</span><span class="sxs-lookup"><span data-stu-id="8829e-390">At the end, you have a web API that can manage "to-do" items stored in a relational database.</span></span>

## <a name="overview"></a><span data-ttu-id="8829e-391">Übersicht</span><span class="sxs-lookup"><span data-stu-id="8829e-391">Overview</span></span>

<span data-ttu-id="8829e-392">In diesem Tutorial wird die folgende API erstellt:</span><span class="sxs-lookup"><span data-stu-id="8829e-392">This tutorial creates the following API:</span></span>

|<span data-ttu-id="8829e-393">API</span><span class="sxs-lookup"><span data-stu-id="8829e-393">API</span></span> | <span data-ttu-id="8829e-394">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="8829e-394">Description</span></span> | <span data-ttu-id="8829e-395">Anforderungstext</span><span class="sxs-lookup"><span data-stu-id="8829e-395">Request body</span></span> | <span data-ttu-id="8829e-396">Antworttext</span><span class="sxs-lookup"><span data-stu-id="8829e-396">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="8829e-397">GET /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="8829e-397">GET /api/TodoItems</span></span> | <span data-ttu-id="8829e-398">Alle To-do-Elemente abrufen</span><span class="sxs-lookup"><span data-stu-id="8829e-398">Get all to-do items</span></span> | <span data-ttu-id="8829e-399">Keine</span><span class="sxs-lookup"><span data-stu-id="8829e-399">None</span></span> | <span data-ttu-id="8829e-400">Array von To-do-Elementen</span><span class="sxs-lookup"><span data-stu-id="8829e-400">Array of to-do items</span></span>|
|<span data-ttu-id="8829e-401">GET /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="8829e-401">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="8829e-402">Ein Element nach ID abrufen</span><span class="sxs-lookup"><span data-stu-id="8829e-402">Get an item by ID</span></span> | <span data-ttu-id="8829e-403">Keine</span><span class="sxs-lookup"><span data-stu-id="8829e-403">None</span></span> | <span data-ttu-id="8829e-404">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="8829e-404">To-do item</span></span>|
|<span data-ttu-id="8829e-405">POST /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="8829e-405">POST /api/TodoItems</span></span> | <span data-ttu-id="8829e-406">Neues Element hinzufügen</span><span class="sxs-lookup"><span data-stu-id="8829e-406">Add a new item</span></span> | <span data-ttu-id="8829e-407">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="8829e-407">To-do item</span></span> | <span data-ttu-id="8829e-408">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="8829e-408">To-do item</span></span> |
|<span data-ttu-id="8829e-409">PUT /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="8829e-409">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="8829e-410">Vorhandenes Element aktualisieren &nbsp;</span><span class="sxs-lookup"><span data-stu-id="8829e-410">Update an existing item &nbsp;</span></span> | <span data-ttu-id="8829e-411">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="8829e-411">To-do item</span></span> | <span data-ttu-id="8829e-412">Keine</span><span class="sxs-lookup"><span data-stu-id="8829e-412">None</span></span> |
|<span data-ttu-id="8829e-413">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="8829e-413">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="8829e-414">Löschen eines Elements &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="8829e-414">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="8829e-415">Keine</span><span class="sxs-lookup"><span data-stu-id="8829e-415">None</span></span> | <span data-ttu-id="8829e-416">Keine</span><span class="sxs-lookup"><span data-stu-id="8829e-416">None</span></span>|

<span data-ttu-id="8829e-417">Das folgende Diagramm zeigt den Entwurf der App.</span><span class="sxs-lookup"><span data-stu-id="8829e-417">The following diagram shows the design of the app.</span></span>

![Der Client ist das Feld ganz links.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="8829e-423">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="8829e-423">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8829e-424">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8829e-424">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="8829e-425">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8829e-425">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="8829e-426">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="8829e-426">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="8829e-427">Erstellen eines Webprojekts</span><span class="sxs-lookup"><span data-stu-id="8829e-427">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8829e-428">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8829e-428">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="8829e-429">Klicken Sie im Menü **Datei** auf **Neu** > **Projekt**.</span><span class="sxs-lookup"><span data-stu-id="8829e-429">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="8829e-430">Wählen Sie die Vorlage **ASP.NET Core-Webanwendung** aus, und klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="8829e-430">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="8829e-431">Geben Sie dem Projekt den Namen *TodoApi*, und klicken Sie auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="8829e-431">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="8829e-432">Vergewissern Sie sich, dass im Dialogfeld **Neue ASP.NET Core-Webanwendung erstellen** die Optionen **.NET Core** und **ASP.NET Core 2.2** ausgewählt sind.</span><span class="sxs-lookup"><span data-stu-id="8829e-432">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 2.2** are selected.</span></span> <span data-ttu-id="8829e-433">Wählen Sie die Vorlage **API** aus, und klicken Sie auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="8829e-433">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="8829e-434">Wählen Sie **nicht** **Enable Docker Support** (Docker-Unterstützung aktivieren) aus.</span><span class="sxs-lookup"><span data-stu-id="8829e-434">**Don't** select **Enable Docker Support**.</span></span>

![VS-Dialogfeld „Neues Projekt“](first-web-api/_static/vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="8829e-436">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8829e-436">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="8829e-437">Öffnen Sie das [integrierte Terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="8829e-437">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="8829e-438">Wechseln Sie mit dem Befehl `cd`' zu dem Ordner, der den Projektordner enthalten soll.</span><span class="sxs-lookup"><span data-stu-id="8829e-438">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="8829e-439">Führen Sie die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="8829e-439">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  <span data-ttu-id="8829e-440">Diese Befehle erstellen ein neues Web-API-Projekt und öffnen eine neue Visual Studio Code-Instanz im neuen Projektordner.</span><span class="sxs-lookup"><span data-stu-id="8829e-440">These commands create a new web API project and open a new instance of Visual Studio Code in the new project folder.</span></span>

* <span data-ttu-id="8829e-441">Wenn Sie in einem Dialogfeld angeben müssen, ob Sie dem Projekt die erforderlichen Elemente hinzufügen möchten, wählen Sie **Ja** aus.</span><span class="sxs-lookup"><span data-stu-id="8829e-441">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="8829e-442">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="8829e-442">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="8829e-443">Klicken Sie auf **Datei** > **Neue Projektmappe**.</span><span class="sxs-lookup"><span data-stu-id="8829e-443">Select **File** > **New Solution**.</span></span>

  ![Neue Projektmappe in macOS](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="8829e-445">Navigieren Sie in Visual Studio für Mac-Versionen vor Version 8.6 zu **.NET Core** > **App** > **API** > **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="8829e-445">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="8829e-446">Bei Version 8.6 oder höher klicken Sie auf **Web and Console** > **App** > **API** > **Weiter** (Web und Konsole).</span><span class="sxs-lookup"><span data-stu-id="8829e-446">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>
  
* <span data-ttu-id="8829e-447">Wählen Sie im Dialogfeld **Neue ASP.NET Core-Web-API konfigurieren** die neueste .NET Core 2.x-Version als **Zielframework** aus.</span><span class="sxs-lookup"><span data-stu-id="8829e-447">In the **Configure the new ASP.NET Core Web API** dialog, select the latest .NET Core 2.x **Target Framework**.</span></span> <span data-ttu-id="8829e-448">Klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="8829e-448">Select **Next**.</span></span>

* <span data-ttu-id="8829e-449">Geben Sie für **Projektname** *TodoApi* ein, und wählen Sie dann **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="8829e-449">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![Dialogfeld „Konfiguration“](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api"></a><span data-ttu-id="8829e-451">Testen der API</span><span class="sxs-lookup"><span data-stu-id="8829e-451">Test the API</span></span>

<span data-ttu-id="8829e-452">Die Projektvorlage erstellt eine `values`-API.</span><span class="sxs-lookup"><span data-stu-id="8829e-452">The project template creates a `values` API.</span></span> <span data-ttu-id="8829e-453">Rufen Sie in einem Browser die `Get`-Methode zum Testen der App auf.</span><span class="sxs-lookup"><span data-stu-id="8829e-453">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8829e-454">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8829e-454">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="8829e-455">Drücken Sie STRG+F5, um die App auszuführen.</span><span class="sxs-lookup"><span data-stu-id="8829e-455">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="8829e-456">Visual Studio startet einen Browser und navigiert zu `https://localhost:<port>/api/values`, wobei es sich bei `<port>` um eine zufällig ausgewählte Portnummer handelt.</span><span class="sxs-lookup"><span data-stu-id="8829e-456">Visual Studio launches a browser and navigates to `https://localhost:<port>/api/values`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="8829e-457">Wenn Sie in einem Dialogfeld angeben müssen, ob Sie dem IIS Express-Zertifikat vertrauen, wählen Sie **Ja** aus.</span><span class="sxs-lookup"><span data-stu-id="8829e-457">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="8829e-458">Wählen Sie im folgenden Dialogfeld **Sicherheitswarnung** die Option **Ja** aus.</span><span class="sxs-lookup"><span data-stu-id="8829e-458">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="8829e-459">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8829e-459">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="8829e-460">Drücken Sie STRG+F5, um die App auszuführen.</span><span class="sxs-lookup"><span data-stu-id="8829e-460">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="8829e-461">Navigieren Sie in einem Browser zur folgenden URL: `https://localhost:5001/api/values`.</span><span class="sxs-lookup"><span data-stu-id="8829e-461">In a browser, go to following URL: `https://localhost:5001/api/values`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="8829e-462">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="8829e-462">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="8829e-463">Wählen Sie **Ausführen** > **Debugging starten** aus, um die App zu starten.</span><span class="sxs-lookup"><span data-stu-id="8829e-463">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="8829e-464">Visual Studio für Mac startet einen Browser und navigiert zu `https://localhost:<port>`, wobei es sich bei `<port>` um eine zufällig ausgewählte Portnummer handelt.</span><span class="sxs-lookup"><span data-stu-id="8829e-464">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="8829e-465">Der Fehler „HTTP 404: Nicht gefunden“ wird zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="8829e-465">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="8829e-466">Fügen Sie der URL `/api/values` an, d.h. ändern Sie die URL in `https://localhost:<port>/api/values`.</span><span class="sxs-lookup"><span data-stu-id="8829e-466">Append `/api/values` to the URL (change the URL to `https://localhost:<port>/api/values`).</span></span>

---

<span data-ttu-id="8829e-467">Die folgende JSON-Datei wird zurückgegeben:</span><span class="sxs-lookup"><span data-stu-id="8829e-467">The following JSON is returned:</span></span>

```json
["value1","value2"]
```

## <a name="add-a-model-class"></a><span data-ttu-id="8829e-468">Hinzufügen einer Modellklasse</span><span class="sxs-lookup"><span data-stu-id="8829e-468">Add a model class</span></span>

<span data-ttu-id="8829e-469">Ein *Modell* ist eine Gruppe von Klassen, die die Daten darstellen, die die App verwaltet.</span><span class="sxs-lookup"><span data-stu-id="8829e-469">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="8829e-470">Das Modell für diese App ist eine einzelne `TodoItem`-Klasse.</span><span class="sxs-lookup"><span data-stu-id="8829e-470">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8829e-471">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8829e-471">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="8829e-472">Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt.</span><span class="sxs-lookup"><span data-stu-id="8829e-472">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="8829e-473">Klicken Sie auf **Hinzufügen** > **Neuer Ordner**.</span><span class="sxs-lookup"><span data-stu-id="8829e-473">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="8829e-474">Geben Sie dem Ordner den Namen *Modelle*.</span><span class="sxs-lookup"><span data-stu-id="8829e-474">Name the folder *Models*.</span></span>

* <span data-ttu-id="8829e-475">Klicken Sie mit der rechten Maustaste auf den Ordner *Modelle*, und wählen Sie **Hinzufügen** > **Klasse** aus.</span><span class="sxs-lookup"><span data-stu-id="8829e-475">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="8829e-476">Nennen Sie die Klasse *TodoItem*, und wählen Sie **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="8829e-476">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="8829e-477">Ersetzen Sie den Vorlagencode durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="8829e-477">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="8829e-478">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8829e-478">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="8829e-479">Fügen Sie einen Ordner namens *Modelle* hinzu.</span><span class="sxs-lookup"><span data-stu-id="8829e-479">Add a folder named *Models*.</span></span>

* <span data-ttu-id="8829e-480">Fügen Sie dem Ordner *Modelle* mit dem folgenden Code eine `TodoItem`-Klasse hinzu:</span><span class="sxs-lookup"><span data-stu-id="8829e-480">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="8829e-481">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="8829e-481">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="8829e-482">Klicken Sie mit der rechten Maustaste auf das Projekt.</span><span class="sxs-lookup"><span data-stu-id="8829e-482">Right-click the project.</span></span> <span data-ttu-id="8829e-483">Klicken Sie auf **Hinzufügen** > **Neuer Ordner**.</span><span class="sxs-lookup"><span data-stu-id="8829e-483">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="8829e-484">Geben Sie dem Ordner den Namen *Modelle*.</span><span class="sxs-lookup"><span data-stu-id="8829e-484">Name the folder *Models*.</span></span>

  ![Neuer Ordner](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="8829e-486">Klicken Sie mit der rechten Maustaste auf den Ordner *Modelle*, und wählen Sie **Hinzufügen** > **Neue Datei** > **Allgemein** > **Leere Klasse** aus.</span><span class="sxs-lookup"><span data-stu-id="8829e-486">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="8829e-487">Nennen Sie die Klasse *TodoItem*, und klicken Sie dann auf **Neu**.</span><span class="sxs-lookup"><span data-stu-id="8829e-487">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="8829e-488">Ersetzen Sie den Vorlagencode durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="8829e-488">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="8829e-489">Die `Id`-Eigenschaft fungiert als eindeutiger Schlüssel in einer relationalen Datenbank.</span><span class="sxs-lookup"><span data-stu-id="8829e-489">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="8829e-490">Modellklassen können überall im Projekt platziert werden, doch gemäß der Konvention wird der Ordner *Modelle* verwendet.</span><span class="sxs-lookup"><span data-stu-id="8829e-490">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="8829e-491">Hinzufügen eines Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="8829e-491">Add a database context</span></span>

<span data-ttu-id="8829e-492">Der *Datenbankkontext* ist die Hauptklasse, die die Entity Framework-Funktionen für ein Datenmodell koordiniert.</span><span class="sxs-lookup"><span data-stu-id="8829e-492">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="8829e-493">Diese Klasse wird durch Ableiten von der `Microsoft.EntityFrameworkCore.DbContext`-Klasse erstellt.</span><span class="sxs-lookup"><span data-stu-id="8829e-493">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8829e-494">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8829e-494">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="8829e-495">Klicken Sie mit der rechten Maustaste auf den Ordner *Modelle*, und wählen Sie **Hinzufügen** > **Klasse** aus.</span><span class="sxs-lookup"><span data-stu-id="8829e-495">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="8829e-496">Nennen Sie die Klasse *TodoContext*, und klicken Sie auf **Hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="8829e-496">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="8829e-497">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="8829e-497">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="8829e-498">Fügen Sie eine `TodoContext`-Klasse zum Ordner *Modelle* hinzu.</span><span class="sxs-lookup"><span data-stu-id="8829e-498">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="8829e-499">Ersetzen Sie den Vorlagencode durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="8829e-499">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="8829e-500">Registrieren des Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="8829e-500">Register the database context</span></span>

<span data-ttu-id="8829e-501">In ASP.NET Core müssen Dienste wie der Datenbankkontext mit dem Container [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) registriert werden.</span><span class="sxs-lookup"><span data-stu-id="8829e-501">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="8829e-502">Der Container stellt den Dienst für Controller bereit.</span><span class="sxs-lookup"><span data-stu-id="8829e-502">The container provides the service to controllers.</span></span>

<span data-ttu-id="8829e-503">Aktualisieren Sie *Startup.cs* mit dem folgenden hervorgehobenen Code:</span><span class="sxs-lookup"><span data-stu-id="8829e-503">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

<span data-ttu-id="8829e-504">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="8829e-504">The preceding code:</span></span>

* <span data-ttu-id="8829e-505">Entfernt nicht benötigte `using`-Deklarationen</span><span class="sxs-lookup"><span data-stu-id="8829e-505">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="8829e-506">Fügt dem Abhängigkeitscontainer den Datenbankkontext hinzu</span><span class="sxs-lookup"><span data-stu-id="8829e-506">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="8829e-507">Gibt an, dass der Datenbankkontext eine In-Memory Database verwendet</span><span class="sxs-lookup"><span data-stu-id="8829e-507">Specifies that the database context will use an in-memory database.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="8829e-508">Hinzufügen eines Controllers</span><span class="sxs-lookup"><span data-stu-id="8829e-508">Add a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8829e-509">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8829e-509">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="8829e-510">Klicken Sie mit der rechten Maustaste auf den Ordner *Controller*.</span><span class="sxs-lookup"><span data-stu-id="8829e-510">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="8829e-511">Wählen Sie **Hinzufügen** > **Neues Element** aus.</span><span class="sxs-lookup"><span data-stu-id="8829e-511">Select **Add** > **New Item**.</span></span>
* <span data-ttu-id="8829e-512">Wählen Sie im Dialogfeld **Neues Element hinzufügen** die Vorlage **API-Controllerklasse** aus.</span><span class="sxs-lookup"><span data-stu-id="8829e-512">In the **Add New Item** dialog, select the **API Controller Class** template.</span></span>
* <span data-ttu-id="8829e-513">Benennen Sie die Klasse *TodoController*, und wählen Sie **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="8829e-513">Name the class *TodoController*, and select **Add**.</span></span>

  ![Dialogfeld „Neues Element hinzufügen“ mit Controller im Suchfeld und ausgewähltem Web-API-Controller](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="8829e-515">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="8829e-515">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="8829e-516">Erstellen Sie im Ordner *Controllers* eine Klasse namens `TodoController`.</span><span class="sxs-lookup"><span data-stu-id="8829e-516">In the *Controllers* folder, create a class named `TodoController`.</span></span>

---

* <span data-ttu-id="8829e-517">Ersetzen Sie den Vorlagencode durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="8829e-517">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

<span data-ttu-id="8829e-518">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="8829e-518">The preceding code:</span></span>

* <span data-ttu-id="8829e-519">Definiert eine API-Controllerklasse ohne Methoden.</span><span class="sxs-lookup"><span data-stu-id="8829e-519">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="8829e-520">Markiert die Klasse mit dem [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute)-Attribut.</span><span class="sxs-lookup"><span data-stu-id="8829e-520">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="8829e-521">Dieses Attribut gibt an, dass der Controller auf Web-API-Anforderungen reagiert.</span><span class="sxs-lookup"><span data-stu-id="8829e-521">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="8829e-522">Weitere Informationen zu bestimmten Verhaltensweisen, die das Attribut ermöglicht, finden Sie unter <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="8829e-522">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="8829e-523">Verwendet die Abhängigkeitsinjektion, um den Datenbankkontext (`TodoContext`) dem Controller hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="8829e-523">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="8829e-524">Der Datenbankkontext wird in den einzelnen [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete)-Methoden im Controller verwendet.</span><span class="sxs-lookup"><span data-stu-id="8829e-524">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>
* <span data-ttu-id="8829e-525">Fügt der Datenbank, falls sie leer ist, ein Element mit dem Namen `Item1` hinzu.</span><span class="sxs-lookup"><span data-stu-id="8829e-525">Adds an item named `Item1` to the database if the database is empty.</span></span> <span data-ttu-id="8829e-526">Dieser Code befindet sich im Konstruktor. Er wird bei jeder neuen HTTP-Anforderung ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="8829e-526">This code is in the constructor, so it runs every time there's a new HTTP request.</span></span> <span data-ttu-id="8829e-527">Wenn Sie alle Elemente löschen, erstellt der Konstruktor beim nächsten Aufruf einer API-Methode erneut `Item1`.</span><span class="sxs-lookup"><span data-stu-id="8829e-527">If you delete all items, the constructor creates `Item1` again the next time an API method is called.</span></span> <span data-ttu-id="8829e-528">So sieht es möglicherweise so aus, als sei die Löschung fehlgeschlagen, obwohl sie erfolgreich war.</span><span class="sxs-lookup"><span data-stu-id="8829e-528">So it may look like the deletion didn't work when it actually did work.</span></span>

## <a name="add-get-methods"></a><span data-ttu-id="8829e-529">Hinzufügen von GET-Methoden</span><span class="sxs-lookup"><span data-stu-id="8829e-529">Add Get methods</span></span>

<span data-ttu-id="8829e-530">Fügen Sie der Klasse `TodoController` die folgenden Methoden hinzu, um eine API bereitzustellen, die Aufgaben abruft:</span><span class="sxs-lookup"><span data-stu-id="8829e-530">To provide an API that retrieves to-do items, add the following methods to the `TodoController` class:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

<span data-ttu-id="8829e-531">Diese Methoden implementieren zwei GET-Endpunkte:</span><span class="sxs-lookup"><span data-stu-id="8829e-531">These methods implement two GET endpoints:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="8829e-532">Halten Sie die App an, falls diese noch ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="8829e-532">Stop the app if it's still running.</span></span> <span data-ttu-id="8829e-533">Führen Sie diese dann noch mal aus, damit die letzten Änderungen übernommen werden.</span><span class="sxs-lookup"><span data-stu-id="8829e-533">Then run it again to include the latest changes.</span></span>

<span data-ttu-id="8829e-534">Testen Sie die App, indem Sie die beiden Endpunkte in einem Browser aufrufen.</span><span class="sxs-lookup"><span data-stu-id="8829e-534">Test the app by calling the two endpoints from a browser.</span></span> <span data-ttu-id="8829e-535">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="8829e-535">For example:</span></span>

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

<span data-ttu-id="8829e-536">Die folgende HTTP-Antwort wird durch den Aufruf von `GetTodoItems` erzeugt:</span><span class="sxs-lookup"><span data-stu-id="8829e-536">The following HTTP response is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths"></a><span data-ttu-id="8829e-537">Routing und URL-Pfade</span><span class="sxs-lookup"><span data-stu-id="8829e-537">Routing and URL paths</span></span>

<span data-ttu-id="8829e-538">Das Attribut [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) gibt eine Methode an, die auf eine HTTP GET-Anforderung antwortet.</span><span class="sxs-lookup"><span data-stu-id="8829e-538">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="8829e-539">Der URL-Pfad für jede Methode wird wie folgt erstellt:</span><span class="sxs-lookup"><span data-stu-id="8829e-539">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="8829e-540">Beginnen Sie mit der Vorlagenzeichenfolge im `Route`-Attribut des Controllers:</span><span class="sxs-lookup"><span data-stu-id="8829e-540">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* <span data-ttu-id="8829e-541">Ersetzen Sie `[controller]` durch den Namen des Controllers, bei dem es sich standardmäßig um den Namen der Controller-Klasse ohne das Suffix „Controller“ handelt.</span><span class="sxs-lookup"><span data-stu-id="8829e-541">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="8829e-542">Bei diesem Beispiel ist der Klassenname des Controllers „**Todo**Controller“, d.h. der Controllername lautet „todo“.</span><span class="sxs-lookup"><span data-stu-id="8829e-542">For this sample, the controller class name is **Todo**Controller, so the controller name is "todo".</span></span> <span data-ttu-id="8829e-543">Beim ASP.NET Core-[Routing](xref:mvc/controllers/routing) wird die Groß-/Kleinschreibung nicht beachtet.</span><span class="sxs-lookup"><span data-stu-id="8829e-543">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="8829e-544">Wenn das `[HttpGet]`-Attribut eine Routenvorlage (z. B. `[HttpGet("products")]`) hat, fügen Sie diese an den Pfad an.</span><span class="sxs-lookup"><span data-stu-id="8829e-544">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="8829e-545">In diesem Beispiel wird keine Vorlage verwendet.</span><span class="sxs-lookup"><span data-stu-id="8829e-545">This sample doesn't use a template.</span></span> <span data-ttu-id="8829e-546">Weitere Informationen finden Sie unter [Attributrouting mit Http[Verb]-Attributen](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="8829e-546">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="8829e-547">In der folgenden `GetTodoItem`-Methode ist `"{id}"` eine Platzhaltervariable für den eindeutigen Bezeichner des To-do-Elements.</span><span class="sxs-lookup"><span data-stu-id="8829e-547">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="8829e-548">Wenn `GetTodoItem` aufgerufen wird, wird der Wert von `"{id}"` in der URL der Methode in ihrem Parameter `id` bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="8829e-548">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="8829e-549">Rückgabewerte</span><span class="sxs-lookup"><span data-stu-id="8829e-549">Return values</span></span>

<span data-ttu-id="8829e-550">Der Rückgabetyp der Methoden `GetTodoItems` und `GetTodoItem` ist der [ActionResult\<T>-Typ](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="8829e-550">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="8829e-551">ASP.NET Core serialisiert automatisch das Objekt in [JSON](https://www.json.org/) und schreibt den JSON-Code in den Text der Antwortnachricht.</span><span class="sxs-lookup"><span data-stu-id="8829e-551">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="8829e-552">Der Antwortcode für diesen Rückgabetyp ist 200, vorausgesetzt, es gibt keine Ausnahmefehler.</span><span class="sxs-lookup"><span data-stu-id="8829e-552">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="8829e-553">Nicht behandelte Ausnahmen werden in 5xx-Fehler übersetzt.</span><span class="sxs-lookup"><span data-stu-id="8829e-553">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="8829e-554">`ActionResult`-Rückgabetypen können eine Vielzahl von HTTP-Statuscodes darstellen.</span><span class="sxs-lookup"><span data-stu-id="8829e-554">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="8829e-555">Beispielsweise kann `GetTodoItem` zwei verschiedene Statuswerte zurückgeben:</span><span class="sxs-lookup"><span data-stu-id="8829e-555">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="8829e-556">Wenn kein Element mit der angeforderten ID übereinstimmt, gibt die Methode einen 404-Fehlercode <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> zurück.</span><span class="sxs-lookup"><span data-stu-id="8829e-556">If no item matches the requested ID, the method returns a 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="8829e-557">Andernfalls gibt die Methode 200 mit einem JSON-Antworttext zurück.</span><span class="sxs-lookup"><span data-stu-id="8829e-557">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="8829e-558">Die Rückgabe von `item` löst eine HTTP 200-Antwort aus.</span><span class="sxs-lookup"><span data-stu-id="8829e-558">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="test-the-gettodoitems-method"></a><span data-ttu-id="8829e-559">Testen der GetTodoItems-Methode</span><span class="sxs-lookup"><span data-stu-id="8829e-559">Test the GetTodoItems method</span></span>

<span data-ttu-id="8829e-560">Dieses Tutorial verwendet Postman zum Testen der Web-API.</span><span class="sxs-lookup"><span data-stu-id="8829e-560">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="8829e-561">Installieren Sie [Postman](https://www.getpostman.com/downloads/).</span><span class="sxs-lookup"><span data-stu-id="8829e-561">Install [Postman](https://www.getpostman.com/downloads/).</span></span>
* <span data-ttu-id="8829e-562">Starten Sie die Web-App.</span><span class="sxs-lookup"><span data-stu-id="8829e-562">Start the web app.</span></span>
* <span data-ttu-id="8829e-563">Starten Sie Postman.</span><span class="sxs-lookup"><span data-stu-id="8829e-563">Start Postman.</span></span>
* <span data-ttu-id="8829e-564">Deaktivieren Sie **SSL certificate verification** (Verifizierung des SSL-Zertifikats).</span><span class="sxs-lookup"><span data-stu-id="8829e-564">Disable **SSL certificate verification**.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8829e-565">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8829e-565">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="8829e-566">Deaktivieren Sie auf der Registerkarte **General** (Allgemein) unter File > Settings (**Datei** > **Einstellungen**) **SSL certificate verification** (Verifizierung des SSL-Zertifikats).</span><span class="sxs-lookup"><span data-stu-id="8829e-566">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="8829e-567">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="8829e-567">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="8829e-568">Deaktivieren Sie auf der Registerkarte **General** (Allgemein) unter **Postman** > **Preferences** (Postman > Einstellungen) **SSL certificate verification** (Verifizierung des SSL-Zertifikats).</span><span class="sxs-lookup"><span data-stu-id="8829e-568">From **Postman** > **Preferences** (**General** tab), disable **SSL certificate verification**.</span></span> <span data-ttu-id="8829e-569">Wählen Sie alternativ den Schraubenschlüssel und **Settings** (Einstellungen) aus, und deaktivieren Sie dann die Überprüfung des SSL-Zertifikats.</span><span class="sxs-lookup"><span data-stu-id="8829e-569">Alternatively, select the wrench and select **Settings**, then disable the SSL certificate verification.</span></span>

---
  
> [!WARNING]
> <span data-ttu-id="8829e-570">Aktivieren Sie die Verifizierung des SSL-Zertifikats wieder, nachdem Sie den Controller getestet haben.</span><span class="sxs-lookup"><span data-stu-id="8829e-570">Re-enable SSL certificate verification after testing the controller.</span></span>

* <span data-ttu-id="8829e-571">Erstellen Sie eine neue Anforderung.</span><span class="sxs-lookup"><span data-stu-id="8829e-571">Create a new request.</span></span>
  * <span data-ttu-id="8829e-572">Legen Sie die HTTP-Methode auf **GET** fest.</span><span class="sxs-lookup"><span data-stu-id="8829e-572">Set the HTTP method to **GET**.</span></span>
  * <span data-ttu-id="8829e-573">Legen Sie den Anforderungs-URI auf `https://localhost:<port>/api/todo` fest.</span><span class="sxs-lookup"><span data-stu-id="8829e-573">Set the request URI to `https://localhost:<port>/api/todo`.</span></span> <span data-ttu-id="8829e-574">Beispielsweise `https://localhost:5001/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="8829e-574">For example, `https://localhost:5001/api/todo`.</span></span>
* <span data-ttu-id="8829e-575">Wählen Sie in Postman **Two pane view** (Ansicht mit zwei Bereichen) aus.</span><span class="sxs-lookup"><span data-stu-id="8829e-575">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="8829e-576">Wählen Sie **Send** (Senden) aus.</span><span class="sxs-lookup"><span data-stu-id="8829e-576">Select **Send**.</span></span>

![Postman mit GET-Anforderung](first-web-api/_static/2pv.png)

## <a name="add-a-create-method"></a><span data-ttu-id="8829e-578">Hinzufügen einer Methode</span><span class="sxs-lookup"><span data-stu-id="8829e-578">Add a Create method</span></span>

<span data-ttu-id="8829e-579">Fügen Sie die folgende `PostTodoItem`-Methode in *Controllers/TodoController.cs* hinzu:</span><span class="sxs-lookup"><span data-stu-id="8829e-579">Add the following `PostTodoItem` method inside of *Controllers/TodoController.cs*:</span></span> 

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="8829e-580">Der oben stehende Code ist eine HTTP POST-Methode, wie durch das [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute)-Attribut angegeben.</span><span class="sxs-lookup"><span data-stu-id="8829e-580">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="8829e-581">Die Methode ruft den Wert der Aufgabe aus dem Text der HTTP-Anforderung ab.</span><span class="sxs-lookup"><span data-stu-id="8829e-581">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="8829e-582">Die `CreatedAtAction`-Methode:</span><span class="sxs-lookup"><span data-stu-id="8829e-582">The `CreatedAtAction` method:</span></span>

* <span data-ttu-id="8829e-583">Gibt bei einer erfolgreichen Anforderung den Statuscode „HTTP 201“ zurück.</span><span class="sxs-lookup"><span data-stu-id="8829e-583">Returns an HTTP 201 status code, if successful.</span></span> <span data-ttu-id="8829e-584">HTTP 201 ist die Standardantwort für eine HTTP POST-Methode, die eine neue Ressource auf dem Server erstellt.</span><span class="sxs-lookup"><span data-stu-id="8829e-584">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="8829e-585">Fügt der Antwort einen `Location`-Header hinzu.</span><span class="sxs-lookup"><span data-stu-id="8829e-585">Adds a `Location` header to the response.</span></span> <span data-ttu-id="8829e-586">Der `Location`-Header gibt den URI des neu erstellten To-Do-Elements zurück.</span><span class="sxs-lookup"><span data-stu-id="8829e-586">The `Location` header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="8829e-587">Weitere Informationen finden Sie unter [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="8829e-587">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="8829e-588">Verweist auf die `GetTodoItem`-Aktion zum Erstellen des URIs des `Location`-Headers.</span><span class="sxs-lookup"><span data-stu-id="8829e-588">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="8829e-589">Das `nameof`-Schlüsselwort von C# wird verwendet, um eine Hartcodierung des Aktionsnamens im `CreatedAtAction`-Aufruf zu vermeiden.</span><span class="sxs-lookup"><span data-stu-id="8829e-589">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method"></a><span data-ttu-id="8829e-590">Testen der PostTodoItem-Methode</span><span class="sxs-lookup"><span data-stu-id="8829e-590">Test the PostTodoItem method</span></span>

* <span data-ttu-id="8829e-591">Erstellen Sie das Projekt.</span><span class="sxs-lookup"><span data-stu-id="8829e-591">Build the project.</span></span>
* <span data-ttu-id="8829e-592">Legen Sie in Postman die HTTP-Methode auf `POST` fest.</span><span class="sxs-lookup"><span data-stu-id="8829e-592">In Postman, set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="8829e-593">Legen Sie den URI auf `https://localhost:<port>/api/TodoItem` fest.</span><span class="sxs-lookup"><span data-stu-id="8829e-593">Set the URI to `https://localhost:<port>/api/TodoItem`.</span></span> <span data-ttu-id="8829e-594">Beispiel: `https://localhost:5001/api/TodoItem`.</span><span class="sxs-lookup"><span data-stu-id="8829e-594">For example, `https://localhost:5001/api/TodoItem`.</span></span>
* <span data-ttu-id="8829e-595">Wählen Sie die Registerkarte **Body** (Text) aus.</span><span class="sxs-lookup"><span data-stu-id="8829e-595">Select the **Body** tab.</span></span>
* <span data-ttu-id="8829e-596">Wählen Sie das Optionsfeld **raw** (Unformatiert) aus.</span><span class="sxs-lookup"><span data-stu-id="8829e-596">Select the **raw** radio button.</span></span>
* <span data-ttu-id="8829e-597">Legen Sie den Typ auf **JSON (application/json)** fest.</span><span class="sxs-lookup"><span data-stu-id="8829e-597">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="8829e-598">Geben Sie für die Aufgabe den Anforderungstext im JSON-Format ein:</span><span class="sxs-lookup"><span data-stu-id="8829e-598">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="8829e-599">Wählen Sie **Send** (Senden) aus.</span><span class="sxs-lookup"><span data-stu-id="8829e-599">Select **Send**.</span></span>

  ![Postman mit erstellter Anforderung](first-web-api/_static/create.png)

  <span data-ttu-id="8829e-601">Wenn Sie die Fehlermeldung „405: Methode nicht zulässig“ erhalten, wurde das Projekt wahrscheinlich nicht kompiliert, nachdem die Methode `PostTodoItem` hinzugefügt wurde.</span><span class="sxs-lookup"><span data-stu-id="8829e-601">If you get a 405 Method Not Allowed error, it's probably the result of not compiling the project after adding the `PostTodoItem` method.</span></span>

### <a name="test-the-location-header-uri"></a><span data-ttu-id="8829e-602">Testen des Adressheader-URIs</span><span class="sxs-lookup"><span data-stu-id="8829e-602">Test the location header URI</span></span>

* <span data-ttu-id="8829e-603">Wählen Sie auf der Registerkarte **Header** (Header) den Bereich **Response** (Antwort) aus.</span><span class="sxs-lookup"><span data-stu-id="8829e-603">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="8829e-604">Kopieren Sie den den Headerwert von **Location** (Speicherort):</span><span class="sxs-lookup"><span data-stu-id="8829e-604">Copy the **Location** header value:</span></span>

  ![Registerkarte „Header“ in der Postman-Konsole](first-web-api/_static/pmc2.png)

* <span data-ttu-id="8829e-606">Legen Sie die Methode auf „GET“ fest.</span><span class="sxs-lookup"><span data-stu-id="8829e-606">Set the method to GET.</span></span>
<span data-ttu-id="8829e-607">\*Legen Sie den URI auf  `https://localhost:<port>/api/TodoItems/2` fest.</span><span class="sxs-lookup"><span data-stu-id="8829e-607">\* Set the URI to `https://localhost:<port>/api/TodoItems/2`.</span></span><span data-ttu-id="8829e-608"> Beispiel:  `https://localhost:5001/api/TodoItems/2`.</span><span class="sxs-lookup"><span data-stu-id="8829e-608"> For example, `https://localhost:5001/api/TodoItems/2`.</span></span>
* <span data-ttu-id="8829e-609">Wählen Sie **Send** (Senden) aus.</span><span class="sxs-lookup"><span data-stu-id="8829e-609">Select **Send**.</span></span>

## <a name="add-a-puttodoitem-method"></a><span data-ttu-id="8829e-610">Hinzufügen einer PutTodoItem-Methode</span><span class="sxs-lookup"><span data-stu-id="8829e-610">Add a PutTodoItem method</span></span>

<span data-ttu-id="8829e-611">Fügen Sie die folgende `PutTodoItem`-Methode hinzu:</span><span class="sxs-lookup"><span data-stu-id="8829e-611">Add the following `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="8829e-612">`PutTodoItem` ähnelt `PostTodoItem`, verwendet allerdings HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="8829e-612">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="8829e-613">Die Antwort ist [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="8829e-613">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="8829e-614">Gemäß der HTTP-Spezifikation erfordert eine PUT-Anforderung, dass der Client die gesamte aktualisierte Entität (nicht nur die Änderungen) sendet.</span><span class="sxs-lookup"><span data-stu-id="8829e-614">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="8829e-615">Verwenden Sie [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute), um Teilupdates zu unterstützen.</span><span class="sxs-lookup"><span data-stu-id="8829e-615">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="8829e-616">Wenn beim Aufrufen von `PutTodoItem` ein Fehler zurückgegeben wird, rufen Sie `GET` auf, um sicherzustellen, dass die Datenbank ein Element enthält.</span><span class="sxs-lookup"><span data-stu-id="8829e-616">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="8829e-617">Testen der PutTodoItem-Methode</span><span class="sxs-lookup"><span data-stu-id="8829e-617">Test the PutTodoItem method</span></span>

<span data-ttu-id="8829e-618">In diesem Beispiel wird eine In-Memory-Datenbank verwendet, die jedes Mal initialisiert werden muss, wenn die App gestartet wird.</span><span class="sxs-lookup"><span data-stu-id="8829e-618">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="8829e-619">Es muss ein Element in der Datenbank vorhanden sein, bevor Sie einen PUT-Aufruf durchführen.</span><span class="sxs-lookup"><span data-stu-id="8829e-619">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="8829e-620">Rufen Sie vor einem PUT-Aufruf GET auf, um sicherzustellen, dass ein Element in der Datenbank vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="8829e-620">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="8829e-621">Aktualisieren Sie die Aufgabe mit der ID = 1, und setzen Sie ihren Namen auf „feed fish“:</span><span class="sxs-lookup"><span data-stu-id="8829e-621">Update the to-do item that has id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="8829e-622">Die folgende Abbildung zeigt das Postman-Update:</span><span class="sxs-lookup"><span data-stu-id="8829e-622">The following image shows the Postman update:</span></span>

![Postman-Konsole mit der Antwort „204 (Kein Inhalt)“](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method"></a><span data-ttu-id="8829e-624">Hinzufügen einer DeleteTodoItem-Methode</span><span class="sxs-lookup"><span data-stu-id="8829e-624">Add a DeleteTodoItem method</span></span>

<span data-ttu-id="8829e-625">Fügen Sie die folgende `DeleteTodoItem`-Methode hinzu:</span><span class="sxs-lookup"><span data-stu-id="8829e-625">Add the following `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="8829e-626">Die `DeleteTodoItem`-Antwort lautet [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="8829e-626">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="8829e-627">Testen der DeleteTodoItem-Methode</span><span class="sxs-lookup"><span data-stu-id="8829e-627">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="8829e-628">So löschen Sie mit Postman eine Aufgabe</span><span class="sxs-lookup"><span data-stu-id="8829e-628">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="8829e-629">Legen Sie die Methode auf `DELETE` fest.</span><span class="sxs-lookup"><span data-stu-id="8829e-629">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="8829e-630">Legen Sie den URI des zu löschenden Objekts fest, z. B. `https://localhost:5001/api/todo/1`.</span><span class="sxs-lookup"><span data-stu-id="8829e-630">Set the URI of the object to delete (for example, `https://localhost:5001/api/todo/1`).</span></span>
* <span data-ttu-id="8829e-631">Wählen Sie **Send** (Senden) aus.</span><span class="sxs-lookup"><span data-stu-id="8829e-631">Select **Send**.</span></span>

<span data-ttu-id="8829e-632">Sie können in der Beispiel-App alle Elemente löschen.</span><span class="sxs-lookup"><span data-stu-id="8829e-632">The sample app allows you to delete all the items.</span></span> <span data-ttu-id="8829e-633">Sobald das letzte Element gelöscht wurde, wird allerdings beim nächsten API-Aufruf vom Modellklassenkonstruktor ein neues erstellt.</span><span class="sxs-lookup"><span data-stu-id="8829e-633">However, when the last item is deleted, a new one is created by the model class constructor the next time the API is called.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="8829e-634">Aufrufen der Web-API mit JavaScript</span><span class="sxs-lookup"><span data-stu-id="8829e-634">Call the web API with JavaScript</span></span>

<span data-ttu-id="8829e-635">In diesem Abschnitt wird eine HTML-Seite hinzugefügt, die mithilfe von JavaScript die Web-API aufruft.</span><span class="sxs-lookup"><span data-stu-id="8829e-635">In this section, an HTML page is added that uses JavaScript to call the web API.</span></span> <span data-ttu-id="8829e-636">jQuery initiiert die Anforderung.</span><span class="sxs-lookup"><span data-stu-id="8829e-636">jQuery initiates the request.</span></span> <span data-ttu-id="8829e-637">JavaScript aktualisiert die Seite mit den Details aus der Antwort der Web-API.</span><span class="sxs-lookup"><span data-stu-id="8829e-637">JavaScript updates the page with the details from the web API's response.</span></span>

<span data-ttu-id="8829e-638">Konfigurieren Sie die App so, dass sie [statische Dateien bereitstellt](xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A), und [aktivieren Sie die Standarddateizuordnung](xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A), indem Sie *startup.cs* mit dem unten markierten Code aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="8829e-638">Configure the app to [serve static files](xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A) and [enable default file mapping](xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A) by updating *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

<span data-ttu-id="8829e-639">Erstellen Sie im Projektverzeichnis den Ordner *wwwwroot*.</span><span class="sxs-lookup"><span data-stu-id="8829e-639">Create a *wwwroot* folder in the project directory.</span></span>

<span data-ttu-id="8829e-640">Fügen Sie dem Verzeichnis *wwwroot* eine HTML-Datei namens *index.html* hinzu.</span><span class="sxs-lookup"><span data-stu-id="8829e-640">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="8829e-641">Ersetzen Sie den Inhalt durch folgendes Markup:</span><span class="sxs-lookup"><span data-stu-id="8829e-641">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

<span data-ttu-id="8829e-642">Fügen Sie dem Verzeichnis *wwwroot* eine JavaScript-Datei namens *site.js* hinzu.</span><span class="sxs-lookup"><span data-stu-id="8829e-642">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="8829e-643">Ersetzen Sie den Inhalt durch folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="8829e-643">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="8829e-644">Möglicherweise ist eine Änderung an den Starteinstellungen des ASP.NET Core-Projekts erforderlich, um die HTML-Seite lokal zu testen:</span><span class="sxs-lookup"><span data-stu-id="8829e-644">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="8829e-645">Öffnen Sie *Properties\launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="8829e-645">Open *Properties\launchSettings.json*.</span></span>
* <span data-ttu-id="8829e-646">Entfernen Sie die `launchUrl`-Eigenschaft, um zu erzwingen, dass die App mit *index.html* als Startseite geöffnet wird. Dies ist die Standarddatei des Projekts.</span><span class="sxs-lookup"><span data-stu-id="8829e-646">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="8829e-647">Dieses Beispiel ruft alle CRUD-Methoden der Web-API auf.</span><span class="sxs-lookup"><span data-stu-id="8829e-647">This sample calls all of the CRUD methods of the web API.</span></span> <span data-ttu-id="8829e-648">Im Folgenden werden die API-Aufrufe erläutert.</span><span class="sxs-lookup"><span data-stu-id="8829e-648">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items"></a><span data-ttu-id="8829e-649">Abrufen einer Liste von To-Do-Elementen</span><span class="sxs-lookup"><span data-stu-id="8829e-649">Get a list of to-do items</span></span>

<span data-ttu-id="8829e-650">jQuery sendet eine HTTP GET-Anforderung an die Web-API, die JSON-Code mit einem Array aus To-Do-Elementen zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="8829e-650">jQuery sends an HTTP GET request to the web API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="8829e-651">Die Rückruffunktion `success` wird aufgerufen, wenn die Anforderung erfolgreich ist.</span><span class="sxs-lookup"><span data-stu-id="8829e-651">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="8829e-652">Im Rückruf wird DOM mit den To-Do-Informationen aktualisiert.</span><span class="sxs-lookup"><span data-stu-id="8829e-652">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item"></a><span data-ttu-id="8829e-653">Hinzufügen eines To-Do-Elements</span><span class="sxs-lookup"><span data-stu-id="8829e-653">Add a to-do item</span></span>

<span data-ttu-id="8829e-654">jQuery sendet eine HTTP POST-Anforderung mit dem To-Do-Element im Anforderungstext.</span><span class="sxs-lookup"><span data-stu-id="8829e-654">jQuery sends an HTTP POST request with the to-do item in the request body.</span></span> <span data-ttu-id="8829e-655">Die Optionen `accepts` und `contentType` werden auf `application/json` festgelegt, um den gesendeten und empfangenen Medientyp anzugeben.</span><span class="sxs-lookup"><span data-stu-id="8829e-655">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="8829e-656">Die Aufgabe wird mit [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify) in JSON konvertiert.</span><span class="sxs-lookup"><span data-stu-id="8829e-656">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="8829e-657">Wenn die API den Statuscode „Erfolgreich“ zurückgibt, wird die Funktion `getData` aufgerufen, um die HTML-Tabelle zu aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="8829e-657">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item"></a><span data-ttu-id="8829e-658">Aktualisieren eines To-Do-Elements</span><span class="sxs-lookup"><span data-stu-id="8829e-658">Update a to-do item</span></span>

<span data-ttu-id="8829e-659">Das Aktualisieren einer Aufgabe ist vergleichbar mit dem Hinzufügen einer Aufgabe.</span><span class="sxs-lookup"><span data-stu-id="8829e-659">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="8829e-660">Die `url` ändert sich, um den eindeutigen Bezeichner des Elements hinzuzufügen. Der `type` lautet `PUT`.</span><span class="sxs-lookup"><span data-stu-id="8829e-660">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item"></a><span data-ttu-id="8829e-661">Löschen eines To-Do-Elements</span><span class="sxs-lookup"><span data-stu-id="8829e-661">Delete a to-do item</span></span>

<span data-ttu-id="8829e-662">Eine Aufgabe wird folgendermaßen gelöscht: im AJAX-Aufruf wird `type` auf `DELETE` festgelegt, und der eindeutige Bezeichner des Elements wird in der URL angegeben.</span><span class="sxs-lookup"><span data-stu-id="8829e-662">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

<a name="auth"></a>

## <a name="add-authentication-support-to-a-web-api"></a><span data-ttu-id="8829e-663">Hinzufügen der Authentifizierungsunterstützung zu einer Web-API</span><span class="sxs-lookup"><span data-stu-id="8829e-663">Add authentication support to a web API</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="additional-resources"></a><span data-ttu-id="8829e-664">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="8829e-664">Additional resources</span></span>

<span data-ttu-id="8829e-665">[Sie können den Beispielcode für dieses Tutorial anzeigen oder herunterladen.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples)</span><span class="sxs-lookup"><span data-stu-id="8829e-665">[View or download sample code for this tutorial](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span></span> <span data-ttu-id="8829e-666">[Informationen zum Herunterladen](xref:index#how-to-download-a-sample) finden Sie hier.</span><span class="sxs-lookup"><span data-stu-id="8829e-666">See [how to download](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="8829e-667">Weitere Informationen finden Sie in den folgenden Ressourcen:</span><span class="sxs-lookup"><span data-stu-id="8829e-667">For more information, see the following resources:</span></span>

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/intro>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [<span data-ttu-id="8829e-668">Dieses Tutorial auf YouTube</span><span class="sxs-lookup"><span data-stu-id="8829e-668">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=TTkhEyGBfAk)
