---
title: 'Teil 6: Hinzufügen der Suche'
author: rick-anderson
description: Dies ist Teil 6 der Tutorialreihe zu Razor Pages.
ms.author: riande
ms.date: 12/05/2019
no-loc:
- Index
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
uid: tutorials/razor-pages/search
ms.openlocfilehash: 3b95fe117895555ebcd44f971e7bb9d1173e1697
ms.sourcegitcommit: db0a6eb0be7bd7f22810a71fe9bf30e957fd116a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96419979"
---
# <a name="part-6-add-search-to-aspnet-core-no-locrazor-pages"></a>Teil 6: Hinzufügen der Suche zu Razor Pages in ASP.NET Core

Von [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-5.0"

[Zeigen Sie Beispielcode an, oder laden Sie diesen herunter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample)).

::: moniker-end

::: moniker range="< aspnetcore-5.0 >= aspnetcore-3.0"

[Zeigen Sie Beispielcode an, oder laden Sie diesen herunter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample)).

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

In den folgenden Abschnitten wird eine Suche nach Filmen anhand von *Genre* oder *Name* hinzugefügt.

Fügen Sie *Pages/Movies/Index.cshtml.cs* folgende hervorgehobene Anweisung und Eigenschaften hinzu:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_newProps&highlight=3,23,24,25,26,27)]

Im vorherigen Code:

* `SearchString`: Enthält den Text, den Benutzer in das Suchtextfeld eingeben. `SearchString` weist das Attribut [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) auf. `[BindProperty]` bindet Formularwerte und Abfragezeichenfolgen mit dem gleichen Namen wie die Eigenschaft. `[BindProperty(SupportsGet = true)]` ist für die Bindung in HTTP GET-Anforderungen erforderlich.
* `Genres`: Enthält die Liste der Genres. `Genres` ermöglicht es dem Benutzer, ein Genre in der Liste auszuwählen. `SelectList` erfordert `using Microsoft.AspNetCore.Mvc.Rendering;`.
* `MovieGenre`: Enthält das spezifische vom Benutzer auswählte Genre. Beispiel: Western.
* `Genres` und `MovieGenre` werden später in diesem Tutorial verwendet.

[!INCLUDE[](~/includes/bind-get.md)]

Aktualisieren Sie die `OnGetAsync`-Methode der Seite Index mit folgendem Code:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_1stSearch)]

Die erste Zeile der `OnGetAsync`-Methode erstellt eine [LINQ](/dotnet/csharp/programming-guide/concepts/linq/)-Abfrage zum Auswählen der Filme:

```csharp
// using System.Linq;
var movies = from m in _context.Movie
             select m;
```

Die Abfrage wird an dieser Stelle *nur* definiert und **nicht** auf die Datenbank angewendet.

Wenn die `SearchString`-Eigenschaft nicht NULL oder leer ist, wird die Abfrage nach Filmen so geändert, dass die Suchzeichenfolge gefiltert wird:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_SearchNull)]

Der Code `s => s.Title.Contains()` ist ein [Lambdaausdruck](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions). Lambdaausdrücke werden in methodenbasierten [LINQ](/dotnet/csharp/programming-guide/concepts/linq/)-Abfragen als Argumente für standardmäßige Abfrageoperatormethoden wie die [Where](/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq)-Methode oder `Contains` verwendet. LINQ-Abfragen werden nicht ausgeführt, wenn sie definiert oder durch Aufrufen einer Methode wie z. B. `Where`, `Contains` oder `OrderBy` geändert werden. Stattdessen wird die Ausführung der Abfrage verzögert. Die Auswertung eines Ausdrucks wird so lange hinausgezögert, bis dessen realisierter Wert durchlaufen oder die `ToListAsync`-Methode aufgerufen wird. Weitere Informationen finden Sie unter [Abfrageausführung](/dotnet/framework/data/adonet/ef/language-reference/query-execution).

> [!NOTE]
> Die [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains)-Methode wird in der Datenbank und nicht im C#-Code ausgeführt. Die Groß-/Kleinschreibung in der Abfrage hängt von der Datenbank und Sortierung ab. In SQL Server wird `Contains` zu [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql) zugeordnet, das Groß-/Kleinschreibung nicht beachtet. In SQLite wird bei der Standardsortierung Groß-/Kleinschreibung beachtet.

Navigieren Sie zur Seite „Movies“, und fügen Sie eine Abfragezeichenfolge wie z. B. `?searchString=Ghost` an die URL an. Beispiel: `https://localhost:5001/Movies?searchString=Ghost`. Die gefilterten Filme werden angezeigt.

![Ansicht Index](search/_static/ghost.png)

Wenn die Routenvorlage der Seite Index hinzugefügt wird, kann die Suchzeichenfolge als URL-Segment übergeben werden. Beispiel: `https://localhost:5001/Movies/Ghost`.

```cshtml
@page "{searchString?}"
```

Die vorangehende Routeneinschränkung ermöglicht das Suchen des Titels als Routendaten (URL-Segment) anstatt als Wert einer Abfragezeichenfolge.  Das `?` in `"{searchString?}"` bedeutet, dass dies ein optionaler Routenparameter ist.

![Ansicht Index mit dem der URL hinzugefügten Wort „ghost“ und einer zurückgegebenen Filmliste mit zwei Filmen: Ghostbusters und Ghostbusters 2](search/_static/g2.png)

Die ASP.NET Core-Runtime verwendet die [Modellbindung](xref:mvc/models/model-binding), um den Wert der `SearchString`-Eigenschaft aus der Abfragezeichenfolge (`?searchString=Ghost`) oder aus Datenrouten (`https://localhost:5001/Movies/Ghost`) festzulegen. Bei der Modellbindung wird Groß-/ Kleinschreibung **_nicht_* _ berücksichtigt.

Sie können jedoch von Benutzern nicht erwarten, dass sie die URL ändern, um nach einem Film zu suchen. In diesem Schritt wird eine Benutzeroberflächenoption hinzugefügt, um Filme zu filtern. Wenn Sie die Routeneinschränkung `"{searchString?}"` hinzugefügt haben, entfernen Sie sie.

Öffnen Sie die Datei _Pages/Movies/Index.cshtml*, und fügen Sie im folgenden Code das hervorgehobene Markup hinzu:

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/SnapShots/Index2.cshtml?highlight=14-19&range=1-22)]

Das HTML-Tag `<form>` verwendet die folgenden [Taghilfsprogramme](xref:mvc/views/tag-helpers/intro):

* [Hilfsprogramm für Formulartags](xref:mvc/views/working-with-forms#the-form-tag-helper). Bei der Übermittlung des Formulars wird die Filterzeichenfolge über die Abfragezeichenfolge an die Seite *Pages/Movies/Index* gesendet.
* [Hilfsprogramm für Eingabetags](xref:mvc/views/working-with-forms#the-input-tag-helper)

Speichern Sie die Änderungen, und testen Sie den Filter.

![Ansicht Index mit dem in das Filtertextfeld eingegebenen Wort „ghost“](search/_static/filter.png)

## <a name="search-by-genre"></a>Suche nach Genre

Aktualisieren Sie die `OnGetAsync`-Methode der Seite Index mit folgendem Code:

   [!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_SearchGenre)]

Der folgende Code ist eine LINQ-Abfrage, die alle Genres aus der Datenbank abruft.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_LINQ)]

Die `SelectList` von Genres wird durch Projizieren der unterschiedlichen Genres erstellt.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_SelectList)]

### <a name="add-search-by-genre-to-the-no-locrazor-page"></a>Hinzufügen einer Suche anhand von Genre zur Razor Page

1. Aktualisieren Sie die Datei *Index.cshtml* [Element `<form>`] (https://developer.mozilla.org/docs/Web/HTML/Element/form) wie im folgenden Markup hervorgehoben):

   [!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/SnapShots/IndexFormGenreNoRating.cshtml?highlight=16-18&range=1-26)]

1. Testen Sie die App mit einer Suche nach Genre, Filmtitel und beidem.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

> [!div class="step-by-step"]
> [Zurück: Aktualisieren der Seiten](xref:tutorials/razor-pages/da1)
> [Weiter: Hinzufügen eines neuen Felds](xref:tutorials/razor-pages/new-field)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[Zeigen Sie Beispielcode an, oder laden Sie diesen herunter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample)).

In den folgenden Abschnitten wird eine Suche nach Filmen anhand von *Genre* oder *Name* hinzugefügt.

Fügen Sie *Pages/Movies/Index.cshtml.cs* folgende hervorgehobene Eigenschaften hinzu:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_newProps&highlight=11-999)]

* `SearchString`: Enthält den Text, den Benutzer in das Suchtextfeld eingeben. `SearchString` weist das Attribut [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) auf. `[BindProperty]` bindet Formularwerte und Abfragezeichenfolgen mit dem gleichen Namen wie die Eigenschaft. `[BindProperty(SupportsGet = true)]` ist für die Bindung in HTTP GET-Anforderungen erforderlich.
* `Genres`: Enthält die Liste der Genres. `Genres` ermöglicht es dem Benutzer, ein Genre in der Liste auszuwählen. `SelectList` erfordert `using Microsoft.AspNetCore.Mvc.Rendering;`.
* `MovieGenre`: Enthält das spezifische vom Benutzer auswählte Genre. Beispiel: Western.
* `Genres` und `MovieGenre` werden später in diesem Tutorial verwendet.

[!INCLUDE[](~/includes/bind-get.md)]

Aktualisieren Sie die `OnGetAsync`-Methode der Seite Index mit folgendem Code:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_1stSearch)]

Die erste Zeile der `OnGetAsync`-Methode erstellt eine [LINQ](/dotnet/csharp/programming-guide/concepts/linq/)-Abfrage zum Auswählen der Filme:

```csharp
// using System.Linq;
var movies = from m in _context.Movie
             select m;
```

Die Abfrage wird an dieser Stelle *nur* definiert und **nicht** auf die Datenbank angewendet.

Wenn die `SearchString`-Eigenschaft nicht NULL oder leer ist, wird die Abfrage nach Filmen so geändert, dass die Suchzeichenfolge gefiltert wird:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_SearchNull)]

Der Code `s => s.Title.Contains()` ist ein [Lambdaausdruck](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions). Lambdaausdrücke werden in methodenbasierten [LINQ](/dotnet/csharp/programming-guide/concepts/linq/)-Abfragen als Argumente für standardmäßige Abfrageoperatormethoden wie die [Where](/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq)-Methode oder `Contains` verwendet. LINQ-Abfragen werden nicht ausgeführt, wenn sie definiert oder durch Aufrufen einer Methode wie `Where`, `Contains` oder `OrderBy` geändert werden. Stattdessen wird die Ausführung der Abfrage verzögert. Die Auswertung eines Ausdrucks wird so lange hinausgezögert, bis dessen realisierter Wert durchlaufen oder die `ToListAsync`-Methode aufgerufen wird. Weitere Informationen finden Sie unter [Abfrageausführung](/dotnet/framework/data/adonet/ef/language-reference/query-execution).

**Hinweis**: Die [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains)-Methode wird in der Datenbank und nicht im C#-Code ausgeführt. Die Groß-/Kleinschreibung in der Abfrage hängt von der Datenbank und Sortierung ab. In SQL Server wird `Contains` zu [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql) zugeordnet, das Groß-/Kleinschreibung nicht beachtet. In SQLite wird bei der Standardsortierung Groß-/Kleinschreibung beachtet.

Navigieren Sie zur Seite „Movies“, und fügen Sie eine Abfragezeichenfolge wie z. B. `?searchString=Ghost` an die URL an. Beispiel: `https://localhost:5001/Movies?searchString=Ghost`. Die gefilterten Filme werden angezeigt.

![Ansicht Index](search/_static/ghost.png)

Wenn die Routenvorlage der Seite Index hinzugefügt wird, kann die Suchzeichenfolge als URL-Segment übergeben werden. Beispiel: `https://localhost:5001/Movies/Ghost`.

```cshtml
@page "{searchString?}"
```

Die vorangehende Routeneinschränkung ermöglicht das Suchen des Titels als Routendaten (URL-Segment) anstatt als Wert einer Abfragezeichenfolge.  Das `?` in `"{searchString?}"` bedeutet, dass dies ein optionaler Routenparameter ist.

![Ansicht Index mit dem der URL hinzugefügten Wort „ghost“ und einer zurückgegebenen Filmliste mit zwei Filmen: Ghostbusters und Ghostbusters 2](search/_static/g2.png)

Die ASP.NET Core-Runtime verwendet die [Modellbindung](xref:mvc/models/model-binding), um den Wert der `SearchString`-Eigenschaft aus der Abfragezeichenfolge (`?searchString=Ghost`) oder aus Datenrouten (`https://localhost:5001/Movies/Ghost`) festzulegen. Bei der Modellbindung wird Groß-/ Kleinschreibung **_nicht_* _ berücksichtigt.

Sie können jedoch von Benutzern nicht erwarten, dass sie die URL ändern, um nach einem Film zu suchen. In diesem Schritt wird eine Benutzeroberflächenoption hinzugefügt, um Filme zu filtern. Wenn Sie die Routeneinschränkung `"{searchString?}"` hinzugefügt haben, entfernen Sie sie.

Öffnen Sie die Datei _Pages/Movies/Index.cshtml*, und fügen Sie im folgenden Code das hervorgehobene `<form>`-Markup hinzu:

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index2.cshtml?highlight=14-19&range=1-22)]

Das HTML-Tag `<form>` verwendet die folgenden [Taghilfsprogramme](xref:mvc/views/tag-helpers/intro):

* [Hilfsprogramm für Formulartags](xref:mvc/views/working-with-forms#the-form-tag-helper). Bei der Übermittlung des Formulars wird die Filterzeichenfolge über die Abfragezeichenfolge an die Seite *Pages/Movies/Index* gesendet.
* [Hilfsprogramm für Eingabetags](xref:mvc/views/working-with-forms#the-input-tag-helper)

Speichern Sie die Änderungen, und testen Sie den Filter.

![Ansicht Index mit dem in das Filtertextfeld eingegebenen Wort „ghost“](search/_static/filter.png)

## <a name="search-by-genre"></a>Suche nach Genre

Aktualisieren Sie die `OnGetAsync`-Methode mit folgendem Code:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_SearchGenre)]

Der folgende Code ist eine LINQ-Abfrage, die alle Genres aus der Datenbank abruft.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_LINQ)]

Die `SelectList` von Genres wird durch Projizieren der unterschiedlichen Genres erstellt.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_SelectList)]

### <a name="add-search-by-genre-to-the-no-locrazor-page"></a>Hinzufügen einer Suche anhand von Genre zur Razor Page

Aktualisieren Sie die Datei *Index.cshtml* [Element `<form>`] (https://developer.mozilla.org/docs/Web/HTML/Element/form) wie im folgenden Markup hervorgehoben):

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/IndexFormGenreNoRating.cshtml?highlight=16-18&range=1-26)]

Testen Sie die App mit einer Suche nach Genre, Filmtitel und beidem.
Der oben stehende Code verwendet das [Auswahl-Taghilfsprogramm](xref:mvc/views/working-with-forms#the-select-tag-helper) und das Options-Taghilfsprogramm.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Dieses Tutorial auf YouTube](https://youtu.be/4B6pHtdyo08)

> [!div class="step-by-step"]
> [Zurück: Aktualisieren der Seiten](xref:tutorials/razor-pages/da1)
> [Weiter: Hinzufügen eines neuen Felds](xref:tutorials/razor-pages/new-field)

::: moniker-end
