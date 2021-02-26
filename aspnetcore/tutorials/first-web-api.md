---
title: 'Tutorial: Erstellen einer Web-API mit ASP.NET Core'
author: rick-anderson
description: Informationen zum Erstellen einer Web-API mit ASP.NET Core.
ms.author: riande
ms.custom: mvc, devx-track-js
ms.date: 02/04/2021
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
ms.openlocfilehash: 1f7c7db857090ff0a174d37b86e1265bab40b4fd
ms.sourcegitcommit: f77a7467651bab61b24261da9dc5c1dd75fc1fa9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100564093"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a><span data-ttu-id="4f6bd-103">Tutorial: Erstellen einer Web-API mit ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4f6bd-103">Tutorial: Create a web API with ASP.NET Core</span></span>

<span data-ttu-id="4f6bd-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5) und [Mike Wasson](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="4f6bd-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="4f6bd-105">In diesem Tutorial lernen Sie die Grundlagen der Erstellung einer Web-API mit ASP.NET Core kennen.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-105">This tutorial teaches the basics of building a web API with ASP.NET Core.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="4f6bd-106">In diesem Tutorial lernen Sie, wie die folgenden Aufgaben ausgeführt werden:</span><span class="sxs-lookup"><span data-stu-id="4f6bd-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="4f6bd-107">Erstellen eines Web-API-Projekts</span><span class="sxs-lookup"><span data-stu-id="4f6bd-107">Create a web API project.</span></span>
> * <span data-ttu-id="4f6bd-108">Hinzufügen einer Modellklasse und eines Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="4f6bd-108">Add a model class and a database context.</span></span>
> * <span data-ttu-id="4f6bd-109">Erstellen eines Controllergerüsts mit CRUD-Methoden</span><span class="sxs-lookup"><span data-stu-id="4f6bd-109">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="4f6bd-110">Konfigurieren des Routings, der URL-Pfade und der Rückgabewerte</span><span class="sxs-lookup"><span data-stu-id="4f6bd-110">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="4f6bd-111">Aufrufen der Web-API mit Postman</span><span class="sxs-lookup"><span data-stu-id="4f6bd-111">Call the web API with Postman.</span></span>

<span data-ttu-id="4f6bd-112">Abschließend steht Ihnen eine Web-API zur Verfügung, die in einer relationalen Datenbank gespeicherte To-do-Elemente verwalten kann.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-112">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="4f6bd-113">Übersicht</span><span class="sxs-lookup"><span data-stu-id="4f6bd-113">Overview</span></span>

<span data-ttu-id="4f6bd-114">In diesem Tutorial wird die folgende API erstellt:</span><span class="sxs-lookup"><span data-stu-id="4f6bd-114">This tutorial creates the following API:</span></span>

|<span data-ttu-id="4f6bd-115">API</span><span class="sxs-lookup"><span data-stu-id="4f6bd-115">API</span></span> | <span data-ttu-id="4f6bd-116">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="4f6bd-116">Description</span></span> | <span data-ttu-id="4f6bd-117">Anforderungstext</span><span class="sxs-lookup"><span data-stu-id="4f6bd-117">Request body</span></span> | <span data-ttu-id="4f6bd-118">Antworttext</span><span class="sxs-lookup"><span data-stu-id="4f6bd-118">Response body</span></span> |
|--- | ---- | ---- | ---- |
|`GET /api/TodoItems` | <span data-ttu-id="4f6bd-119">Alle To-do-Elemente abrufen</span><span class="sxs-lookup"><span data-stu-id="4f6bd-119">Get all to-do items</span></span> | <span data-ttu-id="4f6bd-120">Keine</span><span class="sxs-lookup"><span data-stu-id="4f6bd-120">None</span></span> | <span data-ttu-id="4f6bd-121">Array von To-do-Elementen</span><span class="sxs-lookup"><span data-stu-id="4f6bd-121">Array of to-do items</span></span>|
|`GET /api/TodoItems/{id}` | <span data-ttu-id="4f6bd-122">Ein Element nach ID abrufen</span><span class="sxs-lookup"><span data-stu-id="4f6bd-122">Get an item by ID</span></span> | <span data-ttu-id="4f6bd-123">Keine</span><span class="sxs-lookup"><span data-stu-id="4f6bd-123">None</span></span> | <span data-ttu-id="4f6bd-124">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="4f6bd-124">To-do item</span></span>|
|`POST /api/TodoItems` | <span data-ttu-id="4f6bd-125">Neues Element hinzufügen</span><span class="sxs-lookup"><span data-stu-id="4f6bd-125">Add a new item</span></span> | <span data-ttu-id="4f6bd-126">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="4f6bd-126">To-do item</span></span> | <span data-ttu-id="4f6bd-127">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="4f6bd-127">To-do item</span></span> |
|`PUT /api/TodoItems/{id}` | <span data-ttu-id="4f6bd-128">Vorhandenes Element aktualisieren &nbsp;</span><span class="sxs-lookup"><span data-stu-id="4f6bd-128">Update an existing item &nbsp;</span></span> | <span data-ttu-id="4f6bd-129">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="4f6bd-129">To-do item</span></span> | <span data-ttu-id="4f6bd-130">Keine</span><span class="sxs-lookup"><span data-stu-id="4f6bd-130">None</span></span> |
|<span data-ttu-id="4f6bd-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="4f6bd-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span></span> | <span data-ttu-id="4f6bd-132">Löschen eines Elements &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="4f6bd-132">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="4f6bd-133">Keine</span><span class="sxs-lookup"><span data-stu-id="4f6bd-133">None</span></span> | <span data-ttu-id="4f6bd-134">Keine</span><span class="sxs-lookup"><span data-stu-id="4f6bd-134">None</span></span>|

<span data-ttu-id="4f6bd-135">Das folgende Diagramm zeigt den Entwurf der App.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-135">The following diagram shows the design of the app.</span></span>

![Der Client ist das Feld ganz links.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="4f6bd-141">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="4f6bd-141">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4f6bd-142">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4f6bd-142">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="4f6bd-143">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4f6bd-143">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="4f6bd-144">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="4f6bd-144">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-5.0.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="4f6bd-145">Erstellen eines Webprojekts</span><span class="sxs-lookup"><span data-stu-id="4f6bd-145">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4f6bd-146">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4f6bd-146">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4f6bd-147">Klicken Sie im Menü **Datei** auf **Neu** > **Projekt**.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-147">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="4f6bd-148">Wählen Sie die Vorlage **ASP.NET Core-Webanwendung** aus, und klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-148">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="4f6bd-149">Geben Sie dem Projekt den Namen *TodoApi*, und klicken Sie auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-149">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="4f6bd-150">Vergewissern Sie sich, dass im Dialogfeld **Neue ASP.NET Core-Webanwendung erstellen** die Optionen **.NET Core** und **ASP.NET Core 5.0** ausgewählt sind.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-150">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 5.0** are selected.</span></span> <span data-ttu-id="4f6bd-151">Wählen Sie die Vorlage **API** aus, und klicken Sie auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-151">Select the **API** template and click **Create**.</span></span>

![VS-Dialogfeld „Neues Projekt“](first-web-api/_static/5/vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="4f6bd-153">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4f6bd-153">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="4f6bd-154">Öffnen Sie das [integrierte Terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="4f6bd-154">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="4f6bd-155">Wechseln Sie mit dem Befehl `cd`' zu dem Ordner, der den Projektordner enthalten soll.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-155">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="4f6bd-156">Führen Sie die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="4f6bd-156">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="4f6bd-157">Wenn Sie in einem Dialogfeld angeben müssen, ob Sie dem Projekt die erforderlichen Elemente hinzufügen möchten, wählen Sie **Ja** aus.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-157">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="4f6bd-158">Die obenstehenden Befehle haben folgende Konsequenzen:</span><span class="sxs-lookup"><span data-stu-id="4f6bd-158">The preceding commands:</span></span>

  * <span data-ttu-id="4f6bd-159">Ein neues Web-API-Projekt wird erstellt und in Visual Studio Code geöffnet.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-159">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="4f6bd-160">Die NuGet-Pakete, die im nächsten Abschnitt erforderlich sind, werden hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-160">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="4f6bd-161">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="4f6bd-161">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="4f6bd-162">Klicken Sie auf **Datei** > **Neue Projektmappe**.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-162">Select **File** > **New Solution**.</span></span>

  ![Neue Projektmappe in macOS](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="4f6bd-164">Navigieren Sie in Visual Studio für Mac-Versionen vor Version 8.6 zu **.NET Core** > **App** > **API** > **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-164">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="4f6bd-165">Bei Version 8.6 oder höher klicken Sie auf **Web and Console** > **App** > **API** > **Weiter** (Web und Konsole).</span><span class="sxs-lookup"><span data-stu-id="4f6bd-165">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>

  ![Auswählen von macOS-API-Vorlagen](first-web-api-mac/_static/api_template.png)

* <span data-ttu-id="4f6bd-167">Wählen Sie im Dialogfeld **Neue ASP.NET Core-Web-API konfigurieren** die neueste .NET Core 5.x-Version als **Zielframework** aus.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-167">In the **Configure the new ASP.NET Core Web API** dialog, select the latest .NET Core 5.x **Target Framework**.</span></span> <span data-ttu-id="4f6bd-168">Klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-168">Select **Next**.</span></span>

* <span data-ttu-id="4f6bd-169">Geben Sie für **Projektname** *TodoApi* ein, und wählen Sie dann **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-169">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![Dialogfeld „Konfiguration“](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="4f6bd-171">Öffnen Sie im Projektordner ein Befehlsterminal, und führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="4f6bd-171">Open a command terminal in the project folder and run the following command:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-project"></a><span data-ttu-id="4f6bd-172">Testen des Projekts</span><span class="sxs-lookup"><span data-stu-id="4f6bd-172">Test the project</span></span>

<span data-ttu-id="4f6bd-173">Die Projektvorlage erstellt eine `WeatherForecast`-API mit Unterstützung für [Swagger](xref:tutorials/web-api-help-pages-using-swagger).</span><span class="sxs-lookup"><span data-stu-id="4f6bd-173">The project template creates a `WeatherForecast` API with support for [Swagger](xref:tutorials/web-api-help-pages-using-swagger).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4f6bd-174">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4f6bd-174">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="4f6bd-175">Drücken Sie STRG+F5, um die Ausführung ohne den Debugger zu starten.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-175">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="4f6bd-176">Visual Studio startet Folgendes:</span><span class="sxs-lookup"><span data-stu-id="4f6bd-176">Visual Studio launches:</span></span>

* <span data-ttu-id="4f6bd-177">Den IIS Express-Webserver.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-177">The IIS Express web server.</span></span>
* <span data-ttu-id="4f6bd-178">Den Standardbrowser. Visual Studio navigiert zu `https://localhost:<port>/swagger/index.html`, wobei es sich bei `<port>` um eine zufällig ausgewählte Portnummer handelt.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-178">The default browser and navigates to `https://localhost:<port>/swagger/index.html`, where `<port>` is a randomly chosen port number.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="4f6bd-179">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4f6bd-179">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/trustCertVSC.md)]

<span data-ttu-id="4f6bd-180">Drücken Sie STRG+F5, um die App auszuführen.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-180">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="4f6bd-181">Navigieren Sie in einem Browser zur folgenden URL: [https://localhost:5001/swagger](https://localhost:5001/swagger).</span><span class="sxs-lookup"><span data-stu-id="4f6bd-181">In a browser, go to following URL: [https://localhost:5001/swagger](https://localhost:5001/swagger)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="4f6bd-182">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="4f6bd-182">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="4f6bd-183">Wählen Sie **Ausführen** > **Debugging starten** aus, um die App zu starten.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-183">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="4f6bd-184">Visual Studio für Mac startet einen Browser und navigiert zu `https://localhost:<port>`, wobei es sich bei `<port>` um eine zufällig ausgewählte Portnummer handelt.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-184">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="4f6bd-185">Der Fehler „HTTP 404: Nicht gefunden“ wird zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-185">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="4f6bd-186">Fügen Sie der URL `/swagger` an, d.h. ändern Sie die URL in `https://localhost:<port>/swagger`.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-186">Append `/swagger` to the URL (change the URL to `https://localhost:<port>/swagger`).</span></span>

---

<span data-ttu-id="4f6bd-187">Die Swagger-Seite `/swagger/index.html` wird angezeigt.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-187">The Swagger page `/swagger/index.html` is displayed.</span></span> <span data-ttu-id="4f6bd-188">Wählen Sie **GET** > **Tryit out** > **Execute**  (GET > Testen> Ausführen) aus.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-188">Select **GET** > **Try it out** > **Execute**.</span></span> <span data-ttu-id="4f6bd-189">Die Seite zeigt Folgendes an:</span><span class="sxs-lookup"><span data-stu-id="4f6bd-189">The page displays:</span></span>

* <span data-ttu-id="4f6bd-190">Der [Curl](https://curl.haxx.se/)-Befehl, zum Testen der WeatherForecast-API.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-190">The [Curl](https://curl.haxx.se/) command to test the WeatherForecast API.</span></span>
* <span data-ttu-id="4f6bd-191">Die URL zum Testen der WeatherForecast-API.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-191">The URL to test the WeatherForecast API.</span></span>
* <span data-ttu-id="4f6bd-192">Der Antwortcode, der Text und die Header.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-192">The response code, body, and headers.</span></span>
* <span data-ttu-id="4f6bd-193">Ein Dropdown-Listenfeld mit Medientypen und dem Beispielwert und -schema.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-193">A drop down list box with media types and the example value and schema.</span></span>

<!-- Review: Do we care the IE generates several errors. It shows the data, but with  Unrecognized response type; displaying content as text.
-->
<span data-ttu-id="4f6bd-194">Swagger wird verwendet, um hilfreiche Dokumentation und Hilfeseiten für Web-APIs zu generieren.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-194">Swagger is used to generate useful documentation and help pages for web APIs.</span></span> <span data-ttu-id="4f6bd-195">Dieses Tutorial konzentriert sich auf die Erstellung einer Web-API.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-195">This tutorial focuses on creating a web API.</span></span> <span data-ttu-id="4f6bd-196">Weitere Informationen zu Swagger finden Sie unter <xref:tutorials/web-api-help-pages-using-swagger>.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-196">For more information on Swagger, see <xref:tutorials/web-api-help-pages-using-swagger>.</span></span>

<span data-ttu-id="4f6bd-197">Kopieren Sie die **Anforderungs-URL**, und fügen Sie sie im Browser ein: `https://localhost:<port>/WeatherForecast`</span><span class="sxs-lookup"><span data-stu-id="4f6bd-197">Copy and paste the **Request URL** in the browser:  `https://localhost:<port>/WeatherForecast`</span></span>

<span data-ttu-id="4f6bd-198">Der zurückgegebene JSON-Code sieht in etwa wie folgt aus:</span><span class="sxs-lookup"><span data-stu-id="4f6bd-198">JSON similar to the following is returned:</span></span>

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

### <a name="update-the-launchurl"></a><span data-ttu-id="4f6bd-199">Aktualisieren der launchUrl</span><span class="sxs-lookup"><span data-stu-id="4f6bd-199">Update the launchUrl</span></span>

<span data-ttu-id="4f6bd-200">Aktualisieren Sie `launchUrl` in *Properties\launchSettings.json* aus `"swagger"` in `"api/TodoItems"`:</span><span class="sxs-lookup"><span data-stu-id="4f6bd-200">In *Properties\launchSettings.json*, update `launchUrl` from `"swagger"` to `"api/TodoItems"`:</span></span>

```json
"launchUrl": "api/TodoItems",
```

<span data-ttu-id="4f6bd-201">Da Swagger entfernt wurde, ändert das oben gezeigte Markup die URL, die für die GET-Methode des Controllers gestartet wird, der in den folgenden Abschnitten hinzugefügt wurde.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-201">Because Swagger has been removed, the preceding markup changes the URL that is launched to the GET method of the controller added in the following sections.</span></span>

## <a name="add-a-model-class"></a><span data-ttu-id="4f6bd-202">Hinzufügen einer Modellklasse</span><span class="sxs-lookup"><span data-stu-id="4f6bd-202">Add a model class</span></span>

<span data-ttu-id="4f6bd-203">Ein *Modell* ist eine Gruppe von Klassen, die die Daten darstellen, die die App verwaltet.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-203">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="4f6bd-204">Das Modell für diese App ist eine einzelne `TodoItem`-Klasse.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-204">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4f6bd-205">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4f6bd-205">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4f6bd-206">Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-206">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="4f6bd-207">Klicken Sie auf **Hinzufügen** > **Neuer Ordner**.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-207">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="4f6bd-208">Geben Sie dem Ordner den Namen *Models* .</span><span class="sxs-lookup"><span data-stu-id="4f6bd-208">Name the folder *Models*.</span></span>

* <span data-ttu-id="4f6bd-209">Klicken Sie mit der rechten Maustaste auf den Ordner *Models* , und wählen Sie **Hinzufügen** > **Klasse** aus.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-209">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="4f6bd-210">Nennen Sie die Klasse *TodoItem*, und wählen Sie **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-210">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="4f6bd-211">Ersetzen Sie den Vorlagencode durch Folgendes:</span><span class="sxs-lookup"><span data-stu-id="4f6bd-211">Replace the template code with the following:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="4f6bd-212">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4f6bd-212">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="4f6bd-213">Fügen Sie einen Ordner mit dem Namen *Models* hinzu.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-213">Add a folder named *Models*.</span></span>

* <span data-ttu-id="4f6bd-214">Fügen Sie dem Ordner *Models* mit dem folgenden Code eine `TodoItem`-Klasse hinzu:</span><span class="sxs-lookup"><span data-stu-id="4f6bd-214">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="4f6bd-215">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="4f6bd-215">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="4f6bd-216">Klicken Sie mit der rechten Maustaste auf das Projekt.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-216">Right-click the project.</span></span> <span data-ttu-id="4f6bd-217">Klicken Sie auf **Hinzufügen** > **Neuer Ordner**.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-217">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="4f6bd-218">Geben Sie dem Ordner den Namen *Models* .</span><span class="sxs-lookup"><span data-stu-id="4f6bd-218">Name the folder *Models*.</span></span>

  ![Neuer Ordner](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="4f6bd-220">Klicken Sie mit der rechten Maustaste auf den Ordner *Models* , und wählen Sie **Hinzufügen** > **Neue Datei** > **Allgemein** > **Leere Klasse** aus.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-220">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="4f6bd-221">Nennen Sie die Klasse *TodoItem*, und klicken Sie dann auf **Neu**.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-221">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="4f6bd-222">Ersetzen Sie den Vorlagencode durch Folgendes:</span><span class="sxs-lookup"><span data-stu-id="4f6bd-222">Replace the template code with the following:</span></span>

---

  [!code-csharp[](first-web-api/samples/5.x/TodoApi/Models/TodoItem.cs?name=snippet)]

<span data-ttu-id="4f6bd-223">Die `Id`-Eigenschaft fungiert als eindeutiger Schlüssel in einer relationalen Datenbank.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-223">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="4f6bd-224">Modellklassen können überall im Projekt platziert werden, doch gemäß der Konvention wird der Ordner *Models* verwendet.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-224">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="4f6bd-225">Hinzufügen eines Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="4f6bd-225">Add a database context</span></span>

<span data-ttu-id="4f6bd-226">Der *Datenbankkontext* ist die Hauptklasse, die die Entity Framework-Funktionen für ein Datenmodell koordiniert.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-226">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="4f6bd-227">Diese Klasse wird durch Ableiten von der <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName>-Klasse erstellt.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-227">This class is created by deriving from the <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName> class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4f6bd-228">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4f6bd-228">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-nuget-packages"></a><span data-ttu-id="4f6bd-229">Hinzufügen von NuGet-Paketen</span><span class="sxs-lookup"><span data-stu-id="4f6bd-229">Add NuGet packages</span></span>

* <span data-ttu-id="4f6bd-230">Klicken Sie im Menü **Extras** auf **NuGet-Paket-Manager > NuGet-Pakete für Projektmappe verwalten...** .</span><span class="sxs-lookup"><span data-stu-id="4f6bd-230">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="4f6bd-231">Klicken Sie auf die Registerkarte **Durchsuchen**, und geben Sie dann **Microsoft.EntityFrameworkCore.InMemory** in das Suchfeld ein.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-231">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.InMemory** in the search box.</span></span>
* <span data-ttu-id="4f6bd-232">Wählen Sie im linken Bereich **Microsoft.EntityFrameworkCore.InMemory** aus.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-232">Select **Microsoft.EntityFrameworkCore.InMemory** in the left pane.</span></span>
* <span data-ttu-id="4f6bd-233">Aktivieren Sie das Kontrollkästchen **Projekt** im rechten Bereich, und klicken Sie dann auf **Installieren**.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-233">Select the **Project** check box in the right pane and then select **Install**.</span></span>

![NuGet-Paket-Manager](first-web-api/_static/5/vsNuGet.png)

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="4f6bd-235">Hinzufügen des TodoContext-Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="4f6bd-235">Add the TodoContext database context</span></span>

* <span data-ttu-id="4f6bd-236">Klicken Sie mit der rechten Maustaste auf den Ordner *Models* , und wählen Sie **Hinzufügen** > **Klasse** aus.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-236">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="4f6bd-237">Nennen Sie die Klasse *TodoContext*, und klicken Sie auf **Hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-237">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="4f6bd-238">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="4f6bd-238">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="4f6bd-239">Fügen Sie eine `TodoContext`-Klasse zum Ordner *Models* hinzu.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-239">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="4f6bd-240">Geben Sie den folgenden Code ein:</span><span class="sxs-lookup"><span data-stu-id="4f6bd-240">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/5.x/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="4f6bd-241">Registrieren des Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="4f6bd-241">Register the database context</span></span>

<span data-ttu-id="4f6bd-242">In ASP.NET Core müssen Dienste wie der Datenbankkontext mit dem Container [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) registriert werden.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-242">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="4f6bd-243">Der Container stellt den Dienst für Controller bereit.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-243">The container provides the service to controllers.</span></span>

<span data-ttu-id="4f6bd-244">Aktualisieren Sie *Startup.cs* mit dem folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="4f6bd-244">Update *Startup.cs* with the following code:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="4f6bd-245">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="4f6bd-245">The preceding code:</span></span>

* <span data-ttu-id="4f6bd-246">Entfernt die Swagger-Aufrufe.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-246">Removes the Swagger calls.</span></span>
* <span data-ttu-id="4f6bd-247">Entfernt nicht benötigte `using`-Deklarationen</span><span class="sxs-lookup"><span data-stu-id="4f6bd-247">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="4f6bd-248">Fügt dem Abhängigkeitscontainer den Datenbankkontext hinzu</span><span class="sxs-lookup"><span data-stu-id="4f6bd-248">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="4f6bd-249">Gibt an, dass der Datenbankkontext eine In-Memory Database verwendet</span><span class="sxs-lookup"><span data-stu-id="4f6bd-249">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="4f6bd-250">Erstellen eines Controllergerüsts</span><span class="sxs-lookup"><span data-stu-id="4f6bd-250">Scaffold a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4f6bd-251">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4f6bd-251">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4f6bd-252">Klicken Sie mit der rechten Maustaste auf den Ordner *Controller*.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-252">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="4f6bd-253">Wählen Sie **Hinzufügen** > **Neues Gerüstelement** aus.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-253">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="4f6bd-254">Klicken Sie auf **API-Controller mit Aktionen unter Verwendung von Entity Framework** und dann auf **Hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-254">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="4f6bd-255">Führen Sie im Dialogfeld **API-Controller mit Aktionen unter Verwendung von Entity Framework** folgende Schritte aus:</span><span class="sxs-lookup"><span data-stu-id="4f6bd-255">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="4f6bd-256">Wählen Sie in der **Modellklasse** das Element **TodoItem (TodoApi.Models)** aus.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-256">Select **TodoItem (TodoApi.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="4f6bd-257">Wählen Sie in der **Datenkontextklasse** das Element **TodoContext (TodoApi.Models)** aus.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-257">Select **TodoContext (TodoApi.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="4f6bd-258">Wählen Sie **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-258">Select **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="4f6bd-259">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="4f6bd-259">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="4f6bd-260">Führen Sie die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="4f6bd-260">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet tool update -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="4f6bd-261">Die obenstehenden Befehle haben folgende Konsequenzen:</span><span class="sxs-lookup"><span data-stu-id="4f6bd-261">The preceding commands:</span></span>

* <span data-ttu-id="4f6bd-262">Die NuGet-Pakete für den Gerüstbau werden hinzufügt.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-262">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="4f6bd-263">Die Gerüstbau-Engine (`dotnet-aspnet-codegenerator`) wird installiert.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-263">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="4f6bd-264">Das Gerüst für `TodoItemsController` wird erstellt.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-264">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="4f6bd-265">Der generierte Code hat folgende Auswirkungen:</span><span class="sxs-lookup"><span data-stu-id="4f6bd-265">The generated code:</span></span>

* <span data-ttu-id="4f6bd-266">Markiert die Klasse mit dem [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute)-Attribut.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-266">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="4f6bd-267">Dieses Attribut gibt an, dass der Controller auf Web-API-Anforderungen reagiert.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-267">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="4f6bd-268">Weitere Informationen zu bestimmten Verhaltensweisen, die das Attribut ermöglicht, finden Sie unter <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-268">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="4f6bd-269">Verwendet die Abhängigkeitsinjektion, um den Datenbankkontext (`TodoContext`) dem Controller hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-269">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="4f6bd-270">Der Datenbankkontext wird in den einzelnen [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete)-Methoden im Controller verwendet.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-270">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<span data-ttu-id="4f6bd-271">Die ASP.NET Core-Vorlagen für:</span><span class="sxs-lookup"><span data-stu-id="4f6bd-271">The ASP.NET Core templates for:</span></span>

* <span data-ttu-id="4f6bd-272">Controller mit Ansichten enthalten `[action]` in der Routenvorlage.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-272">Controllers with views include `[action]` in the route template.</span></span>
* <span data-ttu-id="4f6bd-273">API-Controller enthalten keine `[action]` in der Routenvorlage.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-273">API controllers don't include `[action]` in the route template.</span></span>

<span data-ttu-id="4f6bd-274">Wenn sich das `[action]`-Token nicht in der Routenvorlage befindet, wird der [action](xref:mvc/controllers/routing#action)-Name von der Route ausgeschlossen.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-274">When the `[action]` token isn't in the route template, the [action](xref:mvc/controllers/routing#action) name is excluded from the route.</span></span> <span data-ttu-id="4f6bd-275">Dies bedeutet, dass der zugehörige Methodenname der Aktion nicht in der übereinstimmenden Route verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-275">That is, the action's associated method name isn't used in the matching route.</span></span>

## <a name="update-the-posttodoitem-create-method"></a><span data-ttu-id="4f6bd-276">Aktualisieren der PostTodoItem-Erstellungsmethode</span><span class="sxs-lookup"><span data-stu-id="4f6bd-276">Update the PostTodoItem create method</span></span>

<span data-ttu-id="4f6bd-277">Aktualisieren Sie die Rückgabeanweisung in `PostTodoItem` mit dem [nameof](/dotnet/csharp/language-reference/operators/nameof)-Operator:</span><span class="sxs-lookup"><span data-stu-id="4f6bd-277">Update the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="4f6bd-278">Der oben stehende Code ist eine HTTP POST-Methode, wie durch das [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute)-Attribut angegeben.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-278">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="4f6bd-279">Die Methode ruft den Wert der Aufgabe aus dem Text der HTTP-Anforderung ab.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-279">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="4f6bd-280">Weitere Informationen finden Sie unter [Attributrouting mit Http[Verb]-Attributen](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="4f6bd-280">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="4f6bd-281">Die <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*>-Methode:</span><span class="sxs-lookup"><span data-stu-id="4f6bd-281">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="4f6bd-282">Gibt bei Erfolg den [HTTP-Statuscode 201](https://developer.mozilla.org/docs/Web/HTTP/Status/201) zurück.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-282">Returns an [HTTP 201 status code](https://developer.mozilla.org/docs/Web/HTTP/Status/201) if successful.</span></span> <span data-ttu-id="4f6bd-283">HTTP 201 ist die Standardantwort für eine HTTP POST-Methode, die eine neue Ressource auf dem Server erstellt.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-283">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="4f6bd-284">Fügt der Antwort einen [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location)-Header hinzu.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-284">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="4f6bd-285">Der `Location`-Header legt den [URI](https://developer.mozilla.org/docs/Glossary/URI) des neu erstellten To-do-Elements fest.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-285">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="4f6bd-286">Weitere Informationen finden Sie unter [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="4f6bd-286">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="4f6bd-287">Verweist auf die `GetTodoItem`-Aktion zum Erstellen des URIs des `Location`-Headers.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-287">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="4f6bd-288">Das `nameof`-Schlüsselwort von C# wird verwendet, um eine Hartcodierung des Aktionsnamens im `CreatedAtAction`-Aufruf zu vermeiden.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-288">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="4f6bd-289">Installieren von Postman</span><span class="sxs-lookup"><span data-stu-id="4f6bd-289">Install Postman</span></span>

<span data-ttu-id="4f6bd-290">Dieses Tutorial verwendet Postman zum Testen der Web-API.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-290">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="4f6bd-291">Installieren Sie [Postman](https://www.getpostman.com/downloads/).</span><span class="sxs-lookup"><span data-stu-id="4f6bd-291">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="4f6bd-292">Starten Sie die Web-App.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-292">Start the web app.</span></span>
* <span data-ttu-id="4f6bd-293">Starten Sie Postman.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-293">Start Postman.</span></span>
* <span data-ttu-id="4f6bd-294">Deaktivieren Sie **SSL certificate verification** (Verifizierung des SSL-Zertifikats).</span><span class="sxs-lookup"><span data-stu-id="4f6bd-294">Disable **SSL certificate verification**</span></span>
  * <span data-ttu-id="4f6bd-295">Deaktivieren Sie auf der Registerkarte **General** (Allgemein) unter File > Settings (**Datei** > **Einstellungen**) **SSL certificate verification** (Verifizierung des SSL-Zertifikats).</span><span class="sxs-lookup"><span data-stu-id="4f6bd-295">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="4f6bd-296">Aktivieren Sie die Verifizierung des SSL-Zertifikats wieder, nachdem Sie den Controller getestet haben.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-296">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="4f6bd-297">Testen von PostTodoItem mit Postman</span><span class="sxs-lookup"><span data-stu-id="4f6bd-297">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="4f6bd-298">Erstellen Sie eine neue Anforderung.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-298">Create a new request.</span></span>
* <span data-ttu-id="4f6bd-299">Legen Sie als HTTP-Methode `POST` fest.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-299">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="4f6bd-300">Legen Sie den URI auf `https://localhost:<port>/api/TodoItems` fest.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-300">Set the URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="4f6bd-301">Beispiel: `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-301">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="4f6bd-302">Wählen Sie die Registerkarte **Body** (Text) aus.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-302">Select the **Body** tab.</span></span>
* <span data-ttu-id="4f6bd-303">Wählen Sie das Optionsfeld **raw** (Unformatiert) aus.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-303">Select the **raw** radio button.</span></span>
* <span data-ttu-id="4f6bd-304">Legen Sie den Typ auf **JSON (application/json)** fest.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-304">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="4f6bd-305">Geben Sie für die Aufgabe den Anforderungstext im JSON-Format ein:</span><span class="sxs-lookup"><span data-stu-id="4f6bd-305">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="4f6bd-306">Wählen Sie **Send** (Senden) aus.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-306">Select **Send**.</span></span>

  ![Postman mit erstellter Anforderung](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri"></a><span data-ttu-id="4f6bd-308">Testen des Adressheader-URIs</span><span class="sxs-lookup"><span data-stu-id="4f6bd-308">Test the location header URI</span></span>

<span data-ttu-id="4f6bd-309">Der Location-Header-URI kann im Browser getestet werden.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-309">The location header URI can be tested in the browser.</span></span> <span data-ttu-id="4f6bd-310">Kopieren Sie den Location-Header-URI, und fügen Sie ihn im Browser ein.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-310">Copy and paste the location header URI into the browser.</span></span>

<span data-ttu-id="4f6bd-311">So testen Sie in Postman:</span><span class="sxs-lookup"><span data-stu-id="4f6bd-311">To test in Postman:</span></span>

* <span data-ttu-id="4f6bd-312">Wählen Sie auf der Registerkarte **Header** (Header) den Bereich **Response** (Antwort) aus.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-312">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="4f6bd-313">Kopieren Sie den den Headerwert von **Location** (Speicherort):</span><span class="sxs-lookup"><span data-stu-id="4f6bd-313">Copy the **Location** header value:</span></span>

  ![Registerkarte „Header“ in der Postman-Konsole](first-web-api/_static/3/create.png)

* <span data-ttu-id="4f6bd-315">Legen Sie als HTTP-Methode `GET` fest.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-315">Set the HTTP method to `GET`.</span></span>
* <span data-ttu-id="4f6bd-316">Legen Sie den URI auf `https://localhost:<port>/api/TodoItems/1` fest.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-316">Set the URI to `https://localhost:<port>/api/TodoItems/1`.</span></span> <span data-ttu-id="4f6bd-317">Beispiel: `https://localhost:5001/api/TodoItems/1`.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-317">For example, `https://localhost:5001/api/TodoItems/1`.</span></span>
* <span data-ttu-id="4f6bd-318">Wählen Sie **Send** (Senden) aus.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-318">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="4f6bd-319">Überblick über die GET-Methoden</span><span class="sxs-lookup"><span data-stu-id="4f6bd-319">Examine the GET methods</span></span>

<span data-ttu-id="4f6bd-320">Zwei GET-Endpunkte werden implementiert:</span><span class="sxs-lookup"><span data-stu-id="4f6bd-320">Two GET endpoints are implemented:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="4f6bd-321">Testen Sie die App, indem Sie die beiden Endpunkte in einem Browser oder über Postman aufrufen.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-321">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="4f6bd-322">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="4f6bd-322">For example:</span></span>

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

<span data-ttu-id="4f6bd-323">Durch einen Aufruf von `GetTodoItems` wird eine Antwort gesendet, die der folgenden ähnelt:</span><span class="sxs-lookup"><span data-stu-id="4f6bd-323">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="4f6bd-324">Testen von GET mit Postman</span><span class="sxs-lookup"><span data-stu-id="4f6bd-324">Test Get with Postman</span></span>

* <span data-ttu-id="4f6bd-325">Erstellen Sie eine neue Anforderung.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-325">Create a new request.</span></span>
* <span data-ttu-id="4f6bd-326">Legen Sie die HTTP-Methode auf **GET** fest.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-326">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="4f6bd-327">Legen Sie den Anforderungs-URI auf `https://localhost:<port>/api/TodoItems` fest.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-327">Set the request URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="4f6bd-328">Beispielsweise `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-328">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="4f6bd-329">Wählen Sie in Postman **Two pane view** (Ansicht mit zwei Bereichen) aus.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-329">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="4f6bd-330">Wählen Sie **Send** (Senden) aus.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-330">Select **Send**.</span></span>

<span data-ttu-id="4f6bd-331">Diese App verwendet eine In-Memory-Datenbank.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-331">This app uses an in-memory database.</span></span> <span data-ttu-id="4f6bd-332">Wenn die App angehalten und dann wieder gestartet wird, werden durch die vorherige GET-Anforderung keine Daten zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-332">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="4f6bd-333">Wenn keine Daten zurückgegeben werden, senden Sie mit [POST](#post) Daten an die App.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-333">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="4f6bd-334">Routing und URL-Pfade</span><span class="sxs-lookup"><span data-stu-id="4f6bd-334">Routing and URL paths</span></span>

<span data-ttu-id="4f6bd-335">Das Attribut [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) gibt eine Methode an, die auf eine HTTP GET-Anforderung antwortet.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-335">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="4f6bd-336">Der URL-Pfad für jede Methode wird wie folgt erstellt:</span><span class="sxs-lookup"><span data-stu-id="4f6bd-336">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="4f6bd-337">Beginnen Sie mit der Vorlagenzeichenfolge im `Route`-Attribut des Controllers:</span><span class="sxs-lookup"><span data-stu-id="4f6bd-337">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="4f6bd-338">Ersetzen Sie `[controller]` durch den Namen des Controllers, bei dem es sich standardmäßig um den Namen der Controller-Klasse ohne das Suffix „Controller“ handelt.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-338">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="4f6bd-339">In diesem Beispiel ist der Klassenname des Controllers „**TodoItems** Controller“. Der Controllername lautet also „TodoItems“.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-339">For this sample, the controller class name is **TodoItems** Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="4f6bd-340">Beim ASP.NET Core-[Routing](xref:mvc/controllers/routing) wird die Groß-/Kleinschreibung nicht beachtet.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-340">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="4f6bd-341">Wenn das `[HttpGet]`-Attribut eine Routenvorlage (z. B. `[HttpGet("products")]`) hat, fügen Sie diese an den Pfad an.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-341">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="4f6bd-342">In diesem Beispiel wird keine Vorlage verwendet.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-342">This sample doesn't use a template.</span></span> <span data-ttu-id="4f6bd-343">Weitere Informationen finden Sie unter [Attributrouting mit Http[Verb]-Attributen](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="4f6bd-343">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="4f6bd-344">In der folgenden `GetTodoItem`-Methode ist `"{id}"` eine Platzhaltervariable für den eindeutigen Bezeichner des To-do-Elements.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-344">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="4f6bd-345">Wenn `GetTodoItem` aufgerufen wird, wird der Wert von `"{id}"` in der URL der Methode in ihrem Parameter `id` bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-345">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="4f6bd-346">Rückgabewerte</span><span class="sxs-lookup"><span data-stu-id="4f6bd-346">Return values</span></span>

<span data-ttu-id="4f6bd-347">Der Rückgabetyp der Methoden `GetTodoItems` und `GetTodoItem` ist der [ActionResult\<T>-Typ](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="4f6bd-347">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="4f6bd-348">ASP.NET Core serialisiert automatisch das Objekt in [JSON](https://www.json.org/) und schreibt den JSON-Code in den Text der Antwortnachricht.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-348">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="4f6bd-349">Der Antwortcode für diesen Rückgabetyp ist [200 OK](https://developer.mozilla.org/docs/Web/HTTP/Status/200), vorausgesetzt, es gibt keine Ausnahmefehler.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-349">The response code for this return type is [200 OK](https://developer.mozilla.org/docs/Web/HTTP/Status/200), assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="4f6bd-350">Nicht behandelte Ausnahmen werden in 5xx-Fehler übersetzt.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-350">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="4f6bd-351">`ActionResult`-Rückgabetypen können eine Vielzahl von HTTP-Statuscodes darstellen.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-351">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="4f6bd-352">Beispielsweise kann `GetTodoItem` zwei verschiedene Statuswerte zurückgeben:</span><span class="sxs-lookup"><span data-stu-id="4f6bd-352">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="4f6bd-353">Wenn kein Element mit der angeforderten ID übereinstimmt, gibt die Methode einen <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A>-Fehlercode [Status 404](https://developer.mozilla.org/docs/Web/HTTP/Status/404) zurück.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-353">If no item matches the requested ID, the method returns a [404 status](https://developer.mozilla.org/docs/Web/HTTP/Status/404) <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="4f6bd-354">Andernfalls gibt die Methode 200 mit einem JSON-Antworttext zurück.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-354">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="4f6bd-355">Die Rückgabe von `item` löst eine HTTP 200-Antwort aus.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-355">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="4f6bd-356">PutTodoItem-Methode</span><span class="sxs-lookup"><span data-stu-id="4f6bd-356">The PutTodoItem method</span></span>

<span data-ttu-id="4f6bd-357">Untersuchen Sie die `PutTodoItem`-Methode.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-357">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="4f6bd-358">`PutTodoItem` ähnelt `PostTodoItem`, verwendet allerdings HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-358">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="4f6bd-359">Die Antwort ist [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="4f6bd-359">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="4f6bd-360">Gemäß der HTTP-Spezifikation erfordert eine PUT-Anforderung, dass der Client die gesamte aktualisierte Entität (nicht nur die Änderungen) sendet.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-360">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="4f6bd-361">Verwenden Sie [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute), um Teilupdates zu unterstützen.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-361">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="4f6bd-362">Wenn beim Aufrufen von `PutTodoItem` ein Fehler zurückgegeben wird, rufen Sie `GET` auf, um sicherzustellen, dass die Datenbank ein Element enthält.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-362">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="4f6bd-363">Testen der PutTodoItem-Methode</span><span class="sxs-lookup"><span data-stu-id="4f6bd-363">Test the PutTodoItem method</span></span>

<span data-ttu-id="4f6bd-364">In diesem Beispiel wird eine In-Memory-Datenbank verwendet, die jedes Mal initialisiert werden muss, wenn die App gestartet wird.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-364">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="4f6bd-365">Es muss ein Element in der Datenbank vorhanden sein, bevor Sie einen PUT-Aufruf durchführen.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-365">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="4f6bd-366">Rufen Sie vor einem PUT-Aufruf GET auf, um sicherzustellen, dass ein Element in der Datenbank vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-366">Call GET to ensure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="4f6bd-367">Aktualisieren Sie das To-do-Element, das über den ID-Wert 1 verfügt, und legen Sie als Namen `"feed fish"` fest:</span><span class="sxs-lookup"><span data-stu-id="4f6bd-367">Update the to-do item that has Id = 1 and set its name to `"feed fish"`:</span></span>

```json
  {
    "Id":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="4f6bd-368">Die folgende Abbildung zeigt das Postman-Update:</span><span class="sxs-lookup"><span data-stu-id="4f6bd-368">The following image shows the Postman update:</span></span>

![Postman-Konsole mit der Antwort „204 (Kein Inhalt)“](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="4f6bd-370">DeleteTodoItem-Methode</span><span class="sxs-lookup"><span data-stu-id="4f6bd-370">The DeleteTodoItem method</span></span>

<span data-ttu-id="4f6bd-371">Untersuchen Sie die `DeleteTodoItem`-Methode.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-371">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="4f6bd-372">Testen der DeleteTodoItem-Methode</span><span class="sxs-lookup"><span data-stu-id="4f6bd-372">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="4f6bd-373">So löschen Sie mit Postman eine Aufgabe</span><span class="sxs-lookup"><span data-stu-id="4f6bd-373">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="4f6bd-374">Legen Sie die Methode auf `DELETE` fest.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-374">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="4f6bd-375">Legen Sie den URI des zu löschenden Objekts fest, z. B. `https://localhost:5001/api/TodoItems/1`.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-375">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="4f6bd-376">Wählen Sie **Send** (Senden) aus.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-376">Select **Send**.</span></span>

<a name="over-post-v5"></a>

## <a name="prevent-over-posting"></a><span data-ttu-id="4f6bd-377">Vermeiden von Overposting</span><span class="sxs-lookup"><span data-stu-id="4f6bd-377">Prevent over-posting</span></span>

<span data-ttu-id="4f6bd-378">Derzeit macht die Beispiel-App das gesamte `TodoItem`-Objekt verfügbar.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-378">Currently the sample app exposes the entire `TodoItem` object.</span></span> <span data-ttu-id="4f6bd-379">In den Produktions-Apps sind die Daten, die eingegeben und mithilfe einer Teilmenge des Modells zurückgegeben werden, in der Regel eingeschränkt.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-379">Production apps typically limit the data that's input and returned using a subset of the model.</span></span> <span data-ttu-id="4f6bd-380">Hierfür gibt es mehrere Gründe, wobei die Sicherheit einer der Hauptgründe ist.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-380">There are multiple reasons behind this and security is a major one.</span></span> <span data-ttu-id="4f6bd-381">Die Teilmenge eines Modells wird üblicherweise als Datenübertragungsobjekt (DTO, Data Transfer Object), Eingabemodell oder Anzeigemodell bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-381">The subset of a model is usually referred to as a Data Transfer Object (DTO), input model, or view model.</span></span> <span data-ttu-id="4f6bd-382">In diesem Artikel wird das **DTO** verwendet.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-382">**DTO** is used in this article.</span></span>

<span data-ttu-id="4f6bd-383">Ein DTO kann für Folgendes verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="4f6bd-383">A DTO may be used to:</span></span>

* <span data-ttu-id="4f6bd-384">Vermeiden Sie Overposting.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-384">Prevent over-posting.</span></span>
* <span data-ttu-id="4f6bd-385">Ausblenden von Eigenschaften, die Clients nicht anzeigen sollen</span><span class="sxs-lookup"><span data-stu-id="4f6bd-385">Hide properties that clients are not supposed to view.</span></span>
* <span data-ttu-id="4f6bd-386">Auslassen einiger Eigenschaften, um die Nutzlast zu verringern</span><span class="sxs-lookup"><span data-stu-id="4f6bd-386">Omit some properties in order to reduce payload size.</span></span>
* <span data-ttu-id="4f6bd-387">Vereinfachen von Objektgraphen, die geschachtelte Objekte enthalten</span><span class="sxs-lookup"><span data-stu-id="4f6bd-387">Flatten object graphs that contain nested objects.</span></span> <span data-ttu-id="4f6bd-388">Vereinfachte Objektgraphen können für Clients zweckmäßiger sein.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-388">Flattened object graphs can be more convenient for clients.</span></span>

<span data-ttu-id="4f6bd-389">Um den DTO-Ansatz zu veranschaulichen, aktualisieren Sie die `TodoItem`-Klasse, sodass sie ein geheimes Feld einschließt:</span><span class="sxs-lookup"><span data-stu-id="4f6bd-389">To demonstrate the DTO approach, update the `TodoItem` class to include a secret field:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApiDTO/Models/TodoItem.cs?name=snippet&highlight=8)]

<span data-ttu-id="4f6bd-390">Das geheime Feld muss in dieser App ausgeblendet werden, eine administrative App kann es jedoch verfügbar machen.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-390">The secret field needs to be hidden from this app, but an administrative app could choose to expose it.</span></span>

<span data-ttu-id="4f6bd-391">Vergewissern Sie sich, dass Sie das geheime Feld veröffentlichen und abrufen können.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-391">Verify you can post and get the secret field.</span></span>

<span data-ttu-id="4f6bd-392">Erstellen Sie ein DTO-Modell:</span><span class="sxs-lookup"><span data-stu-id="4f6bd-392">Create a DTO model:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApiDTO/Models/TodoItemDTO.cs?name=snippet)]

<span data-ttu-id="4f6bd-393">Aktualisieren Sie `TodoItemsController`, sodass `TodoItemDTO` verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="4f6bd-393">Update the `TodoItemsController` to use `TodoItemDTO`:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

<span data-ttu-id="4f6bd-394">Vergewissern Sie sich, dass Sie das geheime Feld weder veröffentlichen noch abrufen können.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-394">Verify you can't post or get the secret field.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="4f6bd-395">Aufrufen der Web-API mit JavaScript</span><span class="sxs-lookup"><span data-stu-id="4f6bd-395">Call the web API with JavaScript</span></span>

<span data-ttu-id="4f6bd-396">Mehr dazu finden Sie im [-Tutorial: Aufrufen einer ASP.NET Core-Web-API mit JavaScript](xref:tutorials/web-api-javascript).</span><span class="sxs-lookup"><span data-stu-id="4f6bd-396">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="4f6bd-397">In diesem Tutorial lernen Sie, wie die folgenden Aufgaben ausgeführt werden:</span><span class="sxs-lookup"><span data-stu-id="4f6bd-397">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="4f6bd-398">Erstellen eines Web-API-Projekts</span><span class="sxs-lookup"><span data-stu-id="4f6bd-398">Create a web API project.</span></span>
> * <span data-ttu-id="4f6bd-399">Hinzufügen einer Modellklasse und eines Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="4f6bd-399">Add a model class and a database context.</span></span>
> * <span data-ttu-id="4f6bd-400">Erstellen eines Controllergerüsts mit CRUD-Methoden</span><span class="sxs-lookup"><span data-stu-id="4f6bd-400">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="4f6bd-401">Konfigurieren des Routings, der URL-Pfade und der Rückgabewerte</span><span class="sxs-lookup"><span data-stu-id="4f6bd-401">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="4f6bd-402">Aufrufen der Web-API mit Postman</span><span class="sxs-lookup"><span data-stu-id="4f6bd-402">Call the web API with Postman.</span></span>

<span data-ttu-id="4f6bd-403">Abschließend steht Ihnen eine Web-API zur Verfügung, die in einer relationalen Datenbank gespeicherte To-do-Elemente verwalten kann.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-403">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="4f6bd-404">Übersicht</span><span class="sxs-lookup"><span data-stu-id="4f6bd-404">Overview</span></span>

<span data-ttu-id="4f6bd-405">In diesem Tutorial wird die folgende API erstellt:</span><span class="sxs-lookup"><span data-stu-id="4f6bd-405">This tutorial creates the following API:</span></span>

|<span data-ttu-id="4f6bd-406">API</span><span class="sxs-lookup"><span data-stu-id="4f6bd-406">API</span></span> | <span data-ttu-id="4f6bd-407">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="4f6bd-407">Description</span></span> | <span data-ttu-id="4f6bd-408">Anforderungstext</span><span class="sxs-lookup"><span data-stu-id="4f6bd-408">Request body</span></span> | <span data-ttu-id="4f6bd-409">Antworttext</span><span class="sxs-lookup"><span data-stu-id="4f6bd-409">Response body</span></span> |
|--- | ---- | ---- | ---- |
|`GET /api/TodoItems` | <span data-ttu-id="4f6bd-410">Alle To-do-Elemente abrufen</span><span class="sxs-lookup"><span data-stu-id="4f6bd-410">Get all to-do items</span></span> | <span data-ttu-id="4f6bd-411">Keine</span><span class="sxs-lookup"><span data-stu-id="4f6bd-411">None</span></span> | <span data-ttu-id="4f6bd-412">Array von To-do-Elementen</span><span class="sxs-lookup"><span data-stu-id="4f6bd-412">Array of to-do items</span></span>|
|`GET /api/TodoItems/{id}` | <span data-ttu-id="4f6bd-413">Ein Element nach ID abrufen</span><span class="sxs-lookup"><span data-stu-id="4f6bd-413">Get an item by ID</span></span> | <span data-ttu-id="4f6bd-414">Keine</span><span class="sxs-lookup"><span data-stu-id="4f6bd-414">None</span></span> | <span data-ttu-id="4f6bd-415">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="4f6bd-415">To-do item</span></span>|
|`POST /api/TodoItems` | <span data-ttu-id="4f6bd-416">Neues Element hinzufügen</span><span class="sxs-lookup"><span data-stu-id="4f6bd-416">Add a new item</span></span> | <span data-ttu-id="4f6bd-417">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="4f6bd-417">To-do item</span></span> | <span data-ttu-id="4f6bd-418">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="4f6bd-418">To-do item</span></span> |
|`PUT /api/TodoItems/{id}` | <span data-ttu-id="4f6bd-419">Vorhandenes Element aktualisieren &nbsp;</span><span class="sxs-lookup"><span data-stu-id="4f6bd-419">Update an existing item &nbsp;</span></span> | <span data-ttu-id="4f6bd-420">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="4f6bd-420">To-do item</span></span> | <span data-ttu-id="4f6bd-421">Keine</span><span class="sxs-lookup"><span data-stu-id="4f6bd-421">None</span></span> |
|<span data-ttu-id="4f6bd-422">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="4f6bd-422">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span></span> | <span data-ttu-id="4f6bd-423">Löschen eines Elements &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="4f6bd-423">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="4f6bd-424">Keine</span><span class="sxs-lookup"><span data-stu-id="4f6bd-424">None</span></span> | <span data-ttu-id="4f6bd-425">Keine</span><span class="sxs-lookup"><span data-stu-id="4f6bd-425">None</span></span>|

<span data-ttu-id="4f6bd-426">Das folgende Diagramm zeigt den Entwurf der App.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-426">The following diagram shows the design of the app.</span></span>

![Der Client ist das Feld ganz links.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="4f6bd-432">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="4f6bd-432">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4f6bd-433">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4f6bd-433">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="4f6bd-434">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4f6bd-434">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="4f6bd-435">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="4f6bd-435">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="4f6bd-436">Erstellen eines Webprojekts</span><span class="sxs-lookup"><span data-stu-id="4f6bd-436">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4f6bd-437">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4f6bd-437">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4f6bd-438">Klicken Sie im Menü **Datei** auf **Neu** > **Projekt**.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-438">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="4f6bd-439">Wählen Sie die Vorlage **ASP.NET Core-Webanwendung** aus, und klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-439">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="4f6bd-440">Geben Sie dem Projekt den Namen *TodoApi*, und klicken Sie auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-440">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="4f6bd-441">Vergewissern Sie sich, dass im Dialogfeld **Neue ASP.NET Core-Webanwendung erstellen** die Optionen **.NET Core** und **ASP.NET Core 3.1** ausgewählt sind.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-441">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.1** are selected.</span></span> <span data-ttu-id="4f6bd-442">Wählen Sie die Vorlage **API** aus, und klicken Sie auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-442">Select the **API** template and click **Create**.</span></span>

![VS-Dialogfeld „Neues Projekt“](first-web-api/_static/vs3.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="4f6bd-444">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4f6bd-444">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="4f6bd-445">Öffnen Sie das [integrierte Terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="4f6bd-445">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="4f6bd-446">Wechseln Sie mit dem Befehl `cd`' zu dem Ordner, der den Projektordner enthalten soll.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-446">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="4f6bd-447">Führen Sie die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="4f6bd-447">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="4f6bd-448">Wenn Sie in einem Dialogfeld angeben müssen, ob Sie dem Projekt die erforderlichen Elemente hinzufügen möchten, wählen Sie **Ja** aus.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-448">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="4f6bd-449">Die obenstehenden Befehle haben folgende Konsequenzen:</span><span class="sxs-lookup"><span data-stu-id="4f6bd-449">The preceding commands:</span></span>

  * <span data-ttu-id="4f6bd-450">Ein neues Web-API-Projekt wird erstellt und in Visual Studio Code geöffnet.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-450">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="4f6bd-451">Die NuGet-Pakete, die im nächsten Abschnitt erforderlich sind, werden hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-451">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="4f6bd-452">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="4f6bd-452">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="4f6bd-453">Klicken Sie auf **Datei** > **Neue Projektmappe**.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-453">Select **File** > **New Solution**.</span></span>

  ![Neue Projektmappe in macOS](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="4f6bd-455">Navigieren Sie in Visual Studio für Mac-Versionen vor Version 8.6 zu **.NET Core** > **App** > **API** > **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-455">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="4f6bd-456">Bei Version 8.6 oder höher klicken Sie auf **Web and Console** > **App** > **API** > **Weiter** (Web und Konsole).</span><span class="sxs-lookup"><span data-stu-id="4f6bd-456">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>

  ![Auswählen von macOS-API-Vorlagen](first-web-api-mac/_static/api_template.png)

* <span data-ttu-id="4f6bd-458">Wählen Sie im Dialogfeld **Neue ASP.NET Core-Web-API konfigurieren** die neueste .NET Core 3.x-Version als **Zielframework** aus.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-458">In the **Configure the new ASP.NET Core Web API** dialog, select the latest .NET Core 3.x **Target Framework**.</span></span> <span data-ttu-id="4f6bd-459">Klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-459">Select **Next**.</span></span>

* <span data-ttu-id="4f6bd-460">Geben Sie für **Projektname** *TodoApi* ein, und wählen Sie dann **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-460">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![Dialogfeld „Konfiguration“](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="4f6bd-462">Öffnen Sie im Projektordner ein Befehlsterminal, und führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="4f6bd-462">Open a command terminal in the project folder and run the following command:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-api"></a><span data-ttu-id="4f6bd-463">Testen der API</span><span class="sxs-lookup"><span data-stu-id="4f6bd-463">Test the API</span></span>

<span data-ttu-id="4f6bd-464">Die Projektvorlage erstellt eine `WeatherForecast`-API.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-464">The project template creates a `WeatherForecast` API.</span></span> <span data-ttu-id="4f6bd-465">Rufen Sie in einem Browser die `Get`-Methode zum Testen der App auf.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-465">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4f6bd-466">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4f6bd-466">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="4f6bd-467">Drücken Sie STRG+F5, um die App auszuführen.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-467">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="4f6bd-468">Visual Studio startet einen Browser und navigiert zu `https://localhost:<port>/WeatherForecast`, wobei es sich bei `<port>` um eine zufällig ausgewählte Portnummer handelt.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-468">Visual Studio launches a browser and navigates to `https://localhost:<port>/WeatherForecast`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="4f6bd-469">Wenn Sie in einem Dialogfeld angeben müssen, ob Sie dem IIS Express-Zertifikat vertrauen, wählen Sie **Ja** aus.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-469">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="4f6bd-470">Wählen Sie im folgenden Dialogfeld **Sicherheitswarnung** die Option **Ja** aus.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-470">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="4f6bd-471">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4f6bd-471">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="4f6bd-472">Drücken Sie STRG+F5, um die App auszuführen.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-472">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="4f6bd-473">Navigieren Sie in einem Browser zur folgenden URL: `https://localhost:5001/WeatherForecast`.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-473">In a browser, go to following URL: `https://localhost:5001/WeatherForecast`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="4f6bd-474">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="4f6bd-474">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="4f6bd-475">Wählen Sie **Ausführen** > **Debugging starten** aus, um die App zu starten.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-475">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="4f6bd-476">Visual Studio für Mac startet einen Browser und navigiert zu `https://localhost:<port>`, wobei es sich bei `<port>` um eine zufällig ausgewählte Portnummer handelt.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-476">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="4f6bd-477">Der Fehler „HTTP 404: Nicht gefunden“ wird zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-477">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="4f6bd-478">Fügen Sie `/WeatherForecast` an die URL an, d. h., ändern Sie sie in `https://localhost:<port>/WeatherForecast`.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-478">Append `/WeatherForecast` to the URL (change the URL to `https://localhost:<port>/WeatherForecast`).</span></span>

---

<span data-ttu-id="4f6bd-479">Der zurückgegebene JSON-Code sieht in etwa wie folgt aus:</span><span class="sxs-lookup"><span data-stu-id="4f6bd-479">JSON similar to the following is returned:</span></span>

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

## <a name="add-a-model-class"></a><span data-ttu-id="4f6bd-480">Hinzufügen einer Modellklasse</span><span class="sxs-lookup"><span data-stu-id="4f6bd-480">Add a model class</span></span>

<span data-ttu-id="4f6bd-481">Ein *Modell* ist eine Gruppe von Klassen, die die Daten darstellen, die die App verwaltet.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-481">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="4f6bd-482">Das Modell für diese App ist eine einzelne `TodoItem`-Klasse.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-482">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4f6bd-483">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4f6bd-483">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4f6bd-484">Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-484">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="4f6bd-485">Klicken Sie auf **Hinzufügen** > **Neuer Ordner**.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-485">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="4f6bd-486">Geben Sie dem Ordner den Namen *Models* .</span><span class="sxs-lookup"><span data-stu-id="4f6bd-486">Name the folder *Models*.</span></span>

* <span data-ttu-id="4f6bd-487">Klicken Sie mit der rechten Maustaste auf den Ordner *Models* , und wählen Sie **Hinzufügen** > **Klasse** aus.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-487">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="4f6bd-488">Nennen Sie die Klasse *TodoItem*, und wählen Sie **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-488">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="4f6bd-489">Ersetzen Sie den Vorlagencode durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="4f6bd-489">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="4f6bd-490">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4f6bd-490">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="4f6bd-491">Fügen Sie einen Ordner mit dem Namen *Models* hinzu.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-491">Add a folder named *Models*.</span></span>

* <span data-ttu-id="4f6bd-492">Fügen Sie dem Ordner *Models* mit dem folgenden Code eine `TodoItem`-Klasse hinzu:</span><span class="sxs-lookup"><span data-stu-id="4f6bd-492">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="4f6bd-493">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="4f6bd-493">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="4f6bd-494">Klicken Sie mit der rechten Maustaste auf das Projekt.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-494">Right-click the project.</span></span> <span data-ttu-id="4f6bd-495">Klicken Sie auf **Hinzufügen** > **Neuer Ordner**.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-495">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="4f6bd-496">Geben Sie dem Ordner den Namen *Models* .</span><span class="sxs-lookup"><span data-stu-id="4f6bd-496">Name the folder *Models*.</span></span>

  ![Neuer Ordner](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="4f6bd-498">Klicken Sie mit der rechten Maustaste auf den Ordner *Models* , und wählen Sie **Hinzufügen** > **Neue Datei** > **Allgemein** > **Leere Klasse** aus.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-498">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="4f6bd-499">Nennen Sie die Klasse *TodoItem*, und klicken Sie dann auf **Neu**.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-499">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="4f6bd-500">Ersetzen Sie den Vorlagencode durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="4f6bd-500">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs?name=snippet)]

<span data-ttu-id="4f6bd-501">Die `Id`-Eigenschaft fungiert als eindeutiger Schlüssel in einer relationalen Datenbank.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-501">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="4f6bd-502">Modellklassen können überall im Projekt platziert werden, doch gemäß der Konvention wird der Ordner *Models* verwendet.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-502">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="4f6bd-503">Hinzufügen eines Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="4f6bd-503">Add a database context</span></span>

<span data-ttu-id="4f6bd-504">Der *Datenbankkontext* ist die Hauptklasse, die die Entity Framework-Funktionen für ein Datenmodell koordiniert.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-504">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="4f6bd-505">Diese Klasse wird durch Ableiten von der `Microsoft.EntityFrameworkCore.DbContext`-Klasse erstellt.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-505">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4f6bd-506">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4f6bd-506">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-nuget-packages"></a><span data-ttu-id="4f6bd-507">Hinzufügen von NuGet-Paketen</span><span class="sxs-lookup"><span data-stu-id="4f6bd-507">Add NuGet packages</span></span>

* <span data-ttu-id="4f6bd-508">Klicken Sie im Menü **Extras** auf **NuGet-Paket-Manager > NuGet-Pakete für Projektmappe verwalten...** .</span><span class="sxs-lookup"><span data-stu-id="4f6bd-508">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="4f6bd-509">Klicken Sie auf die Registerkarte **Durchsuchen**, und geben Sie dann **Microsoft.EntityFrameworkCore.InMemory** in das Suchfeld ein.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-509">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.InMemory** in the search box.</span></span>
* <span data-ttu-id="4f6bd-510">Wählen Sie im linken Bereich **Microsoft.EntityFrameworkCore.InMemory** aus.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-510">Select **Microsoft.EntityFrameworkCore.InMemory** in the left pane.</span></span>
* <span data-ttu-id="4f6bd-511">Aktivieren Sie das Kontrollkästchen **Projekt** im rechten Bereich, und klicken Sie dann auf **Installieren**.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-511">Select the **Project** check box in the right pane and then select **Install**.</span></span>

![NuGet-Paket-Manager](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="4f6bd-513">Hinzufügen des TodoContext-Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="4f6bd-513">Add the TodoContext database context</span></span>

* <span data-ttu-id="4f6bd-514">Klicken Sie mit der rechten Maustaste auf den Ordner *Models* , und wählen Sie **Hinzufügen** > **Klasse** aus.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-514">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="4f6bd-515">Nennen Sie die Klasse *TodoContext*, und klicken Sie auf **Hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-515">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="4f6bd-516">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="4f6bd-516">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="4f6bd-517">Fügen Sie eine `TodoContext`-Klasse zum Ordner *Models* hinzu.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-517">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="4f6bd-518">Geben Sie den folgenden Code ein:</span><span class="sxs-lookup"><span data-stu-id="4f6bd-518">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="4f6bd-519">Registrieren des Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="4f6bd-519">Register the database context</span></span>

<span data-ttu-id="4f6bd-520">In ASP.NET Core müssen Dienste wie der Datenbankkontext mit dem Container [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) registriert werden.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-520">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="4f6bd-521">Der Container stellt den Dienst für Controller bereit.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-521">The container provides the service to controllers.</span></span>

<span data-ttu-id="4f6bd-522">Aktualisieren Sie *Startup.cs* mit dem folgenden hervorgehobenen Code:</span><span class="sxs-lookup"><span data-stu-id="4f6bd-522">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="4f6bd-523">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="4f6bd-523">The preceding code:</span></span>

* <span data-ttu-id="4f6bd-524">Entfernt nicht benötigte `using`-Deklarationen</span><span class="sxs-lookup"><span data-stu-id="4f6bd-524">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="4f6bd-525">Fügt dem Abhängigkeitscontainer den Datenbankkontext hinzu</span><span class="sxs-lookup"><span data-stu-id="4f6bd-525">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="4f6bd-526">Gibt an, dass der Datenbankkontext eine In-Memory Database verwendet</span><span class="sxs-lookup"><span data-stu-id="4f6bd-526">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="4f6bd-527">Erstellen eines Controllergerüsts</span><span class="sxs-lookup"><span data-stu-id="4f6bd-527">Scaffold a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4f6bd-528">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4f6bd-528">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4f6bd-529">Klicken Sie mit der rechten Maustaste auf den Ordner *Controller*.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-529">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="4f6bd-530">Wählen Sie **Hinzufügen** > **Neues Gerüstelement** aus.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-530">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="4f6bd-531">Klicken Sie auf **API-Controller mit Aktionen unter Verwendung von Entity Framework** und dann auf **Hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-531">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="4f6bd-532">Führen Sie im Dialogfeld **API-Controller mit Aktionen unter Verwendung von Entity Framework** folgende Schritte aus:</span><span class="sxs-lookup"><span data-stu-id="4f6bd-532">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="4f6bd-533">Wählen Sie in der **Modellklasse** das Element **TodoItem (TodoApi.Models)** aus.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-533">Select **TodoItem (TodoApi.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="4f6bd-534">Wählen Sie in der **Datenkontextklasse** das Element **TodoContext (TodoApi.Models)** aus.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-534">Select **TodoContext (TodoApi.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="4f6bd-535">Wählen Sie **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-535">Select **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="4f6bd-536">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="4f6bd-536">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="4f6bd-537">Führen Sie die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="4f6bd-537">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet tool update -g Dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="4f6bd-538">Die obenstehenden Befehle haben folgende Konsequenzen:</span><span class="sxs-lookup"><span data-stu-id="4f6bd-538">The preceding commands:</span></span>

* <span data-ttu-id="4f6bd-539">Die NuGet-Pakete für den Gerüstbau werden hinzufügt.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-539">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="4f6bd-540">Die Gerüstbau-Engine (`dotnet-aspnet-codegenerator`) wird installiert.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-540">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="4f6bd-541">Das Gerüst für `TodoItemsController` wird erstellt.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-541">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="4f6bd-542">Der generierte Code hat folgende Auswirkungen:</span><span class="sxs-lookup"><span data-stu-id="4f6bd-542">The generated code:</span></span>

* <span data-ttu-id="4f6bd-543">Markiert die Klasse mit dem [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute)-Attribut.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-543">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="4f6bd-544">Dieses Attribut gibt an, dass der Controller auf Web-API-Anforderungen reagiert.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-544">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="4f6bd-545">Weitere Informationen zu bestimmten Verhaltensweisen, die das Attribut ermöglicht, finden Sie unter <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-545">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="4f6bd-546">Verwendet die Abhängigkeitsinjektion, um den Datenbankkontext (`TodoContext`) dem Controller hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-546">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="4f6bd-547">Der Datenbankkontext wird in den einzelnen [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete)-Methoden im Controller verwendet.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-547">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<span data-ttu-id="4f6bd-548">Die ASP.NET Core-Vorlagen für:</span><span class="sxs-lookup"><span data-stu-id="4f6bd-548">The ASP.NET Core templates for:</span></span>

* <span data-ttu-id="4f6bd-549">Controller mit Ansichten enthalten `[action]` in der Routenvorlage.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-549">Controllers with views include `[action]` in the route template.</span></span>
* <span data-ttu-id="4f6bd-550">API-Controller enthalten keine `[action]` in der Routenvorlage.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-550">API controllers don't include `[action]` in the route template.</span></span>

<span data-ttu-id="4f6bd-551">Wenn sich das `[action]`-Token nicht in der Routenvorlage befindet, wird der [action](xref:mvc/controllers/routing#action)-Name von der Route ausgeschlossen.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-551">When the `[action]` token isn't in the route template, the [action](xref:mvc/controllers/routing#action) name is excluded from the route.</span></span> <span data-ttu-id="4f6bd-552">Dies bedeutet, dass der zugehörige Methodenname der Aktion nicht in der übereinstimmenden Route verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-552">That is, the action's associated method name isn't used in the matching route.</span></span>

## <a name="examine-the-posttodoitem-create-method"></a><span data-ttu-id="4f6bd-553">Überblick über die PostTodoItem-Erstellungsmethode</span><span class="sxs-lookup"><span data-stu-id="4f6bd-553">Examine the PostTodoItem create method</span></span>

<span data-ttu-id="4f6bd-554">Ersetzen Sie die Rückgabeanweisung in `PostTodoItem` durch eine Anweisung mit dem [nameof](/dotnet/csharp/language-reference/operators/nameof)-Operator:</span><span class="sxs-lookup"><span data-stu-id="4f6bd-554">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="4f6bd-555">Der oben stehende Code ist eine HTTP POST-Methode, wie durch das [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute)-Attribut angegeben.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-555">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="4f6bd-556">Die Methode ruft den Wert der Aufgabe aus dem Text der HTTP-Anforderung ab.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-556">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="4f6bd-557">Weitere Informationen finden Sie unter [Attributrouting mit Http[Verb]-Attributen](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="4f6bd-557">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="4f6bd-558">Die <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*>-Methode:</span><span class="sxs-lookup"><span data-stu-id="4f6bd-558">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="4f6bd-559">Gibt den HTTP-Statuscode 201 zurück, wenn der Vorgang erfolgreich ist.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-559">Returns an HTTP 201 status code if successful.</span></span> <span data-ttu-id="4f6bd-560">HTTP 201 ist die Standardantwort für eine HTTP POST-Methode, die eine neue Ressource auf dem Server erstellt.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-560">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="4f6bd-561">Fügt der Antwort einen [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location)-Header hinzu.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-561">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="4f6bd-562">Der `Location`-Header legt den [URI](https://developer.mozilla.org/docs/Glossary/URI) des neu erstellten To-do-Elements fest.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-562">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="4f6bd-563">Weitere Informationen finden Sie unter [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="4f6bd-563">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="4f6bd-564">Verweist auf die `GetTodoItem`-Aktion zum Erstellen des URIs des `Location`-Headers.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-564">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="4f6bd-565">Das `nameof`-Schlüsselwort von C# wird verwendet, um eine Hartcodierung des Aktionsnamens im `CreatedAtAction`-Aufruf zu vermeiden.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-565">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="4f6bd-566">Installieren von Postman</span><span class="sxs-lookup"><span data-stu-id="4f6bd-566">Install Postman</span></span>

<span data-ttu-id="4f6bd-567">Dieses Tutorial verwendet Postman zum Testen der Web-API.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-567">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="4f6bd-568">Installieren Sie [Postman](https://www.getpostman.com/downloads/).</span><span class="sxs-lookup"><span data-stu-id="4f6bd-568">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="4f6bd-569">Starten Sie die Web-App.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-569">Start the web app.</span></span>
* <span data-ttu-id="4f6bd-570">Starten Sie Postman.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-570">Start Postman.</span></span>
* <span data-ttu-id="4f6bd-571">Deaktivieren Sie **SSL certificate verification** (Verifizierung des SSL-Zertifikats).</span><span class="sxs-lookup"><span data-stu-id="4f6bd-571">Disable **SSL certificate verification**</span></span>
  * <span data-ttu-id="4f6bd-572">Deaktivieren Sie auf der Registerkarte **General** (Allgemein) unter File > Settings (**Datei** > **Einstellungen**) **SSL certificate verification** (Verifizierung des SSL-Zertifikats).</span><span class="sxs-lookup"><span data-stu-id="4f6bd-572">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="4f6bd-573">Aktivieren Sie die Verifizierung des SSL-Zertifikats wieder, nachdem Sie den Controller getestet haben.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-573">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="4f6bd-574">Testen von PostTodoItem mit Postman</span><span class="sxs-lookup"><span data-stu-id="4f6bd-574">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="4f6bd-575">Erstellen Sie eine neue Anforderung.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-575">Create a new request.</span></span>
* <span data-ttu-id="4f6bd-576">Legen Sie als HTTP-Methode `POST` fest.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-576">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="4f6bd-577">Legen Sie den URI auf `https://localhost:<port>/api/TodoItems` fest.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-577">Set the URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="4f6bd-578">Beispiel: `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-578">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="4f6bd-579">Wählen Sie die Registerkarte **Body** (Text) aus.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-579">Select the **Body** tab.</span></span>
* <span data-ttu-id="4f6bd-580">Wählen Sie das Optionsfeld **raw** (Unformatiert) aus.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-580">Select the **raw** radio button.</span></span>
* <span data-ttu-id="4f6bd-581">Legen Sie den Typ auf **JSON (application/json)** fest.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-581">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="4f6bd-582">Geben Sie für die Aufgabe den Anforderungstext im JSON-Format ein:</span><span class="sxs-lookup"><span data-stu-id="4f6bd-582">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="4f6bd-583">Wählen Sie **Send** (Senden) aus.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-583">Select **Send**.</span></span>

  ![Postman mit erstellter Anforderung](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri-with-postman"></a><span data-ttu-id="4f6bd-585">Testen des Adressheader-URIs mit Postman</span><span class="sxs-lookup"><span data-stu-id="4f6bd-585">Test the location header URI with Postman</span></span>

* <span data-ttu-id="4f6bd-586">Wählen Sie auf der Registerkarte **Header** (Header) den Bereich **Response** (Antwort) aus.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-586">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="4f6bd-587">Kopieren Sie den den Headerwert von **Location** (Speicherort):</span><span class="sxs-lookup"><span data-stu-id="4f6bd-587">Copy the **Location** header value:</span></span>

  ![Registerkarte „Header“ in der Postman-Konsole](first-web-api/_static/3/create.png)

* <span data-ttu-id="4f6bd-589">Legen Sie als HTTP-Methode `GET` fest.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-589">Set the HTTP method to `GET`.</span></span>
* <span data-ttu-id="4f6bd-590">Legen Sie den URI auf `https://localhost:<port>/api/TodoItems/1` fest.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-590">Set the URI to `https://localhost:<port>/api/TodoItems/1`.</span></span> <span data-ttu-id="4f6bd-591">Beispiel: `https://localhost:5001/api/TodoItems/1`.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-591">For example, `https://localhost:5001/api/TodoItems/1`.</span></span>
* <span data-ttu-id="4f6bd-592">Wählen Sie **Send** (Senden) aus.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-592">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="4f6bd-593">Überblick über die GET-Methoden</span><span class="sxs-lookup"><span data-stu-id="4f6bd-593">Examine the GET methods</span></span>

<span data-ttu-id="4f6bd-594">Diese Methoden implementieren zwei GET-Endpunkte:</span><span class="sxs-lookup"><span data-stu-id="4f6bd-594">These methods implement two GET endpoints:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="4f6bd-595">Testen Sie die App, indem Sie die beiden Endpunkte in einem Browser oder über Postman aufrufen.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-595">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="4f6bd-596">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="4f6bd-596">For example:</span></span>

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

<span data-ttu-id="4f6bd-597">Durch einen Aufruf von `GetTodoItems` wird eine Antwort gesendet, die der folgenden ähnelt:</span><span class="sxs-lookup"><span data-stu-id="4f6bd-597">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="4f6bd-598">Testen von GET mit Postman</span><span class="sxs-lookup"><span data-stu-id="4f6bd-598">Test Get with Postman</span></span>

* <span data-ttu-id="4f6bd-599">Erstellen Sie eine neue Anforderung.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-599">Create a new request.</span></span>
* <span data-ttu-id="4f6bd-600">Legen Sie die HTTP-Methode auf **GET** fest.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-600">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="4f6bd-601">Legen Sie den Anforderungs-URI auf `https://localhost:<port>/api/TodoItems` fest.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-601">Set the request URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="4f6bd-602">Beispielsweise `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-602">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="4f6bd-603">Wählen Sie in Postman **Two pane view** (Ansicht mit zwei Bereichen) aus.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-603">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="4f6bd-604">Wählen Sie **Send** (Senden) aus.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-604">Select **Send**.</span></span>

<span data-ttu-id="4f6bd-605">Diese App verwendet eine In-Memory-Datenbank.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-605">This app uses an in-memory database.</span></span> <span data-ttu-id="4f6bd-606">Wenn die App angehalten und dann wieder gestartet wird, werden durch die vorherige GET-Anforderung keine Daten zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-606">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="4f6bd-607">Wenn keine Daten zurückgegeben werden, senden Sie mit [POST](#post) Daten an die App.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-607">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="4f6bd-608">Routing und URL-Pfade</span><span class="sxs-lookup"><span data-stu-id="4f6bd-608">Routing and URL paths</span></span>

<span data-ttu-id="4f6bd-609">Das Attribut [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) gibt eine Methode an, die auf eine HTTP GET-Anforderung antwortet.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-609">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="4f6bd-610">Der URL-Pfad für jede Methode wird wie folgt erstellt:</span><span class="sxs-lookup"><span data-stu-id="4f6bd-610">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="4f6bd-611">Beginnen Sie mit der Vorlagenzeichenfolge im `Route`-Attribut des Controllers:</span><span class="sxs-lookup"><span data-stu-id="4f6bd-611">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="4f6bd-612">Ersetzen Sie `[controller]` durch den Namen des Controllers, bei dem es sich standardmäßig um den Namen der Controller-Klasse ohne das Suffix „Controller“ handelt.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-612">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="4f6bd-613">In diesem Beispiel ist der Klassenname des Controllers „**TodoItems** Controller“. Der Controllername lautet also „TodoItems“.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-613">For this sample, the controller class name is **TodoItems** Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="4f6bd-614">Beim ASP.NET Core-[Routing](xref:mvc/controllers/routing) wird die Groß-/Kleinschreibung nicht beachtet.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-614">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="4f6bd-615">Wenn das `[HttpGet]`-Attribut eine Routenvorlage (z. B. `[HttpGet("products")]`) hat, fügen Sie diese an den Pfad an.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-615">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="4f6bd-616">In diesem Beispiel wird keine Vorlage verwendet.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-616">This sample doesn't use a template.</span></span> <span data-ttu-id="4f6bd-617">Weitere Informationen finden Sie unter [Attributrouting mit Http[Verb]-Attributen](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="4f6bd-617">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="4f6bd-618">In der folgenden `GetTodoItem`-Methode ist `"{id}"` eine Platzhaltervariable für den eindeutigen Bezeichner des To-do-Elements.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-618">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="4f6bd-619">Wenn `GetTodoItem` aufgerufen wird, wird der Wert von `"{id}"` in der URL der Methode in ihrem Parameter `id` bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-619">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="4f6bd-620">Rückgabewerte</span><span class="sxs-lookup"><span data-stu-id="4f6bd-620">Return values</span></span> 

<span data-ttu-id="4f6bd-621">Der Rückgabetyp der Methoden `GetTodoItems` und `GetTodoItem` ist der [ActionResult\<T>-Typ](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="4f6bd-621">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="4f6bd-622">ASP.NET Core serialisiert automatisch das Objekt in [JSON](https://www.json.org/) und schreibt den JSON-Code in den Text der Antwortnachricht.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-622">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="4f6bd-623">Der Antwortcode für diesen Rückgabetyp ist 200, vorausgesetzt, es gibt keine Ausnahmefehler.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-623">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="4f6bd-624">Nicht behandelte Ausnahmen werden in 5xx-Fehler übersetzt.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-624">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="4f6bd-625">`ActionResult`-Rückgabetypen können eine Vielzahl von HTTP-Statuscodes darstellen.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-625">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="4f6bd-626">Beispielsweise kann `GetTodoItem` zwei verschiedene Statuswerte zurückgeben:</span><span class="sxs-lookup"><span data-stu-id="4f6bd-626">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="4f6bd-627">Wenn kein Element mit der angeforderten ID übereinstimmt, gibt die Methode einen 404-Fehlercode <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> zurück.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-627">If no item matches the requested ID, the method returns a 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="4f6bd-628">Andernfalls gibt die Methode 200 mit einem JSON-Antworttext zurück.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-628">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="4f6bd-629">Die Rückgabe von `item` löst eine HTTP 200-Antwort aus.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-629">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="4f6bd-630">PutTodoItem-Methode</span><span class="sxs-lookup"><span data-stu-id="4f6bd-630">The PutTodoItem method</span></span>

<span data-ttu-id="4f6bd-631">Untersuchen Sie die `PutTodoItem`-Methode.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-631">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="4f6bd-632">`PutTodoItem` ähnelt `PostTodoItem`, verwendet allerdings HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-632">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="4f6bd-633">Die Antwort ist [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="4f6bd-633">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="4f6bd-634">Gemäß der HTTP-Spezifikation erfordert eine PUT-Anforderung, dass der Client die gesamte aktualisierte Entität (nicht nur die Änderungen) sendet.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-634">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="4f6bd-635">Verwenden Sie [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute), um Teilupdates zu unterstützen.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-635">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="4f6bd-636">Wenn beim Aufrufen von `PutTodoItem` ein Fehler zurückgegeben wird, rufen Sie `GET` auf, um sicherzustellen, dass die Datenbank ein Element enthält.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-636">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="4f6bd-637">Testen der PutTodoItem-Methode</span><span class="sxs-lookup"><span data-stu-id="4f6bd-637">Test the PutTodoItem method</span></span>

<span data-ttu-id="4f6bd-638">In diesem Beispiel wird eine In-Memory-Datenbank verwendet, die jedes Mal initialisiert werden muss, wenn die App gestartet wird.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-638">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="4f6bd-639">Es muss ein Element in der Datenbank vorhanden sein, bevor Sie einen PUT-Aufruf durchführen.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-639">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="4f6bd-640">Rufen Sie vor einem PUT-Aufruf GET auf, um sicherzustellen, dass ein Element in der Datenbank vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-640">Call GET to ensure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="4f6bd-641">Aktualisieren Sie das To-do-Element, das über den ID-Wert 1 verfügt, und legen Sie als Namen „feed fish“ fest:</span><span class="sxs-lookup"><span data-stu-id="4f6bd-641">Update the to-do item that has Id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "id":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="4f6bd-642">Die folgende Abbildung zeigt das Postman-Update:</span><span class="sxs-lookup"><span data-stu-id="4f6bd-642">The following image shows the Postman update:</span></span>

![Postman-Konsole mit der Antwort „204 (Kein Inhalt)“](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="4f6bd-644">DeleteTodoItem-Methode</span><span class="sxs-lookup"><span data-stu-id="4f6bd-644">The DeleteTodoItem method</span></span>

<span data-ttu-id="4f6bd-645">Untersuchen Sie die `DeleteTodoItem`-Methode.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-645">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="4f6bd-646">Testen der DeleteTodoItem-Methode</span><span class="sxs-lookup"><span data-stu-id="4f6bd-646">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="4f6bd-647">So löschen Sie mit Postman eine Aufgabe</span><span class="sxs-lookup"><span data-stu-id="4f6bd-647">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="4f6bd-648">Legen Sie die Methode auf `DELETE` fest.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-648">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="4f6bd-649">Legen Sie den URI des zu löschenden Objekts fest, z. B. `https://localhost:5001/api/TodoItems/1`.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-649">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="4f6bd-650">Wählen Sie **Send** (Senden) aus.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-650">Select **Send**.</span></span>

<a name="over-post"></a>
<a name="over-post-v3"></a>

## <a name="prevent-over-posting"></a><span data-ttu-id="4f6bd-651">Vermeiden von Overposting</span><span class="sxs-lookup"><span data-stu-id="4f6bd-651">Prevent over-posting</span></span>

<span data-ttu-id="4f6bd-652">Derzeit macht die Beispiel-App das gesamte `TodoItem`-Objekt verfügbar.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-652">Currently the sample app exposes the entire `TodoItem` object.</span></span> <span data-ttu-id="4f6bd-653">In den Produktions-Apps sind die Daten, die eingegeben und mithilfe einer Teilmenge des Modells zurückgegeben werden, in der Regel eingeschränkt.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-653">Production apps typically limit the data that's input and returned using a subset of the model.</span></span> <span data-ttu-id="4f6bd-654">Hierfür gibt es mehrere Gründe, wobei die Sicherheit einer der Hauptgründe ist.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-654">There are multiple reasons behind this and security is a major one.</span></span> <span data-ttu-id="4f6bd-655">Die Teilmenge eines Modells wird üblicherweise als Datenübertragungsobjekt (DTO, Data Transfer Object), Eingabemodell oder Anzeigemodell bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-655">The subset of a model is usually referred to as a Data Transfer Object (DTO), input model, or view model.</span></span> <span data-ttu-id="4f6bd-656">In diesem Artikel wird das **DTO** verwendet.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-656">**DTO** is used in this article.</span></span>

<span data-ttu-id="4f6bd-657">Ein DTO kann für Folgendes verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="4f6bd-657">A DTO may be used to:</span></span>

* <span data-ttu-id="4f6bd-658">Vermeiden Sie Overposting.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-658">Prevent over-posting.</span></span>
* <span data-ttu-id="4f6bd-659">Ausblenden von Eigenschaften, die Clients nicht anzeigen sollen</span><span class="sxs-lookup"><span data-stu-id="4f6bd-659">Hide properties that clients are not supposed to view.</span></span>
* <span data-ttu-id="4f6bd-660">Auslassen einiger Eigenschaften, um die Nutzlast zu verringern</span><span class="sxs-lookup"><span data-stu-id="4f6bd-660">Omit some properties in order to reduce payload size.</span></span>
* <span data-ttu-id="4f6bd-661">Vereinfachen von Objektgraphen, die geschachtelte Objekte enthalten</span><span class="sxs-lookup"><span data-stu-id="4f6bd-661">Flatten object graphs that contain nested objects.</span></span> <span data-ttu-id="4f6bd-662">Vereinfachte Objektgraphen können für Clients zweckmäßiger sein.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-662">Flattened object graphs can be more convenient for clients.</span></span>

<span data-ttu-id="4f6bd-663">Um den DTO-Ansatz zu veranschaulichen, aktualisieren Sie die `TodoItem`-Klasse, sodass sie ein geheimes Feld einschließt:</span><span class="sxs-lookup"><span data-stu-id="4f6bd-663">To demonstrate the DTO approach, update the `TodoItem` class to include a secret field:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItem.cs?name=snippet&highlight=6)]

<span data-ttu-id="4f6bd-664">Das geheime Feld muss in dieser App ausgeblendet werden, eine administrative App kann es jedoch verfügbar machen.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-664">The secret field needs to be hidden from this app, but an administrative app could choose to expose it.</span></span>

<span data-ttu-id="4f6bd-665">Vergewissern Sie sich, dass Sie das geheime Feld veröffentlichen und abrufen können.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-665">Verify you can post and get the secret field.</span></span>

<span data-ttu-id="4f6bd-666">Erstellen Sie ein DTO-Modell:</span><span class="sxs-lookup"><span data-stu-id="4f6bd-666">Create a DTO model:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItemDTO.cs?name=snippet)]

<span data-ttu-id="4f6bd-667">Aktualisieren Sie `TodoItemsController`, sodass `TodoItemDTO` verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="4f6bd-667">Update the `TodoItemsController` to use `TodoItemDTO`:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

<span data-ttu-id="4f6bd-668">Vergewissern Sie sich, dass Sie das geheime Feld weder veröffentlichen noch abrufen können.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-668">Verify you can't post or get the secret field.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="4f6bd-669">Aufrufen der Web-API mit JavaScript</span><span class="sxs-lookup"><span data-stu-id="4f6bd-669">Call the web API with JavaScript</span></span>

<span data-ttu-id="4f6bd-670">Mehr dazu finden Sie im [-Tutorial: Aufrufen einer ASP.NET Core-Web-API mit JavaScript](xref:tutorials/web-api-javascript).</span><span class="sxs-lookup"><span data-stu-id="4f6bd-670">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="4f6bd-671">In diesem Tutorial lernen Sie, wie die folgenden Aufgaben ausgeführt werden:</span><span class="sxs-lookup"><span data-stu-id="4f6bd-671">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="4f6bd-672">Erstellen eines Web-API-Projekts</span><span class="sxs-lookup"><span data-stu-id="4f6bd-672">Create a web API project.</span></span>
> * <span data-ttu-id="4f6bd-673">Hinzufügen einer Modellklasse und eines Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="4f6bd-673">Add a model class and a database context.</span></span>
> * <span data-ttu-id="4f6bd-674">Hinzufügen eines Controllers</span><span class="sxs-lookup"><span data-stu-id="4f6bd-674">Add a controller.</span></span>
> * <span data-ttu-id="4f6bd-675">Hinzufügen von CRUD-Methoden</span><span class="sxs-lookup"><span data-stu-id="4f6bd-675">Add CRUD methods.</span></span>
> * <span data-ttu-id="4f6bd-676">Konfigurieren von Routing und URL-Pfaden</span><span class="sxs-lookup"><span data-stu-id="4f6bd-676">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="4f6bd-677">Angeben von Rückgabewerten</span><span class="sxs-lookup"><span data-stu-id="4f6bd-677">Specify return values.</span></span>
> * <span data-ttu-id="4f6bd-678">Aufrufen der Web-API mit Postman</span><span class="sxs-lookup"><span data-stu-id="4f6bd-678">Call the web API with Postman.</span></span>
> * <span data-ttu-id="4f6bd-679">Aufrufen der Web-API mit JavaScript</span><span class="sxs-lookup"><span data-stu-id="4f6bd-679">Call the web API with JavaScript.</span></span>

<span data-ttu-id="4f6bd-680">Am Ende haben Sie eine Web-API, die in einer relationalen Datenbank gespeicherte „Aufgaben“ verwalten kann.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-680">At the end, you have a web API that can manage "to-do" items stored in a relational database.</span></span>

## <a name="overview-21"></a><span data-ttu-id="4f6bd-681">Übersicht 2.1</span><span class="sxs-lookup"><span data-stu-id="4f6bd-681">Overview 2.1</span></span>

<span data-ttu-id="4f6bd-682">In diesem Tutorial wird die folgende API erstellt:</span><span class="sxs-lookup"><span data-stu-id="4f6bd-682">This tutorial creates the following API:</span></span>

|<span data-ttu-id="4f6bd-683">API</span><span class="sxs-lookup"><span data-stu-id="4f6bd-683">API</span></span> | <span data-ttu-id="4f6bd-684">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="4f6bd-684">Description</span></span> | <span data-ttu-id="4f6bd-685">Anforderungstext</span><span class="sxs-lookup"><span data-stu-id="4f6bd-685">Request body</span></span> | <span data-ttu-id="4f6bd-686">Antworttext</span><span class="sxs-lookup"><span data-stu-id="4f6bd-686">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="4f6bd-687">GET /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="4f6bd-687">GET /api/TodoItems</span></span> | <span data-ttu-id="4f6bd-688">Alle To-do-Elemente abrufen</span><span class="sxs-lookup"><span data-stu-id="4f6bd-688">Get all to-do items</span></span> | <span data-ttu-id="4f6bd-689">Keine</span><span class="sxs-lookup"><span data-stu-id="4f6bd-689">None</span></span> | <span data-ttu-id="4f6bd-690">Array von To-do-Elementen</span><span class="sxs-lookup"><span data-stu-id="4f6bd-690">Array of to-do items</span></span>|
|<span data-ttu-id="4f6bd-691">GET /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="4f6bd-691">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="4f6bd-692">Ein Element nach ID abrufen</span><span class="sxs-lookup"><span data-stu-id="4f6bd-692">Get an item by ID</span></span> | <span data-ttu-id="4f6bd-693">Keine</span><span class="sxs-lookup"><span data-stu-id="4f6bd-693">None</span></span> | <span data-ttu-id="4f6bd-694">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="4f6bd-694">To-do item</span></span>|
|<span data-ttu-id="4f6bd-695">POST /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="4f6bd-695">POST /api/TodoItems</span></span> | <span data-ttu-id="4f6bd-696">Neues Element hinzufügen</span><span class="sxs-lookup"><span data-stu-id="4f6bd-696">Add a new item</span></span> | <span data-ttu-id="4f6bd-697">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="4f6bd-697">To-do item</span></span> | <span data-ttu-id="4f6bd-698">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="4f6bd-698">To-do item</span></span> |
|<span data-ttu-id="4f6bd-699">PUT /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="4f6bd-699">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="4f6bd-700">Vorhandenes Element aktualisieren &nbsp;</span><span class="sxs-lookup"><span data-stu-id="4f6bd-700">Update an existing item &nbsp;</span></span> | <span data-ttu-id="4f6bd-701">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="4f6bd-701">To-do item</span></span> | <span data-ttu-id="4f6bd-702">Keine</span><span class="sxs-lookup"><span data-stu-id="4f6bd-702">None</span></span> |
|<span data-ttu-id="4f6bd-703">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="4f6bd-703">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="4f6bd-704">Löschen eines Elements &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="4f6bd-704">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="4f6bd-705">Keine</span><span class="sxs-lookup"><span data-stu-id="4f6bd-705">None</span></span> | <span data-ttu-id="4f6bd-706">Keine</span><span class="sxs-lookup"><span data-stu-id="4f6bd-706">None</span></span>|

<span data-ttu-id="4f6bd-707">Das folgende Diagramm zeigt den Entwurf der App.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-707">The following diagram shows the design of the app.</span></span>

![Der Client ist das Feld ganz links.](first-web-api/_static/architecture.png)

## <a name="prerequisites-21"></a><span data-ttu-id="4f6bd-713">Voraussetzungen 2.1</span><span class="sxs-lookup"><span data-stu-id="4f6bd-713">Prerequisites 2.1</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4f6bd-714">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4f6bd-714">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="4f6bd-715">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4f6bd-715">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="4f6bd-716">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="4f6bd-716">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project-21"></a><span data-ttu-id="4f6bd-717">Erstellen eines Webprojekts 2.1</span><span class="sxs-lookup"><span data-stu-id="4f6bd-717">Create a web project 2.1</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4f6bd-718">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4f6bd-718">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4f6bd-719">Klicken Sie im Menü **Datei** auf **Neu** > **Projekt**.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-719">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="4f6bd-720">Wählen Sie die Vorlage **ASP.NET Core-Webanwendung** aus, und klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-720">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="4f6bd-721">Geben Sie dem Projekt den Namen *TodoApi*, und klicken Sie auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-721">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="4f6bd-722">Vergewissern Sie sich, dass im Dialogfeld **Neue ASP.NET Core-Webanwendung erstellen** die Optionen **.NET Core** und **ASP.NET Core 2.2** ausgewählt sind.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-722">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 2.2** are selected.</span></span> <span data-ttu-id="4f6bd-723">Wählen Sie die Vorlage **API** aus, und klicken Sie auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-723">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="4f6bd-724">Wählen Sie **nicht** **Enable Docker Support** (Docker-Unterstützung aktivieren) aus.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-724">**Don't** select **Enable Docker Support**.</span></span>

![VS-Dialogfeld „Neues Projekt“](first-web-api/_static/vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="4f6bd-726">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4f6bd-726">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="4f6bd-727">Öffnen Sie das [integrierte Terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="4f6bd-727">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="4f6bd-728">Wechseln Sie mit dem Befehl `cd`' zu dem Ordner, der den Projektordner enthalten soll.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-728">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="4f6bd-729">Führen Sie die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="4f6bd-729">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  <span data-ttu-id="4f6bd-730">Diese Befehle erstellen ein neues Web-API-Projekt und öffnen eine neue Visual Studio Code-Instanz im neuen Projektordner.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-730">These commands create a new web API project and open a new instance of Visual Studio Code in the new project folder.</span></span>

* <span data-ttu-id="4f6bd-731">Wenn Sie in einem Dialogfeld angeben müssen, ob Sie dem Projekt die erforderlichen Elemente hinzufügen möchten, wählen Sie **Ja** aus.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-731">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="4f6bd-732">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="4f6bd-732">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="4f6bd-733">Klicken Sie auf **Datei** > **Neue Projektmappe**.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-733">Select **File** > **New Solution**.</span></span>

  ![Neue Projektmappe in macOS](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="4f6bd-735">Navigieren Sie in Visual Studio für Mac-Versionen vor Version 8.6 zu **.NET Core** > **App** > **API** > **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-735">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="4f6bd-736">Bei Version 8.6 oder höher klicken Sie auf **Web and Console** > **App** > **API** > **Weiter** (Web und Konsole).</span><span class="sxs-lookup"><span data-stu-id="4f6bd-736">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>
  
* <span data-ttu-id="4f6bd-737">Wählen Sie im Dialogfeld **Neue ASP.NET Core-Web-API konfigurieren** die neueste .NET Core 2.x-Version als **Zielframework** aus.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-737">In the **Configure the new ASP.NET Core Web API** dialog, select the latest .NET Core 2.x **Target Framework**.</span></span> <span data-ttu-id="4f6bd-738">Klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-738">Select **Next**.</span></span>

* <span data-ttu-id="4f6bd-739">Geben Sie für **Projektname** *TodoApi* ein, und wählen Sie dann **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-739">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![Dialogfeld „Konfiguration“](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api-21"></a><span data-ttu-id="4f6bd-741">Testen der API 2.1</span><span class="sxs-lookup"><span data-stu-id="4f6bd-741">Test the API 2.1</span></span>

<span data-ttu-id="4f6bd-742">Die Projektvorlage erstellt eine `values`-API.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-742">The project template creates a `values` API.</span></span> <span data-ttu-id="4f6bd-743">Rufen Sie in einem Browser die `Get`-Methode zum Testen der App auf.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-743">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4f6bd-744">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4f6bd-744">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="4f6bd-745">Drücken Sie STRG+F5, um die App auszuführen.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-745">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="4f6bd-746">Visual Studio startet einen Browser und navigiert zu `https://localhost:<port>/api/values`, wobei es sich bei `<port>` um eine zufällig ausgewählte Portnummer handelt.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-746">Visual Studio launches a browser and navigates to `https://localhost:<port>/api/values`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="4f6bd-747">Wenn Sie in einem Dialogfeld angeben müssen, ob Sie dem IIS Express-Zertifikat vertrauen, wählen Sie **Ja** aus.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-747">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="4f6bd-748">Wählen Sie im folgenden Dialogfeld **Sicherheitswarnung** die Option **Ja** aus.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-748">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="4f6bd-749">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4f6bd-749">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="4f6bd-750">Drücken Sie STRG+F5, um die App auszuführen.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-750">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="4f6bd-751">Navigieren Sie in einem Browser zur folgenden URL: `https://localhost:5001/api/values`.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-751">In a browser, go to following URL: `https://localhost:5001/api/values`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="4f6bd-752">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="4f6bd-752">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="4f6bd-753">Wählen Sie **Ausführen** > **Debugging starten** aus, um die App zu starten.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-753">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="4f6bd-754">Visual Studio für Mac startet einen Browser und navigiert zu `https://localhost:<port>`, wobei es sich bei `<port>` um eine zufällig ausgewählte Portnummer handelt.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-754">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="4f6bd-755">Der Fehler „HTTP 404: Nicht gefunden“ wird zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-755">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="4f6bd-756">Fügen Sie der URL `/api/values` an, d.h. ändern Sie die URL in `https://localhost:<port>/api/values`.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-756">Append `/api/values` to the URL (change the URL to `https://localhost:<port>/api/values`).</span></span>

---

<span data-ttu-id="4f6bd-757">Die folgende JSON-Datei wird zurückgegeben:</span><span class="sxs-lookup"><span data-stu-id="4f6bd-757">The following JSON is returned:</span></span>

```json
["value1","value2"]
```

## <a name="add-a-model-class-21"></a><span data-ttu-id="4f6bd-758">Hinzufügen einer Modellklasse 2.1</span><span class="sxs-lookup"><span data-stu-id="4f6bd-758">Add a model class 2.1</span></span>

<span data-ttu-id="4f6bd-759">Ein *Modell* ist eine Gruppe von Klassen, die die Daten darstellen, die die App verwaltet.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-759">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="4f6bd-760">Das Modell für diese App ist eine einzelne `TodoItem`-Klasse.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-760">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4f6bd-761">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4f6bd-761">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4f6bd-762">Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-762">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="4f6bd-763">Klicken Sie auf **Hinzufügen** > **Neuer Ordner**.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-763">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="4f6bd-764">Geben Sie dem Ordner den Namen *Models* .</span><span class="sxs-lookup"><span data-stu-id="4f6bd-764">Name the folder *Models*.</span></span>

* <span data-ttu-id="4f6bd-765">Klicken Sie mit der rechten Maustaste auf den Ordner *Models* , und wählen Sie **Hinzufügen** > **Klasse** aus.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-765">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="4f6bd-766">Nennen Sie die Klasse *TodoItem*, und wählen Sie **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-766">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="4f6bd-767">Ersetzen Sie den Vorlagencode durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="4f6bd-767">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="4f6bd-768">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4f6bd-768">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="4f6bd-769">Fügen Sie einen Ordner mit dem Namen *Models* hinzu.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-769">Add a folder named *Models*.</span></span>

* <span data-ttu-id="4f6bd-770">Fügen Sie dem Ordner *Models* mit dem folgenden Code eine `TodoItem`-Klasse hinzu:</span><span class="sxs-lookup"><span data-stu-id="4f6bd-770">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="4f6bd-771">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="4f6bd-771">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="4f6bd-772">Klicken Sie mit der rechten Maustaste auf das Projekt.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-772">Right-click the project.</span></span> <span data-ttu-id="4f6bd-773">Klicken Sie auf **Hinzufügen** > **Neuer Ordner**.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-773">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="4f6bd-774">Geben Sie dem Ordner den Namen *Models* .</span><span class="sxs-lookup"><span data-stu-id="4f6bd-774">Name the folder *Models*.</span></span>

  ![Neuer Ordner](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="4f6bd-776">Klicken Sie mit der rechten Maustaste auf den Ordner *Models* , und wählen Sie **Hinzufügen** > **Neue Datei** > **Allgemein** > **Leere Klasse** aus.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-776">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="4f6bd-777">Nennen Sie die Klasse *TodoItem*, und klicken Sie dann auf **Neu**.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-777">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="4f6bd-778">Ersetzen Sie den Vorlagencode durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="4f6bd-778">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="4f6bd-779">Die `Id`-Eigenschaft fungiert als eindeutiger Schlüssel in einer relationalen Datenbank.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-779">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="4f6bd-780">Modellklassen können überall im Projekt platziert werden, doch gemäß der Konvention wird der Ordner *Models* verwendet.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-780">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context-21"></a><span data-ttu-id="4f6bd-781">Hinzufügen eines Datenbankkontexts 2.1</span><span class="sxs-lookup"><span data-stu-id="4f6bd-781">Add a database context 2.1</span></span>

<span data-ttu-id="4f6bd-782">Der *Datenbankkontext* ist die Hauptklasse, die die Entity Framework-Funktionen für ein Datenmodell koordiniert.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-782">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="4f6bd-783">Diese Klasse wird durch Ableiten von der `Microsoft.EntityFrameworkCore.DbContext`-Klasse erstellt.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-783">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4f6bd-784">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4f6bd-784">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4f6bd-785">Klicken Sie mit der rechten Maustaste auf den Ordner *Models* , und wählen Sie **Hinzufügen** > **Klasse** aus.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-785">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="4f6bd-786">Nennen Sie die Klasse *TodoContext*, und klicken Sie auf **Hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-786">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="4f6bd-787">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="4f6bd-787">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="4f6bd-788">Fügen Sie eine `TodoContext`-Klasse zum Ordner *Models* hinzu.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-788">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="4f6bd-789">Ersetzen Sie den Vorlagencode durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="4f6bd-789">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context-21"></a><span data-ttu-id="4f6bd-790">Registrieren des Datenbankkontexts 2.1</span><span class="sxs-lookup"><span data-stu-id="4f6bd-790">Register the database context 2.1</span></span>

<span data-ttu-id="4f6bd-791">In ASP.NET Core müssen Dienste wie der Datenbankkontext mit dem Container [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) registriert werden.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-791">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="4f6bd-792">Der Container stellt den Dienst für Controller bereit.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-792">The container provides the service to controllers.</span></span>

<span data-ttu-id="4f6bd-793">Aktualisieren Sie *Startup.cs* mit dem folgenden hervorgehobenen Code:</span><span class="sxs-lookup"><span data-stu-id="4f6bd-793">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

<span data-ttu-id="4f6bd-794">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="4f6bd-794">The preceding code:</span></span>

* <span data-ttu-id="4f6bd-795">Entfernt nicht benötigte `using`-Deklarationen</span><span class="sxs-lookup"><span data-stu-id="4f6bd-795">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="4f6bd-796">Fügt dem Abhängigkeitscontainer den Datenbankkontext hinzu</span><span class="sxs-lookup"><span data-stu-id="4f6bd-796">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="4f6bd-797">Gibt an, dass der Datenbankkontext eine In-Memory Database verwendet</span><span class="sxs-lookup"><span data-stu-id="4f6bd-797">Specifies that the database context will use an in-memory database.</span></span>

## <a name="add-a-controller-21"></a><span data-ttu-id="4f6bd-798">Hinzufügen eines Controllers 2.1</span><span class="sxs-lookup"><span data-stu-id="4f6bd-798">Add a controller 2.1</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4f6bd-799">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4f6bd-799">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4f6bd-800">Klicken Sie mit der rechten Maustaste auf den Ordner *Controller*.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-800">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="4f6bd-801">Wählen Sie **Hinzufügen** > **Neues Element** aus.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-801">Select **Add** > **New Item**.</span></span>
* <span data-ttu-id="4f6bd-802">Wählen Sie im Dialogfeld **Neues Element hinzufügen** die Vorlage **API-Controllerklasse** aus.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-802">In the **Add New Item** dialog, select the **API Controller Class** template.</span></span>
* <span data-ttu-id="4f6bd-803">Benennen Sie die Klasse *TodoController*, und wählen Sie **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-803">Name the class *TodoController*, and select **Add**.</span></span>

  ![Dialogfeld „Neues Element hinzufügen“ mit Controller im Suchfeld und ausgewähltem Web-API-Controller](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="4f6bd-805">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="4f6bd-805">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="4f6bd-806">Erstellen Sie im Ordner *Controllers* eine Klasse namens `TodoController`.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-806">In the *Controllers* folder, create a class named `TodoController`.</span></span>

---

* <span data-ttu-id="4f6bd-807">Ersetzen Sie den Vorlagencode durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="4f6bd-807">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

<span data-ttu-id="4f6bd-808">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="4f6bd-808">The preceding code:</span></span>

* <span data-ttu-id="4f6bd-809">Definiert eine API-Controllerklasse ohne Methoden.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-809">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="4f6bd-810">Markiert die Klasse mit dem [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute)-Attribut.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-810">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="4f6bd-811">Dieses Attribut gibt an, dass der Controller auf Web-API-Anforderungen reagiert.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-811">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="4f6bd-812">Weitere Informationen zu bestimmten Verhaltensweisen, die das Attribut ermöglicht, finden Sie unter <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-812">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="4f6bd-813">Verwendet die Abhängigkeitsinjektion, um den Datenbankkontext (`TodoContext`) dem Controller hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-813">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="4f6bd-814">Der Datenbankkontext wird in den einzelnen [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete)-Methoden im Controller verwendet.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-814">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>
* <span data-ttu-id="4f6bd-815">Fügt der Datenbank, falls sie leer ist, ein Element mit dem Namen `Item1` hinzu.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-815">Adds an item named `Item1` to the database if the database is empty.</span></span> <span data-ttu-id="4f6bd-816">Dieser Code befindet sich im Konstruktor. Er wird bei jeder neuen HTTP-Anforderung ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-816">This code is in the constructor, so it runs every time there's a new HTTP request.</span></span> <span data-ttu-id="4f6bd-817">Wenn Sie alle Elemente löschen, erstellt der Konstruktor beim nächsten Aufruf einer API-Methode erneut `Item1`.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-817">If you delete all items, the constructor creates `Item1` again the next time an API method is called.</span></span> <span data-ttu-id="4f6bd-818">So sieht es möglicherweise so aus, als sei die Löschung fehlgeschlagen, obwohl sie erfolgreich war.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-818">So it may look like the deletion didn't work when it actually did work.</span></span>

## <a name="add-get-methods-21"></a><span data-ttu-id="4f6bd-819">Hinzufügen von GET-Methoden 2.1</span><span class="sxs-lookup"><span data-stu-id="4f6bd-819">Add Get methods 2.1</span></span>

<span data-ttu-id="4f6bd-820">Fügen Sie der Klasse `TodoController` die folgenden Methoden hinzu, um eine API bereitzustellen, die Aufgaben abruft:</span><span class="sxs-lookup"><span data-stu-id="4f6bd-820">To provide an API that retrieves to-do items, add the following methods to the `TodoController` class:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

<span data-ttu-id="4f6bd-821">Diese Methoden implementieren zwei GET-Endpunkte:</span><span class="sxs-lookup"><span data-stu-id="4f6bd-821">These methods implement two GET endpoints:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="4f6bd-822">Halten Sie die App an, falls diese noch ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-822">Stop the app if it's still running.</span></span> <span data-ttu-id="4f6bd-823">Führen Sie diese dann noch mal aus, damit die letzten Änderungen übernommen werden.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-823">Then run it again to include the latest changes.</span></span>

<span data-ttu-id="4f6bd-824">Testen Sie die App, indem Sie die beiden Endpunkte in einem Browser aufrufen.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-824">Test the app by calling the two endpoints from a browser.</span></span> <span data-ttu-id="4f6bd-825">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="4f6bd-825">For example:</span></span>

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

<span data-ttu-id="4f6bd-826">Die folgende HTTP-Antwort wird durch den Aufruf von `GetTodoItems` erzeugt:</span><span class="sxs-lookup"><span data-stu-id="4f6bd-826">The following HTTP response is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths-21"></a><span data-ttu-id="4f6bd-827">Routing und URL-Pfade 2.1</span><span class="sxs-lookup"><span data-stu-id="4f6bd-827">Routing and URL paths 2.1</span></span>

<span data-ttu-id="4f6bd-828">Das Attribut [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) gibt eine Methode an, die auf eine HTTP GET-Anforderung antwortet.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-828">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="4f6bd-829">Der URL-Pfad für jede Methode wird wie folgt erstellt:</span><span class="sxs-lookup"><span data-stu-id="4f6bd-829">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="4f6bd-830">Beginnen Sie mit der Vorlagenzeichenfolge im `Route`-Attribut des Controllers:</span><span class="sxs-lookup"><span data-stu-id="4f6bd-830">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* <span data-ttu-id="4f6bd-831">Ersetzen Sie `[controller]` durch den Namen des Controllers, bei dem es sich standardmäßig um den Namen der Controller-Klasse ohne das Suffix „Controller“ handelt.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-831">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="4f6bd-832">Bei diesem Beispiel ist der Klassenname des Controllers „**Todo** Controller“, d.h. der Controllername lautet „todo“.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-832">For this sample, the controller class name is **Todo** Controller, so the controller name is "todo".</span></span> <span data-ttu-id="4f6bd-833">Beim ASP.NET Core-[Routing](xref:mvc/controllers/routing) wird die Groß-/Kleinschreibung nicht beachtet.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-833">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="4f6bd-834">Wenn das `[HttpGet]`-Attribut eine Routenvorlage (z. B. `[HttpGet("products")]`) hat, fügen Sie diese an den Pfad an.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-834">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="4f6bd-835">In diesem Beispiel wird keine Vorlage verwendet.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-835">This sample doesn't use a template.</span></span> <span data-ttu-id="4f6bd-836">Weitere Informationen finden Sie unter [Attributrouting mit Http[Verb]-Attributen](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="4f6bd-836">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="4f6bd-837">In der folgenden `GetTodoItem`-Methode ist `"{id}"` eine Platzhaltervariable für den eindeutigen Bezeichner des To-do-Elements.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-837">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="4f6bd-838">Wenn `GetTodoItem` aufgerufen wird, wird der Wert von `"{id}"` in der URL der Methode in ihrem Parameter `id` bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-838">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values-21"></a><span data-ttu-id="4f6bd-839">Rückgabewerte 2.1</span><span class="sxs-lookup"><span data-stu-id="4f6bd-839">Return values 2.1</span></span>

<span data-ttu-id="4f6bd-840">Der Rückgabetyp der Methoden `GetTodoItems` und `GetTodoItem` ist der [ActionResult\<T>-Typ](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="4f6bd-840">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="4f6bd-841">ASP.NET Core serialisiert automatisch das Objekt in [JSON](https://www.json.org/) und schreibt den JSON-Code in den Text der Antwortnachricht.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-841">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="4f6bd-842">Der Antwortcode für diesen Rückgabetyp ist 200, vorausgesetzt, es gibt keine Ausnahmefehler.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-842">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="4f6bd-843">Nicht behandelte Ausnahmen werden in 5xx-Fehler übersetzt.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-843">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="4f6bd-844">`ActionResult`-Rückgabetypen können eine Vielzahl von HTTP-Statuscodes darstellen.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-844">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="4f6bd-845">Beispielsweise kann `GetTodoItem` zwei verschiedene Statuswerte zurückgeben:</span><span class="sxs-lookup"><span data-stu-id="4f6bd-845">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="4f6bd-846">Wenn kein Element mit der angeforderten ID übereinstimmt, gibt die Methode einen 404-Fehlercode <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> zurück.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-846">If no item matches the requested ID, the method returns a 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="4f6bd-847">Andernfalls gibt die Methode 200 mit einem JSON-Antworttext zurück.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-847">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="4f6bd-848">Die Rückgabe von `item` löst eine HTTP 200-Antwort aus.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-848">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="test-the-gettodoitems-method-21"></a><span data-ttu-id="4f6bd-849">Testen der GetTodoItems-Methode 2.1</span><span class="sxs-lookup"><span data-stu-id="4f6bd-849">Test the GetTodoItems method 2.1</span></span>

<span data-ttu-id="4f6bd-850">Dieses Tutorial verwendet Postman zum Testen der Web-API.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-850">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="4f6bd-851">Installieren Sie [Postman](https://www.getpostman.com/downloads/).</span><span class="sxs-lookup"><span data-stu-id="4f6bd-851">Install [Postman](https://www.getpostman.com/downloads/).</span></span>
* <span data-ttu-id="4f6bd-852">Starten Sie die Web-App.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-852">Start the web app.</span></span>
* <span data-ttu-id="4f6bd-853">Starten Sie Postman.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-853">Start Postman.</span></span>
* <span data-ttu-id="4f6bd-854">Deaktivieren Sie **SSL certificate verification** (Verifizierung des SSL-Zertifikats).</span><span class="sxs-lookup"><span data-stu-id="4f6bd-854">Disable **SSL certificate verification**.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4f6bd-855">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4f6bd-855">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4f6bd-856">Deaktivieren Sie auf der Registerkarte **General** (Allgemein) unter File > Settings (**Datei** > **Einstellungen**) **SSL certificate verification** (Verifizierung des SSL-Zertifikats).</span><span class="sxs-lookup"><span data-stu-id="4f6bd-856">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="4f6bd-857">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="4f6bd-857">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="4f6bd-858">Deaktivieren Sie auf der Registerkarte **General** (Allgemein) unter **Postman** > **Preferences** (Postman > Einstellungen) **SSL certificate verification** (Verifizierung des SSL-Zertifikats).</span><span class="sxs-lookup"><span data-stu-id="4f6bd-858">From **Postman** > **Preferences** (**General** tab), disable **SSL certificate verification**.</span></span> <span data-ttu-id="4f6bd-859">Wählen Sie alternativ den Schraubenschlüssel und **Settings** (Einstellungen) aus, und deaktivieren Sie dann die Überprüfung des SSL-Zertifikats.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-859">Alternatively, select the wrench and select **Settings**, then disable the SSL certificate verification.</span></span>

---
  
> [!WARNING]
> <span data-ttu-id="4f6bd-860">Aktivieren Sie die Verifizierung des SSL-Zertifikats wieder, nachdem Sie den Controller getestet haben.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-860">Re-enable SSL certificate verification after testing the controller.</span></span>

* <span data-ttu-id="4f6bd-861">Erstellen Sie eine neue Anforderung.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-861">Create a new request.</span></span>
  * <span data-ttu-id="4f6bd-862">Legen Sie die HTTP-Methode auf **GET** fest.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-862">Set the HTTP method to **GET**.</span></span>
  * <span data-ttu-id="4f6bd-863">Legen Sie den Anforderungs-URI auf `https://localhost:<port>/api/todo` fest.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-863">Set the request URI to `https://localhost:<port>/api/todo`.</span></span> <span data-ttu-id="4f6bd-864">Beispielsweise `https://localhost:5001/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-864">For example, `https://localhost:5001/api/todo`.</span></span>
* <span data-ttu-id="4f6bd-865">Wählen Sie in Postman **Two pane view** (Ansicht mit zwei Bereichen) aus.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-865">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="4f6bd-866">Wählen Sie **Send** (Senden) aus.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-866">Select **Send**.</span></span>

![Postman mit GET-Anforderung](first-web-api/_static/2pv.png)

## <a name="add-a-create-method-21"></a><span data-ttu-id="4f6bd-868">Hinzufügen einer Methode 2.1</span><span class="sxs-lookup"><span data-stu-id="4f6bd-868">Add a Create method 2.1</span></span>

<span data-ttu-id="4f6bd-869">Fügen Sie die folgende `PostTodoItem`-Methode in *Controllers/TodoController.cs* hinzu:</span><span class="sxs-lookup"><span data-stu-id="4f6bd-869">Add the following `PostTodoItem` method inside of *Controllers/TodoController.cs*:</span></span> 

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="4f6bd-870">Der oben stehende Code ist eine HTTP POST-Methode, wie durch das [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute)-Attribut angegeben.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-870">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="4f6bd-871">Die Methode ruft den Wert der Aufgabe aus dem Text der HTTP-Anforderung ab.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-871">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="4f6bd-872">Die `CreatedAtAction`-Methode:</span><span class="sxs-lookup"><span data-stu-id="4f6bd-872">The `CreatedAtAction` method:</span></span>

* <span data-ttu-id="4f6bd-873">Gibt bei einer erfolgreichen Anforderung den Statuscode „HTTP 201“ zurück.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-873">Returns an HTTP 201 status code, if successful.</span></span> <span data-ttu-id="4f6bd-874">HTTP 201 ist die Standardantwort für eine HTTP POST-Methode, die eine neue Ressource auf dem Server erstellt.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-874">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="4f6bd-875">Fügt der Antwort einen `Location`-Header hinzu.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-875">Adds a `Location` header to the response.</span></span> <span data-ttu-id="4f6bd-876">Der `Location`-Header gibt den URI des neu erstellten To-Do-Elements zurück.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-876">The `Location` header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="4f6bd-877">Weitere Informationen finden Sie unter [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="4f6bd-877">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="4f6bd-878">Verweist auf die `GetTodoItem`-Aktion zum Erstellen des URIs des `Location`-Headers.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-878">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="4f6bd-879">Das `nameof`-Schlüsselwort von C# wird verwendet, um eine Hartcodierung des Aktionsnamens im `CreatedAtAction`-Aufruf zu vermeiden.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-879">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method-21"></a><span data-ttu-id="4f6bd-880">Testen der PostTodoItem-Methode 2.1</span><span class="sxs-lookup"><span data-stu-id="4f6bd-880">Test the PostTodoItem method 2.1</span></span>

* <span data-ttu-id="4f6bd-881">Erstellen Sie das Projekt.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-881">Build the project.</span></span>
* <span data-ttu-id="4f6bd-882">Legen Sie in Postman die HTTP-Methode auf `POST` fest.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-882">In Postman, set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="4f6bd-883">Legen Sie den URI auf `https://localhost:<port>/api/Todo` fest.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-883">Set the URI to `https://localhost:<port>/api/Todo`.</span></span> <span data-ttu-id="4f6bd-884">Beispiel: `https://localhost:5001/api/Todo`.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-884">For example, `https://localhost:5001/api/Todo`.</span></span>
* <span data-ttu-id="4f6bd-885">Wählen Sie die Registerkarte **Body** (Text) aus.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-885">Select the **Body** tab.</span></span>
* <span data-ttu-id="4f6bd-886">Wählen Sie das Optionsfeld **raw** (Unformatiert) aus.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-886">Select the **raw** radio button.</span></span>
* <span data-ttu-id="4f6bd-887">Legen Sie den Typ auf **JSON (application/json)** fest.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-887">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="4f6bd-888">Geben Sie für die Aufgabe den Anforderungstext im JSON-Format ein:</span><span class="sxs-lookup"><span data-stu-id="4f6bd-888">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="4f6bd-889">Wählen Sie **Send** (Senden) aus.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-889">Select **Send**.</span></span>

  ![Postman mit erstellter Anforderung](first-web-api/_static/create.png)

  <span data-ttu-id="4f6bd-891">Wenn Sie die Fehlermeldung „405: Methode nicht zulässig“ erhalten, wurde das Projekt wahrscheinlich nicht kompiliert, nachdem die Methode `PostTodoItem` hinzugefügt wurde.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-891">If you get a 405 Method Not Allowed error, it's probably the result of not compiling the project after adding the `PostTodoItem` method.</span></span>

### <a name="test-the-location-header-uri-21"></a><span data-ttu-id="4f6bd-892">Testen des Adressheader-URIs 2.1</span><span class="sxs-lookup"><span data-stu-id="4f6bd-892">Test the location header URI 2.1</span></span>

* <span data-ttu-id="4f6bd-893">Wählen Sie auf der Registerkarte **Header** (Header) den Bereich **Response** (Antwort) aus.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-893">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="4f6bd-894">Kopieren Sie den den Headerwert von **Location** (Speicherort):</span><span class="sxs-lookup"><span data-stu-id="4f6bd-894">Copy the **Location** header value:</span></span>

  ![Registerkarte „Header“ in der Postman-Konsole](first-web-api/_static/pmc2.png)

* <span data-ttu-id="4f6bd-896">Legen Sie die Methode auf „GET“ fest.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-896">Set the method to GET.</span></span>
* <span data-ttu-id="4f6bd-897">Legen Sie den URI auf `https://localhost:<port>/api/TodoItems/2` fest.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-897">Set the URI to `https://localhost:<port>/api/TodoItems/2`.</span></span> <span data-ttu-id="4f6bd-898">Beispiel: `https://localhost:5001/api/TodoItems/2`.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-898">For example, `https://localhost:5001/api/TodoItems/2`.</span></span>
* <span data-ttu-id="4f6bd-899">Wählen Sie **Send** (Senden) aus.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-899">Select **Send**.</span></span>

## <a name="add-a-puttodoitem-method-21"></a><span data-ttu-id="4f6bd-900">Hinzufügen einer PutTodoItem-Methode 2.1</span><span class="sxs-lookup"><span data-stu-id="4f6bd-900">Add a PutTodoItem method 2.1</span></span>

<span data-ttu-id="4f6bd-901">Fügen Sie die folgende `PutTodoItem`-Methode hinzu:</span><span class="sxs-lookup"><span data-stu-id="4f6bd-901">Add the following `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="4f6bd-902">`PutTodoItem` ähnelt `PostTodoItem`, verwendet allerdings HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-902">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="4f6bd-903">Die Antwort ist [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="4f6bd-903">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="4f6bd-904">Gemäß der HTTP-Spezifikation erfordert eine PUT-Anforderung, dass der Client die gesamte aktualisierte Entität (nicht nur die Änderungen) sendet.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-904">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="4f6bd-905">Verwenden Sie [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute), um Teilupdates zu unterstützen.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-905">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="4f6bd-906">Wenn beim Aufrufen von `PutTodoItem` ein Fehler zurückgegeben wird, rufen Sie `GET` auf, um sicherzustellen, dass die Datenbank ein Element enthält.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-906">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method-21"></a><span data-ttu-id="4f6bd-907">Testen der PutTodoItem-Methode 2.1</span><span class="sxs-lookup"><span data-stu-id="4f6bd-907">Test the PutTodoItem method 2.1</span></span>

<span data-ttu-id="4f6bd-908">In diesem Beispiel wird eine In-Memory-Datenbank verwendet, die jedes Mal initialisiert werden muss, wenn die App gestartet wird.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-908">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="4f6bd-909">Es muss ein Element in der Datenbank vorhanden sein, bevor Sie einen PUT-Aufruf durchführen.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-909">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="4f6bd-910">Rufen Sie vor einem PUT-Aufruf GET auf, um sicherzustellen, dass ein Element in der Datenbank vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-910">Call GET to ensure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="4f6bd-911">Aktualisieren Sie das To-do-Element, das über den ID-Wert 1 verfügt, und legen Sie als Namen „feed fish“ fest:</span><span class="sxs-lookup"><span data-stu-id="4f6bd-911">Update the to-do item that has Id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "id":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="4f6bd-912">Die folgende Abbildung zeigt das Postman-Update:</span><span class="sxs-lookup"><span data-stu-id="4f6bd-912">The following image shows the Postman update:</span></span>

![Postman-Konsole mit der Antwort „204 (Kein Inhalt)“](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method-21"></a><span data-ttu-id="4f6bd-914">Hinzufügen einer DeleteTodoItem-Methode 2.1</span><span class="sxs-lookup"><span data-stu-id="4f6bd-914">Add a DeleteTodoItem method 2.1</span></span>

<span data-ttu-id="4f6bd-915">Fügen Sie die folgende `DeleteTodoItem`-Methode hinzu:</span><span class="sxs-lookup"><span data-stu-id="4f6bd-915">Add the following `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="4f6bd-916">Die `DeleteTodoItem`-Antwort lautet [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="4f6bd-916">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method-21"></a><span data-ttu-id="4f6bd-917">Testen der DeleteTodoItem-Methode 2.1</span><span class="sxs-lookup"><span data-stu-id="4f6bd-917">Test the DeleteTodoItem method 2.1</span></span>

<span data-ttu-id="4f6bd-918">So löschen Sie mit Postman eine Aufgabe</span><span class="sxs-lookup"><span data-stu-id="4f6bd-918">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="4f6bd-919">Legen Sie die Methode auf `DELETE` fest.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-919">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="4f6bd-920">Legen Sie den URI des zu löschenden Objekts fest, z. B. `https://localhost:5001/api/todo/1`.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-920">Set the URI of the object to delete (for example, `https://localhost:5001/api/todo/1`).</span></span>
* <span data-ttu-id="4f6bd-921">Wählen Sie **Send** (Senden) aus.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-921">Select **Send**.</span></span>

<span data-ttu-id="4f6bd-922">Sie können in der Beispiel-App alle Elemente löschen.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-922">The sample app allows you to delete all the items.</span></span> <span data-ttu-id="4f6bd-923">Sobald das letzte Element gelöscht wurde, wird allerdings beim nächsten API-Aufruf vom Modellklassenkonstruktor ein neues erstellt.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-923">However, when the last item is deleted, a new one is created by the model class constructor the next time the API is called.</span></span>

## <a name="call-the-web-api-with-javascript-21"></a><span data-ttu-id="4f6bd-924">Aufrufen der Web-API mit JavaScript 2.1</span><span class="sxs-lookup"><span data-stu-id="4f6bd-924">Call the web API with JavaScript 2.1</span></span>

<span data-ttu-id="4f6bd-925">In diesem Abschnitt wird eine HTML-Seite hinzugefügt, die mithilfe von JavaScript die Web-API aufruft.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-925">In this section, an HTML page is added that uses JavaScript to call the web API.</span></span> <span data-ttu-id="4f6bd-926">jQuery initiiert die Anforderung.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-926">jQuery initiates the request.</span></span> <span data-ttu-id="4f6bd-927">JavaScript aktualisiert die Seite mit den Details aus der Antwort der Web-API.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-927">JavaScript updates the page with the details from the web API's response.</span></span>

<span data-ttu-id="4f6bd-928">Konfigurieren Sie die App so, dass sie [statische Dateien bereitstellt](xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A), und [aktivieren Sie die Standarddateizuordnung](xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A), indem Sie *startup.cs* mit dem unten markierten Code aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-928">Configure the app to [serve static files](xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A) and [enable default file mapping](xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A) by updating *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

<span data-ttu-id="4f6bd-929">Erstellen Sie im Projektverzeichnis den Ordner *wwwwroot*.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-929">Create a *wwwroot* folder in the project directory.</span></span>

<span data-ttu-id="4f6bd-930">Fügen Sie dem Verzeichnis *wwwroot* eine HTML-Datei namens *index.html* hinzu.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-930">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="4f6bd-931">Ersetzen Sie den Inhalt durch folgendes Markup:</span><span class="sxs-lookup"><span data-stu-id="4f6bd-931">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

<span data-ttu-id="4f6bd-932">Fügen Sie dem Verzeichnis *wwwroot* eine JavaScript-Datei namens *site.js* hinzu.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-932">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="4f6bd-933">Ersetzen Sie den Inhalt durch folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="4f6bd-933">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="4f6bd-934">Möglicherweise ist eine Änderung an den Starteinstellungen des ASP.NET Core-Projekts erforderlich, um die HTML-Seite lokal zu testen:</span><span class="sxs-lookup"><span data-stu-id="4f6bd-934">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="4f6bd-935">Öffnen Sie *Properties\launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-935">Open *Properties\launchSettings.json*.</span></span>
* <span data-ttu-id="4f6bd-936">Entfernen Sie die `launchUrl`-Eigenschaft, um zu erzwingen, dass die App mit *index.html* als Startseite geöffnet wird. Dies ist die Standarddatei des Projekts.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-936">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="4f6bd-937">Dieses Beispiel ruft alle CRUD-Methoden der Web-API auf.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-937">This sample calls all of the CRUD methods of the web API.</span></span> <span data-ttu-id="4f6bd-938">Im Folgenden werden die API-Aufrufe erläutert.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-938">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items-21"></a><span data-ttu-id="4f6bd-939">Abrufen einer Liste von To-Do-Elementen 2.1</span><span class="sxs-lookup"><span data-stu-id="4f6bd-939">Get a list of to-do items 2.1</span></span>

<span data-ttu-id="4f6bd-940">jQuery sendet eine HTTP GET-Anforderung an die Web-API, die JSON-Code mit einem Array aus To-Do-Elementen zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-940">jQuery sends an HTTP GET request to the web API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="4f6bd-941">Die Rückruffunktion `success` wird aufgerufen, wenn die Anforderung erfolgreich ist.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-941">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="4f6bd-942">Im Rückruf wird DOM mit den To-Do-Informationen aktualisiert.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-942">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item-21"></a><span data-ttu-id="4f6bd-943">Hinzufügen eines To-Do-Elements 2.1</span><span class="sxs-lookup"><span data-stu-id="4f6bd-943">Add a to-do item 2.1</span></span>

<span data-ttu-id="4f6bd-944">jQuery sendet eine HTTP POST-Anforderung mit dem To-Do-Element im Anforderungstext.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-944">jQuery sends an HTTP POST request with the to-do item in the request body.</span></span> <span data-ttu-id="4f6bd-945">Die Optionen `accepts` und `contentType` werden auf `application/json` festgelegt, um den gesendeten und empfangenen Medientyp anzugeben.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-945">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="4f6bd-946">Die Aufgabe wird mit [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify) in JSON konvertiert.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-946">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="4f6bd-947">Wenn die API den Statuscode „Erfolgreich“ zurückgibt, wird die Funktion `getData` aufgerufen, um die HTML-Tabelle zu aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-947">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item-21"></a><span data-ttu-id="4f6bd-948">Aktualisieren eines To-Do-Elements 2.1</span><span class="sxs-lookup"><span data-stu-id="4f6bd-948">Update a to-do item 2.1</span></span>

<span data-ttu-id="4f6bd-949">Das Aktualisieren einer Aufgabe ist vergleichbar mit dem Hinzufügen einer Aufgabe.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-949">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="4f6bd-950">Die `url` ändert sich, um den eindeutigen Bezeichner des Elements hinzuzufügen. Der `type` lautet `PUT`.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-950">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item-21"></a><span data-ttu-id="4f6bd-951">Löschen eines To-Do-Elements 2.1</span><span class="sxs-lookup"><span data-stu-id="4f6bd-951">Delete a to-do item 2.1</span></span>

<span data-ttu-id="4f6bd-952">Eine Aufgabe wird folgendermaßen gelöscht: im AJAX-Aufruf wird `type` auf `DELETE` festgelegt, und der eindeutige Bezeichner des Elements wird in der URL angegeben.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-952">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

<a name="auth"></a>

## <a name="add-authentication-support-to-a-web-api-21"></a><span data-ttu-id="4f6bd-953">Hinzufügen der Authentifizierungsunterstützung zu einer Web-API 2.1</span><span class="sxs-lookup"><span data-stu-id="4f6bd-953">Add authentication support to a web API 2.1</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="additional-resources-21"></a><span data-ttu-id="4f6bd-954">Zusätzliche Ressourcen 2.1</span><span class="sxs-lookup"><span data-stu-id="4f6bd-954">Additional resources 2.1</span></span>

<span data-ttu-id="4f6bd-955">[Sie können den Beispielcode für dieses Tutorial anzeigen oder herunterladen.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples)</span><span class="sxs-lookup"><span data-stu-id="4f6bd-955">[View or download sample code for this tutorial](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span></span> <span data-ttu-id="4f6bd-956">[Informationen zum Herunterladen](xref:index#how-to-download-a-sample) finden Sie hier.</span><span class="sxs-lookup"><span data-stu-id="4f6bd-956">See [how to download](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="4f6bd-957">Weitere Informationen finden Sie in den folgenden Ressourcen:</span><span class="sxs-lookup"><span data-stu-id="4f6bd-957">For more information, see the following resources:</span></span>

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/intro>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [<span data-ttu-id="4f6bd-958">Dieses Tutorial auf YouTube</span><span class="sxs-lookup"><span data-stu-id="4f6bd-958">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=TTkhEyGBfAk)
