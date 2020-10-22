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
- Models
uid: tutorials/first-web-api
ms.openlocfilehash: 9f67f8104eb5c21f244449ca3a4aaa96c750836d
ms.sourcegitcommit: fad0cd264c9d07a48a8c6ba1690807e0f8728898
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2020
ms.locfileid: "92379477"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a><span data-ttu-id="0d434-103">Tutorial: Erstellen einer Web-API mit ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0d434-103">Tutorial: Create a web API with ASP.NET Core</span></span>

<span data-ttu-id="0d434-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5) und [Mike Wasson](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="0d434-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="0d434-105">In diesem Tutorial lernen Sie die Grundlagen der Erstellung einer Web-API mit ASP.NET Core kennen.</span><span class="sxs-lookup"><span data-stu-id="0d434-105">This tutorial teaches the basics of building a web API with ASP.NET Core.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="0d434-106">In diesem Tutorial lernen Sie, wie die folgenden Aufgaben ausgeführt werden:</span><span class="sxs-lookup"><span data-stu-id="0d434-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="0d434-107">Erstellen eines Web-API-Projekts</span><span class="sxs-lookup"><span data-stu-id="0d434-107">Create a web API project.</span></span>
> * <span data-ttu-id="0d434-108">Hinzufügen einer Modellklasse und eines Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="0d434-108">Add a model class and a database context.</span></span>
> * <span data-ttu-id="0d434-109">Erstellen eines Controllergerüsts mit CRUD-Methoden</span><span class="sxs-lookup"><span data-stu-id="0d434-109">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="0d434-110">Konfigurieren des Routings, der URL-Pfade und der Rückgabewerte</span><span class="sxs-lookup"><span data-stu-id="0d434-110">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="0d434-111">Aufrufen der Web-API mit Postman</span><span class="sxs-lookup"><span data-stu-id="0d434-111">Call the web API with Postman.</span></span>

<span data-ttu-id="0d434-112">Abschließend steht Ihnen eine Web-API zur Verfügung, die in einer relationalen Datenbank gespeicherte To-do-Elemente verwalten kann.</span><span class="sxs-lookup"><span data-stu-id="0d434-112">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="0d434-113">Übersicht</span><span class="sxs-lookup"><span data-stu-id="0d434-113">Overview</span></span>

<span data-ttu-id="0d434-114">In diesem Tutorial wird die folgende API erstellt:</span><span class="sxs-lookup"><span data-stu-id="0d434-114">This tutorial creates the following API:</span></span>

|<span data-ttu-id="0d434-115">API</span><span class="sxs-lookup"><span data-stu-id="0d434-115">API</span></span> | <span data-ttu-id="0d434-116">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="0d434-116">Description</span></span> | <span data-ttu-id="0d434-117">Anforderungstext</span><span class="sxs-lookup"><span data-stu-id="0d434-117">Request body</span></span> | <span data-ttu-id="0d434-118">Antworttext</span><span class="sxs-lookup"><span data-stu-id="0d434-118">Response body</span></span> |
|--- | ---- | ---- | ---- |
|`GET /api/TodoItems` | <span data-ttu-id="0d434-119">Alle To-do-Elemente abrufen</span><span class="sxs-lookup"><span data-stu-id="0d434-119">Get all to-do items</span></span> | <span data-ttu-id="0d434-120">Keine</span><span class="sxs-lookup"><span data-stu-id="0d434-120">None</span></span> | <span data-ttu-id="0d434-121">Array von To-do-Elementen</span><span class="sxs-lookup"><span data-stu-id="0d434-121">Array of to-do items</span></span>|
|`GET /api/TodoItems/{id}` | <span data-ttu-id="0d434-122">Ein Element nach ID abrufen</span><span class="sxs-lookup"><span data-stu-id="0d434-122">Get an item by ID</span></span> | <span data-ttu-id="0d434-123">Keine</span><span class="sxs-lookup"><span data-stu-id="0d434-123">None</span></span> | <span data-ttu-id="0d434-124">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="0d434-124">To-do item</span></span>|
|`POST /api/TodoItems` | <span data-ttu-id="0d434-125">Neues Element hinzufügen</span><span class="sxs-lookup"><span data-stu-id="0d434-125">Add a new item</span></span> | <span data-ttu-id="0d434-126">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="0d434-126">To-do item</span></span> | <span data-ttu-id="0d434-127">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="0d434-127">To-do item</span></span> |
|`PUT /api/TodoItems/{id}` | <span data-ttu-id="0d434-128">Vorhandenes Element aktualisieren &nbsp;</span><span class="sxs-lookup"><span data-stu-id="0d434-128">Update an existing item &nbsp;</span></span> | <span data-ttu-id="0d434-129">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="0d434-129">To-do item</span></span> | <span data-ttu-id="0d434-130">Keine</span><span class="sxs-lookup"><span data-stu-id="0d434-130">None</span></span> |
|<span data-ttu-id="0d434-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="0d434-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span></span> | <span data-ttu-id="0d434-132">Löschen eines Elements &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="0d434-132">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="0d434-133">Keine</span><span class="sxs-lookup"><span data-stu-id="0d434-133">None</span></span> | <span data-ttu-id="0d434-134">Keine</span><span class="sxs-lookup"><span data-stu-id="0d434-134">None</span></span>|

<span data-ttu-id="0d434-135">Das folgende Diagramm zeigt den Entwurf der App.</span><span class="sxs-lookup"><span data-stu-id="0d434-135">The following diagram shows the design of the app.</span></span>

![Der Client ist das Feld ganz links.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="0d434-141">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="0d434-141">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0d434-142">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0d434-142">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="0d434-143">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0d434-143">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0d434-144">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="0d434-144">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-5.0.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="0d434-145">Erstellen eines Webprojekts</span><span class="sxs-lookup"><span data-stu-id="0d434-145">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0d434-146">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0d434-146">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0d434-147">Klicken Sie im Menü **Datei** auf **Neu** > **Projekt**.</span><span class="sxs-lookup"><span data-stu-id="0d434-147">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="0d434-148">Wählen Sie die Vorlage **ASP.NET Core-Webanwendung** aus, und klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="0d434-148">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="0d434-149">Geben Sie dem Projekt den Namen *TodoApi*, und klicken Sie auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="0d434-149">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="0d434-150">Vergewissern Sie sich, dass im Dialogfeld **Neue ASP.NET Core-Webanwendung erstellen** die Optionen **.NET Core** und **ASP.NET Core 5.0** ausgewählt sind.</span><span class="sxs-lookup"><span data-stu-id="0d434-150">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 5.0** are selected.</span></span> <span data-ttu-id="0d434-151">Wählen Sie die Vorlage **API** aus, und klicken Sie auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="0d434-151">Select the **API** template and click **Create**.</span></span>

![VS-Dialogfeld „Neues Projekt“](first-web-api/_static/5/vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="0d434-153">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0d434-153">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="0d434-154">Öffnen Sie das [integrierte Terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="0d434-154">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="0d434-155">Wechseln Sie mit dem Befehl `cd`' zu dem Ordner, der den Projektordner enthalten soll.</span><span class="sxs-lookup"><span data-stu-id="0d434-155">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="0d434-156">Führen Sie die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="0d434-156">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="0d434-157">Wenn Sie in einem Dialogfeld angeben müssen, ob Sie dem Projekt die erforderlichen Elemente hinzufügen möchten, wählen Sie **Ja** aus.</span><span class="sxs-lookup"><span data-stu-id="0d434-157">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="0d434-158">Die obenstehenden Befehle haben folgende Konsequenzen:</span><span class="sxs-lookup"><span data-stu-id="0d434-158">The preceding commands:</span></span>

  * <span data-ttu-id="0d434-159">Ein neues Web-API-Projekt wird erstellt und in Visual Studio Code geöffnet.</span><span class="sxs-lookup"><span data-stu-id="0d434-159">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="0d434-160">Die NuGet-Pakete, die im nächsten Abschnitt erforderlich sind, werden hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="0d434-160">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0d434-161">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="0d434-161">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="0d434-162">Klicken Sie auf **Datei** > **Neue Projektmappe**.</span><span class="sxs-lookup"><span data-stu-id="0d434-162">Select **File** > **New Solution**.</span></span>

  ![Neue Projektmappe in macOS](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="0d434-164">Navigieren Sie in Visual Studio für Mac-Versionen vor Version 8.6 zu **.NET Core** > **App** > **API** > **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="0d434-164">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="0d434-165">Bei Version 8.6 oder höher klicken Sie auf **Web and Console** > **App** > **API** > **Weiter** (Web und Konsole).</span><span class="sxs-lookup"><span data-stu-id="0d434-165">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>

  ![Auswählen von macOS-API-Vorlagen](first-web-api-mac/_static/api_template.png)

* <span data-ttu-id="0d434-167">Wählen Sie im Dialogfeld **Neue ASP.NET Core-Web-API konfigurieren** die neueste .NET Core 3.x-Version als **Zielframework** aus.</span><span class="sxs-lookup"><span data-stu-id="0d434-167">In the **Configure the new ASP.NET Core Web API** dialog, select the latest .NET Core 3.x **Target Framework**.</span></span> <span data-ttu-id="0d434-168">Klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="0d434-168">Select **Next**.</span></span>

* <span data-ttu-id="0d434-169">Geben Sie für **Projektname** *TodoApi* ein, und wählen Sie dann **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="0d434-169">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![Dialogfeld „Konfiguration“](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="0d434-171">Öffnen Sie im Projektordner ein Befehlsterminal, und führen Sie die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="0d434-171">Open a command terminal in the project folder and run the following commands:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-project"></a><span data-ttu-id="0d434-172">Testen des Projekts</span><span class="sxs-lookup"><span data-stu-id="0d434-172">Test the project</span></span>

<span data-ttu-id="0d434-173">Die Projektvorlage erstellt eine `WeatherForecast`-API mit Unterstützung für [Swagger](xref:tutorials/web-api-help-pages-using-swagger).</span><span class="sxs-lookup"><span data-stu-id="0d434-173">The project template creates a `WeatherForecast` API with support for [Swagger](xref:tutorials/web-api-help-pages-using-swagger).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0d434-174">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0d434-174">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="0d434-175">Drücken Sie STRG+F5, um die Ausführung ohne den Debugger zu starten.</span><span class="sxs-lookup"><span data-stu-id="0d434-175">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="0d434-176">Visual Studio startet Folgendes:</span><span class="sxs-lookup"><span data-stu-id="0d434-176">Visual Studio launches:</span></span>

* <span data-ttu-id="0d434-177">Den IIS Express-Webserver.</span><span class="sxs-lookup"><span data-stu-id="0d434-177">The IIS Express web server.</span></span>
* <span data-ttu-id="0d434-178">Den Standardbrowser. Visual Studio navigiert zu `https://localhost:<port>/https://localhost:5001/swagger/index.html`, wobei es sich bei `<port>` um eine zufällig ausgewählte Portnummer handelt.</span><span class="sxs-lookup"><span data-stu-id="0d434-178">The default browser and navigates to `https://localhost:<port>/https://localhost:5001/swagger/index.html`, where `<port>` is a randomly chosen port number.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="0d434-179">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0d434-179">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/trustCertVSC.md)]

<span data-ttu-id="0d434-180">Drücken Sie STRG+F5, um die App auszuführen.</span><span class="sxs-lookup"><span data-stu-id="0d434-180">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="0d434-181">Navigieren Sie in einem Browser zur folgenden URL: [https://localhost:5001/swagger](https://localhost:5001/swagger).</span><span class="sxs-lookup"><span data-stu-id="0d434-181">In a browser, go to following URL: [https://localhost:5001/swagger](https://localhost:5001/swagger)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0d434-182">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="0d434-182">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="0d434-183">Wählen Sie **Ausführen** > **Debugging starten** aus, um die App zu starten.</span><span class="sxs-lookup"><span data-stu-id="0d434-183">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="0d434-184">Visual Studio für Mac startet einen Browser und navigiert zu `https://localhost:<port>`, wobei es sich bei `<port>` um eine zufällig ausgewählte Portnummer handelt.</span><span class="sxs-lookup"><span data-stu-id="0d434-184">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="0d434-185">Der Fehler „HTTP 404: Nicht gefunden“ wird zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="0d434-185">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="0d434-186">Fügen Sie der URL `/swagger` an, d.h. ändern Sie die URL in `https://localhost:<port>/swagger`.</span><span class="sxs-lookup"><span data-stu-id="0d434-186">Append `/swagger` to the URL (change the URL to `https://localhost:<port>/swagger`).</span></span>

---

<span data-ttu-id="0d434-187">Die Swagger-Seite `/swagger/index.html` wird angezeigt.</span><span class="sxs-lookup"><span data-stu-id="0d434-187">The Swagger page `/swagger/index.html` is displayed.</span></span> <span data-ttu-id="0d434-188">Wählen Sie **GET** > **Tryit out** > **Execute**  (GET > Testen> Ausführen) aus.</span><span class="sxs-lookup"><span data-stu-id="0d434-188">Select **GET** > **Try it out** > **Execute**.</span></span> <span data-ttu-id="0d434-189">Die Seite zeigt Folgendes an:</span><span class="sxs-lookup"><span data-stu-id="0d434-189">The page displays:</span></span>

* <span data-ttu-id="0d434-190">Der [Curl](https://curl.haxx.se/)-Befehl, zum Testen der WeatherForecast-API.</span><span class="sxs-lookup"><span data-stu-id="0d434-190">The [Curl](https://curl.haxx.se/) command to test the WeatherForecast API.</span></span>
* <span data-ttu-id="0d434-191">Die URL zum Testen der WeatherForecast-API.</span><span class="sxs-lookup"><span data-stu-id="0d434-191">The URL to test the WeatherForecast API.</span></span>
* <span data-ttu-id="0d434-192">Der Antwortcode, der Text und die Header.</span><span class="sxs-lookup"><span data-stu-id="0d434-192">The response code, body, and headers.</span></span>
* <span data-ttu-id="0d434-193">Ein Dropdown-Listenfeld mit Medientypen und dem Beispielwert und -schema.</span><span class="sxs-lookup"><span data-stu-id="0d434-193">A drop down list box with media types and the example value and schema.</span></span>

<!-- Review: Do we care the IE generates several errors. It shows the data, but with  Unrecognized response type; displaying content as text.
-->
<span data-ttu-id="0d434-194">Swagger wird verwendet, um hilfreiche Dokumentation und Hilfeseiten für Web-APIs zu generieren.</span><span class="sxs-lookup"><span data-stu-id="0d434-194">Swagger is used to generate useful documentation and help pages for web APIs.</span></span> <span data-ttu-id="0d434-195">Dieses Tutorial konzentriert sich auf die Erstellung einer Web-API.</span><span class="sxs-lookup"><span data-stu-id="0d434-195">This tutorial focuses on creating a web API.</span></span> <span data-ttu-id="0d434-196">Weitere Informationen zu Swagger finden Sie unter <xref:tutorials/web-api-help-pages-using-swagger>.</span><span class="sxs-lookup"><span data-stu-id="0d434-196">For more information on Swagger, see <xref:tutorials/web-api-help-pages-using-swagger>.</span></span>

<span data-ttu-id="0d434-197">Kopieren Sie die **Anforderungs-URL**, und fügen Sie sie im Browser ein: `https://localhost:<port>/WeatherForecast`</span><span class="sxs-lookup"><span data-stu-id="0d434-197">Copy and past the **Request URL** in the browser:  `https://localhost:<port>/WeatherForecast`</span></span>

<span data-ttu-id="0d434-198">Der zurückgegebene JSON-Code sieht in etwa wie folgt aus:</span><span class="sxs-lookup"><span data-stu-id="0d434-198">JSON similar to the following is returned:</span></span>

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

### <a name="update-the-launchurl"></a><span data-ttu-id="0d434-199">Aktualisieren der launchUrl</span><span class="sxs-lookup"><span data-stu-id="0d434-199">Update the launchUrl</span></span>

<span data-ttu-id="0d434-200">Aktualisieren Sie `launchUrl` in *Properties\launchSettings.json* aus `"swagger"` in `"api/TodoItems"`:</span><span class="sxs-lookup"><span data-stu-id="0d434-200">In *Properties\launchSettings.json*, update `launchUrl` from `"swagger"` to `"api/TodoItems"`:</span></span>

```json
"launchUrl": "api/TodoItems",
```

<span data-ttu-id="0d434-201">Da Swagger entfernt wurde, ändert das oben gezeigte Markup die URL, die für die GET-Methode des Controllers gestartet wird, der in den folgenden Abschnitten hinzugefügt wurde.</span><span class="sxs-lookup"><span data-stu-id="0d434-201">Because Swagger has been removed, the preceding markup changes the URL that is launched to the GET method of the controller added in the following sections.</span></span>

## <a name="add-a-model-class"></a><span data-ttu-id="0d434-202">Hinzufügen einer Modellklasse</span><span class="sxs-lookup"><span data-stu-id="0d434-202">Add a model class</span></span>

<span data-ttu-id="0d434-203">Ein *Modell* ist eine Gruppe von Klassen, die die Daten darstellen, die die App verwaltet.</span><span class="sxs-lookup"><span data-stu-id="0d434-203">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="0d434-204">Das Modell für diese App ist eine einzelne `TodoItem`-Klasse.</span><span class="sxs-lookup"><span data-stu-id="0d434-204">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0d434-205">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0d434-205">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0d434-206">Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt.</span><span class="sxs-lookup"><span data-stu-id="0d434-206">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="0d434-207">Klicken Sie auf **Hinzufügen** > **Neuer Ordner**.</span><span class="sxs-lookup"><span data-stu-id="0d434-207">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="0d434-208">Geben Sie dem Ordner den Namen *Models* .</span><span class="sxs-lookup"><span data-stu-id="0d434-208">Name the folder *Models*.</span></span>

* <span data-ttu-id="0d434-209">Klicken Sie mit der rechten Maustaste auf den Ordner *Models* , und wählen Sie **Hinzufügen** > **Klasse** aus.</span><span class="sxs-lookup"><span data-stu-id="0d434-209">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="0d434-210">Nennen Sie die Klasse *TodoItem*, und wählen Sie **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="0d434-210">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="0d434-211">Ersetzen Sie den Vorlagencode durch Folgendes:</span><span class="sxs-lookup"><span data-stu-id="0d434-211">Replace the template code with the following:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="0d434-212">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0d434-212">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="0d434-213">Fügen Sie einen Ordner mit dem Namen *Models* hinzu.</span><span class="sxs-lookup"><span data-stu-id="0d434-213">Add a folder named *Models*.</span></span>

* <span data-ttu-id="0d434-214">Fügen Sie dem Ordner *Models* mit dem folgenden Code eine `TodoItem`-Klasse hinzu:</span><span class="sxs-lookup"><span data-stu-id="0d434-214">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0d434-215">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="0d434-215">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="0d434-216">Klicken Sie mit der rechten Maustaste auf das Projekt.</span><span class="sxs-lookup"><span data-stu-id="0d434-216">Right-click the project.</span></span> <span data-ttu-id="0d434-217">Klicken Sie auf **Hinzufügen** > **Neuer Ordner**.</span><span class="sxs-lookup"><span data-stu-id="0d434-217">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="0d434-218">Geben Sie dem Ordner den Namen *Models* .</span><span class="sxs-lookup"><span data-stu-id="0d434-218">Name the folder *Models*.</span></span>

  ![Neuer Ordner](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="0d434-220">Klicken Sie mit der rechten Maustaste auf den Ordner *Models* , und wählen Sie **Hinzufügen** > **Neue Datei** > **Allgemein** > **Leere Klasse** aus.</span><span class="sxs-lookup"><span data-stu-id="0d434-220">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="0d434-221">Nennen Sie die Klasse *TodoItem*, und klicken Sie dann auf **Neu**.</span><span class="sxs-lookup"><span data-stu-id="0d434-221">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="0d434-222">Ersetzen Sie den Vorlagencode durch Folgendes:</span><span class="sxs-lookup"><span data-stu-id="0d434-222">Replace the template code with the following:</span></span>

---

  [!code-csharp[](first-web-api/samples/5.x/TodoApi/Models/TodoItem.cs?name=snippet)]

<span data-ttu-id="0d434-223">Die `Id`-Eigenschaft fungiert als eindeutiger Schlüssel in einer relationalen Datenbank.</span><span class="sxs-lookup"><span data-stu-id="0d434-223">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="0d434-224">Modellklassen können überall im Projekt platziert werden, doch gemäß der Konvention wird der Ordner *Models* verwendet.</span><span class="sxs-lookup"><span data-stu-id="0d434-224">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="0d434-225">Hinzufügen eines Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="0d434-225">Add a database context</span></span>

<span data-ttu-id="0d434-226">Der *Datenbankkontext* ist die Hauptklasse, die die Entity Framework-Funktionen für ein Datenmodell koordiniert.</span><span class="sxs-lookup"><span data-stu-id="0d434-226">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="0d434-227">Diese Klasse wird durch Ableiten von der <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName>-Klasse erstellt.</span><span class="sxs-lookup"><span data-stu-id="0d434-227">This class is created by deriving from the <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName> class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0d434-228">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0d434-228">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-nuget-packages"></a><span data-ttu-id="0d434-229">Hinzufügen von NuGet-Paketen</span><span class="sxs-lookup"><span data-stu-id="0d434-229">Add NuGet packages</span></span>

* <span data-ttu-id="0d434-230">Klicken Sie im Menü **Extras** auf **NuGet-Paket-Manager > NuGet-Pakete für Projektmappe verwalten...** .</span><span class="sxs-lookup"><span data-stu-id="0d434-230">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="0d434-231">Wählen Sie die Registerkarte **Durchsuchen** aus, und geben Sie dann Folgendes in das Suchfeld ein: \*\*Microsoft.</span><span class="sxs-lookup"><span data-stu-id="0d434-231">Select the **Browse** tab, and then enter \*\*Microsoft.</span></span>
<span data-ttu-id="0d434-232">**EntityFrameworkCore.SqlServer**.</span><span class="sxs-lookup"><span data-stu-id="0d434-232">**EntityFrameworkCore.SqlServer** in the search box.</span></span>
<!-- https://github.com/dotnet/AspNetCore.Docs/issues/19782 Delete this line at RTM -->
* <span data-ttu-id="0d434-233">Aktivieren Sie das Kontrollkästchen **Vorabversion einschließen**, damit die Version 5.0 RC verfügbar ist.</span><span class="sxs-lookup"><span data-stu-id="0d434-233">Select the **Include prerelease** checkbox so the 5.0 RC version is available.</span></span> 
* <span data-ttu-id="0d434-234">Wählen Sie im linken Bereich **Microsoft.EntityFrameworkCore.SqlServer** aus.</span><span class="sxs-lookup"><span data-stu-id="0d434-234">Select **Microsoft.EntityFrameworkCore.SqlServer** in the left pane.</span></span>
* <span data-ttu-id="0d434-235">Aktivieren Sie das Kontrollkästchen **Projekt** im rechten Bereich, und klicken Sie dann auf **Installieren**.</span><span class="sxs-lookup"><span data-stu-id="0d434-235">Select the **Project** check box in the right pane and then select **Install**.</span></span>
* <span data-ttu-id="0d434-236">Verwenden Sie die Anweisungen oben zum Hinzuzufügen des **Microsoft.EntityFrameworkCore.InMemory**-NuGet-Pakets.</span><span class="sxs-lookup"><span data-stu-id="0d434-236">Use the preceding instructions to add the **Microsoft.EntityFrameworkCore.InMemory** NuGet package.</span></span>

<!-- https://github.com/dotnet/AspNetCore.Docs/issues/19782 Update this image at RTM -->
<span data-ttu-id="0d434-237">![NuGet-Paket-Manager](first-web-api/_static/5/vsNuGet.png)</span><span class="sxs-lookup"><span data-stu-id="0d434-237">![NuGet Package Manager](first-web-api/_static/5/vsNuGet.png)</span></span>

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="0d434-238">Hinzufügen des TodoContext-Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="0d434-238">Add the TodoContext database context</span></span>

* <span data-ttu-id="0d434-239">Klicken Sie mit der rechten Maustaste auf den Ordner *Models* , und wählen Sie **Hinzufügen** > **Klasse** aus.</span><span class="sxs-lookup"><span data-stu-id="0d434-239">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="0d434-240">Nennen Sie die Klasse *TodoContext*, und klicken Sie auf **Hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="0d434-240">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="0d434-241">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="0d434-241">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="0d434-242">Fügen Sie eine `TodoContext`-Klasse zum Ordner *Models* hinzu.</span><span class="sxs-lookup"><span data-stu-id="0d434-242">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="0d434-243">Geben Sie den folgenden Code ein:</span><span class="sxs-lookup"><span data-stu-id="0d434-243">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/5.x/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="0d434-244">Registrieren des Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="0d434-244">Register the database context</span></span>

<span data-ttu-id="0d434-245">In ASP.NET Core müssen Dienste wie der Datenbankkontext mit dem Container [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) registriert werden.</span><span class="sxs-lookup"><span data-stu-id="0d434-245">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="0d434-246">Der Container stellt den Dienst für Controller bereit.</span><span class="sxs-lookup"><span data-stu-id="0d434-246">The container provides the service to controllers.</span></span>

<span data-ttu-id="0d434-247">Aktualisieren Sie *Startup.cs* mit dem folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="0d434-247">Update *Startup.cs* with the following code:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="0d434-248">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="0d434-248">The preceding code:</span></span>

* <span data-ttu-id="0d434-249">Entfernt die Swagger-Aufrufe.</span><span class="sxs-lookup"><span data-stu-id="0d434-249">Removes the Swagger calls.</span></span>
* <span data-ttu-id="0d434-250">Entfernt nicht benötigte `using`-Deklarationen</span><span class="sxs-lookup"><span data-stu-id="0d434-250">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="0d434-251">Fügt dem Abhängigkeitscontainer den Datenbankkontext hinzu</span><span class="sxs-lookup"><span data-stu-id="0d434-251">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="0d434-252">Gibt an, dass der Datenbankkontext eine In-Memory Database verwendet</span><span class="sxs-lookup"><span data-stu-id="0d434-252">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="0d434-253">Erstellen eines Controllergerüsts</span><span class="sxs-lookup"><span data-stu-id="0d434-253">Scaffold a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0d434-254">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0d434-254">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0d434-255">Klicken Sie mit der rechten Maustaste auf den Ordner *Controller*.</span><span class="sxs-lookup"><span data-stu-id="0d434-255">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="0d434-256">Wählen Sie **Hinzufügen** > **Neues Gerüstelement** aus.</span><span class="sxs-lookup"><span data-stu-id="0d434-256">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="0d434-257">Klicken Sie auf **API-Controller mit Aktionen unter Verwendung von Entity Framework** und dann auf **Hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="0d434-257">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="0d434-258">Führen Sie im Dialogfeld **API-Controller mit Aktionen unter Verwendung von Entity Framework** folgende Schritte aus:</span><span class="sxs-lookup"><span data-stu-id="0d434-258">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="0d434-259">Wählen Sie in der **Modellklasse** das Element **TodoItem (TodoApi.Models)** aus.</span><span class="sxs-lookup"><span data-stu-id="0d434-259">Select **TodoItem (TodoApi.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="0d434-260">Wählen Sie in der **Datenkontextklasse** das Element **TodoContext (TodoApi.Models)** aus.</span><span class="sxs-lookup"><span data-stu-id="0d434-260">Select **TodoContext (TodoApi.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="0d434-261">Wählen Sie **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="0d434-261">Select **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="0d434-262">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="0d434-262">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="0d434-263">Führen Sie die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="0d434-263">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet tool update -g Dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="0d434-264">Die obenstehenden Befehle haben folgende Konsequenzen:</span><span class="sxs-lookup"><span data-stu-id="0d434-264">The preceding commands:</span></span>

* <span data-ttu-id="0d434-265">Die NuGet-Pakete für den Gerüstbau werden hinzufügt.</span><span class="sxs-lookup"><span data-stu-id="0d434-265">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="0d434-266">Die Gerüstbau-Engine (`dotnet-aspnet-codegenerator`) wird installiert.</span><span class="sxs-lookup"><span data-stu-id="0d434-266">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="0d434-267">Das Gerüst für `TodoItemsController` wird erstellt.</span><span class="sxs-lookup"><span data-stu-id="0d434-267">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="0d434-268">Der generierte Code hat folgende Auswirkungen:</span><span class="sxs-lookup"><span data-stu-id="0d434-268">The generated code:</span></span>

* <span data-ttu-id="0d434-269">Markiert die Klasse mit dem [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute)-Attribut.</span><span class="sxs-lookup"><span data-stu-id="0d434-269">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="0d434-270">Dieses Attribut gibt an, dass der Controller auf Web-API-Anforderungen reagiert.</span><span class="sxs-lookup"><span data-stu-id="0d434-270">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="0d434-271">Weitere Informationen zu bestimmten Verhaltensweisen, die das Attribut ermöglicht, finden Sie unter <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="0d434-271">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="0d434-272">Verwendet die Abhängigkeitsinjektion, um den Datenbankkontext (`TodoContext`) dem Controller hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="0d434-272">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="0d434-273">Der Datenbankkontext wird in den einzelnen [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete)-Methoden im Controller verwendet.</span><span class="sxs-lookup"><span data-stu-id="0d434-273">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<span data-ttu-id="0d434-274">Die ASP.NET Core-Vorlagen für:</span><span class="sxs-lookup"><span data-stu-id="0d434-274">The ASP.NET Core templates for:</span></span>

* <span data-ttu-id="0d434-275">Controller mit Ansichten enthalten `[action]` in der Routenvorlage.</span><span class="sxs-lookup"><span data-stu-id="0d434-275">Controllers with views include `[action]` in the route template.</span></span>
* <span data-ttu-id="0d434-276">API-Controller enthalten keine `[action]` in der Routenvorlage.</span><span class="sxs-lookup"><span data-stu-id="0d434-276">API controllers don't include `[action]` in the route template.</span></span>

<span data-ttu-id="0d434-277">Wenn sich das `[action]`-Token nicht in der Routenvorlage befindet, wird der [action](xref:mvc/controllers/routing#action)-Name von der Route ausgeschlossen.</span><span class="sxs-lookup"><span data-stu-id="0d434-277">When the `[action]` token isn't in the route template, the [action](xref:mvc/controllers/routing#action) name is excluded from the route.</span></span> <span data-ttu-id="0d434-278">Dies bedeutet, dass der zugehörige Methodenname der Aktion nicht in der übereinstimmenden Route verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="0d434-278">That is, the action's associated method name isn't used in the matching route.</span></span>

## <a name="update-the-posttodoitem-create-method"></a><span data-ttu-id="0d434-279">Aktualisieren der PostTodoItem-Erstellungsmethode</span><span class="sxs-lookup"><span data-stu-id="0d434-279">Update the PostTodoItem create method</span></span>

<span data-ttu-id="0d434-280">Ersetzen Sie die Rückgabeanweisung in `PostTodoItem` durch eine Anweisung mit dem [nameof](/dotnet/csharp/language-reference/operators/nameof)-Operator:</span><span class="sxs-lookup"><span data-stu-id="0d434-280">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="0d434-281">Der oben stehende Code ist eine HTTP POST-Methode, wie durch das [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute)-Attribut angegeben.</span><span class="sxs-lookup"><span data-stu-id="0d434-281">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="0d434-282">Die Methode ruft den Wert der Aufgabe aus dem Text der HTTP-Anforderung ab.</span><span class="sxs-lookup"><span data-stu-id="0d434-282">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="0d434-283">Weitere Informationen finden Sie unter [Attributrouting mit Http[Verb]-Attributen](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="0d434-283">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="0d434-284">Die <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*>-Methode:</span><span class="sxs-lookup"><span data-stu-id="0d434-284">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="0d434-285">Gibt bei Erfolg den [HTTP-Statuscode 201](https://developer.mozilla.org/docs/Web/HTTP/Status/201) zurück.</span><span class="sxs-lookup"><span data-stu-id="0d434-285">Returns an [HTTP 201 status code](https://developer.mozilla.org/docs/Web/HTTP/Status/201) if successful.</span></span> <span data-ttu-id="0d434-286">HTTP 201 ist die Standardantwort für eine HTTP POST-Methode, die eine neue Ressource auf dem Server erstellt.</span><span class="sxs-lookup"><span data-stu-id="0d434-286">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="0d434-287">Fügt der Antwort einen [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location)-Header hinzu.</span><span class="sxs-lookup"><span data-stu-id="0d434-287">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="0d434-288">Der `Location`-Header legt den [URI](https://developer.mozilla.org/docs/Glossary/URI) des neu erstellten To-do-Elements fest.</span><span class="sxs-lookup"><span data-stu-id="0d434-288">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="0d434-289">Weitere Informationen finden Sie unter [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="0d434-289">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="0d434-290">Verweist auf die `GetTodoItem`-Aktion zum Erstellen des URIs des `Location`-Headers.</span><span class="sxs-lookup"><span data-stu-id="0d434-290">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="0d434-291">Das `nameof`-Schlüsselwort von C# wird verwendet, um eine Hartcodierung des Aktionsnamens im `CreatedAtAction`-Aufruf zu vermeiden.</span><span class="sxs-lookup"><span data-stu-id="0d434-291">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="0d434-292">Installieren von Postman</span><span class="sxs-lookup"><span data-stu-id="0d434-292">Install Postman</span></span>

<span data-ttu-id="0d434-293">Dieses Tutorial verwendet Postman zum Testen der Web-API.</span><span class="sxs-lookup"><span data-stu-id="0d434-293">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="0d434-294">Installieren Sie [Postman](https://www.getpostman.com/downloads/).</span><span class="sxs-lookup"><span data-stu-id="0d434-294">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="0d434-295">Starten Sie die Web-App.</span><span class="sxs-lookup"><span data-stu-id="0d434-295">Start the web app.</span></span>
* <span data-ttu-id="0d434-296">Starten Sie Postman.</span><span class="sxs-lookup"><span data-stu-id="0d434-296">Start Postman.</span></span>
* <span data-ttu-id="0d434-297">Deaktivieren Sie **SSL certificate verification** (Verifizierung des SSL-Zertifikats).</span><span class="sxs-lookup"><span data-stu-id="0d434-297">Disable **SSL certificate verification**</span></span>
  * <span data-ttu-id="0d434-298">Deaktivieren Sie auf der Registerkarte **General** (Allgemein) unter File > Settings (**Datei** > **Einstellungen**) **SSL certificate verification** (Verifizierung des SSL-Zertifikats).</span><span class="sxs-lookup"><span data-stu-id="0d434-298">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="0d434-299">Aktivieren Sie die Verifizierung des SSL-Zertifikats wieder, nachdem Sie den Controller getestet haben.</span><span class="sxs-lookup"><span data-stu-id="0d434-299">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="0d434-300">Testen von PostTodoItem mit Postman</span><span class="sxs-lookup"><span data-stu-id="0d434-300">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="0d434-301">Erstellen Sie eine neue Anforderung.</span><span class="sxs-lookup"><span data-stu-id="0d434-301">Create a new request.</span></span>
* <span data-ttu-id="0d434-302">Legen Sie als HTTP-Methode `POST` fest.</span><span class="sxs-lookup"><span data-stu-id="0d434-302">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="0d434-303">Legen Sie den URI auf `https://localhost:<port>/api/TodoItems` fest.</span><span class="sxs-lookup"><span data-stu-id="0d434-303">Set the URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="0d434-304">Beispiel: `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="0d434-304">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="0d434-305">Wählen Sie die Registerkarte **Body** (Text) aus.</span><span class="sxs-lookup"><span data-stu-id="0d434-305">Select the **Body** tab.</span></span>
* <span data-ttu-id="0d434-306">Wählen Sie das Optionsfeld **raw** (Unformatiert) aus.</span><span class="sxs-lookup"><span data-stu-id="0d434-306">Select the **raw** radio button.</span></span>
* <span data-ttu-id="0d434-307">Legen Sie den Typ auf **JSON (application/json)** fest.</span><span class="sxs-lookup"><span data-stu-id="0d434-307">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="0d434-308">Geben Sie für die Aufgabe den Anforderungstext im JSON-Format ein:</span><span class="sxs-lookup"><span data-stu-id="0d434-308">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="0d434-309">Wählen Sie **Send** (Senden) aus.</span><span class="sxs-lookup"><span data-stu-id="0d434-309">Select **Send**.</span></span>

  ![Postman mit erstellter Anforderung](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri"></a><span data-ttu-id="0d434-311">Testen des Adressheader-URIs</span><span class="sxs-lookup"><span data-stu-id="0d434-311">Test the location header URI</span></span>

<span data-ttu-id="0d434-312">Der Location-Header-URI kann im Browser getestet werden.</span><span class="sxs-lookup"><span data-stu-id="0d434-312">The location header URI can be tested in the browser.</span></span> <span data-ttu-id="0d434-313">Kopieren Sie den Location-Header-URI, und fügen Sie ihn im Browser ein.</span><span class="sxs-lookup"><span data-stu-id="0d434-313">Copy and paste the location header URI into the browser.</span></span>

<span data-ttu-id="0d434-314">So testen Sie in Postman:</span><span class="sxs-lookup"><span data-stu-id="0d434-314">To test in Postman:</span></span>

* <span data-ttu-id="0d434-315">Wählen Sie auf der Registerkarte **Header** (Header) den Bereich **Response** (Antwort) aus.</span><span class="sxs-lookup"><span data-stu-id="0d434-315">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="0d434-316">Kopieren Sie den den Headerwert von **Location** (Speicherort):</span><span class="sxs-lookup"><span data-stu-id="0d434-316">Copy the **Location** header value:</span></span>

  ![Registerkarte „Header“ in der Postman-Konsole](first-web-api/_static/3/create.png)

* <span data-ttu-id="0d434-318">Legen Sie als HTTP-Methode `GET` fest.</span><span class="sxs-lookup"><span data-stu-id="0d434-318">Set the HTTP method to `GET`.</span></span>
* <span data-ttu-id="0d434-319">Legen Sie den URI auf `https://localhost:<port>/api/TodoItems/1` fest.</span><span class="sxs-lookup"><span data-stu-id="0d434-319">Set the URI to `https://localhost:<port>/api/TodoItems/1`.</span></span> <span data-ttu-id="0d434-320">Beispiel: `https://localhost:5001/api/TodoItems/1`.</span><span class="sxs-lookup"><span data-stu-id="0d434-320">For example, `https://localhost:5001/api/TodoItems/1`.</span></span>
* <span data-ttu-id="0d434-321">Wählen Sie **Send** (Senden) aus.</span><span class="sxs-lookup"><span data-stu-id="0d434-321">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="0d434-322">Überblick über die GET-Methoden</span><span class="sxs-lookup"><span data-stu-id="0d434-322">Examine the GET methods</span></span>

<span data-ttu-id="0d434-323">Zwei GET-Endpunkte werden implementiert:</span><span class="sxs-lookup"><span data-stu-id="0d434-323">Two GET endpoints are implemented:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="0d434-324">Testen Sie die App, indem Sie die beiden Endpunkte in einem Browser oder über Postman aufrufen.</span><span class="sxs-lookup"><span data-stu-id="0d434-324">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="0d434-325">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="0d434-325">For example:</span></span>

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

<span data-ttu-id="0d434-326">Durch einen Aufruf von `GetTodoItems` wird eine Antwort gesendet, die der folgenden ähnelt:</span><span class="sxs-lookup"><span data-stu-id="0d434-326">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="0d434-327">Testen von GET mit Postman</span><span class="sxs-lookup"><span data-stu-id="0d434-327">Test Get with Postman</span></span>

* <span data-ttu-id="0d434-328">Erstellen Sie eine neue Anforderung.</span><span class="sxs-lookup"><span data-stu-id="0d434-328">Create a new request.</span></span>
* <span data-ttu-id="0d434-329">Legen Sie die HTTP-Methode auf **GET** fest.</span><span class="sxs-lookup"><span data-stu-id="0d434-329">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="0d434-330">Legen Sie den Anforderungs-URI auf `https://localhost:<port>/api/TodoItems` fest.</span><span class="sxs-lookup"><span data-stu-id="0d434-330">Set the request URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="0d434-331">Beispielsweise `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="0d434-331">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="0d434-332">Wählen Sie in Postman **Two pane view** (Ansicht mit zwei Bereichen) aus.</span><span class="sxs-lookup"><span data-stu-id="0d434-332">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="0d434-333">Wählen Sie **Send** (Senden) aus.</span><span class="sxs-lookup"><span data-stu-id="0d434-333">Select **Send**.</span></span>

<span data-ttu-id="0d434-334">Diese App verwendet eine In-Memory-Datenbank.</span><span class="sxs-lookup"><span data-stu-id="0d434-334">This app uses an in-memory database.</span></span> <span data-ttu-id="0d434-335">Wenn die App angehalten und dann wieder gestartet wird, werden durch die vorherige GET-Anforderung keine Daten zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="0d434-335">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="0d434-336">Wenn keine Daten zurückgegeben werden, senden Sie mit [POST](#post) Daten an die App.</span><span class="sxs-lookup"><span data-stu-id="0d434-336">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="0d434-337">Routing und URL-Pfade</span><span class="sxs-lookup"><span data-stu-id="0d434-337">Routing and URL paths</span></span>

<span data-ttu-id="0d434-338">Das Attribut [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) gibt eine Methode an, die auf eine HTTP GET-Anforderung antwortet.</span><span class="sxs-lookup"><span data-stu-id="0d434-338">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="0d434-339">Der URL-Pfad für jede Methode wird wie folgt erstellt:</span><span class="sxs-lookup"><span data-stu-id="0d434-339">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="0d434-340">Beginnen Sie mit der Vorlagenzeichenfolge im `Route`-Attribut des Controllers:</span><span class="sxs-lookup"><span data-stu-id="0d434-340">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="0d434-341">Ersetzen Sie `[controller]` durch den Namen des Controllers, bei dem es sich standardmäßig um den Namen der Controller-Klasse ohne das Suffix „Controller“ handelt.</span><span class="sxs-lookup"><span data-stu-id="0d434-341">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="0d434-342">In diesem Beispiel ist der Klassenname des Controllers „**TodoItems**Controller“. Der Controllername lautet also „TodoItems“.</span><span class="sxs-lookup"><span data-stu-id="0d434-342">For this sample, the controller class name is **TodoItems**Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="0d434-343">Beim ASP.NET Core-[Routing](xref:mvc/controllers/routing) wird die Groß-/Kleinschreibung nicht beachtet.</span><span class="sxs-lookup"><span data-stu-id="0d434-343">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="0d434-344">Wenn das `[HttpGet]`-Attribut eine Routenvorlage (z. B. `[HttpGet("products")]`) hat, fügen Sie diese an den Pfad an.</span><span class="sxs-lookup"><span data-stu-id="0d434-344">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="0d434-345">In diesem Beispiel wird keine Vorlage verwendet.</span><span class="sxs-lookup"><span data-stu-id="0d434-345">This sample doesn't use a template.</span></span> <span data-ttu-id="0d434-346">Weitere Informationen finden Sie unter [Attributrouting mit Http[Verb]-Attributen](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="0d434-346">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="0d434-347">In der folgenden `GetTodoItem`-Methode ist `"{id}"` eine Platzhaltervariable für den eindeutigen Bezeichner des To-do-Elements.</span><span class="sxs-lookup"><span data-stu-id="0d434-347">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="0d434-348">Wenn `GetTodoItem` aufgerufen wird, wird der Wert von `"{id}"` in der URL der Methode in ihrem Parameter `id` bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="0d434-348">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="0d434-349">Rückgabewerte</span><span class="sxs-lookup"><span data-stu-id="0d434-349">Return values</span></span>

<span data-ttu-id="0d434-350">Der Rückgabetyp der Methoden `GetTodoItems` und `GetTodoItem` ist der [ActionResult\<T>-Typ](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="0d434-350">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="0d434-351">ASP.NET Core serialisiert automatisch das Objekt in [JSON](https://www.json.org/) und schreibt den JSON-Code in den Text der Antwortnachricht.</span><span class="sxs-lookup"><span data-stu-id="0d434-351">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="0d434-352">Der Antwortcode für diesen Rückgabetyp ist [200 OK](https://developer.mozilla.org/docs/Web/HTTP/Status/200), vorausgesetzt, es gibt keine Ausnahmefehler.</span><span class="sxs-lookup"><span data-stu-id="0d434-352">The response code for this return type is [200 OK](https://developer.mozilla.org/docs/Web/HTTP/Status/200), assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="0d434-353">Nicht behandelte Ausnahmen werden in 5xx-Fehler übersetzt.</span><span class="sxs-lookup"><span data-stu-id="0d434-353">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="0d434-354">`ActionResult`-Rückgabetypen können eine Vielzahl von HTTP-Statuscodes darstellen.</span><span class="sxs-lookup"><span data-stu-id="0d434-354">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="0d434-355">Beispielsweise kann `GetTodoItem` zwei verschiedene Statuswerte zurückgeben:</span><span class="sxs-lookup"><span data-stu-id="0d434-355">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="0d434-356">Wenn kein Element mit der angeforderten ID übereinstimmt, gibt die Methode einen <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A>-Fehlercode [Status 404](https://developer.mozilla.org/docs/Web/HTTP/Status/404) zurück.</span><span class="sxs-lookup"><span data-stu-id="0d434-356">If no item matches the requested ID, the method returns a [404 status](https://developer.mozilla.org/docs/Web/HTTP/Status/404) <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="0d434-357">Andernfalls gibt die Methode 200 mit einem JSON-Antworttext zurück.</span><span class="sxs-lookup"><span data-stu-id="0d434-357">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="0d434-358">Die Rückgabe von `item` löst eine HTTP 200-Antwort aus.</span><span class="sxs-lookup"><span data-stu-id="0d434-358">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="0d434-359">PutTodoItem-Methode</span><span class="sxs-lookup"><span data-stu-id="0d434-359">The PutTodoItem method</span></span>

<span data-ttu-id="0d434-360">Untersuchen Sie die `PutTodoItem`-Methode.</span><span class="sxs-lookup"><span data-stu-id="0d434-360">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="0d434-361">`PutTodoItem` ähnelt `PostTodoItem`, verwendet allerdings HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="0d434-361">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="0d434-362">Die Antwort ist [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="0d434-362">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="0d434-363">Gemäß der HTTP-Spezifikation erfordert eine PUT-Anforderung, dass der Client die gesamte aktualisierte Entität (nicht nur die Änderungen) sendet.</span><span class="sxs-lookup"><span data-stu-id="0d434-363">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="0d434-364">Verwenden Sie [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute), um Teilupdates zu unterstützen.</span><span class="sxs-lookup"><span data-stu-id="0d434-364">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="0d434-365">Wenn beim Aufrufen von `PutTodoItem` ein Fehler zurückgegeben wird, rufen Sie `GET` auf, um sicherzustellen, dass die Datenbank ein Element enthält.</span><span class="sxs-lookup"><span data-stu-id="0d434-365">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="0d434-366">Testen der PutTodoItem-Methode</span><span class="sxs-lookup"><span data-stu-id="0d434-366">Test the PutTodoItem method</span></span>

<span data-ttu-id="0d434-367">In diesem Beispiel wird eine In-Memory-Datenbank verwendet, die jedes Mal initialisiert werden muss, wenn die App gestartet wird.</span><span class="sxs-lookup"><span data-stu-id="0d434-367">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="0d434-368">Es muss ein Element in der Datenbank vorhanden sein, bevor Sie einen PUT-Aufruf durchführen.</span><span class="sxs-lookup"><span data-stu-id="0d434-368">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="0d434-369">Rufen Sie vor einem PUT-Aufruf GET auf, um sicherzustellen, dass ein Element in der Datenbank vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="0d434-369">Call GET to ensure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="0d434-370">Aktualisieren Sie das To-do-Element, das über den ID-Wert 1 verfügt, und legen Sie als Namen `"feed fish"` fest:</span><span class="sxs-lookup"><span data-stu-id="0d434-370">Update the to-do item that has Id = 1 and set its name to `"feed fish"`:</span></span>

```json
  {
    "Id":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="0d434-371">Die folgende Abbildung zeigt das Postman-Update:</span><span class="sxs-lookup"><span data-stu-id="0d434-371">The following image shows the Postman update:</span></span>

![Postman-Konsole mit der Antwort „204 (Kein Inhalt)“](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="0d434-373">DeleteTodoItem-Methode</span><span class="sxs-lookup"><span data-stu-id="0d434-373">The DeleteTodoItem method</span></span>

<span data-ttu-id="0d434-374">Untersuchen Sie die `DeleteTodoItem`-Methode.</span><span class="sxs-lookup"><span data-stu-id="0d434-374">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="0d434-375">Testen der DeleteTodoItem-Methode</span><span class="sxs-lookup"><span data-stu-id="0d434-375">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="0d434-376">So löschen Sie mit Postman eine Aufgabe</span><span class="sxs-lookup"><span data-stu-id="0d434-376">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="0d434-377">Legen Sie die Methode auf `DELETE` fest.</span><span class="sxs-lookup"><span data-stu-id="0d434-377">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="0d434-378">Legen Sie den URI des zu löschenden Objekts fest, z. B. `https://localhost:5001/api/TodoItems/1`.</span><span class="sxs-lookup"><span data-stu-id="0d434-378">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="0d434-379">Wählen Sie **Send** (Senden) aus.</span><span class="sxs-lookup"><span data-stu-id="0d434-379">Select **Send**.</span></span>

<a name="over-post-v5"></a>

## <a name="prevent-over-posting"></a><span data-ttu-id="0d434-380">Vermeiden von Overposting</span><span class="sxs-lookup"><span data-stu-id="0d434-380">Prevent over-posting</span></span>

<span data-ttu-id="0d434-381">Derzeit macht die Beispiel-App das gesamte `TodoItem`-Objekt verfügbar.</span><span class="sxs-lookup"><span data-stu-id="0d434-381">Currently the sample app exposes the entire `TodoItem` object.</span></span> <span data-ttu-id="0d434-382">In den Produktions-Apps sind die Daten, die eingegeben und mithilfe einer Teilmenge des Modells zurückgegeben werden, in der Regel eingeschränkt.</span><span class="sxs-lookup"><span data-stu-id="0d434-382">Production apps typically limit the data that's input and returned using a subset of the model.</span></span> <span data-ttu-id="0d434-383">Hierfür gibt es mehrere Gründe, wobei die Sicherheit einer der Hauptgründe ist.</span><span class="sxs-lookup"><span data-stu-id="0d434-383">There are multiple reasons behind this and security is a major one.</span></span> <span data-ttu-id="0d434-384">Die Teilmenge eines Modells wird üblicherweise als Datenübertragungsobjekt (DTO, Data Transfer Object), Eingabemodell oder Anzeigemodell bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="0d434-384">The subset of a model is usually referred to as a Data Transfer Object (DTO), input model, or view model.</span></span> <span data-ttu-id="0d434-385">In diesem Artikel wird das **DTO** verwendet.</span><span class="sxs-lookup"><span data-stu-id="0d434-385">**DTO** is used in this article.</span></span>

<span data-ttu-id="0d434-386">Ein DTO kann für Folgendes verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="0d434-386">A DTO may be used to:</span></span>

* <span data-ttu-id="0d434-387">Vermeiden Sie Overposting.</span><span class="sxs-lookup"><span data-stu-id="0d434-387">Prevent over-posting.</span></span>
* <span data-ttu-id="0d434-388">Ausblenden von Eigenschaften, die Clients nicht anzeigen sollen</span><span class="sxs-lookup"><span data-stu-id="0d434-388">Hide properties that clients are not supposed to view.</span></span>
* <span data-ttu-id="0d434-389">Auslassen einiger Eigenschaften, um die Nutzlast zu verringern</span><span class="sxs-lookup"><span data-stu-id="0d434-389">Omit some properties in order to reduce payload size.</span></span>
* <span data-ttu-id="0d434-390">Vereinfachen von Objektgraphen, die geschachtelte Objekte enthalten</span><span class="sxs-lookup"><span data-stu-id="0d434-390">Flatten object graphs that contain nested objects.</span></span> <span data-ttu-id="0d434-391">Vereinfachte Objektgraphen können für Clients zweckmäßiger sein.</span><span class="sxs-lookup"><span data-stu-id="0d434-391">Flattened object graphs can be more convenient for clients.</span></span>

<span data-ttu-id="0d434-392">Um den DTO-Ansatz zu veranschaulichen, aktualisieren Sie die `TodoItem`-Klasse, sodass sie ein geheimes Feld einschließt:</span><span class="sxs-lookup"><span data-stu-id="0d434-392">To demonstrate the DTO approach, update the `TodoItem` class to include a secret field:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApiDTO/Models/TodoItem.cs?name=snippet&highlight=6)]

<span data-ttu-id="0d434-393">Das geheime Feld muss in dieser App ausgeblendet werden, eine administrative App kann es jedoch verfügbar machen.</span><span class="sxs-lookup"><span data-stu-id="0d434-393">The secret field needs to be hidden from this app, but an administrative app could choose to expose it.</span></span>

<span data-ttu-id="0d434-394">Vergewissern Sie sich, dass Sie das geheime Feld veröffentlichen und abrufen können.</span><span class="sxs-lookup"><span data-stu-id="0d434-394">Verify you can post and get the secret field.</span></span>

<span data-ttu-id="0d434-395">Erstellen Sie ein DTO-Modell:</span><span class="sxs-lookup"><span data-stu-id="0d434-395">Create a DTO model:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApiDTO/Models/TodoItemDTO.cs?name=snippet)]

<span data-ttu-id="0d434-396">Aktualisieren Sie `TodoItemsController`, sodass `TodoItemDTO` verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="0d434-396">Update the `TodoItemsController` to use `TodoItemDTO`:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

<span data-ttu-id="0d434-397">Vergewissern Sie sich, dass Sie das geheime Feld weder veröffentlichen noch abrufen können.</span><span class="sxs-lookup"><span data-stu-id="0d434-397">Verify you can't post or get the secret field.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="0d434-398">Aufrufen der Web-API mit JavaScript</span><span class="sxs-lookup"><span data-stu-id="0d434-398">Call the web API with JavaScript</span></span>

<span data-ttu-id="0d434-399">Mehr dazu finden Sie im [-Tutorial: Aufrufen einer ASP.NET Core-Web-API mit JavaScript](xref:tutorials/web-api-javascript).</span><span class="sxs-lookup"><span data-stu-id="0d434-399">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="0d434-400">In diesem Tutorial lernen Sie, wie die folgenden Aufgaben ausgeführt werden:</span><span class="sxs-lookup"><span data-stu-id="0d434-400">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="0d434-401">Erstellen eines Web-API-Projekts</span><span class="sxs-lookup"><span data-stu-id="0d434-401">Create a web API project.</span></span>
> * <span data-ttu-id="0d434-402">Hinzufügen einer Modellklasse und eines Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="0d434-402">Add a model class and a database context.</span></span>
> * <span data-ttu-id="0d434-403">Erstellen eines Controllergerüsts mit CRUD-Methoden</span><span class="sxs-lookup"><span data-stu-id="0d434-403">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="0d434-404">Konfigurieren des Routings, der URL-Pfade und der Rückgabewerte</span><span class="sxs-lookup"><span data-stu-id="0d434-404">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="0d434-405">Aufrufen der Web-API mit Postman</span><span class="sxs-lookup"><span data-stu-id="0d434-405">Call the web API with Postman.</span></span>

<span data-ttu-id="0d434-406">Abschließend steht Ihnen eine Web-API zur Verfügung, die in einer relationalen Datenbank gespeicherte To-do-Elemente verwalten kann.</span><span class="sxs-lookup"><span data-stu-id="0d434-406">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="0d434-407">Übersicht</span><span class="sxs-lookup"><span data-stu-id="0d434-407">Overview</span></span>

<span data-ttu-id="0d434-408">In diesem Tutorial wird die folgende API erstellt:</span><span class="sxs-lookup"><span data-stu-id="0d434-408">This tutorial creates the following API:</span></span>

|<span data-ttu-id="0d434-409">API</span><span class="sxs-lookup"><span data-stu-id="0d434-409">API</span></span> | <span data-ttu-id="0d434-410">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="0d434-410">Description</span></span> | <span data-ttu-id="0d434-411">Anforderungstext</span><span class="sxs-lookup"><span data-stu-id="0d434-411">Request body</span></span> | <span data-ttu-id="0d434-412">Antworttext</span><span class="sxs-lookup"><span data-stu-id="0d434-412">Response body</span></span> |
|--- | ---- | ---- | ---- |
|`GET /api/TodoItems` | <span data-ttu-id="0d434-413">Alle To-do-Elemente abrufen</span><span class="sxs-lookup"><span data-stu-id="0d434-413">Get all to-do items</span></span> | <span data-ttu-id="0d434-414">Keine</span><span class="sxs-lookup"><span data-stu-id="0d434-414">None</span></span> | <span data-ttu-id="0d434-415">Array von To-do-Elementen</span><span class="sxs-lookup"><span data-stu-id="0d434-415">Array of to-do items</span></span>|
|`GET /api/TodoItems/{id}` | <span data-ttu-id="0d434-416">Ein Element nach ID abrufen</span><span class="sxs-lookup"><span data-stu-id="0d434-416">Get an item by ID</span></span> | <span data-ttu-id="0d434-417">Keine</span><span class="sxs-lookup"><span data-stu-id="0d434-417">None</span></span> | <span data-ttu-id="0d434-418">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="0d434-418">To-do item</span></span>|
|`POST /api/TodoItems` | <span data-ttu-id="0d434-419">Neues Element hinzufügen</span><span class="sxs-lookup"><span data-stu-id="0d434-419">Add a new item</span></span> | <span data-ttu-id="0d434-420">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="0d434-420">To-do item</span></span> | <span data-ttu-id="0d434-421">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="0d434-421">To-do item</span></span> |
|`PUT /api/TodoItems/{id}` | <span data-ttu-id="0d434-422">Vorhandenes Element aktualisieren &nbsp;</span><span class="sxs-lookup"><span data-stu-id="0d434-422">Update an existing item &nbsp;</span></span> | <span data-ttu-id="0d434-423">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="0d434-423">To-do item</span></span> | <span data-ttu-id="0d434-424">Keine</span><span class="sxs-lookup"><span data-stu-id="0d434-424">None</span></span> |
|<span data-ttu-id="0d434-425">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="0d434-425">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span></span> | <span data-ttu-id="0d434-426">Löschen eines Elements &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="0d434-426">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="0d434-427">Keine</span><span class="sxs-lookup"><span data-stu-id="0d434-427">None</span></span> | <span data-ttu-id="0d434-428">Keine</span><span class="sxs-lookup"><span data-stu-id="0d434-428">None</span></span>|

<span data-ttu-id="0d434-429">Das folgende Diagramm zeigt den Entwurf der App.</span><span class="sxs-lookup"><span data-stu-id="0d434-429">The following diagram shows the design of the app.</span></span>

![Der Client ist das Feld ganz links.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="0d434-435">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="0d434-435">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0d434-436">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0d434-436">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="0d434-437">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0d434-437">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0d434-438">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="0d434-438">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="0d434-439">Erstellen eines Webprojekts</span><span class="sxs-lookup"><span data-stu-id="0d434-439">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0d434-440">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0d434-440">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0d434-441">Klicken Sie im Menü **Datei** auf **Neu** > **Projekt**.</span><span class="sxs-lookup"><span data-stu-id="0d434-441">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="0d434-442">Wählen Sie die Vorlage **ASP.NET Core-Webanwendung** aus, und klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="0d434-442">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="0d434-443">Geben Sie dem Projekt den Namen *TodoApi*, und klicken Sie auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="0d434-443">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="0d434-444">Vergewissern Sie sich, dass im Dialogfeld **Neue ASP.NET Core-Webanwendung erstellen** die Optionen **.NET Core** und **ASP.NET Core 3.1** ausgewählt sind.</span><span class="sxs-lookup"><span data-stu-id="0d434-444">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.1** are selected.</span></span> <span data-ttu-id="0d434-445">Wählen Sie die Vorlage **API** aus, und klicken Sie auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="0d434-445">Select the **API** template and click **Create**.</span></span>

![VS-Dialogfeld „Neues Projekt“](first-web-api/_static/vs3.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="0d434-447">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0d434-447">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="0d434-448">Öffnen Sie das [integrierte Terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="0d434-448">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="0d434-449">Wechseln Sie mit dem Befehl `cd`' zu dem Ordner, der den Projektordner enthalten soll.</span><span class="sxs-lookup"><span data-stu-id="0d434-449">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="0d434-450">Führen Sie die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="0d434-450">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="0d434-451">Wenn Sie in einem Dialogfeld angeben müssen, ob Sie dem Projekt die erforderlichen Elemente hinzufügen möchten, wählen Sie **Ja** aus.</span><span class="sxs-lookup"><span data-stu-id="0d434-451">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="0d434-452">Die obenstehenden Befehle haben folgende Konsequenzen:</span><span class="sxs-lookup"><span data-stu-id="0d434-452">The preceding commands:</span></span>

  * <span data-ttu-id="0d434-453">Ein neues Web-API-Projekt wird erstellt und in Visual Studio Code geöffnet.</span><span class="sxs-lookup"><span data-stu-id="0d434-453">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="0d434-454">Die NuGet-Pakete, die im nächsten Abschnitt erforderlich sind, werden hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="0d434-454">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0d434-455">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="0d434-455">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="0d434-456">Klicken Sie auf **Datei** > **Neue Projektmappe**.</span><span class="sxs-lookup"><span data-stu-id="0d434-456">Select **File** > **New Solution**.</span></span>

  ![Neue Projektmappe in macOS](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="0d434-458">Navigieren Sie in Visual Studio für Mac-Versionen vor Version 8.6 zu **.NET Core** > **App** > **API** > **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="0d434-458">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="0d434-459">Bei Version 8.6 oder höher klicken Sie auf **Web and Console** > **App** > **API** > **Weiter** (Web und Konsole).</span><span class="sxs-lookup"><span data-stu-id="0d434-459">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>

  ![Auswählen von macOS-API-Vorlagen](first-web-api-mac/_static/api_template.png)

* <span data-ttu-id="0d434-461">Wählen Sie im Dialogfeld **Neue ASP.NET Core-Web-API konfigurieren** die neueste .NET Core 3.x-Version als **Zielframework** aus.</span><span class="sxs-lookup"><span data-stu-id="0d434-461">In the **Configure the new ASP.NET Core Web API** dialog, select the latest .NET Core 3.x **Target Framework**.</span></span> <span data-ttu-id="0d434-462">Klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="0d434-462">Select **Next**.</span></span>

* <span data-ttu-id="0d434-463">Geben Sie für **Projektname** *TodoApi* ein, und wählen Sie dann **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="0d434-463">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![Dialogfeld „Konfiguration“](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="0d434-465">Öffnen Sie im Projektordner ein Befehlsterminal, und führen Sie die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="0d434-465">Open a command terminal in the project folder and run the following commands:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-api"></a><span data-ttu-id="0d434-466">Testen der API</span><span class="sxs-lookup"><span data-stu-id="0d434-466">Test the API</span></span>

<span data-ttu-id="0d434-467">Die Projektvorlage erstellt eine `WeatherForecast`-API.</span><span class="sxs-lookup"><span data-stu-id="0d434-467">The project template creates a `WeatherForecast` API.</span></span> <span data-ttu-id="0d434-468">Rufen Sie in einem Browser die `Get`-Methode zum Testen der App auf.</span><span class="sxs-lookup"><span data-stu-id="0d434-468">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0d434-469">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0d434-469">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="0d434-470">Drücken Sie STRG+F5, um die App auszuführen.</span><span class="sxs-lookup"><span data-stu-id="0d434-470">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="0d434-471">Visual Studio startet einen Browser und navigiert zu `https://localhost:<port>/WeatherForecast`, wobei es sich bei `<port>` um eine zufällig ausgewählte Portnummer handelt.</span><span class="sxs-lookup"><span data-stu-id="0d434-471">Visual Studio launches a browser and navigates to `https://localhost:<port>/WeatherForecast`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="0d434-472">Wenn Sie in einem Dialogfeld angeben müssen, ob Sie dem IIS Express-Zertifikat vertrauen, wählen Sie **Ja** aus.</span><span class="sxs-lookup"><span data-stu-id="0d434-472">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="0d434-473">Wählen Sie im folgenden Dialogfeld **Sicherheitswarnung** die Option **Ja** aus.</span><span class="sxs-lookup"><span data-stu-id="0d434-473">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="0d434-474">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0d434-474">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="0d434-475">Drücken Sie STRG+F5, um die App auszuführen.</span><span class="sxs-lookup"><span data-stu-id="0d434-475">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="0d434-476">Navigieren Sie in einem Browser zur folgenden URL: `https://localhost:5001/WeatherForecast`.</span><span class="sxs-lookup"><span data-stu-id="0d434-476">In a browser, go to following URL: `https://localhost:5001/WeatherForecast`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0d434-477">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="0d434-477">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="0d434-478">Wählen Sie **Ausführen** > **Debugging starten** aus, um die App zu starten.</span><span class="sxs-lookup"><span data-stu-id="0d434-478">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="0d434-479">Visual Studio für Mac startet einen Browser und navigiert zu `https://localhost:<port>`, wobei es sich bei `<port>` um eine zufällig ausgewählte Portnummer handelt.</span><span class="sxs-lookup"><span data-stu-id="0d434-479">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="0d434-480">Der Fehler „HTTP 404: Nicht gefunden“ wird zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="0d434-480">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="0d434-481">Fügen Sie `/WeatherForecast` an die URL an, d. h., ändern Sie sie in `https://localhost:<port>/WeatherForecast`.</span><span class="sxs-lookup"><span data-stu-id="0d434-481">Append `/WeatherForecast` to the URL (change the URL to `https://localhost:<port>/WeatherForecast`).</span></span>

---

<span data-ttu-id="0d434-482">Der zurückgegebene JSON-Code sieht in etwa wie folgt aus:</span><span class="sxs-lookup"><span data-stu-id="0d434-482">JSON similar to the following is returned:</span></span>

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

## <a name="add-a-model-class"></a><span data-ttu-id="0d434-483">Hinzufügen einer Modellklasse</span><span class="sxs-lookup"><span data-stu-id="0d434-483">Add a model class</span></span>

<span data-ttu-id="0d434-484">Ein *Modell* ist eine Gruppe von Klassen, die die Daten darstellen, die die App verwaltet.</span><span class="sxs-lookup"><span data-stu-id="0d434-484">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="0d434-485">Das Modell für diese App ist eine einzelne `TodoItem`-Klasse.</span><span class="sxs-lookup"><span data-stu-id="0d434-485">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0d434-486">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0d434-486">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0d434-487">Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt.</span><span class="sxs-lookup"><span data-stu-id="0d434-487">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="0d434-488">Klicken Sie auf **Hinzufügen** > **Neuer Ordner**.</span><span class="sxs-lookup"><span data-stu-id="0d434-488">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="0d434-489">Geben Sie dem Ordner den Namen *Models* .</span><span class="sxs-lookup"><span data-stu-id="0d434-489">Name the folder *Models*.</span></span>

* <span data-ttu-id="0d434-490">Klicken Sie mit der rechten Maustaste auf den Ordner *Models* , und wählen Sie **Hinzufügen** > **Klasse** aus.</span><span class="sxs-lookup"><span data-stu-id="0d434-490">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="0d434-491">Nennen Sie die Klasse *TodoItem*, und wählen Sie **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="0d434-491">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="0d434-492">Ersetzen Sie den Vorlagencode durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="0d434-492">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="0d434-493">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0d434-493">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="0d434-494">Fügen Sie einen Ordner mit dem Namen *Models* hinzu.</span><span class="sxs-lookup"><span data-stu-id="0d434-494">Add a folder named *Models*.</span></span>

* <span data-ttu-id="0d434-495">Fügen Sie dem Ordner *Models* mit dem folgenden Code eine `TodoItem`-Klasse hinzu:</span><span class="sxs-lookup"><span data-stu-id="0d434-495">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0d434-496">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="0d434-496">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="0d434-497">Klicken Sie mit der rechten Maustaste auf das Projekt.</span><span class="sxs-lookup"><span data-stu-id="0d434-497">Right-click the project.</span></span> <span data-ttu-id="0d434-498">Klicken Sie auf **Hinzufügen** > **Neuer Ordner**.</span><span class="sxs-lookup"><span data-stu-id="0d434-498">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="0d434-499">Geben Sie dem Ordner den Namen *Models* .</span><span class="sxs-lookup"><span data-stu-id="0d434-499">Name the folder *Models*.</span></span>

  ![Neuer Ordner](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="0d434-501">Klicken Sie mit der rechten Maustaste auf den Ordner *Models* , und wählen Sie **Hinzufügen** > **Neue Datei** > **Allgemein** > **Leere Klasse** aus.</span><span class="sxs-lookup"><span data-stu-id="0d434-501">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="0d434-502">Nennen Sie die Klasse *TodoItem*, und klicken Sie dann auf **Neu**.</span><span class="sxs-lookup"><span data-stu-id="0d434-502">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="0d434-503">Ersetzen Sie den Vorlagencode durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="0d434-503">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs?name=snippet)]

<span data-ttu-id="0d434-504">Die `Id`-Eigenschaft fungiert als eindeutiger Schlüssel in einer relationalen Datenbank.</span><span class="sxs-lookup"><span data-stu-id="0d434-504">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="0d434-505">Modellklassen können überall im Projekt platziert werden, doch gemäß der Konvention wird der Ordner *Models* verwendet.</span><span class="sxs-lookup"><span data-stu-id="0d434-505">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="0d434-506">Hinzufügen eines Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="0d434-506">Add a database context</span></span>

<span data-ttu-id="0d434-507">Der *Datenbankkontext* ist die Hauptklasse, die die Entity Framework-Funktionen für ein Datenmodell koordiniert.</span><span class="sxs-lookup"><span data-stu-id="0d434-507">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="0d434-508">Diese Klasse wird durch Ableiten von der `Microsoft.EntityFrameworkCore.DbContext`-Klasse erstellt.</span><span class="sxs-lookup"><span data-stu-id="0d434-508">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0d434-509">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0d434-509">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-nuget-packages"></a><span data-ttu-id="0d434-510">Hinzufügen von NuGet-Paketen</span><span class="sxs-lookup"><span data-stu-id="0d434-510">Add NuGet packages</span></span>

* <span data-ttu-id="0d434-511">Klicken Sie im Menü **Extras** auf **NuGet-Paket-Manager > NuGet-Pakete für Projektmappe verwalten...** .</span><span class="sxs-lookup"><span data-stu-id="0d434-511">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="0d434-512">Klicken Sie auf die Registerkarte **Durchsuchen**, und geben Sie dann **Microsoft.EntityFrameworkCore.SqlServer** in das Suchfeld ein.</span><span class="sxs-lookup"><span data-stu-id="0d434-512">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.SqlServer** in the search box.</span></span>
* <span data-ttu-id="0d434-513">Wählen Sie im linken Bereich **Microsoft.EntityFrameworkCore.SqlServer** aus.</span><span class="sxs-lookup"><span data-stu-id="0d434-513">Select **Microsoft.EntityFrameworkCore.SqlServer** in the left pane.</span></span>
* <span data-ttu-id="0d434-514">Aktivieren Sie das Kontrollkästchen **Projekt** im rechten Bereich, und klicken Sie dann auf **Installieren**.</span><span class="sxs-lookup"><span data-stu-id="0d434-514">Select the **Project** check box in the right pane and then select **Install**.</span></span>
* <span data-ttu-id="0d434-515">Verwenden Sie die Anweisungen oben zum Hinzuzufügen des **Microsoft.EntityFrameworkCore.InMemory**-NuGet-Pakets.</span><span class="sxs-lookup"><span data-stu-id="0d434-515">Use the preceding instructions to add the **Microsoft.EntityFrameworkCore.InMemory** NuGet package.</span></span>

![NuGet-Paket-Manager](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="0d434-517">Hinzufügen des TodoContext-Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="0d434-517">Add the TodoContext database context</span></span>

* <span data-ttu-id="0d434-518">Klicken Sie mit der rechten Maustaste auf den Ordner *Models* , und wählen Sie **Hinzufügen** > **Klasse** aus.</span><span class="sxs-lookup"><span data-stu-id="0d434-518">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="0d434-519">Nennen Sie die Klasse *TodoContext*, und klicken Sie auf **Hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="0d434-519">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="0d434-520">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="0d434-520">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="0d434-521">Fügen Sie eine `TodoContext`-Klasse zum Ordner *Models* hinzu.</span><span class="sxs-lookup"><span data-stu-id="0d434-521">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="0d434-522">Geben Sie den folgenden Code ein:</span><span class="sxs-lookup"><span data-stu-id="0d434-522">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="0d434-523">Registrieren des Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="0d434-523">Register the database context</span></span>

<span data-ttu-id="0d434-524">In ASP.NET Core müssen Dienste wie der Datenbankkontext mit dem Container [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) registriert werden.</span><span class="sxs-lookup"><span data-stu-id="0d434-524">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="0d434-525">Der Container stellt den Dienst für Controller bereit.</span><span class="sxs-lookup"><span data-stu-id="0d434-525">The container provides the service to controllers.</span></span>

<span data-ttu-id="0d434-526">Aktualisieren Sie *Startup.cs* mit dem folgenden hervorgehobenen Code:</span><span class="sxs-lookup"><span data-stu-id="0d434-526">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="0d434-527">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="0d434-527">The preceding code:</span></span>

* <span data-ttu-id="0d434-528">Entfernt nicht benötigte `using`-Deklarationen</span><span class="sxs-lookup"><span data-stu-id="0d434-528">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="0d434-529">Fügt dem Abhängigkeitscontainer den Datenbankkontext hinzu</span><span class="sxs-lookup"><span data-stu-id="0d434-529">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="0d434-530">Gibt an, dass der Datenbankkontext eine In-Memory Database verwendet</span><span class="sxs-lookup"><span data-stu-id="0d434-530">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="0d434-531">Erstellen eines Controllergerüsts</span><span class="sxs-lookup"><span data-stu-id="0d434-531">Scaffold a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0d434-532">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0d434-532">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0d434-533">Klicken Sie mit der rechten Maustaste auf den Ordner *Controller*.</span><span class="sxs-lookup"><span data-stu-id="0d434-533">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="0d434-534">Wählen Sie **Hinzufügen** > **Neues Gerüstelement** aus.</span><span class="sxs-lookup"><span data-stu-id="0d434-534">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="0d434-535">Klicken Sie auf **API-Controller mit Aktionen unter Verwendung von Entity Framework** und dann auf **Hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="0d434-535">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="0d434-536">Führen Sie im Dialogfeld **API-Controller mit Aktionen unter Verwendung von Entity Framework** folgende Schritte aus:</span><span class="sxs-lookup"><span data-stu-id="0d434-536">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="0d434-537">Wählen Sie in der **Modellklasse** das Element **TodoItem (TodoApi.Models)** aus.</span><span class="sxs-lookup"><span data-stu-id="0d434-537">Select **TodoItem (TodoApi.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="0d434-538">Wählen Sie in der **Datenkontextklasse** das Element **TodoContext (TodoApi.Models)** aus.</span><span class="sxs-lookup"><span data-stu-id="0d434-538">Select **TodoContext (TodoApi.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="0d434-539">Wählen Sie **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="0d434-539">Select **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="0d434-540">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="0d434-540">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="0d434-541">Führen Sie die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="0d434-541">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet tool update -g Dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="0d434-542">Die obenstehenden Befehle haben folgende Konsequenzen:</span><span class="sxs-lookup"><span data-stu-id="0d434-542">The preceding commands:</span></span>

* <span data-ttu-id="0d434-543">Die NuGet-Pakete für den Gerüstbau werden hinzufügt.</span><span class="sxs-lookup"><span data-stu-id="0d434-543">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="0d434-544">Die Gerüstbau-Engine (`dotnet-aspnet-codegenerator`) wird installiert.</span><span class="sxs-lookup"><span data-stu-id="0d434-544">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="0d434-545">Das Gerüst für `TodoItemsController` wird erstellt.</span><span class="sxs-lookup"><span data-stu-id="0d434-545">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="0d434-546">Der generierte Code hat folgende Auswirkungen:</span><span class="sxs-lookup"><span data-stu-id="0d434-546">The generated code:</span></span>

* <span data-ttu-id="0d434-547">Markiert die Klasse mit dem [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute)-Attribut.</span><span class="sxs-lookup"><span data-stu-id="0d434-547">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="0d434-548">Dieses Attribut gibt an, dass der Controller auf Web-API-Anforderungen reagiert.</span><span class="sxs-lookup"><span data-stu-id="0d434-548">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="0d434-549">Weitere Informationen zu bestimmten Verhaltensweisen, die das Attribut ermöglicht, finden Sie unter <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="0d434-549">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="0d434-550">Verwendet die Abhängigkeitsinjektion, um den Datenbankkontext (`TodoContext`) dem Controller hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="0d434-550">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="0d434-551">Der Datenbankkontext wird in den einzelnen [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete)-Methoden im Controller verwendet.</span><span class="sxs-lookup"><span data-stu-id="0d434-551">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<span data-ttu-id="0d434-552">Die ASP.NET Core-Vorlagen für:</span><span class="sxs-lookup"><span data-stu-id="0d434-552">The ASP.NET Core templates for:</span></span>

* <span data-ttu-id="0d434-553">Controller mit Ansichten enthalten `[action]` in der Routenvorlage.</span><span class="sxs-lookup"><span data-stu-id="0d434-553">Controllers with views include `[action]` in the route template.</span></span>
* <span data-ttu-id="0d434-554">API-Controller enthalten keine `[action]` in der Routenvorlage.</span><span class="sxs-lookup"><span data-stu-id="0d434-554">API controllers don't include `[action]` in the route template.</span></span>

<span data-ttu-id="0d434-555">Wenn sich das `[action]`-Token nicht in der Routenvorlage befindet, wird der [action](xref:mvc/controllers/routing#action)-Name von der Route ausgeschlossen.</span><span class="sxs-lookup"><span data-stu-id="0d434-555">When the `[action]` token isn't in the route template, the [action](xref:mvc/controllers/routing#action) name is excluded from the route.</span></span> <span data-ttu-id="0d434-556">Dies bedeutet, dass der zugehörige Methodenname der Aktion nicht in der übereinstimmenden Route verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="0d434-556">That is, the action's associated method name isn't used in the matching route.</span></span>

## <a name="examine-the-posttodoitem-create-method"></a><span data-ttu-id="0d434-557">Überblick über die PostTodoItem-Erstellungsmethode</span><span class="sxs-lookup"><span data-stu-id="0d434-557">Examine the PostTodoItem create method</span></span>

<span data-ttu-id="0d434-558">Ersetzen Sie die Rückgabeanweisung in `PostTodoItem` durch eine Anweisung mit dem [nameof](/dotnet/csharp/language-reference/operators/nameof)-Operator:</span><span class="sxs-lookup"><span data-stu-id="0d434-558">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="0d434-559">Der oben stehende Code ist eine HTTP POST-Methode, wie durch das [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute)-Attribut angegeben.</span><span class="sxs-lookup"><span data-stu-id="0d434-559">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="0d434-560">Die Methode ruft den Wert der Aufgabe aus dem Text der HTTP-Anforderung ab.</span><span class="sxs-lookup"><span data-stu-id="0d434-560">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="0d434-561">Weitere Informationen finden Sie unter [Attributrouting mit Http[Verb]-Attributen](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="0d434-561">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="0d434-562">Die <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*>-Methode:</span><span class="sxs-lookup"><span data-stu-id="0d434-562">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="0d434-563">Gibt den HTTP-Statuscode 201 zurück, wenn der Vorgang erfolgreich ist.</span><span class="sxs-lookup"><span data-stu-id="0d434-563">Returns an HTTP 201 status code if successful.</span></span> <span data-ttu-id="0d434-564">HTTP 201 ist die Standardantwort für eine HTTP POST-Methode, die eine neue Ressource auf dem Server erstellt.</span><span class="sxs-lookup"><span data-stu-id="0d434-564">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="0d434-565">Fügt der Antwort einen [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location)-Header hinzu.</span><span class="sxs-lookup"><span data-stu-id="0d434-565">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="0d434-566">Der `Location`-Header legt den [URI](https://developer.mozilla.org/docs/Glossary/URI) des neu erstellten To-do-Elements fest.</span><span class="sxs-lookup"><span data-stu-id="0d434-566">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="0d434-567">Weitere Informationen finden Sie unter [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="0d434-567">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="0d434-568">Verweist auf die `GetTodoItem`-Aktion zum Erstellen des URIs des `Location`-Headers.</span><span class="sxs-lookup"><span data-stu-id="0d434-568">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="0d434-569">Das `nameof`-Schlüsselwort von C# wird verwendet, um eine Hartcodierung des Aktionsnamens im `CreatedAtAction`-Aufruf zu vermeiden.</span><span class="sxs-lookup"><span data-stu-id="0d434-569">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="0d434-570">Installieren von Postman</span><span class="sxs-lookup"><span data-stu-id="0d434-570">Install Postman</span></span>

<span data-ttu-id="0d434-571">Dieses Tutorial verwendet Postman zum Testen der Web-API.</span><span class="sxs-lookup"><span data-stu-id="0d434-571">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="0d434-572">Installieren Sie [Postman](https://www.getpostman.com/downloads/).</span><span class="sxs-lookup"><span data-stu-id="0d434-572">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="0d434-573">Starten Sie die Web-App.</span><span class="sxs-lookup"><span data-stu-id="0d434-573">Start the web app.</span></span>
* <span data-ttu-id="0d434-574">Starten Sie Postman.</span><span class="sxs-lookup"><span data-stu-id="0d434-574">Start Postman.</span></span>
* <span data-ttu-id="0d434-575">Deaktivieren Sie **SSL certificate verification** (Verifizierung des SSL-Zertifikats).</span><span class="sxs-lookup"><span data-stu-id="0d434-575">Disable **SSL certificate verification**</span></span>
  * <span data-ttu-id="0d434-576">Deaktivieren Sie auf der Registerkarte **General** (Allgemein) unter File > Settings (**Datei** > **Einstellungen**) **SSL certificate verification** (Verifizierung des SSL-Zertifikats).</span><span class="sxs-lookup"><span data-stu-id="0d434-576">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="0d434-577">Aktivieren Sie die Verifizierung des SSL-Zertifikats wieder, nachdem Sie den Controller getestet haben.</span><span class="sxs-lookup"><span data-stu-id="0d434-577">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="0d434-578">Testen von PostTodoItem mit Postman</span><span class="sxs-lookup"><span data-stu-id="0d434-578">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="0d434-579">Erstellen Sie eine neue Anforderung.</span><span class="sxs-lookup"><span data-stu-id="0d434-579">Create a new request.</span></span>
* <span data-ttu-id="0d434-580">Legen Sie als HTTP-Methode `POST` fest.</span><span class="sxs-lookup"><span data-stu-id="0d434-580">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="0d434-581">Legen Sie den URI auf `https://localhost:<port>/api/TodoItems` fest.</span><span class="sxs-lookup"><span data-stu-id="0d434-581">Set the URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="0d434-582">Beispiel: `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="0d434-582">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="0d434-583">Wählen Sie die Registerkarte **Body** (Text) aus.</span><span class="sxs-lookup"><span data-stu-id="0d434-583">Select the **Body** tab.</span></span>
* <span data-ttu-id="0d434-584">Wählen Sie das Optionsfeld **raw** (Unformatiert) aus.</span><span class="sxs-lookup"><span data-stu-id="0d434-584">Select the **raw** radio button.</span></span>
* <span data-ttu-id="0d434-585">Legen Sie den Typ auf **JSON (application/json)** fest.</span><span class="sxs-lookup"><span data-stu-id="0d434-585">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="0d434-586">Geben Sie für die Aufgabe den Anforderungstext im JSON-Format ein:</span><span class="sxs-lookup"><span data-stu-id="0d434-586">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="0d434-587">Wählen Sie **Send** (Senden) aus.</span><span class="sxs-lookup"><span data-stu-id="0d434-587">Select **Send**.</span></span>

  ![Postman mit erstellter Anforderung](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri-with-postman"></a><span data-ttu-id="0d434-589">Testen des Adressheader-URIs mit Postman</span><span class="sxs-lookup"><span data-stu-id="0d434-589">Test the location header URI with Postman</span></span>

* <span data-ttu-id="0d434-590">Wählen Sie auf der Registerkarte **Header** (Header) den Bereich **Response** (Antwort) aus.</span><span class="sxs-lookup"><span data-stu-id="0d434-590">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="0d434-591">Kopieren Sie den den Headerwert von **Location** (Speicherort):</span><span class="sxs-lookup"><span data-stu-id="0d434-591">Copy the **Location** header value:</span></span>

  ![Registerkarte „Header“ in der Postman-Konsole](first-web-api/_static/3/create.png)

* <span data-ttu-id="0d434-593">Legen Sie als HTTP-Methode `GET` fest.</span><span class="sxs-lookup"><span data-stu-id="0d434-593">Set the HTTP method to `GET`.</span></span>
* <span data-ttu-id="0d434-594">Legen Sie den URI auf `https://localhost:<port>/api/TodoItems/1` fest.</span><span class="sxs-lookup"><span data-stu-id="0d434-594">Set the URI to `https://localhost:<port>/api/TodoItems/1`.</span></span> <span data-ttu-id="0d434-595">Beispiel: `https://localhost:5001/api/TodoItems/1`.</span><span class="sxs-lookup"><span data-stu-id="0d434-595">For example, `https://localhost:5001/api/TodoItems/1`.</span></span>
* <span data-ttu-id="0d434-596">Wählen Sie **Send** (Senden) aus.</span><span class="sxs-lookup"><span data-stu-id="0d434-596">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="0d434-597">Überblick über die GET-Methoden</span><span class="sxs-lookup"><span data-stu-id="0d434-597">Examine the GET methods</span></span>

<span data-ttu-id="0d434-598">Diese Methoden implementieren zwei GET-Endpunkte:</span><span class="sxs-lookup"><span data-stu-id="0d434-598">These methods implement two GET endpoints:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="0d434-599">Testen Sie die App, indem Sie die beiden Endpunkte in einem Browser oder über Postman aufrufen.</span><span class="sxs-lookup"><span data-stu-id="0d434-599">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="0d434-600">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="0d434-600">For example:</span></span>

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

<span data-ttu-id="0d434-601">Durch einen Aufruf von `GetTodoItems` wird eine Antwort gesendet, die der folgenden ähnelt:</span><span class="sxs-lookup"><span data-stu-id="0d434-601">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="0d434-602">Testen von GET mit Postman</span><span class="sxs-lookup"><span data-stu-id="0d434-602">Test Get with Postman</span></span>

* <span data-ttu-id="0d434-603">Erstellen Sie eine neue Anforderung.</span><span class="sxs-lookup"><span data-stu-id="0d434-603">Create a new request.</span></span>
* <span data-ttu-id="0d434-604">Legen Sie die HTTP-Methode auf **GET** fest.</span><span class="sxs-lookup"><span data-stu-id="0d434-604">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="0d434-605">Legen Sie den Anforderungs-URI auf `https://localhost:<port>/api/TodoItems` fest.</span><span class="sxs-lookup"><span data-stu-id="0d434-605">Set the request URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="0d434-606">Beispielsweise `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="0d434-606">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="0d434-607">Wählen Sie in Postman **Two pane view** (Ansicht mit zwei Bereichen) aus.</span><span class="sxs-lookup"><span data-stu-id="0d434-607">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="0d434-608">Wählen Sie **Send** (Senden) aus.</span><span class="sxs-lookup"><span data-stu-id="0d434-608">Select **Send**.</span></span>

<span data-ttu-id="0d434-609">Diese App verwendet eine In-Memory-Datenbank.</span><span class="sxs-lookup"><span data-stu-id="0d434-609">This app uses an in-memory database.</span></span> <span data-ttu-id="0d434-610">Wenn die App angehalten und dann wieder gestartet wird, werden durch die vorherige GET-Anforderung keine Daten zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="0d434-610">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="0d434-611">Wenn keine Daten zurückgegeben werden, senden Sie mit [POST](#post) Daten an die App.</span><span class="sxs-lookup"><span data-stu-id="0d434-611">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="0d434-612">Routing und URL-Pfade</span><span class="sxs-lookup"><span data-stu-id="0d434-612">Routing and URL paths</span></span>

<span data-ttu-id="0d434-613">Das Attribut [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) gibt eine Methode an, die auf eine HTTP GET-Anforderung antwortet.</span><span class="sxs-lookup"><span data-stu-id="0d434-613">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="0d434-614">Der URL-Pfad für jede Methode wird wie folgt erstellt:</span><span class="sxs-lookup"><span data-stu-id="0d434-614">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="0d434-615">Beginnen Sie mit der Vorlagenzeichenfolge im `Route`-Attribut des Controllers:</span><span class="sxs-lookup"><span data-stu-id="0d434-615">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="0d434-616">Ersetzen Sie `[controller]` durch den Namen des Controllers, bei dem es sich standardmäßig um den Namen der Controller-Klasse ohne das Suffix „Controller“ handelt.</span><span class="sxs-lookup"><span data-stu-id="0d434-616">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="0d434-617">In diesem Beispiel ist der Klassenname des Controllers „**TodoItems**Controller“. Der Controllername lautet also „TodoItems“.</span><span class="sxs-lookup"><span data-stu-id="0d434-617">For this sample, the controller class name is **TodoItems**Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="0d434-618">Beim ASP.NET Core-[Routing](xref:mvc/controllers/routing) wird die Groß-/Kleinschreibung nicht beachtet.</span><span class="sxs-lookup"><span data-stu-id="0d434-618">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="0d434-619">Wenn das `[HttpGet]`-Attribut eine Routenvorlage (z. B. `[HttpGet("products")]`) hat, fügen Sie diese an den Pfad an.</span><span class="sxs-lookup"><span data-stu-id="0d434-619">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="0d434-620">In diesem Beispiel wird keine Vorlage verwendet.</span><span class="sxs-lookup"><span data-stu-id="0d434-620">This sample doesn't use a template.</span></span> <span data-ttu-id="0d434-621">Weitere Informationen finden Sie unter [Attributrouting mit Http[Verb]-Attributen](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="0d434-621">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="0d434-622">In der folgenden `GetTodoItem`-Methode ist `"{id}"` eine Platzhaltervariable für den eindeutigen Bezeichner des To-do-Elements.</span><span class="sxs-lookup"><span data-stu-id="0d434-622">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="0d434-623">Wenn `GetTodoItem` aufgerufen wird, wird der Wert von `"{id}"` in der URL der Methode in ihrem Parameter `id` bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="0d434-623">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="0d434-624">Rückgabewerte</span><span class="sxs-lookup"><span data-stu-id="0d434-624">Return values</span></span> 

<span data-ttu-id="0d434-625">Der Rückgabetyp der Methoden `GetTodoItems` und `GetTodoItem` ist der [ActionResult\<T>-Typ](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="0d434-625">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="0d434-626">ASP.NET Core serialisiert automatisch das Objekt in [JSON](https://www.json.org/) und schreibt den JSON-Code in den Text der Antwortnachricht.</span><span class="sxs-lookup"><span data-stu-id="0d434-626">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="0d434-627">Der Antwortcode für diesen Rückgabetyp ist 200, vorausgesetzt, es gibt keine Ausnahmefehler.</span><span class="sxs-lookup"><span data-stu-id="0d434-627">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="0d434-628">Nicht behandelte Ausnahmen werden in 5xx-Fehler übersetzt.</span><span class="sxs-lookup"><span data-stu-id="0d434-628">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="0d434-629">`ActionResult`-Rückgabetypen können eine Vielzahl von HTTP-Statuscodes darstellen.</span><span class="sxs-lookup"><span data-stu-id="0d434-629">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="0d434-630">Beispielsweise kann `GetTodoItem` zwei verschiedene Statuswerte zurückgeben:</span><span class="sxs-lookup"><span data-stu-id="0d434-630">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="0d434-631">Wenn kein Element mit der angeforderten ID übereinstimmt, gibt die Methode einen 404-Fehlercode <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> zurück.</span><span class="sxs-lookup"><span data-stu-id="0d434-631">If no item matches the requested ID, the method returns a 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="0d434-632">Andernfalls gibt die Methode 200 mit einem JSON-Antworttext zurück.</span><span class="sxs-lookup"><span data-stu-id="0d434-632">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="0d434-633">Die Rückgabe von `item` löst eine HTTP 200-Antwort aus.</span><span class="sxs-lookup"><span data-stu-id="0d434-633">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="0d434-634">PutTodoItem-Methode</span><span class="sxs-lookup"><span data-stu-id="0d434-634">The PutTodoItem method</span></span>

<span data-ttu-id="0d434-635">Untersuchen Sie die `PutTodoItem`-Methode.</span><span class="sxs-lookup"><span data-stu-id="0d434-635">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="0d434-636">`PutTodoItem` ähnelt `PostTodoItem`, verwendet allerdings HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="0d434-636">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="0d434-637">Die Antwort ist [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="0d434-637">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="0d434-638">Gemäß der HTTP-Spezifikation erfordert eine PUT-Anforderung, dass der Client die gesamte aktualisierte Entität (nicht nur die Änderungen) sendet.</span><span class="sxs-lookup"><span data-stu-id="0d434-638">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="0d434-639">Verwenden Sie [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute), um Teilupdates zu unterstützen.</span><span class="sxs-lookup"><span data-stu-id="0d434-639">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="0d434-640">Wenn beim Aufrufen von `PutTodoItem` ein Fehler zurückgegeben wird, rufen Sie `GET` auf, um sicherzustellen, dass die Datenbank ein Element enthält.</span><span class="sxs-lookup"><span data-stu-id="0d434-640">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="0d434-641">Testen der PutTodoItem-Methode</span><span class="sxs-lookup"><span data-stu-id="0d434-641">Test the PutTodoItem method</span></span>

<span data-ttu-id="0d434-642">In diesem Beispiel wird eine In-Memory-Datenbank verwendet, die jedes Mal initialisiert werden muss, wenn die App gestartet wird.</span><span class="sxs-lookup"><span data-stu-id="0d434-642">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="0d434-643">Es muss ein Element in der Datenbank vorhanden sein, bevor Sie einen PUT-Aufruf durchführen.</span><span class="sxs-lookup"><span data-stu-id="0d434-643">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="0d434-644">Rufen Sie vor einem PUT-Aufruf GET auf, um sicherzustellen, dass ein Element in der Datenbank vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="0d434-644">Call GET to ensure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="0d434-645">Aktualisieren Sie das To-do-Element, das über den ID-Wert 1 verfügt, und legen Sie als Namen „feed fish“ fest:</span><span class="sxs-lookup"><span data-stu-id="0d434-645">Update the to-do item that has Id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "id":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="0d434-646">Die folgende Abbildung zeigt das Postman-Update:</span><span class="sxs-lookup"><span data-stu-id="0d434-646">The following image shows the Postman update:</span></span>

![Postman-Konsole mit der Antwort „204 (Kein Inhalt)“](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="0d434-648">DeleteTodoItem-Methode</span><span class="sxs-lookup"><span data-stu-id="0d434-648">The DeleteTodoItem method</span></span>

<span data-ttu-id="0d434-649">Untersuchen Sie die `DeleteTodoItem`-Methode.</span><span class="sxs-lookup"><span data-stu-id="0d434-649">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="0d434-650">Testen der DeleteTodoItem-Methode</span><span class="sxs-lookup"><span data-stu-id="0d434-650">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="0d434-651">So löschen Sie mit Postman eine Aufgabe</span><span class="sxs-lookup"><span data-stu-id="0d434-651">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="0d434-652">Legen Sie die Methode auf `DELETE` fest.</span><span class="sxs-lookup"><span data-stu-id="0d434-652">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="0d434-653">Legen Sie den URI des zu löschenden Objekts fest, z. B. `https://localhost:5001/api/TodoItems/1`.</span><span class="sxs-lookup"><span data-stu-id="0d434-653">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="0d434-654">Wählen Sie **Send** (Senden) aus.</span><span class="sxs-lookup"><span data-stu-id="0d434-654">Select **Send**.</span></span>

<a name="over-post"></a>
<a name="over-post-v3"></a>

## <a name="prevent-over-posting"></a><span data-ttu-id="0d434-655">Vermeiden von Overposting</span><span class="sxs-lookup"><span data-stu-id="0d434-655">Prevent over-posting</span></span>

<span data-ttu-id="0d434-656">Derzeit macht die Beispiel-App das gesamte `TodoItem`-Objekt verfügbar.</span><span class="sxs-lookup"><span data-stu-id="0d434-656">Currently the sample app exposes the entire `TodoItem` object.</span></span> <span data-ttu-id="0d434-657">In den Produktions-Apps sind die Daten, die eingegeben und mithilfe einer Teilmenge des Modells zurückgegeben werden, in der Regel eingeschränkt.</span><span class="sxs-lookup"><span data-stu-id="0d434-657">Production apps typically limit the data that's input and returned using a subset of the model.</span></span> <span data-ttu-id="0d434-658">Hierfür gibt es mehrere Gründe, wobei die Sicherheit einer der Hauptgründe ist.</span><span class="sxs-lookup"><span data-stu-id="0d434-658">There are multiple reasons behind this and security is a major one.</span></span> <span data-ttu-id="0d434-659">Die Teilmenge eines Modells wird üblicherweise als Datenübertragungsobjekt (DTO, Data Transfer Object), Eingabemodell oder Anzeigemodell bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="0d434-659">The subset of a model is usually referred to as a Data Transfer Object (DTO), input model, or view model.</span></span> <span data-ttu-id="0d434-660">In diesem Artikel wird das **DTO** verwendet.</span><span class="sxs-lookup"><span data-stu-id="0d434-660">**DTO** is used in this article.</span></span>

<span data-ttu-id="0d434-661">Ein DTO kann für Folgendes verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="0d434-661">A DTO may be used to:</span></span>

* <span data-ttu-id="0d434-662">Vermeiden Sie Overposting.</span><span class="sxs-lookup"><span data-stu-id="0d434-662">Prevent over-posting.</span></span>
* <span data-ttu-id="0d434-663">Ausblenden von Eigenschaften, die Clients nicht anzeigen sollen</span><span class="sxs-lookup"><span data-stu-id="0d434-663">Hide properties that clients are not supposed to view.</span></span>
* <span data-ttu-id="0d434-664">Auslassen einiger Eigenschaften, um die Nutzlast zu verringern</span><span class="sxs-lookup"><span data-stu-id="0d434-664">Omit some properties in order to reduce payload size.</span></span>
* <span data-ttu-id="0d434-665">Vereinfachen von Objektgraphen, die geschachtelte Objekte enthalten</span><span class="sxs-lookup"><span data-stu-id="0d434-665">Flatten object graphs that contain nested objects.</span></span> <span data-ttu-id="0d434-666">Vereinfachte Objektgraphen können für Clients zweckmäßiger sein.</span><span class="sxs-lookup"><span data-stu-id="0d434-666">Flattened object graphs can be more convenient for clients.</span></span>

<span data-ttu-id="0d434-667">Um den DTO-Ansatz zu veranschaulichen, aktualisieren Sie die `TodoItem`-Klasse, sodass sie ein geheimes Feld einschließt:</span><span class="sxs-lookup"><span data-stu-id="0d434-667">To demonstrate the DTO approach, update the `TodoItem` class to include a secret field:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItem.cs?name=snippet&highlight=6)]

<span data-ttu-id="0d434-668">Das geheime Feld muss in dieser App ausgeblendet werden, eine administrative App kann es jedoch verfügbar machen.</span><span class="sxs-lookup"><span data-stu-id="0d434-668">The secret field needs to be hidden from this app, but an administrative app could choose to expose it.</span></span>

<span data-ttu-id="0d434-669">Vergewissern Sie sich, dass Sie das geheime Feld veröffentlichen und abrufen können.</span><span class="sxs-lookup"><span data-stu-id="0d434-669">Verify you can post and get the secret field.</span></span>

<span data-ttu-id="0d434-670">Erstellen Sie ein DTO-Modell:</span><span class="sxs-lookup"><span data-stu-id="0d434-670">Create a DTO model:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItemDTO.cs?name=snippet)]

<span data-ttu-id="0d434-671">Aktualisieren Sie `TodoItemsController`, sodass `TodoItemDTO` verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="0d434-671">Update the `TodoItemsController` to use `TodoItemDTO`:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

<span data-ttu-id="0d434-672">Vergewissern Sie sich, dass Sie das geheime Feld weder veröffentlichen noch abrufen können.</span><span class="sxs-lookup"><span data-stu-id="0d434-672">Verify you can't post or get the secret field.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="0d434-673">Aufrufen der Web-API mit JavaScript</span><span class="sxs-lookup"><span data-stu-id="0d434-673">Call the web API with JavaScript</span></span>

<span data-ttu-id="0d434-674">Mehr dazu finden Sie im [-Tutorial: Aufrufen einer ASP.NET Core-Web-API mit JavaScript](xref:tutorials/web-api-javascript).</span><span class="sxs-lookup"><span data-stu-id="0d434-674">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="0d434-675">In diesem Tutorial lernen Sie, wie die folgenden Aufgaben ausgeführt werden:</span><span class="sxs-lookup"><span data-stu-id="0d434-675">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="0d434-676">Erstellen eines Web-API-Projekts</span><span class="sxs-lookup"><span data-stu-id="0d434-676">Create a web API project.</span></span>
> * <span data-ttu-id="0d434-677">Hinzufügen einer Modellklasse und eines Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="0d434-677">Add a model class and a database context.</span></span>
> * <span data-ttu-id="0d434-678">Hinzufügen eines Controllers</span><span class="sxs-lookup"><span data-stu-id="0d434-678">Add a controller.</span></span>
> * <span data-ttu-id="0d434-679">Hinzufügen von CRUD-Methoden</span><span class="sxs-lookup"><span data-stu-id="0d434-679">Add CRUD methods.</span></span>
> * <span data-ttu-id="0d434-680">Konfigurieren von Routing und URL-Pfaden</span><span class="sxs-lookup"><span data-stu-id="0d434-680">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="0d434-681">Angeben von Rückgabewerten</span><span class="sxs-lookup"><span data-stu-id="0d434-681">Specify return values.</span></span>
> * <span data-ttu-id="0d434-682">Aufrufen der Web-API mit Postman</span><span class="sxs-lookup"><span data-stu-id="0d434-682">Call the web API with Postman.</span></span>
> * <span data-ttu-id="0d434-683">Aufrufen der Web-API mit JavaScript</span><span class="sxs-lookup"><span data-stu-id="0d434-683">Call the web API with JavaScript.</span></span>

<span data-ttu-id="0d434-684">Am Ende haben Sie eine Web-API, die in einer relationalen Datenbank gespeicherte „Aufgaben“ verwalten kann.</span><span class="sxs-lookup"><span data-stu-id="0d434-684">At the end, you have a web API that can manage "to-do" items stored in a relational database.</span></span>

## <a name="overview-21"></a><span data-ttu-id="0d434-685">Übersicht 2.1</span><span class="sxs-lookup"><span data-stu-id="0d434-685">Overview 2.1</span></span>

<span data-ttu-id="0d434-686">In diesem Tutorial wird die folgende API erstellt:</span><span class="sxs-lookup"><span data-stu-id="0d434-686">This tutorial creates the following API:</span></span>

|<span data-ttu-id="0d434-687">API</span><span class="sxs-lookup"><span data-stu-id="0d434-687">API</span></span> | <span data-ttu-id="0d434-688">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="0d434-688">Description</span></span> | <span data-ttu-id="0d434-689">Anforderungstext</span><span class="sxs-lookup"><span data-stu-id="0d434-689">Request body</span></span> | <span data-ttu-id="0d434-690">Antworttext</span><span class="sxs-lookup"><span data-stu-id="0d434-690">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="0d434-691">GET /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="0d434-691">GET /api/TodoItems</span></span> | <span data-ttu-id="0d434-692">Alle To-do-Elemente abrufen</span><span class="sxs-lookup"><span data-stu-id="0d434-692">Get all to-do items</span></span> | <span data-ttu-id="0d434-693">Keine</span><span class="sxs-lookup"><span data-stu-id="0d434-693">None</span></span> | <span data-ttu-id="0d434-694">Array von To-do-Elementen</span><span class="sxs-lookup"><span data-stu-id="0d434-694">Array of to-do items</span></span>|
|<span data-ttu-id="0d434-695">GET /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="0d434-695">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="0d434-696">Ein Element nach ID abrufen</span><span class="sxs-lookup"><span data-stu-id="0d434-696">Get an item by ID</span></span> | <span data-ttu-id="0d434-697">Keine</span><span class="sxs-lookup"><span data-stu-id="0d434-697">None</span></span> | <span data-ttu-id="0d434-698">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="0d434-698">To-do item</span></span>|
|<span data-ttu-id="0d434-699">POST /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="0d434-699">POST /api/TodoItems</span></span> | <span data-ttu-id="0d434-700">Neues Element hinzufügen</span><span class="sxs-lookup"><span data-stu-id="0d434-700">Add a new item</span></span> | <span data-ttu-id="0d434-701">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="0d434-701">To-do item</span></span> | <span data-ttu-id="0d434-702">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="0d434-702">To-do item</span></span> |
|<span data-ttu-id="0d434-703">PUT /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="0d434-703">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="0d434-704">Vorhandenes Element aktualisieren &nbsp;</span><span class="sxs-lookup"><span data-stu-id="0d434-704">Update an existing item &nbsp;</span></span> | <span data-ttu-id="0d434-705">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="0d434-705">To-do item</span></span> | <span data-ttu-id="0d434-706">Keine</span><span class="sxs-lookup"><span data-stu-id="0d434-706">None</span></span> |
|<span data-ttu-id="0d434-707">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="0d434-707">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="0d434-708">Löschen eines Elements &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="0d434-708">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="0d434-709">Keine</span><span class="sxs-lookup"><span data-stu-id="0d434-709">None</span></span> | <span data-ttu-id="0d434-710">Keine</span><span class="sxs-lookup"><span data-stu-id="0d434-710">None</span></span>|

<span data-ttu-id="0d434-711">Das folgende Diagramm zeigt den Entwurf der App.</span><span class="sxs-lookup"><span data-stu-id="0d434-711">The following diagram shows the design of the app.</span></span>

![Der Client ist das Feld ganz links.](first-web-api/_static/architecture.png)

## <a name="prerequisites-21"></a><span data-ttu-id="0d434-717">Voraussetzungen 2.1</span><span class="sxs-lookup"><span data-stu-id="0d434-717">Prerequisites 2.1</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0d434-718">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0d434-718">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="0d434-719">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0d434-719">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0d434-720">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="0d434-720">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project-21"></a><span data-ttu-id="0d434-721">Erstellen eines Webprojekts 2.1</span><span class="sxs-lookup"><span data-stu-id="0d434-721">Create a web project 2.1</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0d434-722">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0d434-722">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0d434-723">Klicken Sie im Menü **Datei** auf **Neu** > **Projekt**.</span><span class="sxs-lookup"><span data-stu-id="0d434-723">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="0d434-724">Wählen Sie die Vorlage **ASP.NET Core-Webanwendung** aus, und klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="0d434-724">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="0d434-725">Geben Sie dem Projekt den Namen *TodoApi*, und klicken Sie auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="0d434-725">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="0d434-726">Vergewissern Sie sich, dass im Dialogfeld **Neue ASP.NET Core-Webanwendung erstellen** die Optionen **.NET Core** und **ASP.NET Core 2.2** ausgewählt sind.</span><span class="sxs-lookup"><span data-stu-id="0d434-726">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 2.2** are selected.</span></span> <span data-ttu-id="0d434-727">Wählen Sie die Vorlage **API** aus, und klicken Sie auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="0d434-727">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="0d434-728">Wählen Sie **nicht** **Enable Docker Support** (Docker-Unterstützung aktivieren) aus.</span><span class="sxs-lookup"><span data-stu-id="0d434-728">**Don't** select **Enable Docker Support**.</span></span>

![VS-Dialogfeld „Neues Projekt“](first-web-api/_static/vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="0d434-730">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0d434-730">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="0d434-731">Öffnen Sie das [integrierte Terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="0d434-731">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="0d434-732">Wechseln Sie mit dem Befehl `cd`' zu dem Ordner, der den Projektordner enthalten soll.</span><span class="sxs-lookup"><span data-stu-id="0d434-732">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="0d434-733">Führen Sie die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="0d434-733">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  <span data-ttu-id="0d434-734">Diese Befehle erstellen ein neues Web-API-Projekt und öffnen eine neue Visual Studio Code-Instanz im neuen Projektordner.</span><span class="sxs-lookup"><span data-stu-id="0d434-734">These commands create a new web API project and open a new instance of Visual Studio Code in the new project folder.</span></span>

* <span data-ttu-id="0d434-735">Wenn Sie in einem Dialogfeld angeben müssen, ob Sie dem Projekt die erforderlichen Elemente hinzufügen möchten, wählen Sie **Ja** aus.</span><span class="sxs-lookup"><span data-stu-id="0d434-735">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0d434-736">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="0d434-736">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="0d434-737">Klicken Sie auf **Datei** > **Neue Projektmappe**.</span><span class="sxs-lookup"><span data-stu-id="0d434-737">Select **File** > **New Solution**.</span></span>

  ![Neue Projektmappe in macOS](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="0d434-739">Navigieren Sie in Visual Studio für Mac-Versionen vor Version 8.6 zu **.NET Core** > **App** > **API** > **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="0d434-739">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="0d434-740">Bei Version 8.6 oder höher klicken Sie auf **Web and Console** > **App** > **API** > **Weiter** (Web und Konsole).</span><span class="sxs-lookup"><span data-stu-id="0d434-740">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>
  
* <span data-ttu-id="0d434-741">Wählen Sie im Dialogfeld **Neue ASP.NET Core-Web-API konfigurieren** die neueste .NET Core 2.x-Version als **Zielframework** aus.</span><span class="sxs-lookup"><span data-stu-id="0d434-741">In the **Configure the new ASP.NET Core Web API** dialog, select the latest .NET Core 2.x **Target Framework**.</span></span> <span data-ttu-id="0d434-742">Klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="0d434-742">Select **Next**.</span></span>

* <span data-ttu-id="0d434-743">Geben Sie für **Projektname** *TodoApi* ein, und wählen Sie dann **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="0d434-743">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![Dialogfeld „Konfiguration“](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api-21"></a><span data-ttu-id="0d434-745">Testen der API 2.1</span><span class="sxs-lookup"><span data-stu-id="0d434-745">Test the API 2.1</span></span>

<span data-ttu-id="0d434-746">Die Projektvorlage erstellt eine `values`-API.</span><span class="sxs-lookup"><span data-stu-id="0d434-746">The project template creates a `values` API.</span></span> <span data-ttu-id="0d434-747">Rufen Sie in einem Browser die `Get`-Methode zum Testen der App auf.</span><span class="sxs-lookup"><span data-stu-id="0d434-747">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0d434-748">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0d434-748">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="0d434-749">Drücken Sie STRG+F5, um die App auszuführen.</span><span class="sxs-lookup"><span data-stu-id="0d434-749">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="0d434-750">Visual Studio startet einen Browser und navigiert zu `https://localhost:<port>/api/values`, wobei es sich bei `<port>` um eine zufällig ausgewählte Portnummer handelt.</span><span class="sxs-lookup"><span data-stu-id="0d434-750">Visual Studio launches a browser and navigates to `https://localhost:<port>/api/values`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="0d434-751">Wenn Sie in einem Dialogfeld angeben müssen, ob Sie dem IIS Express-Zertifikat vertrauen, wählen Sie **Ja** aus.</span><span class="sxs-lookup"><span data-stu-id="0d434-751">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="0d434-752">Wählen Sie im folgenden Dialogfeld **Sicherheitswarnung** die Option **Ja** aus.</span><span class="sxs-lookup"><span data-stu-id="0d434-752">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="0d434-753">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0d434-753">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="0d434-754">Drücken Sie STRG+F5, um die App auszuführen.</span><span class="sxs-lookup"><span data-stu-id="0d434-754">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="0d434-755">Navigieren Sie in einem Browser zur folgenden URL: `https://localhost:5001/api/values`.</span><span class="sxs-lookup"><span data-stu-id="0d434-755">In a browser, go to following URL: `https://localhost:5001/api/values`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0d434-756">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="0d434-756">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="0d434-757">Wählen Sie **Ausführen** > **Debugging starten** aus, um die App zu starten.</span><span class="sxs-lookup"><span data-stu-id="0d434-757">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="0d434-758">Visual Studio für Mac startet einen Browser und navigiert zu `https://localhost:<port>`, wobei es sich bei `<port>` um eine zufällig ausgewählte Portnummer handelt.</span><span class="sxs-lookup"><span data-stu-id="0d434-758">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="0d434-759">Der Fehler „HTTP 404: Nicht gefunden“ wird zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="0d434-759">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="0d434-760">Fügen Sie der URL `/api/values` an, d.h. ändern Sie die URL in `https://localhost:<port>/api/values`.</span><span class="sxs-lookup"><span data-stu-id="0d434-760">Append `/api/values` to the URL (change the URL to `https://localhost:<port>/api/values`).</span></span>

---

<span data-ttu-id="0d434-761">Die folgende JSON-Datei wird zurückgegeben:</span><span class="sxs-lookup"><span data-stu-id="0d434-761">The following JSON is returned:</span></span>

```json
["value1","value2"]
```

## <a name="add-a-model-class-21"></a><span data-ttu-id="0d434-762">Hinzufügen einer Modellklasse 2.1</span><span class="sxs-lookup"><span data-stu-id="0d434-762">Add a model class 2.1</span></span>

<span data-ttu-id="0d434-763">Ein *Modell* ist eine Gruppe von Klassen, die die Daten darstellen, die die App verwaltet.</span><span class="sxs-lookup"><span data-stu-id="0d434-763">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="0d434-764">Das Modell für diese App ist eine einzelne `TodoItem`-Klasse.</span><span class="sxs-lookup"><span data-stu-id="0d434-764">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0d434-765">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0d434-765">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0d434-766">Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt.</span><span class="sxs-lookup"><span data-stu-id="0d434-766">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="0d434-767">Klicken Sie auf **Hinzufügen** > **Neuer Ordner**.</span><span class="sxs-lookup"><span data-stu-id="0d434-767">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="0d434-768">Geben Sie dem Ordner den Namen *Models* .</span><span class="sxs-lookup"><span data-stu-id="0d434-768">Name the folder *Models*.</span></span>

* <span data-ttu-id="0d434-769">Klicken Sie mit der rechten Maustaste auf den Ordner *Models* , und wählen Sie **Hinzufügen** > **Klasse** aus.</span><span class="sxs-lookup"><span data-stu-id="0d434-769">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="0d434-770">Nennen Sie die Klasse *TodoItem*, und wählen Sie **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="0d434-770">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="0d434-771">Ersetzen Sie den Vorlagencode durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="0d434-771">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="0d434-772">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0d434-772">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="0d434-773">Fügen Sie einen Ordner mit dem Namen *Models* hinzu.</span><span class="sxs-lookup"><span data-stu-id="0d434-773">Add a folder named *Models*.</span></span>

* <span data-ttu-id="0d434-774">Fügen Sie dem Ordner *Models* mit dem folgenden Code eine `TodoItem`-Klasse hinzu:</span><span class="sxs-lookup"><span data-stu-id="0d434-774">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0d434-775">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="0d434-775">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="0d434-776">Klicken Sie mit der rechten Maustaste auf das Projekt.</span><span class="sxs-lookup"><span data-stu-id="0d434-776">Right-click the project.</span></span> <span data-ttu-id="0d434-777">Klicken Sie auf **Hinzufügen** > **Neuer Ordner**.</span><span class="sxs-lookup"><span data-stu-id="0d434-777">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="0d434-778">Geben Sie dem Ordner den Namen *Models* .</span><span class="sxs-lookup"><span data-stu-id="0d434-778">Name the folder *Models*.</span></span>

  ![Neuer Ordner](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="0d434-780">Klicken Sie mit der rechten Maustaste auf den Ordner *Models* , und wählen Sie **Hinzufügen** > **Neue Datei** > **Allgemein** > **Leere Klasse** aus.</span><span class="sxs-lookup"><span data-stu-id="0d434-780">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="0d434-781">Nennen Sie die Klasse *TodoItem*, und klicken Sie dann auf **Neu**.</span><span class="sxs-lookup"><span data-stu-id="0d434-781">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="0d434-782">Ersetzen Sie den Vorlagencode durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="0d434-782">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="0d434-783">Die `Id`-Eigenschaft fungiert als eindeutiger Schlüssel in einer relationalen Datenbank.</span><span class="sxs-lookup"><span data-stu-id="0d434-783">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="0d434-784">Modellklassen können überall im Projekt platziert werden, doch gemäß der Konvention wird der Ordner *Models* verwendet.</span><span class="sxs-lookup"><span data-stu-id="0d434-784">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context-21"></a><span data-ttu-id="0d434-785">Hinzufügen eines Datenbankkontexts 2.1</span><span class="sxs-lookup"><span data-stu-id="0d434-785">Add a database context 2.1</span></span>

<span data-ttu-id="0d434-786">Der *Datenbankkontext* ist die Hauptklasse, die die Entity Framework-Funktionen für ein Datenmodell koordiniert.</span><span class="sxs-lookup"><span data-stu-id="0d434-786">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="0d434-787">Diese Klasse wird durch Ableiten von der `Microsoft.EntityFrameworkCore.DbContext`-Klasse erstellt.</span><span class="sxs-lookup"><span data-stu-id="0d434-787">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0d434-788">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0d434-788">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0d434-789">Klicken Sie mit der rechten Maustaste auf den Ordner *Models* , und wählen Sie **Hinzufügen** > **Klasse** aus.</span><span class="sxs-lookup"><span data-stu-id="0d434-789">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="0d434-790">Nennen Sie die Klasse *TodoContext*, und klicken Sie auf **Hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="0d434-790">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="0d434-791">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="0d434-791">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="0d434-792">Fügen Sie eine `TodoContext`-Klasse zum Ordner *Models* hinzu.</span><span class="sxs-lookup"><span data-stu-id="0d434-792">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="0d434-793">Ersetzen Sie den Vorlagencode durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="0d434-793">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context-21"></a><span data-ttu-id="0d434-794">Registrieren des Datenbankkontexts 2.1</span><span class="sxs-lookup"><span data-stu-id="0d434-794">Register the database context 2.1</span></span>

<span data-ttu-id="0d434-795">In ASP.NET Core müssen Dienste wie der Datenbankkontext mit dem Container [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) registriert werden.</span><span class="sxs-lookup"><span data-stu-id="0d434-795">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="0d434-796">Der Container stellt den Dienst für Controller bereit.</span><span class="sxs-lookup"><span data-stu-id="0d434-796">The container provides the service to controllers.</span></span>

<span data-ttu-id="0d434-797">Aktualisieren Sie *Startup.cs* mit dem folgenden hervorgehobenen Code:</span><span class="sxs-lookup"><span data-stu-id="0d434-797">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

<span data-ttu-id="0d434-798">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="0d434-798">The preceding code:</span></span>

* <span data-ttu-id="0d434-799">Entfernt nicht benötigte `using`-Deklarationen</span><span class="sxs-lookup"><span data-stu-id="0d434-799">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="0d434-800">Fügt dem Abhängigkeitscontainer den Datenbankkontext hinzu</span><span class="sxs-lookup"><span data-stu-id="0d434-800">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="0d434-801">Gibt an, dass der Datenbankkontext eine In-Memory Database verwendet</span><span class="sxs-lookup"><span data-stu-id="0d434-801">Specifies that the database context will use an in-memory database.</span></span>

## <a name="add-a-controller-21"></a><span data-ttu-id="0d434-802">Hinzufügen eines Controllers 2.1</span><span class="sxs-lookup"><span data-stu-id="0d434-802">Add a controller 2.1</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0d434-803">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0d434-803">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0d434-804">Klicken Sie mit der rechten Maustaste auf den Ordner *Controller*.</span><span class="sxs-lookup"><span data-stu-id="0d434-804">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="0d434-805">Wählen Sie **Hinzufügen** > **Neues Element** aus.</span><span class="sxs-lookup"><span data-stu-id="0d434-805">Select **Add** > **New Item**.</span></span>
* <span data-ttu-id="0d434-806">Wählen Sie im Dialogfeld **Neues Element hinzufügen** die Vorlage **API-Controllerklasse** aus.</span><span class="sxs-lookup"><span data-stu-id="0d434-806">In the **Add New Item** dialog, select the **API Controller Class** template.</span></span>
* <span data-ttu-id="0d434-807">Benennen Sie die Klasse *TodoController*, und wählen Sie **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="0d434-807">Name the class *TodoController*, and select **Add**.</span></span>

  ![Dialogfeld „Neues Element hinzufügen“ mit Controller im Suchfeld und ausgewähltem Web-API-Controller](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="0d434-809">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="0d434-809">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="0d434-810">Erstellen Sie im Ordner *Controllers* eine Klasse namens `TodoController`.</span><span class="sxs-lookup"><span data-stu-id="0d434-810">In the *Controllers* folder, create a class named `TodoController`.</span></span>

---

* <span data-ttu-id="0d434-811">Ersetzen Sie den Vorlagencode durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="0d434-811">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

<span data-ttu-id="0d434-812">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="0d434-812">The preceding code:</span></span>

* <span data-ttu-id="0d434-813">Definiert eine API-Controllerklasse ohne Methoden.</span><span class="sxs-lookup"><span data-stu-id="0d434-813">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="0d434-814">Markiert die Klasse mit dem [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute)-Attribut.</span><span class="sxs-lookup"><span data-stu-id="0d434-814">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="0d434-815">Dieses Attribut gibt an, dass der Controller auf Web-API-Anforderungen reagiert.</span><span class="sxs-lookup"><span data-stu-id="0d434-815">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="0d434-816">Weitere Informationen zu bestimmten Verhaltensweisen, die das Attribut ermöglicht, finden Sie unter <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="0d434-816">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="0d434-817">Verwendet die Abhängigkeitsinjektion, um den Datenbankkontext (`TodoContext`) dem Controller hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="0d434-817">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="0d434-818">Der Datenbankkontext wird in den einzelnen [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete)-Methoden im Controller verwendet.</span><span class="sxs-lookup"><span data-stu-id="0d434-818">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>
* <span data-ttu-id="0d434-819">Fügt der Datenbank, falls sie leer ist, ein Element mit dem Namen `Item1` hinzu.</span><span class="sxs-lookup"><span data-stu-id="0d434-819">Adds an item named `Item1` to the database if the database is empty.</span></span> <span data-ttu-id="0d434-820">Dieser Code befindet sich im Konstruktor. Er wird bei jeder neuen HTTP-Anforderung ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="0d434-820">This code is in the constructor, so it runs every time there's a new HTTP request.</span></span> <span data-ttu-id="0d434-821">Wenn Sie alle Elemente löschen, erstellt der Konstruktor beim nächsten Aufruf einer API-Methode erneut `Item1`.</span><span class="sxs-lookup"><span data-stu-id="0d434-821">If you delete all items, the constructor creates `Item1` again the next time an API method is called.</span></span> <span data-ttu-id="0d434-822">So sieht es möglicherweise so aus, als sei die Löschung fehlgeschlagen, obwohl sie erfolgreich war.</span><span class="sxs-lookup"><span data-stu-id="0d434-822">So it may look like the deletion didn't work when it actually did work.</span></span>

## <a name="add-get-methods-21"></a><span data-ttu-id="0d434-823">Hinzufügen von GET-Methoden 2.1</span><span class="sxs-lookup"><span data-stu-id="0d434-823">Add Get methods 2.1</span></span>

<span data-ttu-id="0d434-824">Fügen Sie der Klasse `TodoController` die folgenden Methoden hinzu, um eine API bereitzustellen, die Aufgaben abruft:</span><span class="sxs-lookup"><span data-stu-id="0d434-824">To provide an API that retrieves to-do items, add the following methods to the `TodoController` class:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

<span data-ttu-id="0d434-825">Diese Methoden implementieren zwei GET-Endpunkte:</span><span class="sxs-lookup"><span data-stu-id="0d434-825">These methods implement two GET endpoints:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="0d434-826">Halten Sie die App an, falls diese noch ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="0d434-826">Stop the app if it's still running.</span></span> <span data-ttu-id="0d434-827">Führen Sie diese dann noch mal aus, damit die letzten Änderungen übernommen werden.</span><span class="sxs-lookup"><span data-stu-id="0d434-827">Then run it again to include the latest changes.</span></span>

<span data-ttu-id="0d434-828">Testen Sie die App, indem Sie die beiden Endpunkte in einem Browser aufrufen.</span><span class="sxs-lookup"><span data-stu-id="0d434-828">Test the app by calling the two endpoints from a browser.</span></span> <span data-ttu-id="0d434-829">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="0d434-829">For example:</span></span>

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

<span data-ttu-id="0d434-830">Die folgende HTTP-Antwort wird durch den Aufruf von `GetTodoItems` erzeugt:</span><span class="sxs-lookup"><span data-stu-id="0d434-830">The following HTTP response is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths-21"></a><span data-ttu-id="0d434-831">Routing und URL-Pfade 2.1</span><span class="sxs-lookup"><span data-stu-id="0d434-831">Routing and URL paths 2.1</span></span>

<span data-ttu-id="0d434-832">Das Attribut [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) gibt eine Methode an, die auf eine HTTP GET-Anforderung antwortet.</span><span class="sxs-lookup"><span data-stu-id="0d434-832">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="0d434-833">Der URL-Pfad für jede Methode wird wie folgt erstellt:</span><span class="sxs-lookup"><span data-stu-id="0d434-833">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="0d434-834">Beginnen Sie mit der Vorlagenzeichenfolge im `Route`-Attribut des Controllers:</span><span class="sxs-lookup"><span data-stu-id="0d434-834">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* <span data-ttu-id="0d434-835">Ersetzen Sie `[controller]` durch den Namen des Controllers, bei dem es sich standardmäßig um den Namen der Controller-Klasse ohne das Suffix „Controller“ handelt.</span><span class="sxs-lookup"><span data-stu-id="0d434-835">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="0d434-836">Bei diesem Beispiel ist der Klassenname des Controllers „**Todo**Controller“, d.h. der Controllername lautet „todo“.</span><span class="sxs-lookup"><span data-stu-id="0d434-836">For this sample, the controller class name is **Todo**Controller, so the controller name is "todo".</span></span> <span data-ttu-id="0d434-837">Beim ASP.NET Core-[Routing](xref:mvc/controllers/routing) wird die Groß-/Kleinschreibung nicht beachtet.</span><span class="sxs-lookup"><span data-stu-id="0d434-837">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="0d434-838">Wenn das `[HttpGet]`-Attribut eine Routenvorlage (z. B. `[HttpGet("products")]`) hat, fügen Sie diese an den Pfad an.</span><span class="sxs-lookup"><span data-stu-id="0d434-838">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="0d434-839">In diesem Beispiel wird keine Vorlage verwendet.</span><span class="sxs-lookup"><span data-stu-id="0d434-839">This sample doesn't use a template.</span></span> <span data-ttu-id="0d434-840">Weitere Informationen finden Sie unter [Attributrouting mit Http[Verb]-Attributen](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="0d434-840">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="0d434-841">In der folgenden `GetTodoItem`-Methode ist `"{id}"` eine Platzhaltervariable für den eindeutigen Bezeichner des To-do-Elements.</span><span class="sxs-lookup"><span data-stu-id="0d434-841">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="0d434-842">Wenn `GetTodoItem` aufgerufen wird, wird der Wert von `"{id}"` in der URL der Methode in ihrem Parameter `id` bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="0d434-842">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values-21"></a><span data-ttu-id="0d434-843">Rückgabewerte 2.1</span><span class="sxs-lookup"><span data-stu-id="0d434-843">Return values 2.1</span></span>

<span data-ttu-id="0d434-844">Der Rückgabetyp der Methoden `GetTodoItems` und `GetTodoItem` ist der [ActionResult\<T>-Typ](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="0d434-844">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="0d434-845">ASP.NET Core serialisiert automatisch das Objekt in [JSON](https://www.json.org/) und schreibt den JSON-Code in den Text der Antwortnachricht.</span><span class="sxs-lookup"><span data-stu-id="0d434-845">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="0d434-846">Der Antwortcode für diesen Rückgabetyp ist 200, vorausgesetzt, es gibt keine Ausnahmefehler.</span><span class="sxs-lookup"><span data-stu-id="0d434-846">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="0d434-847">Nicht behandelte Ausnahmen werden in 5xx-Fehler übersetzt.</span><span class="sxs-lookup"><span data-stu-id="0d434-847">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="0d434-848">`ActionResult`-Rückgabetypen können eine Vielzahl von HTTP-Statuscodes darstellen.</span><span class="sxs-lookup"><span data-stu-id="0d434-848">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="0d434-849">Beispielsweise kann `GetTodoItem` zwei verschiedene Statuswerte zurückgeben:</span><span class="sxs-lookup"><span data-stu-id="0d434-849">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="0d434-850">Wenn kein Element mit der angeforderten ID übereinstimmt, gibt die Methode einen 404-Fehlercode <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> zurück.</span><span class="sxs-lookup"><span data-stu-id="0d434-850">If no item matches the requested ID, the method returns a 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="0d434-851">Andernfalls gibt die Methode 200 mit einem JSON-Antworttext zurück.</span><span class="sxs-lookup"><span data-stu-id="0d434-851">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="0d434-852">Die Rückgabe von `item` löst eine HTTP 200-Antwort aus.</span><span class="sxs-lookup"><span data-stu-id="0d434-852">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="test-the-gettodoitems-method-21"></a><span data-ttu-id="0d434-853">Testen der GetTodoItems-Methode 2.1</span><span class="sxs-lookup"><span data-stu-id="0d434-853">Test the GetTodoItems method 2.1</span></span>

<span data-ttu-id="0d434-854">Dieses Tutorial verwendet Postman zum Testen der Web-API.</span><span class="sxs-lookup"><span data-stu-id="0d434-854">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="0d434-855">Installieren Sie [Postman](https://www.getpostman.com/downloads/).</span><span class="sxs-lookup"><span data-stu-id="0d434-855">Install [Postman](https://www.getpostman.com/downloads/).</span></span>
* <span data-ttu-id="0d434-856">Starten Sie die Web-App.</span><span class="sxs-lookup"><span data-stu-id="0d434-856">Start the web app.</span></span>
* <span data-ttu-id="0d434-857">Starten Sie Postman.</span><span class="sxs-lookup"><span data-stu-id="0d434-857">Start Postman.</span></span>
* <span data-ttu-id="0d434-858">Deaktivieren Sie **SSL certificate verification** (Verifizierung des SSL-Zertifikats).</span><span class="sxs-lookup"><span data-stu-id="0d434-858">Disable **SSL certificate verification**.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0d434-859">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0d434-859">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0d434-860">Deaktivieren Sie auf der Registerkarte **General** (Allgemein) unter File > Settings (**Datei** > **Einstellungen**) **SSL certificate verification** (Verifizierung des SSL-Zertifikats).</span><span class="sxs-lookup"><span data-stu-id="0d434-860">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="0d434-861">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="0d434-861">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="0d434-862">Deaktivieren Sie auf der Registerkarte **General** (Allgemein) unter **Postman** > **Preferences** (Postman > Einstellungen) **SSL certificate verification** (Verifizierung des SSL-Zertifikats).</span><span class="sxs-lookup"><span data-stu-id="0d434-862">From **Postman** > **Preferences** (**General** tab), disable **SSL certificate verification**.</span></span> <span data-ttu-id="0d434-863">Wählen Sie alternativ den Schraubenschlüssel und **Settings** (Einstellungen) aus, und deaktivieren Sie dann die Überprüfung des SSL-Zertifikats.</span><span class="sxs-lookup"><span data-stu-id="0d434-863">Alternatively, select the wrench and select **Settings**, then disable the SSL certificate verification.</span></span>

---
  
> [!WARNING]
> <span data-ttu-id="0d434-864">Aktivieren Sie die Verifizierung des SSL-Zertifikats wieder, nachdem Sie den Controller getestet haben.</span><span class="sxs-lookup"><span data-stu-id="0d434-864">Re-enable SSL certificate verification after testing the controller.</span></span>

* <span data-ttu-id="0d434-865">Erstellen Sie eine neue Anforderung.</span><span class="sxs-lookup"><span data-stu-id="0d434-865">Create a new request.</span></span>
  * <span data-ttu-id="0d434-866">Legen Sie die HTTP-Methode auf **GET** fest.</span><span class="sxs-lookup"><span data-stu-id="0d434-866">Set the HTTP method to **GET**.</span></span>
  * <span data-ttu-id="0d434-867">Legen Sie den Anforderungs-URI auf `https://localhost:<port>/api/todo` fest.</span><span class="sxs-lookup"><span data-stu-id="0d434-867">Set the request URI to `https://localhost:<port>/api/todo`.</span></span> <span data-ttu-id="0d434-868">Beispielsweise `https://localhost:5001/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="0d434-868">For example, `https://localhost:5001/api/todo`.</span></span>
* <span data-ttu-id="0d434-869">Wählen Sie in Postman **Two pane view** (Ansicht mit zwei Bereichen) aus.</span><span class="sxs-lookup"><span data-stu-id="0d434-869">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="0d434-870">Wählen Sie **Send** (Senden) aus.</span><span class="sxs-lookup"><span data-stu-id="0d434-870">Select **Send**.</span></span>

![Postman mit GET-Anforderung](first-web-api/_static/2pv.png)

## <a name="add-a-create-method-21"></a><span data-ttu-id="0d434-872">Hinzufügen einer Methode 2.1</span><span class="sxs-lookup"><span data-stu-id="0d434-872">Add a Create method 2.1</span></span>

<span data-ttu-id="0d434-873">Fügen Sie die folgende `PostTodoItem`-Methode in *Controllers/TodoController.cs* hinzu:</span><span class="sxs-lookup"><span data-stu-id="0d434-873">Add the following `PostTodoItem` method inside of *Controllers/TodoController.cs*:</span></span> 

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="0d434-874">Der oben stehende Code ist eine HTTP POST-Methode, wie durch das [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute)-Attribut angegeben.</span><span class="sxs-lookup"><span data-stu-id="0d434-874">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="0d434-875">Die Methode ruft den Wert der Aufgabe aus dem Text der HTTP-Anforderung ab.</span><span class="sxs-lookup"><span data-stu-id="0d434-875">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="0d434-876">Die `CreatedAtAction`-Methode:</span><span class="sxs-lookup"><span data-stu-id="0d434-876">The `CreatedAtAction` method:</span></span>

* <span data-ttu-id="0d434-877">Gibt bei einer erfolgreichen Anforderung den Statuscode „HTTP 201“ zurück.</span><span class="sxs-lookup"><span data-stu-id="0d434-877">Returns an HTTP 201 status code, if successful.</span></span> <span data-ttu-id="0d434-878">HTTP 201 ist die Standardantwort für eine HTTP POST-Methode, die eine neue Ressource auf dem Server erstellt.</span><span class="sxs-lookup"><span data-stu-id="0d434-878">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="0d434-879">Fügt der Antwort einen `Location`-Header hinzu.</span><span class="sxs-lookup"><span data-stu-id="0d434-879">Adds a `Location` header to the response.</span></span> <span data-ttu-id="0d434-880">Der `Location`-Header gibt den URI des neu erstellten To-Do-Elements zurück.</span><span class="sxs-lookup"><span data-stu-id="0d434-880">The `Location` header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="0d434-881">Weitere Informationen finden Sie unter [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="0d434-881">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="0d434-882">Verweist auf die `GetTodoItem`-Aktion zum Erstellen des URIs des `Location`-Headers.</span><span class="sxs-lookup"><span data-stu-id="0d434-882">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="0d434-883">Das `nameof`-Schlüsselwort von C# wird verwendet, um eine Hartcodierung des Aktionsnamens im `CreatedAtAction`-Aufruf zu vermeiden.</span><span class="sxs-lookup"><span data-stu-id="0d434-883">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method-21"></a><span data-ttu-id="0d434-884">Testen der PostTodoItem-Methode 2.1</span><span class="sxs-lookup"><span data-stu-id="0d434-884">Test the PostTodoItem method 2.1</span></span>

* <span data-ttu-id="0d434-885">Erstellen Sie das Projekt.</span><span class="sxs-lookup"><span data-stu-id="0d434-885">Build the project.</span></span>
* <span data-ttu-id="0d434-886">Legen Sie in Postman die HTTP-Methode auf `POST` fest.</span><span class="sxs-lookup"><span data-stu-id="0d434-886">In Postman, set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="0d434-887">Legen Sie den URI auf `https://localhost:<port>/api/TodoItem` fest.</span><span class="sxs-lookup"><span data-stu-id="0d434-887">Set the URI to `https://localhost:<port>/api/TodoItem`.</span></span> <span data-ttu-id="0d434-888">Beispiel: `https://localhost:5001/api/TodoItem`.</span><span class="sxs-lookup"><span data-stu-id="0d434-888">For example, `https://localhost:5001/api/TodoItem`.</span></span>
* <span data-ttu-id="0d434-889">Wählen Sie die Registerkarte **Body** (Text) aus.</span><span class="sxs-lookup"><span data-stu-id="0d434-889">Select the **Body** tab.</span></span>
* <span data-ttu-id="0d434-890">Wählen Sie das Optionsfeld **raw** (Unformatiert) aus.</span><span class="sxs-lookup"><span data-stu-id="0d434-890">Select the **raw** radio button.</span></span>
* <span data-ttu-id="0d434-891">Legen Sie den Typ auf **JSON (application/json)** fest.</span><span class="sxs-lookup"><span data-stu-id="0d434-891">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="0d434-892">Geben Sie für die Aufgabe den Anforderungstext im JSON-Format ein:</span><span class="sxs-lookup"><span data-stu-id="0d434-892">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="0d434-893">Wählen Sie **Send** (Senden) aus.</span><span class="sxs-lookup"><span data-stu-id="0d434-893">Select **Send**.</span></span>

  ![Postman mit erstellter Anforderung](first-web-api/_static/create.png)

  <span data-ttu-id="0d434-895">Wenn Sie die Fehlermeldung „405: Methode nicht zulässig“ erhalten, wurde das Projekt wahrscheinlich nicht kompiliert, nachdem die Methode `PostTodoItem` hinzugefügt wurde.</span><span class="sxs-lookup"><span data-stu-id="0d434-895">If you get a 405 Method Not Allowed error, it's probably the result of not compiling the project after adding the `PostTodoItem` method.</span></span>

### <a name="test-the-location-header-uri-21"></a><span data-ttu-id="0d434-896">Testen des Adressheader-URIs 2.1</span><span class="sxs-lookup"><span data-stu-id="0d434-896">Test the location header URI 2.1</span></span>

* <span data-ttu-id="0d434-897">Wählen Sie auf der Registerkarte **Header** (Header) den Bereich **Response** (Antwort) aus.</span><span class="sxs-lookup"><span data-stu-id="0d434-897">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="0d434-898">Kopieren Sie den den Headerwert von **Location** (Speicherort):</span><span class="sxs-lookup"><span data-stu-id="0d434-898">Copy the **Location** header value:</span></span>

  ![Registerkarte „Header“ in der Postman-Konsole](first-web-api/_static/pmc2.png)

* <span data-ttu-id="0d434-900">Legen Sie die Methode auf „GET“ fest.</span><span class="sxs-lookup"><span data-stu-id="0d434-900">Set the method to GET.</span></span>
* <span data-ttu-id="0d434-901">Legen Sie den URI auf `https://localhost:<port>/api/TodoItems/2` fest.</span><span class="sxs-lookup"><span data-stu-id="0d434-901">Set the URI to `https://localhost:<port>/api/TodoItems/2`.</span></span> <span data-ttu-id="0d434-902">Beispiel: `https://localhost:5001/api/TodoItems/2`.</span><span class="sxs-lookup"><span data-stu-id="0d434-902">For example, `https://localhost:5001/api/TodoItems/2`.</span></span>
* <span data-ttu-id="0d434-903">Wählen Sie **Send** (Senden) aus.</span><span class="sxs-lookup"><span data-stu-id="0d434-903">Select **Send**.</span></span>

## <a name="add-a-puttodoitem-method-21"></a><span data-ttu-id="0d434-904">Hinzufügen einer PutTodoItem-Methode 2.1</span><span class="sxs-lookup"><span data-stu-id="0d434-904">Add a PutTodoItem method 2.1</span></span>

<span data-ttu-id="0d434-905">Fügen Sie die folgende `PutTodoItem`-Methode hinzu:</span><span class="sxs-lookup"><span data-stu-id="0d434-905">Add the following `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="0d434-906">`PutTodoItem` ähnelt `PostTodoItem`, verwendet allerdings HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="0d434-906">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="0d434-907">Die Antwort ist [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="0d434-907">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="0d434-908">Gemäß der HTTP-Spezifikation erfordert eine PUT-Anforderung, dass der Client die gesamte aktualisierte Entität (nicht nur die Änderungen) sendet.</span><span class="sxs-lookup"><span data-stu-id="0d434-908">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="0d434-909">Verwenden Sie [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute), um Teilupdates zu unterstützen.</span><span class="sxs-lookup"><span data-stu-id="0d434-909">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="0d434-910">Wenn beim Aufrufen von `PutTodoItem` ein Fehler zurückgegeben wird, rufen Sie `GET` auf, um sicherzustellen, dass die Datenbank ein Element enthält.</span><span class="sxs-lookup"><span data-stu-id="0d434-910">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method-21"></a><span data-ttu-id="0d434-911">Testen der PutTodoItem-Methode 2.1</span><span class="sxs-lookup"><span data-stu-id="0d434-911">Test the PutTodoItem method 2.1</span></span>

<span data-ttu-id="0d434-912">In diesem Beispiel wird eine In-Memory-Datenbank verwendet, die jedes Mal initialisiert werden muss, wenn die App gestartet wird.</span><span class="sxs-lookup"><span data-stu-id="0d434-912">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="0d434-913">Es muss ein Element in der Datenbank vorhanden sein, bevor Sie einen PUT-Aufruf durchführen.</span><span class="sxs-lookup"><span data-stu-id="0d434-913">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="0d434-914">Rufen Sie vor einem PUT-Aufruf GET auf, um sicherzustellen, dass ein Element in der Datenbank vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="0d434-914">Call GET to ensure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="0d434-915">Aktualisieren Sie das To-do-Element, das über den ID-Wert 1 verfügt, und legen Sie als Namen „feed fish“ fest:</span><span class="sxs-lookup"><span data-stu-id="0d434-915">Update the to-do item that has Id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "id":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="0d434-916">Die folgende Abbildung zeigt das Postman-Update:</span><span class="sxs-lookup"><span data-stu-id="0d434-916">The following image shows the Postman update:</span></span>

![Postman-Konsole mit der Antwort „204 (Kein Inhalt)“](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method-21"></a><span data-ttu-id="0d434-918">Hinzufügen einer DeleteTodoItem-Methode 2.1</span><span class="sxs-lookup"><span data-stu-id="0d434-918">Add a DeleteTodoItem method 2.1</span></span>

<span data-ttu-id="0d434-919">Fügen Sie die folgende `DeleteTodoItem`-Methode hinzu:</span><span class="sxs-lookup"><span data-stu-id="0d434-919">Add the following `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="0d434-920">Die `DeleteTodoItem`-Antwort lautet [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="0d434-920">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method-21"></a><span data-ttu-id="0d434-921">Testen der DeleteTodoItem-Methode 2.1</span><span class="sxs-lookup"><span data-stu-id="0d434-921">Test the DeleteTodoItem method 2.1</span></span>

<span data-ttu-id="0d434-922">So löschen Sie mit Postman eine Aufgabe</span><span class="sxs-lookup"><span data-stu-id="0d434-922">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="0d434-923">Legen Sie die Methode auf `DELETE` fest.</span><span class="sxs-lookup"><span data-stu-id="0d434-923">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="0d434-924">Legen Sie den URI des zu löschenden Objekts fest, z. B. `https://localhost:5001/api/todo/1`.</span><span class="sxs-lookup"><span data-stu-id="0d434-924">Set the URI of the object to delete (for example, `https://localhost:5001/api/todo/1`).</span></span>
* <span data-ttu-id="0d434-925">Wählen Sie **Send** (Senden) aus.</span><span class="sxs-lookup"><span data-stu-id="0d434-925">Select **Send**.</span></span>

<span data-ttu-id="0d434-926">Sie können in der Beispiel-App alle Elemente löschen.</span><span class="sxs-lookup"><span data-stu-id="0d434-926">The sample app allows you to delete all the items.</span></span> <span data-ttu-id="0d434-927">Sobald das letzte Element gelöscht wurde, wird allerdings beim nächsten API-Aufruf vom Modellklassenkonstruktor ein neues erstellt.</span><span class="sxs-lookup"><span data-stu-id="0d434-927">However, when the last item is deleted, a new one is created by the model class constructor the next time the API is called.</span></span>

## <a name="call-the-web-api-with-javascript-21"></a><span data-ttu-id="0d434-928">Aufrufen der Web-API mit JavaScript 2.1</span><span class="sxs-lookup"><span data-stu-id="0d434-928">Call the web API with JavaScript 2.1</span></span>

<span data-ttu-id="0d434-929">In diesem Abschnitt wird eine HTML-Seite hinzugefügt, die mithilfe von JavaScript die Web-API aufruft.</span><span class="sxs-lookup"><span data-stu-id="0d434-929">In this section, an HTML page is added that uses JavaScript to call the web API.</span></span> <span data-ttu-id="0d434-930">jQuery initiiert die Anforderung.</span><span class="sxs-lookup"><span data-stu-id="0d434-930">jQuery initiates the request.</span></span> <span data-ttu-id="0d434-931">JavaScript aktualisiert die Seite mit den Details aus der Antwort der Web-API.</span><span class="sxs-lookup"><span data-stu-id="0d434-931">JavaScript updates the page with the details from the web API's response.</span></span>

<span data-ttu-id="0d434-932">Konfigurieren Sie die App so, dass sie [statische Dateien bereitstellt](xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A), und [aktivieren Sie die Standarddateizuordnung](xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A), indem Sie *startup.cs* mit dem unten markierten Code aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="0d434-932">Configure the app to [serve static files](xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A) and [enable default file mapping](xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A) by updating *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

<span data-ttu-id="0d434-933">Erstellen Sie im Projektverzeichnis den Ordner *wwwwroot*.</span><span class="sxs-lookup"><span data-stu-id="0d434-933">Create a *wwwroot* folder in the project directory.</span></span>

<span data-ttu-id="0d434-934">Fügen Sie dem Verzeichnis *wwwroot* eine HTML-Datei namens *index.html* hinzu.</span><span class="sxs-lookup"><span data-stu-id="0d434-934">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="0d434-935">Ersetzen Sie den Inhalt durch folgendes Markup:</span><span class="sxs-lookup"><span data-stu-id="0d434-935">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

<span data-ttu-id="0d434-936">Fügen Sie dem Verzeichnis *wwwroot* eine JavaScript-Datei namens *site.js* hinzu.</span><span class="sxs-lookup"><span data-stu-id="0d434-936">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="0d434-937">Ersetzen Sie den Inhalt durch folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="0d434-937">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="0d434-938">Möglicherweise ist eine Änderung an den Starteinstellungen des ASP.NET Core-Projekts erforderlich, um die HTML-Seite lokal zu testen:</span><span class="sxs-lookup"><span data-stu-id="0d434-938">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="0d434-939">Öffnen Sie *Properties\launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="0d434-939">Open *Properties\launchSettings.json*.</span></span>
* <span data-ttu-id="0d434-940">Entfernen Sie die `launchUrl`-Eigenschaft, um zu erzwingen, dass die App mit *index.html* als Startseite geöffnet wird. Dies ist die Standarddatei des Projekts.</span><span class="sxs-lookup"><span data-stu-id="0d434-940">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="0d434-941">Dieses Beispiel ruft alle CRUD-Methoden der Web-API auf.</span><span class="sxs-lookup"><span data-stu-id="0d434-941">This sample calls all of the CRUD methods of the web API.</span></span> <span data-ttu-id="0d434-942">Im Folgenden werden die API-Aufrufe erläutert.</span><span class="sxs-lookup"><span data-stu-id="0d434-942">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items-21"></a><span data-ttu-id="0d434-943">Abrufen einer Liste von To-Do-Elementen 2.1</span><span class="sxs-lookup"><span data-stu-id="0d434-943">Get a list of to-do items 2.1</span></span>

<span data-ttu-id="0d434-944">jQuery sendet eine HTTP GET-Anforderung an die Web-API, die JSON-Code mit einem Array aus To-Do-Elementen zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="0d434-944">jQuery sends an HTTP GET request to the web API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="0d434-945">Die Rückruffunktion `success` wird aufgerufen, wenn die Anforderung erfolgreich ist.</span><span class="sxs-lookup"><span data-stu-id="0d434-945">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="0d434-946">Im Rückruf wird DOM mit den To-Do-Informationen aktualisiert.</span><span class="sxs-lookup"><span data-stu-id="0d434-946">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item-21"></a><span data-ttu-id="0d434-947">Hinzufügen eines To-Do-Elements 2.1</span><span class="sxs-lookup"><span data-stu-id="0d434-947">Add a to-do item 2.1</span></span>

<span data-ttu-id="0d434-948">jQuery sendet eine HTTP POST-Anforderung mit dem To-Do-Element im Anforderungstext.</span><span class="sxs-lookup"><span data-stu-id="0d434-948">jQuery sends an HTTP POST request with the to-do item in the request body.</span></span> <span data-ttu-id="0d434-949">Die Optionen `accepts` und `contentType` werden auf `application/json` festgelegt, um den gesendeten und empfangenen Medientyp anzugeben.</span><span class="sxs-lookup"><span data-stu-id="0d434-949">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="0d434-950">Die Aufgabe wird mit [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify) in JSON konvertiert.</span><span class="sxs-lookup"><span data-stu-id="0d434-950">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="0d434-951">Wenn die API den Statuscode „Erfolgreich“ zurückgibt, wird die Funktion `getData` aufgerufen, um die HTML-Tabelle zu aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="0d434-951">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item-21"></a><span data-ttu-id="0d434-952">Aktualisieren eines To-Do-Elements 2.1</span><span class="sxs-lookup"><span data-stu-id="0d434-952">Update a to-do item 2.1</span></span>

<span data-ttu-id="0d434-953">Das Aktualisieren einer Aufgabe ist vergleichbar mit dem Hinzufügen einer Aufgabe.</span><span class="sxs-lookup"><span data-stu-id="0d434-953">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="0d434-954">Die `url` ändert sich, um den eindeutigen Bezeichner des Elements hinzuzufügen. Der `type` lautet `PUT`.</span><span class="sxs-lookup"><span data-stu-id="0d434-954">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item-21"></a><span data-ttu-id="0d434-955">Löschen eines To-Do-Elements 2.1</span><span class="sxs-lookup"><span data-stu-id="0d434-955">Delete a to-do item 2.1</span></span>

<span data-ttu-id="0d434-956">Eine Aufgabe wird folgendermaßen gelöscht: im AJAX-Aufruf wird `type` auf `DELETE` festgelegt, und der eindeutige Bezeichner des Elements wird in der URL angegeben.</span><span class="sxs-lookup"><span data-stu-id="0d434-956">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

<a name="auth"></a>

## <a name="add-authentication-support-to-a-web-api-21"></a><span data-ttu-id="0d434-957">Hinzufügen der Authentifizierungsunterstützung zu einer Web-API 2.1</span><span class="sxs-lookup"><span data-stu-id="0d434-957">Add authentication support to a web API 2.1</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="additional-resources-21"></a><span data-ttu-id="0d434-958">Zusätzliche Ressourcen 2.1</span><span class="sxs-lookup"><span data-stu-id="0d434-958">Additional resources 2.1</span></span>

<span data-ttu-id="0d434-959">[Sie können den Beispielcode für dieses Tutorial anzeigen oder herunterladen.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples)</span><span class="sxs-lookup"><span data-stu-id="0d434-959">[View or download sample code for this tutorial](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span></span> <span data-ttu-id="0d434-960">[Informationen zum Herunterladen](xref:index#how-to-download-a-sample) finden Sie hier.</span><span class="sxs-lookup"><span data-stu-id="0d434-960">See [how to download](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="0d434-961">Weitere Informationen finden Sie in den folgenden Ressourcen:</span><span class="sxs-lookup"><span data-stu-id="0d434-961">For more information, see the following resources:</span></span>

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/intro>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [<span data-ttu-id="0d434-962">Dieses Tutorial auf YouTube</span><span class="sxs-lookup"><span data-stu-id="0d434-962">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=TTkhEyGBfAk)
