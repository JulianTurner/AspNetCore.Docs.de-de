---
title: 'Tutorial: Aufrufen einer ASP.NET Core-Web-API mit JavaScript'
author: rick-anderson
description: Erfahren Sie, wie Sie eine ASP.NET Core-Web-API mit JavaScript aufrufen.
ms.author: riande
ms.custom: mvc, devx-track-js
ms.date: 11/26/2019
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
uid: tutorials/web-api-javascript
ms.openlocfilehash: 4a80e3e82a18e0066c7628c8f40401155538e32b
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061196"
---
# <a name="tutorial-call-an-aspnet-core-web-api-with-javascript"></a><span data-ttu-id="cd3fa-103">Tutorial: Aufrufen einer ASP.NET Core-Web-API mit JavaScript</span><span class="sxs-lookup"><span data-stu-id="cd3fa-103">Tutorial: Call an ASP.NET Core web API with JavaScript</span></span>

<span data-ttu-id="cd3fa-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="cd3fa-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="cd3fa-105">In diesem Tutorial erfahren Sie, wie Sie eine ASP.NET Core-Web-API über die [Fetch-API](https://developer.mozilla.org/docs/Web/API/Fetch_API) mit JavaScript aufrufen.</span><span class="sxs-lookup"><span data-stu-id="cd3fa-105">This tutorial shows how to call an ASP.NET Core web API with JavaScript, using the [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API).</span></span>

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="cd3fa-106">Informationen zur Verwendung von ASP.NET Core 2.2 finden Sie im Artikel [Aufrufen einer Web-API mit JavaScript](xref:tutorials/first-web-api#call-the-web-api-with-javascript) (Version für 2.2).</span><span class="sxs-lookup"><span data-stu-id="cd3fa-106">For ASP.NET Core 2.2, see the 2.2 version of [Call the web API with JavaScript](xref:tutorials/first-web-api#call-the-web-api-with-javascript).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="cd3fa-107">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="cd3fa-107">Prerequisites</span></span>

* <span data-ttu-id="cd3fa-108">Sie haben [Tutorial: Erstellen einer Web-API](xref:tutorials/first-web-api) abgeschlossen.</span><span class="sxs-lookup"><span data-stu-id="cd3fa-108">Complete [Tutorial: Create a web API](xref:tutorials/first-web-api)</span></span>
* <span data-ttu-id="cd3fa-109">Sie sind mit CSS, HTML und JavaScript vertraut.</span><span class="sxs-lookup"><span data-stu-id="cd3fa-109">Familiarity with CSS, HTML, and JavaScript</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="cd3fa-110">Aufrufen der Web-API mit JavaScript</span><span class="sxs-lookup"><span data-stu-id="cd3fa-110">Call the web API with JavaScript</span></span>

<span data-ttu-id="cd3fa-111">In diesem Abschnitt fügen Sie eine HTML-Seite hinzu, die Formulare zum Erstellen und Verwalten von To-Do-Elementen enthält.</span><span class="sxs-lookup"><span data-stu-id="cd3fa-111">In this section, you'll add an HTML page containing forms for creating and managing to-do items.</span></span> <span data-ttu-id="cd3fa-112">An die Elemente auf der Seite sind Ereignishandler angefügt.</span><span class="sxs-lookup"><span data-stu-id="cd3fa-112">Event handlers are attached to elements on the page.</span></span> <span data-ttu-id="cd3fa-113">Die Ereignishandler führen zu HTTP-Anforderungen an die Aktionsmethoden der Web-API.</span><span class="sxs-lookup"><span data-stu-id="cd3fa-113">The event handlers result in HTTP requests to the web API's action methods.</span></span> <span data-ttu-id="cd3fa-114">Die Funktion `fetch` der Fetch-API initiiert jede HTTP-Anforderung.</span><span class="sxs-lookup"><span data-stu-id="cd3fa-114">The Fetch API's `fetch` function initiates each HTTP request.</span></span>

<span data-ttu-id="cd3fa-115">Die Funktion `fetch` gibt ein [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)-Objekt zurück, das eine HTTP-Antwort in Form eines `Response`-Objekts enthält.</span><span class="sxs-lookup"><span data-stu-id="cd3fa-115">The `fetch` function returns a [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) object, which contains an HTTP response represented as a `Response` object.</span></span> <span data-ttu-id="cd3fa-116">Ein gängiges Muster besteht darin, den JSON-Antworttext durch Aufrufen der `json`-Funktion im `Response`-Objekt zu extrahieren.</span><span class="sxs-lookup"><span data-stu-id="cd3fa-116">A common pattern is to extract the JSON response body by invoking the `json` function on the `Response` object.</span></span> <span data-ttu-id="cd3fa-117">JavaScript aktualisiert die Seite mit den Details aus der Antwort der Web-API.</span><span class="sxs-lookup"><span data-stu-id="cd3fa-117">JavaScript updates the page with the details from the web API's response.</span></span>

<span data-ttu-id="cd3fa-118">Ein sehr einfacher `fetch`-Aufruf akzeptiert einen einzelnen Parameter, der die Route darstellt.</span><span class="sxs-lookup"><span data-stu-id="cd3fa-118">The simplest `fetch` call accepts a single parameter representing the route.</span></span> <span data-ttu-id="cd3fa-119">Ein zweiter Parameter, der als `init`-Objekt bezeichnet wird, ist optional.</span><span class="sxs-lookup"><span data-stu-id="cd3fa-119">A second parameter, known as the `init` object, is optional.</span></span> <span data-ttu-id="cd3fa-120">`init` wird zum Konfigurieren der HTTP-Anforderung verwendet.</span><span class="sxs-lookup"><span data-stu-id="cd3fa-120">`init` is used to configure the HTTP request.</span></span>

1. <span data-ttu-id="cd3fa-121">Konfigurieren Sie die App so, dass [statische Dateien unterstützt](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) werden und die [Standarddateizuordnung aktiviert](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) wird.</span><span class="sxs-lookup"><span data-stu-id="cd3fa-121">Configure the app to [serve static files](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [enable default file mapping](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span> <span data-ttu-id="cd3fa-122">Der folgende markierte Code wird in der `Configure`-Methode von *Startup.cs* benötigt:</span><span class="sxs-lookup"><span data-stu-id="cd3fa-122">The following highlighted code is needed in the `Configure` method of *Startup.cs* :</span></span>

    [!code-csharp[](first-web-api/samples/3.0/TodoApi/StartupJavaScript.cs?highlight=8-9&name=snippet_configure)]

1. <span data-ttu-id="cd3fa-123">Erstellen Sie im Stammverzeichnis den Ordner *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="cd3fa-123">Create a *wwwroot* folder in the project root.</span></span>

1. <span data-ttu-id="cd3fa-124">Erstellen Sie einen *js* -Ordner im *wwwroot* -Ordner.</span><span class="sxs-lookup"><span data-stu-id="cd3fa-124">Create a *js* folder inside of the *wwwroot* folder.</span></span>

1. <span data-ttu-id="cd3fa-125">Fügen Sie eine HTML-Datei namens *index.html* zum *wwwroot* -Ordner hinzu.</span><span class="sxs-lookup"><span data-stu-id="cd3fa-125">Add an HTML file named *index.html* to the *wwwroot* folder.</span></span> <span data-ttu-id="cd3fa-126">Ersetzen Sie den Inhalt von *index.html* durch folgendes Markup:</span><span class="sxs-lookup"><span data-stu-id="cd3fa-126">Replace the contents of *index.html* with the following markup:</span></span>

    [!code-html[](first-web-api/samples/3.0/TodoApi/wwwroot/index.html)]

1. <span data-ttu-id="cd3fa-127">Fügen Sie eine JavaScript-Datei namens *site.js* zum Ordner *wwwroot/js* hinzu.</span><span class="sxs-lookup"><span data-stu-id="cd3fa-127">Add a JavaScript file named *site.js* to the *wwwroot/js* folder.</span></span> <span data-ttu-id="cd3fa-128">Ersetzen Sie den Inhalt der Datei *site.js* durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="cd3fa-128">Replace the contents of *site.js* with the following code:</span></span>

    [!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_SiteJs)]

<span data-ttu-id="cd3fa-129">Möglicherweise ist eine Änderung an den Starteinstellungen des ASP.NET Core-Projekts erforderlich, um die HTML-Seite lokal zu testen:</span><span class="sxs-lookup"><span data-stu-id="cd3fa-129">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

1. <span data-ttu-id="cd3fa-130">Öffnen Sie *Properties\launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="cd3fa-130">Open *Properties\launchSettings.json*.</span></span>
1. <span data-ttu-id="cd3fa-131">Entfernen Sie die `launchUrl`-Eigenschaft, um zu erzwingen, dass die App mit *index.html* als Startseite geöffnet wird. Dies ist die Standarddatei des Projekts.</span><span class="sxs-lookup"><span data-stu-id="cd3fa-131">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="cd3fa-132">Dieses Beispiel ruft alle CRUD-Methoden der Web-API auf.</span><span class="sxs-lookup"><span data-stu-id="cd3fa-132">This sample calls all of the CRUD methods of the web API.</span></span> <span data-ttu-id="cd3fa-133">Im Folgenden werden die Web-API-Aufrufe erläutert.</span><span class="sxs-lookup"><span data-stu-id="cd3fa-133">Following are explanations of the web API requests.</span></span>

### <a name="get-a-list-of-to-do-items"></a><span data-ttu-id="cd3fa-134">Abrufen einer Liste von To-Do-Elementen</span><span class="sxs-lookup"><span data-stu-id="cd3fa-134">Get a list of to-do items</span></span>

<span data-ttu-id="cd3fa-135">Im folgenden Code wird eine HTTP GET-Anforderung an die Route *api/TodoItems* gesendet:</span><span class="sxs-lookup"><span data-stu-id="cd3fa-135">In the following code, an HTTP GET request is sent to the *api/TodoItems* route:</span></span>

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_GetItems)]

<span data-ttu-id="cd3fa-136">Wenn die Web-API den Statuscode „Erfolgreich“ zurückgibt, wird die Funktion `_displayItems` aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="cd3fa-136">When the web API returns a successful status code, the `_displayItems` function is invoked.</span></span> <span data-ttu-id="cd3fa-137">Jedes To-Do-Element im array-Parameter, das von `_displayItems` akzeptiert wird, wird der Tabelle mit den Schaltflächen **Bearbeiten** und **Löschen** hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="cd3fa-137">Each to-do item in the array parameter accepted by `_displayItems` is added to a table with **Edit** and **Delete** buttons.</span></span> <span data-ttu-id="cd3fa-138">Wenn die Web-API-Anforderung nicht erfolgreich ausgeführt wird, wird in der Browserkonsole ein Fehler protokolliert.</span><span class="sxs-lookup"><span data-stu-id="cd3fa-138">If the web API request fails, an error is logged to the browser's console.</span></span>

### <a name="add-a-to-do-item"></a><span data-ttu-id="cd3fa-139">Hinzufügen eines To-Do-Elements</span><span class="sxs-lookup"><span data-stu-id="cd3fa-139">Add a to-do item</span></span>

<span data-ttu-id="cd3fa-140">Im folgenden Code wird Folgendes ausgeführt:</span><span class="sxs-lookup"><span data-stu-id="cd3fa-140">In the following code:</span></span>

* <span data-ttu-id="cd3fa-141">Eine `item`-Variable wird deklariert, um eine Objektliteraldarstellung des To-Do-Elements zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="cd3fa-141">An `item` variable is declared to construct an object literal representation of the to-do item.</span></span>
* <span data-ttu-id="cd3fa-142">Eine Fetch-Anforderung wird mit den folgenden Optionen konfiguriert:</span><span class="sxs-lookup"><span data-stu-id="cd3fa-142">A Fetch request is configured with the following options:</span></span>
  * <span data-ttu-id="cd3fa-143">`method` – gibt das POST HTTP-Aktionsverb an.</span><span class="sxs-lookup"><span data-stu-id="cd3fa-143">`method`&mdash;specifies the POST HTTP action verb.</span></span>
  * <span data-ttu-id="cd3fa-144">`body` – gibt die JSON-Darstellung des Anforderungstexts an.</span><span class="sxs-lookup"><span data-stu-id="cd3fa-144">`body`&mdash;specifies the JSON representation of the request body.</span></span> <span data-ttu-id="cd3fa-145">Die JSON wird erzeugt, indem das in `item` gespeicherte Objektliteral an die Funktion [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify) übergeben wird.</span><span class="sxs-lookup"><span data-stu-id="cd3fa-145">The JSON is produced by passing the object literal stored in `item` to the [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify) function.</span></span>
  * <span data-ttu-id="cd3fa-146">`headers` – gibt die HTTP-Anforderungsheader `Accept` und `Content-Type` an.</span><span class="sxs-lookup"><span data-stu-id="cd3fa-146">`headers`&mdash;specifies the `Accept` and `Content-Type` HTTP request headers.</span></span> <span data-ttu-id="cd3fa-147">Beide Header sind auf `application/json` festgelegt, um den gesendeten und empfangenen Medientyp anzugeben.</span><span class="sxs-lookup"><span data-stu-id="cd3fa-147">Both headers are set to `application/json` to specify the media type being received and sent, respectively.</span></span>
* <span data-ttu-id="cd3fa-148">Eine HTTP POST-Anforderung wird an die Route *api/TodoItems* gesendet.</span><span class="sxs-lookup"><span data-stu-id="cd3fa-148">An HTTP POST request is sent to the *api/TodoItems* route.</span></span>

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_AddItem)]

<span data-ttu-id="cd3fa-149">Wenn die Web-API den Statuscode „Erfolgreich“ zurückgibt, wird die Funktion `getItems` aufgerufen, um die HTML-Tabelle zu aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="cd3fa-149">When the web API returns a successful status code, the `getItems` function is invoked to update the HTML table.</span></span> <span data-ttu-id="cd3fa-150">Wenn die Web-API-Anforderung nicht erfolgreich ausgeführt wird, wird in der Browserkonsole ein Fehler protokolliert.</span><span class="sxs-lookup"><span data-stu-id="cd3fa-150">If the web API request fails, an error is logged to the browser's console.</span></span>

### <a name="update-a-to-do-item"></a><span data-ttu-id="cd3fa-151">Aktualisieren eines To-Do-Elements</span><span class="sxs-lookup"><span data-stu-id="cd3fa-151">Update a to-do item</span></span>

<span data-ttu-id="cd3fa-152">Das Aktualisieren eines To-Do-Elements funktioniert sehr ähnlich wie das Hinzufügen. Allerdings gibt es zwei wesentliche Unterschiede:</span><span class="sxs-lookup"><span data-stu-id="cd3fa-152">Updating a to-do item is similar to adding one; however, there are two significant differences:</span></span>

* <span data-ttu-id="cd3fa-153">Die Route erhält den eindeutigen Bezeichner des zu aktualisierenden Elements als Suffix.</span><span class="sxs-lookup"><span data-stu-id="cd3fa-153">The route is suffixed with the unique identifier of the item to update.</span></span> <span data-ttu-id="cd3fa-154">Beispiel: *api/TodoItems/1*.</span><span class="sxs-lookup"><span data-stu-id="cd3fa-154">For example, *api/TodoItems/1*.</span></span>
* <span data-ttu-id="cd3fa-155">Das HTTP-Aktionsverb ist PUT, wie durch die Option `method` angegeben.</span><span class="sxs-lookup"><span data-stu-id="cd3fa-155">The HTTP action verb is PUT, as indicated by the `method` option.</span></span>

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_UpdateItem)]

### <a name="delete-a-to-do-item"></a><span data-ttu-id="cd3fa-156">Löschen eines To-Do-Elements</span><span class="sxs-lookup"><span data-stu-id="cd3fa-156">Delete a to-do item</span></span>

<span data-ttu-id="cd3fa-157">Um ein To-Do-Element zu löschen, legen Sie die `method`-Option der Anforderung auf `DELETE` fest, und geben Sie den eindeutigen Bezeichner des Elements in der URL an.</span><span class="sxs-lookup"><span data-stu-id="cd3fa-157">To delete a to-do item, set the request's `method` option to `DELETE` and specify the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_DeleteItem)]

<span data-ttu-id="cd3fa-158">Fahren Sie mit dem nächsten Tutorial fort, um zu erfahren, wie Sie Web-API-Hilfeseiten generieren:</span><span class="sxs-lookup"><span data-stu-id="cd3fa-158">Advance to the next tutorial to learn how to generate web API help pages:</span></span>

> [!div class="nextstepaction"]
> <xref:tutorials/get-started-with-swashbuckle>

::: moniker-end
