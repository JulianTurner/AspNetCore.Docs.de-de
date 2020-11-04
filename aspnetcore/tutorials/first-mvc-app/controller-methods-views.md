---
title: 'Teil 6: Controllermethoden und Ansichten in ASP.NET Core'
author: rick-anderson
description: Dies ist Teil 6 der Tutorialreihe zum Hinzufügen eines Modells zu einer ASP.NET Core MVC-App.
ms.author: riande
ms.date: 12/13/2018
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
uid: tutorials/first-mvc-app/controller-methods-views
ms.openlocfilehash: 745703aaa4ceb39c75789bab0bde4564f3d79a30
ms.sourcegitcommit: c06a5bf419541d17595af30e4cf6f2787c21855e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92678558"
---
# <a name="part-6-controller-methods-and-views-in-aspnet-core"></a>Teil 6: Controllermethoden und Ansichten in ASP.NET Core

Von [Rick Anderson](https://twitter.com/RickAndMSFT)

Für den Anfang ist die Movie-App schon recht ansprechend, doch es gibt Raum für Verbesserungen. Zum Beispiel sollte **ReleaseDate** zwei Wörter sein.

![Indexansicht: „ReleaseDate“ ist ein Wort (ohne Leerzeichen), und bei jedem Veröffentlichungsdatum wird die Uhrzeit 12: 00 Uhr angezeigt](working-with-sql/_static/m55.png)

Öffnen Sie die Datei *Models/Movie.cs* , und fügen Sie die nachfolgend gezeigten markierten Zeilen hinzu:

[!code-csharp[](start-mvc/sample/MvcMovie22/Models/MovieDateFixed.cs?name=snippet_1&highlight=2,3,12-13,17)]

Im nächsten Tutorial behandeln wir [Datenanmerkungen](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6). Das [Display](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.metadata.displaymetadata)-Attribut gibt an, was für den Namen eines Felds angezeigt werden soll (in diesem Fall „Release Date“ anstatt „ReleaseDate“). Das [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter)-Attribut gibt den Typ der Daten (Datum) an, damit die im Feld gespeicherten Informationen zur Uhrzeit nicht angezeigt werden.

Die Datenanmerkung `[Column(TypeName = "decimal(18, 2)")]` ist erforderlich, damit Entity Framework Core `Price` ordnungsgemäß einer Währung in der Datenbank zuordnen kann. Weitere Informationen finden Sie unter [Datentypen](/ef/core/modeling/relational/data-types).

Navigieren Sie zum `Movies`-Controller, und halten Sie den Mauszeiger über einen **Bearbeiten** -Link, um die Ziel-URL zu sehen.

![Browserfenster mit Maus über dem Link „Bearbeiten“ und der angezeigten Link-URL von https://localhost:5001/Movies/Edit/5](~/tutorials/first-mvc-app/controller-methods-views/_static/edit7.png)

Die Links **Bearbeiten** , **Details** und **Löschen** werden mithilfe des MVC Core-Hilfsprogramms für Ankertags in der Datei *Views/Movies/Index.cshtml* generiert.

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Movies/IndexOriginal.cshtml?highlight=1-3&range=46-50)]

[Taghilfsprogramme](xref:mvc/views/tag-helpers/intro) ermöglichen serverseitigem Code das Mitwirken am Erstellen und Rendern von HTML-Elementen in Razor-Dateien. Im obigen Code generiert `AnchorTagHelper` dynamisch den HTML-`href`-Attributwert aus der Controlleraktionsmethode und der Routen-ID. Verwenden Sie in Ihrem bevorzugten Browser **Quelltext anzeigen** oder die Entwicklertools, um das generierte Markup zu untersuchen. Ein Teil des generierten HTML-Codes wird unten gezeigt:

```html
 <td>
    <a href="/Movies/Edit/4"> Edit </a> |
    <a href="/Movies/Details/4"> Details </a> |
    <a href="/Movies/Delete/4"> Delete </a>
</td>
```

Erinnern Sie sich an das Format für das [Routing](xref:mvc/controllers/routing) in der Datei *Startup.cs* :

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_1&highlight=5)]

ASP.NET Core übersetzt `https://localhost:5001/Movies/Edit/4` in eine Anforderung an die `Edit`-Aktionsmethode des `Movies`-Controllers mit dem `Id`-Parameter 4. (Controllermethoden werden auch als Aktionsmethoden bezeichnet.)

[Taghilfsprogramme](xref:mvc/views/tag-helpers/intro) sind eines der am häufigsten verwendeten neuen Features in ASP.NET Core. Weitere Informationen finden Sie unter [Zusätzliche Ressourcen](#additional-resources).

<a name="get-post"></a>

Öffnen Sie den `Movies`-Controller, und untersuchen Sie die beiden `Edit`-Aktionsmethoden. Der folgende Code zeigt die `HTTP GET Edit`-Methode, die den Film abruft und das Bearbeitungsformular ausfüllt, das von der Razor-Datei *Edit.cshtml* generiert wurde.

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie21/Controllers/MC1.cs?name=snippet_edit1)]

Der folgende Code zeigt die `HTTP POST Edit`-Methode, die die bereitgestellten Filmwerte verarbeitet:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MC1.cs?name=snippet_edit2)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MC1.cs?name=snippet_edit1)]

Der folgende Code zeigt die `HTTP POST Edit`-Methode, die die bereitgestellten Filmwerte verarbeitet:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MC1.cs?name=snippet_edit2)]

::: moniker-end

Das `[Bind]`-Attribut ist eine Möglichkeit zum Schutz vor [zu vielen Angaben](/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application#overpost). Sie sollten nur Eigenschaften in das `[Bind]`-Attribut aufnehmen, die Sie ändern möchten. Weitere Informationen finden Sie unter [Protect your controller from over-posting](/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application) (Schützen Ihres Controllers vor zu vielen Angaben). [ViewModels](https://rachelappel.com/use-viewmodels-to-manage-data-amp-organize-code-in-asp-net-mvc-applications/) bietet eine alternative Methode, um zu viele Angaben zu verhindern.

Beachten Sie, dass der zweiten `Edit`-Aktionsmethode das `[HttpPost]`-Attribut vorangestellt ist.

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie21/Controllers/MC1.cs?name=snippet_edit2&highlight=1)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MC1.cs?name=snippet_edit2&highlight=4)]

::: moniker-end

Das `HttpPost`-Attribut gibt an, dass diese `Edit`-Methode *nur* für `POST`-Anforderungen aufgerufen werden kann. Sie könnten das `[HttpGet]`-Attribut auf die erste Bearbeitungsmethode anwenden, aber dies ist nicht erforderlich, da `[HttpGet]` der Standardwert ist.

Das `ValidateAntiForgeryToken`-Attribut wird verwendet, um [die Fälschung einer Anforderung zu verhindern](xref:security/anti-request-forgery). Es wird einem Fälschungssicherheitstoken zugeordnet, das in der Datei für die Bearbeitungsansicht ( *Views/Movies/Edit.cshtml* ) generiert wird. Die Datei für die Bearbeitungsansicht generiert das Fälschungssicherheitstoken mit dem [Hilfsprogramm für Formulartags](xref:mvc/views/working-with-forms).

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Movies/Edit.cshtml?range=9)]

Das [Hilfsprogramm für Formulartags](xref:mvc/views/working-with-forms) generiert ein ausgeblendetes Fälschungssicherheitstoken, das mit dem von `[ValidateAntiForgeryToken]` generierten Fälschungssicherheitstoken in der `Edit`-Methode des Movies-Controllers übereinstimmen muss. Weitere Informationen finden Sie unter <xref:security/anti-request-forgery>.

Die `HttpGet Edit`-Methode verwendet den `ID`-Parameter eines Films, sucht den Film mit der `FindAsync`-Methode von Entity Framework, und gibt den ausgewählten Film an die Bearbeitungsansicht zurück. Wenn ein Film nicht gefunden werden kann, wird `NotFound` (HTTP 404) zurückgegeben.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie21/Controllers/MC1.cs?name=snippet_edit1)]

Als das Gerüstsystem die Bearbeitungsansicht erstellt hat, wurde die `Movie`-Klasse überprüft und Code zum Rendern der `<label>`- und `<input>`-Elemente für jede Eigenschaft der Klasse erstellt. Das folgende Beispiel zeigt die Bearbeitungsansicht, die vom Visual Studio-Gerüstsystem generiert wurde:

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/EditOriginal.cshtml)]

Beachten Sie, dass die Ansichtsvorlage eine `@model MvcMovie.Models.Movie`-Anweisung am Anfang der Datei aufweist. `@model MvcMovie.Models.Movie` gibt an, dass die Ansicht erwartet, dass das Modell für die Ansichtsvorlage den Typ `Movie` hat.

Der Gerüstcode verwendet mehrere Methoden von Taghilfsprogrammen, um das HTML-Markup zu optimieren. Das [Hilfsprogramm für Bezeichnungstags](xref:mvc/views/working-with-forms) zeigt den Namen des Felds an („Title“, „ReleaseDate“, „Genre“ oder „Price“). Das [Hilfsprogramm für Eingabetags](xref:mvc/views/working-with-forms) rendert ein HTML-`<input>`-Element. Das [Hilfsprogramm für Validierungstags](xref:mvc/views/working-with-forms) zeigt Validierungsmeldungen an, die dieser Eigenschaft zugeordnet sind.

Führen Sie die Anwendung aus, und navigieren Sie zur `/Movies`-URL. Klicken Sie auf einen Link **Bearbeiten**. Zeigen Sie im Browser den Quelltext für die Seite an. Der generierte HTML-Code für das `<form>`-Element wird unten dargestellt.

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Shared/edit_view_source.html?highlight=1,6,10,17,24,28)]

Die `<input>`-Elemente sind in einem `HTML <form>`-Element enthalten, dessen `action`-Attribut so festgelegt ist, dass Beiträge an die URL `/Movies/Edit/id` gesendet werden. Die Formulardaten werden an den Server gesendet, wenn auf die Schaltfläche `Save` geklickt wird. Die letzte Zeile vor dem schließenden `</form>`-Element zeigt das ausgeblendete [XSRF](xref:security/anti-request-forgery)-Token, das durch das [Hilfsprogramm für Formulartags](xref:mvc/views/working-with-forms) generiert wurde.

## <a name="processing-the-post-request"></a>Verarbeiten der POST-Anforderung

Die folgende Liste zeigt die `[HttpPost]`-Version der `Edit`-Aktionsmethode.

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie21/Controllers/MC1.cs?name=snippet_edit2)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MC1.cs?name=snippet_edit2)]

::: moniker-end

Das `[ValidateAntiForgeryToken]`-Attribut überprüft das ausgeblendete [XSRF](xref:security/anti-request-forgery)-Token, das vom Generator für Fälschungssicherheitstoken im [Hilfsprogramm für Formulartags](xref:mvc/views/working-with-forms) generiert wurde.

Das [Modellbindungssystem](xref:mvc/models/model-binding) verwendet die übermittelten Formularwerte und erstellt ein `Movie`-Objekt, das als `movie`-Parameter übergeben wird. Die `ModelState.IsValid`-Eigenschaft stellt sicher, dass die im Formular übermittelten Daten verwendet werden können, um ein `Movie`-Objekt zu ändern (zu bearbeiten oder zu aktualisieren). Wenn die Daten gültig sind, werden sie gespeichert. Die aktualisierten (bearbeiteten) Filmdaten werden in der Datenbank gespeichert, indem die `SaveChangesAsync`-Methode des Datenbankkontexts aufgerufen wird. Nach dem Speichern der Daten leitet der Code den Benutzer an die `Index`-Aktionsmethode der `MoviesController`-Klasse weiter, die die Filmsammlung einschließlich der gerade vorgenommenen Änderungen anzeigt.

Bevor das Formular an den Server gesendet wird, überprüft die clientseitige Validierung alle Validierungsregeln der Felder. Sind Validierungsfehler vorhanden, wird eine Fehlermeldung angezeigt, und das Formular wird nicht gesendet. Wenn JavaScript deaktiviert ist, erfolgt keine clientseitige Validierung. Der Server erkennt jedoch, dass die bereitgestellten Werte nicht gültig sind, und die Formularwerte werden wieder mit Fehlermeldungen angezeigt. Später in diesem Tutorial untersuchen wir die [Modellvalidierung](xref:mvc/models/validation) im Detail. Das [Hilfsprogramm für Validierungstags](xref:mvc/views/working-with-forms) in der Ansichtsvorlage *Views/Movies/Edit.cshtml* übernimmt die Anzeige der entsprechenden Fehlermeldungen.

![Ansicht bearbeiten: Eine Ausnahme für einen falschen Wert für den Preis von „abc“ gibt an, dass das Feld für den Preis eine Zahl sein muss. Eine Ausnahme für einen falschen Wert für das Veröffentlichungsdatum von „xyz“ fordert zur Eingabe eines gültigen Datums auf.](~/tutorials/first-mvc-app/controller-methods-views/_static/val.png)

Alle `HttpGet`-Methoden im Movie-Controller folgen einem ähnlichen Muster. Sie erhalten ein Movie-Objekt (oder eine Liste von Objekten bei `Index`), und übergeben das Objekt (Modell) an die Ansicht. Die `Create`-Methode übergibt ein leeres Movie-Objekt an die Ansicht `Create`. Alle Methoden, die Daten erstellen, bearbeiten, löschen oder in beliebiger Weise ändern, nutzen dazu die `[HttpPost]`-Überladung der Methode. Das Ändern von Daten in einer `HTTP GET`-Methode ist ein Sicherheitsrisiko. Das Ändern von Daten in einer `HTTP GET`-Methode verstößt auch gegen bewährte HTTP-Methoden und das architektonische [REST](http://rest.elkstein.org/)-Muster, das angibt, dass die GET-Anforderungen nicht den Status Ihrer Anwendung ändern dürfen. Die Durchführung eines GET-Vorgangs sollte also eine sichere Operation sein, die keine Nebenwirkungen hat und die permanenten Daten nicht ändert.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Globalisierung und Lokalisierung](xref:fundamentals/localization)
* [Einführung in Taghilfsprogramme](xref:mvc/views/tag-helpers/intro)
* [Erstellen von Taghilfsprogrammen](xref:mvc/views/tag-helpers/authoring)
* <xref:security/anti-request-forgery>
* Schützen Sie Ihre Domänencontroller vor [zu vielen Angaben](/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application)
* [ViewModels](https://rachelappel.com/use-viewmodels-to-manage-data-amp-organize-code-in-asp-net-mvc-applications/)
* [Hilfsprogramm für Formulartags](xref:mvc/views/working-with-forms)
* [Hilfsprogramm für Eingabetags](xref:mvc/views/working-with-forms)
* [Hilfsprogramm für Bezeichnungstags](xref:mvc/views/working-with-forms)
* [Hilfsprogramm für Auswahltags](xref:mvc/views/working-with-forms)
* [Hilfsprogramm für Validierungstags](xref:mvc/views/working-with-forms)

> [!div class="step-by-step"]
> [Zurück](working-with-sql.md)
> [Weiter](search.md)  
