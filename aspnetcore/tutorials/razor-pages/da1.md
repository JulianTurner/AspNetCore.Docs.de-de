---
title: 'Teil 5: Aktualisieren der generierten Seiten'
author: rick-anderson
description: Dies ist Teil 5 der Tutorialreihe zu Razor Pages.
ms.author: riande
ms.date: 09/20/2020
ms.custom: contperf-fy21q2
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
uid: tutorials/razor-pages/da1
ms.openlocfilehash: 46fbfb50afd03f918f9e02bcc8c1dbde9a080ca4
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/04/2021
ms.locfileid: "97485939"
---
# <a name="part-5-update-the-generated-pages-in-an-aspnet-core-app"></a>Teil 5: Aktualisieren der generierten Seiten in einer ASP.NET Core-App

Von [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

Für den Anfang ist die mit einem Gerüst erstellte Movie-App schon recht ansprechend, doch es gibt Raum für Verbesserungen. **ReleaseDate** sollte **Release Date** lauten (zwei Wörter).

![In Chrome geöffnete Movie-Anwendung](sql/_static/5/m55.png)

## <a name="update-the-generated-code"></a>Aktualisieren des generierten Codes

Öffnen Sie die Datei *Models/Movie.cs*, und fügen Sie die im folgenden Code gezeigten markierten Zeilen hinzu:

[!code-csharp[Main](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateFixed.cs?name=snippet_1&highlight=3,12,17)]

Im vorherigen Code:

* Mit der Datenanmerkung `[Column(TypeName = "decimal(18, 2)")]` kann Entity Framework Core `Price` ordnungsgemäß einer Währung in der Datenbank zuordnen. Weitere Informationen finden Sie unter [Datentypen](/ef/core/modeling/relational/data-types).
* Das Attribut [[Display]](xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DisplayMetadata) gibt den Anzeigenamen eines Felds an. Im vorherigen Code „Release Date“ anstelle von ReleaseDate.
* Das Attribut [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) gibt den Typ der Daten (`Date`) an. Die im Feld gespeicherten Zeitinformationen werden nicht angezeigt.

[Datenanmerkungen](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) werden im nächsten Tutorial behandelt.

Navigieren Sie zu *Pages/Movies*, und bewegen Sie den Mauszeiger über dem Link **Bearbeiten**, um die Ziel-URL anzuzeigen.

![Browserfenster mit Maus über dem Link „Bearbeiten“ und der angezeigten Link-URL von https://localhost:1234/Movies/Edit/5](~/tutorials/razor-pages/da1/edit7.png)

Die Links **Edit**, **Details** und **Delete** werden mithilfe des [Hilfsprogramms für Ankertags](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) in der Datei *Pages/Movies/Index.cshtml* generiert.

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?highlight=16-18&range=32-)]

[Taghilfsprogramme](xref:mvc/views/tag-helpers/intro) ermöglichen serverseitigem Code das Mitwirken am Erstellen und Rendern von HTML-Elementen in Razor-Dateien.

Im vorherige Code generiert das [Hilfsprogramm für Ankertags](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) dynamisch den Wert des HTML-Attributs `href` auf der Razor Page (die Route ist relativ), das Element `asp-page` und die Routen-ID (`asp-route-id`). Weitere Informationen finden Sie unter [URL-Generierung für Seiten](xref:razor-pages/index#url-generation-for-pages).

Rufen Sie in einem Browser **Quelltext anzeigen** auf, um das generierte Markup zu untersuchen. Ein Teil des generierten HTML-Codes wird unten gezeigt:

```html
<td>
  <a href="/Movies/Edit?id=1">Edit</a> |
  <a href="/Movies/Details?id=1">Details</a> |
  <a href="/Movies/Delete?id=1">Delete</a>
</td>
```

   Die dynamisch generierten Links übergeben die Film-ID mit einer Abfragezeichenfolge. Beispiel: `?id=1` in `https://localhost:5001/Movies/Details?id=1`.

### <a name="add-route-template"></a>Hinzufügen der Routenvorlage

Aktualisieren Sie die Razor Pages „Edit“ (Bearbeiten), „Details“ und „Delete“ (Löschen) so, dass die Routenvorlage `{id:int}` verwendet wird. Ändern Sie die „page“-Direktive für jede dieser Seiten aus `@page` in `@page "{id:int}"`. Führen Sie die App aus, und zeigen Sie dann den Quelltext an.

Der generierte HTML-Code fügt die ID dem Pfadteil der URL hinzu:

```html
<td>
  <a href="/Movies/Edit/1">Edit</a> |
  <a href="/Movies/Details/1">Details</a> |
  <a href="/Movies/Delete/1">Delete</a>
</td>
```

Eine Anforderung an die Seite mit der Routenvorlage `{id:int}`, die **nicht** die ganze Zahl enthält, gibt den HTTP-Fehler 404 (Nicht gefunden) zurück. `https://localhost:5001/Movies/Details` gibt beispielsweise den Fehler 404 zurück. Um die ID optional zu machen, fügen Sie `?` an die Routeneinschränkung an:

```cshtml
@page "{id:int?}"
```

Testen Sie das Verhalten von `@page "{id:int?}"`:

1. Legen Sie die page-Anweisung in *Pages/Movies/Details.cshtml* auf `@page "{id:int?}"` fest.
1. Setzen Sie einen Haltepunkt in `public async Task<IActionResult> OnGetAsync(int? id)` (in *Pages/Movies/Details.cshtml.cs*).
1. Navigieren Sie zu `https://localhost:5001/Movies/Details/`.

Mit der `@page "{id:int}"`-Direktive wird der Haltepunkt nie erreicht. Die Routing-Engine gibt den HTTP-Fehler 404 zurück. Bei Verwendung von `@page "{id:int?}"` gibt die `OnGetAsync`-Methode `NotFound` zurück (HTTP 404):

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Pages/Movies/Details.cshtml.cs?name=snippet1&highlight=10-13)]

### <a name="review-concurrency-exception-handling"></a>Überprüfen der Behandlung von Ausnahmen bei Parallelität

Überprüfen Sie die `OnPostAsync`-Methode in der Datei *Pages/Movies/Edit.cshtml.cs*:

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Edit.cshtml.cs?name=snippet)]

Der obige Code erkennt Parallelitätsausnahmen, wenn ein Client den Film löscht und der andere Client Änderungen am Film übermittelt.

So testen Sie den Block `catch`

1. Legen Sie einen Haltepunkt bei `catch (DbUpdateConcurrencyException)` fest.
1. Wählen Sie **Edit** für einen Film aus, nehmen Sie Änderungen vor, aber geben Sie nicht **Save** ein.
1. Klicken Sie in einem anderen Browserfenster auf den Link **Delete** für denselben Film, und löschen Sie dann den Film.
1. Übermitteln Sie im vorherigen Browserfenster Änderungen am Film.

Der Produktionscode sollte Nebenläufigkeitskonflikte erkennen. Weitere Informationen finden Sie unter [Verarbeiten von Nebenläufigkeitskonflikten](xref:data/ef-rp/concurrency).

### <a name="posting-and-binding-review"></a>Überprüfen der Bereitstellung und Bindung

Sehen Sie sich die Datei *Pages/Movies/Edit.cshtml.cs* an:

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/SnapShots/Edit.cshtml.cs?name=snippet2)]

Wenn eine HTTP GET-Anforderung an die Seite „Movies/Edit“ gerichtet wird (z. B. `https://localhost:5001/Movies/Edit/3`):

* Die `OnGetAsync`-Methode ruft den Film aus der Datenbank ab und gibt die `Page`-Methode zurück.
* Die `Page`-Methode rendert die Seite  *Pages/Movies/Edit.cshtml* Razor. Die Datei *Pages/Movies/Edit.cshtml* enthält die Modellanweisung `@model RazorPagesMovie.Pages.Movies.EditModel`, die das Filmmodell auf der Seite verfügbar macht.
* Das Bearbeitungsformular wird mit den Werten aus dem Film angezeigt.

Wenn die Seite „Filme/Bearbeiten“ bereitgestellt wird:

* Die Formularwerte auf der Seite sind an die `Movie`-Eigenschaft gebunden. Das `[BindProperty]`-Attribut ermöglicht die [Modellbindung](xref:mvc/models/model-binding).

  ```csharp
  [BindProperty]
  public Movie Movie { get; set; }
  ```

* Bei Fehlern beim Modellstatus (Beispiel: `ReleaseDate` kann nicht in ein Datum konvertiert werden) wird das Formular mit den übermittelten Werten erneut angezeigt.
* Wenn keine Modellfehler vorhanden sind, wird der Film gespeichert.

Die HTTP GET-Methoden auf den Razor Pages Index „Create“ und „Delete“ folgen einem ähnlichen Muster. Die HTTP-POST-Methode `OnPostAsync` auf der Razor Page „Create“ folgt einem ähnlichen Muster wie die `OnPostAsync`-Methode auf der Razor Page „Edit“.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

> [!div class="step-by-step"]
> [Zurück: Arbeiten mit einer Datenbank](xref:tutorials/razor-pages/sql)
> [Weiter: Hinzufügen der Suche](xref:tutorials/razor-pages/search)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Für den Anfang ist die mit einem Gerüst erstellte Movie-App schon recht ansprechend, doch es gibt Raum für Verbesserungen. **ReleaseDate** sollte **Release Date** lauten (zwei Wörter).

![In Chrome geöffnete Movie-Anwendung](sql/_static/m55https.png)

## <a name="update-the-generated-code"></a>Aktualisieren des generierten Codes

Öffnen Sie die Datei *Models/Movie.cs*, und fügen Sie die im folgenden Code gezeigten markierten Zeilen hinzu:

[!code-csharp[Main](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/MovieDateFixed.cs?name=snippet_1&highlight=3,12,17)]

Mit der Datenanmerkung `[Column(TypeName = "decimal(18, 2)")]` kann Entity Framework Core `Price` ordnungsgemäß einer Währung in der Datenbank zuordnen. Weitere Informationen finden Sie unter [Datentypen](/ef/core/modeling/relational/data-types).

[Datenanmerkungen](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) werden im nächsten Tutorial behandelt. Das Attribut [[Display]](xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DisplayMetadata) gibt an, was für den Namen eines Felds angezeigt werden soll (in diesem Fall „Release Date“ statt „ReleaseDate“). Das Attribut [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) gibt den Typ der Daten (`Date`) an, damit die im Feld gespeicherten Zeitinformationen nicht angezeigt werden.

Navigieren Sie zu „Pages/Movies“, und bewegen Sie den Mauszeiger über dem Link **Bearbeiten**, um die Ziel-URL anzuzeigen.

![Browserfenster mit Maus über dem Link „Bearbeiten“ und der angezeigten Link-URL von http://localhost:1234/Movies/Edit/5](~/tutorials/razor-pages/da1/edit7.png)

Die Links **Edit**, **Details** und **Delete** werden mithilfe des [Hilfsprogramms für Ankertags](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) in der Datei *Pages/Movies/Index.cshtml* generiert.

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?highlight=16-18&range=32-)]

[Taghilfsprogramme](xref:mvc/views/tag-helpers/intro) ermöglichen serverseitigem Code das Mitwirken am Erstellen und Rendern von HTML-Elementen in Razor-Dateien. Im vorangehenden Code generiert das `AnchorTagHelper` dynamisch den Wert des HTML-Attributs `href` auf der Razor Page (die Route ist relativ), das `asp-page`-Element und die Routen-ID (`asp-route-id`). Weitere Informationen finden Sie unter [URL-Generierung für Seiten](xref:razor-pages/index#url-generation-for-pages).

Rufen Sie in einem Browser **Quelltext anzeigen** auf, um das generierte Markup zu untersuchen. Ein Teil des generierten HTML-Codes wird unten gezeigt:

```html
<td>
  <a href="/Movies/Edit?id=1">Edit</a> |
  <a href="/Movies/Details?id=1">Details</a> |
  <a href="/Movies/Delete?id=1">Delete</a>
</td>
```

Die dynamisch generierten Links übergeben die Film-ID mit einer Abfragezeichenfolge. Beispiel: `?id=1` in `https://localhost:5001/Movies/Details?id=1`.

Aktualisieren Sie die Razor Pages „Edit“, „Details“ und „Delete“ so, dass die Routenvorlage „{id:int}“ verwendet wird. Ändern Sie die „page“-Direktive für jede dieser Seiten aus `@page` in `@page "{id:int}"`. Führen Sie die App aus, und zeigen Sie dann den Quelltext an. Der generierte HTML-Code fügt die ID dem Pfadteil der URL hinzu:

```html
<td>
  <a href="/Movies/Edit/1">Edit</a> |
  <a href="/Movies/Details/1">Details</a> |
  <a href="/Movies/Delete/1">Delete</a>
</td>
```

Eine Anforderung an die Seite mit der Routenvorlage „{id:int}“, die **nicht** die ganze Zahl enthält, gibt den HTTP-Fehler 404 (Nicht gefunden) zurück. `https://localhost:5001/Movies/Details` gibt beispielsweise den Fehler 404 zurück. Um die ID optional zu machen, fügen Sie `?` an die Routeneinschränkung an:

 ```cshtml
@page "{id:int?}"
```

So testen Sie das Verhalten von `@page "{id:int?}"`:

* Legen Sie die page-Anweisung in *Pages/Movies/Details.cshtml* auf `@page "{id:int?}"` fest.
* Setzen Sie einen Haltepunkt in `public async Task<IActionResult> OnGetAsync(int? id)` (in *Pages/Movies/Details.cshtml.cs*).
* Navigieren Sie zu `https://localhost:5001/Movies/Details/`.

Mit der `@page "{id:int}"`-Direktive wird der Haltepunkt nie erreicht. Die Routing-Engine gibt den HTTP-Fehler 404 zurück. Bei Verwendung von `@page "{id:int?}"` gibt die `OnGetAsync`-Methode `NotFound` zurück (HTTP 404):

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Details.cshtml.cs?name=snippet1&highlight=10-13)]

### <a name="review-concurrency-exception-handling"></a>Überprüfen der Behandlung von Ausnahmen bei Parallelität

Überprüfen Sie die `OnPostAsync`-Methode in der Datei *Pages/Movies/Edit.cshtml.cs*:

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Edit.cshtml.cs?name=snippet)]

Der obige Code erkennt Parallelitätsausnahmen, wenn ein Client den Film löscht und der andere Client Änderungen am Film übermittelt.

So testen Sie den Block `catch`

* Legen Sie einen Haltepunkt auf `catch (DbUpdateConcurrencyException)`fest.
* Wählen Sie **Edit** für einen Film aus, nehmen Sie Änderungen vor, aber geben Sie nicht **Save** ein.
* Klicken Sie in einem anderen Browserfenster auf den Link **Delete** für denselben Film, und löschen Sie dann den Film.
* Übermitteln Sie im vorherigen Browserfenster Änderungen am Film.

Der Produktionscode sollte Nebenläufigkeitskonflikte erkennen. Weitere Informationen finden Sie unter [Verarbeiten von Nebenläufigkeitskonflikten](xref:data/ef-rp/concurrency).

### <a name="posting-and-binding-review"></a>Überprüfen der Bereitstellung und Bindung

Sehen Sie sich die Datei *Pages/Movies/Edit.cshtml.cs* an:

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Edit21.cshtml.cs?name=snippet2)]

Wenn eine HTTP GET-Anforderung an die Seite „Movies/Edit“ gerichtet wird (z. B. `https://localhost:5001/Movies/Edit/3`):

* Die `OnGetAsync`-Methode ruft den Film aus der Datenbank ab und gibt die `Page`-Methode zurück. 
* Die `Page`-Methode rendert die Seite  *Pages/Movies/Edit.cshtml* Razor. Die Datei *Pages/Movies/Edit.cshtml* enthält die Modellanweisung `@model RazorPagesMovie.Pages.Movies.EditModel`, die das Filmmodell auf der Seite verfügbar macht.
* Das Bearbeitungsformular wird mit den Werten aus dem Film angezeigt.

Wenn die Seite „Filme/Bearbeiten“ bereitgestellt wird:

* Die Formularwerte auf der Seite sind an die `Movie`-Eigenschaft gebunden. Das `[BindProperty]`-Attribut ermöglicht die [Modellbindung](xref:mvc/models/model-binding).

  ```csharp
  [BindProperty]
  public Movie Movie { get; set; }
  ```

* Bei Fehlern beim Modellstatus (Beispiel: `ReleaseDate` kann nicht in ein Datum konvertiert werden) wird das Formular mit den übermittelten Werten angezeigt.
* Wenn keine Modellfehler vorhanden sind, wird der Film gespeichert.

Die HTTP GET-Methoden auf den Razor Pages Index „Create“ und „Delete“ folgen einem ähnlichen Muster. Die HTTP-POST-Methode `OnPostAsync` auf der Razor Page „Create“ folgt einem ähnlichen Muster wie die `OnPostAsync`-Methode auf der Razor Page „Edit“.

Die Suche wird im nächsten Tutorial hinzugefügt.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Dieses Tutorial auf YouTube](https://youtu.be/yLnnleREMtQ)

> [!div class="step-by-step"]
> [Zurück: Arbeiten mit einer Datenbank](xref:tutorials/razor-pages/sql)
> [Weiter: Hinzufügen der Suche](xref:tutorials/razor-pages/search)

::: moniker-end
