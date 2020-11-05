---
title: 'Teil 7: Hinzufügen der Suche zu einer ASP.NET Core MVC-App'
author: rick-anderson
description: Dies ist Teil 7 der Tutorialreihe zu ASP.NET Core MVC.
ms.author: riande
ms.date: 12/13/2018
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
uid: tutorials/first-mvc-app/search
ms.openlocfilehash: 657072803f59feb99de8b31ddb3a6433d832aa30
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059623"
---
# <a name="part-7-add-search-to-an-aspnet-core-mvc-app"></a>Teil 7: Hinzufügen der Suche zu einer ASP.NET Core MVC-App

Von [Rick Anderson](https://twitter.com/RickAndMSFT)

In diesem Abschnitt fügen Sie die Suchfunktion zur Aktionsmethode `Index` hinzu, mit der Sie Filme nach *Genre* oder *Name* suchen können.

Aktualisieren Sie die `Index`-Methode in *Controllers/MoviesController.cs* mit folgendem Code:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_1stSearch)]

Die erste Zeile der Aktionsmethode `Index` erstellt eine [LINQ](/dotnet/standard/using-linq)-Abfrage zum Auswählen der Filme:

```csharp
var movies = from m in _context.Movie
             select m;
```

Die Abfrage wird an dieser Stelle *nur* definiert und **nicht** auf die Datenbank angewendet.

Wenn der `searchString`-Parameter eine Zeichenfolge enthält, wird die Filmabfrage so geändert, dass nach dem Wert der Suchzeichenfolge gefiltert wird:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_SearchNull2)]

Der Code `s => s.Title.Contains()` oben ist ein [Lambdaausdruck](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions). Lambdas werden in methodenbasierten [LINQ](/dotnet/standard/using-linq)-Abfragen als Argumente für standardmäßige Abfrageoperatormethoden wie die [Where](/dotnet/api/system.linq.enumerable.where)-Methode oder `Contains` verwendet (siehe den vorangehenden Code). LINQ-Abfragen werden nicht ausgeführt, wenn sie definiert oder durch Aufrufen einer Methode geändert werden (z. B. `Where`, `Contains` oder `OrderBy`). Stattdessen wird die Ausführung der Abfrage verzögert.  Dies bedeutet, dass die Auswertung eines Ausdrucks so lange hinausgezögert wird, bis dessen realisierter Wert tatsächlich durchlaufen oder die `ToListAsync`-Methode aufgerufen wird. Weitere Informationen zur verzögerten Abfrageausführung finden Sie unter [Abfrageausführung](/dotnet/framework/data/adonet/ef/language-reference/query-execution).

Hinweis: Die [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains)-Methode wird in der Datenbank und nicht im oben gezeigten C#-Code ausgeführt. Die Groß-/Kleinschreibung in der Abfrage hängt von der Datenbank und Sortierung ab. In SQL Server wird [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) zu [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql) zugeordnet, das Groß-/Kleinschreibung nicht beachtet. In SQLite wird bei der Standardsortierung Groß-/Kleinschreibung beachtet.

Navigieren Sie zu `/Movies/Index`. Fügen Sie eine Abfragezeichenfolge wie `?searchString=Ghost` an die URL an. Die gefilterten Filme werden angezeigt.

![Indexansicht](~/tutorials/first-mvc-app/search/_static/ghost.png)

Wenn Sie die Signatur der `Index`-Methode so ändern, dass sie einen Parameter mit dem Namen `id` hat, entspricht der Parameter `id` dem optionalen Platzhalter `{id}` für die Standardrouten, die in *Startup.cs* festgelegt sind.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Startup.cs?highlight=5&name=snippet_1)]

Ändern Sie den Parameter in `id` und alle Vorkommen von `searchString` in `id`.

Die vorherige `Index`-Methode:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1,6,8&name=snippet_1stSearch)]

Die aktualisierte `Index`-Methode mit `id`-Parameter:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1,6,8&name=snippet_SearchID)]

Sie können nun den Suchtitel als Routendaten (ein URL-Segment) anstatt als Wert einer Abfragezeichenfolge übergeben.

![Indexansicht mit dem der URL hinzugefügten Wort „ghost“ und einer zurückgegebenen Filmliste mit zwei Filmen: Ghostbusters und Ghostbusters 2](~/tutorials/first-mvc-app/search/_static/g2.png)

Sie können jedoch von den Benutzern nicht erwarten, dass sie jedes Mal die URL ändern, wenn sie nach einem Film suchen möchten. Deshalb fügen Sie nun Benutzeroberflächenelemente zum besseren Filtern von Filmen hinzu. Wenn Sie die Signatur der `Index`-Methode geändert haben, um das Übergeben des routengebundenen Parameters `ID` zu testen, ändern Sie sie erneut so, dass sie einen Parameter namens `searchString` verwendet:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1,6,8&name=snippet_1stSearch)]

Öffnen Sie die Datei *Views/Movies/Index.cshtml* , und fügen Sie das hervorgehobene `<form>`-Markup hinzu:

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Movies/IndexForm1.cshtml?highlight=10-16&range=4-21)]

Das HTML-Tag `<form>` nutzt das [Hilfsprogramm für Formulartags](xref:mvc/views/working-with-forms). Wenn Sie nun das Formular senden, wird die Filterzeichenfolge an die Aktion `Index` des „movies“-Controllers übermittelt. Speichern Sie Ihre Änderungen, und testen Sie dann den Filter.

![Indexansicht mit dem in das Filtertextfeld eingegebenen Wort „ghost“](~/tutorials/first-mvc-app/search/_static/filter.png)

Entgegen Ihrer Erwartung gibt es keine `[HttpPost]`-Überladung der `Index`-Methode. Diese ist nicht erforderlich, da die Methode nicht den Status der App ändert, sondern bloß Daten filtert.

Sie können die folgende `[HttpPost] Index`-Methode hinzufügen.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1&name=snippet_SearchPost)]

Der Parameter `notUsed` dient zum Erstellen einer Überladung für die `Index`-Methode. Damit beschäftigen wir uns später im Tutorial.

Wenn Sie diese Methode hinzufügen, entspricht die aufrufende Aktionsinstanz der `[HttpPost] Index`-Methode, und die `[HttpPost] Index`-Methode wird wie in der folgenden Abbildung gezeigt ausgeführt.

![Browserfenster mit der Antwort der Anwendung von „Von HttpPost-Index“: Filter für „ghost“](~/tutorials/first-mvc-app/search/_static/fo.png)

Doch selbst wenn Sie diese `[HttpPost]`-Version der `Index`-Methode hinzufügen, gibt es eine Einschränkung für die gesamte Implementierung. Stellen Sie sich vor, Sie möchten eine bestimmte Suche als Favorit speichern oder einen Link an Freunde senden, auf den diese klicken können, um dieselbe gefilterte Liste von Filmen anzuzeigen. Beachten Sie, dass die URL der HTTP POST-Anforderung identisch mit der URL der GET-Anforderung (localhost:{PORT}/Movies/Index) ist. Es sind keine Suchinformationen in der URL vorhanden. Die Informationen in der Suchzeichenfolge werden an den Server als [Formularfeldwert](https://developer.mozilla.org/docs/Learn/HTML/Forms/Sending_and_retrieving_form_data) gesendet. Sie können dies mit den Entwicklertools für den Browser oder dem exzellenten Tool [Fiddler](https://www.telerik.com/fiddler) überprüfen. Die folgende Abbildung zeigt die Entwicklertools für den Browser Chrome:

![Registerkarte „Netzwerk“ der Entwicklertools in Microsoft Edge mit einem Anforderungstext mit dem „searchString“-Wert „ghost“](~/tutorials/first-mvc-app/search/_static/f12_rb.png)

Sie können den Suchparameter und das [XSRF](xref:security/anti-request-forgery)-Token im Anforderungstext erkennen. Wie im vorherigen Tutorial erwähnt, generiert das [Hilfsprogramm für Formulartags](xref:mvc/views/working-with-forms) ein [XSRF](xref:security/anti-request-forgery)-Fälschungssicherheitstoken. Da wir keine Daten ändern, müssen wir nicht das Token in der Controllermethode validieren.

Da sich der Suchparameter im Anforderungstext und nicht in der URL befindet, können Sie diese Suchinformationen nicht als Favorit speichern oder mit anderen teilen. Beheben Sie dieses Problem, indem Sie angeben, dass eine `HTTP GET`-Anforderung verwendet werden soll, die sich in der Datei *Views/Movies/Index.cshtml* befindet.

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexGet.cshtml?highlight=12&range=1-23)]

Wenn Sie nun eine Suche übermitteln, enthält die URL die Suchabfragezeichenfolge. Das Suchen wird auch an Aktionsmethode `HttpGet Index` übertragen, auch wenn Sie eine `HttpPost Index`-Methode haben.

![Browserfenster mit „searchString=ghost“ in der URL und den zurückgegebenen Filmen Ghostbusters und Ghostbusters 2, die das Wort „Ghost“ enthalten](~/tutorials/first-mvc-app/search/_static/search_get.png)

Das folgende Markup zeigt die Änderung am Tag `form`:

```cshtml
<form asp-controller="Movies" asp-action="Index" method="get">
```

## <a name="add-search-by-genre"></a>Hinzufügen der Suche nach Genre

Fügen Sie dem Ordner *Models* die folgende `MovieGenreViewModel`-Klasse hinzu:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Models/MovieGenreViewModel.cs)]

Das Ansichtsmodell „movie-genre“ enthält Folgendes:

* Eine Liste von Filmen.
* Ein `SelectList`-Element mit der Liste der Genres. Dies ermöglicht dem Benutzer, ein Genre in der Liste auszuwählen.
* Ein `MovieGenre`-Element, das das ausgewählte Genre enthält.
* `SearchString`, die den Text enthält, den Benutzer in das Suchtextfeld eingeben.

Ersetzen Sie die `Index`-Methode in `MoviesController.cs` durch folgenden Code:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MoviesController.cs?name=snippet_SearchGenre)]

Der folgende Code ist eine `LINQ`-Abfrage, die alle Genres aus der Datenbank abruft.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MoviesController.cs?name=snippet_LINQ)]

Das `SelectList`-Element von Genres wird durch Projizieren der unterschiedlichen Genres erstellt (wir möchten nicht, dass unsere Auswahlliste doppelte Genres enthält).

Wenn der Benutzer nach dem Element sucht, wird der Wert für die Suche im Suchfeld beibehalten.

## <a name="add-search-by-genre-to-the-index-view"></a>Hinzufügen der Suche nach Genre zur Indexansicht

Aktualisieren Sie `Index.cshtml` in *Views/Movies/* folgendermaßen:

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexFormGenreNoRating.cshtml?highlight=1,15,16,17,19,28,31,34,37,43)]

Überprüfen Sie den Lambdaausdruck, der im folgenden HTML-Hilfsprogramm verwendet wird:

`@Html.DisplayNameFor(model => model.Movies[0].Title)`

Das HTML-Hilfsprogramm `DisplayNameFor` im vorangehenden Code überprüft die Eigenschaft `Title`, auf die im Lambdaausdruck verwiesen wird, um den Anzeigenamen zu bestimmen. Da der Lambda-Ausdruck überprüft statt ausgewertet wird, erhalten Sie keine Zugriffsverletzung, wenn `model`, `model.Movies`, `model.Movies[0]` oder `null` leer sind. Wenn der Lambdaausdruck ausgewertet wird, (z.B. mit `@Html.DisplayFor(modelItem => item.Title)`), werden die Eigenschaftswerte ausgewertet.

Testen Sie die App mit einer Suche nach Genre, Filmtitel und beidem:

![Browser-Fenster mit den Ergebnissen von https://localhost:5001/Movies?MovieGenre=Comedy&SearchString=2](~/tutorials/first-mvc-app/search/_static/s2.png)

> [!div class="step-by-step"]
> [Zurück](controller-methods-views.md)
> [Weiter](new-field.md)
