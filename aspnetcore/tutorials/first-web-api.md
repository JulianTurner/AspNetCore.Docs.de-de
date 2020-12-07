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
ms.sourcegitcommit: fe2e3174c34bee1e425c6e52dd8f663fe52b8756
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96175051"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a><span data-ttu-id="94a6e-103">Tutorial: Erstellen einer Web-API mit ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="94a6e-103">Tutorial: Create a web API with ASP.NET Core</span></span>

<span data-ttu-id="94a6e-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5) und [Mike Wasson](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="94a6e-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="94a6e-105">In diesem Tutorial lernen Sie die Grundlagen der Erstellung einer Web-API mit ASP.NET Core kennen.</span><span class="sxs-lookup"><span data-stu-id="94a6e-105">This tutorial teaches the basics of building a web API with ASP.NET Core.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="94a6e-106">In diesem Tutorial lernen Sie, wie die folgenden Aufgaben ausgeführt werden:</span><span class="sxs-lookup"><span data-stu-id="94a6e-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="94a6e-107">Erstellen eines Web-API-Projekts</span><span class="sxs-lookup"><span data-stu-id="94a6e-107">Create a web API project.</span></span>
> * <span data-ttu-id="94a6e-108">Hinzufügen einer Modellklasse und eines Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="94a6e-108">Add a model class and a database context.</span></span>
> * <span data-ttu-id="94a6e-109">Erstellen eines Controllergerüsts mit CRUD-Methoden</span><span class="sxs-lookup"><span data-stu-id="94a6e-109">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="94a6e-110">Konfigurieren des Routings, der URL-Pfade und der Rückgabewerte</span><span class="sxs-lookup"><span data-stu-id="94a6e-110">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="94a6e-111">Aufrufen der Web-API mit Postman</span><span class="sxs-lookup"><span data-stu-id="94a6e-111">Call the web API with Postman.</span></span>

<span data-ttu-id="94a6e-112">Abschließend steht Ihnen eine Web-API zur Verfügung, die in einer relationalen Datenbank gespeicherte To-do-Elemente verwalten kann.</span><span class="sxs-lookup"><span data-stu-id="94a6e-112">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="94a6e-113">Übersicht</span><span class="sxs-lookup"><span data-stu-id="94a6e-113">Overview</span></span>

<span data-ttu-id="94a6e-114">In diesem Tutorial wird die folgende API erstellt:</span><span class="sxs-lookup"><span data-stu-id="94a6e-114">This tutorial creates the following API:</span></span>

|<span data-ttu-id="94a6e-115">API</span><span class="sxs-lookup"><span data-stu-id="94a6e-115">API</span></span> | <span data-ttu-id="94a6e-116">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="94a6e-116">Description</span></span> | <span data-ttu-id="94a6e-117">Anforderungstext</span><span class="sxs-lookup"><span data-stu-id="94a6e-117">Request body</span></span> | <span data-ttu-id="94a6e-118">Antworttext</span><span class="sxs-lookup"><span data-stu-id="94a6e-118">Response body</span></span> |
|--- | ---- | ---- | ---- |
|`GET /api/TodoItems` | <span data-ttu-id="94a6e-119">Alle To-do-Elemente abrufen</span><span class="sxs-lookup"><span data-stu-id="94a6e-119">Get all to-do items</span></span> | <span data-ttu-id="94a6e-120">Keine</span><span class="sxs-lookup"><span data-stu-id="94a6e-120">None</span></span> | <span data-ttu-id="94a6e-121">Array von To-do-Elementen</span><span class="sxs-lookup"><span data-stu-id="94a6e-121">Array of to-do items</span></span>|
|`GET /api/TodoItems/{id}` | <span data-ttu-id="94a6e-122">Ein Element nach ID abrufen</span><span class="sxs-lookup"><span data-stu-id="94a6e-122">Get an item by ID</span></span> | <span data-ttu-id="94a6e-123">Keine</span><span class="sxs-lookup"><span data-stu-id="94a6e-123">None</span></span> | <span data-ttu-id="94a6e-124">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="94a6e-124">To-do item</span></span>|
|`POST /api/TodoItems` | <span data-ttu-id="94a6e-125">Neues Element hinzufügen</span><span class="sxs-lookup"><span data-stu-id="94a6e-125">Add a new item</span></span> | <span data-ttu-id="94a6e-126">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="94a6e-126">To-do item</span></span> | <span data-ttu-id="94a6e-127">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="94a6e-127">To-do item</span></span> |
|`PUT /api/TodoItems/{id}` | <span data-ttu-id="94a6e-128">Vorhandenes Element aktualisieren &nbsp;</span><span class="sxs-lookup"><span data-stu-id="94a6e-128">Update an existing item &nbsp;</span></span> | <span data-ttu-id="94a6e-129">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="94a6e-129">To-do item</span></span> | <span data-ttu-id="94a6e-130">Keine</span><span class="sxs-lookup"><span data-stu-id="94a6e-130">None</span></span> |
|<span data-ttu-id="94a6e-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="94a6e-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span></span> | <span data-ttu-id="94a6e-132">Löschen eines Elements &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="94a6e-132">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="94a6e-133">Keine</span><span class="sxs-lookup"><span data-stu-id="94a6e-133">None</span></span> | <span data-ttu-id="94a6e-134">Keine</span><span class="sxs-lookup"><span data-stu-id="94a6e-134">None</span></span>|

<span data-ttu-id="94a6e-135">Das folgende Diagramm zeigt den Entwurf der App.</span><span class="sxs-lookup"><span data-stu-id="94a6e-135">The following diagram shows the design of the app.</span></span>

![Der Client ist das Feld ganz links.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="94a6e-141">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="94a6e-141">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="94a6e-142">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="94a6e-142">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="94a6e-143">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="94a6e-143">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="94a6e-144">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="94a6e-144">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-5.0.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="94a6e-145">Erstellen eines Webprojekts</span><span class="sxs-lookup"><span data-stu-id="94a6e-145">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="94a6e-146">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="94a6e-146">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="94a6e-147">Klicken Sie im Menü **Datei** auf **Neu** > **Projekt**.</span><span class="sxs-lookup"><span data-stu-id="94a6e-147">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="94a6e-148">Wählen Sie die Vorlage **ASP.NET Core-Webanwendung** aus, und klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="94a6e-148">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="94a6e-149">Geben Sie dem Projekt den Namen *TodoApi*, und klicken Sie auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="94a6e-149">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="94a6e-150">Vergewissern Sie sich, dass im Dialogfeld **Neue ASP.NET Core-Webanwendung erstellen** die Optionen **.NET Core** und **ASP.NET Core 5.0** ausgewählt sind.</span><span class="sxs-lookup"><span data-stu-id="94a6e-150">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 5.0** are selected.</span></span> <span data-ttu-id="94a6e-151">Wählen Sie die Vorlage **API** aus, und klicken Sie auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="94a6e-151">Select the **API** template and click **Create**.</span></span>

![VS-Dialogfeld „Neues Projekt“](first-web-api/_static/5/vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="94a6e-153">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="94a6e-153">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="94a6e-154">Öffnen Sie das [integrierte Terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="94a6e-154">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="94a6e-155">Wechseln Sie mit dem Befehl `cd`' zu dem Ordner, der den Projektordner enthalten soll.</span><span class="sxs-lookup"><span data-stu-id="94a6e-155">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="94a6e-156">Führen Sie die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="94a6e-156">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="94a6e-157">Wenn Sie in einem Dialogfeld angeben müssen, ob Sie dem Projekt die erforderlichen Elemente hinzufügen möchten, wählen Sie **Ja** aus.</span><span class="sxs-lookup"><span data-stu-id="94a6e-157">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="94a6e-158">Die obenstehenden Befehle haben folgende Konsequenzen:</span><span class="sxs-lookup"><span data-stu-id="94a6e-158">The preceding commands:</span></span>

  * <span data-ttu-id="94a6e-159">Ein neues Web-API-Projekt wird erstellt und in Visual Studio Code geöffnet.</span><span class="sxs-lookup"><span data-stu-id="94a6e-159">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="94a6e-160">Die NuGet-Pakete, die im nächsten Abschnitt erforderlich sind, werden hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="94a6e-160">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="94a6e-161">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="94a6e-161">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="94a6e-162">Klicken Sie auf **Datei** > **Neue Projektmappe**.</span><span class="sxs-lookup"><span data-stu-id="94a6e-162">Select **File** > **New Solution**.</span></span>

  ![Neue Projektmappe in macOS](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="94a6e-164">Navigieren Sie in Visual Studio für Mac-Versionen vor Version 8.6 zu **.NET Core** > **App** > **API** > **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="94a6e-164">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="94a6e-165">Bei Version 8.6 oder höher klicken Sie auf **Web and Console** > **App** > **API** > **Weiter** (Web und Konsole).</span><span class="sxs-lookup"><span data-stu-id="94a6e-165">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>

  ![Auswählen von macOS-API-Vorlagen](first-web-api-mac/_static/api_template.png)

* <span data-ttu-id="94a6e-167">Wählen Sie im Dialogfeld **Neue ASP.NET Core-Web-API konfigurieren** die neueste .NET Core 5.x-Version als **Zielframework** aus.</span><span class="sxs-lookup"><span data-stu-id="94a6e-167">In the **Configure the new ASP.NET Core Web API** dialog, select the latest .NET Core 5.x **Target Framework**.</span></span> <span data-ttu-id="94a6e-168">Klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="94a6e-168">Select **Next**.</span></span>

* <span data-ttu-id="94a6e-169">Geben Sie für **Projektname** *TodoApi* ein, und wählen Sie dann **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="94a6e-169">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![Dialogfeld „Konfiguration“](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="94a6e-171">Öffnen Sie im Projektordner ein Befehlsterminal, und führen Sie die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="94a6e-171">Open a command terminal in the project folder and run the following commands:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-project"></a><span data-ttu-id="94a6e-172">Testen des Projekts</span><span class="sxs-lookup"><span data-stu-id="94a6e-172">Test the project</span></span>

<span data-ttu-id="94a6e-173">Die Projektvorlage erstellt eine `WeatherForecast`-API mit Unterstützung für [Swagger](xref:tutorials/web-api-help-pages-using-swagger).</span><span class="sxs-lookup"><span data-stu-id="94a6e-173">The project template creates a `WeatherForecast` API with support for [Swagger](xref:tutorials/web-api-help-pages-using-swagger).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="94a6e-174">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="94a6e-174">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="94a6e-175">Drücken Sie STRG+F5, um die Ausführung ohne den Debugger zu starten.</span><span class="sxs-lookup"><span data-stu-id="94a6e-175">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="94a6e-176">Visual Studio startet Folgendes:</span><span class="sxs-lookup"><span data-stu-id="94a6e-176">Visual Studio launches:</span></span>

* <span data-ttu-id="94a6e-177">Den IIS Express-Webserver.</span><span class="sxs-lookup"><span data-stu-id="94a6e-177">The IIS Express web server.</span></span>
* <span data-ttu-id="94a6e-178">Den Standardbrowser. Visual Studio navigiert zu `https://localhost:<port>/swagger/index.html`, wobei es sich bei `<port>` um eine zufällig ausgewählte Portnummer handelt.</span><span class="sxs-lookup"><span data-stu-id="94a6e-178">The default browser and navigates to `https://localhost:<port>/swagger/index.html`, where `<port>` is a randomly chosen port number.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="94a6e-179">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="94a6e-179">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/trustCertVSC.md)]

<span data-ttu-id="94a6e-180">Drücken Sie STRG+F5, um die App auszuführen.</span><span class="sxs-lookup"><span data-stu-id="94a6e-180">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="94a6e-181">Navigieren Sie in einem Browser zur folgenden URL: [https://localhost:5001/swagger](https://localhost:5001/swagger).</span><span class="sxs-lookup"><span data-stu-id="94a6e-181">In a browser, go to following URL: [https://localhost:5001/swagger](https://localhost:5001/swagger)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="94a6e-182">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="94a6e-182">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="94a6e-183">Wählen Sie **Ausführen** > **Debugging starten** aus, um die App zu starten.</span><span class="sxs-lookup"><span data-stu-id="94a6e-183">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="94a6e-184">Visual Studio für Mac startet einen Browser und navigiert zu `https://localhost:<port>`, wobei es sich bei `<port>` um eine zufällig ausgewählte Portnummer handelt.</span><span class="sxs-lookup"><span data-stu-id="94a6e-184">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="94a6e-185">Der Fehler „HTTP 404: Nicht gefunden“ wird zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="94a6e-185">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="94a6e-186">Fügen Sie der URL `/swagger` an, d.h. ändern Sie die URL in `https://localhost:<port>/swagger`.</span><span class="sxs-lookup"><span data-stu-id="94a6e-186">Append `/swagger` to the URL (change the URL to `https://localhost:<port>/swagger`).</span></span>

---

<span data-ttu-id="94a6e-187">Die Swagger-Seite `/swagger/index.html` wird angezeigt.</span><span class="sxs-lookup"><span data-stu-id="94a6e-187">The Swagger page `/swagger/index.html` is displayed.</span></span> <span data-ttu-id="94a6e-188">Wählen Sie **GET** > **Tryit out** > **Execute**  (GET > Testen> Ausführen) aus.</span><span class="sxs-lookup"><span data-stu-id="94a6e-188">Select **GET** > **Try it out** > **Execute**.</span></span> <span data-ttu-id="94a6e-189">Die Seite zeigt Folgendes an:</span><span class="sxs-lookup"><span data-stu-id="94a6e-189">The page displays:</span></span>

* <span data-ttu-id="94a6e-190">Der [Curl](https://curl.haxx.se/)-Befehl, zum Testen der WeatherForecast-API.</span><span class="sxs-lookup"><span data-stu-id="94a6e-190">The [Curl](https://curl.haxx.se/) command to test the WeatherForecast API.</span></span>
* <span data-ttu-id="94a6e-191">Die URL zum Testen der WeatherForecast-API.</span><span class="sxs-lookup"><span data-stu-id="94a6e-191">The URL to test the WeatherForecast API.</span></span>
* <span data-ttu-id="94a6e-192">Der Antwortcode, der Text und die Header.</span><span class="sxs-lookup"><span data-stu-id="94a6e-192">The response code, body, and headers.</span></span>
* <span data-ttu-id="94a6e-193">Ein Dropdown-Listenfeld mit Medientypen und dem Beispielwert und -schema.</span><span class="sxs-lookup"><span data-stu-id="94a6e-193">A drop down list box with media types and the example value and schema.</span></span>

<!-- Review: Do we care the IE generates several errors. It shows the data, but with  Unrecognized response type; displaying content as text.
-->
<span data-ttu-id="94a6e-194">Swagger wird verwendet, um hilfreiche Dokumentation und Hilfeseiten für Web-APIs zu generieren.</span><span class="sxs-lookup"><span data-stu-id="94a6e-194">Swagger is used to generate useful documentation and help pages for web APIs.</span></span> <span data-ttu-id="94a6e-195">Dieses Tutorial konzentriert sich auf die Erstellung einer Web-API.</span><span class="sxs-lookup"><span data-stu-id="94a6e-195">This tutorial focuses on creating a web API.</span></span> <span data-ttu-id="94a6e-196">Weitere Informationen zu Swagger finden Sie unter <xref:tutorials/web-api-help-pages-using-swagger>.</span><span class="sxs-lookup"><span data-stu-id="94a6e-196">For more information on Swagger, see <xref:tutorials/web-api-help-pages-using-swagger>.</span></span>

<span data-ttu-id="94a6e-197">Kopieren Sie die **Anforderungs-URL**, und fügen Sie sie im Browser ein: `https://localhost:<port>/WeatherForecast`</span><span class="sxs-lookup"><span data-stu-id="94a6e-197">Copy and paste the **Request URL** in the browser:  `https://localhost:<port>/WeatherForecast`</span></span>

<span data-ttu-id="94a6e-198">Der zurückgegebene JSON-Code sieht in etwa wie folgt aus:</span><span class="sxs-lookup"><span data-stu-id="94a6e-198">JSON similar to the following is returned:</span></span>

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

### <a name="update-the-launchurl"></a><span data-ttu-id="94a6e-199">Aktualisieren der launchUrl</span><span class="sxs-lookup"><span data-stu-id="94a6e-199">Update the launchUrl</span></span>

<span data-ttu-id="94a6e-200">Aktualisieren Sie `launchUrl` in *Properties\launchSettings.json* aus `"swagger"` in `"api/TodoItems"`:</span><span class="sxs-lookup"><span data-stu-id="94a6e-200">In *Properties\launchSettings.json*, update `launchUrl` from `"swagger"` to `"api/TodoItems"`:</span></span>

```json
"launchUrl": "api/TodoItems",
```

<span data-ttu-id="94a6e-201">Da Swagger entfernt wurde, ändert das oben gezeigte Markup die URL, die für die GET-Methode des Controllers gestartet wird, der in den folgenden Abschnitten hinzugefügt wurde.</span><span class="sxs-lookup"><span data-stu-id="94a6e-201">Because Swagger has been removed, the preceding markup changes the URL that is launched to the GET method of the controller added in the following sections.</span></span>

## <a name="add-a-model-class"></a><span data-ttu-id="94a6e-202">Hinzufügen einer Modellklasse</span><span class="sxs-lookup"><span data-stu-id="94a6e-202">Add a model class</span></span>

<span data-ttu-id="94a6e-203">Ein *Modell* ist eine Gruppe von Klassen, die die Daten darstellen, die die App verwaltet.</span><span class="sxs-lookup"><span data-stu-id="94a6e-203">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="94a6e-204">Das Modell für diese App ist eine einzelne `TodoItem`-Klasse.</span><span class="sxs-lookup"><span data-stu-id="94a6e-204">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="94a6e-205">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="94a6e-205">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="94a6e-206">Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt.</span><span class="sxs-lookup"><span data-stu-id="94a6e-206">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="94a6e-207">Klicken Sie auf **Hinzufügen** > **Neuer Ordner**.</span><span class="sxs-lookup"><span data-stu-id="94a6e-207">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="94a6e-208">Geben Sie dem Ordner den Namen *Models* .</span><span class="sxs-lookup"><span data-stu-id="94a6e-208">Name the folder *Models*.</span></span>

* <span data-ttu-id="94a6e-209">Klicken Sie mit der rechten Maustaste auf den Ordner *Models* , und wählen Sie **Hinzufügen** > **Klasse** aus.</span><span class="sxs-lookup"><span data-stu-id="94a6e-209">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="94a6e-210">Nennen Sie die Klasse *TodoItem*, und wählen Sie **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="94a6e-210">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="94a6e-211">Ersetzen Sie den Vorlagencode durch Folgendes:</span><span class="sxs-lookup"><span data-stu-id="94a6e-211">Replace the template code with the following:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="94a6e-212">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="94a6e-212">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="94a6e-213">Fügen Sie einen Ordner mit dem Namen *Models* hinzu.</span><span class="sxs-lookup"><span data-stu-id="94a6e-213">Add a folder named *Models*.</span></span>

* <span data-ttu-id="94a6e-214">Fügen Sie dem Ordner *Models* mit dem folgenden Code eine `TodoItem`-Klasse hinzu:</span><span class="sxs-lookup"><span data-stu-id="94a6e-214">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="94a6e-215">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="94a6e-215">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="94a6e-216">Klicken Sie mit der rechten Maustaste auf das Projekt.</span><span class="sxs-lookup"><span data-stu-id="94a6e-216">Right-click the project.</span></span> <span data-ttu-id="94a6e-217">Klicken Sie auf **Hinzufügen** > **Neuer Ordner**.</span><span class="sxs-lookup"><span data-stu-id="94a6e-217">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="94a6e-218">Geben Sie dem Ordner den Namen *Models* .</span><span class="sxs-lookup"><span data-stu-id="94a6e-218">Name the folder *Models*.</span></span>

  ![Neuer Ordner](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="94a6e-220">Klicken Sie mit der rechten Maustaste auf den Ordner *Models* , und wählen Sie **Hinzufügen** > **Neue Datei** > **Allgemein** > **Leere Klasse** aus.</span><span class="sxs-lookup"><span data-stu-id="94a6e-220">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="94a6e-221">Nennen Sie die Klasse *TodoItem*, und klicken Sie dann auf **Neu**.</span><span class="sxs-lookup"><span data-stu-id="94a6e-221">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="94a6e-222">Ersetzen Sie den Vorlagencode durch Folgendes:</span><span class="sxs-lookup"><span data-stu-id="94a6e-222">Replace the template code with the following:</span></span>

---

  [!code-csharp[](first-web-api/samples/5.x/TodoApi/Models/TodoItem.cs?name=snippet)]

<span data-ttu-id="94a6e-223">Die `Id`-Eigenschaft fungiert als eindeutiger Schlüssel in einer relationalen Datenbank.</span><span class="sxs-lookup"><span data-stu-id="94a6e-223">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="94a6e-224">Modellklassen können überall im Projekt platziert werden, doch gemäß der Konvention wird der Ordner *Models* verwendet.</span><span class="sxs-lookup"><span data-stu-id="94a6e-224">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="94a6e-225">Hinzufügen eines Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="94a6e-225">Add a database context</span></span>

<span data-ttu-id="94a6e-226">Der *Datenbankkontext* ist die Hauptklasse, die die Entity Framework-Funktionen für ein Datenmodell koordiniert.</span><span class="sxs-lookup"><span data-stu-id="94a6e-226">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="94a6e-227">Diese Klasse wird durch Ableiten von der <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName>-Klasse erstellt.</span><span class="sxs-lookup"><span data-stu-id="94a6e-227">This class is created by deriving from the <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName> class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="94a6e-228">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="94a6e-228">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-nuget-packages"></a><span data-ttu-id="94a6e-229">Hinzufügen von NuGet-Paketen</span><span class="sxs-lookup"><span data-stu-id="94a6e-229">Add NuGet packages</span></span>

* <span data-ttu-id="94a6e-230">Klicken Sie im Menü **Extras** auf **NuGet-Paket-Manager > NuGet-Pakete für Projektmappe verwalten...** .</span><span class="sxs-lookup"><span data-stu-id="94a6e-230">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="94a6e-231">Klicken Sie auf die Registerkarte **Durchsuchen**, und geben Sie dann **Microsoft.EntityFrameworkCore.SqlServer** in das Suchfeld ein.</span><span class="sxs-lookup"><span data-stu-id="94a6e-231">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.SqlServer** in the search box.</span></span>
<!-- https://github.com/dotnet/AspNetCore.Docs/issues/19782 Delete this line at RTM -->
* <span data-ttu-id="94a6e-232">Wählen Sie im linken Bereich **Microsoft.EntityFrameworkCore.SqlServer** aus.</span><span class="sxs-lookup"><span data-stu-id="94a6e-232">Select **Microsoft.EntityFrameworkCore.SqlServer** in the left pane.</span></span>
* <span data-ttu-id="94a6e-233">Aktivieren Sie das Kontrollkästchen **Projekt** im rechten Bereich, und klicken Sie dann auf **Installieren**.</span><span class="sxs-lookup"><span data-stu-id="94a6e-233">Select the **Project** check box in the right pane and then select **Install**.</span></span>
* <span data-ttu-id="94a6e-234">Verwenden Sie die Anweisungen oben zum Hinzuzufügen des **Microsoft.EntityFrameworkCore.InMemory**-NuGet-Pakets.</span><span class="sxs-lookup"><span data-stu-id="94a6e-234">Use the preceding instructions to add the **Microsoft.EntityFrameworkCore.InMemory** NuGet package.</span></span>

<!-- https://github.com/dotnet/AspNetCore.Docs/issues/19782 Update this image at RTM -->
<span data-ttu-id="94a6e-235">![NuGet-Paket-Manager](first-web-api/_static/5/vsNuGet.png)</span><span class="sxs-lookup"><span data-stu-id="94a6e-235">![NuGet Package Manager](first-web-api/_static/5/vsNuGet.png)</span></span>

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="94a6e-236">Hinzufügen des TodoContext-Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="94a6e-236">Add the TodoContext database context</span></span>

* <span data-ttu-id="94a6e-237">Klicken Sie mit der rechten Maustaste auf den Ordner *Models* , und wählen Sie **Hinzufügen** > **Klasse** aus.</span><span class="sxs-lookup"><span data-stu-id="94a6e-237">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="94a6e-238">Nennen Sie die Klasse *TodoContext*, und klicken Sie auf **Hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="94a6e-238">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="94a6e-239">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="94a6e-239">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="94a6e-240">Fügen Sie eine `TodoContext`-Klasse zum Ordner *Models* hinzu.</span><span class="sxs-lookup"><span data-stu-id="94a6e-240">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="94a6e-241">Geben Sie den folgenden Code ein:</span><span class="sxs-lookup"><span data-stu-id="94a6e-241">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/5.x/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="94a6e-242">Registrieren des Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="94a6e-242">Register the database context</span></span>

<span data-ttu-id="94a6e-243">In ASP.NET Core müssen Dienste wie der Datenbankkontext mit dem Container [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) registriert werden.</span><span class="sxs-lookup"><span data-stu-id="94a6e-243">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="94a6e-244">Der Container stellt den Dienst für Controller bereit.</span><span class="sxs-lookup"><span data-stu-id="94a6e-244">The container provides the service to controllers.</span></span>

<span data-ttu-id="94a6e-245">Aktualisieren Sie *Startup.cs* mit dem folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="94a6e-245">Update *Startup.cs* with the following code:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="94a6e-246">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="94a6e-246">The preceding code:</span></span>

* <span data-ttu-id="94a6e-247">Entfernt die Swagger-Aufrufe.</span><span class="sxs-lookup"><span data-stu-id="94a6e-247">Removes the Swagger calls.</span></span>
* <span data-ttu-id="94a6e-248">Entfernt nicht benötigte `using`-Deklarationen</span><span class="sxs-lookup"><span data-stu-id="94a6e-248">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="94a6e-249">Fügt dem Abhängigkeitscontainer den Datenbankkontext hinzu</span><span class="sxs-lookup"><span data-stu-id="94a6e-249">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="94a6e-250">Gibt an, dass der Datenbankkontext eine In-Memory Database verwendet</span><span class="sxs-lookup"><span data-stu-id="94a6e-250">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="94a6e-251">Erstellen eines Controllergerüsts</span><span class="sxs-lookup"><span data-stu-id="94a6e-251">Scaffold a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="94a6e-252">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="94a6e-252">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="94a6e-253">Klicken Sie mit der rechten Maustaste auf den Ordner *Controller*.</span><span class="sxs-lookup"><span data-stu-id="94a6e-253">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="94a6e-254">Wählen Sie **Hinzufügen** > **Neues Gerüstelement** aus.</span><span class="sxs-lookup"><span data-stu-id="94a6e-254">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="94a6e-255">Klicken Sie auf **API-Controller mit Aktionen unter Verwendung von Entity Framework** und dann auf **Hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="94a6e-255">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="94a6e-256">Führen Sie im Dialogfeld **API-Controller mit Aktionen unter Verwendung von Entity Framework** folgende Schritte aus:</span><span class="sxs-lookup"><span data-stu-id="94a6e-256">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="94a6e-257">Wählen Sie in der **Modellklasse** das Element **TodoItem (TodoApi.Models)** aus.</span><span class="sxs-lookup"><span data-stu-id="94a6e-257">Select **TodoItem (TodoApi.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="94a6e-258">Wählen Sie in der **Datenkontextklasse** das Element **TodoContext (TodoApi.Models)** aus.</span><span class="sxs-lookup"><span data-stu-id="94a6e-258">Select **TodoContext (TodoApi.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="94a6e-259">Wählen Sie **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="94a6e-259">Select **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="94a6e-260">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="94a6e-260">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="94a6e-261">Führen Sie die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="94a6e-261">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet tool update -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="94a6e-262">Die obenstehenden Befehle haben folgende Konsequenzen:</span><span class="sxs-lookup"><span data-stu-id="94a6e-262">The preceding commands:</span></span>

* <span data-ttu-id="94a6e-263">Die NuGet-Pakete für den Gerüstbau werden hinzufügt.</span><span class="sxs-lookup"><span data-stu-id="94a6e-263">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="94a6e-264">Die Gerüstbau-Engine (`dotnet-aspnet-codegenerator`) wird installiert.</span><span class="sxs-lookup"><span data-stu-id="94a6e-264">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="94a6e-265">Das Gerüst für `TodoItemsController` wird erstellt.</span><span class="sxs-lookup"><span data-stu-id="94a6e-265">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="94a6e-266">Der generierte Code hat folgende Auswirkungen:</span><span class="sxs-lookup"><span data-stu-id="94a6e-266">The generated code:</span></span>

* <span data-ttu-id="94a6e-267">Markiert die Klasse mit dem [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute)-Attribut.</span><span class="sxs-lookup"><span data-stu-id="94a6e-267">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="94a6e-268">Dieses Attribut gibt an, dass der Controller auf Web-API-Anforderungen reagiert.</span><span class="sxs-lookup"><span data-stu-id="94a6e-268">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="94a6e-269">Weitere Informationen zu bestimmten Verhaltensweisen, die das Attribut ermöglicht, finden Sie unter <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="94a6e-269">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="94a6e-270">Verwendet die Abhängigkeitsinjektion, um den Datenbankkontext (`TodoContext`) dem Controller hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="94a6e-270">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="94a6e-271">Der Datenbankkontext wird in den einzelnen [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete)-Methoden im Controller verwendet.</span><span class="sxs-lookup"><span data-stu-id="94a6e-271">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<span data-ttu-id="94a6e-272">Die ASP.NET Core-Vorlagen für:</span><span class="sxs-lookup"><span data-stu-id="94a6e-272">The ASP.NET Core templates for:</span></span>

* <span data-ttu-id="94a6e-273">Controller mit Ansichten enthalten `[action]` in der Routenvorlage.</span><span class="sxs-lookup"><span data-stu-id="94a6e-273">Controllers with views include `[action]` in the route template.</span></span>
* <span data-ttu-id="94a6e-274">API-Controller enthalten keine `[action]` in der Routenvorlage.</span><span class="sxs-lookup"><span data-stu-id="94a6e-274">API controllers don't include `[action]` in the route template.</span></span>

<span data-ttu-id="94a6e-275">Wenn sich das `[action]`-Token nicht in der Routenvorlage befindet, wird der [action](xref:mvc/controllers/routing#action)-Name von der Route ausgeschlossen.</span><span class="sxs-lookup"><span data-stu-id="94a6e-275">When the `[action]` token isn't in the route template, the [action](xref:mvc/controllers/routing#action) name is excluded from the route.</span></span> <span data-ttu-id="94a6e-276">Dies bedeutet, dass der zugehörige Methodenname der Aktion nicht in der übereinstimmenden Route verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="94a6e-276">That is, the action's associated method name isn't used in the matching route.</span></span>

## <a name="update-the-posttodoitem-create-method"></a><span data-ttu-id="94a6e-277">Aktualisieren der PostTodoItem-Erstellungsmethode</span><span class="sxs-lookup"><span data-stu-id="94a6e-277">Update the PostTodoItem create method</span></span>

<span data-ttu-id="94a6e-278">Ersetzen Sie die Rückgabeanweisung in `PostTodoItem` durch eine Anweisung mit dem [nameof](/dotnet/csharp/language-reference/operators/nameof)-Operator:</span><span class="sxs-lookup"><span data-stu-id="94a6e-278">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="94a6e-279">Der oben stehende Code ist eine HTTP POST-Methode, wie durch das [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute)-Attribut angegeben.</span><span class="sxs-lookup"><span data-stu-id="94a6e-279">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="94a6e-280">Die Methode ruft den Wert der Aufgabe aus dem Text der HTTP-Anforderung ab.</span><span class="sxs-lookup"><span data-stu-id="94a6e-280">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="94a6e-281">Weitere Informationen finden Sie unter [Attributrouting mit Http[Verb]-Attributen](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="94a6e-281">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="94a6e-282">Die <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*>-Methode:</span><span class="sxs-lookup"><span data-stu-id="94a6e-282">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="94a6e-283">Gibt bei Erfolg den [HTTP-Statuscode 201](https://developer.mozilla.org/docs/Web/HTTP/Status/201) zurück.</span><span class="sxs-lookup"><span data-stu-id="94a6e-283">Returns an [HTTP 201 status code](https://developer.mozilla.org/docs/Web/HTTP/Status/201) if successful.</span></span> <span data-ttu-id="94a6e-284">HTTP 201 ist die Standardantwort für eine HTTP POST-Methode, die eine neue Ressource auf dem Server erstellt.</span><span class="sxs-lookup"><span data-stu-id="94a6e-284">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="94a6e-285">Fügt der Antwort einen [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location)-Header hinzu.</span><span class="sxs-lookup"><span data-stu-id="94a6e-285">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="94a6e-286">Der `Location`-Header legt den [URI](https://developer.mozilla.org/docs/Glossary/URI) des neu erstellten To-do-Elements fest.</span><span class="sxs-lookup"><span data-stu-id="94a6e-286">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="94a6e-287">Weitere Informationen finden Sie unter [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="94a6e-287">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="94a6e-288">Verweist auf die `GetTodoItem`-Aktion zum Erstellen des URIs des `Location`-Headers.</span><span class="sxs-lookup"><span data-stu-id="94a6e-288">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="94a6e-289">Das `nameof`-Schlüsselwort von C# wird verwendet, um eine Hartcodierung des Aktionsnamens im `CreatedAtAction`-Aufruf zu vermeiden.</span><span class="sxs-lookup"><span data-stu-id="94a6e-289">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="94a6e-290">Installieren von Postman</span><span class="sxs-lookup"><span data-stu-id="94a6e-290">Install Postman</span></span>

<span data-ttu-id="94a6e-291">Dieses Tutorial verwendet Postman zum Testen der Web-API.</span><span class="sxs-lookup"><span data-stu-id="94a6e-291">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="94a6e-292">Installieren Sie [Postman](https://www.getpostman.com/downloads/).</span><span class="sxs-lookup"><span data-stu-id="94a6e-292">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="94a6e-293">Starten Sie die Web-App.</span><span class="sxs-lookup"><span data-stu-id="94a6e-293">Start the web app.</span></span>
* <span data-ttu-id="94a6e-294">Starten Sie Postman.</span><span class="sxs-lookup"><span data-stu-id="94a6e-294">Start Postman.</span></span>
* <span data-ttu-id="94a6e-295">Deaktivieren Sie **SSL certificate verification** (Verifizierung des SSL-Zertifikats).</span><span class="sxs-lookup"><span data-stu-id="94a6e-295">Disable **SSL certificate verification**</span></span>
  * <span data-ttu-id="94a6e-296">Deaktivieren Sie auf der Registerkarte **General** (Allgemein) unter File > Settings (**Datei** > **Einstellungen**) **SSL certificate verification** (Verifizierung des SSL-Zertifikats).</span><span class="sxs-lookup"><span data-stu-id="94a6e-296">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="94a6e-297">Aktivieren Sie die Verifizierung des SSL-Zertifikats wieder, nachdem Sie den Controller getestet haben.</span><span class="sxs-lookup"><span data-stu-id="94a6e-297">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="94a6e-298">Testen von PostTodoItem mit Postman</span><span class="sxs-lookup"><span data-stu-id="94a6e-298">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="94a6e-299">Erstellen Sie eine neue Anforderung.</span><span class="sxs-lookup"><span data-stu-id="94a6e-299">Create a new request.</span></span>
* <span data-ttu-id="94a6e-300">Legen Sie als HTTP-Methode `POST` fest.</span><span class="sxs-lookup"><span data-stu-id="94a6e-300">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="94a6e-301">Legen Sie den URI auf `https://localhost:<port>/api/TodoItems` fest.</span><span class="sxs-lookup"><span data-stu-id="94a6e-301">Set the URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="94a6e-302">Beispiel: `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="94a6e-302">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="94a6e-303">Wählen Sie die Registerkarte **Body** (Text) aus.</span><span class="sxs-lookup"><span data-stu-id="94a6e-303">Select the **Body** tab.</span></span>
* <span data-ttu-id="94a6e-304">Wählen Sie das Optionsfeld **raw** (Unformatiert) aus.</span><span class="sxs-lookup"><span data-stu-id="94a6e-304">Select the **raw** radio button.</span></span>
* <span data-ttu-id="94a6e-305">Legen Sie den Typ auf **JSON (application/json)** fest.</span><span class="sxs-lookup"><span data-stu-id="94a6e-305">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="94a6e-306">Geben Sie für die Aufgabe den Anforderungstext im JSON-Format ein:</span><span class="sxs-lookup"><span data-stu-id="94a6e-306">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="94a6e-307">Wählen Sie **Send** (Senden) aus.</span><span class="sxs-lookup"><span data-stu-id="94a6e-307">Select **Send**.</span></span>

  ![Postman mit erstellter Anforderung](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri"></a><span data-ttu-id="94a6e-309">Testen des Adressheader-URIs</span><span class="sxs-lookup"><span data-stu-id="94a6e-309">Test the location header URI</span></span>

<span data-ttu-id="94a6e-310">Der Location-Header-URI kann im Browser getestet werden.</span><span class="sxs-lookup"><span data-stu-id="94a6e-310">The location header URI can be tested in the browser.</span></span> <span data-ttu-id="94a6e-311">Kopieren Sie den Location-Header-URI, und fügen Sie ihn im Browser ein.</span><span class="sxs-lookup"><span data-stu-id="94a6e-311">Copy and paste the location header URI into the browser.</span></span>

<span data-ttu-id="94a6e-312">So testen Sie in Postman:</span><span class="sxs-lookup"><span data-stu-id="94a6e-312">To test in Postman:</span></span>

* <span data-ttu-id="94a6e-313">Wählen Sie auf der Registerkarte **Header** (Header) den Bereich **Response** (Antwort) aus.</span><span class="sxs-lookup"><span data-stu-id="94a6e-313">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="94a6e-314">Kopieren Sie den den Headerwert von **Location** (Speicherort):</span><span class="sxs-lookup"><span data-stu-id="94a6e-314">Copy the **Location** header value:</span></span>

  ![Registerkarte „Header“ in der Postman-Konsole](first-web-api/_static/3/create.png)

* <span data-ttu-id="94a6e-316">Legen Sie als HTTP-Methode `GET` fest.</span><span class="sxs-lookup"><span data-stu-id="94a6e-316">Set the HTTP method to `GET`.</span></span>
* <span data-ttu-id="94a6e-317">Legen Sie den URI auf `https://localhost:<port>/api/TodoItems/1` fest.</span><span class="sxs-lookup"><span data-stu-id="94a6e-317">Set the URI to `https://localhost:<port>/api/TodoItems/1`.</span></span> <span data-ttu-id="94a6e-318">Beispiel: `https://localhost:5001/api/TodoItems/1`.</span><span class="sxs-lookup"><span data-stu-id="94a6e-318">For example, `https://localhost:5001/api/TodoItems/1`.</span></span>
* <span data-ttu-id="94a6e-319">Wählen Sie **Send** (Senden) aus.</span><span class="sxs-lookup"><span data-stu-id="94a6e-319">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="94a6e-320">Überblick über die GET-Methoden</span><span class="sxs-lookup"><span data-stu-id="94a6e-320">Examine the GET methods</span></span>

<span data-ttu-id="94a6e-321">Zwei GET-Endpunkte werden implementiert:</span><span class="sxs-lookup"><span data-stu-id="94a6e-321">Two GET endpoints are implemented:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="94a6e-322">Testen Sie die App, indem Sie die beiden Endpunkte in einem Browser oder über Postman aufrufen.</span><span class="sxs-lookup"><span data-stu-id="94a6e-322">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="94a6e-323">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="94a6e-323">For example:</span></span>

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

<span data-ttu-id="94a6e-324">Durch einen Aufruf von `GetTodoItems` wird eine Antwort gesendet, die der folgenden ähnelt:</span><span class="sxs-lookup"><span data-stu-id="94a6e-324">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="94a6e-325">Testen von GET mit Postman</span><span class="sxs-lookup"><span data-stu-id="94a6e-325">Test Get with Postman</span></span>

* <span data-ttu-id="94a6e-326">Erstellen Sie eine neue Anforderung.</span><span class="sxs-lookup"><span data-stu-id="94a6e-326">Create a new request.</span></span>
* <span data-ttu-id="94a6e-327">Legen Sie die HTTP-Methode auf **GET** fest.</span><span class="sxs-lookup"><span data-stu-id="94a6e-327">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="94a6e-328">Legen Sie den Anforderungs-URI auf `https://localhost:<port>/api/TodoItems` fest.</span><span class="sxs-lookup"><span data-stu-id="94a6e-328">Set the request URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="94a6e-329">Beispielsweise `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="94a6e-329">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="94a6e-330">Wählen Sie in Postman **Two pane view** (Ansicht mit zwei Bereichen) aus.</span><span class="sxs-lookup"><span data-stu-id="94a6e-330">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="94a6e-331">Wählen Sie **Send** (Senden) aus.</span><span class="sxs-lookup"><span data-stu-id="94a6e-331">Select **Send**.</span></span>

<span data-ttu-id="94a6e-332">Diese App verwendet eine In-Memory-Datenbank.</span><span class="sxs-lookup"><span data-stu-id="94a6e-332">This app uses an in-memory database.</span></span> <span data-ttu-id="94a6e-333">Wenn die App angehalten und dann wieder gestartet wird, werden durch die vorherige GET-Anforderung keine Daten zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="94a6e-333">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="94a6e-334">Wenn keine Daten zurückgegeben werden, senden Sie mit [POST](#post) Daten an die App.</span><span class="sxs-lookup"><span data-stu-id="94a6e-334">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="94a6e-335">Routing und URL-Pfade</span><span class="sxs-lookup"><span data-stu-id="94a6e-335">Routing and URL paths</span></span>

<span data-ttu-id="94a6e-336">Das Attribut [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) gibt eine Methode an, die auf eine HTTP GET-Anforderung antwortet.</span><span class="sxs-lookup"><span data-stu-id="94a6e-336">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="94a6e-337">Der URL-Pfad für jede Methode wird wie folgt erstellt:</span><span class="sxs-lookup"><span data-stu-id="94a6e-337">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="94a6e-338">Beginnen Sie mit der Vorlagenzeichenfolge im `Route`-Attribut des Controllers:</span><span class="sxs-lookup"><span data-stu-id="94a6e-338">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="94a6e-339">Ersetzen Sie `[controller]` durch den Namen des Controllers, bei dem es sich standardmäßig um den Namen der Controller-Klasse ohne das Suffix „Controller“ handelt.</span><span class="sxs-lookup"><span data-stu-id="94a6e-339">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="94a6e-340">In diesem Beispiel ist der Klassenname des Controllers „**TodoItems** Controller“. Der Controllername lautet also „TodoItems“.</span><span class="sxs-lookup"><span data-stu-id="94a6e-340">For this sample, the controller class name is **TodoItems** Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="94a6e-341">Beim ASP.NET Core-[Routing](xref:mvc/controllers/routing) wird die Groß-/Kleinschreibung nicht beachtet.</span><span class="sxs-lookup"><span data-stu-id="94a6e-341">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="94a6e-342">Wenn das `[HttpGet]`-Attribut eine Routenvorlage (z. B. `[HttpGet("products")]`) hat, fügen Sie diese an den Pfad an.</span><span class="sxs-lookup"><span data-stu-id="94a6e-342">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="94a6e-343">In diesem Beispiel wird keine Vorlage verwendet.</span><span class="sxs-lookup"><span data-stu-id="94a6e-343">This sample doesn't use a template.</span></span> <span data-ttu-id="94a6e-344">Weitere Informationen finden Sie unter [Attributrouting mit Http[Verb]-Attributen](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="94a6e-344">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="94a6e-345">In der folgenden `GetTodoItem`-Methode ist `"{id}"` eine Platzhaltervariable für den eindeutigen Bezeichner des To-do-Elements.</span><span class="sxs-lookup"><span data-stu-id="94a6e-345">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="94a6e-346">Wenn `GetTodoItem` aufgerufen wird, wird der Wert von `"{id}"` in der URL der Methode in ihrem Parameter `id` bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="94a6e-346">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="94a6e-347">Rückgabewerte</span><span class="sxs-lookup"><span data-stu-id="94a6e-347">Return values</span></span>

<span data-ttu-id="94a6e-348">Der Rückgabetyp der Methoden `GetTodoItems` und `GetTodoItem` ist der [ActionResult\<T>-Typ](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="94a6e-348">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="94a6e-349">ASP.NET Core serialisiert automatisch das Objekt in [JSON](https://www.json.org/) und schreibt den JSON-Code in den Text der Antwortnachricht.</span><span class="sxs-lookup"><span data-stu-id="94a6e-349">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="94a6e-350">Der Antwortcode für diesen Rückgabetyp ist [200 OK](https://developer.mozilla.org/docs/Web/HTTP/Status/200), vorausgesetzt, es gibt keine Ausnahmefehler.</span><span class="sxs-lookup"><span data-stu-id="94a6e-350">The response code for this return type is [200 OK](https://developer.mozilla.org/docs/Web/HTTP/Status/200), assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="94a6e-351">Nicht behandelte Ausnahmen werden in 5xx-Fehler übersetzt.</span><span class="sxs-lookup"><span data-stu-id="94a6e-351">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="94a6e-352">`ActionResult`-Rückgabetypen können eine Vielzahl von HTTP-Statuscodes darstellen.</span><span class="sxs-lookup"><span data-stu-id="94a6e-352">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="94a6e-353">Beispielsweise kann `GetTodoItem` zwei verschiedene Statuswerte zurückgeben:</span><span class="sxs-lookup"><span data-stu-id="94a6e-353">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="94a6e-354">Wenn kein Element mit der angeforderten ID übereinstimmt, gibt die Methode einen <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A>-Fehlercode [Status 404](https://developer.mozilla.org/docs/Web/HTTP/Status/404) zurück.</span><span class="sxs-lookup"><span data-stu-id="94a6e-354">If no item matches the requested ID, the method returns a [404 status](https://developer.mozilla.org/docs/Web/HTTP/Status/404) <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="94a6e-355">Andernfalls gibt die Methode 200 mit einem JSON-Antworttext zurück.</span><span class="sxs-lookup"><span data-stu-id="94a6e-355">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="94a6e-356">Die Rückgabe von `item` löst eine HTTP 200-Antwort aus.</span><span class="sxs-lookup"><span data-stu-id="94a6e-356">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="94a6e-357">PutTodoItem-Methode</span><span class="sxs-lookup"><span data-stu-id="94a6e-357">The PutTodoItem method</span></span>

<span data-ttu-id="94a6e-358">Untersuchen Sie die `PutTodoItem`-Methode.</span><span class="sxs-lookup"><span data-stu-id="94a6e-358">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="94a6e-359">`PutTodoItem` ähnelt `PostTodoItem`, verwendet allerdings HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="94a6e-359">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="94a6e-360">Die Antwort ist [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="94a6e-360">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="94a6e-361">Gemäß der HTTP-Spezifikation erfordert eine PUT-Anforderung, dass der Client die gesamte aktualisierte Entität (nicht nur die Änderungen) sendet.</span><span class="sxs-lookup"><span data-stu-id="94a6e-361">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="94a6e-362">Verwenden Sie [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute), um Teilupdates zu unterstützen.</span><span class="sxs-lookup"><span data-stu-id="94a6e-362">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="94a6e-363">Wenn beim Aufrufen von `PutTodoItem` ein Fehler zurückgegeben wird, rufen Sie `GET` auf, um sicherzustellen, dass die Datenbank ein Element enthält.</span><span class="sxs-lookup"><span data-stu-id="94a6e-363">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="94a6e-364">Testen der PutTodoItem-Methode</span><span class="sxs-lookup"><span data-stu-id="94a6e-364">Test the PutTodoItem method</span></span>

<span data-ttu-id="94a6e-365">In diesem Beispiel wird eine In-Memory-Datenbank verwendet, die jedes Mal initialisiert werden muss, wenn die App gestartet wird.</span><span class="sxs-lookup"><span data-stu-id="94a6e-365">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="94a6e-366">Es muss ein Element in der Datenbank vorhanden sein, bevor Sie einen PUT-Aufruf durchführen.</span><span class="sxs-lookup"><span data-stu-id="94a6e-366">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="94a6e-367">Rufen Sie vor einem PUT-Aufruf GET auf, um sicherzustellen, dass ein Element in der Datenbank vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="94a6e-367">Call GET to ensure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="94a6e-368">Aktualisieren Sie das To-do-Element, das über den ID-Wert 1 verfügt, und legen Sie als Namen `"feed fish"` fest:</span><span class="sxs-lookup"><span data-stu-id="94a6e-368">Update the to-do item that has Id = 1 and set its name to `"feed fish"`:</span></span>

```json
  {
    "Id":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="94a6e-369">Die folgende Abbildung zeigt das Postman-Update:</span><span class="sxs-lookup"><span data-stu-id="94a6e-369">The following image shows the Postman update:</span></span>

![Postman-Konsole mit der Antwort „204 (Kein Inhalt)“](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="94a6e-371">DeleteTodoItem-Methode</span><span class="sxs-lookup"><span data-stu-id="94a6e-371">The DeleteTodoItem method</span></span>

<span data-ttu-id="94a6e-372">Untersuchen Sie die `DeleteTodoItem`-Methode.</span><span class="sxs-lookup"><span data-stu-id="94a6e-372">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="94a6e-373">Testen der DeleteTodoItem-Methode</span><span class="sxs-lookup"><span data-stu-id="94a6e-373">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="94a6e-374">So löschen Sie mit Postman eine Aufgabe</span><span class="sxs-lookup"><span data-stu-id="94a6e-374">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="94a6e-375">Legen Sie die Methode auf `DELETE` fest.</span><span class="sxs-lookup"><span data-stu-id="94a6e-375">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="94a6e-376">Legen Sie den URI des zu löschenden Objekts fest, z. B. `https://localhost:5001/api/TodoItems/1`.</span><span class="sxs-lookup"><span data-stu-id="94a6e-376">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="94a6e-377">Wählen Sie **Send** (Senden) aus.</span><span class="sxs-lookup"><span data-stu-id="94a6e-377">Select **Send**.</span></span>

<a name="over-post-v5"></a>

## <a name="prevent-over-posting"></a><span data-ttu-id="94a6e-378">Vermeiden von Overposting</span><span class="sxs-lookup"><span data-stu-id="94a6e-378">Prevent over-posting</span></span>

<span data-ttu-id="94a6e-379">Derzeit macht die Beispiel-App das gesamte `TodoItem`-Objekt verfügbar.</span><span class="sxs-lookup"><span data-stu-id="94a6e-379">Currently the sample app exposes the entire `TodoItem` object.</span></span> <span data-ttu-id="94a6e-380">In den Produktions-Apps sind die Daten, die eingegeben und mithilfe einer Teilmenge des Modells zurückgegeben werden, in der Regel eingeschränkt.</span><span class="sxs-lookup"><span data-stu-id="94a6e-380">Production apps typically limit the data that's input and returned using a subset of the model.</span></span> <span data-ttu-id="94a6e-381">Hierfür gibt es mehrere Gründe, wobei die Sicherheit einer der Hauptgründe ist.</span><span class="sxs-lookup"><span data-stu-id="94a6e-381">There are multiple reasons behind this and security is a major one.</span></span> <span data-ttu-id="94a6e-382">Die Teilmenge eines Modells wird üblicherweise als Datenübertragungsobjekt (DTO, Data Transfer Object), Eingabemodell oder Anzeigemodell bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="94a6e-382">The subset of a model is usually referred to as a Data Transfer Object (DTO), input model, or view model.</span></span> <span data-ttu-id="94a6e-383">In diesem Artikel wird das **DTO** verwendet.</span><span class="sxs-lookup"><span data-stu-id="94a6e-383">**DTO** is used in this article.</span></span>

<span data-ttu-id="94a6e-384">Ein DTO kann für Folgendes verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="94a6e-384">A DTO may be used to:</span></span>

* <span data-ttu-id="94a6e-385">Vermeiden Sie Overposting.</span><span class="sxs-lookup"><span data-stu-id="94a6e-385">Prevent over-posting.</span></span>
* <span data-ttu-id="94a6e-386">Ausblenden von Eigenschaften, die Clients nicht anzeigen sollen</span><span class="sxs-lookup"><span data-stu-id="94a6e-386">Hide properties that clients are not supposed to view.</span></span>
* <span data-ttu-id="94a6e-387">Auslassen einiger Eigenschaften, um die Nutzlast zu verringern</span><span class="sxs-lookup"><span data-stu-id="94a6e-387">Omit some properties in order to reduce payload size.</span></span>
* <span data-ttu-id="94a6e-388">Vereinfachen von Objektgraphen, die geschachtelte Objekte enthalten</span><span class="sxs-lookup"><span data-stu-id="94a6e-388">Flatten object graphs that contain nested objects.</span></span> <span data-ttu-id="94a6e-389">Vereinfachte Objektgraphen können für Clients zweckmäßiger sein.</span><span class="sxs-lookup"><span data-stu-id="94a6e-389">Flattened object graphs can be more convenient for clients.</span></span>

<span data-ttu-id="94a6e-390">Um den DTO-Ansatz zu veranschaulichen, aktualisieren Sie die `TodoItem`-Klasse, sodass sie ein geheimes Feld einschließt:</span><span class="sxs-lookup"><span data-stu-id="94a6e-390">To demonstrate the DTO approach, update the `TodoItem` class to include a secret field:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApiDTO/Models/TodoItem.cs?name=snippet&highlight=8)]

<span data-ttu-id="94a6e-391">Das geheime Feld muss in dieser App ausgeblendet werden, eine administrative App kann es jedoch verfügbar machen.</span><span class="sxs-lookup"><span data-stu-id="94a6e-391">The secret field needs to be hidden from this app, but an administrative app could choose to expose it.</span></span>

<span data-ttu-id="94a6e-392">Vergewissern Sie sich, dass Sie das geheime Feld veröffentlichen und abrufen können.</span><span class="sxs-lookup"><span data-stu-id="94a6e-392">Verify you can post and get the secret field.</span></span>

<span data-ttu-id="94a6e-393">Erstellen Sie ein DTO-Modell:</span><span class="sxs-lookup"><span data-stu-id="94a6e-393">Create a DTO model:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApiDTO/Models/TodoItemDTO.cs?name=snippet)]

<span data-ttu-id="94a6e-394">Aktualisieren Sie `TodoItemsController`, sodass `TodoItemDTO` verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="94a6e-394">Update the `TodoItemsController` to use `TodoItemDTO`:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

<span data-ttu-id="94a6e-395">Vergewissern Sie sich, dass Sie das geheime Feld weder veröffentlichen noch abrufen können.</span><span class="sxs-lookup"><span data-stu-id="94a6e-395">Verify you can't post or get the secret field.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="94a6e-396">Aufrufen der Web-API mit JavaScript</span><span class="sxs-lookup"><span data-stu-id="94a6e-396">Call the web API with JavaScript</span></span>

<span data-ttu-id="94a6e-397">Mehr dazu finden Sie im [-Tutorial: Aufrufen einer ASP.NET Core-Web-API mit JavaScript](xref:tutorials/web-api-javascript).</span><span class="sxs-lookup"><span data-stu-id="94a6e-397">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="94a6e-398">In diesem Tutorial lernen Sie, wie die folgenden Aufgaben ausgeführt werden:</span><span class="sxs-lookup"><span data-stu-id="94a6e-398">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="94a6e-399">Erstellen eines Web-API-Projekts</span><span class="sxs-lookup"><span data-stu-id="94a6e-399">Create a web API project.</span></span>
> * <span data-ttu-id="94a6e-400">Hinzufügen einer Modellklasse und eines Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="94a6e-400">Add a model class and a database context.</span></span>
> * <span data-ttu-id="94a6e-401">Erstellen eines Controllergerüsts mit CRUD-Methoden</span><span class="sxs-lookup"><span data-stu-id="94a6e-401">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="94a6e-402">Konfigurieren des Routings, der URL-Pfade und der Rückgabewerte</span><span class="sxs-lookup"><span data-stu-id="94a6e-402">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="94a6e-403">Aufrufen der Web-API mit Postman</span><span class="sxs-lookup"><span data-stu-id="94a6e-403">Call the web API with Postman.</span></span>

<span data-ttu-id="94a6e-404">Abschließend steht Ihnen eine Web-API zur Verfügung, die in einer relationalen Datenbank gespeicherte To-do-Elemente verwalten kann.</span><span class="sxs-lookup"><span data-stu-id="94a6e-404">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="94a6e-405">Übersicht</span><span class="sxs-lookup"><span data-stu-id="94a6e-405">Overview</span></span>

<span data-ttu-id="94a6e-406">In diesem Tutorial wird die folgende API erstellt:</span><span class="sxs-lookup"><span data-stu-id="94a6e-406">This tutorial creates the following API:</span></span>

|<span data-ttu-id="94a6e-407">API</span><span class="sxs-lookup"><span data-stu-id="94a6e-407">API</span></span> | <span data-ttu-id="94a6e-408">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="94a6e-408">Description</span></span> | <span data-ttu-id="94a6e-409">Anforderungstext</span><span class="sxs-lookup"><span data-stu-id="94a6e-409">Request body</span></span> | <span data-ttu-id="94a6e-410">Antworttext</span><span class="sxs-lookup"><span data-stu-id="94a6e-410">Response body</span></span> |
|--- | ---- | ---- | ---- |
|`GET /api/TodoItems` | <span data-ttu-id="94a6e-411">Alle To-do-Elemente abrufen</span><span class="sxs-lookup"><span data-stu-id="94a6e-411">Get all to-do items</span></span> | <span data-ttu-id="94a6e-412">Keine</span><span class="sxs-lookup"><span data-stu-id="94a6e-412">None</span></span> | <span data-ttu-id="94a6e-413">Array von To-do-Elementen</span><span class="sxs-lookup"><span data-stu-id="94a6e-413">Array of to-do items</span></span>|
|`GET /api/TodoItems/{id}` | <span data-ttu-id="94a6e-414">Ein Element nach ID abrufen</span><span class="sxs-lookup"><span data-stu-id="94a6e-414">Get an item by ID</span></span> | <span data-ttu-id="94a6e-415">Keine</span><span class="sxs-lookup"><span data-stu-id="94a6e-415">None</span></span> | <span data-ttu-id="94a6e-416">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="94a6e-416">To-do item</span></span>|
|`POST /api/TodoItems` | <span data-ttu-id="94a6e-417">Neues Element hinzufügen</span><span class="sxs-lookup"><span data-stu-id="94a6e-417">Add a new item</span></span> | <span data-ttu-id="94a6e-418">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="94a6e-418">To-do item</span></span> | <span data-ttu-id="94a6e-419">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="94a6e-419">To-do item</span></span> |
|`PUT /api/TodoItems/{id}` | <span data-ttu-id="94a6e-420">Vorhandenes Element aktualisieren &nbsp;</span><span class="sxs-lookup"><span data-stu-id="94a6e-420">Update an existing item &nbsp;</span></span> | <span data-ttu-id="94a6e-421">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="94a6e-421">To-do item</span></span> | <span data-ttu-id="94a6e-422">Keine</span><span class="sxs-lookup"><span data-stu-id="94a6e-422">None</span></span> |
|<span data-ttu-id="94a6e-423">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="94a6e-423">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span></span> | <span data-ttu-id="94a6e-424">Löschen eines Elements &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="94a6e-424">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="94a6e-425">Keine</span><span class="sxs-lookup"><span data-stu-id="94a6e-425">None</span></span> | <span data-ttu-id="94a6e-426">Keine</span><span class="sxs-lookup"><span data-stu-id="94a6e-426">None</span></span>|

<span data-ttu-id="94a6e-427">Das folgende Diagramm zeigt den Entwurf der App.</span><span class="sxs-lookup"><span data-stu-id="94a6e-427">The following diagram shows the design of the app.</span></span>

![Der Client ist das Feld ganz links.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="94a6e-433">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="94a6e-433">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="94a6e-434">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="94a6e-434">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="94a6e-435">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="94a6e-435">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="94a6e-436">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="94a6e-436">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="94a6e-437">Erstellen eines Webprojekts</span><span class="sxs-lookup"><span data-stu-id="94a6e-437">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="94a6e-438">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="94a6e-438">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="94a6e-439">Klicken Sie im Menü **Datei** auf **Neu** > **Projekt**.</span><span class="sxs-lookup"><span data-stu-id="94a6e-439">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="94a6e-440">Wählen Sie die Vorlage **ASP.NET Core-Webanwendung** aus, und klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="94a6e-440">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="94a6e-441">Geben Sie dem Projekt den Namen *TodoApi*, und klicken Sie auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="94a6e-441">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="94a6e-442">Vergewissern Sie sich, dass im Dialogfeld **Neue ASP.NET Core-Webanwendung erstellen** die Optionen **.NET Core** und **ASP.NET Core 3.1** ausgewählt sind.</span><span class="sxs-lookup"><span data-stu-id="94a6e-442">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.1** are selected.</span></span> <span data-ttu-id="94a6e-443">Wählen Sie die Vorlage **API** aus, und klicken Sie auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="94a6e-443">Select the **API** template and click **Create**.</span></span>

![VS-Dialogfeld „Neues Projekt“](first-web-api/_static/vs3.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="94a6e-445">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="94a6e-445">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="94a6e-446">Öffnen Sie das [integrierte Terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="94a6e-446">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="94a6e-447">Wechseln Sie mit dem Befehl `cd`' zu dem Ordner, der den Projektordner enthalten soll.</span><span class="sxs-lookup"><span data-stu-id="94a6e-447">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="94a6e-448">Führen Sie die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="94a6e-448">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="94a6e-449">Wenn Sie in einem Dialogfeld angeben müssen, ob Sie dem Projekt die erforderlichen Elemente hinzufügen möchten, wählen Sie **Ja** aus.</span><span class="sxs-lookup"><span data-stu-id="94a6e-449">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="94a6e-450">Die obenstehenden Befehle haben folgende Konsequenzen:</span><span class="sxs-lookup"><span data-stu-id="94a6e-450">The preceding commands:</span></span>

  * <span data-ttu-id="94a6e-451">Ein neues Web-API-Projekt wird erstellt und in Visual Studio Code geöffnet.</span><span class="sxs-lookup"><span data-stu-id="94a6e-451">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="94a6e-452">Die NuGet-Pakete, die im nächsten Abschnitt erforderlich sind, werden hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="94a6e-452">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="94a6e-453">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="94a6e-453">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="94a6e-454">Klicken Sie auf **Datei** > **Neue Projektmappe**.</span><span class="sxs-lookup"><span data-stu-id="94a6e-454">Select **File** > **New Solution**.</span></span>

  ![Neue Projektmappe in macOS](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="94a6e-456">Navigieren Sie in Visual Studio für Mac-Versionen vor Version 8.6 zu **.NET Core** > **App** > **API** > **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="94a6e-456">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="94a6e-457">Bei Version 8.6 oder höher klicken Sie auf **Web and Console** > **App** > **API** > **Weiter** (Web und Konsole).</span><span class="sxs-lookup"><span data-stu-id="94a6e-457">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>

  ![Auswählen von macOS-API-Vorlagen](first-web-api-mac/_static/api_template.png)

* <span data-ttu-id="94a6e-459">Wählen Sie im Dialogfeld **Neue ASP.NET Core-Web-API konfigurieren** die neueste .NET Core 3.x-Version als **Zielframework** aus.</span><span class="sxs-lookup"><span data-stu-id="94a6e-459">In the **Configure the new ASP.NET Core Web API** dialog, select the latest .NET Core 3.x **Target Framework**.</span></span> <span data-ttu-id="94a6e-460">Klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="94a6e-460">Select **Next**.</span></span>

* <span data-ttu-id="94a6e-461">Geben Sie für **Projektname** *TodoApi* ein, und wählen Sie dann **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="94a6e-461">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![Dialogfeld „Konfiguration“](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="94a6e-463">Öffnen Sie im Projektordner ein Befehlsterminal, und führen Sie die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="94a6e-463">Open a command terminal in the project folder and run the following commands:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-api"></a><span data-ttu-id="94a6e-464">Testen der API</span><span class="sxs-lookup"><span data-stu-id="94a6e-464">Test the API</span></span>

<span data-ttu-id="94a6e-465">Die Projektvorlage erstellt eine `WeatherForecast`-API.</span><span class="sxs-lookup"><span data-stu-id="94a6e-465">The project template creates a `WeatherForecast` API.</span></span> <span data-ttu-id="94a6e-466">Rufen Sie in einem Browser die `Get`-Methode zum Testen der App auf.</span><span class="sxs-lookup"><span data-stu-id="94a6e-466">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="94a6e-467">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="94a6e-467">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="94a6e-468">Drücken Sie STRG+F5, um die App auszuführen.</span><span class="sxs-lookup"><span data-stu-id="94a6e-468">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="94a6e-469">Visual Studio startet einen Browser und navigiert zu `https://localhost:<port>/WeatherForecast`, wobei es sich bei `<port>` um eine zufällig ausgewählte Portnummer handelt.</span><span class="sxs-lookup"><span data-stu-id="94a6e-469">Visual Studio launches a browser and navigates to `https://localhost:<port>/WeatherForecast`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="94a6e-470">Wenn Sie in einem Dialogfeld angeben müssen, ob Sie dem IIS Express-Zertifikat vertrauen, wählen Sie **Ja** aus.</span><span class="sxs-lookup"><span data-stu-id="94a6e-470">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="94a6e-471">Wählen Sie im folgenden Dialogfeld **Sicherheitswarnung** die Option **Ja** aus.</span><span class="sxs-lookup"><span data-stu-id="94a6e-471">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="94a6e-472">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="94a6e-472">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="94a6e-473">Drücken Sie STRG+F5, um die App auszuführen.</span><span class="sxs-lookup"><span data-stu-id="94a6e-473">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="94a6e-474">Navigieren Sie in einem Browser zur folgenden URL: `https://localhost:5001/WeatherForecast`.</span><span class="sxs-lookup"><span data-stu-id="94a6e-474">In a browser, go to following URL: `https://localhost:5001/WeatherForecast`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="94a6e-475">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="94a6e-475">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="94a6e-476">Wählen Sie **Ausführen** > **Debugging starten** aus, um die App zu starten.</span><span class="sxs-lookup"><span data-stu-id="94a6e-476">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="94a6e-477">Visual Studio für Mac startet einen Browser und navigiert zu `https://localhost:<port>`, wobei es sich bei `<port>` um eine zufällig ausgewählte Portnummer handelt.</span><span class="sxs-lookup"><span data-stu-id="94a6e-477">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="94a6e-478">Der Fehler „HTTP 404: Nicht gefunden“ wird zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="94a6e-478">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="94a6e-479">Fügen Sie `/WeatherForecast` an die URL an, d. h., ändern Sie sie in `https://localhost:<port>/WeatherForecast`.</span><span class="sxs-lookup"><span data-stu-id="94a6e-479">Append `/WeatherForecast` to the URL (change the URL to `https://localhost:<port>/WeatherForecast`).</span></span>

---

<span data-ttu-id="94a6e-480">Der zurückgegebene JSON-Code sieht in etwa wie folgt aus:</span><span class="sxs-lookup"><span data-stu-id="94a6e-480">JSON similar to the following is returned:</span></span>

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

## <a name="add-a-model-class"></a><span data-ttu-id="94a6e-481">Hinzufügen einer Modellklasse</span><span class="sxs-lookup"><span data-stu-id="94a6e-481">Add a model class</span></span>

<span data-ttu-id="94a6e-482">Ein *Modell* ist eine Gruppe von Klassen, die die Daten darstellen, die die App verwaltet.</span><span class="sxs-lookup"><span data-stu-id="94a6e-482">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="94a6e-483">Das Modell für diese App ist eine einzelne `TodoItem`-Klasse.</span><span class="sxs-lookup"><span data-stu-id="94a6e-483">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="94a6e-484">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="94a6e-484">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="94a6e-485">Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt.</span><span class="sxs-lookup"><span data-stu-id="94a6e-485">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="94a6e-486">Klicken Sie auf **Hinzufügen** > **Neuer Ordner**.</span><span class="sxs-lookup"><span data-stu-id="94a6e-486">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="94a6e-487">Geben Sie dem Ordner den Namen *Models* .</span><span class="sxs-lookup"><span data-stu-id="94a6e-487">Name the folder *Models*.</span></span>

* <span data-ttu-id="94a6e-488">Klicken Sie mit der rechten Maustaste auf den Ordner *Models* , und wählen Sie **Hinzufügen** > **Klasse** aus.</span><span class="sxs-lookup"><span data-stu-id="94a6e-488">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="94a6e-489">Nennen Sie die Klasse *TodoItem*, und wählen Sie **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="94a6e-489">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="94a6e-490">Ersetzen Sie den Vorlagencode durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="94a6e-490">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="94a6e-491">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="94a6e-491">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="94a6e-492">Fügen Sie einen Ordner mit dem Namen *Models* hinzu.</span><span class="sxs-lookup"><span data-stu-id="94a6e-492">Add a folder named *Models*.</span></span>

* <span data-ttu-id="94a6e-493">Fügen Sie dem Ordner *Models* mit dem folgenden Code eine `TodoItem`-Klasse hinzu:</span><span class="sxs-lookup"><span data-stu-id="94a6e-493">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="94a6e-494">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="94a6e-494">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="94a6e-495">Klicken Sie mit der rechten Maustaste auf das Projekt.</span><span class="sxs-lookup"><span data-stu-id="94a6e-495">Right-click the project.</span></span> <span data-ttu-id="94a6e-496">Klicken Sie auf **Hinzufügen** > **Neuer Ordner**.</span><span class="sxs-lookup"><span data-stu-id="94a6e-496">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="94a6e-497">Geben Sie dem Ordner den Namen *Models* .</span><span class="sxs-lookup"><span data-stu-id="94a6e-497">Name the folder *Models*.</span></span>

  ![Neuer Ordner](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="94a6e-499">Klicken Sie mit der rechten Maustaste auf den Ordner *Models* , und wählen Sie **Hinzufügen** > **Neue Datei** > **Allgemein** > **Leere Klasse** aus.</span><span class="sxs-lookup"><span data-stu-id="94a6e-499">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="94a6e-500">Nennen Sie die Klasse *TodoItem*, und klicken Sie dann auf **Neu**.</span><span class="sxs-lookup"><span data-stu-id="94a6e-500">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="94a6e-501">Ersetzen Sie den Vorlagencode durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="94a6e-501">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs?name=snippet)]

<span data-ttu-id="94a6e-502">Die `Id`-Eigenschaft fungiert als eindeutiger Schlüssel in einer relationalen Datenbank.</span><span class="sxs-lookup"><span data-stu-id="94a6e-502">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="94a6e-503">Modellklassen können überall im Projekt platziert werden, doch gemäß der Konvention wird der Ordner *Models* verwendet.</span><span class="sxs-lookup"><span data-stu-id="94a6e-503">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="94a6e-504">Hinzufügen eines Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="94a6e-504">Add a database context</span></span>

<span data-ttu-id="94a6e-505">Der *Datenbankkontext* ist die Hauptklasse, die die Entity Framework-Funktionen für ein Datenmodell koordiniert.</span><span class="sxs-lookup"><span data-stu-id="94a6e-505">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="94a6e-506">Diese Klasse wird durch Ableiten von der `Microsoft.EntityFrameworkCore.DbContext`-Klasse erstellt.</span><span class="sxs-lookup"><span data-stu-id="94a6e-506">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="94a6e-507">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="94a6e-507">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-nuget-packages"></a><span data-ttu-id="94a6e-508">Hinzufügen von NuGet-Paketen</span><span class="sxs-lookup"><span data-stu-id="94a6e-508">Add NuGet packages</span></span>

* <span data-ttu-id="94a6e-509">Klicken Sie im Menü **Extras** auf **NuGet-Paket-Manager > NuGet-Pakete für Projektmappe verwalten...** .</span><span class="sxs-lookup"><span data-stu-id="94a6e-509">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="94a6e-510">Klicken Sie auf die Registerkarte **Durchsuchen**, und geben Sie dann **Microsoft.EntityFrameworkCore.SqlServer** in das Suchfeld ein.</span><span class="sxs-lookup"><span data-stu-id="94a6e-510">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.SqlServer** in the search box.</span></span>
* <span data-ttu-id="94a6e-511">Wählen Sie im linken Bereich **Microsoft.EntityFrameworkCore.SqlServer** aus.</span><span class="sxs-lookup"><span data-stu-id="94a6e-511">Select **Microsoft.EntityFrameworkCore.SqlServer** in the left pane.</span></span>
* <span data-ttu-id="94a6e-512">Aktivieren Sie das Kontrollkästchen **Projekt** im rechten Bereich, und klicken Sie dann auf **Installieren**.</span><span class="sxs-lookup"><span data-stu-id="94a6e-512">Select the **Project** check box in the right pane and then select **Install**.</span></span>
* <span data-ttu-id="94a6e-513">Verwenden Sie die Anweisungen oben zum Hinzuzufügen des **Microsoft.EntityFrameworkCore.InMemory**-NuGet-Pakets.</span><span class="sxs-lookup"><span data-stu-id="94a6e-513">Use the preceding instructions to add the **Microsoft.EntityFrameworkCore.InMemory** NuGet package.</span></span>

![NuGet-Paket-Manager](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="94a6e-515">Hinzufügen des TodoContext-Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="94a6e-515">Add the TodoContext database context</span></span>

* <span data-ttu-id="94a6e-516">Klicken Sie mit der rechten Maustaste auf den Ordner *Models* , und wählen Sie **Hinzufügen** > **Klasse** aus.</span><span class="sxs-lookup"><span data-stu-id="94a6e-516">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="94a6e-517">Nennen Sie die Klasse *TodoContext*, und klicken Sie auf **Hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="94a6e-517">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="94a6e-518">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="94a6e-518">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="94a6e-519">Fügen Sie eine `TodoContext`-Klasse zum Ordner *Models* hinzu.</span><span class="sxs-lookup"><span data-stu-id="94a6e-519">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="94a6e-520">Geben Sie den folgenden Code ein:</span><span class="sxs-lookup"><span data-stu-id="94a6e-520">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="94a6e-521">Registrieren des Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="94a6e-521">Register the database context</span></span>

<span data-ttu-id="94a6e-522">In ASP.NET Core müssen Dienste wie der Datenbankkontext mit dem Container [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) registriert werden.</span><span class="sxs-lookup"><span data-stu-id="94a6e-522">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="94a6e-523">Der Container stellt den Dienst für Controller bereit.</span><span class="sxs-lookup"><span data-stu-id="94a6e-523">The container provides the service to controllers.</span></span>

<span data-ttu-id="94a6e-524">Aktualisieren Sie *Startup.cs* mit dem folgenden hervorgehobenen Code:</span><span class="sxs-lookup"><span data-stu-id="94a6e-524">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="94a6e-525">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="94a6e-525">The preceding code:</span></span>

* <span data-ttu-id="94a6e-526">Entfernt nicht benötigte `using`-Deklarationen</span><span class="sxs-lookup"><span data-stu-id="94a6e-526">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="94a6e-527">Fügt dem Abhängigkeitscontainer den Datenbankkontext hinzu</span><span class="sxs-lookup"><span data-stu-id="94a6e-527">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="94a6e-528">Gibt an, dass der Datenbankkontext eine In-Memory Database verwendet</span><span class="sxs-lookup"><span data-stu-id="94a6e-528">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="94a6e-529">Erstellen eines Controllergerüsts</span><span class="sxs-lookup"><span data-stu-id="94a6e-529">Scaffold a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="94a6e-530">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="94a6e-530">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="94a6e-531">Klicken Sie mit der rechten Maustaste auf den Ordner *Controller*.</span><span class="sxs-lookup"><span data-stu-id="94a6e-531">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="94a6e-532">Wählen Sie **Hinzufügen** > **Neues Gerüstelement** aus.</span><span class="sxs-lookup"><span data-stu-id="94a6e-532">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="94a6e-533">Klicken Sie auf **API-Controller mit Aktionen unter Verwendung von Entity Framework** und dann auf **Hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="94a6e-533">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="94a6e-534">Führen Sie im Dialogfeld **API-Controller mit Aktionen unter Verwendung von Entity Framework** folgende Schritte aus:</span><span class="sxs-lookup"><span data-stu-id="94a6e-534">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="94a6e-535">Wählen Sie in der **Modellklasse** das Element **TodoItem (TodoApi.Models)** aus.</span><span class="sxs-lookup"><span data-stu-id="94a6e-535">Select **TodoItem (TodoApi.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="94a6e-536">Wählen Sie in der **Datenkontextklasse** das Element **TodoContext (TodoApi.Models)** aus.</span><span class="sxs-lookup"><span data-stu-id="94a6e-536">Select **TodoContext (TodoApi.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="94a6e-537">Wählen Sie **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="94a6e-537">Select **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="94a6e-538">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="94a6e-538">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="94a6e-539">Führen Sie die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="94a6e-539">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet tool update -g Dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="94a6e-540">Die obenstehenden Befehle haben folgende Konsequenzen:</span><span class="sxs-lookup"><span data-stu-id="94a6e-540">The preceding commands:</span></span>

* <span data-ttu-id="94a6e-541">Die NuGet-Pakete für den Gerüstbau werden hinzufügt.</span><span class="sxs-lookup"><span data-stu-id="94a6e-541">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="94a6e-542">Die Gerüstbau-Engine (`dotnet-aspnet-codegenerator`) wird installiert.</span><span class="sxs-lookup"><span data-stu-id="94a6e-542">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="94a6e-543">Das Gerüst für `TodoItemsController` wird erstellt.</span><span class="sxs-lookup"><span data-stu-id="94a6e-543">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="94a6e-544">Der generierte Code hat folgende Auswirkungen:</span><span class="sxs-lookup"><span data-stu-id="94a6e-544">The generated code:</span></span>

* <span data-ttu-id="94a6e-545">Markiert die Klasse mit dem [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute)-Attribut.</span><span class="sxs-lookup"><span data-stu-id="94a6e-545">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="94a6e-546">Dieses Attribut gibt an, dass der Controller auf Web-API-Anforderungen reagiert.</span><span class="sxs-lookup"><span data-stu-id="94a6e-546">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="94a6e-547">Weitere Informationen zu bestimmten Verhaltensweisen, die das Attribut ermöglicht, finden Sie unter <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="94a6e-547">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="94a6e-548">Verwendet die Abhängigkeitsinjektion, um den Datenbankkontext (`TodoContext`) dem Controller hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="94a6e-548">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="94a6e-549">Der Datenbankkontext wird in den einzelnen [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete)-Methoden im Controller verwendet.</span><span class="sxs-lookup"><span data-stu-id="94a6e-549">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<span data-ttu-id="94a6e-550">Die ASP.NET Core-Vorlagen für:</span><span class="sxs-lookup"><span data-stu-id="94a6e-550">The ASP.NET Core templates for:</span></span>

* <span data-ttu-id="94a6e-551">Controller mit Ansichten enthalten `[action]` in der Routenvorlage.</span><span class="sxs-lookup"><span data-stu-id="94a6e-551">Controllers with views include `[action]` in the route template.</span></span>
* <span data-ttu-id="94a6e-552">API-Controller enthalten keine `[action]` in der Routenvorlage.</span><span class="sxs-lookup"><span data-stu-id="94a6e-552">API controllers don't include `[action]` in the route template.</span></span>

<span data-ttu-id="94a6e-553">Wenn sich das `[action]`-Token nicht in der Routenvorlage befindet, wird der [action](xref:mvc/controllers/routing#action)-Name von der Route ausgeschlossen.</span><span class="sxs-lookup"><span data-stu-id="94a6e-553">When the `[action]` token isn't in the route template, the [action](xref:mvc/controllers/routing#action) name is excluded from the route.</span></span> <span data-ttu-id="94a6e-554">Dies bedeutet, dass der zugehörige Methodenname der Aktion nicht in der übereinstimmenden Route verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="94a6e-554">That is, the action's associated method name isn't used in the matching route.</span></span>

## <a name="examine-the-posttodoitem-create-method"></a><span data-ttu-id="94a6e-555">Überblick über die PostTodoItem-Erstellungsmethode</span><span class="sxs-lookup"><span data-stu-id="94a6e-555">Examine the PostTodoItem create method</span></span>

<span data-ttu-id="94a6e-556">Ersetzen Sie die Rückgabeanweisung in `PostTodoItem` durch eine Anweisung mit dem [nameof](/dotnet/csharp/language-reference/operators/nameof)-Operator:</span><span class="sxs-lookup"><span data-stu-id="94a6e-556">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="94a6e-557">Der oben stehende Code ist eine HTTP POST-Methode, wie durch das [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute)-Attribut angegeben.</span><span class="sxs-lookup"><span data-stu-id="94a6e-557">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="94a6e-558">Die Methode ruft den Wert der Aufgabe aus dem Text der HTTP-Anforderung ab.</span><span class="sxs-lookup"><span data-stu-id="94a6e-558">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="94a6e-559">Weitere Informationen finden Sie unter [Attributrouting mit Http[Verb]-Attributen](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="94a6e-559">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="94a6e-560">Die <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*>-Methode:</span><span class="sxs-lookup"><span data-stu-id="94a6e-560">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="94a6e-561">Gibt den HTTP-Statuscode 201 zurück, wenn der Vorgang erfolgreich ist.</span><span class="sxs-lookup"><span data-stu-id="94a6e-561">Returns an HTTP 201 status code if successful.</span></span> <span data-ttu-id="94a6e-562">HTTP 201 ist die Standardantwort für eine HTTP POST-Methode, die eine neue Ressource auf dem Server erstellt.</span><span class="sxs-lookup"><span data-stu-id="94a6e-562">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="94a6e-563">Fügt der Antwort einen [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location)-Header hinzu.</span><span class="sxs-lookup"><span data-stu-id="94a6e-563">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="94a6e-564">Der `Location`-Header legt den [URI](https://developer.mozilla.org/docs/Glossary/URI) des neu erstellten To-do-Elements fest.</span><span class="sxs-lookup"><span data-stu-id="94a6e-564">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="94a6e-565">Weitere Informationen finden Sie unter [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="94a6e-565">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="94a6e-566">Verweist auf die `GetTodoItem`-Aktion zum Erstellen des URIs des `Location`-Headers.</span><span class="sxs-lookup"><span data-stu-id="94a6e-566">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="94a6e-567">Das `nameof`-Schlüsselwort von C# wird verwendet, um eine Hartcodierung des Aktionsnamens im `CreatedAtAction`-Aufruf zu vermeiden.</span><span class="sxs-lookup"><span data-stu-id="94a6e-567">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="94a6e-568">Installieren von Postman</span><span class="sxs-lookup"><span data-stu-id="94a6e-568">Install Postman</span></span>

<span data-ttu-id="94a6e-569">Dieses Tutorial verwendet Postman zum Testen der Web-API.</span><span class="sxs-lookup"><span data-stu-id="94a6e-569">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="94a6e-570">Installieren Sie [Postman](https://www.getpostman.com/downloads/).</span><span class="sxs-lookup"><span data-stu-id="94a6e-570">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="94a6e-571">Starten Sie die Web-App.</span><span class="sxs-lookup"><span data-stu-id="94a6e-571">Start the web app.</span></span>
* <span data-ttu-id="94a6e-572">Starten Sie Postman.</span><span class="sxs-lookup"><span data-stu-id="94a6e-572">Start Postman.</span></span>
* <span data-ttu-id="94a6e-573">Deaktivieren Sie **SSL certificate verification** (Verifizierung des SSL-Zertifikats).</span><span class="sxs-lookup"><span data-stu-id="94a6e-573">Disable **SSL certificate verification**</span></span>
  * <span data-ttu-id="94a6e-574">Deaktivieren Sie auf der Registerkarte **General** (Allgemein) unter File > Settings (**Datei** > **Einstellungen**) **SSL certificate verification** (Verifizierung des SSL-Zertifikats).</span><span class="sxs-lookup"><span data-stu-id="94a6e-574">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="94a6e-575">Aktivieren Sie die Verifizierung des SSL-Zertifikats wieder, nachdem Sie den Controller getestet haben.</span><span class="sxs-lookup"><span data-stu-id="94a6e-575">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="94a6e-576">Testen von PostTodoItem mit Postman</span><span class="sxs-lookup"><span data-stu-id="94a6e-576">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="94a6e-577">Erstellen Sie eine neue Anforderung.</span><span class="sxs-lookup"><span data-stu-id="94a6e-577">Create a new request.</span></span>
* <span data-ttu-id="94a6e-578">Legen Sie als HTTP-Methode `POST` fest.</span><span class="sxs-lookup"><span data-stu-id="94a6e-578">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="94a6e-579">Legen Sie den URI auf `https://localhost:<port>/api/TodoItems` fest.</span><span class="sxs-lookup"><span data-stu-id="94a6e-579">Set the URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="94a6e-580">Beispiel: `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="94a6e-580">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="94a6e-581">Wählen Sie die Registerkarte **Body** (Text) aus.</span><span class="sxs-lookup"><span data-stu-id="94a6e-581">Select the **Body** tab.</span></span>
* <span data-ttu-id="94a6e-582">Wählen Sie das Optionsfeld **raw** (Unformatiert) aus.</span><span class="sxs-lookup"><span data-stu-id="94a6e-582">Select the **raw** radio button.</span></span>
* <span data-ttu-id="94a6e-583">Legen Sie den Typ auf **JSON (application/json)** fest.</span><span class="sxs-lookup"><span data-stu-id="94a6e-583">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="94a6e-584">Geben Sie für die Aufgabe den Anforderungstext im JSON-Format ein:</span><span class="sxs-lookup"><span data-stu-id="94a6e-584">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="94a6e-585">Wählen Sie **Send** (Senden) aus.</span><span class="sxs-lookup"><span data-stu-id="94a6e-585">Select **Send**.</span></span>

  ![Postman mit erstellter Anforderung](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri-with-postman"></a><span data-ttu-id="94a6e-587">Testen des Adressheader-URIs mit Postman</span><span class="sxs-lookup"><span data-stu-id="94a6e-587">Test the location header URI with Postman</span></span>

* <span data-ttu-id="94a6e-588">Wählen Sie auf der Registerkarte **Header** (Header) den Bereich **Response** (Antwort) aus.</span><span class="sxs-lookup"><span data-stu-id="94a6e-588">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="94a6e-589">Kopieren Sie den den Headerwert von **Location** (Speicherort):</span><span class="sxs-lookup"><span data-stu-id="94a6e-589">Copy the **Location** header value:</span></span>

  ![Registerkarte „Header“ in der Postman-Konsole](first-web-api/_static/3/create.png)

* <span data-ttu-id="94a6e-591">Legen Sie als HTTP-Methode `GET` fest.</span><span class="sxs-lookup"><span data-stu-id="94a6e-591">Set the HTTP method to `GET`.</span></span>
* <span data-ttu-id="94a6e-592">Legen Sie den URI auf `https://localhost:<port>/api/TodoItems/1` fest.</span><span class="sxs-lookup"><span data-stu-id="94a6e-592">Set the URI to `https://localhost:<port>/api/TodoItems/1`.</span></span> <span data-ttu-id="94a6e-593">Beispiel: `https://localhost:5001/api/TodoItems/1`.</span><span class="sxs-lookup"><span data-stu-id="94a6e-593">For example, `https://localhost:5001/api/TodoItems/1`.</span></span>
* <span data-ttu-id="94a6e-594">Wählen Sie **Send** (Senden) aus.</span><span class="sxs-lookup"><span data-stu-id="94a6e-594">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="94a6e-595">Überblick über die GET-Methoden</span><span class="sxs-lookup"><span data-stu-id="94a6e-595">Examine the GET methods</span></span>

<span data-ttu-id="94a6e-596">Diese Methoden implementieren zwei GET-Endpunkte:</span><span class="sxs-lookup"><span data-stu-id="94a6e-596">These methods implement two GET endpoints:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="94a6e-597">Testen Sie die App, indem Sie die beiden Endpunkte in einem Browser oder über Postman aufrufen.</span><span class="sxs-lookup"><span data-stu-id="94a6e-597">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="94a6e-598">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="94a6e-598">For example:</span></span>

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

<span data-ttu-id="94a6e-599">Durch einen Aufruf von `GetTodoItems` wird eine Antwort gesendet, die der folgenden ähnelt:</span><span class="sxs-lookup"><span data-stu-id="94a6e-599">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="94a6e-600">Testen von GET mit Postman</span><span class="sxs-lookup"><span data-stu-id="94a6e-600">Test Get with Postman</span></span>

* <span data-ttu-id="94a6e-601">Erstellen Sie eine neue Anforderung.</span><span class="sxs-lookup"><span data-stu-id="94a6e-601">Create a new request.</span></span>
* <span data-ttu-id="94a6e-602">Legen Sie die HTTP-Methode auf **GET** fest.</span><span class="sxs-lookup"><span data-stu-id="94a6e-602">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="94a6e-603">Legen Sie den Anforderungs-URI auf `https://localhost:<port>/api/TodoItems` fest.</span><span class="sxs-lookup"><span data-stu-id="94a6e-603">Set the request URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="94a6e-604">Beispielsweise `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="94a6e-604">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="94a6e-605">Wählen Sie in Postman **Two pane view** (Ansicht mit zwei Bereichen) aus.</span><span class="sxs-lookup"><span data-stu-id="94a6e-605">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="94a6e-606">Wählen Sie **Send** (Senden) aus.</span><span class="sxs-lookup"><span data-stu-id="94a6e-606">Select **Send**.</span></span>

<span data-ttu-id="94a6e-607">Diese App verwendet eine In-Memory-Datenbank.</span><span class="sxs-lookup"><span data-stu-id="94a6e-607">This app uses an in-memory database.</span></span> <span data-ttu-id="94a6e-608">Wenn die App angehalten und dann wieder gestartet wird, werden durch die vorherige GET-Anforderung keine Daten zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="94a6e-608">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="94a6e-609">Wenn keine Daten zurückgegeben werden, senden Sie mit [POST](#post) Daten an die App.</span><span class="sxs-lookup"><span data-stu-id="94a6e-609">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="94a6e-610">Routing und URL-Pfade</span><span class="sxs-lookup"><span data-stu-id="94a6e-610">Routing and URL paths</span></span>

<span data-ttu-id="94a6e-611">Das Attribut [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) gibt eine Methode an, die auf eine HTTP GET-Anforderung antwortet.</span><span class="sxs-lookup"><span data-stu-id="94a6e-611">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="94a6e-612">Der URL-Pfad für jede Methode wird wie folgt erstellt:</span><span class="sxs-lookup"><span data-stu-id="94a6e-612">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="94a6e-613">Beginnen Sie mit der Vorlagenzeichenfolge im `Route`-Attribut des Controllers:</span><span class="sxs-lookup"><span data-stu-id="94a6e-613">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="94a6e-614">Ersetzen Sie `[controller]` durch den Namen des Controllers, bei dem es sich standardmäßig um den Namen der Controller-Klasse ohne das Suffix „Controller“ handelt.</span><span class="sxs-lookup"><span data-stu-id="94a6e-614">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="94a6e-615">In diesem Beispiel ist der Klassenname des Controllers „**TodoItems** Controller“. Der Controllername lautet also „TodoItems“.</span><span class="sxs-lookup"><span data-stu-id="94a6e-615">For this sample, the controller class name is **TodoItems** Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="94a6e-616">Beim ASP.NET Core-[Routing](xref:mvc/controllers/routing) wird die Groß-/Kleinschreibung nicht beachtet.</span><span class="sxs-lookup"><span data-stu-id="94a6e-616">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="94a6e-617">Wenn das `[HttpGet]`-Attribut eine Routenvorlage (z. B. `[HttpGet("products")]`) hat, fügen Sie diese an den Pfad an.</span><span class="sxs-lookup"><span data-stu-id="94a6e-617">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="94a6e-618">In diesem Beispiel wird keine Vorlage verwendet.</span><span class="sxs-lookup"><span data-stu-id="94a6e-618">This sample doesn't use a template.</span></span> <span data-ttu-id="94a6e-619">Weitere Informationen finden Sie unter [Attributrouting mit Http[Verb]-Attributen](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="94a6e-619">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="94a6e-620">In der folgenden `GetTodoItem`-Methode ist `"{id}"` eine Platzhaltervariable für den eindeutigen Bezeichner des To-do-Elements.</span><span class="sxs-lookup"><span data-stu-id="94a6e-620">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="94a6e-621">Wenn `GetTodoItem` aufgerufen wird, wird der Wert von `"{id}"` in der URL der Methode in ihrem Parameter `id` bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="94a6e-621">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="94a6e-622">Rückgabewerte</span><span class="sxs-lookup"><span data-stu-id="94a6e-622">Return values</span></span> 

<span data-ttu-id="94a6e-623">Der Rückgabetyp der Methoden `GetTodoItems` und `GetTodoItem` ist der [ActionResult\<T>-Typ](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="94a6e-623">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="94a6e-624">ASP.NET Core serialisiert automatisch das Objekt in [JSON](https://www.json.org/) und schreibt den JSON-Code in den Text der Antwortnachricht.</span><span class="sxs-lookup"><span data-stu-id="94a6e-624">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="94a6e-625">Der Antwortcode für diesen Rückgabetyp ist 200, vorausgesetzt, es gibt keine Ausnahmefehler.</span><span class="sxs-lookup"><span data-stu-id="94a6e-625">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="94a6e-626">Nicht behandelte Ausnahmen werden in 5xx-Fehler übersetzt.</span><span class="sxs-lookup"><span data-stu-id="94a6e-626">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="94a6e-627">`ActionResult`-Rückgabetypen können eine Vielzahl von HTTP-Statuscodes darstellen.</span><span class="sxs-lookup"><span data-stu-id="94a6e-627">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="94a6e-628">Beispielsweise kann `GetTodoItem` zwei verschiedene Statuswerte zurückgeben:</span><span class="sxs-lookup"><span data-stu-id="94a6e-628">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="94a6e-629">Wenn kein Element mit der angeforderten ID übereinstimmt, gibt die Methode einen 404-Fehlercode <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> zurück.</span><span class="sxs-lookup"><span data-stu-id="94a6e-629">If no item matches the requested ID, the method returns a 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="94a6e-630">Andernfalls gibt die Methode 200 mit einem JSON-Antworttext zurück.</span><span class="sxs-lookup"><span data-stu-id="94a6e-630">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="94a6e-631">Die Rückgabe von `item` löst eine HTTP 200-Antwort aus.</span><span class="sxs-lookup"><span data-stu-id="94a6e-631">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="94a6e-632">PutTodoItem-Methode</span><span class="sxs-lookup"><span data-stu-id="94a6e-632">The PutTodoItem method</span></span>

<span data-ttu-id="94a6e-633">Untersuchen Sie die `PutTodoItem`-Methode.</span><span class="sxs-lookup"><span data-stu-id="94a6e-633">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="94a6e-634">`PutTodoItem` ähnelt `PostTodoItem`, verwendet allerdings HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="94a6e-634">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="94a6e-635">Die Antwort ist [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="94a6e-635">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="94a6e-636">Gemäß der HTTP-Spezifikation erfordert eine PUT-Anforderung, dass der Client die gesamte aktualisierte Entität (nicht nur die Änderungen) sendet.</span><span class="sxs-lookup"><span data-stu-id="94a6e-636">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="94a6e-637">Verwenden Sie [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute), um Teilupdates zu unterstützen.</span><span class="sxs-lookup"><span data-stu-id="94a6e-637">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="94a6e-638">Wenn beim Aufrufen von `PutTodoItem` ein Fehler zurückgegeben wird, rufen Sie `GET` auf, um sicherzustellen, dass die Datenbank ein Element enthält.</span><span class="sxs-lookup"><span data-stu-id="94a6e-638">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="94a6e-639">Testen der PutTodoItem-Methode</span><span class="sxs-lookup"><span data-stu-id="94a6e-639">Test the PutTodoItem method</span></span>

<span data-ttu-id="94a6e-640">In diesem Beispiel wird eine In-Memory-Datenbank verwendet, die jedes Mal initialisiert werden muss, wenn die App gestartet wird.</span><span class="sxs-lookup"><span data-stu-id="94a6e-640">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="94a6e-641">Es muss ein Element in der Datenbank vorhanden sein, bevor Sie einen PUT-Aufruf durchführen.</span><span class="sxs-lookup"><span data-stu-id="94a6e-641">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="94a6e-642">Rufen Sie vor einem PUT-Aufruf GET auf, um sicherzustellen, dass ein Element in der Datenbank vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="94a6e-642">Call GET to ensure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="94a6e-643">Aktualisieren Sie das To-do-Element, das über den ID-Wert 1 verfügt, und legen Sie als Namen „feed fish“ fest:</span><span class="sxs-lookup"><span data-stu-id="94a6e-643">Update the to-do item that has Id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "id":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="94a6e-644">Die folgende Abbildung zeigt das Postman-Update:</span><span class="sxs-lookup"><span data-stu-id="94a6e-644">The following image shows the Postman update:</span></span>

![Postman-Konsole mit der Antwort „204 (Kein Inhalt)“](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="94a6e-646">DeleteTodoItem-Methode</span><span class="sxs-lookup"><span data-stu-id="94a6e-646">The DeleteTodoItem method</span></span>

<span data-ttu-id="94a6e-647">Untersuchen Sie die `DeleteTodoItem`-Methode.</span><span class="sxs-lookup"><span data-stu-id="94a6e-647">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="94a6e-648">Testen der DeleteTodoItem-Methode</span><span class="sxs-lookup"><span data-stu-id="94a6e-648">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="94a6e-649">So löschen Sie mit Postman eine Aufgabe</span><span class="sxs-lookup"><span data-stu-id="94a6e-649">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="94a6e-650">Legen Sie die Methode auf `DELETE` fest.</span><span class="sxs-lookup"><span data-stu-id="94a6e-650">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="94a6e-651">Legen Sie den URI des zu löschenden Objekts fest, z. B. `https://localhost:5001/api/TodoItems/1`.</span><span class="sxs-lookup"><span data-stu-id="94a6e-651">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="94a6e-652">Wählen Sie **Send** (Senden) aus.</span><span class="sxs-lookup"><span data-stu-id="94a6e-652">Select **Send**.</span></span>

<a name="over-post"></a>
<a name="over-post-v3"></a>

## <a name="prevent-over-posting"></a><span data-ttu-id="94a6e-653">Vermeiden von Overposting</span><span class="sxs-lookup"><span data-stu-id="94a6e-653">Prevent over-posting</span></span>

<span data-ttu-id="94a6e-654">Derzeit macht die Beispiel-App das gesamte `TodoItem`-Objekt verfügbar.</span><span class="sxs-lookup"><span data-stu-id="94a6e-654">Currently the sample app exposes the entire `TodoItem` object.</span></span> <span data-ttu-id="94a6e-655">In den Produktions-Apps sind die Daten, die eingegeben und mithilfe einer Teilmenge des Modells zurückgegeben werden, in der Regel eingeschränkt.</span><span class="sxs-lookup"><span data-stu-id="94a6e-655">Production apps typically limit the data that's input and returned using a subset of the model.</span></span> <span data-ttu-id="94a6e-656">Hierfür gibt es mehrere Gründe, wobei die Sicherheit einer der Hauptgründe ist.</span><span class="sxs-lookup"><span data-stu-id="94a6e-656">There are multiple reasons behind this and security is a major one.</span></span> <span data-ttu-id="94a6e-657">Die Teilmenge eines Modells wird üblicherweise als Datenübertragungsobjekt (DTO, Data Transfer Object), Eingabemodell oder Anzeigemodell bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="94a6e-657">The subset of a model is usually referred to as a Data Transfer Object (DTO), input model, or view model.</span></span> <span data-ttu-id="94a6e-658">In diesem Artikel wird das **DTO** verwendet.</span><span class="sxs-lookup"><span data-stu-id="94a6e-658">**DTO** is used in this article.</span></span>

<span data-ttu-id="94a6e-659">Ein DTO kann für Folgendes verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="94a6e-659">A DTO may be used to:</span></span>

* <span data-ttu-id="94a6e-660">Vermeiden Sie Overposting.</span><span class="sxs-lookup"><span data-stu-id="94a6e-660">Prevent over-posting.</span></span>
* <span data-ttu-id="94a6e-661">Ausblenden von Eigenschaften, die Clients nicht anzeigen sollen</span><span class="sxs-lookup"><span data-stu-id="94a6e-661">Hide properties that clients are not supposed to view.</span></span>
* <span data-ttu-id="94a6e-662">Auslassen einiger Eigenschaften, um die Nutzlast zu verringern</span><span class="sxs-lookup"><span data-stu-id="94a6e-662">Omit some properties in order to reduce payload size.</span></span>
* <span data-ttu-id="94a6e-663">Vereinfachen von Objektgraphen, die geschachtelte Objekte enthalten</span><span class="sxs-lookup"><span data-stu-id="94a6e-663">Flatten object graphs that contain nested objects.</span></span> <span data-ttu-id="94a6e-664">Vereinfachte Objektgraphen können für Clients zweckmäßiger sein.</span><span class="sxs-lookup"><span data-stu-id="94a6e-664">Flattened object graphs can be more convenient for clients.</span></span>

<span data-ttu-id="94a6e-665">Um den DTO-Ansatz zu veranschaulichen, aktualisieren Sie die `TodoItem`-Klasse, sodass sie ein geheimes Feld einschließt:</span><span class="sxs-lookup"><span data-stu-id="94a6e-665">To demonstrate the DTO approach, update the `TodoItem` class to include a secret field:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItem.cs?name=snippet&highlight=6)]

<span data-ttu-id="94a6e-666">Das geheime Feld muss in dieser App ausgeblendet werden, eine administrative App kann es jedoch verfügbar machen.</span><span class="sxs-lookup"><span data-stu-id="94a6e-666">The secret field needs to be hidden from this app, but an administrative app could choose to expose it.</span></span>

<span data-ttu-id="94a6e-667">Vergewissern Sie sich, dass Sie das geheime Feld veröffentlichen und abrufen können.</span><span class="sxs-lookup"><span data-stu-id="94a6e-667">Verify you can post and get the secret field.</span></span>

<span data-ttu-id="94a6e-668">Erstellen Sie ein DTO-Modell:</span><span class="sxs-lookup"><span data-stu-id="94a6e-668">Create a DTO model:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItemDTO.cs?name=snippet)]

<span data-ttu-id="94a6e-669">Aktualisieren Sie `TodoItemsController`, sodass `TodoItemDTO` verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="94a6e-669">Update the `TodoItemsController` to use `TodoItemDTO`:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

<span data-ttu-id="94a6e-670">Vergewissern Sie sich, dass Sie das geheime Feld weder veröffentlichen noch abrufen können.</span><span class="sxs-lookup"><span data-stu-id="94a6e-670">Verify you can't post or get the secret field.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="94a6e-671">Aufrufen der Web-API mit JavaScript</span><span class="sxs-lookup"><span data-stu-id="94a6e-671">Call the web API with JavaScript</span></span>

<span data-ttu-id="94a6e-672">Mehr dazu finden Sie im [-Tutorial: Aufrufen einer ASP.NET Core-Web-API mit JavaScript](xref:tutorials/web-api-javascript).</span><span class="sxs-lookup"><span data-stu-id="94a6e-672">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="94a6e-673">In diesem Tutorial lernen Sie, wie die folgenden Aufgaben ausgeführt werden:</span><span class="sxs-lookup"><span data-stu-id="94a6e-673">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="94a6e-674">Erstellen eines Web-API-Projekts</span><span class="sxs-lookup"><span data-stu-id="94a6e-674">Create a web API project.</span></span>
> * <span data-ttu-id="94a6e-675">Hinzufügen einer Modellklasse und eines Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="94a6e-675">Add a model class and a database context.</span></span>
> * <span data-ttu-id="94a6e-676">Hinzufügen eines Controllers</span><span class="sxs-lookup"><span data-stu-id="94a6e-676">Add a controller.</span></span>
> * <span data-ttu-id="94a6e-677">Hinzufügen von CRUD-Methoden</span><span class="sxs-lookup"><span data-stu-id="94a6e-677">Add CRUD methods.</span></span>
> * <span data-ttu-id="94a6e-678">Konfigurieren von Routing und URL-Pfaden</span><span class="sxs-lookup"><span data-stu-id="94a6e-678">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="94a6e-679">Angeben von Rückgabewerten</span><span class="sxs-lookup"><span data-stu-id="94a6e-679">Specify return values.</span></span>
> * <span data-ttu-id="94a6e-680">Aufrufen der Web-API mit Postman</span><span class="sxs-lookup"><span data-stu-id="94a6e-680">Call the web API with Postman.</span></span>
> * <span data-ttu-id="94a6e-681">Aufrufen der Web-API mit JavaScript</span><span class="sxs-lookup"><span data-stu-id="94a6e-681">Call the web API with JavaScript.</span></span>

<span data-ttu-id="94a6e-682">Am Ende haben Sie eine Web-API, die in einer relationalen Datenbank gespeicherte „Aufgaben“ verwalten kann.</span><span class="sxs-lookup"><span data-stu-id="94a6e-682">At the end, you have a web API that can manage "to-do" items stored in a relational database.</span></span>

## <a name="overview-21"></a><span data-ttu-id="94a6e-683">Übersicht 2.1</span><span class="sxs-lookup"><span data-stu-id="94a6e-683">Overview 2.1</span></span>

<span data-ttu-id="94a6e-684">In diesem Tutorial wird die folgende API erstellt:</span><span class="sxs-lookup"><span data-stu-id="94a6e-684">This tutorial creates the following API:</span></span>

|<span data-ttu-id="94a6e-685">API</span><span class="sxs-lookup"><span data-stu-id="94a6e-685">API</span></span> | <span data-ttu-id="94a6e-686">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="94a6e-686">Description</span></span> | <span data-ttu-id="94a6e-687">Anforderungstext</span><span class="sxs-lookup"><span data-stu-id="94a6e-687">Request body</span></span> | <span data-ttu-id="94a6e-688">Antworttext</span><span class="sxs-lookup"><span data-stu-id="94a6e-688">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="94a6e-689">GET /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="94a6e-689">GET /api/TodoItems</span></span> | <span data-ttu-id="94a6e-690">Alle To-do-Elemente abrufen</span><span class="sxs-lookup"><span data-stu-id="94a6e-690">Get all to-do items</span></span> | <span data-ttu-id="94a6e-691">Keine</span><span class="sxs-lookup"><span data-stu-id="94a6e-691">None</span></span> | <span data-ttu-id="94a6e-692">Array von To-do-Elementen</span><span class="sxs-lookup"><span data-stu-id="94a6e-692">Array of to-do items</span></span>|
|<span data-ttu-id="94a6e-693">GET /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="94a6e-693">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="94a6e-694">Ein Element nach ID abrufen</span><span class="sxs-lookup"><span data-stu-id="94a6e-694">Get an item by ID</span></span> | <span data-ttu-id="94a6e-695">Keine</span><span class="sxs-lookup"><span data-stu-id="94a6e-695">None</span></span> | <span data-ttu-id="94a6e-696">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="94a6e-696">To-do item</span></span>|
|<span data-ttu-id="94a6e-697">POST /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="94a6e-697">POST /api/TodoItems</span></span> | <span data-ttu-id="94a6e-698">Neues Element hinzufügen</span><span class="sxs-lookup"><span data-stu-id="94a6e-698">Add a new item</span></span> | <span data-ttu-id="94a6e-699">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="94a6e-699">To-do item</span></span> | <span data-ttu-id="94a6e-700">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="94a6e-700">To-do item</span></span> |
|<span data-ttu-id="94a6e-701">PUT /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="94a6e-701">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="94a6e-702">Vorhandenes Element aktualisieren &nbsp;</span><span class="sxs-lookup"><span data-stu-id="94a6e-702">Update an existing item &nbsp;</span></span> | <span data-ttu-id="94a6e-703">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="94a6e-703">To-do item</span></span> | <span data-ttu-id="94a6e-704">Keine</span><span class="sxs-lookup"><span data-stu-id="94a6e-704">None</span></span> |
|<span data-ttu-id="94a6e-705">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="94a6e-705">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="94a6e-706">Löschen eines Elements &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="94a6e-706">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="94a6e-707">Keine</span><span class="sxs-lookup"><span data-stu-id="94a6e-707">None</span></span> | <span data-ttu-id="94a6e-708">Keine</span><span class="sxs-lookup"><span data-stu-id="94a6e-708">None</span></span>|

<span data-ttu-id="94a6e-709">Das folgende Diagramm zeigt den Entwurf der App.</span><span class="sxs-lookup"><span data-stu-id="94a6e-709">The following diagram shows the design of the app.</span></span>

![Der Client ist das Feld ganz links.](first-web-api/_static/architecture.png)

## <a name="prerequisites-21"></a><span data-ttu-id="94a6e-715">Voraussetzungen 2.1</span><span class="sxs-lookup"><span data-stu-id="94a6e-715">Prerequisites 2.1</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="94a6e-716">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="94a6e-716">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="94a6e-717">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="94a6e-717">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="94a6e-718">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="94a6e-718">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project-21"></a><span data-ttu-id="94a6e-719">Erstellen eines Webprojekts 2.1</span><span class="sxs-lookup"><span data-stu-id="94a6e-719">Create a web project 2.1</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="94a6e-720">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="94a6e-720">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="94a6e-721">Klicken Sie im Menü **Datei** auf **Neu** > **Projekt**.</span><span class="sxs-lookup"><span data-stu-id="94a6e-721">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="94a6e-722">Wählen Sie die Vorlage **ASP.NET Core-Webanwendung** aus, und klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="94a6e-722">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="94a6e-723">Geben Sie dem Projekt den Namen *TodoApi*, und klicken Sie auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="94a6e-723">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="94a6e-724">Vergewissern Sie sich, dass im Dialogfeld **Neue ASP.NET Core-Webanwendung erstellen** die Optionen **.NET Core** und **ASP.NET Core 2.2** ausgewählt sind.</span><span class="sxs-lookup"><span data-stu-id="94a6e-724">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 2.2** are selected.</span></span> <span data-ttu-id="94a6e-725">Wählen Sie die Vorlage **API** aus, und klicken Sie auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="94a6e-725">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="94a6e-726">Wählen Sie **nicht** **Enable Docker Support** (Docker-Unterstützung aktivieren) aus.</span><span class="sxs-lookup"><span data-stu-id="94a6e-726">**Don't** select **Enable Docker Support**.</span></span>

![VS-Dialogfeld „Neues Projekt“](first-web-api/_static/vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="94a6e-728">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="94a6e-728">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="94a6e-729">Öffnen Sie das [integrierte Terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="94a6e-729">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="94a6e-730">Wechseln Sie mit dem Befehl `cd`' zu dem Ordner, der den Projektordner enthalten soll.</span><span class="sxs-lookup"><span data-stu-id="94a6e-730">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="94a6e-731">Führen Sie die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="94a6e-731">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  <span data-ttu-id="94a6e-732">Diese Befehle erstellen ein neues Web-API-Projekt und öffnen eine neue Visual Studio Code-Instanz im neuen Projektordner.</span><span class="sxs-lookup"><span data-stu-id="94a6e-732">These commands create a new web API project and open a new instance of Visual Studio Code in the new project folder.</span></span>

* <span data-ttu-id="94a6e-733">Wenn Sie in einem Dialogfeld angeben müssen, ob Sie dem Projekt die erforderlichen Elemente hinzufügen möchten, wählen Sie **Ja** aus.</span><span class="sxs-lookup"><span data-stu-id="94a6e-733">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="94a6e-734">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="94a6e-734">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="94a6e-735">Klicken Sie auf **Datei** > **Neue Projektmappe**.</span><span class="sxs-lookup"><span data-stu-id="94a6e-735">Select **File** > **New Solution**.</span></span>

  ![Neue Projektmappe in macOS](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="94a6e-737">Navigieren Sie in Visual Studio für Mac-Versionen vor Version 8.6 zu **.NET Core** > **App** > **API** > **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="94a6e-737">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="94a6e-738">Bei Version 8.6 oder höher klicken Sie auf **Web and Console** > **App** > **API** > **Weiter** (Web und Konsole).</span><span class="sxs-lookup"><span data-stu-id="94a6e-738">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>
  
* <span data-ttu-id="94a6e-739">Wählen Sie im Dialogfeld **Neue ASP.NET Core-Web-API konfigurieren** die neueste .NET Core 2.x-Version als **Zielframework** aus.</span><span class="sxs-lookup"><span data-stu-id="94a6e-739">In the **Configure the new ASP.NET Core Web API** dialog, select the latest .NET Core 2.x **Target Framework**.</span></span> <span data-ttu-id="94a6e-740">Klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="94a6e-740">Select **Next**.</span></span>

* <span data-ttu-id="94a6e-741">Geben Sie für **Projektname** *TodoApi* ein, und wählen Sie dann **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="94a6e-741">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![Dialogfeld „Konfiguration“](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api-21"></a><span data-ttu-id="94a6e-743">Testen der API 2.1</span><span class="sxs-lookup"><span data-stu-id="94a6e-743">Test the API 2.1</span></span>

<span data-ttu-id="94a6e-744">Die Projektvorlage erstellt eine `values`-API.</span><span class="sxs-lookup"><span data-stu-id="94a6e-744">The project template creates a `values` API.</span></span> <span data-ttu-id="94a6e-745">Rufen Sie in einem Browser die `Get`-Methode zum Testen der App auf.</span><span class="sxs-lookup"><span data-stu-id="94a6e-745">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="94a6e-746">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="94a6e-746">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="94a6e-747">Drücken Sie STRG+F5, um die App auszuführen.</span><span class="sxs-lookup"><span data-stu-id="94a6e-747">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="94a6e-748">Visual Studio startet einen Browser und navigiert zu `https://localhost:<port>/api/values`, wobei es sich bei `<port>` um eine zufällig ausgewählte Portnummer handelt.</span><span class="sxs-lookup"><span data-stu-id="94a6e-748">Visual Studio launches a browser and navigates to `https://localhost:<port>/api/values`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="94a6e-749">Wenn Sie in einem Dialogfeld angeben müssen, ob Sie dem IIS Express-Zertifikat vertrauen, wählen Sie **Ja** aus.</span><span class="sxs-lookup"><span data-stu-id="94a6e-749">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="94a6e-750">Wählen Sie im folgenden Dialogfeld **Sicherheitswarnung** die Option **Ja** aus.</span><span class="sxs-lookup"><span data-stu-id="94a6e-750">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="94a6e-751">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="94a6e-751">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="94a6e-752">Drücken Sie STRG+F5, um die App auszuführen.</span><span class="sxs-lookup"><span data-stu-id="94a6e-752">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="94a6e-753">Navigieren Sie in einem Browser zur folgenden URL: `https://localhost:5001/api/values`.</span><span class="sxs-lookup"><span data-stu-id="94a6e-753">In a browser, go to following URL: `https://localhost:5001/api/values`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="94a6e-754">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="94a6e-754">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="94a6e-755">Wählen Sie **Ausführen** > **Debugging starten** aus, um die App zu starten.</span><span class="sxs-lookup"><span data-stu-id="94a6e-755">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="94a6e-756">Visual Studio für Mac startet einen Browser und navigiert zu `https://localhost:<port>`, wobei es sich bei `<port>` um eine zufällig ausgewählte Portnummer handelt.</span><span class="sxs-lookup"><span data-stu-id="94a6e-756">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="94a6e-757">Der Fehler „HTTP 404: Nicht gefunden“ wird zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="94a6e-757">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="94a6e-758">Fügen Sie der URL `/api/values` an, d.h. ändern Sie die URL in `https://localhost:<port>/api/values`.</span><span class="sxs-lookup"><span data-stu-id="94a6e-758">Append `/api/values` to the URL (change the URL to `https://localhost:<port>/api/values`).</span></span>

---

<span data-ttu-id="94a6e-759">Die folgende JSON-Datei wird zurückgegeben:</span><span class="sxs-lookup"><span data-stu-id="94a6e-759">The following JSON is returned:</span></span>

```json
["value1","value2"]
```

## <a name="add-a-model-class-21"></a><span data-ttu-id="94a6e-760">Hinzufügen einer Modellklasse 2.1</span><span class="sxs-lookup"><span data-stu-id="94a6e-760">Add a model class 2.1</span></span>

<span data-ttu-id="94a6e-761">Ein *Modell* ist eine Gruppe von Klassen, die die Daten darstellen, die die App verwaltet.</span><span class="sxs-lookup"><span data-stu-id="94a6e-761">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="94a6e-762">Das Modell für diese App ist eine einzelne `TodoItem`-Klasse.</span><span class="sxs-lookup"><span data-stu-id="94a6e-762">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="94a6e-763">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="94a6e-763">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="94a6e-764">Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt.</span><span class="sxs-lookup"><span data-stu-id="94a6e-764">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="94a6e-765">Klicken Sie auf **Hinzufügen** > **Neuer Ordner**.</span><span class="sxs-lookup"><span data-stu-id="94a6e-765">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="94a6e-766">Geben Sie dem Ordner den Namen *Models* .</span><span class="sxs-lookup"><span data-stu-id="94a6e-766">Name the folder *Models*.</span></span>

* <span data-ttu-id="94a6e-767">Klicken Sie mit der rechten Maustaste auf den Ordner *Models* , und wählen Sie **Hinzufügen** > **Klasse** aus.</span><span class="sxs-lookup"><span data-stu-id="94a6e-767">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="94a6e-768">Nennen Sie die Klasse *TodoItem*, und wählen Sie **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="94a6e-768">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="94a6e-769">Ersetzen Sie den Vorlagencode durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="94a6e-769">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="94a6e-770">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="94a6e-770">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="94a6e-771">Fügen Sie einen Ordner mit dem Namen *Models* hinzu.</span><span class="sxs-lookup"><span data-stu-id="94a6e-771">Add a folder named *Models*.</span></span>

* <span data-ttu-id="94a6e-772">Fügen Sie dem Ordner *Models* mit dem folgenden Code eine `TodoItem`-Klasse hinzu:</span><span class="sxs-lookup"><span data-stu-id="94a6e-772">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="94a6e-773">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="94a6e-773">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="94a6e-774">Klicken Sie mit der rechten Maustaste auf das Projekt.</span><span class="sxs-lookup"><span data-stu-id="94a6e-774">Right-click the project.</span></span> <span data-ttu-id="94a6e-775">Klicken Sie auf **Hinzufügen** > **Neuer Ordner**.</span><span class="sxs-lookup"><span data-stu-id="94a6e-775">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="94a6e-776">Geben Sie dem Ordner den Namen *Models* .</span><span class="sxs-lookup"><span data-stu-id="94a6e-776">Name the folder *Models*.</span></span>

  ![Neuer Ordner](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="94a6e-778">Klicken Sie mit der rechten Maustaste auf den Ordner *Models* , und wählen Sie **Hinzufügen** > **Neue Datei** > **Allgemein** > **Leere Klasse** aus.</span><span class="sxs-lookup"><span data-stu-id="94a6e-778">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="94a6e-779">Nennen Sie die Klasse *TodoItem*, und klicken Sie dann auf **Neu**.</span><span class="sxs-lookup"><span data-stu-id="94a6e-779">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="94a6e-780">Ersetzen Sie den Vorlagencode durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="94a6e-780">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="94a6e-781">Die `Id`-Eigenschaft fungiert als eindeutiger Schlüssel in einer relationalen Datenbank.</span><span class="sxs-lookup"><span data-stu-id="94a6e-781">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="94a6e-782">Modellklassen können überall im Projekt platziert werden, doch gemäß der Konvention wird der Ordner *Models* verwendet.</span><span class="sxs-lookup"><span data-stu-id="94a6e-782">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context-21"></a><span data-ttu-id="94a6e-783">Hinzufügen eines Datenbankkontexts 2.1</span><span class="sxs-lookup"><span data-stu-id="94a6e-783">Add a database context 2.1</span></span>

<span data-ttu-id="94a6e-784">Der *Datenbankkontext* ist die Hauptklasse, die die Entity Framework-Funktionen für ein Datenmodell koordiniert.</span><span class="sxs-lookup"><span data-stu-id="94a6e-784">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="94a6e-785">Diese Klasse wird durch Ableiten von der `Microsoft.EntityFrameworkCore.DbContext`-Klasse erstellt.</span><span class="sxs-lookup"><span data-stu-id="94a6e-785">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="94a6e-786">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="94a6e-786">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="94a6e-787">Klicken Sie mit der rechten Maustaste auf den Ordner *Models* , und wählen Sie **Hinzufügen** > **Klasse** aus.</span><span class="sxs-lookup"><span data-stu-id="94a6e-787">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="94a6e-788">Nennen Sie die Klasse *TodoContext*, und klicken Sie auf **Hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="94a6e-788">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="94a6e-789">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="94a6e-789">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="94a6e-790">Fügen Sie eine `TodoContext`-Klasse zum Ordner *Models* hinzu.</span><span class="sxs-lookup"><span data-stu-id="94a6e-790">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="94a6e-791">Ersetzen Sie den Vorlagencode durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="94a6e-791">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context-21"></a><span data-ttu-id="94a6e-792">Registrieren des Datenbankkontexts 2.1</span><span class="sxs-lookup"><span data-stu-id="94a6e-792">Register the database context 2.1</span></span>

<span data-ttu-id="94a6e-793">In ASP.NET Core müssen Dienste wie der Datenbankkontext mit dem Container [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) registriert werden.</span><span class="sxs-lookup"><span data-stu-id="94a6e-793">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="94a6e-794">Der Container stellt den Dienst für Controller bereit.</span><span class="sxs-lookup"><span data-stu-id="94a6e-794">The container provides the service to controllers.</span></span>

<span data-ttu-id="94a6e-795">Aktualisieren Sie *Startup.cs* mit dem folgenden hervorgehobenen Code:</span><span class="sxs-lookup"><span data-stu-id="94a6e-795">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

<span data-ttu-id="94a6e-796">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="94a6e-796">The preceding code:</span></span>

* <span data-ttu-id="94a6e-797">Entfernt nicht benötigte `using`-Deklarationen</span><span class="sxs-lookup"><span data-stu-id="94a6e-797">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="94a6e-798">Fügt dem Abhängigkeitscontainer den Datenbankkontext hinzu</span><span class="sxs-lookup"><span data-stu-id="94a6e-798">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="94a6e-799">Gibt an, dass der Datenbankkontext eine In-Memory Database verwendet</span><span class="sxs-lookup"><span data-stu-id="94a6e-799">Specifies that the database context will use an in-memory database.</span></span>

## <a name="add-a-controller-21"></a><span data-ttu-id="94a6e-800">Hinzufügen eines Controllers 2.1</span><span class="sxs-lookup"><span data-stu-id="94a6e-800">Add a controller 2.1</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="94a6e-801">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="94a6e-801">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="94a6e-802">Klicken Sie mit der rechten Maustaste auf den Ordner *Controller*.</span><span class="sxs-lookup"><span data-stu-id="94a6e-802">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="94a6e-803">Wählen Sie **Hinzufügen** > **Neues Element** aus.</span><span class="sxs-lookup"><span data-stu-id="94a6e-803">Select **Add** > **New Item**.</span></span>
* <span data-ttu-id="94a6e-804">Wählen Sie im Dialogfeld **Neues Element hinzufügen** die Vorlage **API-Controllerklasse** aus.</span><span class="sxs-lookup"><span data-stu-id="94a6e-804">In the **Add New Item** dialog, select the **API Controller Class** template.</span></span>
* <span data-ttu-id="94a6e-805">Benennen Sie die Klasse *TodoController*, und wählen Sie **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="94a6e-805">Name the class *TodoController*, and select **Add**.</span></span>

  ![Dialogfeld „Neues Element hinzufügen“ mit Controller im Suchfeld und ausgewähltem Web-API-Controller](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="94a6e-807">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="94a6e-807">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="94a6e-808">Erstellen Sie im Ordner *Controllers* eine Klasse namens `TodoController`.</span><span class="sxs-lookup"><span data-stu-id="94a6e-808">In the *Controllers* folder, create a class named `TodoController`.</span></span>

---

* <span data-ttu-id="94a6e-809">Ersetzen Sie den Vorlagencode durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="94a6e-809">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

<span data-ttu-id="94a6e-810">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="94a6e-810">The preceding code:</span></span>

* <span data-ttu-id="94a6e-811">Definiert eine API-Controllerklasse ohne Methoden.</span><span class="sxs-lookup"><span data-stu-id="94a6e-811">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="94a6e-812">Markiert die Klasse mit dem [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute)-Attribut.</span><span class="sxs-lookup"><span data-stu-id="94a6e-812">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="94a6e-813">Dieses Attribut gibt an, dass der Controller auf Web-API-Anforderungen reagiert.</span><span class="sxs-lookup"><span data-stu-id="94a6e-813">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="94a6e-814">Weitere Informationen zu bestimmten Verhaltensweisen, die das Attribut ermöglicht, finden Sie unter <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="94a6e-814">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="94a6e-815">Verwendet die Abhängigkeitsinjektion, um den Datenbankkontext (`TodoContext`) dem Controller hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="94a6e-815">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="94a6e-816">Der Datenbankkontext wird in den einzelnen [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete)-Methoden im Controller verwendet.</span><span class="sxs-lookup"><span data-stu-id="94a6e-816">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>
* <span data-ttu-id="94a6e-817">Fügt der Datenbank, falls sie leer ist, ein Element mit dem Namen `Item1` hinzu.</span><span class="sxs-lookup"><span data-stu-id="94a6e-817">Adds an item named `Item1` to the database if the database is empty.</span></span> <span data-ttu-id="94a6e-818">Dieser Code befindet sich im Konstruktor. Er wird bei jeder neuen HTTP-Anforderung ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="94a6e-818">This code is in the constructor, so it runs every time there's a new HTTP request.</span></span> <span data-ttu-id="94a6e-819">Wenn Sie alle Elemente löschen, erstellt der Konstruktor beim nächsten Aufruf einer API-Methode erneut `Item1`.</span><span class="sxs-lookup"><span data-stu-id="94a6e-819">If you delete all items, the constructor creates `Item1` again the next time an API method is called.</span></span> <span data-ttu-id="94a6e-820">So sieht es möglicherweise so aus, als sei die Löschung fehlgeschlagen, obwohl sie erfolgreich war.</span><span class="sxs-lookup"><span data-stu-id="94a6e-820">So it may look like the deletion didn't work when it actually did work.</span></span>

## <a name="add-get-methods-21"></a><span data-ttu-id="94a6e-821">Hinzufügen von GET-Methoden 2.1</span><span class="sxs-lookup"><span data-stu-id="94a6e-821">Add Get methods 2.1</span></span>

<span data-ttu-id="94a6e-822">Fügen Sie der Klasse `TodoController` die folgenden Methoden hinzu, um eine API bereitzustellen, die Aufgaben abruft:</span><span class="sxs-lookup"><span data-stu-id="94a6e-822">To provide an API that retrieves to-do items, add the following methods to the `TodoController` class:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

<span data-ttu-id="94a6e-823">Diese Methoden implementieren zwei GET-Endpunkte:</span><span class="sxs-lookup"><span data-stu-id="94a6e-823">These methods implement two GET endpoints:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="94a6e-824">Halten Sie die App an, falls diese noch ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="94a6e-824">Stop the app if it's still running.</span></span> <span data-ttu-id="94a6e-825">Führen Sie diese dann noch mal aus, damit die letzten Änderungen übernommen werden.</span><span class="sxs-lookup"><span data-stu-id="94a6e-825">Then run it again to include the latest changes.</span></span>

<span data-ttu-id="94a6e-826">Testen Sie die App, indem Sie die beiden Endpunkte in einem Browser aufrufen.</span><span class="sxs-lookup"><span data-stu-id="94a6e-826">Test the app by calling the two endpoints from a browser.</span></span> <span data-ttu-id="94a6e-827">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="94a6e-827">For example:</span></span>

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

<span data-ttu-id="94a6e-828">Die folgende HTTP-Antwort wird durch den Aufruf von `GetTodoItems` erzeugt:</span><span class="sxs-lookup"><span data-stu-id="94a6e-828">The following HTTP response is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths-21"></a><span data-ttu-id="94a6e-829">Routing und URL-Pfade 2.1</span><span class="sxs-lookup"><span data-stu-id="94a6e-829">Routing and URL paths 2.1</span></span>

<span data-ttu-id="94a6e-830">Das Attribut [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) gibt eine Methode an, die auf eine HTTP GET-Anforderung antwortet.</span><span class="sxs-lookup"><span data-stu-id="94a6e-830">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="94a6e-831">Der URL-Pfad für jede Methode wird wie folgt erstellt:</span><span class="sxs-lookup"><span data-stu-id="94a6e-831">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="94a6e-832">Beginnen Sie mit der Vorlagenzeichenfolge im `Route`-Attribut des Controllers:</span><span class="sxs-lookup"><span data-stu-id="94a6e-832">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* <span data-ttu-id="94a6e-833">Ersetzen Sie `[controller]` durch den Namen des Controllers, bei dem es sich standardmäßig um den Namen der Controller-Klasse ohne das Suffix „Controller“ handelt.</span><span class="sxs-lookup"><span data-stu-id="94a6e-833">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="94a6e-834">Bei diesem Beispiel ist der Klassenname des Controllers „**Todo** Controller“, d.h. der Controllername lautet „todo“.</span><span class="sxs-lookup"><span data-stu-id="94a6e-834">For this sample, the controller class name is **Todo** Controller, so the controller name is "todo".</span></span> <span data-ttu-id="94a6e-835">Beim ASP.NET Core-[Routing](xref:mvc/controllers/routing) wird die Groß-/Kleinschreibung nicht beachtet.</span><span class="sxs-lookup"><span data-stu-id="94a6e-835">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="94a6e-836">Wenn das `[HttpGet]`-Attribut eine Routenvorlage (z. B. `[HttpGet("products")]`) hat, fügen Sie diese an den Pfad an.</span><span class="sxs-lookup"><span data-stu-id="94a6e-836">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="94a6e-837">In diesem Beispiel wird keine Vorlage verwendet.</span><span class="sxs-lookup"><span data-stu-id="94a6e-837">This sample doesn't use a template.</span></span> <span data-ttu-id="94a6e-838">Weitere Informationen finden Sie unter [Attributrouting mit Http[Verb]-Attributen](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="94a6e-838">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="94a6e-839">In der folgenden `GetTodoItem`-Methode ist `"{id}"` eine Platzhaltervariable für den eindeutigen Bezeichner des To-do-Elements.</span><span class="sxs-lookup"><span data-stu-id="94a6e-839">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="94a6e-840">Wenn `GetTodoItem` aufgerufen wird, wird der Wert von `"{id}"` in der URL der Methode in ihrem Parameter `id` bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="94a6e-840">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values-21"></a><span data-ttu-id="94a6e-841">Rückgabewerte 2.1</span><span class="sxs-lookup"><span data-stu-id="94a6e-841">Return values 2.1</span></span>

<span data-ttu-id="94a6e-842">Der Rückgabetyp der Methoden `GetTodoItems` und `GetTodoItem` ist der [ActionResult\<T>-Typ](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="94a6e-842">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="94a6e-843">ASP.NET Core serialisiert automatisch das Objekt in [JSON](https://www.json.org/) und schreibt den JSON-Code in den Text der Antwortnachricht.</span><span class="sxs-lookup"><span data-stu-id="94a6e-843">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="94a6e-844">Der Antwortcode für diesen Rückgabetyp ist 200, vorausgesetzt, es gibt keine Ausnahmefehler.</span><span class="sxs-lookup"><span data-stu-id="94a6e-844">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="94a6e-845">Nicht behandelte Ausnahmen werden in 5xx-Fehler übersetzt.</span><span class="sxs-lookup"><span data-stu-id="94a6e-845">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="94a6e-846">`ActionResult`-Rückgabetypen können eine Vielzahl von HTTP-Statuscodes darstellen.</span><span class="sxs-lookup"><span data-stu-id="94a6e-846">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="94a6e-847">Beispielsweise kann `GetTodoItem` zwei verschiedene Statuswerte zurückgeben:</span><span class="sxs-lookup"><span data-stu-id="94a6e-847">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="94a6e-848">Wenn kein Element mit der angeforderten ID übereinstimmt, gibt die Methode einen 404-Fehlercode <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> zurück.</span><span class="sxs-lookup"><span data-stu-id="94a6e-848">If no item matches the requested ID, the method returns a 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="94a6e-849">Andernfalls gibt die Methode 200 mit einem JSON-Antworttext zurück.</span><span class="sxs-lookup"><span data-stu-id="94a6e-849">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="94a6e-850">Die Rückgabe von `item` löst eine HTTP 200-Antwort aus.</span><span class="sxs-lookup"><span data-stu-id="94a6e-850">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="test-the-gettodoitems-method-21"></a><span data-ttu-id="94a6e-851">Testen der GetTodoItems-Methode 2.1</span><span class="sxs-lookup"><span data-stu-id="94a6e-851">Test the GetTodoItems method 2.1</span></span>

<span data-ttu-id="94a6e-852">Dieses Tutorial verwendet Postman zum Testen der Web-API.</span><span class="sxs-lookup"><span data-stu-id="94a6e-852">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="94a6e-853">Installieren Sie [Postman](https://www.getpostman.com/downloads/).</span><span class="sxs-lookup"><span data-stu-id="94a6e-853">Install [Postman](https://www.getpostman.com/downloads/).</span></span>
* <span data-ttu-id="94a6e-854">Starten Sie die Web-App.</span><span class="sxs-lookup"><span data-stu-id="94a6e-854">Start the web app.</span></span>
* <span data-ttu-id="94a6e-855">Starten Sie Postman.</span><span class="sxs-lookup"><span data-stu-id="94a6e-855">Start Postman.</span></span>
* <span data-ttu-id="94a6e-856">Deaktivieren Sie **SSL certificate verification** (Verifizierung des SSL-Zertifikats).</span><span class="sxs-lookup"><span data-stu-id="94a6e-856">Disable **SSL certificate verification**.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="94a6e-857">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="94a6e-857">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="94a6e-858">Deaktivieren Sie auf der Registerkarte **General** (Allgemein) unter File > Settings (**Datei** > **Einstellungen**) **SSL certificate verification** (Verifizierung des SSL-Zertifikats).</span><span class="sxs-lookup"><span data-stu-id="94a6e-858">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="94a6e-859">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="94a6e-859">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="94a6e-860">Deaktivieren Sie auf der Registerkarte **General** (Allgemein) unter **Postman** > **Preferences** (Postman > Einstellungen) **SSL certificate verification** (Verifizierung des SSL-Zertifikats).</span><span class="sxs-lookup"><span data-stu-id="94a6e-860">From **Postman** > **Preferences** (**General** tab), disable **SSL certificate verification**.</span></span> <span data-ttu-id="94a6e-861">Wählen Sie alternativ den Schraubenschlüssel und **Settings** (Einstellungen) aus, und deaktivieren Sie dann die Überprüfung des SSL-Zertifikats.</span><span class="sxs-lookup"><span data-stu-id="94a6e-861">Alternatively, select the wrench and select **Settings**, then disable the SSL certificate verification.</span></span>

---
  
> [!WARNING]
> <span data-ttu-id="94a6e-862">Aktivieren Sie die Verifizierung des SSL-Zertifikats wieder, nachdem Sie den Controller getestet haben.</span><span class="sxs-lookup"><span data-stu-id="94a6e-862">Re-enable SSL certificate verification after testing the controller.</span></span>

* <span data-ttu-id="94a6e-863">Erstellen Sie eine neue Anforderung.</span><span class="sxs-lookup"><span data-stu-id="94a6e-863">Create a new request.</span></span>
  * <span data-ttu-id="94a6e-864">Legen Sie die HTTP-Methode auf **GET** fest.</span><span class="sxs-lookup"><span data-stu-id="94a6e-864">Set the HTTP method to **GET**.</span></span>
  * <span data-ttu-id="94a6e-865">Legen Sie den Anforderungs-URI auf `https://localhost:<port>/api/todo` fest.</span><span class="sxs-lookup"><span data-stu-id="94a6e-865">Set the request URI to `https://localhost:<port>/api/todo`.</span></span> <span data-ttu-id="94a6e-866">Beispielsweise `https://localhost:5001/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="94a6e-866">For example, `https://localhost:5001/api/todo`.</span></span>
* <span data-ttu-id="94a6e-867">Wählen Sie in Postman **Two pane view** (Ansicht mit zwei Bereichen) aus.</span><span class="sxs-lookup"><span data-stu-id="94a6e-867">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="94a6e-868">Wählen Sie **Send** (Senden) aus.</span><span class="sxs-lookup"><span data-stu-id="94a6e-868">Select **Send**.</span></span>

![Postman mit GET-Anforderung](first-web-api/_static/2pv.png)

## <a name="add-a-create-method-21"></a><span data-ttu-id="94a6e-870">Hinzufügen einer Methode 2.1</span><span class="sxs-lookup"><span data-stu-id="94a6e-870">Add a Create method 2.1</span></span>

<span data-ttu-id="94a6e-871">Fügen Sie die folgende `PostTodoItem`-Methode in *Controllers/TodoController.cs* hinzu:</span><span class="sxs-lookup"><span data-stu-id="94a6e-871">Add the following `PostTodoItem` method inside of *Controllers/TodoController.cs*:</span></span> 

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="94a6e-872">Der oben stehende Code ist eine HTTP POST-Methode, wie durch das [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute)-Attribut angegeben.</span><span class="sxs-lookup"><span data-stu-id="94a6e-872">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="94a6e-873">Die Methode ruft den Wert der Aufgabe aus dem Text der HTTP-Anforderung ab.</span><span class="sxs-lookup"><span data-stu-id="94a6e-873">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="94a6e-874">Die `CreatedAtAction`-Methode:</span><span class="sxs-lookup"><span data-stu-id="94a6e-874">The `CreatedAtAction` method:</span></span>

* <span data-ttu-id="94a6e-875">Gibt bei einer erfolgreichen Anforderung den Statuscode „HTTP 201“ zurück.</span><span class="sxs-lookup"><span data-stu-id="94a6e-875">Returns an HTTP 201 status code, if successful.</span></span> <span data-ttu-id="94a6e-876">HTTP 201 ist die Standardantwort für eine HTTP POST-Methode, die eine neue Ressource auf dem Server erstellt.</span><span class="sxs-lookup"><span data-stu-id="94a6e-876">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="94a6e-877">Fügt der Antwort einen `Location`-Header hinzu.</span><span class="sxs-lookup"><span data-stu-id="94a6e-877">Adds a `Location` header to the response.</span></span> <span data-ttu-id="94a6e-878">Der `Location`-Header gibt den URI des neu erstellten To-Do-Elements zurück.</span><span class="sxs-lookup"><span data-stu-id="94a6e-878">The `Location` header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="94a6e-879">Weitere Informationen finden Sie unter [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="94a6e-879">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="94a6e-880">Verweist auf die `GetTodoItem`-Aktion zum Erstellen des URIs des `Location`-Headers.</span><span class="sxs-lookup"><span data-stu-id="94a6e-880">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="94a6e-881">Das `nameof`-Schlüsselwort von C# wird verwendet, um eine Hartcodierung des Aktionsnamens im `CreatedAtAction`-Aufruf zu vermeiden.</span><span class="sxs-lookup"><span data-stu-id="94a6e-881">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method-21"></a><span data-ttu-id="94a6e-882">Testen der PostTodoItem-Methode 2.1</span><span class="sxs-lookup"><span data-stu-id="94a6e-882">Test the PostTodoItem method 2.1</span></span>

* <span data-ttu-id="94a6e-883">Erstellen Sie das Projekt.</span><span class="sxs-lookup"><span data-stu-id="94a6e-883">Build the project.</span></span>
* <span data-ttu-id="94a6e-884">Legen Sie in Postman die HTTP-Methode auf `POST` fest.</span><span class="sxs-lookup"><span data-stu-id="94a6e-884">In Postman, set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="94a6e-885">Legen Sie den URI auf `https://localhost:<port>/api/Todo` fest.</span><span class="sxs-lookup"><span data-stu-id="94a6e-885">Set the URI to `https://localhost:<port>/api/Todo`.</span></span> <span data-ttu-id="94a6e-886">Beispiel: `https://localhost:5001/api/Todo`.</span><span class="sxs-lookup"><span data-stu-id="94a6e-886">For example, `https://localhost:5001/api/Todo`.</span></span>
* <span data-ttu-id="94a6e-887">Wählen Sie die Registerkarte **Body** (Text) aus.</span><span class="sxs-lookup"><span data-stu-id="94a6e-887">Select the **Body** tab.</span></span>
* <span data-ttu-id="94a6e-888">Wählen Sie das Optionsfeld **raw** (Unformatiert) aus.</span><span class="sxs-lookup"><span data-stu-id="94a6e-888">Select the **raw** radio button.</span></span>
* <span data-ttu-id="94a6e-889">Legen Sie den Typ auf **JSON (application/json)** fest.</span><span class="sxs-lookup"><span data-stu-id="94a6e-889">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="94a6e-890">Geben Sie für die Aufgabe den Anforderungstext im JSON-Format ein:</span><span class="sxs-lookup"><span data-stu-id="94a6e-890">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="94a6e-891">Wählen Sie **Send** (Senden) aus.</span><span class="sxs-lookup"><span data-stu-id="94a6e-891">Select **Send**.</span></span>

  ![Postman mit erstellter Anforderung](first-web-api/_static/create.png)

  <span data-ttu-id="94a6e-893">Wenn Sie die Fehlermeldung „405: Methode nicht zulässig“ erhalten, wurde das Projekt wahrscheinlich nicht kompiliert, nachdem die Methode `PostTodoItem` hinzugefügt wurde.</span><span class="sxs-lookup"><span data-stu-id="94a6e-893">If you get a 405 Method Not Allowed error, it's probably the result of not compiling the project after adding the `PostTodoItem` method.</span></span>

### <a name="test-the-location-header-uri-21"></a><span data-ttu-id="94a6e-894">Testen des Adressheader-URIs 2.1</span><span class="sxs-lookup"><span data-stu-id="94a6e-894">Test the location header URI 2.1</span></span>

* <span data-ttu-id="94a6e-895">Wählen Sie auf der Registerkarte **Header** (Header) den Bereich **Response** (Antwort) aus.</span><span class="sxs-lookup"><span data-stu-id="94a6e-895">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="94a6e-896">Kopieren Sie den den Headerwert von **Location** (Speicherort):</span><span class="sxs-lookup"><span data-stu-id="94a6e-896">Copy the **Location** header value:</span></span>

  ![Registerkarte „Header“ in der Postman-Konsole](first-web-api/_static/pmc2.png)

* <span data-ttu-id="94a6e-898">Legen Sie die Methode auf „GET“ fest.</span><span class="sxs-lookup"><span data-stu-id="94a6e-898">Set the method to GET.</span></span>
* <span data-ttu-id="94a6e-899">Legen Sie den URI auf `https://localhost:<port>/api/TodoItems/2` fest.</span><span class="sxs-lookup"><span data-stu-id="94a6e-899">Set the URI to `https://localhost:<port>/api/TodoItems/2`.</span></span> <span data-ttu-id="94a6e-900">Beispiel: `https://localhost:5001/api/TodoItems/2`.</span><span class="sxs-lookup"><span data-stu-id="94a6e-900">For example, `https://localhost:5001/api/TodoItems/2`.</span></span>
* <span data-ttu-id="94a6e-901">Wählen Sie **Send** (Senden) aus.</span><span class="sxs-lookup"><span data-stu-id="94a6e-901">Select **Send**.</span></span>

## <a name="add-a-puttodoitem-method-21"></a><span data-ttu-id="94a6e-902">Hinzufügen einer PutTodoItem-Methode 2.1</span><span class="sxs-lookup"><span data-stu-id="94a6e-902">Add a PutTodoItem method 2.1</span></span>

<span data-ttu-id="94a6e-903">Fügen Sie die folgende `PutTodoItem`-Methode hinzu:</span><span class="sxs-lookup"><span data-stu-id="94a6e-903">Add the following `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="94a6e-904">`PutTodoItem` ähnelt `PostTodoItem`, verwendet allerdings HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="94a6e-904">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="94a6e-905">Die Antwort ist [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="94a6e-905">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="94a6e-906">Gemäß der HTTP-Spezifikation erfordert eine PUT-Anforderung, dass der Client die gesamte aktualisierte Entität (nicht nur die Änderungen) sendet.</span><span class="sxs-lookup"><span data-stu-id="94a6e-906">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="94a6e-907">Verwenden Sie [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute), um Teilupdates zu unterstützen.</span><span class="sxs-lookup"><span data-stu-id="94a6e-907">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="94a6e-908">Wenn beim Aufrufen von `PutTodoItem` ein Fehler zurückgegeben wird, rufen Sie `GET` auf, um sicherzustellen, dass die Datenbank ein Element enthält.</span><span class="sxs-lookup"><span data-stu-id="94a6e-908">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method-21"></a><span data-ttu-id="94a6e-909">Testen der PutTodoItem-Methode 2.1</span><span class="sxs-lookup"><span data-stu-id="94a6e-909">Test the PutTodoItem method 2.1</span></span>

<span data-ttu-id="94a6e-910">In diesem Beispiel wird eine In-Memory-Datenbank verwendet, die jedes Mal initialisiert werden muss, wenn die App gestartet wird.</span><span class="sxs-lookup"><span data-stu-id="94a6e-910">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="94a6e-911">Es muss ein Element in der Datenbank vorhanden sein, bevor Sie einen PUT-Aufruf durchführen.</span><span class="sxs-lookup"><span data-stu-id="94a6e-911">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="94a6e-912">Rufen Sie vor einem PUT-Aufruf GET auf, um sicherzustellen, dass ein Element in der Datenbank vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="94a6e-912">Call GET to ensure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="94a6e-913">Aktualisieren Sie das To-do-Element, das über den ID-Wert 1 verfügt, und legen Sie als Namen „feed fish“ fest:</span><span class="sxs-lookup"><span data-stu-id="94a6e-913">Update the to-do item that has Id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "id":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="94a6e-914">Die folgende Abbildung zeigt das Postman-Update:</span><span class="sxs-lookup"><span data-stu-id="94a6e-914">The following image shows the Postman update:</span></span>

![Postman-Konsole mit der Antwort „204 (Kein Inhalt)“](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method-21"></a><span data-ttu-id="94a6e-916">Hinzufügen einer DeleteTodoItem-Methode 2.1</span><span class="sxs-lookup"><span data-stu-id="94a6e-916">Add a DeleteTodoItem method 2.1</span></span>

<span data-ttu-id="94a6e-917">Fügen Sie die folgende `DeleteTodoItem`-Methode hinzu:</span><span class="sxs-lookup"><span data-stu-id="94a6e-917">Add the following `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="94a6e-918">Die `DeleteTodoItem`-Antwort lautet [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="94a6e-918">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method-21"></a><span data-ttu-id="94a6e-919">Testen der DeleteTodoItem-Methode 2.1</span><span class="sxs-lookup"><span data-stu-id="94a6e-919">Test the DeleteTodoItem method 2.1</span></span>

<span data-ttu-id="94a6e-920">So löschen Sie mit Postman eine Aufgabe</span><span class="sxs-lookup"><span data-stu-id="94a6e-920">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="94a6e-921">Legen Sie die Methode auf `DELETE` fest.</span><span class="sxs-lookup"><span data-stu-id="94a6e-921">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="94a6e-922">Legen Sie den URI des zu löschenden Objekts fest, z. B. `https://localhost:5001/api/todo/1`.</span><span class="sxs-lookup"><span data-stu-id="94a6e-922">Set the URI of the object to delete (for example, `https://localhost:5001/api/todo/1`).</span></span>
* <span data-ttu-id="94a6e-923">Wählen Sie **Send** (Senden) aus.</span><span class="sxs-lookup"><span data-stu-id="94a6e-923">Select **Send**.</span></span>

<span data-ttu-id="94a6e-924">Sie können in der Beispiel-App alle Elemente löschen.</span><span class="sxs-lookup"><span data-stu-id="94a6e-924">The sample app allows you to delete all the items.</span></span> <span data-ttu-id="94a6e-925">Sobald das letzte Element gelöscht wurde, wird allerdings beim nächsten API-Aufruf vom Modellklassenkonstruktor ein neues erstellt.</span><span class="sxs-lookup"><span data-stu-id="94a6e-925">However, when the last item is deleted, a new one is created by the model class constructor the next time the API is called.</span></span>

## <a name="call-the-web-api-with-javascript-21"></a><span data-ttu-id="94a6e-926">Aufrufen der Web-API mit JavaScript 2.1</span><span class="sxs-lookup"><span data-stu-id="94a6e-926">Call the web API with JavaScript 2.1</span></span>

<span data-ttu-id="94a6e-927">In diesem Abschnitt wird eine HTML-Seite hinzugefügt, die mithilfe von JavaScript die Web-API aufruft.</span><span class="sxs-lookup"><span data-stu-id="94a6e-927">In this section, an HTML page is added that uses JavaScript to call the web API.</span></span> <span data-ttu-id="94a6e-928">jQuery initiiert die Anforderung.</span><span class="sxs-lookup"><span data-stu-id="94a6e-928">jQuery initiates the request.</span></span> <span data-ttu-id="94a6e-929">JavaScript aktualisiert die Seite mit den Details aus der Antwort der Web-API.</span><span class="sxs-lookup"><span data-stu-id="94a6e-929">JavaScript updates the page with the details from the web API's response.</span></span>

<span data-ttu-id="94a6e-930">Konfigurieren Sie die App so, dass sie [statische Dateien bereitstellt](xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A), und [aktivieren Sie die Standarddateizuordnung](xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A), indem Sie *startup.cs* mit dem unten markierten Code aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="94a6e-930">Configure the app to [serve static files](xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A) and [enable default file mapping](xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A) by updating *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

<span data-ttu-id="94a6e-931">Erstellen Sie im Projektverzeichnis den Ordner *wwwwroot*.</span><span class="sxs-lookup"><span data-stu-id="94a6e-931">Create a *wwwroot* folder in the project directory.</span></span>

<span data-ttu-id="94a6e-932">Fügen Sie dem Verzeichnis *wwwroot* eine HTML-Datei namens *index.html* hinzu.</span><span class="sxs-lookup"><span data-stu-id="94a6e-932">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="94a6e-933">Ersetzen Sie den Inhalt durch folgendes Markup:</span><span class="sxs-lookup"><span data-stu-id="94a6e-933">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

<span data-ttu-id="94a6e-934">Fügen Sie dem Verzeichnis *wwwroot* eine JavaScript-Datei namens *site.js* hinzu.</span><span class="sxs-lookup"><span data-stu-id="94a6e-934">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="94a6e-935">Ersetzen Sie den Inhalt durch folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="94a6e-935">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="94a6e-936">Möglicherweise ist eine Änderung an den Starteinstellungen des ASP.NET Core-Projekts erforderlich, um die HTML-Seite lokal zu testen:</span><span class="sxs-lookup"><span data-stu-id="94a6e-936">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="94a6e-937">Öffnen Sie *Properties\launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="94a6e-937">Open *Properties\launchSettings.json*.</span></span>
* <span data-ttu-id="94a6e-938">Entfernen Sie die `launchUrl`-Eigenschaft, um zu erzwingen, dass die App mit *index.html* als Startseite geöffnet wird. Dies ist die Standarddatei des Projekts.</span><span class="sxs-lookup"><span data-stu-id="94a6e-938">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="94a6e-939">Dieses Beispiel ruft alle CRUD-Methoden der Web-API auf.</span><span class="sxs-lookup"><span data-stu-id="94a6e-939">This sample calls all of the CRUD methods of the web API.</span></span> <span data-ttu-id="94a6e-940">Im Folgenden werden die API-Aufrufe erläutert.</span><span class="sxs-lookup"><span data-stu-id="94a6e-940">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items-21"></a><span data-ttu-id="94a6e-941">Abrufen einer Liste von To-Do-Elementen 2.1</span><span class="sxs-lookup"><span data-stu-id="94a6e-941">Get a list of to-do items 2.1</span></span>

<span data-ttu-id="94a6e-942">jQuery sendet eine HTTP GET-Anforderung an die Web-API, die JSON-Code mit einem Array aus To-Do-Elementen zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="94a6e-942">jQuery sends an HTTP GET request to the web API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="94a6e-943">Die Rückruffunktion `success` wird aufgerufen, wenn die Anforderung erfolgreich ist.</span><span class="sxs-lookup"><span data-stu-id="94a6e-943">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="94a6e-944">Im Rückruf wird DOM mit den To-Do-Informationen aktualisiert.</span><span class="sxs-lookup"><span data-stu-id="94a6e-944">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item-21"></a><span data-ttu-id="94a6e-945">Hinzufügen eines To-Do-Elements 2.1</span><span class="sxs-lookup"><span data-stu-id="94a6e-945">Add a to-do item 2.1</span></span>

<span data-ttu-id="94a6e-946">jQuery sendet eine HTTP POST-Anforderung mit dem To-Do-Element im Anforderungstext.</span><span class="sxs-lookup"><span data-stu-id="94a6e-946">jQuery sends an HTTP POST request with the to-do item in the request body.</span></span> <span data-ttu-id="94a6e-947">Die Optionen `accepts` und `contentType` werden auf `application/json` festgelegt, um den gesendeten und empfangenen Medientyp anzugeben.</span><span class="sxs-lookup"><span data-stu-id="94a6e-947">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="94a6e-948">Die Aufgabe wird mit [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify) in JSON konvertiert.</span><span class="sxs-lookup"><span data-stu-id="94a6e-948">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="94a6e-949">Wenn die API den Statuscode „Erfolgreich“ zurückgibt, wird die Funktion `getData` aufgerufen, um die HTML-Tabelle zu aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="94a6e-949">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item-21"></a><span data-ttu-id="94a6e-950">Aktualisieren eines To-Do-Elements 2.1</span><span class="sxs-lookup"><span data-stu-id="94a6e-950">Update a to-do item 2.1</span></span>

<span data-ttu-id="94a6e-951">Das Aktualisieren einer Aufgabe ist vergleichbar mit dem Hinzufügen einer Aufgabe.</span><span class="sxs-lookup"><span data-stu-id="94a6e-951">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="94a6e-952">Die `url` ändert sich, um den eindeutigen Bezeichner des Elements hinzuzufügen. Der `type` lautet `PUT`.</span><span class="sxs-lookup"><span data-stu-id="94a6e-952">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item-21"></a><span data-ttu-id="94a6e-953">Löschen eines To-Do-Elements 2.1</span><span class="sxs-lookup"><span data-stu-id="94a6e-953">Delete a to-do item 2.1</span></span>

<span data-ttu-id="94a6e-954">Eine Aufgabe wird folgendermaßen gelöscht: im AJAX-Aufruf wird `type` auf `DELETE` festgelegt, und der eindeutige Bezeichner des Elements wird in der URL angegeben.</span><span class="sxs-lookup"><span data-stu-id="94a6e-954">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

<a name="auth"></a>

## <a name="add-authentication-support-to-a-web-api-21"></a><span data-ttu-id="94a6e-955">Hinzufügen der Authentifizierungsunterstützung zu einer Web-API 2.1</span><span class="sxs-lookup"><span data-stu-id="94a6e-955">Add authentication support to a web API 2.1</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="additional-resources-21"></a><span data-ttu-id="94a6e-956">Zusätzliche Ressourcen 2.1</span><span class="sxs-lookup"><span data-stu-id="94a6e-956">Additional resources 2.1</span></span>

<span data-ttu-id="94a6e-957">[Sie können den Beispielcode für dieses Tutorial anzeigen oder herunterladen.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples)</span><span class="sxs-lookup"><span data-stu-id="94a6e-957">[View or download sample code for this tutorial](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span></span> <span data-ttu-id="94a6e-958">[Informationen zum Herunterladen](xref:index#how-to-download-a-sample) finden Sie hier.</span><span class="sxs-lookup"><span data-stu-id="94a6e-958">See [how to download](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="94a6e-959">Weitere Informationen finden Sie in den folgenden Ressourcen:</span><span class="sxs-lookup"><span data-stu-id="94a6e-959">For more information, see the following resources:</span></span>

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/intro>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [<span data-ttu-id="94a6e-960">Dieses Tutorial auf YouTube</span><span class="sxs-lookup"><span data-stu-id="94a6e-960">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=TTkhEyGBfAk)
