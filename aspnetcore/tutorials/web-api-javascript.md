---
title: 'Tutorial: Aufrufen einer ASP.NET Core-Web-API mit JavaScript'
author: rick-anderson
description: Erfahren Sie, wie Sie eine ASP.NET Core-Web-API mit JavaScript aufrufen.
ms.author: riande
ms.custom: mvc, devx-track-js
ms.date: 11/26/2019
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
uid: tutorials/web-api-javascript
ms.openlocfilehash: b41288bd63267a9aa7035e25ebc8d838eed5d93b
ms.sourcegitcommit: 2e3a967331b2c69f585dd61e9ad5c09763615b44
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92690681"
---
# <a name="tutorial-call-an-aspnet-core-web-api-with-javascript"></a>Tutorial: Aufrufen einer ASP.NET Core-Web-API mit JavaScript

Von [Rick Anderson](https://twitter.com/RickAndMSFT)

In diesem Tutorial erfahren Sie, wie Sie eine ASP.NET Core-Web-API über die [Fetch-API](https://developer.mozilla.org/docs/Web/API/Fetch_API) mit JavaScript aufrufen.

::: moniker range="< aspnetcore-3.0"

Informationen zur Verwendung von ASP.NET Core 2.2 finden Sie im Artikel [Aufrufen einer Web-API mit JavaScript](xref:tutorials/first-web-api#call-the-web-api-with-javascript) (Version für 2.2).

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="prerequisites"></a>Voraussetzungen

* Sie haben [Tutorial: Erstellen einer Web-API](xref:tutorials/first-web-api) abgeschlossen.
* Sie sind mit CSS, HTML und JavaScript vertraut.

## <a name="call-the-web-api-with-javascript"></a>Aufrufen der Web-API mit JavaScript

In diesem Abschnitt fügen Sie eine HTML-Seite hinzu, die Formulare zum Erstellen und Verwalten von To-Do-Elementen enthält. An die Elemente auf der Seite sind Ereignishandler angefügt. Die Ereignishandler führen zu HTTP-Anforderungen an die Aktionsmethoden der Web-API. Die Funktion `fetch` der Fetch-API initiiert jede HTTP-Anforderung.

Die Funktion `fetch` gibt ein [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)-Objekt zurück, das eine HTTP-Antwort in Form eines `Response`-Objekts enthält. Ein gängiges Muster besteht darin, den JSON-Antworttext durch Aufrufen der `json`-Funktion im `Response`-Objekt zu extrahieren. JavaScript aktualisiert die Seite mit den Details aus der Antwort der Web-API.

Ein sehr einfacher `fetch`-Aufruf akzeptiert einen einzelnen Parameter, der die Route darstellt. Ein zweiter Parameter, der als `init`-Objekt bezeichnet wird, ist optional. `init` wird zum Konfigurieren der HTTP-Anforderung verwendet.

1. Konfigurieren Sie die App so, dass [statische Dateien unterstützt](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) werden und die [Standarddateizuordnung aktiviert](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) wird. Der folgende markierte Code wird in der `Configure`-Methode von *Startup.cs* benötigt:

    [!code-csharp[](first-web-api/samples/3.0/TodoApi/StartupJavaScript.cs?highlight=8-9&name=snippet_configure)]

1. Erstellen Sie im Stammverzeichnis den Ordner *wwwroot* .

1. Erstellen Sie einen *js* -Ordner im *wwwroot* -Ordner.

1. Fügen Sie eine HTML-Datei namens *index.html* zum *wwwroot* -Ordner hinzu. Ersetzen Sie den Inhalt von *index.html* durch folgendes Markup:

    [!code-html[](first-web-api/samples/3.0/TodoApi/wwwroot/index.html)]

1. Fügen Sie eine JavaScript-Datei namens *site.js* zum Ordner *wwwroot/js* hinzu. Ersetzen Sie den Inhalt der Datei *site.js* durch den folgenden Code:

    [!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_SiteJs)]

Möglicherweise ist eine Änderung an den Starteinstellungen des ASP.NET Core-Projekts erforderlich, um die HTML-Seite lokal zu testen:

1. Öffnen Sie *Properties\launchSettings.json* .
1. Entfernen Sie die `launchUrl`-Eigenschaft, um zu erzwingen, dass die App mit *index.html* als Startseite geöffnet wird. Dies ist die Standarddatei des Projekts.

Dieses Beispiel ruft alle CRUD-Methoden der Web-API auf. Im Folgenden werden die Web-API-Aufrufe erläutert.

### <a name="get-a-list-of-to-do-items"></a>Abrufen einer Liste von To-Do-Elementen

Im folgenden Code wird eine HTTP GET-Anforderung an die Route *api/TodoItems* gesendet:

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_GetItems)]

Wenn die Web-API den Statuscode „Erfolgreich“ zurückgibt, wird die Funktion `_displayItems` aufgerufen. Jedes To-Do-Element im array-Parameter, das von `_displayItems` akzeptiert wird, wird der Tabelle mit den Schaltflächen **Bearbeiten** und **Löschen** hinzugefügt. Wenn die Web-API-Anforderung nicht erfolgreich ausgeführt wird, wird in der Browserkonsole ein Fehler protokolliert.

### <a name="add-a-to-do-item"></a>Hinzufügen eines To-Do-Elements

Im folgenden Code wird Folgendes ausgeführt:

* Eine `item`-Variable wird deklariert, um eine Objektliteraldarstellung des To-Do-Elements zu erstellen.
* Eine Fetch-Anforderung wird mit den folgenden Optionen konfiguriert:
  * `method` – gibt das POST HTTP-Aktionsverb an.
  * `body` – gibt die JSON-Darstellung des Anforderungstexts an. Die JSON wird erzeugt, indem das in `item` gespeicherte Objektliteral an die Funktion [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify) übergeben wird.
  * `headers` – gibt die HTTP-Anforderungsheader `Accept` und `Content-Type` an. Beide Header sind auf `application/json` festgelegt, um den gesendeten und empfangenen Medientyp anzugeben.
* Eine HTTP POST-Anforderung wird an die Route *api/TodoItems* gesendet.

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_AddItem)]

Wenn die Web-API den Statuscode „Erfolgreich“ zurückgibt, wird die Funktion `getItems` aufgerufen, um die HTML-Tabelle zu aktualisieren. Wenn die Web-API-Anforderung nicht erfolgreich ausgeführt wird, wird in der Browserkonsole ein Fehler protokolliert.

### <a name="update-a-to-do-item"></a>Aktualisieren eines To-Do-Elements

Das Aktualisieren eines To-Do-Elements funktioniert sehr ähnlich wie das Hinzufügen. Allerdings gibt es zwei wesentliche Unterschiede:

* Die Route erhält den eindeutigen Bezeichner des zu aktualisierenden Elements als Suffix. Beispiel: *api/TodoItems/1* .
* Das HTTP-Aktionsverb ist PUT, wie durch die Option `method` angegeben.

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_UpdateItem)]

### <a name="delete-a-to-do-item"></a>Löschen eines To-Do-Elements

Um ein To-Do-Element zu löschen, legen Sie die `method`-Option der Anforderung auf `DELETE` fest, und geben Sie den eindeutigen Bezeichner des Elements in der URL an.

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_DeleteItem)]

Fahren Sie mit dem nächsten Tutorial fort, um zu erfahren, wie Sie Web-API-Hilfeseiten generieren:

> [!div class="nextstepaction"]
> <xref:tutorials/get-started-with-swashbuckle>

::: moniker-end
