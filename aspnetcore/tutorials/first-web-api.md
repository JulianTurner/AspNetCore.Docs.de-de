---
title: 'Tutorial: Erstellen einer Web-API mit ASP.NET Core'
author: rick-anderson
description: Informationen zum Erstellen einer Web-API mit ASP.NET Core.
ms.author: riande
ms.custom: mvc, devx-track-js
ms.date: 08/13/2020
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
- Models
uid: tutorials/first-web-api
ms.openlocfilehash: ccbfc27eb89e23938a69f0ab4cb306d6a4136889
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/04/2021
ms.locfileid: "96175051"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a><span data-ttu-id="c1854-103">Tutorial: Erstellen einer Web-API mit ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c1854-103">Tutorial: Create a web API with ASP.NET Core</span></span>

<span data-ttu-id="c1854-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5) und [Mike Wasson](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="c1854-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="c1854-105">In diesem Tutorial lernen Sie die Grundlagen der Erstellung einer Web-API mit ASP.NET Core kennen.</span><span class="sxs-lookup"><span data-stu-id="c1854-105">This tutorial teaches the basics of building a web API with ASP.NET Core.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="c1854-106">In diesem Tutorial lernen Sie, wie die folgenden Aufgaben ausgeführt werden:</span><span class="sxs-lookup"><span data-stu-id="c1854-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="c1854-107">Erstellen eines Web-API-Projekts</span><span class="sxs-lookup"><span data-stu-id="c1854-107">Create a web API project.</span></span>
> * <span data-ttu-id="c1854-108">Hinzufügen einer Modellklasse und eines Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="c1854-108">Add a model class and a database context.</span></span>
> * <span data-ttu-id="c1854-109">Erstellen eines Controllergerüsts mit CRUD-Methoden</span><span class="sxs-lookup"><span data-stu-id="c1854-109">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="c1854-110">Konfigurieren des Routings, der URL-Pfade und der Rückgabewerte</span><span class="sxs-lookup"><span data-stu-id="c1854-110">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="c1854-111">Aufrufen der Web-API mit Postman</span><span class="sxs-lookup"><span data-stu-id="c1854-111">Call the web API with Postman.</span></span>

<span data-ttu-id="c1854-112">Abschließend steht Ihnen eine Web-API zur Verfügung, die in einer relationalen Datenbank gespeicherte To-do-Elemente verwalten kann.</span><span class="sxs-lookup"><span data-stu-id="c1854-112">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="c1854-113">Übersicht</span><span class="sxs-lookup"><span data-stu-id="c1854-113">Overview</span></span>

<span data-ttu-id="c1854-114">In diesem Tutorial wird die folgende API erstellt:</span><span class="sxs-lookup"><span data-stu-id="c1854-114">This tutorial creates the following API:</span></span>

|<span data-ttu-id="c1854-115">API</span><span class="sxs-lookup"><span data-stu-id="c1854-115">API</span></span> | <span data-ttu-id="c1854-116">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="c1854-116">Description</span></span> | <span data-ttu-id="c1854-117">Anforderungstext</span><span class="sxs-lookup"><span data-stu-id="c1854-117">Request body</span></span> | <span data-ttu-id="c1854-118">Antworttext</span><span class="sxs-lookup"><span data-stu-id="c1854-118">Response body</span></span> |
|--- | ---- | ---- | ---- |
|`GET /api/TodoItems` | <span data-ttu-id="c1854-119">Alle To-do-Elemente abrufen</span><span class="sxs-lookup"><span data-stu-id="c1854-119">Get all to-do items</span></span> | <span data-ttu-id="c1854-120">Keine</span><span class="sxs-lookup"><span data-stu-id="c1854-120">None</span></span> | <span data-ttu-id="c1854-121">Array von To-do-Elementen</span><span class="sxs-lookup"><span data-stu-id="c1854-121">Array of to-do items</span></span>|
|`GET /api/TodoItems/{id}` | <span data-ttu-id="c1854-122">Ein Element nach ID abrufen</span><span class="sxs-lookup"><span data-stu-id="c1854-122">Get an item by ID</span></span> | <span data-ttu-id="c1854-123">Keine</span><span class="sxs-lookup"><span data-stu-id="c1854-123">None</span></span> | <span data-ttu-id="c1854-124">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="c1854-124">To-do item</span></span>|
|`POST /api/TodoItems` | <span data-ttu-id="c1854-125">Neues Element hinzufügen</span><span class="sxs-lookup"><span data-stu-id="c1854-125">Add a new item</span></span> | <span data-ttu-id="c1854-126">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="c1854-126">To-do item</span></span> | <span data-ttu-id="c1854-127">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="c1854-127">To-do item</span></span> |
|`PUT /api/TodoItems/{id}` | <span data-ttu-id="c1854-128">Vorhandenes Element aktualisieren &nbsp;</span><span class="sxs-lookup"><span data-stu-id="c1854-128">Update an existing item &nbsp;</span></span> | <span data-ttu-id="c1854-129">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="c1854-129">To-do item</span></span> | <span data-ttu-id="c1854-130">Keine</span><span class="sxs-lookup"><span data-stu-id="c1854-130">None</span></span> |
|<span data-ttu-id="c1854-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="c1854-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span></span> | <span data-ttu-id="c1854-132">Löschen eines Elements &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="c1854-132">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="c1854-133">Keine</span><span class="sxs-lookup"><span data-stu-id="c1854-133">None</span></span> | <span data-ttu-id="c1854-134">Keine</span><span class="sxs-lookup"><span data-stu-id="c1854-134">None</span></span>|

<span data-ttu-id="c1854-135">Das folgende Diagramm zeigt den Entwurf der App.</span><span class="sxs-lookup"><span data-stu-id="c1854-135">The following diagram shows the design of the app.</span></span>

![Der Client ist das Feld ganz links.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="c1854-141">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="c1854-141">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c1854-142">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c1854-142">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="c1854-143">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c1854-143">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="c1854-144">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="c1854-144">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-5.0.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="c1854-145">Erstellen eines Webprojekts</span><span class="sxs-lookup"><span data-stu-id="c1854-145">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c1854-146">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c1854-146">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c1854-147">Klicken Sie im Menü **Datei** auf **Neu** > **Projekt**.</span><span class="sxs-lookup"><span data-stu-id="c1854-147">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="c1854-148">Wählen Sie die Vorlage **ASP.NET Core-Webanwendung** aus, und klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="c1854-148">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="c1854-149">Geben Sie dem Projekt den Namen *TodoApi*, und klicken Sie auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="c1854-149">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="c1854-150">Vergewissern Sie sich, dass im Dialogfeld **Neue ASP.NET Core-Webanwendung erstellen** die Optionen **.NET Core** und **ASP.NET Core 5.0** ausgewählt sind.</span><span class="sxs-lookup"><span data-stu-id="c1854-150">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 5.0** are selected.</span></span> <span data-ttu-id="c1854-151">Wählen Sie die Vorlage **API** aus, und klicken Sie auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="c1854-151">Select the **API** template and click **Create**.</span></span>

![VS-Dialogfeld „Neues Projekt“](first-web-api/_static/5/vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="c1854-153">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c1854-153">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="c1854-154">Öffnen Sie das [integrierte Terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="c1854-154">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="c1854-155">Wechseln Sie mit dem Befehl `cd`' zu dem Ordner, der den Projektordner enthalten soll.</span><span class="sxs-lookup"><span data-stu-id="c1854-155">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="c1854-156">Führen Sie die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="c1854-156">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="c1854-157">Wenn Sie in einem Dialogfeld angeben müssen, ob Sie dem Projekt die erforderlichen Elemente hinzufügen möchten, wählen Sie **Ja** aus.</span><span class="sxs-lookup"><span data-stu-id="c1854-157">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="c1854-158">Die obenstehenden Befehle haben folgende Konsequenzen:</span><span class="sxs-lookup"><span data-stu-id="c1854-158">The preceding commands:</span></span>

  * <span data-ttu-id="c1854-159">Ein neues Web-API-Projekt wird erstellt und in Visual Studio Code geöffnet.</span><span class="sxs-lookup"><span data-stu-id="c1854-159">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="c1854-160">Die NuGet-Pakete, die im nächsten Abschnitt erforderlich sind, werden hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="c1854-160">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="c1854-161">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="c1854-161">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="c1854-162">Klicken Sie auf **Datei** > **Neue Projektmappe**.</span><span class="sxs-lookup"><span data-stu-id="c1854-162">Select **File** > **New Solution**.</span></span>

  ![Neue Projektmappe in macOS](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="c1854-164">Navigieren Sie in Visual Studio für Mac-Versionen vor Version 8.6 zu **.NET Core** > **App** > **API** > **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="c1854-164">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="c1854-165">Bei Version 8.6 oder höher klicken Sie auf **Web and Console** > **App** > **API** > **Weiter** (Web und Konsole).</span><span class="sxs-lookup"><span data-stu-id="c1854-165">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>

  ![Auswählen von macOS-API-Vorlagen](first-web-api-mac/_static/api_template.png)

* <span data-ttu-id="c1854-167">Wählen Sie im Dialogfeld **Neue ASP.NET Core-Web-API konfigurieren** die neueste .NET Core 5.x-Version als **Zielframework** aus.</span><span class="sxs-lookup"><span data-stu-id="c1854-167">In the **Configure the new ASP.NET Core Web API** dialog, select the latest .NET Core 5.x **Target Framework**.</span></span> <span data-ttu-id="c1854-168">Klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="c1854-168">Select **Next**.</span></span>

* <span data-ttu-id="c1854-169">Geben Sie für **Projektname** *TodoApi* ein, und wählen Sie dann **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="c1854-169">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![Dialogfeld „Konfiguration“](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="c1854-171">Öffnen Sie im Projektordner ein Befehlsterminal, und führen Sie die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="c1854-171">Open a command terminal in the project folder and run the following commands:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-project"></a><span data-ttu-id="c1854-172">Testen des Projekts</span><span class="sxs-lookup"><span data-stu-id="c1854-172">Test the project</span></span>

<span data-ttu-id="c1854-173">Die Projektvorlage erstellt eine `WeatherForecast`-API mit Unterstützung für [Swagger](xref:tutorials/web-api-help-pages-using-swagger).</span><span class="sxs-lookup"><span data-stu-id="c1854-173">The project template creates a `WeatherForecast` API with support for [Swagger](xref:tutorials/web-api-help-pages-using-swagger).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c1854-174">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c1854-174">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="c1854-175">Drücken Sie STRG+F5, um die Ausführung ohne den Debugger zu starten.</span><span class="sxs-lookup"><span data-stu-id="c1854-175">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="c1854-176">Visual Studio startet Folgendes:</span><span class="sxs-lookup"><span data-stu-id="c1854-176">Visual Studio launches:</span></span>

* <span data-ttu-id="c1854-177">Den IIS Express-Webserver.</span><span class="sxs-lookup"><span data-stu-id="c1854-177">The IIS Express web server.</span></span>
* <span data-ttu-id="c1854-178">Den Standardbrowser. Visual Studio navigiert zu `https://localhost:<port>/swagger/index.html`, wobei es sich bei `<port>` um eine zufällig ausgewählte Portnummer handelt.</span><span class="sxs-lookup"><span data-stu-id="c1854-178">The default browser and navigates to `https://localhost:<port>/swagger/index.html`, where `<port>` is a randomly chosen port number.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="c1854-179">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c1854-179">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/trustCertVSC.md)]

<span data-ttu-id="c1854-180">Drücken Sie STRG+F5, um die App auszuführen.</span><span class="sxs-lookup"><span data-stu-id="c1854-180">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="c1854-181">Navigieren Sie in einem Browser zur folgenden URL: [https://localhost:5001/swagger](https://localhost:5001/swagger).</span><span class="sxs-lookup"><span data-stu-id="c1854-181">In a browser, go to following URL: [https://localhost:5001/swagger](https://localhost:5001/swagger)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="c1854-182">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="c1854-182">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="c1854-183">Wählen Sie **Ausführen** > **Debugging starten** aus, um die App zu starten.</span><span class="sxs-lookup"><span data-stu-id="c1854-183">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="c1854-184">Visual Studio für Mac startet einen Browser und navigiert zu `https://localhost:<port>`, wobei es sich bei `<port>` um eine zufällig ausgewählte Portnummer handelt.</span><span class="sxs-lookup"><span data-stu-id="c1854-184">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="c1854-185">Der Fehler „HTTP 404: Nicht gefunden“ wird zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="c1854-185">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="c1854-186">Fügen Sie der URL `/swagger` an, d.h. ändern Sie die URL in `https://localhost:<port>/swagger`.</span><span class="sxs-lookup"><span data-stu-id="c1854-186">Append `/swagger` to the URL (change the URL to `https://localhost:<port>/swagger`).</span></span>

---

<span data-ttu-id="c1854-187">Die Swagger-Seite `/swagger/index.html` wird angezeigt.</span><span class="sxs-lookup"><span data-stu-id="c1854-187">The Swagger page `/swagger/index.html` is displayed.</span></span> <span data-ttu-id="c1854-188">Wählen Sie **GET** > **Tryit out** > **Execute**  (GET > Testen> Ausführen) aus.</span><span class="sxs-lookup"><span data-stu-id="c1854-188">Select **GET** > **Try it out** > **Execute**.</span></span> <span data-ttu-id="c1854-189">Die Seite zeigt Folgendes an:</span><span class="sxs-lookup"><span data-stu-id="c1854-189">The page displays:</span></span>

* <span data-ttu-id="c1854-190">Der [Curl](https://curl.haxx.se/)-Befehl, zum Testen der WeatherForecast-API.</span><span class="sxs-lookup"><span data-stu-id="c1854-190">The [Curl](https://curl.haxx.se/) command to test the WeatherForecast API.</span></span>
* <span data-ttu-id="c1854-191">Die URL zum Testen der WeatherForecast-API.</span><span class="sxs-lookup"><span data-stu-id="c1854-191">The URL to test the WeatherForecast API.</span></span>
* <span data-ttu-id="c1854-192">Der Antwortcode, der Text und die Header.</span><span class="sxs-lookup"><span data-stu-id="c1854-192">The response code, body, and headers.</span></span>
* <span data-ttu-id="c1854-193">Ein Dropdown-Listenfeld mit Medientypen und dem Beispielwert und -schema.</span><span class="sxs-lookup"><span data-stu-id="c1854-193">A drop down list box with media types and the example value and schema.</span></span>

<!-- Review: Do we care the IE generates several errors. It shows the data, but with  Unrecognized response type; displaying content as text.
-->
<span data-ttu-id="c1854-194">Swagger wird verwendet, um hilfreiche Dokumentation und Hilfeseiten für Web-APIs zu generieren.</span><span class="sxs-lookup"><span data-stu-id="c1854-194">Swagger is used to generate useful documentation and help pages for web APIs.</span></span> <span data-ttu-id="c1854-195">Dieses Tutorial konzentriert sich auf die Erstellung einer Web-API.</span><span class="sxs-lookup"><span data-stu-id="c1854-195">This tutorial focuses on creating a web API.</span></span> <span data-ttu-id="c1854-196">Weitere Informationen zu Swagger finden Sie unter <xref:tutorials/web-api-help-pages-using-swagger>.</span><span class="sxs-lookup"><span data-stu-id="c1854-196">For more information on Swagger, see <xref:tutorials/web-api-help-pages-using-swagger>.</span></span>

<span data-ttu-id="c1854-197">Kopieren Sie die **Anforderungs-URL**, und fügen Sie sie im Browser ein: `https://localhost:<port>/WeatherForecast`</span><span class="sxs-lookup"><span data-stu-id="c1854-197">Copy and paste the **Request URL** in the browser:  `https://localhost:<port>/WeatherForecast`</span></span>

<span data-ttu-id="c1854-198">Der zurückgegebene JSON-Code sieht in etwa wie folgt aus:</span><span class="sxs-lookup"><span data-stu-id="c1854-198">JSON similar to the following is returned:</span></span>

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

### <a name="update-the-launchurl"></a><span data-ttu-id="c1854-199">Aktualisieren der launchUrl</span><span class="sxs-lookup"><span data-stu-id="c1854-199">Update the launchUrl</span></span>

<span data-ttu-id="c1854-200">Aktualisieren Sie `launchUrl` in *Properties\launchSettings.json* aus `"swagger"` in `"api/TodoItems"`:</span><span class="sxs-lookup"><span data-stu-id="c1854-200">In *Properties\launchSettings.json*, update `launchUrl` from `"swagger"` to `"api/TodoItems"`:</span></span>

```json
"launchUrl": "api/TodoItems",
```

<span data-ttu-id="c1854-201">Da Swagger entfernt wurde, ändert das oben gezeigte Markup die URL, die für die GET-Methode des Controllers gestartet wird, der in den folgenden Abschnitten hinzugefügt wurde.</span><span class="sxs-lookup"><span data-stu-id="c1854-201">Because Swagger has been removed, the preceding markup changes the URL that is launched to the GET method of the controller added in the following sections.</span></span>

## <a name="add-a-model-class"></a><span data-ttu-id="c1854-202">Hinzufügen einer Modellklasse</span><span class="sxs-lookup"><span data-stu-id="c1854-202">Add a model class</span></span>

<span data-ttu-id="c1854-203">Ein *Modell* ist eine Gruppe von Klassen, die die Daten darstellen, die die App verwaltet.</span><span class="sxs-lookup"><span data-stu-id="c1854-203">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="c1854-204">Das Modell für diese App ist eine einzelne `TodoItem`-Klasse.</span><span class="sxs-lookup"><span data-stu-id="c1854-204">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c1854-205">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c1854-205">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c1854-206">Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt.</span><span class="sxs-lookup"><span data-stu-id="c1854-206">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="c1854-207">Klicken Sie auf **Hinzufügen** > **Neuer Ordner**.</span><span class="sxs-lookup"><span data-stu-id="c1854-207">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="c1854-208">Geben Sie dem Ordner den Namen *Models* .</span><span class="sxs-lookup"><span data-stu-id="c1854-208">Name the folder *Models*.</span></span>

* <span data-ttu-id="c1854-209">Klicken Sie mit der rechten Maustaste auf den Ordner *Models* , und wählen Sie **Hinzufügen** > **Klasse** aus.</span><span class="sxs-lookup"><span data-stu-id="c1854-209">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="c1854-210">Nennen Sie die Klasse *TodoItem*, und wählen Sie **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="c1854-210">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="c1854-211">Ersetzen Sie den Vorlagencode durch Folgendes:</span><span class="sxs-lookup"><span data-stu-id="c1854-211">Replace the template code with the following:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="c1854-212">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c1854-212">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="c1854-213">Fügen Sie einen Ordner mit dem Namen *Models* hinzu.</span><span class="sxs-lookup"><span data-stu-id="c1854-213">Add a folder named *Models*.</span></span>

* <span data-ttu-id="c1854-214">Fügen Sie dem Ordner *Models* mit dem folgenden Code eine `TodoItem`-Klasse hinzu:</span><span class="sxs-lookup"><span data-stu-id="c1854-214">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="c1854-215">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="c1854-215">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="c1854-216">Klicken Sie mit der rechten Maustaste auf das Projekt.</span><span class="sxs-lookup"><span data-stu-id="c1854-216">Right-click the project.</span></span> <span data-ttu-id="c1854-217">Klicken Sie auf **Hinzufügen** > **Neuer Ordner**.</span><span class="sxs-lookup"><span data-stu-id="c1854-217">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="c1854-218">Geben Sie dem Ordner den Namen *Models* .</span><span class="sxs-lookup"><span data-stu-id="c1854-218">Name the folder *Models*.</span></span>

  ![Neuer Ordner](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="c1854-220">Klicken Sie mit der rechten Maustaste auf den Ordner *Models* , und wählen Sie **Hinzufügen** > **Neue Datei** > **Allgemein** > **Leere Klasse** aus.</span><span class="sxs-lookup"><span data-stu-id="c1854-220">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="c1854-221">Nennen Sie die Klasse *TodoItem*, und klicken Sie dann auf **Neu**.</span><span class="sxs-lookup"><span data-stu-id="c1854-221">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="c1854-222">Ersetzen Sie den Vorlagencode durch Folgendes:</span><span class="sxs-lookup"><span data-stu-id="c1854-222">Replace the template code with the following:</span></span>

---

  [!code-csharp[](first-web-api/samples/5.x/TodoApi/Models/TodoItem.cs?name=snippet)]

<span data-ttu-id="c1854-223">Die `Id`-Eigenschaft fungiert als eindeutiger Schlüssel in einer relationalen Datenbank.</span><span class="sxs-lookup"><span data-stu-id="c1854-223">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="c1854-224">Modellklassen können überall im Projekt platziert werden, doch gemäß der Konvention wird der Ordner *Models* verwendet.</span><span class="sxs-lookup"><span data-stu-id="c1854-224">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="c1854-225">Hinzufügen eines Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="c1854-225">Add a database context</span></span>

<span data-ttu-id="c1854-226">Der *Datenbankkontext* ist die Hauptklasse, die die Entity Framework-Funktionen für ein Datenmodell koordiniert.</span><span class="sxs-lookup"><span data-stu-id="c1854-226">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="c1854-227">Diese Klasse wird durch Ableiten von der <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName>-Klasse erstellt.</span><span class="sxs-lookup"><span data-stu-id="c1854-227">This class is created by deriving from the <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName> class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c1854-228">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c1854-228">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-nuget-packages"></a><span data-ttu-id="c1854-229">Hinzufügen von NuGet-Paketen</span><span class="sxs-lookup"><span data-stu-id="c1854-229">Add NuGet packages</span></span>

* <span data-ttu-id="c1854-230">Klicken Sie im Menü **Extras** auf **NuGet-Paket-Manager > NuGet-Pakete für Projektmappe verwalten...** .</span><span class="sxs-lookup"><span data-stu-id="c1854-230">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="c1854-231">Klicken Sie auf die Registerkarte **Durchsuchen**, und geben Sie dann **Microsoft.EntityFrameworkCore.SqlServer** in das Suchfeld ein.</span><span class="sxs-lookup"><span data-stu-id="c1854-231">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.SqlServer** in the search box.</span></span>
<!-- https://github.com/dotnet/AspNetCore.Docs/issues/19782 Delete this line at RTM -->
* <span data-ttu-id="c1854-232">Wählen Sie im linken Bereich **Microsoft.EntityFrameworkCore.SqlServer** aus.</span><span class="sxs-lookup"><span data-stu-id="c1854-232">Select **Microsoft.EntityFrameworkCore.SqlServer** in the left pane.</span></span>
* <span data-ttu-id="c1854-233">Aktivieren Sie das Kontrollkästchen **Projekt** im rechten Bereich, und klicken Sie dann auf **Installieren**.</span><span class="sxs-lookup"><span data-stu-id="c1854-233">Select the **Project** check box in the right pane and then select **Install**.</span></span>
* <span data-ttu-id="c1854-234">Verwenden Sie die Anweisungen oben zum Hinzuzufügen des **Microsoft.EntityFrameworkCore.InMemory**-NuGet-Pakets.</span><span class="sxs-lookup"><span data-stu-id="c1854-234">Use the preceding instructions to add the **Microsoft.EntityFrameworkCore.InMemory** NuGet package.</span></span>

<!-- https://github.com/dotnet/AspNetCore.Docs/issues/19782 Update this image at RTM -->
<span data-ttu-id="c1854-235">![NuGet-Paket-Manager](first-web-api/_static/5/vsNuGet.png)</span><span class="sxs-lookup"><span data-stu-id="c1854-235">![NuGet Package Manager](first-web-api/_static/5/vsNuGet.png)</span></span>

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="c1854-236">Hinzufügen des TodoContext-Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="c1854-236">Add the TodoContext database context</span></span>

* <span data-ttu-id="c1854-237">Klicken Sie mit der rechten Maustaste auf den Ordner *Models* , und wählen Sie **Hinzufügen** > **Klasse** aus.</span><span class="sxs-lookup"><span data-stu-id="c1854-237">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="c1854-238">Nennen Sie die Klasse *TodoContext*, und klicken Sie auf **Hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="c1854-238">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="c1854-239">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="c1854-239">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="c1854-240">Fügen Sie eine `TodoContext`-Klasse zum Ordner *Models* hinzu.</span><span class="sxs-lookup"><span data-stu-id="c1854-240">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="c1854-241">Geben Sie den folgenden Code ein:</span><span class="sxs-lookup"><span data-stu-id="c1854-241">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/5.x/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="c1854-242">Registrieren des Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="c1854-242">Register the database context</span></span>

<span data-ttu-id="c1854-243">In ASP.NET Core müssen Dienste wie der Datenbankkontext mit dem Container [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) registriert werden.</span><span class="sxs-lookup"><span data-stu-id="c1854-243">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="c1854-244">Der Container stellt den Dienst für Controller bereit.</span><span class="sxs-lookup"><span data-stu-id="c1854-244">The container provides the service to controllers.</span></span>

<span data-ttu-id="c1854-245">Aktualisieren Sie *Startup.cs* mit dem folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="c1854-245">Update *Startup.cs* with the following code:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="c1854-246">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="c1854-246">The preceding code:</span></span>

* <span data-ttu-id="c1854-247">Entfernt die Swagger-Aufrufe.</span><span class="sxs-lookup"><span data-stu-id="c1854-247">Removes the Swagger calls.</span></span>
* <span data-ttu-id="c1854-248">Entfernt nicht benötigte `using`-Deklarationen</span><span class="sxs-lookup"><span data-stu-id="c1854-248">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="c1854-249">Fügt dem Abhängigkeitscontainer den Datenbankkontext hinzu</span><span class="sxs-lookup"><span data-stu-id="c1854-249">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="c1854-250">Gibt an, dass der Datenbankkontext eine In-Memory Database verwendet</span><span class="sxs-lookup"><span data-stu-id="c1854-250">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="c1854-251">Erstellen eines Controllergerüsts</span><span class="sxs-lookup"><span data-stu-id="c1854-251">Scaffold a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c1854-252">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c1854-252">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c1854-253">Klicken Sie mit der rechten Maustaste auf den Ordner *Controller*.</span><span class="sxs-lookup"><span data-stu-id="c1854-253">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="c1854-254">Wählen Sie **Hinzufügen** > **Neues Gerüstelement** aus.</span><span class="sxs-lookup"><span data-stu-id="c1854-254">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="c1854-255">Klicken Sie auf **API-Controller mit Aktionen unter Verwendung von Entity Framework** und dann auf **Hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="c1854-255">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="c1854-256">Führen Sie im Dialogfeld **API-Controller mit Aktionen unter Verwendung von Entity Framework** folgende Schritte aus:</span><span class="sxs-lookup"><span data-stu-id="c1854-256">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="c1854-257">Wählen Sie in der **Modellklasse** das Element **TodoItem (TodoApi.Models)** aus.</span><span class="sxs-lookup"><span data-stu-id="c1854-257">Select **TodoItem (TodoApi.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="c1854-258">Wählen Sie in der **Datenkontextklasse** das Element **TodoContext (TodoApi.Models)** aus.</span><span class="sxs-lookup"><span data-stu-id="c1854-258">Select **TodoContext (TodoApi.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="c1854-259">Wählen Sie **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="c1854-259">Select **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="c1854-260">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="c1854-260">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="c1854-261">Führen Sie die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="c1854-261">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet tool update -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="c1854-262">Die obenstehenden Befehle haben folgende Konsequenzen:</span><span class="sxs-lookup"><span data-stu-id="c1854-262">The preceding commands:</span></span>

* <span data-ttu-id="c1854-263">Die NuGet-Pakete für den Gerüstbau werden hinzufügt.</span><span class="sxs-lookup"><span data-stu-id="c1854-263">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="c1854-264">Die Gerüstbau-Engine (`dotnet-aspnet-codegenerator`) wird installiert.</span><span class="sxs-lookup"><span data-stu-id="c1854-264">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="c1854-265">Das Gerüst für `TodoItemsController` wird erstellt.</span><span class="sxs-lookup"><span data-stu-id="c1854-265">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="c1854-266">Der generierte Code hat folgende Auswirkungen:</span><span class="sxs-lookup"><span data-stu-id="c1854-266">The generated code:</span></span>

* <span data-ttu-id="c1854-267">Markiert die Klasse mit dem [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute)-Attribut.</span><span class="sxs-lookup"><span data-stu-id="c1854-267">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="c1854-268">Dieses Attribut gibt an, dass der Controller auf Web-API-Anforderungen reagiert.</span><span class="sxs-lookup"><span data-stu-id="c1854-268">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="c1854-269">Weitere Informationen zu bestimmten Verhaltensweisen, die das Attribut ermöglicht, finden Sie unter <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="c1854-269">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="c1854-270">Verwendet die Abhängigkeitsinjektion, um den Datenbankkontext (`TodoContext`) dem Controller hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="c1854-270">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="c1854-271">Der Datenbankkontext wird in den einzelnen [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete)-Methoden im Controller verwendet.</span><span class="sxs-lookup"><span data-stu-id="c1854-271">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<span data-ttu-id="c1854-272">Die ASP.NET Core-Vorlagen für:</span><span class="sxs-lookup"><span data-stu-id="c1854-272">The ASP.NET Core templates for:</span></span>

* <span data-ttu-id="c1854-273">Controller mit Ansichten enthalten `[action]` in der Routenvorlage.</span><span class="sxs-lookup"><span data-stu-id="c1854-273">Controllers with views include `[action]` in the route template.</span></span>
* <span data-ttu-id="c1854-274">API-Controller enthalten keine `[action]` in der Routenvorlage.</span><span class="sxs-lookup"><span data-stu-id="c1854-274">API controllers don't include `[action]` in the route template.</span></span>

<span data-ttu-id="c1854-275">Wenn sich das `[action]`-Token nicht in der Routenvorlage befindet, wird der [action](xref:mvc/controllers/routing#action)-Name von der Route ausgeschlossen.</span><span class="sxs-lookup"><span data-stu-id="c1854-275">When the `[action]` token isn't in the route template, the [action](xref:mvc/controllers/routing#action) name is excluded from the route.</span></span> <span data-ttu-id="c1854-276">Dies bedeutet, dass der zugehörige Methodenname der Aktion nicht in der übereinstimmenden Route verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="c1854-276">That is, the action's associated method name isn't used in the matching route.</span></span>

## <a name="update-the-posttodoitem-create-method"></a><span data-ttu-id="c1854-277">Aktualisieren der PostTodoItem-Erstellungsmethode</span><span class="sxs-lookup"><span data-stu-id="c1854-277">Update the PostTodoItem create method</span></span>

<span data-ttu-id="c1854-278">Ersetzen Sie die Rückgabeanweisung in `PostTodoItem` durch eine Anweisung mit dem [nameof](/dotnet/csharp/language-reference/operators/nameof)-Operator:</span><span class="sxs-lookup"><span data-stu-id="c1854-278">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="c1854-279">Der oben stehende Code ist eine HTTP POST-Methode, wie durch das [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute)-Attribut angegeben.</span><span class="sxs-lookup"><span data-stu-id="c1854-279">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="c1854-280">Die Methode ruft den Wert der Aufgabe aus dem Text der HTTP-Anforderung ab.</span><span class="sxs-lookup"><span data-stu-id="c1854-280">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="c1854-281">Weitere Informationen finden Sie unter [Attributrouting mit Http[Verb]-Attributen](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="c1854-281">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="c1854-282">Die <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*>-Methode:</span><span class="sxs-lookup"><span data-stu-id="c1854-282">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="c1854-283">Gibt bei Erfolg den [HTTP-Statuscode 201](https://developer.mozilla.org/docs/Web/HTTP/Status/201) zurück.</span><span class="sxs-lookup"><span data-stu-id="c1854-283">Returns an [HTTP 201 status code](https://developer.mozilla.org/docs/Web/HTTP/Status/201) if successful.</span></span> <span data-ttu-id="c1854-284">HTTP 201 ist die Standardantwort für eine HTTP POST-Methode, die eine neue Ressource auf dem Server erstellt.</span><span class="sxs-lookup"><span data-stu-id="c1854-284">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="c1854-285">Fügt der Antwort einen [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location)-Header hinzu.</span><span class="sxs-lookup"><span data-stu-id="c1854-285">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="c1854-286">Der `Location`-Header legt den [URI](https://developer.mozilla.org/docs/Glossary/URI) des neu erstellten To-do-Elements fest.</span><span class="sxs-lookup"><span data-stu-id="c1854-286">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="c1854-287">Weitere Informationen finden Sie unter [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="c1854-287">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="c1854-288">Verweist auf die `GetTodoItem`-Aktion zum Erstellen des URIs des `Location`-Headers.</span><span class="sxs-lookup"><span data-stu-id="c1854-288">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="c1854-289">Das `nameof`-Schlüsselwort von C# wird verwendet, um eine Hartcodierung des Aktionsnamens im `CreatedAtAction`-Aufruf zu vermeiden.</span><span class="sxs-lookup"><span data-stu-id="c1854-289">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="c1854-290">Installieren von Postman</span><span class="sxs-lookup"><span data-stu-id="c1854-290">Install Postman</span></span>

<span data-ttu-id="c1854-291">Dieses Tutorial verwendet Postman zum Testen der Web-API.</span><span class="sxs-lookup"><span data-stu-id="c1854-291">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="c1854-292">Installieren Sie [Postman](https://www.getpostman.com/downloads/).</span><span class="sxs-lookup"><span data-stu-id="c1854-292">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="c1854-293">Starten Sie die Web-App.</span><span class="sxs-lookup"><span data-stu-id="c1854-293">Start the web app.</span></span>
* <span data-ttu-id="c1854-294">Starten Sie Postman.</span><span class="sxs-lookup"><span data-stu-id="c1854-294">Start Postman.</span></span>
* <span data-ttu-id="c1854-295">Deaktivieren Sie **SSL certificate verification** (Verifizierung des SSL-Zertifikats).</span><span class="sxs-lookup"><span data-stu-id="c1854-295">Disable **SSL certificate verification**</span></span>
  * <span data-ttu-id="c1854-296">Deaktivieren Sie auf der Registerkarte **General** (Allgemein) unter File > Settings (**Datei** > **Einstellungen**) **SSL certificate verification** (Verifizierung des SSL-Zertifikats).</span><span class="sxs-lookup"><span data-stu-id="c1854-296">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="c1854-297">Aktivieren Sie die Verifizierung des SSL-Zertifikats wieder, nachdem Sie den Controller getestet haben.</span><span class="sxs-lookup"><span data-stu-id="c1854-297">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="c1854-298">Testen von PostTodoItem mit Postman</span><span class="sxs-lookup"><span data-stu-id="c1854-298">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="c1854-299">Erstellen Sie eine neue Anforderung.</span><span class="sxs-lookup"><span data-stu-id="c1854-299">Create a new request.</span></span>
* <span data-ttu-id="c1854-300">Legen Sie als HTTP-Methode `POST` fest.</span><span class="sxs-lookup"><span data-stu-id="c1854-300">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="c1854-301">Legen Sie den URI auf `https://localhost:<port>/api/TodoItems` fest.</span><span class="sxs-lookup"><span data-stu-id="c1854-301">Set the URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="c1854-302">Beispiel: `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="c1854-302">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="c1854-303">Wählen Sie die Registerkarte **Body** (Text) aus.</span><span class="sxs-lookup"><span data-stu-id="c1854-303">Select the **Body** tab.</span></span>
* <span data-ttu-id="c1854-304">Wählen Sie das Optionsfeld **raw** (Unformatiert) aus.</span><span class="sxs-lookup"><span data-stu-id="c1854-304">Select the **raw** radio button.</span></span>
* <span data-ttu-id="c1854-305">Legen Sie den Typ auf **JSON (application/json)** fest.</span><span class="sxs-lookup"><span data-stu-id="c1854-305">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="c1854-306">Geben Sie für die Aufgabe den Anforderungstext im JSON-Format ein:</span><span class="sxs-lookup"><span data-stu-id="c1854-306">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="c1854-307">Wählen Sie **Send** (Senden) aus.</span><span class="sxs-lookup"><span data-stu-id="c1854-307">Select **Send**.</span></span>

  ![Postman mit erstellter Anforderung](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri"></a><span data-ttu-id="c1854-309">Testen des Adressheader-URIs</span><span class="sxs-lookup"><span data-stu-id="c1854-309">Test the location header URI</span></span>

<span data-ttu-id="c1854-310">Der Location-Header-URI kann im Browser getestet werden.</span><span class="sxs-lookup"><span data-stu-id="c1854-310">The location header URI can be tested in the browser.</span></span> <span data-ttu-id="c1854-311">Kopieren Sie den Location-Header-URI, und fügen Sie ihn im Browser ein.</span><span class="sxs-lookup"><span data-stu-id="c1854-311">Copy and paste the location header URI into the browser.</span></span>

<span data-ttu-id="c1854-312">So testen Sie in Postman:</span><span class="sxs-lookup"><span data-stu-id="c1854-312">To test in Postman:</span></span>

* <span data-ttu-id="c1854-313">Wählen Sie auf der Registerkarte **Header** (Header) den Bereich **Response** (Antwort) aus.</span><span class="sxs-lookup"><span data-stu-id="c1854-313">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="c1854-314">Kopieren Sie den den Headerwert von **Location** (Speicherort):</span><span class="sxs-lookup"><span data-stu-id="c1854-314">Copy the **Location** header value:</span></span>

  ![Registerkarte „Header“ in der Postman-Konsole](first-web-api/_static/3/create.png)

* <span data-ttu-id="c1854-316">Legen Sie als HTTP-Methode `GET` fest.</span><span class="sxs-lookup"><span data-stu-id="c1854-316">Set the HTTP method to `GET`.</span></span>
* <span data-ttu-id="c1854-317">Legen Sie den URI auf `https://localhost:<port>/api/TodoItems/1` fest.</span><span class="sxs-lookup"><span data-stu-id="c1854-317">Set the URI to `https://localhost:<port>/api/TodoItems/1`.</span></span> <span data-ttu-id="c1854-318">Beispiel: `https://localhost:5001/api/TodoItems/1`.</span><span class="sxs-lookup"><span data-stu-id="c1854-318">For example, `https://localhost:5001/api/TodoItems/1`.</span></span>
* <span data-ttu-id="c1854-319">Wählen Sie **Send** (Senden) aus.</span><span class="sxs-lookup"><span data-stu-id="c1854-319">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="c1854-320">Überblick über die GET-Methoden</span><span class="sxs-lookup"><span data-stu-id="c1854-320">Examine the GET methods</span></span>

<span data-ttu-id="c1854-321">Zwei GET-Endpunkte werden implementiert:</span><span class="sxs-lookup"><span data-stu-id="c1854-321">Two GET endpoints are implemented:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="c1854-322">Testen Sie die App, indem Sie die beiden Endpunkte in einem Browser oder über Postman aufrufen.</span><span class="sxs-lookup"><span data-stu-id="c1854-322">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="c1854-323">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="c1854-323">For example:</span></span>

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

<span data-ttu-id="c1854-324">Durch einen Aufruf von `GetTodoItems` wird eine Antwort gesendet, die der folgenden ähnelt:</span><span class="sxs-lookup"><span data-stu-id="c1854-324">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="c1854-325">Testen von GET mit Postman</span><span class="sxs-lookup"><span data-stu-id="c1854-325">Test Get with Postman</span></span>

* <span data-ttu-id="c1854-326">Erstellen Sie eine neue Anforderung.</span><span class="sxs-lookup"><span data-stu-id="c1854-326">Create a new request.</span></span>
* <span data-ttu-id="c1854-327">Legen Sie die HTTP-Methode auf **GET** fest.</span><span class="sxs-lookup"><span data-stu-id="c1854-327">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="c1854-328">Legen Sie den Anforderungs-URI auf `https://localhost:<port>/api/TodoItems` fest.</span><span class="sxs-lookup"><span data-stu-id="c1854-328">Set the request URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="c1854-329">Beispielsweise `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="c1854-329">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="c1854-330">Wählen Sie in Postman **Two pane view** (Ansicht mit zwei Bereichen) aus.</span><span class="sxs-lookup"><span data-stu-id="c1854-330">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="c1854-331">Wählen Sie **Send** (Senden) aus.</span><span class="sxs-lookup"><span data-stu-id="c1854-331">Select **Send**.</span></span>

<span data-ttu-id="c1854-332">Diese App verwendet eine In-Memory-Datenbank.</span><span class="sxs-lookup"><span data-stu-id="c1854-332">This app uses an in-memory database.</span></span> <span data-ttu-id="c1854-333">Wenn die App angehalten und dann wieder gestartet wird, werden durch die vorherige GET-Anforderung keine Daten zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="c1854-333">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="c1854-334">Wenn keine Daten zurückgegeben werden, senden Sie mit [POST](#post) Daten an die App.</span><span class="sxs-lookup"><span data-stu-id="c1854-334">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="c1854-335">Routing und URL-Pfade</span><span class="sxs-lookup"><span data-stu-id="c1854-335">Routing and URL paths</span></span>

<span data-ttu-id="c1854-336">Das Attribut [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) gibt eine Methode an, die auf eine HTTP GET-Anforderung antwortet.</span><span class="sxs-lookup"><span data-stu-id="c1854-336">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="c1854-337">Der URL-Pfad für jede Methode wird wie folgt erstellt:</span><span class="sxs-lookup"><span data-stu-id="c1854-337">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="c1854-338">Beginnen Sie mit der Vorlagenzeichenfolge im `Route`-Attribut des Controllers:</span><span class="sxs-lookup"><span data-stu-id="c1854-338">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="c1854-339">Ersetzen Sie `[controller]` durch den Namen des Controllers, bei dem es sich standardmäßig um den Namen der Controller-Klasse ohne das Suffix „Controller“ handelt.</span><span class="sxs-lookup"><span data-stu-id="c1854-339">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="c1854-340">In diesem Beispiel ist der Klassenname des Controllers „**TodoItems** Controller“. Der Controllername lautet also „TodoItems“.</span><span class="sxs-lookup"><span data-stu-id="c1854-340">For this sample, the controller class name is **TodoItems** Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="c1854-341">Beim ASP.NET Core-[Routing](xref:mvc/controllers/routing) wird die Groß-/Kleinschreibung nicht beachtet.</span><span class="sxs-lookup"><span data-stu-id="c1854-341">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="c1854-342">Wenn das `[HttpGet]`-Attribut eine Routenvorlage (z. B. `[HttpGet("products")]`) hat, fügen Sie diese an den Pfad an.</span><span class="sxs-lookup"><span data-stu-id="c1854-342">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="c1854-343">In diesem Beispiel wird keine Vorlage verwendet.</span><span class="sxs-lookup"><span data-stu-id="c1854-343">This sample doesn't use a template.</span></span> <span data-ttu-id="c1854-344">Weitere Informationen finden Sie unter [Attributrouting mit Http[Verb]-Attributen](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="c1854-344">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="c1854-345">In der folgenden `GetTodoItem`-Methode ist `"{id}"` eine Platzhaltervariable für den eindeutigen Bezeichner des To-do-Elements.</span><span class="sxs-lookup"><span data-stu-id="c1854-345">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="c1854-346">Wenn `GetTodoItem` aufgerufen wird, wird der Wert von `"{id}"` in der URL der Methode in ihrem Parameter `id` bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="c1854-346">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="c1854-347">Rückgabewerte</span><span class="sxs-lookup"><span data-stu-id="c1854-347">Return values</span></span>

<span data-ttu-id="c1854-348">Der Rückgabetyp der Methoden `GetTodoItems` und `GetTodoItem` ist der [ActionResult\<T>-Typ](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="c1854-348">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="c1854-349">ASP.NET Core serialisiert automatisch das Objekt in [JSON](https://www.json.org/) und schreibt den JSON-Code in den Text der Antwortnachricht.</span><span class="sxs-lookup"><span data-stu-id="c1854-349">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="c1854-350">Der Antwortcode für diesen Rückgabetyp ist [200 OK](https://developer.mozilla.org/docs/Web/HTTP/Status/200), vorausgesetzt, es gibt keine Ausnahmefehler.</span><span class="sxs-lookup"><span data-stu-id="c1854-350">The response code for this return type is [200 OK](https://developer.mozilla.org/docs/Web/HTTP/Status/200), assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="c1854-351">Nicht behandelte Ausnahmen werden in 5xx-Fehler übersetzt.</span><span class="sxs-lookup"><span data-stu-id="c1854-351">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="c1854-352">`ActionResult`-Rückgabetypen können eine Vielzahl von HTTP-Statuscodes darstellen.</span><span class="sxs-lookup"><span data-stu-id="c1854-352">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="c1854-353">Beispielsweise kann `GetTodoItem` zwei verschiedene Statuswerte zurückgeben:</span><span class="sxs-lookup"><span data-stu-id="c1854-353">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="c1854-354">Wenn kein Element mit der angeforderten ID übereinstimmt, gibt die Methode einen <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A>-Fehlercode [Status 404](https://developer.mozilla.org/docs/Web/HTTP/Status/404) zurück.</span><span class="sxs-lookup"><span data-stu-id="c1854-354">If no item matches the requested ID, the method returns a [404 status](https://developer.mozilla.org/docs/Web/HTTP/Status/404) <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="c1854-355">Andernfalls gibt die Methode 200 mit einem JSON-Antworttext zurück.</span><span class="sxs-lookup"><span data-stu-id="c1854-355">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="c1854-356">Die Rückgabe von `item` löst eine HTTP 200-Antwort aus.</span><span class="sxs-lookup"><span data-stu-id="c1854-356">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="c1854-357">PutTodoItem-Methode</span><span class="sxs-lookup"><span data-stu-id="c1854-357">The PutTodoItem method</span></span>

<span data-ttu-id="c1854-358">Untersuchen Sie die `PutTodoItem`-Methode.</span><span class="sxs-lookup"><span data-stu-id="c1854-358">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="c1854-359">`PutTodoItem` ähnelt `PostTodoItem`, verwendet allerdings HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="c1854-359">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="c1854-360">Die Antwort ist [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="c1854-360">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="c1854-361">Gemäß der HTTP-Spezifikation erfordert eine PUT-Anforderung, dass der Client die gesamte aktualisierte Entität (nicht nur die Änderungen) sendet.</span><span class="sxs-lookup"><span data-stu-id="c1854-361">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="c1854-362">Verwenden Sie [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute), um Teilupdates zu unterstützen.</span><span class="sxs-lookup"><span data-stu-id="c1854-362">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="c1854-363">Wenn beim Aufrufen von `PutTodoItem` ein Fehler zurückgegeben wird, rufen Sie `GET` auf, um sicherzustellen, dass die Datenbank ein Element enthält.</span><span class="sxs-lookup"><span data-stu-id="c1854-363">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="c1854-364">Testen der PutTodoItem-Methode</span><span class="sxs-lookup"><span data-stu-id="c1854-364">Test the PutTodoItem method</span></span>

<span data-ttu-id="c1854-365">In diesem Beispiel wird eine In-Memory-Datenbank verwendet, die jedes Mal initialisiert werden muss, wenn die App gestartet wird.</span><span class="sxs-lookup"><span data-stu-id="c1854-365">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="c1854-366">Es muss ein Element in der Datenbank vorhanden sein, bevor Sie einen PUT-Aufruf durchführen.</span><span class="sxs-lookup"><span data-stu-id="c1854-366">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="c1854-367">Rufen Sie vor einem PUT-Aufruf GET auf, um sicherzustellen, dass ein Element in der Datenbank vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="c1854-367">Call GET to ensure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="c1854-368">Aktualisieren Sie das To-do-Element, das über den ID-Wert 1 verfügt, und legen Sie als Namen `"feed fish"` fest:</span><span class="sxs-lookup"><span data-stu-id="c1854-368">Update the to-do item that has Id = 1 and set its name to `"feed fish"`:</span></span>

```json
  {
    "Id":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="c1854-369">Die folgende Abbildung zeigt das Postman-Update:</span><span class="sxs-lookup"><span data-stu-id="c1854-369">The following image shows the Postman update:</span></span>

![Postman-Konsole mit der Antwort „204 (Kein Inhalt)“](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="c1854-371">DeleteTodoItem-Methode</span><span class="sxs-lookup"><span data-stu-id="c1854-371">The DeleteTodoItem method</span></span>

<span data-ttu-id="c1854-372">Untersuchen Sie die `DeleteTodoItem`-Methode.</span><span class="sxs-lookup"><span data-stu-id="c1854-372">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="c1854-373">Testen der DeleteTodoItem-Methode</span><span class="sxs-lookup"><span data-stu-id="c1854-373">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="c1854-374">So löschen Sie mit Postman eine Aufgabe</span><span class="sxs-lookup"><span data-stu-id="c1854-374">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="c1854-375">Legen Sie die Methode auf `DELETE` fest.</span><span class="sxs-lookup"><span data-stu-id="c1854-375">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="c1854-376">Legen Sie den URI des zu löschenden Objekts fest, z. B. `https://localhost:5001/api/TodoItems/1`.</span><span class="sxs-lookup"><span data-stu-id="c1854-376">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="c1854-377">Wählen Sie **Send** (Senden) aus.</span><span class="sxs-lookup"><span data-stu-id="c1854-377">Select **Send**.</span></span>

<a name="over-post-v5"></a>

## <a name="prevent-over-posting"></a><span data-ttu-id="c1854-378">Vermeiden von Overposting</span><span class="sxs-lookup"><span data-stu-id="c1854-378">Prevent over-posting</span></span>

<span data-ttu-id="c1854-379">Derzeit macht die Beispiel-App das gesamte `TodoItem`-Objekt verfügbar.</span><span class="sxs-lookup"><span data-stu-id="c1854-379">Currently the sample app exposes the entire `TodoItem` object.</span></span> <span data-ttu-id="c1854-380">In den Produktions-Apps sind die Daten, die eingegeben und mithilfe einer Teilmenge des Modells zurückgegeben werden, in der Regel eingeschränkt.</span><span class="sxs-lookup"><span data-stu-id="c1854-380">Production apps typically limit the data that's input and returned using a subset of the model.</span></span> <span data-ttu-id="c1854-381">Hierfür gibt es mehrere Gründe, wobei die Sicherheit einer der Hauptgründe ist.</span><span class="sxs-lookup"><span data-stu-id="c1854-381">There are multiple reasons behind this and security is a major one.</span></span> <span data-ttu-id="c1854-382">Die Teilmenge eines Modells wird üblicherweise als Datenübertragungsobjekt (DTO, Data Transfer Object), Eingabemodell oder Anzeigemodell bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="c1854-382">The subset of a model is usually referred to as a Data Transfer Object (DTO), input model, or view model.</span></span> <span data-ttu-id="c1854-383">In diesem Artikel wird das **DTO** verwendet.</span><span class="sxs-lookup"><span data-stu-id="c1854-383">**DTO** is used in this article.</span></span>

<span data-ttu-id="c1854-384">Ein DTO kann für Folgendes verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="c1854-384">A DTO may be used to:</span></span>

* <span data-ttu-id="c1854-385">Vermeiden Sie Overposting.</span><span class="sxs-lookup"><span data-stu-id="c1854-385">Prevent over-posting.</span></span>
* <span data-ttu-id="c1854-386">Ausblenden von Eigenschaften, die Clients nicht anzeigen sollen</span><span class="sxs-lookup"><span data-stu-id="c1854-386">Hide properties that clients are not supposed to view.</span></span>
* <span data-ttu-id="c1854-387">Auslassen einiger Eigenschaften, um die Nutzlast zu verringern</span><span class="sxs-lookup"><span data-stu-id="c1854-387">Omit some properties in order to reduce payload size.</span></span>
* <span data-ttu-id="c1854-388">Vereinfachen von Objektgraphen, die geschachtelte Objekte enthalten</span><span class="sxs-lookup"><span data-stu-id="c1854-388">Flatten object graphs that contain nested objects.</span></span> <span data-ttu-id="c1854-389">Vereinfachte Objektgraphen können für Clients zweckmäßiger sein.</span><span class="sxs-lookup"><span data-stu-id="c1854-389">Flattened object graphs can be more convenient for clients.</span></span>

<span data-ttu-id="c1854-390">Um den DTO-Ansatz zu veranschaulichen, aktualisieren Sie die `TodoItem`-Klasse, sodass sie ein geheimes Feld einschließt:</span><span class="sxs-lookup"><span data-stu-id="c1854-390">To demonstrate the DTO approach, update the `TodoItem` class to include a secret field:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApiDTO/Models/TodoItem.cs?name=snippet&highlight=8)]

<span data-ttu-id="c1854-391">Das geheime Feld muss in dieser App ausgeblendet werden, eine administrative App kann es jedoch verfügbar machen.</span><span class="sxs-lookup"><span data-stu-id="c1854-391">The secret field needs to be hidden from this app, but an administrative app could choose to expose it.</span></span>

<span data-ttu-id="c1854-392">Vergewissern Sie sich, dass Sie das geheime Feld veröffentlichen und abrufen können.</span><span class="sxs-lookup"><span data-stu-id="c1854-392">Verify you can post and get the secret field.</span></span>

<span data-ttu-id="c1854-393">Erstellen Sie ein DTO-Modell:</span><span class="sxs-lookup"><span data-stu-id="c1854-393">Create a DTO model:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApiDTO/Models/TodoItemDTO.cs?name=snippet)]

<span data-ttu-id="c1854-394">Aktualisieren Sie `TodoItemsController`, sodass `TodoItemDTO` verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="c1854-394">Update the `TodoItemsController` to use `TodoItemDTO`:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

<span data-ttu-id="c1854-395">Vergewissern Sie sich, dass Sie das geheime Feld weder veröffentlichen noch abrufen können.</span><span class="sxs-lookup"><span data-stu-id="c1854-395">Verify you can't post or get the secret field.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="c1854-396">Aufrufen der Web-API mit JavaScript</span><span class="sxs-lookup"><span data-stu-id="c1854-396">Call the web API with JavaScript</span></span>

<span data-ttu-id="c1854-397">Mehr dazu finden Sie im [-Tutorial: Aufrufen einer ASP.NET Core-Web-API mit JavaScript](xref:tutorials/web-api-javascript).</span><span class="sxs-lookup"><span data-stu-id="c1854-397">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="c1854-398">In diesem Tutorial lernen Sie, wie die folgenden Aufgaben ausgeführt werden:</span><span class="sxs-lookup"><span data-stu-id="c1854-398">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="c1854-399">Erstellen eines Web-API-Projekts</span><span class="sxs-lookup"><span data-stu-id="c1854-399">Create a web API project.</span></span>
> * <span data-ttu-id="c1854-400">Hinzufügen einer Modellklasse und eines Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="c1854-400">Add a model class and a database context.</span></span>
> * <span data-ttu-id="c1854-401">Erstellen eines Controllergerüsts mit CRUD-Methoden</span><span class="sxs-lookup"><span data-stu-id="c1854-401">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="c1854-402">Konfigurieren des Routings, der URL-Pfade und der Rückgabewerte</span><span class="sxs-lookup"><span data-stu-id="c1854-402">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="c1854-403">Aufrufen der Web-API mit Postman</span><span class="sxs-lookup"><span data-stu-id="c1854-403">Call the web API with Postman.</span></span>

<span data-ttu-id="c1854-404">Abschließend steht Ihnen eine Web-API zur Verfügung, die in einer relationalen Datenbank gespeicherte To-do-Elemente verwalten kann.</span><span class="sxs-lookup"><span data-stu-id="c1854-404">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="c1854-405">Übersicht</span><span class="sxs-lookup"><span data-stu-id="c1854-405">Overview</span></span>

<span data-ttu-id="c1854-406">In diesem Tutorial wird die folgende API erstellt:</span><span class="sxs-lookup"><span data-stu-id="c1854-406">This tutorial creates the following API:</span></span>

|<span data-ttu-id="c1854-407">API</span><span class="sxs-lookup"><span data-stu-id="c1854-407">API</span></span> | <span data-ttu-id="c1854-408">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="c1854-408">Description</span></span> | <span data-ttu-id="c1854-409">Anforderungstext</span><span class="sxs-lookup"><span data-stu-id="c1854-409">Request body</span></span> | <span data-ttu-id="c1854-410">Antworttext</span><span class="sxs-lookup"><span data-stu-id="c1854-410">Response body</span></span> |
|--- | ---- | ---- | ---- |
|`GET /api/TodoItems` | <span data-ttu-id="c1854-411">Alle To-do-Elemente abrufen</span><span class="sxs-lookup"><span data-stu-id="c1854-411">Get all to-do items</span></span> | <span data-ttu-id="c1854-412">Keine</span><span class="sxs-lookup"><span data-stu-id="c1854-412">None</span></span> | <span data-ttu-id="c1854-413">Array von To-do-Elementen</span><span class="sxs-lookup"><span data-stu-id="c1854-413">Array of to-do items</span></span>|
|`GET /api/TodoItems/{id}` | <span data-ttu-id="c1854-414">Ein Element nach ID abrufen</span><span class="sxs-lookup"><span data-stu-id="c1854-414">Get an item by ID</span></span> | <span data-ttu-id="c1854-415">Keine</span><span class="sxs-lookup"><span data-stu-id="c1854-415">None</span></span> | <span data-ttu-id="c1854-416">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="c1854-416">To-do item</span></span>|
|`POST /api/TodoItems` | <span data-ttu-id="c1854-417">Neues Element hinzufügen</span><span class="sxs-lookup"><span data-stu-id="c1854-417">Add a new item</span></span> | <span data-ttu-id="c1854-418">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="c1854-418">To-do item</span></span> | <span data-ttu-id="c1854-419">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="c1854-419">To-do item</span></span> |
|`PUT /api/TodoItems/{id}` | <span data-ttu-id="c1854-420">Vorhandenes Element aktualisieren &nbsp;</span><span class="sxs-lookup"><span data-stu-id="c1854-420">Update an existing item &nbsp;</span></span> | <span data-ttu-id="c1854-421">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="c1854-421">To-do item</span></span> | <span data-ttu-id="c1854-422">Keine</span><span class="sxs-lookup"><span data-stu-id="c1854-422">None</span></span> |
|<span data-ttu-id="c1854-423">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="c1854-423">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span></span> | <span data-ttu-id="c1854-424">Löschen eines Elements &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="c1854-424">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="c1854-425">Keine</span><span class="sxs-lookup"><span data-stu-id="c1854-425">None</span></span> | <span data-ttu-id="c1854-426">Keine</span><span class="sxs-lookup"><span data-stu-id="c1854-426">None</span></span>|

<span data-ttu-id="c1854-427">Das folgende Diagramm zeigt den Entwurf der App.</span><span class="sxs-lookup"><span data-stu-id="c1854-427">The following diagram shows the design of the app.</span></span>

![Der Client ist das Feld ganz links.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="c1854-433">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="c1854-433">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c1854-434">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c1854-434">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="c1854-435">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c1854-435">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="c1854-436">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="c1854-436">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="c1854-437">Erstellen eines Webprojekts</span><span class="sxs-lookup"><span data-stu-id="c1854-437">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c1854-438">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c1854-438">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c1854-439">Klicken Sie im Menü **Datei** auf **Neu** > **Projekt**.</span><span class="sxs-lookup"><span data-stu-id="c1854-439">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="c1854-440">Wählen Sie die Vorlage **ASP.NET Core-Webanwendung** aus, und klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="c1854-440">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="c1854-441">Geben Sie dem Projekt den Namen *TodoApi*, und klicken Sie auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="c1854-441">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="c1854-442">Vergewissern Sie sich, dass im Dialogfeld **Neue ASP.NET Core-Webanwendung erstellen** die Optionen **.NET Core** und **ASP.NET Core 3.1** ausgewählt sind.</span><span class="sxs-lookup"><span data-stu-id="c1854-442">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.1** are selected.</span></span> <span data-ttu-id="c1854-443">Wählen Sie die Vorlage **API** aus, und klicken Sie auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="c1854-443">Select the **API** template and click **Create**.</span></span>

![VS-Dialogfeld „Neues Projekt“](first-web-api/_static/vs3.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="c1854-445">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c1854-445">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="c1854-446">Öffnen Sie das [integrierte Terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="c1854-446">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="c1854-447">Wechseln Sie mit dem Befehl `cd`' zu dem Ordner, der den Projektordner enthalten soll.</span><span class="sxs-lookup"><span data-stu-id="c1854-447">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="c1854-448">Führen Sie die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="c1854-448">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="c1854-449">Wenn Sie in einem Dialogfeld angeben müssen, ob Sie dem Projekt die erforderlichen Elemente hinzufügen möchten, wählen Sie **Ja** aus.</span><span class="sxs-lookup"><span data-stu-id="c1854-449">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="c1854-450">Die obenstehenden Befehle haben folgende Konsequenzen:</span><span class="sxs-lookup"><span data-stu-id="c1854-450">The preceding commands:</span></span>

  * <span data-ttu-id="c1854-451">Ein neues Web-API-Projekt wird erstellt und in Visual Studio Code geöffnet.</span><span class="sxs-lookup"><span data-stu-id="c1854-451">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="c1854-452">Die NuGet-Pakete, die im nächsten Abschnitt erforderlich sind, werden hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="c1854-452">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="c1854-453">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="c1854-453">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="c1854-454">Klicken Sie auf **Datei** > **Neue Projektmappe**.</span><span class="sxs-lookup"><span data-stu-id="c1854-454">Select **File** > **New Solution**.</span></span>

  ![Neue Projektmappe in macOS](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="c1854-456">Navigieren Sie in Visual Studio für Mac-Versionen vor Version 8.6 zu **.NET Core** > **App** > **API** > **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="c1854-456">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="c1854-457">Bei Version 8.6 oder höher klicken Sie auf **Web and Console** > **App** > **API** > **Weiter** (Web und Konsole).</span><span class="sxs-lookup"><span data-stu-id="c1854-457">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>

  ![Auswählen von macOS-API-Vorlagen](first-web-api-mac/_static/api_template.png)

* <span data-ttu-id="c1854-459">Wählen Sie im Dialogfeld **Neue ASP.NET Core-Web-API konfigurieren** die neueste .NET Core 3.x-Version als **Zielframework** aus.</span><span class="sxs-lookup"><span data-stu-id="c1854-459">In the **Configure the new ASP.NET Core Web API** dialog, select the latest .NET Core 3.x **Target Framework**.</span></span> <span data-ttu-id="c1854-460">Klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="c1854-460">Select **Next**.</span></span>

* <span data-ttu-id="c1854-461">Geben Sie für **Projektname** *TodoApi* ein, und wählen Sie dann **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="c1854-461">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![Dialogfeld „Konfiguration“](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="c1854-463">Öffnen Sie im Projektordner ein Befehlsterminal, und führen Sie die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="c1854-463">Open a command terminal in the project folder and run the following commands:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-api"></a><span data-ttu-id="c1854-464">Testen der API</span><span class="sxs-lookup"><span data-stu-id="c1854-464">Test the API</span></span>

<span data-ttu-id="c1854-465">Die Projektvorlage erstellt eine `WeatherForecast`-API.</span><span class="sxs-lookup"><span data-stu-id="c1854-465">The project template creates a `WeatherForecast` API.</span></span> <span data-ttu-id="c1854-466">Rufen Sie in einem Browser die `Get`-Methode zum Testen der App auf.</span><span class="sxs-lookup"><span data-stu-id="c1854-466">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c1854-467">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c1854-467">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="c1854-468">Drücken Sie STRG+F5, um die App auszuführen.</span><span class="sxs-lookup"><span data-stu-id="c1854-468">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="c1854-469">Visual Studio startet einen Browser und navigiert zu `https://localhost:<port>/WeatherForecast`, wobei es sich bei `<port>` um eine zufällig ausgewählte Portnummer handelt.</span><span class="sxs-lookup"><span data-stu-id="c1854-469">Visual Studio launches a browser and navigates to `https://localhost:<port>/WeatherForecast`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="c1854-470">Wenn Sie in einem Dialogfeld angeben müssen, ob Sie dem IIS Express-Zertifikat vertrauen, wählen Sie **Ja** aus.</span><span class="sxs-lookup"><span data-stu-id="c1854-470">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="c1854-471">Wählen Sie im folgenden Dialogfeld **Sicherheitswarnung** die Option **Ja** aus.</span><span class="sxs-lookup"><span data-stu-id="c1854-471">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="c1854-472">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c1854-472">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="c1854-473">Drücken Sie STRG+F5, um die App auszuführen.</span><span class="sxs-lookup"><span data-stu-id="c1854-473">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="c1854-474">Navigieren Sie in einem Browser zur folgenden URL: `https://localhost:5001/WeatherForecast`.</span><span class="sxs-lookup"><span data-stu-id="c1854-474">In a browser, go to following URL: `https://localhost:5001/WeatherForecast`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="c1854-475">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="c1854-475">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="c1854-476">Wählen Sie **Ausführen** > **Debugging starten** aus, um die App zu starten.</span><span class="sxs-lookup"><span data-stu-id="c1854-476">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="c1854-477">Visual Studio für Mac startet einen Browser und navigiert zu `https://localhost:<port>`, wobei es sich bei `<port>` um eine zufällig ausgewählte Portnummer handelt.</span><span class="sxs-lookup"><span data-stu-id="c1854-477">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="c1854-478">Der Fehler „HTTP 404: Nicht gefunden“ wird zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="c1854-478">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="c1854-479">Fügen Sie `/WeatherForecast` an die URL an, d. h., ändern Sie sie in `https://localhost:<port>/WeatherForecast`.</span><span class="sxs-lookup"><span data-stu-id="c1854-479">Append `/WeatherForecast` to the URL (change the URL to `https://localhost:<port>/WeatherForecast`).</span></span>

---

<span data-ttu-id="c1854-480">Der zurückgegebene JSON-Code sieht in etwa wie folgt aus:</span><span class="sxs-lookup"><span data-stu-id="c1854-480">JSON similar to the following is returned:</span></span>

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

## <a name="add-a-model-class"></a><span data-ttu-id="c1854-481">Hinzufügen einer Modellklasse</span><span class="sxs-lookup"><span data-stu-id="c1854-481">Add a model class</span></span>

<span data-ttu-id="c1854-482">Ein *Modell* ist eine Gruppe von Klassen, die die Daten darstellen, die die App verwaltet.</span><span class="sxs-lookup"><span data-stu-id="c1854-482">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="c1854-483">Das Modell für diese App ist eine einzelne `TodoItem`-Klasse.</span><span class="sxs-lookup"><span data-stu-id="c1854-483">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c1854-484">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c1854-484">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c1854-485">Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt.</span><span class="sxs-lookup"><span data-stu-id="c1854-485">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="c1854-486">Klicken Sie auf **Hinzufügen** > **Neuer Ordner**.</span><span class="sxs-lookup"><span data-stu-id="c1854-486">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="c1854-487">Geben Sie dem Ordner den Namen *Models* .</span><span class="sxs-lookup"><span data-stu-id="c1854-487">Name the folder *Models*.</span></span>

* <span data-ttu-id="c1854-488">Klicken Sie mit der rechten Maustaste auf den Ordner *Models* , und wählen Sie **Hinzufügen** > **Klasse** aus.</span><span class="sxs-lookup"><span data-stu-id="c1854-488">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="c1854-489">Nennen Sie die Klasse *TodoItem*, und wählen Sie **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="c1854-489">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="c1854-490">Ersetzen Sie den Vorlagencode durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="c1854-490">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="c1854-491">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c1854-491">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="c1854-492">Fügen Sie einen Ordner mit dem Namen *Models* hinzu.</span><span class="sxs-lookup"><span data-stu-id="c1854-492">Add a folder named *Models*.</span></span>

* <span data-ttu-id="c1854-493">Fügen Sie dem Ordner *Models* mit dem folgenden Code eine `TodoItem`-Klasse hinzu:</span><span class="sxs-lookup"><span data-stu-id="c1854-493">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="c1854-494">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="c1854-494">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="c1854-495">Klicken Sie mit der rechten Maustaste auf das Projekt.</span><span class="sxs-lookup"><span data-stu-id="c1854-495">Right-click the project.</span></span> <span data-ttu-id="c1854-496">Klicken Sie auf **Hinzufügen** > **Neuer Ordner**.</span><span class="sxs-lookup"><span data-stu-id="c1854-496">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="c1854-497">Geben Sie dem Ordner den Namen *Models* .</span><span class="sxs-lookup"><span data-stu-id="c1854-497">Name the folder *Models*.</span></span>

  ![Neuer Ordner](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="c1854-499">Klicken Sie mit der rechten Maustaste auf den Ordner *Models* , und wählen Sie **Hinzufügen** > **Neue Datei** > **Allgemein** > **Leere Klasse** aus.</span><span class="sxs-lookup"><span data-stu-id="c1854-499">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="c1854-500">Nennen Sie die Klasse *TodoItem*, und klicken Sie dann auf **Neu**.</span><span class="sxs-lookup"><span data-stu-id="c1854-500">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="c1854-501">Ersetzen Sie den Vorlagencode durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="c1854-501">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs?name=snippet)]

<span data-ttu-id="c1854-502">Die `Id`-Eigenschaft fungiert als eindeutiger Schlüssel in einer relationalen Datenbank.</span><span class="sxs-lookup"><span data-stu-id="c1854-502">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="c1854-503">Modellklassen können überall im Projekt platziert werden, doch gemäß der Konvention wird der Ordner *Models* verwendet.</span><span class="sxs-lookup"><span data-stu-id="c1854-503">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="c1854-504">Hinzufügen eines Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="c1854-504">Add a database context</span></span>

<span data-ttu-id="c1854-505">Der *Datenbankkontext* ist die Hauptklasse, die die Entity Framework-Funktionen für ein Datenmodell koordiniert.</span><span class="sxs-lookup"><span data-stu-id="c1854-505">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="c1854-506">Diese Klasse wird durch Ableiten von der `Microsoft.EntityFrameworkCore.DbContext`-Klasse erstellt.</span><span class="sxs-lookup"><span data-stu-id="c1854-506">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c1854-507">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c1854-507">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-nuget-packages"></a><span data-ttu-id="c1854-508">Hinzufügen von NuGet-Paketen</span><span class="sxs-lookup"><span data-stu-id="c1854-508">Add NuGet packages</span></span>

* <span data-ttu-id="c1854-509">Klicken Sie im Menü **Extras** auf **NuGet-Paket-Manager > NuGet-Pakete für Projektmappe verwalten...** .</span><span class="sxs-lookup"><span data-stu-id="c1854-509">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="c1854-510">Klicken Sie auf die Registerkarte **Durchsuchen**, und geben Sie dann **Microsoft.EntityFrameworkCore.SqlServer** in das Suchfeld ein.</span><span class="sxs-lookup"><span data-stu-id="c1854-510">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.SqlServer** in the search box.</span></span>
* <span data-ttu-id="c1854-511">Wählen Sie im linken Bereich **Microsoft.EntityFrameworkCore.SqlServer** aus.</span><span class="sxs-lookup"><span data-stu-id="c1854-511">Select **Microsoft.EntityFrameworkCore.SqlServer** in the left pane.</span></span>
* <span data-ttu-id="c1854-512">Aktivieren Sie das Kontrollkästchen **Projekt** im rechten Bereich, und klicken Sie dann auf **Installieren**.</span><span class="sxs-lookup"><span data-stu-id="c1854-512">Select the **Project** check box in the right pane and then select **Install**.</span></span>
* <span data-ttu-id="c1854-513">Verwenden Sie die Anweisungen oben zum Hinzuzufügen des **Microsoft.EntityFrameworkCore.InMemory**-NuGet-Pakets.</span><span class="sxs-lookup"><span data-stu-id="c1854-513">Use the preceding instructions to add the **Microsoft.EntityFrameworkCore.InMemory** NuGet package.</span></span>

![NuGet-Paket-Manager](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="c1854-515">Hinzufügen des TodoContext-Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="c1854-515">Add the TodoContext database context</span></span>

* <span data-ttu-id="c1854-516">Klicken Sie mit der rechten Maustaste auf den Ordner *Models* , und wählen Sie **Hinzufügen** > **Klasse** aus.</span><span class="sxs-lookup"><span data-stu-id="c1854-516">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="c1854-517">Nennen Sie die Klasse *TodoContext*, und klicken Sie auf **Hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="c1854-517">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="c1854-518">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="c1854-518">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="c1854-519">Fügen Sie eine `TodoContext`-Klasse zum Ordner *Models* hinzu.</span><span class="sxs-lookup"><span data-stu-id="c1854-519">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="c1854-520">Geben Sie den folgenden Code ein:</span><span class="sxs-lookup"><span data-stu-id="c1854-520">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="c1854-521">Registrieren des Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="c1854-521">Register the database context</span></span>

<span data-ttu-id="c1854-522">In ASP.NET Core müssen Dienste wie der Datenbankkontext mit dem Container [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) registriert werden.</span><span class="sxs-lookup"><span data-stu-id="c1854-522">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="c1854-523">Der Container stellt den Dienst für Controller bereit.</span><span class="sxs-lookup"><span data-stu-id="c1854-523">The container provides the service to controllers.</span></span>

<span data-ttu-id="c1854-524">Aktualisieren Sie *Startup.cs* mit dem folgenden hervorgehobenen Code:</span><span class="sxs-lookup"><span data-stu-id="c1854-524">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="c1854-525">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="c1854-525">The preceding code:</span></span>

* <span data-ttu-id="c1854-526">Entfernt nicht benötigte `using`-Deklarationen</span><span class="sxs-lookup"><span data-stu-id="c1854-526">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="c1854-527">Fügt dem Abhängigkeitscontainer den Datenbankkontext hinzu</span><span class="sxs-lookup"><span data-stu-id="c1854-527">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="c1854-528">Gibt an, dass der Datenbankkontext eine In-Memory Database verwendet</span><span class="sxs-lookup"><span data-stu-id="c1854-528">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="c1854-529">Erstellen eines Controllergerüsts</span><span class="sxs-lookup"><span data-stu-id="c1854-529">Scaffold a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c1854-530">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c1854-530">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c1854-531">Klicken Sie mit der rechten Maustaste auf den Ordner *Controller*.</span><span class="sxs-lookup"><span data-stu-id="c1854-531">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="c1854-532">Wählen Sie **Hinzufügen** > **Neues Gerüstelement** aus.</span><span class="sxs-lookup"><span data-stu-id="c1854-532">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="c1854-533">Klicken Sie auf **API-Controller mit Aktionen unter Verwendung von Entity Framework** und dann auf **Hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="c1854-533">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="c1854-534">Führen Sie im Dialogfeld **API-Controller mit Aktionen unter Verwendung von Entity Framework** folgende Schritte aus:</span><span class="sxs-lookup"><span data-stu-id="c1854-534">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="c1854-535">Wählen Sie in der **Modellklasse** das Element **TodoItem (TodoApi.Models)** aus.</span><span class="sxs-lookup"><span data-stu-id="c1854-535">Select **TodoItem (TodoApi.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="c1854-536">Wählen Sie in der **Datenkontextklasse** das Element **TodoContext (TodoApi.Models)** aus.</span><span class="sxs-lookup"><span data-stu-id="c1854-536">Select **TodoContext (TodoApi.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="c1854-537">Wählen Sie **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="c1854-537">Select **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="c1854-538">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="c1854-538">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="c1854-539">Führen Sie die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="c1854-539">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet tool update -g Dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="c1854-540">Die obenstehenden Befehle haben folgende Konsequenzen:</span><span class="sxs-lookup"><span data-stu-id="c1854-540">The preceding commands:</span></span>

* <span data-ttu-id="c1854-541">Die NuGet-Pakete für den Gerüstbau werden hinzufügt.</span><span class="sxs-lookup"><span data-stu-id="c1854-541">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="c1854-542">Die Gerüstbau-Engine (`dotnet-aspnet-codegenerator`) wird installiert.</span><span class="sxs-lookup"><span data-stu-id="c1854-542">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="c1854-543">Das Gerüst für `TodoItemsController` wird erstellt.</span><span class="sxs-lookup"><span data-stu-id="c1854-543">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="c1854-544">Der generierte Code hat folgende Auswirkungen:</span><span class="sxs-lookup"><span data-stu-id="c1854-544">The generated code:</span></span>

* <span data-ttu-id="c1854-545">Markiert die Klasse mit dem [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute)-Attribut.</span><span class="sxs-lookup"><span data-stu-id="c1854-545">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="c1854-546">Dieses Attribut gibt an, dass der Controller auf Web-API-Anforderungen reagiert.</span><span class="sxs-lookup"><span data-stu-id="c1854-546">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="c1854-547">Weitere Informationen zu bestimmten Verhaltensweisen, die das Attribut ermöglicht, finden Sie unter <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="c1854-547">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="c1854-548">Verwendet die Abhängigkeitsinjektion, um den Datenbankkontext (`TodoContext`) dem Controller hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="c1854-548">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="c1854-549">Der Datenbankkontext wird in den einzelnen [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete)-Methoden im Controller verwendet.</span><span class="sxs-lookup"><span data-stu-id="c1854-549">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<span data-ttu-id="c1854-550">Die ASP.NET Core-Vorlagen für:</span><span class="sxs-lookup"><span data-stu-id="c1854-550">The ASP.NET Core templates for:</span></span>

* <span data-ttu-id="c1854-551">Controller mit Ansichten enthalten `[action]` in der Routenvorlage.</span><span class="sxs-lookup"><span data-stu-id="c1854-551">Controllers with views include `[action]` in the route template.</span></span>
* <span data-ttu-id="c1854-552">API-Controller enthalten keine `[action]` in der Routenvorlage.</span><span class="sxs-lookup"><span data-stu-id="c1854-552">API controllers don't include `[action]` in the route template.</span></span>

<span data-ttu-id="c1854-553">Wenn sich das `[action]`-Token nicht in der Routenvorlage befindet, wird der [action](xref:mvc/controllers/routing#action)-Name von der Route ausgeschlossen.</span><span class="sxs-lookup"><span data-stu-id="c1854-553">When the `[action]` token isn't in the route template, the [action](xref:mvc/controllers/routing#action) name is excluded from the route.</span></span> <span data-ttu-id="c1854-554">Dies bedeutet, dass der zugehörige Methodenname der Aktion nicht in der übereinstimmenden Route verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="c1854-554">That is, the action's associated method name isn't used in the matching route.</span></span>

## <a name="examine-the-posttodoitem-create-method"></a><span data-ttu-id="c1854-555">Überblick über die PostTodoItem-Erstellungsmethode</span><span class="sxs-lookup"><span data-stu-id="c1854-555">Examine the PostTodoItem create method</span></span>

<span data-ttu-id="c1854-556">Ersetzen Sie die Rückgabeanweisung in `PostTodoItem` durch eine Anweisung mit dem [nameof](/dotnet/csharp/language-reference/operators/nameof)-Operator:</span><span class="sxs-lookup"><span data-stu-id="c1854-556">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="c1854-557">Der oben stehende Code ist eine HTTP POST-Methode, wie durch das [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute)-Attribut angegeben.</span><span class="sxs-lookup"><span data-stu-id="c1854-557">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="c1854-558">Die Methode ruft den Wert der Aufgabe aus dem Text der HTTP-Anforderung ab.</span><span class="sxs-lookup"><span data-stu-id="c1854-558">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="c1854-559">Weitere Informationen finden Sie unter [Attributrouting mit Http[Verb]-Attributen](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="c1854-559">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="c1854-560">Die <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*>-Methode:</span><span class="sxs-lookup"><span data-stu-id="c1854-560">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="c1854-561">Gibt den HTTP-Statuscode 201 zurück, wenn der Vorgang erfolgreich ist.</span><span class="sxs-lookup"><span data-stu-id="c1854-561">Returns an HTTP 201 status code if successful.</span></span> <span data-ttu-id="c1854-562">HTTP 201 ist die Standardantwort für eine HTTP POST-Methode, die eine neue Ressource auf dem Server erstellt.</span><span class="sxs-lookup"><span data-stu-id="c1854-562">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="c1854-563">Fügt der Antwort einen [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location)-Header hinzu.</span><span class="sxs-lookup"><span data-stu-id="c1854-563">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="c1854-564">Der `Location`-Header legt den [URI](https://developer.mozilla.org/docs/Glossary/URI) des neu erstellten To-do-Elements fest.</span><span class="sxs-lookup"><span data-stu-id="c1854-564">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="c1854-565">Weitere Informationen finden Sie unter [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="c1854-565">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="c1854-566">Verweist auf die `GetTodoItem`-Aktion zum Erstellen des URIs des `Location`-Headers.</span><span class="sxs-lookup"><span data-stu-id="c1854-566">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="c1854-567">Das `nameof`-Schlüsselwort von C# wird verwendet, um eine Hartcodierung des Aktionsnamens im `CreatedAtAction`-Aufruf zu vermeiden.</span><span class="sxs-lookup"><span data-stu-id="c1854-567">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="c1854-568">Installieren von Postman</span><span class="sxs-lookup"><span data-stu-id="c1854-568">Install Postman</span></span>

<span data-ttu-id="c1854-569">Dieses Tutorial verwendet Postman zum Testen der Web-API.</span><span class="sxs-lookup"><span data-stu-id="c1854-569">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="c1854-570">Installieren Sie [Postman](https://www.getpostman.com/downloads/).</span><span class="sxs-lookup"><span data-stu-id="c1854-570">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="c1854-571">Starten Sie die Web-App.</span><span class="sxs-lookup"><span data-stu-id="c1854-571">Start the web app.</span></span>
* <span data-ttu-id="c1854-572">Starten Sie Postman.</span><span class="sxs-lookup"><span data-stu-id="c1854-572">Start Postman.</span></span>
* <span data-ttu-id="c1854-573">Deaktivieren Sie **SSL certificate verification** (Verifizierung des SSL-Zertifikats).</span><span class="sxs-lookup"><span data-stu-id="c1854-573">Disable **SSL certificate verification**</span></span>
  * <span data-ttu-id="c1854-574">Deaktivieren Sie auf der Registerkarte **General** (Allgemein) unter File > Settings (**Datei** > **Einstellungen**) **SSL certificate verification** (Verifizierung des SSL-Zertifikats).</span><span class="sxs-lookup"><span data-stu-id="c1854-574">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="c1854-575">Aktivieren Sie die Verifizierung des SSL-Zertifikats wieder, nachdem Sie den Controller getestet haben.</span><span class="sxs-lookup"><span data-stu-id="c1854-575">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="c1854-576">Testen von PostTodoItem mit Postman</span><span class="sxs-lookup"><span data-stu-id="c1854-576">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="c1854-577">Erstellen Sie eine neue Anforderung.</span><span class="sxs-lookup"><span data-stu-id="c1854-577">Create a new request.</span></span>
* <span data-ttu-id="c1854-578">Legen Sie als HTTP-Methode `POST` fest.</span><span class="sxs-lookup"><span data-stu-id="c1854-578">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="c1854-579">Legen Sie den URI auf `https://localhost:<port>/api/TodoItems` fest.</span><span class="sxs-lookup"><span data-stu-id="c1854-579">Set the URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="c1854-580">Beispiel: `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="c1854-580">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="c1854-581">Wählen Sie die Registerkarte **Body** (Text) aus.</span><span class="sxs-lookup"><span data-stu-id="c1854-581">Select the **Body** tab.</span></span>
* <span data-ttu-id="c1854-582">Wählen Sie das Optionsfeld **raw** (Unformatiert) aus.</span><span class="sxs-lookup"><span data-stu-id="c1854-582">Select the **raw** radio button.</span></span>
* <span data-ttu-id="c1854-583">Legen Sie den Typ auf **JSON (application/json)** fest.</span><span class="sxs-lookup"><span data-stu-id="c1854-583">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="c1854-584">Geben Sie für die Aufgabe den Anforderungstext im JSON-Format ein:</span><span class="sxs-lookup"><span data-stu-id="c1854-584">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="c1854-585">Wählen Sie **Send** (Senden) aus.</span><span class="sxs-lookup"><span data-stu-id="c1854-585">Select **Send**.</span></span>

  ![Postman mit erstellter Anforderung](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri-with-postman"></a><span data-ttu-id="c1854-587">Testen des Adressheader-URIs mit Postman</span><span class="sxs-lookup"><span data-stu-id="c1854-587">Test the location header URI with Postman</span></span>

* <span data-ttu-id="c1854-588">Wählen Sie auf der Registerkarte **Header** (Header) den Bereich **Response** (Antwort) aus.</span><span class="sxs-lookup"><span data-stu-id="c1854-588">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="c1854-589">Kopieren Sie den den Headerwert von **Location** (Speicherort):</span><span class="sxs-lookup"><span data-stu-id="c1854-589">Copy the **Location** header value:</span></span>

  ![Registerkarte „Header“ in der Postman-Konsole](first-web-api/_static/3/create.png)

* <span data-ttu-id="c1854-591">Legen Sie als HTTP-Methode `GET` fest.</span><span class="sxs-lookup"><span data-stu-id="c1854-591">Set the HTTP method to `GET`.</span></span>
* <span data-ttu-id="c1854-592">Legen Sie den URI auf `https://localhost:<port>/api/TodoItems/1` fest.</span><span class="sxs-lookup"><span data-stu-id="c1854-592">Set the URI to `https://localhost:<port>/api/TodoItems/1`.</span></span> <span data-ttu-id="c1854-593">Beispiel: `https://localhost:5001/api/TodoItems/1`.</span><span class="sxs-lookup"><span data-stu-id="c1854-593">For example, `https://localhost:5001/api/TodoItems/1`.</span></span>
* <span data-ttu-id="c1854-594">Wählen Sie **Send** (Senden) aus.</span><span class="sxs-lookup"><span data-stu-id="c1854-594">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="c1854-595">Überblick über die GET-Methoden</span><span class="sxs-lookup"><span data-stu-id="c1854-595">Examine the GET methods</span></span>

<span data-ttu-id="c1854-596">Diese Methoden implementieren zwei GET-Endpunkte:</span><span class="sxs-lookup"><span data-stu-id="c1854-596">These methods implement two GET endpoints:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="c1854-597">Testen Sie die App, indem Sie die beiden Endpunkte in einem Browser oder über Postman aufrufen.</span><span class="sxs-lookup"><span data-stu-id="c1854-597">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="c1854-598">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="c1854-598">For example:</span></span>

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

<span data-ttu-id="c1854-599">Durch einen Aufruf von `GetTodoItems` wird eine Antwort gesendet, die der folgenden ähnelt:</span><span class="sxs-lookup"><span data-stu-id="c1854-599">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="c1854-600">Testen von GET mit Postman</span><span class="sxs-lookup"><span data-stu-id="c1854-600">Test Get with Postman</span></span>

* <span data-ttu-id="c1854-601">Erstellen Sie eine neue Anforderung.</span><span class="sxs-lookup"><span data-stu-id="c1854-601">Create a new request.</span></span>
* <span data-ttu-id="c1854-602">Legen Sie die HTTP-Methode auf **GET** fest.</span><span class="sxs-lookup"><span data-stu-id="c1854-602">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="c1854-603">Legen Sie den Anforderungs-URI auf `https://localhost:<port>/api/TodoItems` fest.</span><span class="sxs-lookup"><span data-stu-id="c1854-603">Set the request URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="c1854-604">Beispielsweise `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="c1854-604">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="c1854-605">Wählen Sie in Postman **Two pane view** (Ansicht mit zwei Bereichen) aus.</span><span class="sxs-lookup"><span data-stu-id="c1854-605">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="c1854-606">Wählen Sie **Send** (Senden) aus.</span><span class="sxs-lookup"><span data-stu-id="c1854-606">Select **Send**.</span></span>

<span data-ttu-id="c1854-607">Diese App verwendet eine In-Memory-Datenbank.</span><span class="sxs-lookup"><span data-stu-id="c1854-607">This app uses an in-memory database.</span></span> <span data-ttu-id="c1854-608">Wenn die App angehalten und dann wieder gestartet wird, werden durch die vorherige GET-Anforderung keine Daten zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="c1854-608">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="c1854-609">Wenn keine Daten zurückgegeben werden, senden Sie mit [POST](#post) Daten an die App.</span><span class="sxs-lookup"><span data-stu-id="c1854-609">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="c1854-610">Routing und URL-Pfade</span><span class="sxs-lookup"><span data-stu-id="c1854-610">Routing and URL paths</span></span>

<span data-ttu-id="c1854-611">Das Attribut [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) gibt eine Methode an, die auf eine HTTP GET-Anforderung antwortet.</span><span class="sxs-lookup"><span data-stu-id="c1854-611">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="c1854-612">Der URL-Pfad für jede Methode wird wie folgt erstellt:</span><span class="sxs-lookup"><span data-stu-id="c1854-612">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="c1854-613">Beginnen Sie mit der Vorlagenzeichenfolge im `Route`-Attribut des Controllers:</span><span class="sxs-lookup"><span data-stu-id="c1854-613">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="c1854-614">Ersetzen Sie `[controller]` durch den Namen des Controllers, bei dem es sich standardmäßig um den Namen der Controller-Klasse ohne das Suffix „Controller“ handelt.</span><span class="sxs-lookup"><span data-stu-id="c1854-614">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="c1854-615">In diesem Beispiel ist der Klassenname des Controllers „**TodoItems** Controller“. Der Controllername lautet also „TodoItems“.</span><span class="sxs-lookup"><span data-stu-id="c1854-615">For this sample, the controller class name is **TodoItems** Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="c1854-616">Beim ASP.NET Core-[Routing](xref:mvc/controllers/routing) wird die Groß-/Kleinschreibung nicht beachtet.</span><span class="sxs-lookup"><span data-stu-id="c1854-616">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="c1854-617">Wenn das `[HttpGet]`-Attribut eine Routenvorlage (z. B. `[HttpGet("products")]`) hat, fügen Sie diese an den Pfad an.</span><span class="sxs-lookup"><span data-stu-id="c1854-617">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="c1854-618">In diesem Beispiel wird keine Vorlage verwendet.</span><span class="sxs-lookup"><span data-stu-id="c1854-618">This sample doesn't use a template.</span></span> <span data-ttu-id="c1854-619">Weitere Informationen finden Sie unter [Attributrouting mit Http[Verb]-Attributen](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="c1854-619">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="c1854-620">In der folgenden `GetTodoItem`-Methode ist `"{id}"` eine Platzhaltervariable für den eindeutigen Bezeichner des To-do-Elements.</span><span class="sxs-lookup"><span data-stu-id="c1854-620">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="c1854-621">Wenn `GetTodoItem` aufgerufen wird, wird der Wert von `"{id}"` in der URL der Methode in ihrem Parameter `id` bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="c1854-621">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="c1854-622">Rückgabewerte</span><span class="sxs-lookup"><span data-stu-id="c1854-622">Return values</span></span> 

<span data-ttu-id="c1854-623">Der Rückgabetyp der Methoden `GetTodoItems` und `GetTodoItem` ist der [ActionResult\<T>-Typ](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="c1854-623">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="c1854-624">ASP.NET Core serialisiert automatisch das Objekt in [JSON](https://www.json.org/) und schreibt den JSON-Code in den Text der Antwortnachricht.</span><span class="sxs-lookup"><span data-stu-id="c1854-624">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="c1854-625">Der Antwortcode für diesen Rückgabetyp ist 200, vorausgesetzt, es gibt keine Ausnahmefehler.</span><span class="sxs-lookup"><span data-stu-id="c1854-625">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="c1854-626">Nicht behandelte Ausnahmen werden in 5xx-Fehler übersetzt.</span><span class="sxs-lookup"><span data-stu-id="c1854-626">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="c1854-627">`ActionResult`-Rückgabetypen können eine Vielzahl von HTTP-Statuscodes darstellen.</span><span class="sxs-lookup"><span data-stu-id="c1854-627">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="c1854-628">Beispielsweise kann `GetTodoItem` zwei verschiedene Statuswerte zurückgeben:</span><span class="sxs-lookup"><span data-stu-id="c1854-628">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="c1854-629">Wenn kein Element mit der angeforderten ID übereinstimmt, gibt die Methode einen 404-Fehlercode <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> zurück.</span><span class="sxs-lookup"><span data-stu-id="c1854-629">If no item matches the requested ID, the method returns a 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="c1854-630">Andernfalls gibt die Methode 200 mit einem JSON-Antworttext zurück.</span><span class="sxs-lookup"><span data-stu-id="c1854-630">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="c1854-631">Die Rückgabe von `item` löst eine HTTP 200-Antwort aus.</span><span class="sxs-lookup"><span data-stu-id="c1854-631">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="c1854-632">PutTodoItem-Methode</span><span class="sxs-lookup"><span data-stu-id="c1854-632">The PutTodoItem method</span></span>

<span data-ttu-id="c1854-633">Untersuchen Sie die `PutTodoItem`-Methode.</span><span class="sxs-lookup"><span data-stu-id="c1854-633">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="c1854-634">`PutTodoItem` ähnelt `PostTodoItem`, verwendet allerdings HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="c1854-634">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="c1854-635">Die Antwort ist [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="c1854-635">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="c1854-636">Gemäß der HTTP-Spezifikation erfordert eine PUT-Anforderung, dass der Client die gesamte aktualisierte Entität (nicht nur die Änderungen) sendet.</span><span class="sxs-lookup"><span data-stu-id="c1854-636">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="c1854-637">Verwenden Sie [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute), um Teilupdates zu unterstützen.</span><span class="sxs-lookup"><span data-stu-id="c1854-637">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="c1854-638">Wenn beim Aufrufen von `PutTodoItem` ein Fehler zurückgegeben wird, rufen Sie `GET` auf, um sicherzustellen, dass die Datenbank ein Element enthält.</span><span class="sxs-lookup"><span data-stu-id="c1854-638">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="c1854-639">Testen der PutTodoItem-Methode</span><span class="sxs-lookup"><span data-stu-id="c1854-639">Test the PutTodoItem method</span></span>

<span data-ttu-id="c1854-640">In diesem Beispiel wird eine In-Memory-Datenbank verwendet, die jedes Mal initialisiert werden muss, wenn die App gestartet wird.</span><span class="sxs-lookup"><span data-stu-id="c1854-640">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="c1854-641">Es muss ein Element in der Datenbank vorhanden sein, bevor Sie einen PUT-Aufruf durchführen.</span><span class="sxs-lookup"><span data-stu-id="c1854-641">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="c1854-642">Rufen Sie vor einem PUT-Aufruf GET auf, um sicherzustellen, dass ein Element in der Datenbank vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="c1854-642">Call GET to ensure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="c1854-643">Aktualisieren Sie das To-do-Element, das über den ID-Wert 1 verfügt, und legen Sie als Namen „feed fish“ fest:</span><span class="sxs-lookup"><span data-stu-id="c1854-643">Update the to-do item that has Id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "id":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="c1854-644">Die folgende Abbildung zeigt das Postman-Update:</span><span class="sxs-lookup"><span data-stu-id="c1854-644">The following image shows the Postman update:</span></span>

![Postman-Konsole mit der Antwort „204 (Kein Inhalt)“](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="c1854-646">DeleteTodoItem-Methode</span><span class="sxs-lookup"><span data-stu-id="c1854-646">The DeleteTodoItem method</span></span>

<span data-ttu-id="c1854-647">Untersuchen Sie die `DeleteTodoItem`-Methode.</span><span class="sxs-lookup"><span data-stu-id="c1854-647">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="c1854-648">Testen der DeleteTodoItem-Methode</span><span class="sxs-lookup"><span data-stu-id="c1854-648">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="c1854-649">So löschen Sie mit Postman eine Aufgabe</span><span class="sxs-lookup"><span data-stu-id="c1854-649">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="c1854-650">Legen Sie die Methode auf `DELETE` fest.</span><span class="sxs-lookup"><span data-stu-id="c1854-650">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="c1854-651">Legen Sie den URI des zu löschenden Objekts fest, z. B. `https://localhost:5001/api/TodoItems/1`.</span><span class="sxs-lookup"><span data-stu-id="c1854-651">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="c1854-652">Wählen Sie **Send** (Senden) aus.</span><span class="sxs-lookup"><span data-stu-id="c1854-652">Select **Send**.</span></span>

<a name="over-post"></a>
<a name="over-post-v3"></a>

## <a name="prevent-over-posting"></a><span data-ttu-id="c1854-653">Vermeiden von Overposting</span><span class="sxs-lookup"><span data-stu-id="c1854-653">Prevent over-posting</span></span>

<span data-ttu-id="c1854-654">Derzeit macht die Beispiel-App das gesamte `TodoItem`-Objekt verfügbar.</span><span class="sxs-lookup"><span data-stu-id="c1854-654">Currently the sample app exposes the entire `TodoItem` object.</span></span> <span data-ttu-id="c1854-655">In den Produktions-Apps sind die Daten, die eingegeben und mithilfe einer Teilmenge des Modells zurückgegeben werden, in der Regel eingeschränkt.</span><span class="sxs-lookup"><span data-stu-id="c1854-655">Production apps typically limit the data that's input and returned using a subset of the model.</span></span> <span data-ttu-id="c1854-656">Hierfür gibt es mehrere Gründe, wobei die Sicherheit einer der Hauptgründe ist.</span><span class="sxs-lookup"><span data-stu-id="c1854-656">There are multiple reasons behind this and security is a major one.</span></span> <span data-ttu-id="c1854-657">Die Teilmenge eines Modells wird üblicherweise als Datenübertragungsobjekt (DTO, Data Transfer Object), Eingabemodell oder Anzeigemodell bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="c1854-657">The subset of a model is usually referred to as a Data Transfer Object (DTO), input model, or view model.</span></span> <span data-ttu-id="c1854-658">In diesem Artikel wird das **DTO** verwendet.</span><span class="sxs-lookup"><span data-stu-id="c1854-658">**DTO** is used in this article.</span></span>

<span data-ttu-id="c1854-659">Ein DTO kann für Folgendes verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="c1854-659">A DTO may be used to:</span></span>

* <span data-ttu-id="c1854-660">Vermeiden Sie Overposting.</span><span class="sxs-lookup"><span data-stu-id="c1854-660">Prevent over-posting.</span></span>
* <span data-ttu-id="c1854-661">Ausblenden von Eigenschaften, die Clients nicht anzeigen sollen</span><span class="sxs-lookup"><span data-stu-id="c1854-661">Hide properties that clients are not supposed to view.</span></span>
* <span data-ttu-id="c1854-662">Auslassen einiger Eigenschaften, um die Nutzlast zu verringern</span><span class="sxs-lookup"><span data-stu-id="c1854-662">Omit some properties in order to reduce payload size.</span></span>
* <span data-ttu-id="c1854-663">Vereinfachen von Objektgraphen, die geschachtelte Objekte enthalten</span><span class="sxs-lookup"><span data-stu-id="c1854-663">Flatten object graphs that contain nested objects.</span></span> <span data-ttu-id="c1854-664">Vereinfachte Objektgraphen können für Clients zweckmäßiger sein.</span><span class="sxs-lookup"><span data-stu-id="c1854-664">Flattened object graphs can be more convenient for clients.</span></span>

<span data-ttu-id="c1854-665">Um den DTO-Ansatz zu veranschaulichen, aktualisieren Sie die `TodoItem`-Klasse, sodass sie ein geheimes Feld einschließt:</span><span class="sxs-lookup"><span data-stu-id="c1854-665">To demonstrate the DTO approach, update the `TodoItem` class to include a secret field:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItem.cs?name=snippet&highlight=6)]

<span data-ttu-id="c1854-666">Das geheime Feld muss in dieser App ausgeblendet werden, eine administrative App kann es jedoch verfügbar machen.</span><span class="sxs-lookup"><span data-stu-id="c1854-666">The secret field needs to be hidden from this app, but an administrative app could choose to expose it.</span></span>

<span data-ttu-id="c1854-667">Vergewissern Sie sich, dass Sie das geheime Feld veröffentlichen und abrufen können.</span><span class="sxs-lookup"><span data-stu-id="c1854-667">Verify you can post and get the secret field.</span></span>

<span data-ttu-id="c1854-668">Erstellen Sie ein DTO-Modell:</span><span class="sxs-lookup"><span data-stu-id="c1854-668">Create a DTO model:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItemDTO.cs?name=snippet)]

<span data-ttu-id="c1854-669">Aktualisieren Sie `TodoItemsController`, sodass `TodoItemDTO` verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="c1854-669">Update the `TodoItemsController` to use `TodoItemDTO`:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

<span data-ttu-id="c1854-670">Vergewissern Sie sich, dass Sie das geheime Feld weder veröffentlichen noch abrufen können.</span><span class="sxs-lookup"><span data-stu-id="c1854-670">Verify you can't post or get the secret field.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="c1854-671">Aufrufen der Web-API mit JavaScript</span><span class="sxs-lookup"><span data-stu-id="c1854-671">Call the web API with JavaScript</span></span>

<span data-ttu-id="c1854-672">Mehr dazu finden Sie im [-Tutorial: Aufrufen einer ASP.NET Core-Web-API mit JavaScript](xref:tutorials/web-api-javascript).</span><span class="sxs-lookup"><span data-stu-id="c1854-672">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="c1854-673">In diesem Tutorial lernen Sie, wie die folgenden Aufgaben ausgeführt werden:</span><span class="sxs-lookup"><span data-stu-id="c1854-673">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="c1854-674">Erstellen eines Web-API-Projekts</span><span class="sxs-lookup"><span data-stu-id="c1854-674">Create a web API project.</span></span>
> * <span data-ttu-id="c1854-675">Hinzufügen einer Modellklasse und eines Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="c1854-675">Add a model class and a database context.</span></span>
> * <span data-ttu-id="c1854-676">Hinzufügen eines Controllers</span><span class="sxs-lookup"><span data-stu-id="c1854-676">Add a controller.</span></span>
> * <span data-ttu-id="c1854-677">Hinzufügen von CRUD-Methoden</span><span class="sxs-lookup"><span data-stu-id="c1854-677">Add CRUD methods.</span></span>
> * <span data-ttu-id="c1854-678">Konfigurieren von Routing und URL-Pfaden</span><span class="sxs-lookup"><span data-stu-id="c1854-678">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="c1854-679">Angeben von Rückgabewerten</span><span class="sxs-lookup"><span data-stu-id="c1854-679">Specify return values.</span></span>
> * <span data-ttu-id="c1854-680">Aufrufen der Web-API mit Postman</span><span class="sxs-lookup"><span data-stu-id="c1854-680">Call the web API with Postman.</span></span>
> * <span data-ttu-id="c1854-681">Aufrufen der Web-API mit JavaScript</span><span class="sxs-lookup"><span data-stu-id="c1854-681">Call the web API with JavaScript.</span></span>

<span data-ttu-id="c1854-682">Am Ende haben Sie eine Web-API, die in einer relationalen Datenbank gespeicherte „Aufgaben“ verwalten kann.</span><span class="sxs-lookup"><span data-stu-id="c1854-682">At the end, you have a web API that can manage "to-do" items stored in a relational database.</span></span>

## <a name="overview-21"></a><span data-ttu-id="c1854-683">Übersicht 2.1</span><span class="sxs-lookup"><span data-stu-id="c1854-683">Overview 2.1</span></span>

<span data-ttu-id="c1854-684">In diesem Tutorial wird die folgende API erstellt:</span><span class="sxs-lookup"><span data-stu-id="c1854-684">This tutorial creates the following API:</span></span>

|<span data-ttu-id="c1854-685">API</span><span class="sxs-lookup"><span data-stu-id="c1854-685">API</span></span> | <span data-ttu-id="c1854-686">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="c1854-686">Description</span></span> | <span data-ttu-id="c1854-687">Anforderungstext</span><span class="sxs-lookup"><span data-stu-id="c1854-687">Request body</span></span> | <span data-ttu-id="c1854-688">Antworttext</span><span class="sxs-lookup"><span data-stu-id="c1854-688">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="c1854-689">GET /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="c1854-689">GET /api/TodoItems</span></span> | <span data-ttu-id="c1854-690">Alle To-do-Elemente abrufen</span><span class="sxs-lookup"><span data-stu-id="c1854-690">Get all to-do items</span></span> | <span data-ttu-id="c1854-691">Keine</span><span class="sxs-lookup"><span data-stu-id="c1854-691">None</span></span> | <span data-ttu-id="c1854-692">Array von To-do-Elementen</span><span class="sxs-lookup"><span data-stu-id="c1854-692">Array of to-do items</span></span>|
|<span data-ttu-id="c1854-693">GET /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="c1854-693">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="c1854-694">Ein Element nach ID abrufen</span><span class="sxs-lookup"><span data-stu-id="c1854-694">Get an item by ID</span></span> | <span data-ttu-id="c1854-695">Keine</span><span class="sxs-lookup"><span data-stu-id="c1854-695">None</span></span> | <span data-ttu-id="c1854-696">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="c1854-696">To-do item</span></span>|
|<span data-ttu-id="c1854-697">POST /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="c1854-697">POST /api/TodoItems</span></span> | <span data-ttu-id="c1854-698">Neues Element hinzufügen</span><span class="sxs-lookup"><span data-stu-id="c1854-698">Add a new item</span></span> | <span data-ttu-id="c1854-699">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="c1854-699">To-do item</span></span> | <span data-ttu-id="c1854-700">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="c1854-700">To-do item</span></span> |
|<span data-ttu-id="c1854-701">PUT /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="c1854-701">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="c1854-702">Vorhandenes Element aktualisieren &nbsp;</span><span class="sxs-lookup"><span data-stu-id="c1854-702">Update an existing item &nbsp;</span></span> | <span data-ttu-id="c1854-703">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="c1854-703">To-do item</span></span> | <span data-ttu-id="c1854-704">Keine</span><span class="sxs-lookup"><span data-stu-id="c1854-704">None</span></span> |
|<span data-ttu-id="c1854-705">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="c1854-705">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="c1854-706">Löschen eines Elements &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="c1854-706">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="c1854-707">Keine</span><span class="sxs-lookup"><span data-stu-id="c1854-707">None</span></span> | <span data-ttu-id="c1854-708">Keine</span><span class="sxs-lookup"><span data-stu-id="c1854-708">None</span></span>|

<span data-ttu-id="c1854-709">Das folgende Diagramm zeigt den Entwurf der App.</span><span class="sxs-lookup"><span data-stu-id="c1854-709">The following diagram shows the design of the app.</span></span>

![Der Client ist das Feld ganz links.](first-web-api/_static/architecture.png)

## <a name="prerequisites-21"></a><span data-ttu-id="c1854-715">Voraussetzungen 2.1</span><span class="sxs-lookup"><span data-stu-id="c1854-715">Prerequisites 2.1</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c1854-716">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c1854-716">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="c1854-717">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c1854-717">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="c1854-718">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="c1854-718">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project-21"></a><span data-ttu-id="c1854-719">Erstellen eines Webprojekts 2.1</span><span class="sxs-lookup"><span data-stu-id="c1854-719">Create a web project 2.1</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c1854-720">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c1854-720">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c1854-721">Klicken Sie im Menü **Datei** auf **Neu** > **Projekt**.</span><span class="sxs-lookup"><span data-stu-id="c1854-721">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="c1854-722">Wählen Sie die Vorlage **ASP.NET Core-Webanwendung** aus, und klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="c1854-722">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="c1854-723">Geben Sie dem Projekt den Namen *TodoApi*, und klicken Sie auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="c1854-723">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="c1854-724">Vergewissern Sie sich, dass im Dialogfeld **Neue ASP.NET Core-Webanwendung erstellen** die Optionen **.NET Core** und **ASP.NET Core 2.2** ausgewählt sind.</span><span class="sxs-lookup"><span data-stu-id="c1854-724">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 2.2** are selected.</span></span> <span data-ttu-id="c1854-725">Wählen Sie die Vorlage **API** aus, und klicken Sie auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="c1854-725">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="c1854-726">Wählen Sie **nicht** **Enable Docker Support** (Docker-Unterstützung aktivieren) aus.</span><span class="sxs-lookup"><span data-stu-id="c1854-726">**Don't** select **Enable Docker Support**.</span></span>

![VS-Dialogfeld „Neues Projekt“](first-web-api/_static/vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="c1854-728">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c1854-728">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="c1854-729">Öffnen Sie das [integrierte Terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="c1854-729">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="c1854-730">Wechseln Sie mit dem Befehl `cd`' zu dem Ordner, der den Projektordner enthalten soll.</span><span class="sxs-lookup"><span data-stu-id="c1854-730">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="c1854-731">Führen Sie die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="c1854-731">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  <span data-ttu-id="c1854-732">Diese Befehle erstellen ein neues Web-API-Projekt und öffnen eine neue Visual Studio Code-Instanz im neuen Projektordner.</span><span class="sxs-lookup"><span data-stu-id="c1854-732">These commands create a new web API project and open a new instance of Visual Studio Code in the new project folder.</span></span>

* <span data-ttu-id="c1854-733">Wenn Sie in einem Dialogfeld angeben müssen, ob Sie dem Projekt die erforderlichen Elemente hinzufügen möchten, wählen Sie **Ja** aus.</span><span class="sxs-lookup"><span data-stu-id="c1854-733">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="c1854-734">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="c1854-734">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="c1854-735">Klicken Sie auf **Datei** > **Neue Projektmappe**.</span><span class="sxs-lookup"><span data-stu-id="c1854-735">Select **File** > **New Solution**.</span></span>

  ![Neue Projektmappe in macOS](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="c1854-737">Navigieren Sie in Visual Studio für Mac-Versionen vor Version 8.6 zu **.NET Core** > **App** > **API** > **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="c1854-737">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="c1854-738">Bei Version 8.6 oder höher klicken Sie auf **Web and Console** > **App** > **API** > **Weiter** (Web und Konsole).</span><span class="sxs-lookup"><span data-stu-id="c1854-738">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>
  
* <span data-ttu-id="c1854-739">Wählen Sie im Dialogfeld **Neue ASP.NET Core-Web-API konfigurieren** die neueste .NET Core 2.x-Version als **Zielframework** aus.</span><span class="sxs-lookup"><span data-stu-id="c1854-739">In the **Configure the new ASP.NET Core Web API** dialog, select the latest .NET Core 2.x **Target Framework**.</span></span> <span data-ttu-id="c1854-740">Klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="c1854-740">Select **Next**.</span></span>

* <span data-ttu-id="c1854-741">Geben Sie für **Projektname** *TodoApi* ein, und wählen Sie dann **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="c1854-741">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![Dialogfeld „Konfiguration“](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api-21"></a><span data-ttu-id="c1854-743">Testen der API 2.1</span><span class="sxs-lookup"><span data-stu-id="c1854-743">Test the API 2.1</span></span>

<span data-ttu-id="c1854-744">Die Projektvorlage erstellt eine `values`-API.</span><span class="sxs-lookup"><span data-stu-id="c1854-744">The project template creates a `values` API.</span></span> <span data-ttu-id="c1854-745">Rufen Sie in einem Browser die `Get`-Methode zum Testen der App auf.</span><span class="sxs-lookup"><span data-stu-id="c1854-745">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c1854-746">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c1854-746">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="c1854-747">Drücken Sie STRG+F5, um die App auszuführen.</span><span class="sxs-lookup"><span data-stu-id="c1854-747">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="c1854-748">Visual Studio startet einen Browser und navigiert zu `https://localhost:<port>/api/values`, wobei es sich bei `<port>` um eine zufällig ausgewählte Portnummer handelt.</span><span class="sxs-lookup"><span data-stu-id="c1854-748">Visual Studio launches a browser and navigates to `https://localhost:<port>/api/values`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="c1854-749">Wenn Sie in einem Dialogfeld angeben müssen, ob Sie dem IIS Express-Zertifikat vertrauen, wählen Sie **Ja** aus.</span><span class="sxs-lookup"><span data-stu-id="c1854-749">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="c1854-750">Wählen Sie im folgenden Dialogfeld **Sicherheitswarnung** die Option **Ja** aus.</span><span class="sxs-lookup"><span data-stu-id="c1854-750">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="c1854-751">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c1854-751">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="c1854-752">Drücken Sie STRG+F5, um die App auszuführen.</span><span class="sxs-lookup"><span data-stu-id="c1854-752">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="c1854-753">Navigieren Sie in einem Browser zur folgenden URL: `https://localhost:5001/api/values`.</span><span class="sxs-lookup"><span data-stu-id="c1854-753">In a browser, go to following URL: `https://localhost:5001/api/values`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="c1854-754">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="c1854-754">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="c1854-755">Wählen Sie **Ausführen** > **Debugging starten** aus, um die App zu starten.</span><span class="sxs-lookup"><span data-stu-id="c1854-755">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="c1854-756">Visual Studio für Mac startet einen Browser und navigiert zu `https://localhost:<port>`, wobei es sich bei `<port>` um eine zufällig ausgewählte Portnummer handelt.</span><span class="sxs-lookup"><span data-stu-id="c1854-756">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="c1854-757">Der Fehler „HTTP 404: Nicht gefunden“ wird zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="c1854-757">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="c1854-758">Fügen Sie der URL `/api/values` an, d.h. ändern Sie die URL in `https://localhost:<port>/api/values`.</span><span class="sxs-lookup"><span data-stu-id="c1854-758">Append `/api/values` to the URL (change the URL to `https://localhost:<port>/api/values`).</span></span>

---

<span data-ttu-id="c1854-759">Die folgende JSON-Datei wird zurückgegeben:</span><span class="sxs-lookup"><span data-stu-id="c1854-759">The following JSON is returned:</span></span>

```json
["value1","value2"]
```

## <a name="add-a-model-class-21"></a><span data-ttu-id="c1854-760">Hinzufügen einer Modellklasse 2.1</span><span class="sxs-lookup"><span data-stu-id="c1854-760">Add a model class 2.1</span></span>

<span data-ttu-id="c1854-761">Ein *Modell* ist eine Gruppe von Klassen, die die Daten darstellen, die die App verwaltet.</span><span class="sxs-lookup"><span data-stu-id="c1854-761">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="c1854-762">Das Modell für diese App ist eine einzelne `TodoItem`-Klasse.</span><span class="sxs-lookup"><span data-stu-id="c1854-762">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c1854-763">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c1854-763">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c1854-764">Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt.</span><span class="sxs-lookup"><span data-stu-id="c1854-764">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="c1854-765">Klicken Sie auf **Hinzufügen** > **Neuer Ordner**.</span><span class="sxs-lookup"><span data-stu-id="c1854-765">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="c1854-766">Geben Sie dem Ordner den Namen *Models* .</span><span class="sxs-lookup"><span data-stu-id="c1854-766">Name the folder *Models*.</span></span>

* <span data-ttu-id="c1854-767">Klicken Sie mit der rechten Maustaste auf den Ordner *Models* , und wählen Sie **Hinzufügen** > **Klasse** aus.</span><span class="sxs-lookup"><span data-stu-id="c1854-767">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="c1854-768">Nennen Sie die Klasse *TodoItem*, und wählen Sie **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="c1854-768">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="c1854-769">Ersetzen Sie den Vorlagencode durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="c1854-769">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="c1854-770">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c1854-770">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="c1854-771">Fügen Sie einen Ordner mit dem Namen *Models* hinzu.</span><span class="sxs-lookup"><span data-stu-id="c1854-771">Add a folder named *Models*.</span></span>

* <span data-ttu-id="c1854-772">Fügen Sie dem Ordner *Models* mit dem folgenden Code eine `TodoItem`-Klasse hinzu:</span><span class="sxs-lookup"><span data-stu-id="c1854-772">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="c1854-773">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="c1854-773">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="c1854-774">Klicken Sie mit der rechten Maustaste auf das Projekt.</span><span class="sxs-lookup"><span data-stu-id="c1854-774">Right-click the project.</span></span> <span data-ttu-id="c1854-775">Klicken Sie auf **Hinzufügen** > **Neuer Ordner**.</span><span class="sxs-lookup"><span data-stu-id="c1854-775">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="c1854-776">Geben Sie dem Ordner den Namen *Models* .</span><span class="sxs-lookup"><span data-stu-id="c1854-776">Name the folder *Models*.</span></span>

  ![Neuer Ordner](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="c1854-778">Klicken Sie mit der rechten Maustaste auf den Ordner *Models* , und wählen Sie **Hinzufügen** > **Neue Datei** > **Allgemein** > **Leere Klasse** aus.</span><span class="sxs-lookup"><span data-stu-id="c1854-778">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="c1854-779">Nennen Sie die Klasse *TodoItem*, und klicken Sie dann auf **Neu**.</span><span class="sxs-lookup"><span data-stu-id="c1854-779">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="c1854-780">Ersetzen Sie den Vorlagencode durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="c1854-780">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="c1854-781">Die `Id`-Eigenschaft fungiert als eindeutiger Schlüssel in einer relationalen Datenbank.</span><span class="sxs-lookup"><span data-stu-id="c1854-781">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="c1854-782">Modellklassen können überall im Projekt platziert werden, doch gemäß der Konvention wird der Ordner *Models* verwendet.</span><span class="sxs-lookup"><span data-stu-id="c1854-782">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context-21"></a><span data-ttu-id="c1854-783">Hinzufügen eines Datenbankkontexts 2.1</span><span class="sxs-lookup"><span data-stu-id="c1854-783">Add a database context 2.1</span></span>

<span data-ttu-id="c1854-784">Der *Datenbankkontext* ist die Hauptklasse, die die Entity Framework-Funktionen für ein Datenmodell koordiniert.</span><span class="sxs-lookup"><span data-stu-id="c1854-784">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="c1854-785">Diese Klasse wird durch Ableiten von der `Microsoft.EntityFrameworkCore.DbContext`-Klasse erstellt.</span><span class="sxs-lookup"><span data-stu-id="c1854-785">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c1854-786">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c1854-786">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c1854-787">Klicken Sie mit der rechten Maustaste auf den Ordner *Models* , und wählen Sie **Hinzufügen** > **Klasse** aus.</span><span class="sxs-lookup"><span data-stu-id="c1854-787">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="c1854-788">Nennen Sie die Klasse *TodoContext*, und klicken Sie auf **Hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="c1854-788">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="c1854-789">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="c1854-789">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="c1854-790">Fügen Sie eine `TodoContext`-Klasse zum Ordner *Models* hinzu.</span><span class="sxs-lookup"><span data-stu-id="c1854-790">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="c1854-791">Ersetzen Sie den Vorlagencode durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="c1854-791">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context-21"></a><span data-ttu-id="c1854-792">Registrieren des Datenbankkontexts 2.1</span><span class="sxs-lookup"><span data-stu-id="c1854-792">Register the database context 2.1</span></span>

<span data-ttu-id="c1854-793">In ASP.NET Core müssen Dienste wie der Datenbankkontext mit dem Container [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) registriert werden.</span><span class="sxs-lookup"><span data-stu-id="c1854-793">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="c1854-794">Der Container stellt den Dienst für Controller bereit.</span><span class="sxs-lookup"><span data-stu-id="c1854-794">The container provides the service to controllers.</span></span>

<span data-ttu-id="c1854-795">Aktualisieren Sie *Startup.cs* mit dem folgenden hervorgehobenen Code:</span><span class="sxs-lookup"><span data-stu-id="c1854-795">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

<span data-ttu-id="c1854-796">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="c1854-796">The preceding code:</span></span>

* <span data-ttu-id="c1854-797">Entfernt nicht benötigte `using`-Deklarationen</span><span class="sxs-lookup"><span data-stu-id="c1854-797">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="c1854-798">Fügt dem Abhängigkeitscontainer den Datenbankkontext hinzu</span><span class="sxs-lookup"><span data-stu-id="c1854-798">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="c1854-799">Gibt an, dass der Datenbankkontext eine In-Memory Database verwendet</span><span class="sxs-lookup"><span data-stu-id="c1854-799">Specifies that the database context will use an in-memory database.</span></span>

## <a name="add-a-controller-21"></a><span data-ttu-id="c1854-800">Hinzufügen eines Controllers 2.1</span><span class="sxs-lookup"><span data-stu-id="c1854-800">Add a controller 2.1</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c1854-801">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c1854-801">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c1854-802">Klicken Sie mit der rechten Maustaste auf den Ordner *Controller*.</span><span class="sxs-lookup"><span data-stu-id="c1854-802">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="c1854-803">Wählen Sie **Hinzufügen** > **Neues Element** aus.</span><span class="sxs-lookup"><span data-stu-id="c1854-803">Select **Add** > **New Item**.</span></span>
* <span data-ttu-id="c1854-804">Wählen Sie im Dialogfeld **Neues Element hinzufügen** die Vorlage **API-Controllerklasse** aus.</span><span class="sxs-lookup"><span data-stu-id="c1854-804">In the **Add New Item** dialog, select the **API Controller Class** template.</span></span>
* <span data-ttu-id="c1854-805">Benennen Sie die Klasse *TodoController*, und wählen Sie **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="c1854-805">Name the class *TodoController*, and select **Add**.</span></span>

  ![Dialogfeld „Neues Element hinzufügen“ mit Controller im Suchfeld und ausgewähltem Web-API-Controller](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="c1854-807">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="c1854-807">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="c1854-808">Erstellen Sie im Ordner *Controllers* eine Klasse namens `TodoController`.</span><span class="sxs-lookup"><span data-stu-id="c1854-808">In the *Controllers* folder, create a class named `TodoController`.</span></span>

---

* <span data-ttu-id="c1854-809">Ersetzen Sie den Vorlagencode durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="c1854-809">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

<span data-ttu-id="c1854-810">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="c1854-810">The preceding code:</span></span>

* <span data-ttu-id="c1854-811">Definiert eine API-Controllerklasse ohne Methoden.</span><span class="sxs-lookup"><span data-stu-id="c1854-811">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="c1854-812">Markiert die Klasse mit dem [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute)-Attribut.</span><span class="sxs-lookup"><span data-stu-id="c1854-812">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="c1854-813">Dieses Attribut gibt an, dass der Controller auf Web-API-Anforderungen reagiert.</span><span class="sxs-lookup"><span data-stu-id="c1854-813">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="c1854-814">Weitere Informationen zu bestimmten Verhaltensweisen, die das Attribut ermöglicht, finden Sie unter <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="c1854-814">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="c1854-815">Verwendet die Abhängigkeitsinjektion, um den Datenbankkontext (`TodoContext`) dem Controller hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="c1854-815">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="c1854-816">Der Datenbankkontext wird in den einzelnen [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete)-Methoden im Controller verwendet.</span><span class="sxs-lookup"><span data-stu-id="c1854-816">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>
* <span data-ttu-id="c1854-817">Fügt der Datenbank, falls sie leer ist, ein Element mit dem Namen `Item1` hinzu.</span><span class="sxs-lookup"><span data-stu-id="c1854-817">Adds an item named `Item1` to the database if the database is empty.</span></span> <span data-ttu-id="c1854-818">Dieser Code befindet sich im Konstruktor. Er wird bei jeder neuen HTTP-Anforderung ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="c1854-818">This code is in the constructor, so it runs every time there's a new HTTP request.</span></span> <span data-ttu-id="c1854-819">Wenn Sie alle Elemente löschen, erstellt der Konstruktor beim nächsten Aufruf einer API-Methode erneut `Item1`.</span><span class="sxs-lookup"><span data-stu-id="c1854-819">If you delete all items, the constructor creates `Item1` again the next time an API method is called.</span></span> <span data-ttu-id="c1854-820">So sieht es möglicherweise so aus, als sei die Löschung fehlgeschlagen, obwohl sie erfolgreich war.</span><span class="sxs-lookup"><span data-stu-id="c1854-820">So it may look like the deletion didn't work when it actually did work.</span></span>

## <a name="add-get-methods-21"></a><span data-ttu-id="c1854-821">Hinzufügen von GET-Methoden 2.1</span><span class="sxs-lookup"><span data-stu-id="c1854-821">Add Get methods 2.1</span></span>

<span data-ttu-id="c1854-822">Fügen Sie der Klasse `TodoController` die folgenden Methoden hinzu, um eine API bereitzustellen, die Aufgaben abruft:</span><span class="sxs-lookup"><span data-stu-id="c1854-822">To provide an API that retrieves to-do items, add the following methods to the `TodoController` class:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

<span data-ttu-id="c1854-823">Diese Methoden implementieren zwei GET-Endpunkte:</span><span class="sxs-lookup"><span data-stu-id="c1854-823">These methods implement two GET endpoints:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="c1854-824">Halten Sie die App an, falls diese noch ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="c1854-824">Stop the app if it's still running.</span></span> <span data-ttu-id="c1854-825">Führen Sie diese dann noch mal aus, damit die letzten Änderungen übernommen werden.</span><span class="sxs-lookup"><span data-stu-id="c1854-825">Then run it again to include the latest changes.</span></span>

<span data-ttu-id="c1854-826">Testen Sie die App, indem Sie die beiden Endpunkte in einem Browser aufrufen.</span><span class="sxs-lookup"><span data-stu-id="c1854-826">Test the app by calling the two endpoints from a browser.</span></span> <span data-ttu-id="c1854-827">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="c1854-827">For example:</span></span>

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

<span data-ttu-id="c1854-828">Die folgende HTTP-Antwort wird durch den Aufruf von `GetTodoItems` erzeugt:</span><span class="sxs-lookup"><span data-stu-id="c1854-828">The following HTTP response is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths-21"></a><span data-ttu-id="c1854-829">Routing und URL-Pfade 2.1</span><span class="sxs-lookup"><span data-stu-id="c1854-829">Routing and URL paths 2.1</span></span>

<span data-ttu-id="c1854-830">Das Attribut [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) gibt eine Methode an, die auf eine HTTP GET-Anforderung antwortet.</span><span class="sxs-lookup"><span data-stu-id="c1854-830">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="c1854-831">Der URL-Pfad für jede Methode wird wie folgt erstellt:</span><span class="sxs-lookup"><span data-stu-id="c1854-831">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="c1854-832">Beginnen Sie mit der Vorlagenzeichenfolge im `Route`-Attribut des Controllers:</span><span class="sxs-lookup"><span data-stu-id="c1854-832">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* <span data-ttu-id="c1854-833">Ersetzen Sie `[controller]` durch den Namen des Controllers, bei dem es sich standardmäßig um den Namen der Controller-Klasse ohne das Suffix „Controller“ handelt.</span><span class="sxs-lookup"><span data-stu-id="c1854-833">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="c1854-834">Bei diesem Beispiel ist der Klassenname des Controllers „**Todo** Controller“, d.h. der Controllername lautet „todo“.</span><span class="sxs-lookup"><span data-stu-id="c1854-834">For this sample, the controller class name is **Todo** Controller, so the controller name is "todo".</span></span> <span data-ttu-id="c1854-835">Beim ASP.NET Core-[Routing](xref:mvc/controllers/routing) wird die Groß-/Kleinschreibung nicht beachtet.</span><span class="sxs-lookup"><span data-stu-id="c1854-835">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="c1854-836">Wenn das `[HttpGet]`-Attribut eine Routenvorlage (z. B. `[HttpGet("products")]`) hat, fügen Sie diese an den Pfad an.</span><span class="sxs-lookup"><span data-stu-id="c1854-836">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="c1854-837">In diesem Beispiel wird keine Vorlage verwendet.</span><span class="sxs-lookup"><span data-stu-id="c1854-837">This sample doesn't use a template.</span></span> <span data-ttu-id="c1854-838">Weitere Informationen finden Sie unter [Attributrouting mit Http[Verb]-Attributen](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="c1854-838">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="c1854-839">In der folgenden `GetTodoItem`-Methode ist `"{id}"` eine Platzhaltervariable für den eindeutigen Bezeichner des To-do-Elements.</span><span class="sxs-lookup"><span data-stu-id="c1854-839">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="c1854-840">Wenn `GetTodoItem` aufgerufen wird, wird der Wert von `"{id}"` in der URL der Methode in ihrem Parameter `id` bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="c1854-840">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values-21"></a><span data-ttu-id="c1854-841">Rückgabewerte 2.1</span><span class="sxs-lookup"><span data-stu-id="c1854-841">Return values 2.1</span></span>

<span data-ttu-id="c1854-842">Der Rückgabetyp der Methoden `GetTodoItems` und `GetTodoItem` ist der [ActionResult\<T>-Typ](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="c1854-842">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="c1854-843">ASP.NET Core serialisiert automatisch das Objekt in [JSON](https://www.json.org/) und schreibt den JSON-Code in den Text der Antwortnachricht.</span><span class="sxs-lookup"><span data-stu-id="c1854-843">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="c1854-844">Der Antwortcode für diesen Rückgabetyp ist 200, vorausgesetzt, es gibt keine Ausnahmefehler.</span><span class="sxs-lookup"><span data-stu-id="c1854-844">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="c1854-845">Nicht behandelte Ausnahmen werden in 5xx-Fehler übersetzt.</span><span class="sxs-lookup"><span data-stu-id="c1854-845">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="c1854-846">`ActionResult`-Rückgabetypen können eine Vielzahl von HTTP-Statuscodes darstellen.</span><span class="sxs-lookup"><span data-stu-id="c1854-846">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="c1854-847">Beispielsweise kann `GetTodoItem` zwei verschiedene Statuswerte zurückgeben:</span><span class="sxs-lookup"><span data-stu-id="c1854-847">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="c1854-848">Wenn kein Element mit der angeforderten ID übereinstimmt, gibt die Methode einen 404-Fehlercode <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> zurück.</span><span class="sxs-lookup"><span data-stu-id="c1854-848">If no item matches the requested ID, the method returns a 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="c1854-849">Andernfalls gibt die Methode 200 mit einem JSON-Antworttext zurück.</span><span class="sxs-lookup"><span data-stu-id="c1854-849">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="c1854-850">Die Rückgabe von `item` löst eine HTTP 200-Antwort aus.</span><span class="sxs-lookup"><span data-stu-id="c1854-850">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="test-the-gettodoitems-method-21"></a><span data-ttu-id="c1854-851">Testen der GetTodoItems-Methode 2.1</span><span class="sxs-lookup"><span data-stu-id="c1854-851">Test the GetTodoItems method 2.1</span></span>

<span data-ttu-id="c1854-852">Dieses Tutorial verwendet Postman zum Testen der Web-API.</span><span class="sxs-lookup"><span data-stu-id="c1854-852">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="c1854-853">Installieren Sie [Postman](https://www.getpostman.com/downloads/).</span><span class="sxs-lookup"><span data-stu-id="c1854-853">Install [Postman](https://www.getpostman.com/downloads/).</span></span>
* <span data-ttu-id="c1854-854">Starten Sie die Web-App.</span><span class="sxs-lookup"><span data-stu-id="c1854-854">Start the web app.</span></span>
* <span data-ttu-id="c1854-855">Starten Sie Postman.</span><span class="sxs-lookup"><span data-stu-id="c1854-855">Start Postman.</span></span>
* <span data-ttu-id="c1854-856">Deaktivieren Sie **SSL certificate verification** (Verifizierung des SSL-Zertifikats).</span><span class="sxs-lookup"><span data-stu-id="c1854-856">Disable **SSL certificate verification**.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c1854-857">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c1854-857">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c1854-858">Deaktivieren Sie auf der Registerkarte **General** (Allgemein) unter File > Settings (**Datei** > **Einstellungen**) **SSL certificate verification** (Verifizierung des SSL-Zertifikats).</span><span class="sxs-lookup"><span data-stu-id="c1854-858">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="c1854-859">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="c1854-859">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="c1854-860">Deaktivieren Sie auf der Registerkarte **General** (Allgemein) unter **Postman** > **Preferences** (Postman > Einstellungen) **SSL certificate verification** (Verifizierung des SSL-Zertifikats).</span><span class="sxs-lookup"><span data-stu-id="c1854-860">From **Postman** > **Preferences** (**General** tab), disable **SSL certificate verification**.</span></span> <span data-ttu-id="c1854-861">Wählen Sie alternativ den Schraubenschlüssel und **Settings** (Einstellungen) aus, und deaktivieren Sie dann die Überprüfung des SSL-Zertifikats.</span><span class="sxs-lookup"><span data-stu-id="c1854-861">Alternatively, select the wrench and select **Settings**, then disable the SSL certificate verification.</span></span>

---
  
> [!WARNING]
> <span data-ttu-id="c1854-862">Aktivieren Sie die Verifizierung des SSL-Zertifikats wieder, nachdem Sie den Controller getestet haben.</span><span class="sxs-lookup"><span data-stu-id="c1854-862">Re-enable SSL certificate verification after testing the controller.</span></span>

* <span data-ttu-id="c1854-863">Erstellen Sie eine neue Anforderung.</span><span class="sxs-lookup"><span data-stu-id="c1854-863">Create a new request.</span></span>
  * <span data-ttu-id="c1854-864">Legen Sie die HTTP-Methode auf **GET** fest.</span><span class="sxs-lookup"><span data-stu-id="c1854-864">Set the HTTP method to **GET**.</span></span>
  * <span data-ttu-id="c1854-865">Legen Sie den Anforderungs-URI auf `https://localhost:<port>/api/todo` fest.</span><span class="sxs-lookup"><span data-stu-id="c1854-865">Set the request URI to `https://localhost:<port>/api/todo`.</span></span> <span data-ttu-id="c1854-866">Beispielsweise `https://localhost:5001/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="c1854-866">For example, `https://localhost:5001/api/todo`.</span></span>
* <span data-ttu-id="c1854-867">Wählen Sie in Postman **Two pane view** (Ansicht mit zwei Bereichen) aus.</span><span class="sxs-lookup"><span data-stu-id="c1854-867">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="c1854-868">Wählen Sie **Send** (Senden) aus.</span><span class="sxs-lookup"><span data-stu-id="c1854-868">Select **Send**.</span></span>

![Postman mit GET-Anforderung](first-web-api/_static/2pv.png)

## <a name="add-a-create-method-21"></a><span data-ttu-id="c1854-870">Hinzufügen einer Methode 2.1</span><span class="sxs-lookup"><span data-stu-id="c1854-870">Add a Create method 2.1</span></span>

<span data-ttu-id="c1854-871">Fügen Sie die folgende `PostTodoItem`-Methode in *Controllers/TodoController.cs* hinzu:</span><span class="sxs-lookup"><span data-stu-id="c1854-871">Add the following `PostTodoItem` method inside of *Controllers/TodoController.cs*:</span></span> 

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="c1854-872">Der oben stehende Code ist eine HTTP POST-Methode, wie durch das [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute)-Attribut angegeben.</span><span class="sxs-lookup"><span data-stu-id="c1854-872">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="c1854-873">Die Methode ruft den Wert der Aufgabe aus dem Text der HTTP-Anforderung ab.</span><span class="sxs-lookup"><span data-stu-id="c1854-873">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="c1854-874">Die `CreatedAtAction`-Methode:</span><span class="sxs-lookup"><span data-stu-id="c1854-874">The `CreatedAtAction` method:</span></span>

* <span data-ttu-id="c1854-875">Gibt bei einer erfolgreichen Anforderung den Statuscode „HTTP 201“ zurück.</span><span class="sxs-lookup"><span data-stu-id="c1854-875">Returns an HTTP 201 status code, if successful.</span></span> <span data-ttu-id="c1854-876">HTTP 201 ist die Standardantwort für eine HTTP POST-Methode, die eine neue Ressource auf dem Server erstellt.</span><span class="sxs-lookup"><span data-stu-id="c1854-876">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="c1854-877">Fügt der Antwort einen `Location`-Header hinzu.</span><span class="sxs-lookup"><span data-stu-id="c1854-877">Adds a `Location` header to the response.</span></span> <span data-ttu-id="c1854-878">Der `Location`-Header gibt den URI des neu erstellten To-Do-Elements zurück.</span><span class="sxs-lookup"><span data-stu-id="c1854-878">The `Location` header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="c1854-879">Weitere Informationen finden Sie unter [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="c1854-879">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="c1854-880">Verweist auf die `GetTodoItem`-Aktion zum Erstellen des URIs des `Location`-Headers.</span><span class="sxs-lookup"><span data-stu-id="c1854-880">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="c1854-881">Das `nameof`-Schlüsselwort von C# wird verwendet, um eine Hartcodierung des Aktionsnamens im `CreatedAtAction`-Aufruf zu vermeiden.</span><span class="sxs-lookup"><span data-stu-id="c1854-881">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method-21"></a><span data-ttu-id="c1854-882">Testen der PostTodoItem-Methode 2.1</span><span class="sxs-lookup"><span data-stu-id="c1854-882">Test the PostTodoItem method 2.1</span></span>

* <span data-ttu-id="c1854-883">Erstellen Sie das Projekt.</span><span class="sxs-lookup"><span data-stu-id="c1854-883">Build the project.</span></span>
* <span data-ttu-id="c1854-884">Legen Sie in Postman die HTTP-Methode auf `POST` fest.</span><span class="sxs-lookup"><span data-stu-id="c1854-884">In Postman, set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="c1854-885">Legen Sie den URI auf `https://localhost:<port>/api/Todo` fest.</span><span class="sxs-lookup"><span data-stu-id="c1854-885">Set the URI to `https://localhost:<port>/api/Todo`.</span></span> <span data-ttu-id="c1854-886">Beispiel: `https://localhost:5001/api/Todo`.</span><span class="sxs-lookup"><span data-stu-id="c1854-886">For example, `https://localhost:5001/api/Todo`.</span></span>
* <span data-ttu-id="c1854-887">Wählen Sie die Registerkarte **Body** (Text) aus.</span><span class="sxs-lookup"><span data-stu-id="c1854-887">Select the **Body** tab.</span></span>
* <span data-ttu-id="c1854-888">Wählen Sie das Optionsfeld **raw** (Unformatiert) aus.</span><span class="sxs-lookup"><span data-stu-id="c1854-888">Select the **raw** radio button.</span></span>
* <span data-ttu-id="c1854-889">Legen Sie den Typ auf **JSON (application/json)** fest.</span><span class="sxs-lookup"><span data-stu-id="c1854-889">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="c1854-890">Geben Sie für die Aufgabe den Anforderungstext im JSON-Format ein:</span><span class="sxs-lookup"><span data-stu-id="c1854-890">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="c1854-891">Wählen Sie **Send** (Senden) aus.</span><span class="sxs-lookup"><span data-stu-id="c1854-891">Select **Send**.</span></span>

  ![Postman mit erstellter Anforderung](first-web-api/_static/create.png)

  <span data-ttu-id="c1854-893">Wenn Sie die Fehlermeldung „405: Methode nicht zulässig“ erhalten, wurde das Projekt wahrscheinlich nicht kompiliert, nachdem die Methode `PostTodoItem` hinzugefügt wurde.</span><span class="sxs-lookup"><span data-stu-id="c1854-893">If you get a 405 Method Not Allowed error, it's probably the result of not compiling the project after adding the `PostTodoItem` method.</span></span>

### <a name="test-the-location-header-uri-21"></a><span data-ttu-id="c1854-894">Testen des Adressheader-URIs 2.1</span><span class="sxs-lookup"><span data-stu-id="c1854-894">Test the location header URI 2.1</span></span>

* <span data-ttu-id="c1854-895">Wählen Sie auf der Registerkarte **Header** (Header) den Bereich **Response** (Antwort) aus.</span><span class="sxs-lookup"><span data-stu-id="c1854-895">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="c1854-896">Kopieren Sie den den Headerwert von **Location** (Speicherort):</span><span class="sxs-lookup"><span data-stu-id="c1854-896">Copy the **Location** header value:</span></span>

  ![Registerkarte „Header“ in der Postman-Konsole](first-web-api/_static/pmc2.png)

* <span data-ttu-id="c1854-898">Legen Sie die Methode auf „GET“ fest.</span><span class="sxs-lookup"><span data-stu-id="c1854-898">Set the method to GET.</span></span>
* <span data-ttu-id="c1854-899">Legen Sie den URI auf `https://localhost:<port>/api/TodoItems/2` fest.</span><span class="sxs-lookup"><span data-stu-id="c1854-899">Set the URI to `https://localhost:<port>/api/TodoItems/2`.</span></span> <span data-ttu-id="c1854-900">Beispiel: `https://localhost:5001/api/TodoItems/2`.</span><span class="sxs-lookup"><span data-stu-id="c1854-900">For example, `https://localhost:5001/api/TodoItems/2`.</span></span>
* <span data-ttu-id="c1854-901">Wählen Sie **Send** (Senden) aus.</span><span class="sxs-lookup"><span data-stu-id="c1854-901">Select **Send**.</span></span>

## <a name="add-a-puttodoitem-method-21"></a><span data-ttu-id="c1854-902">Hinzufügen einer PutTodoItem-Methode 2.1</span><span class="sxs-lookup"><span data-stu-id="c1854-902">Add a PutTodoItem method 2.1</span></span>

<span data-ttu-id="c1854-903">Fügen Sie die folgende `PutTodoItem`-Methode hinzu:</span><span class="sxs-lookup"><span data-stu-id="c1854-903">Add the following `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="c1854-904">`PutTodoItem` ähnelt `PostTodoItem`, verwendet allerdings HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="c1854-904">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="c1854-905">Die Antwort ist [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="c1854-905">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="c1854-906">Gemäß der HTTP-Spezifikation erfordert eine PUT-Anforderung, dass der Client die gesamte aktualisierte Entität (nicht nur die Änderungen) sendet.</span><span class="sxs-lookup"><span data-stu-id="c1854-906">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="c1854-907">Verwenden Sie [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute), um Teilupdates zu unterstützen.</span><span class="sxs-lookup"><span data-stu-id="c1854-907">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="c1854-908">Wenn beim Aufrufen von `PutTodoItem` ein Fehler zurückgegeben wird, rufen Sie `GET` auf, um sicherzustellen, dass die Datenbank ein Element enthält.</span><span class="sxs-lookup"><span data-stu-id="c1854-908">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method-21"></a><span data-ttu-id="c1854-909">Testen der PutTodoItem-Methode 2.1</span><span class="sxs-lookup"><span data-stu-id="c1854-909">Test the PutTodoItem method 2.1</span></span>

<span data-ttu-id="c1854-910">In diesem Beispiel wird eine In-Memory-Datenbank verwendet, die jedes Mal initialisiert werden muss, wenn die App gestartet wird.</span><span class="sxs-lookup"><span data-stu-id="c1854-910">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="c1854-911">Es muss ein Element in der Datenbank vorhanden sein, bevor Sie einen PUT-Aufruf durchführen.</span><span class="sxs-lookup"><span data-stu-id="c1854-911">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="c1854-912">Rufen Sie vor einem PUT-Aufruf GET auf, um sicherzustellen, dass ein Element in der Datenbank vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="c1854-912">Call GET to ensure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="c1854-913">Aktualisieren Sie das To-do-Element, das über den ID-Wert 1 verfügt, und legen Sie als Namen „feed fish“ fest:</span><span class="sxs-lookup"><span data-stu-id="c1854-913">Update the to-do item that has Id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "id":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="c1854-914">Die folgende Abbildung zeigt das Postman-Update:</span><span class="sxs-lookup"><span data-stu-id="c1854-914">The following image shows the Postman update:</span></span>

![Postman-Konsole mit der Antwort „204 (Kein Inhalt)“](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method-21"></a><span data-ttu-id="c1854-916">Hinzufügen einer DeleteTodoItem-Methode 2.1</span><span class="sxs-lookup"><span data-stu-id="c1854-916">Add a DeleteTodoItem method 2.1</span></span>

<span data-ttu-id="c1854-917">Fügen Sie die folgende `DeleteTodoItem`-Methode hinzu:</span><span class="sxs-lookup"><span data-stu-id="c1854-917">Add the following `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="c1854-918">Die `DeleteTodoItem`-Antwort lautet [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="c1854-918">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method-21"></a><span data-ttu-id="c1854-919">Testen der DeleteTodoItem-Methode 2.1</span><span class="sxs-lookup"><span data-stu-id="c1854-919">Test the DeleteTodoItem method 2.1</span></span>

<span data-ttu-id="c1854-920">So löschen Sie mit Postman eine Aufgabe</span><span class="sxs-lookup"><span data-stu-id="c1854-920">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="c1854-921">Legen Sie die Methode auf `DELETE` fest.</span><span class="sxs-lookup"><span data-stu-id="c1854-921">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="c1854-922">Legen Sie den URI des zu löschenden Objekts fest, z. B. `https://localhost:5001/api/todo/1`.</span><span class="sxs-lookup"><span data-stu-id="c1854-922">Set the URI of the object to delete (for example, `https://localhost:5001/api/todo/1`).</span></span>
* <span data-ttu-id="c1854-923">Wählen Sie **Send** (Senden) aus.</span><span class="sxs-lookup"><span data-stu-id="c1854-923">Select **Send**.</span></span>

<span data-ttu-id="c1854-924">Sie können in der Beispiel-App alle Elemente löschen.</span><span class="sxs-lookup"><span data-stu-id="c1854-924">The sample app allows you to delete all the items.</span></span> <span data-ttu-id="c1854-925">Sobald das letzte Element gelöscht wurde, wird allerdings beim nächsten API-Aufruf vom Modellklassenkonstruktor ein neues erstellt.</span><span class="sxs-lookup"><span data-stu-id="c1854-925">However, when the last item is deleted, a new one is created by the model class constructor the next time the API is called.</span></span>

## <a name="call-the-web-api-with-javascript-21"></a><span data-ttu-id="c1854-926">Aufrufen der Web-API mit JavaScript 2.1</span><span class="sxs-lookup"><span data-stu-id="c1854-926">Call the web API with JavaScript 2.1</span></span>

<span data-ttu-id="c1854-927">In diesem Abschnitt wird eine HTML-Seite hinzugefügt, die mithilfe von JavaScript die Web-API aufruft.</span><span class="sxs-lookup"><span data-stu-id="c1854-927">In this section, an HTML page is added that uses JavaScript to call the web API.</span></span> <span data-ttu-id="c1854-928">jQuery initiiert die Anforderung.</span><span class="sxs-lookup"><span data-stu-id="c1854-928">jQuery initiates the request.</span></span> <span data-ttu-id="c1854-929">JavaScript aktualisiert die Seite mit den Details aus der Antwort der Web-API.</span><span class="sxs-lookup"><span data-stu-id="c1854-929">JavaScript updates the page with the details from the web API's response.</span></span>

<span data-ttu-id="c1854-930">Konfigurieren Sie die App so, dass sie [statische Dateien bereitstellt](xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A), und [aktivieren Sie die Standarddateizuordnung](xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A), indem Sie *startup.cs* mit dem unten markierten Code aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="c1854-930">Configure the app to [serve static files](xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A) and [enable default file mapping](xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A) by updating *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

<span data-ttu-id="c1854-931">Erstellen Sie im Projektverzeichnis den Ordner *wwwwroot*.</span><span class="sxs-lookup"><span data-stu-id="c1854-931">Create a *wwwroot* folder in the project directory.</span></span>

<span data-ttu-id="c1854-932">Fügen Sie dem Verzeichnis *wwwroot* eine HTML-Datei namens *index.html* hinzu.</span><span class="sxs-lookup"><span data-stu-id="c1854-932">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="c1854-933">Ersetzen Sie den Inhalt durch folgendes Markup:</span><span class="sxs-lookup"><span data-stu-id="c1854-933">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

<span data-ttu-id="c1854-934">Fügen Sie dem Verzeichnis *wwwroot* eine JavaScript-Datei namens *site.js* hinzu.</span><span class="sxs-lookup"><span data-stu-id="c1854-934">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="c1854-935">Ersetzen Sie den Inhalt durch folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="c1854-935">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="c1854-936">Möglicherweise ist eine Änderung an den Starteinstellungen des ASP.NET Core-Projekts erforderlich, um die HTML-Seite lokal zu testen:</span><span class="sxs-lookup"><span data-stu-id="c1854-936">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="c1854-937">Öffnen Sie *Properties\launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="c1854-937">Open *Properties\launchSettings.json*.</span></span>
* <span data-ttu-id="c1854-938">Entfernen Sie die `launchUrl`-Eigenschaft, um zu erzwingen, dass die App mit *index.html* als Startseite geöffnet wird. Dies ist die Standarddatei des Projekts.</span><span class="sxs-lookup"><span data-stu-id="c1854-938">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="c1854-939">Dieses Beispiel ruft alle CRUD-Methoden der Web-API auf.</span><span class="sxs-lookup"><span data-stu-id="c1854-939">This sample calls all of the CRUD methods of the web API.</span></span> <span data-ttu-id="c1854-940">Im Folgenden werden die API-Aufrufe erläutert.</span><span class="sxs-lookup"><span data-stu-id="c1854-940">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items-21"></a><span data-ttu-id="c1854-941">Abrufen einer Liste von To-Do-Elementen 2.1</span><span class="sxs-lookup"><span data-stu-id="c1854-941">Get a list of to-do items 2.1</span></span>

<span data-ttu-id="c1854-942">jQuery sendet eine HTTP GET-Anforderung an die Web-API, die JSON-Code mit einem Array aus To-Do-Elementen zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="c1854-942">jQuery sends an HTTP GET request to the web API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="c1854-943">Die Rückruffunktion `success` wird aufgerufen, wenn die Anforderung erfolgreich ist.</span><span class="sxs-lookup"><span data-stu-id="c1854-943">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="c1854-944">Im Rückruf wird DOM mit den To-Do-Informationen aktualisiert.</span><span class="sxs-lookup"><span data-stu-id="c1854-944">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item-21"></a><span data-ttu-id="c1854-945">Hinzufügen eines To-Do-Elements 2.1</span><span class="sxs-lookup"><span data-stu-id="c1854-945">Add a to-do item 2.1</span></span>

<span data-ttu-id="c1854-946">jQuery sendet eine HTTP POST-Anforderung mit dem To-Do-Element im Anforderungstext.</span><span class="sxs-lookup"><span data-stu-id="c1854-946">jQuery sends an HTTP POST request with the to-do item in the request body.</span></span> <span data-ttu-id="c1854-947">Die Optionen `accepts` und `contentType` werden auf `application/json` festgelegt, um den gesendeten und empfangenen Medientyp anzugeben.</span><span class="sxs-lookup"><span data-stu-id="c1854-947">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="c1854-948">Die Aufgabe wird mit [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify) in JSON konvertiert.</span><span class="sxs-lookup"><span data-stu-id="c1854-948">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="c1854-949">Wenn die API den Statuscode „Erfolgreich“ zurückgibt, wird die Funktion `getData` aufgerufen, um die HTML-Tabelle zu aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="c1854-949">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item-21"></a><span data-ttu-id="c1854-950">Aktualisieren eines To-Do-Elements 2.1</span><span class="sxs-lookup"><span data-stu-id="c1854-950">Update a to-do item 2.1</span></span>

<span data-ttu-id="c1854-951">Das Aktualisieren einer Aufgabe ist vergleichbar mit dem Hinzufügen einer Aufgabe.</span><span class="sxs-lookup"><span data-stu-id="c1854-951">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="c1854-952">Die `url` ändert sich, um den eindeutigen Bezeichner des Elements hinzuzufügen. Der `type` lautet `PUT`.</span><span class="sxs-lookup"><span data-stu-id="c1854-952">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item-21"></a><span data-ttu-id="c1854-953">Löschen eines To-Do-Elements 2.1</span><span class="sxs-lookup"><span data-stu-id="c1854-953">Delete a to-do item 2.1</span></span>

<span data-ttu-id="c1854-954">Eine Aufgabe wird folgendermaßen gelöscht: im AJAX-Aufruf wird `type` auf `DELETE` festgelegt, und der eindeutige Bezeichner des Elements wird in der URL angegeben.</span><span class="sxs-lookup"><span data-stu-id="c1854-954">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

<a name="auth"></a>

## <a name="add-authentication-support-to-a-web-api-21"></a><span data-ttu-id="c1854-955">Hinzufügen der Authentifizierungsunterstützung zu einer Web-API 2.1</span><span class="sxs-lookup"><span data-stu-id="c1854-955">Add authentication support to a web API 2.1</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="additional-resources-21"></a><span data-ttu-id="c1854-956">Zusätzliche Ressourcen 2.1</span><span class="sxs-lookup"><span data-stu-id="c1854-956">Additional resources 2.1</span></span>

<span data-ttu-id="c1854-957">[Sie können den Beispielcode für dieses Tutorial anzeigen oder herunterladen.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples)</span><span class="sxs-lookup"><span data-stu-id="c1854-957">[View or download sample code for this tutorial](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span></span> <span data-ttu-id="c1854-958">[Informationen zum Herunterladen](xref:index#how-to-download-a-sample) finden Sie hier.</span><span class="sxs-lookup"><span data-stu-id="c1854-958">See [how to download](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="c1854-959">Weitere Informationen finden Sie in den folgenden Ressourcen:</span><span class="sxs-lookup"><span data-stu-id="c1854-959">For more information, see the following resources:</span></span>

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/intro>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [<span data-ttu-id="c1854-960">Dieses Tutorial auf YouTube</span><span class="sxs-lookup"><span data-stu-id="c1854-960">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=TTkhEyGBfAk)
