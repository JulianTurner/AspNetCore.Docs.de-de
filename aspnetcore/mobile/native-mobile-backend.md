---
title: Erstellen von Back-End-Diensten für native mobile Apps mit ASP.NET Core
author: rick-anderson
description: Erfahren Sie, wie Back-End-Dienste mit ASP.NET Core MVC zur Unterstützung nativer mobiler Apps erstellt werden.
ms.author: riande
ms.date: 2/12/2021
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
ms.openlocfilehash: e496b7811cc534b6f0f6dfdb857f6e462b38049e
ms.sourcegitcommit: f77a7467651bab61b24261da9dc5c1dd75fc1fa9
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100564022"
---
# <a name="create-backend-services-for-native-mobile-apps-with-aspnet-core"></a>Erstellen von Back-End-Diensten für native mobile Apps mit ASP.NET Core

Von [James Montemagno](https://twitter.com/JamesMontemagno)

Mobile Apps können mit Back-End-Diensten von ASP.NET Core kommunizieren. Anweisungen zum Herstellen einer Verbindung zwischen lokalen Webdienste von iOS-Simulatoren und Android-Emulatoren finden Sie unter [Herstellen von Verbindungen mit lokalen Webdiensten von iOS-Simulatoren und Android-Emulatoren](/xamarin/cross-platform/deploy-test/connect-to-local-web-services).

[Anzeigen oder Herunterladen von Beispielcode für Back-End-Dienste](https://github.com/xamarin/xamarin-forms-samples/tree/master/WebServices/TodoREST)

## <a name="the-sample-native-mobile-app"></a>Die native mobile Beispiel-App

In diesem Tutorial wird veranschaulicht, wie Back-End-Dienste mithilfe von ASP.net Core erstellt werden, um systemeigene Mobile Apps Dabei wird die [xamarin. Forms-App](/xamarin/xamarin-forms/data-cloud/consuming/rest) mit dem nativen Client verwendet, die separate native Clients für Android, IOS und Windows umfasst. Sie können das verlinkten Tutorial befolgen, um die native App zu erstellen (und die erforderlichen kostenfreien Xamarin-Tools zu installieren) und um die Xamarin-Beispielprojektmappe herunterzuladen. Das xamarin-Beispiel enthält ein ASP.net Core Web-API-Dienste-Projekt, das in der ASP.net Core-APP dieses Artikels ersetzt wird (ohne dass vom Client erforderliche Änderungen vorgenommen werden müssen).

![Ausführen der ToDoRest-App auf einem Android-Smartphone](native-mobile-backend/_static/todo-android.png)

### <a name="features"></a>Features

Die Anwendung "um [" unterstützt](https://github.com/xamarin/xamarin-forms-samples/tree/master/WebServices/TodoREST) das auflisten, hinzufügen, löschen und Aktualisieren von To-Do Elementen. Jedes Element verfügt über eine ID, einen Namen, Hinweise und über eine Eigenschaft, die angibt, ob ein Element abgeschlossen ist.

In der Hauptansicht der Elemente werden, wie oben dargestellt, die Namen der einzelnen Elemente aufgeführt. Sie sind mit einem Häkchen versehen, wenn sie abgeschlossen sind.

Durch Tippen auf das Symbol `+` wird ein Dialogfeld zum Hinzufügen eines Elements geöffnet:

![Dialogfeld „Element hinzufügen“](native-mobile-backend/_static/todo-android-new-item.png)

Durch Tippen auf ein Element auf dem Bildschirm mit der Hauptliste wird ein Dialogfeld zur Bearbeitung geöffnet, in dem die Einstellungen „Name“, „Hinweise“, und „Fertig“ für das Element geändert oder das Element gelöscht werden kann:

![Dialogfeld „Element bearbeiten“](native-mobile-backend/_static/todo-android-edit-item.png)

Aktualisieren Sie die APP-Konstante, um Sie selbst für ASP.net Core die APP zu testen, die im nächsten Abschnitt des Computers erstellt wurde [`RestUrl`](https://github.com/xamarin/xamarin-forms-samples/blob/master/WebServices/TodoREST/TodoREST/Constants.cs#L13) .

Android-Emulatoren werden nicht auf dem lokalen Computer ausgeführt und verwenden eine Loopback-IP (10.0.2.2), um mit dem lokalen Computer zu kommunizieren. Verwenden Sie [xamarin. Essentials](/xamarin/essentials/device-information/) , um zu ermitteln, welcher Betriebssystem ausgeführt wird, um die richtige URL zu verwenden.

Navigieren Sie zum [`TodoREST`](https://github.com/xamarin/xamarin-forms-samples/tree/master/WebServices/TodoREST/TodoREST) Projekt, und öffnen Sie die [`Constants.cs`](https://github.com/xamarin/xamarin-forms-samples/blob/master/WebServices/TodoREST/TodoREST/Constants.cs) Datei. Die *Constants.cs* -Datei enthält die folgende Konfiguration.

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoREST/Constants.cs" highlight="13":::

Optional können Sie den Webdienst in einem clouddienst wie Azure bereitstellen und den aktualisieren `RestUrl` .

## <a name="creating-the-aspnet-core-project"></a>Erstellen des ASP.NET Core-Projekts

Erstellen Sie in Visual Studio eine neue ASP.NET Core-Webanwendung. Wählen Sie die Web-API-Vorlage aus. Benennen Sie das Projekt mit " *tdoapi*".

![Dialogfeld „Neue ASP.NET-Webanwendung“ mit ausgewählter Web-API-Projektvorlage](native-mobile-backend/_static/web-api-template.png)

Die APP sollte auf alle an Port 5000 gerichteten Anforderungen (einschließlich Klartext-HTTP-Datenverkehr) für unseren mobilen Client Antworten. Update *Startup.cs* wird <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A> nicht in der Entwicklung ausgeführt:

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Startup.cs" id="snippet" highlight="7-11":::

> [!NOTE]
> Führen Sie die APP direkt anstatt hinter IIS Express aus. IIS Express ignoriert standardmäßig nicht lokale Anforderungen. Führen Sie [dotnet Run](/dotnet/core/tools/dotnet-run) über eine Eingabeaufforderung aus, oder wählen Sie in der Visual Studio-Symbolleiste in der Dropdown Liste Debugziel das Profil App-Name aus.

Fügen Sie eine Modellklasse hinzu, in der die To-Do-Elemente dargestellt werden sollen. Markieren Sie erforderliche Felder mit dem Attribut `[Required]`:

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Models/TodoItem.cs":::

Die API-Methoden müssen mit Daten arbeiten können. Verwenden Sie die gleiche `ITodoRepository`-Schnittstelle, die im ursprünglichen Xamarin-Beispiel verwendet wird:

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Interfaces/ITodoRepository.cs":::

In diesem Beispiel verwendet die Implementierung nur eine private Sammlung von Elementen:

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Services/TodoRepository.cs":::

Konfigurieren Sie die Implementierung in *Startup.cs*:

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Startup.cs" id="snippet2" highlight="3":::

## <a name="creating-the-controller"></a>Erstellen des Controllers

Fügen Sie dem Projekt einen neuen Controller (" [dedoitemscontroller](https://github.com/xamarin/xamarin-forms-samples/tree/master/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs)") hinzu. Er sollte von Erben <xref:Microsoft.AspNetCore.Mvc.ControllerBase> . Fügen Sie das Attribut `Route` hinzu, um anzugeben, dass der Controller Anforderungen für Pfade bearbeitet, die mit `api/todoitems` beginnen. Das Token `[controller]` in der Route wird durch den Namen des Controllers ersetzt (dabei wird das Suffix `Controller` ausgelassen) und ist für globale Routen besonders nützlich. Weitere Informationen zu [Routing](../fundamentals/routing.md).

Damit der Controller funktioniert, ist eine `ITodoRepository`-Schnittstelle erforderlich. Fordern Sie über den Konstruktor des Controllers eine Instanz dieses Typs an. Zur Laufzeit wird diese Instanz über die Frameworkunterstützung für [Dependency Injection](../fundamentals/dependency-injection.md) bereitgestellt.

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs" id="snippetDI":::

Diese API unterstützt vier verschiedene HTTP-Verben zur Durchführung von CRUD-Vorgängen für die Datenquelle (CRUD = Create, Read, Update, Delete; Erstellen, Lesen, Aktualisieren, Löschen). Am einfachsten ist der Lesevorgang, der einer HTTP GET-Anforderung entspricht.

### <a name="reading-items"></a>Lesen von Elementen

Die Anforderung einer Liste mit Elementen erfolgt über eine GET-Anforderung an die Methode `List`. Das Attribut `[HttpGet]` in der Methode `List` gibt an, dass diese Aktion nur GET-Anforderungen verarbeiten soll. Die Route für diese Aktion ist die auf dem Controller angegebene Route. Sie müssen den Aktionsnamen nicht unbedingt als Teil der Route verwenden. Sie müssen nur sicherstellen, dass die einzelnen Aktionen über eine eindeutige Route verfügen. Routingattribute können auf Controller- und auf Methodenebene für die Erstellung bestimmter Routen angewendet werden.

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs" id="snippet":::

Die `List` -Methode gibt einen 200 OK-Antwort Code und alle TODO-Elemente zurück, die als JSON serialisiert werden.

Sie können Ihre neue API-Methode mit einer Vielzahl von Tools testen, z.B. wie im Folgenden dargestellt mit [Postman](https://www.getpostman.com/docs/):

![Postman-Konsole mit einer GET-Anforderung für To-Do-Elemente und dem Antworttext, in dem die JSON für drei zurückgegebene Elemente angezeigt wird](native-mobile-backend/_static/postman-get.png)

### <a name="creating-items"></a>Erstellen von Elementen

Gemäß der Konvention wird die Erstellung neuer Datenelemente dem HTTP POST-Verb zugeordnet. Auf die Methode `Create` wird das Attribut `[HttpPost]` angewendet, und sie akzeptiert eine `TodoItem`-Instanz. Da das Argument `item` im POST-Text übergeben wird, gibt dieser Parameter das Attribut `[FromBody]` an.

Innerhalb der Methode wird überprüft, ob das Element gültig ist und ob es bereits im Datenspeicher vorhanden ist. Wenn keine Probleme auftreten, wird es über das Repository hinzugefügt. Bei der Überprüfung von `ModelState.IsValid` wird eine [Modellvalidierung](../mvc/models/validation.md) durchgeführt. Dies sollte bei jeder API-Methode geschehen, die Benutzereingaben akzeptiert.

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs" id="snippetCreate":::

Das Beispiel verwendet einen `enum` , der Fehlercodes enthält, die an den mobilen Client übermittelt werden:

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs" id="snippetErrorCode":::

Testen Sie das Hinzufügen neuer Elemente mithilfe von Postman, indem Sie das POST-Verb auswählen und im Anforderungstext das neue Objekt im JSON-Format angeben. Sie sollten auch einen Anforderungsheader hinzufügen, in dem ein `Content-Type` von `application/json` angegeben wird.

![Postman-Konsole mit einem POST-Verb und einer Antwort](native-mobile-backend/_static/postman-post.png)

Die Methode gibt das neu erstellte Element in der Antwort zurück.

### <a name="updating-items"></a>Aktualisieren von Elementen

Datensätze werden mithilfe von HTTP PUT-Anforderungen geändert. Abgesehen von dieser Änderung ist die Methode `Edit` mit der Methode `Create` weitgehend identisch. Beachten Sie, dass die Aktion `Edit` die Antwort „`NotFound` (404)“ zurückgibt, wenn der Datensatz nicht gefunden werden kann.

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs" id="snippetEdit":::

Ändern Sie zum Testen mit Postman das Verb in PUT. Geben Sie die aktualisierten Objektdaten in den Anforderungstext ein.

![Postman-Konsole mit einem PUT-Verb und einer Antwort](native-mobile-backend/_static/postman-put.png)

Diese Methode gibt bei erfolgreicher Ausführung die Antwort „`NoContent` (204)“ für die Konsistenz mit der bereits vorhandenen API zurück.

### <a name="deleting-items"></a>Löschen von Elementen | MSDN

Datensätze werden gelöscht, indem DELETE-Anforderungen an den Dienst gestellt werden und die ID des zu löschenden Elements übergeben wird. Wie bei Updates erhalten Anforderungen bei nicht vorhandenen Elementen die Antwort `NotFound`. Bei erfolgreicher Ausführung einer Anforderung hingegen wird die Antwort „`NoContent` (204)“ angezeigt.

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs" id="snippetDelete":::

Beachten Sie, dass beim Testen der DELETE-Funktion im Anforderungstext nichts erforderlich ist.

![Postman-Konsole mit einer DELETE-Funktion und einer Antwort](native-mobile-backend/_static/postman-delete.png)

## <a name="prevent-over-posting"></a>Vermeiden von Overposting

Derzeit macht die Beispiel-App das gesamte `TodoItem`-Objekt verfügbar. In den Produktions-Apps sind die Daten, die eingegeben und mithilfe einer Teilmenge des Modells zurückgegeben werden, in der Regel eingeschränkt. Hierfür gibt es mehrere Gründe, wobei die Sicherheit einer der Hauptgründe ist. Die Teilmenge eines Modells wird üblicherweise als Datenübertragungsobjekt (DTO, Data Transfer Object), Eingabemodell oder Anzeigemodell bezeichnet. In diesem Artikel wird das **DTO** verwendet.

Ein DTO kann für Folgendes verwendet werden:

* Vermeiden Sie Overposting.
* Ausblenden von Eigenschaften, die Clients nicht anzeigen sollen
* Auslassen einiger Eigenschaften, um die Nutzlast zu verringern
* Vereinfachen von Objektgraphen, die geschachtelte Objekte enthalten Vereinfachte Objektgraphen können für Clients zweckmäßiger sein.

Informationen zum veranschaulichen des DTO-Ansatzes finden Sie unter [verhindern von overposting](xref:tutorials/first-web-api#prevent-over-posting) .

## <a name="common-web-api-conventions"></a>Allgemeine Konventionen für die Web-API

Da Sie die Back-End-Dienste für Ihre App entwickeln, sollten Sie sich auch eine Reihe von konsistenten Konventionen oder Richtlinien für den Umgang mit bereichsübergreifenden Anliegen überlegen. Im oben dargestellten Dienst haben Anforderungen für bestimmte Datensätze, die nicht gefunden werden konnten, beispielsweise die Antwort `NotFound` statt der Antwort `BadRequest` erhalten. Gleichermaßen haben für diesen Dienst durchgeführte Befehle, die gebundene Modelltypen übergeben haben, immer `ModelState.IsValid` überprüft und bei ungültigen Modelltypen eine `BadRequest` zurückgegeben.

Sobald Sie eine gängige Richtlinie für Ihre APIs ermittelt haben, können Sie diese in der Regel in einen [Filter](../mvc/controllers/filters.md) einschließen. Weitere Informationen zum [Einschließen gängiger API-Richtlinien in ASP.NET Core MVC-Anwendungen](/archive/msdn-magazine/2016/august/asp-net-core-real-world-asp-net-core-mvc-filters).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Xamarin. Forms: Webdienst Authentifizierung](/xamarin/xamarin-forms/data-cloud/authentication/)
- [Xamarin. Forms: nutzen Sie einen Rest-Webdienst](/xamarin/xamarin-forms/data-cloud/web-services/rest)
- [Microsoft Learn: Verwenden von Rest-Webdiensten in xamarin-apps](/learn/modules/consume-rest-services/)
- [Microsoft Learn: Erstellen einer Web-API mit ASP.NET Core](/learn/modules/build-web-api-aspnet-core/)
