---
title: Erstellen von Back-End-Diensten für native mobile Apps mit ASP.NET Core
author: ardalis
description: Erfahren Sie, wie Back-End-Dienste mit ASP.NET Core MVC zur Unterstützung nativer mobiler Apps erstellt werden.
ms.author: riande
ms.date: 12/05/2019
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
uid: mobile/native-mobile-backend
ms.openlocfilehash: 4e86241771e884ba9079bcdf9a09eebc6acd62c8
ms.sourcegitcommit: e311cfb77f26a0a23681019bd334929d1aaeda20
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99530215"
---
# <a name="create-backend-services-for-native-mobile-apps-with-aspnet-core"></a>Erstellen von Back-End-Diensten für native mobile Apps mit ASP.NET Core

Von [Steve Smith](https://ardalis.com/)

Mobile Apps können mit Back-End-Diensten von ASP.NET Core kommunizieren. Anweisungen zum Herstellen einer Verbindung zwischen lokalen Webdienste von iOS-Simulatoren und Android-Emulatoren finden Sie unter [Herstellen von Verbindungen mit lokalen Webdiensten von iOS-Simulatoren und Android-Emulatoren](/xamarin/cross-platform/deploy-test/connect-to-local-web-services).

[Anzeigen oder Herunterladen von Beispielcode für Back-End-Dienste](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mobile/native-mobile-backend/sample)

## <a name="the-sample-native-mobile-app"></a>Die native mobile Beispiel-App

In diesem Tutorial wird veranschaulicht, wie Back-End-Dienste mit ASP.NET Core MVC zur Unterstützung nativer mobiler Apps erstellt werden. Darin wird die [Xamarin.Forms-App ToDoRest](/xamarin/xamarin-forms/data-cloud/consuming/rest) als nativer Client verwendet, die separate native Clients für Android-, iOS-, Windows Universal- und Windows Phone-Geräte umfasst. Sie können das verlinkten Tutorial befolgen, um die native App zu erstellen (und die erforderlichen kostenfreien Xamarin-Tools zu installieren) und um die Xamarin-Beispielprojektmappe herunterzuladen. Das Xamarin-Beispiel umfasst ein Dienstprojekt der ASP.NET-Web-API 2, das die ASP.NET Core-App aus diesem Artikel ersetzt (dabei sind keine Änderungen durch den Client erforderlich).

![Ausführen der ToDoRest-App auf einem Android-Smartphone](native-mobile-backend/_static/todo-android.png)

### <a name="features"></a>Features

Die ToDoRest-App unterstützt das Auflisten, Hinzufügen, Löschen und Aktualisieren von To-Do-Elementen. Jedes Element verfügt über eine ID, einen Namen, Hinweise und über eine Eigenschaft, die angibt, ob ein Element abgeschlossen ist.

In der Hauptansicht der Elemente werden, wie oben dargestellt, die Namen der einzelnen Elemente aufgeführt. Sie sind mit einem Häkchen versehen, wenn sie abgeschlossen sind.

Durch Tippen auf das Symbol `+` wird ein Dialogfeld zum Hinzufügen eines Elements geöffnet:

![Dialogfeld „Element hinzufügen“](native-mobile-backend/_static/todo-android-new-item.png)

Durch Tippen auf ein Element auf dem Bildschirm mit der Hauptliste wird ein Dialogfeld zur Bearbeitung geöffnet, in dem die Einstellungen „Name“, „Hinweise“, und „Fertig“ für das Element geändert oder das Element gelöscht werden kann:

![Dialogfeld „Element bearbeiten“](native-mobile-backend/_static/todo-android-edit-item.png)

Dieses Beispiel ist standardmäßig für die Verwendung von Back-End-Diensten konfiguriert, die unter developer.xamarin.com gehostet werden, und in denen schreibgeschützte Vorgänge zugelassen sind. Wenn Sie dies selbst bei der im nächsten Abschnitt erstellten ASP.NET Core-App testen möchten, die auf Ihrem Computer ausgeführt wird, müssen Sie die `RestUrl`-Konstante der App aktualisieren. Navigieren Sie zu dem Projekt `ToDoREST`, und öffnen Sie die Datei *Constants.cs*. Ersetzen Sie die `RestUrl` durch eine URL, die die IP-Adresse Ihres Computers enthält (nicht „localhost“ oder „127.0.0.1“, da diese Adresse vom Geräteemulator und nicht von Ihrem Computer verwendet wird). Schließen Sie auch die Portnummer (5000) ein. Stellen Sie sicher, dass keine aktive Firewall den Zugriff auf diesen Port blockiert, wenn Sie testen möchten, ob Ihre Dienste auf einem Gerät funktionieren.

```csharp
// URL of REST service (Xamarin ReadOnly Service)
//public static string RestUrl = "http://developer.xamarin.com:8081/api/todoitems{0}";

// use your machine's IP address
public static string RestUrl = "http://192.168.1.207:5000/api/todoitems/{0}";
```

## <a name="creating-the-aspnet-core-project"></a>Erstellen des ASP.NET Core-Projekts

Erstellen Sie in Visual Studio eine neue ASP.NET Core-Webanwendung. Wählen Sie die Web-API-Vorlage und „Keine Authentifizierung“ aus. Geben Sie dem Projekt den Namen *ToDoApi*.

![Dialogfeld „Neue ASP.NET-Webanwendung“ mit ausgewählter Web-API-Projektvorlage](native-mobile-backend/_static/web-api-template.png)

Die Anwendung sollte auf alle an Port 5000 gestellten Anforderungen reagieren. Aktualisieren Sie *Program.cs*, und schließen Sie `.UseUrls("http://*:5000")` ein, um Folgendes zu erreichen:

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Program.cs?range=10-16&highlight=3)]

> [!NOTE]
> Stellen Sie sicher, dass Sie die Anwendung direkt ausführen und nicht nach IIS Express, da dieser Dienst die nicht lokalen Anforderungen standardmäßig ignoriert. Führen Sie [dotnet run](/dotnet/core/tools/dotnet-run) über eine Eingabeaufforderung aus, oder wählen Sie aus der Dropdownliste „Debugziel“ in der Symbolleiste von Visual Studio das Profil des Anwendungsnamens aus.

Fügen Sie eine Modellklasse hinzu, in der die To-Do-Elemente dargestellt werden sollen. Markieren Sie erforderliche Felder mit dem Attribut `[Required]`:

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Models/ToDoItem.cs)]

Die API-Methoden müssen mit Daten arbeiten können. Verwenden Sie die gleiche `IToDoRepository`-Schnittstelle, die im ursprünglichen Xamarin-Beispiel verwendet wird:

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Interfaces/IToDoRepository.cs)]

In diesem Beispiel verwendet die Implementierung nur eine private Sammlung von Elementen:

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Services/ToDoRepository.cs)]

Konfigurieren Sie die Implementierung in *Startup.cs*:

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Startup.cs?highlight=6&range=29-35)]

An diesem Punkt sind Sie bereit für die Erstellung von *ToDoItemsController*.

> [!TIP]
> Weitere Informationen zur Erstellung von Web-APIs finden Sie unter [Build your first Web API with ASP.NET Core MVC and Visual Studio (Erstellen Ihrer ersten Web-API mit ASP.NET Core MVC und Visual Studio)](../tutorials/first-web-api.md).

## <a name="creating-the-controller"></a>Erstellen des Controllers

Fügen Sie einen neuen Controller, *ToDoItemsController*, zu dem Projekt hinzu. Dieser sollte von „Microsoft.AspNetCore.Mvc.Controller“ erben. Fügen Sie das Attribut `Route` hinzu, um anzugeben, dass der Controller Anforderungen für Pfade bearbeitet, die mit `api/todoitems` beginnen. Das Token `[controller]` in der Route wird durch den Namen des Controllers ersetzt (dabei wird das Suffix `Controller` ausgelassen) und ist für globale Routen besonders nützlich. Weitere Informationen zu [Routing](../fundamentals/routing.md).

Damit der Controller funktioniert, ist eine `IToDoRepository`-Schnittstelle erforderlich. Fordern Sie über den Konstruktor des Controllers eine Instanz dieses Typs an. Zur Laufzeit wird diese Instanz über die Frameworkunterstützung für [Dependency Injection](../fundamentals/dependency-injection.md) bereitgestellt.

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=1-17&highlight=9,14)]

Diese API unterstützt vier verschiedene HTTP-Verben zur Durchführung von CRUD-Vorgängen für die Datenquelle (CRUD = Create, Read, Update, Delete; Erstellen, Lesen, Aktualisieren, Löschen). Am einfachsten ist der Lesevorgang, der einer HTTP GET-Anforderung entspricht.

### <a name="reading-items"></a>Lesen von Elementen

Die Anforderung einer Liste mit Elementen erfolgt über eine GET-Anforderung an die Methode `List`. Das Attribut `[HttpGet]` in der Methode `List` gibt an, dass diese Aktion nur GET-Anforderungen verarbeiten soll. Die Route für diese Aktion ist die auf dem Controller angegebene Route. Sie müssen den Aktionsnamen nicht unbedingt als Teil der Route verwenden. Sie müssen nur sicherstellen, dass die einzelnen Aktionen über eine eindeutige Route verfügen. Routingattribute können auf Controller- und auf Methodenebene für die Erstellung bestimmter Routen angewendet werden.

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=19-23)]

Die Methode `List` gibt den Antwortcode „200 OK“ und alle als JSON serialisierten To-Do-Elemente zurück.

Sie können Ihre neue API-Methode mit einer Vielzahl von Tools testen, z.B. wie im Folgenden dargestellt mit [Postman](https://www.getpostman.com/docs/):

![Postman-Konsole mit einer GET-Anforderung für To-Do-Elemente und dem Antworttext, in dem die JSON für drei zurückgegebene Elemente angezeigt wird](native-mobile-backend/_static/postman-get.png)

### <a name="creating-items"></a>Erstellen von Elementen

Gemäß der Konvention wird die Erstellung neuer Datenelemente dem HTTP POST-Verb zugeordnet. Auf die Methode `Create` wird das Attribut `[HttpPost]` angewendet, und sie akzeptiert eine `ToDoItem`-Instanz. Da das Argument `item` im POST-Text übergeben wird, gibt dieser Parameter das Attribut `[FromBody]` an.

Innerhalb der Methode wird überprüft, ob das Element gültig ist und ob es bereits im Datenspeicher vorhanden ist. Wenn keine Probleme auftreten, wird es über das Repository hinzugefügt. Bei der Überprüfung von `ModelState.IsValid` wird eine [Modellvalidierung](../mvc/models/validation.md) durchgeführt. Dies sollte bei jeder API-Methode geschehen, die Benutzereingaben akzeptiert.

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=25-46)]

Im Beispiel wird eine Enumeration mit Fehlercodes verwendet, die an den mobilen Client übergeben werden:

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=91-99)]

Testen Sie das Hinzufügen neuer Elemente mithilfe von Postman, indem Sie das POST-Verb auswählen und im Anforderungstext das neue Objekt im JSON-Format angeben. Sie sollten auch einen Anforderungsheader hinzufügen, in dem ein `Content-Type` von `application/json` angegeben wird.

![Postman-Konsole mit einem POST-Verb und einer Antwort](native-mobile-backend/_static/postman-post.png)

Die Methode gibt das neu erstellte Element in der Antwort zurück.

### <a name="updating-items"></a>Aktualisieren von Elementen

Datensätze werden mithilfe von HTTP PUT-Anforderungen geändert. Abgesehen von dieser Änderung ist die Methode `Edit` mit der Methode `Create` weitgehend identisch. Beachten Sie, dass die Aktion `Edit` die Antwort „`NotFound` (404)“ zurückgibt, wenn der Datensatz nicht gefunden werden kann.

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=48-69)]

Ändern Sie zum Testen mit Postman das Verb in PUT. Geben Sie die aktualisierten Objektdaten in den Anforderungstext ein.

![Postman-Konsole mit einem PUT-Verb und einer Antwort](native-mobile-backend/_static/postman-put.png)

Diese Methode gibt bei erfolgreicher Ausführung die Antwort „`NoContent` (204)“ für die Konsistenz mit der bereits vorhandenen API zurück.

### <a name="deleting-items"></a>Löschen von Elementen | MSDN

Datensätze werden gelöscht, indem DELETE-Anforderungen an den Dienst gestellt werden und die ID des zu löschenden Elements übergeben wird. Wie bei Updates erhalten Anforderungen bei nicht vorhandenen Elementen die Antwort `NotFound`. Bei erfolgreicher Ausführung einer Anforderung hingegen wird die Antwort „`NoContent` (204)“ angezeigt.

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=71-88)]

Beachten Sie, dass beim Testen der DELETE-Funktion im Anforderungstext nichts erforderlich ist.

![Postman-Konsole mit einer DELETE-Funktion und einer Antwort](native-mobile-backend/_static/postman-delete.png)

## <a name="common-web-api-conventions"></a>Allgemeine Konventionen für die Web-API

Da Sie die Back-End-Dienste für Ihre App entwickeln, sollten Sie sich auch eine Reihe von konsistenten Konventionen oder Richtlinien für den Umgang mit bereichsübergreifenden Anliegen überlegen. Im oben dargestellten Dienst haben Anforderungen für bestimmte Datensätze, die nicht gefunden werden konnten, beispielsweise die Antwort `NotFound` statt der Antwort `BadRequest` erhalten. Gleichermaßen haben für diesen Dienst durchgeführte Befehle, die gebundene Modelltypen übergeben haben, immer `ModelState.IsValid` überprüft und bei ungültigen Modelltypen eine `BadRequest` zurückgegeben.

Sobald Sie eine gängige Richtlinie für Ihre APIs ermittelt haben, können Sie diese in der Regel in einen [Filter](../mvc/controllers/filters.md) einschließen. Weitere Informationen zum [Einschließen gängiger API-Richtlinien in ASP.NET Core MVC-Anwendungen](/archive/msdn-magazine/2016/august/asp-net-core-real-world-asp-net-core-mvc-filters).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Xamarin. Forms: Webdienst Authentifizierung](/xamarin/xamarin-forms/data-cloud/authentication/)
- [Xamarin. Forms: nutzen Sie einen Rest-Webdienst](/xamarin/xamarin-forms/data-cloud/web-services/rest)
- [Microsoft Learn: Verwenden von Rest-Webdiensten in xamarin-apps](/learn/modules/consume-rest-services/)
- [Microsoft Learn: Erstellen einer Web-API mit ASP.net Core](/learn/modules/build-web-api-aspnet-core/)
