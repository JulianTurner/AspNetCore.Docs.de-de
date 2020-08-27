---
title: 'Part 3: Gerüstbau mit Razor Pages in ASP.NET Core'
author: rick-anderson
description: Dies ist Teil 3 der Tutorialreihe zu Razor Pages.
ms.author: riande
ms.date: 08/17/2019
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
uid: tutorials/razor-pages/page
ms.openlocfilehash: 03febbd71df19cd3524d26e229a8bd8798a874b5
ms.sourcegitcommit: f09407d128634d200c893bfb1c163e87fa47a161
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/26/2020
ms.locfileid: "88865116"
---
# <a name="part-3-scaffolded-no-locrazor-pages-in-aspnet-core"></a>Part 3: Gerüstbau mit Razor Pages in ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Von [Rick Anderson](https://twitter.com/RickAndMSFT)

In diesem Tutorial werden die Razor Pages näher untersucht, die durch den Gerüstbau im [vorherigen Tutorial](xref:tutorials/razor-pages/model) erstellt wurden.

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

## <a name="the-create-delete-details-and-edit-pages"></a>Die Seiten „Create“, „Delete“, „Details“ und „Edit“

Sehen Sie sich das Seitenmodell *Pages/Movies/Index.cshtml.cs* an:

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs)]

Razor-Seiten werden von `PageModel` abgeleitet. Gemäß der Konvention wird die von `PageModel` abgeleitete Klasse `<PageName>Model` genannt. Der Konstruktor verwendet [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection), um `RazorPagesMovieContext` zur Seite hinzuzufügen. Alle per Gerüstbau erstellten Seiten folgen diesem Muster. Weitere Informationen zum asynchronen Programmieren mit Entity Framework finden Sie unter [Asynchroner Code](xref:data/ef-rp/intro#asynchronous-code).

Wenn eine Anforderung an die Seite erfolgt, gibt die Methode `OnGetAsync` eine Filmliste an die Razor-Seite zurück. `OnGetAsync` oder `OnGet` wird aufgerufen, um den Status der Seite zu initialisieren. In diesem Fall ruft `OnGetAsync` eine Liste von Filmen ab und zeigt diese an.

Wenn `OnGet``void` oder `OnGetAsync``Task` zurückgibt, wird keine Rückgabeanweisung verwendet. Wenn der Rückgabetyp `IActionResult` oder `Task<IActionResult>` ist, muss eine Rückgabeanweisung bereitgestellt werden. Dies kann z. B. die `OnPostAsync`-Methode *Pages/Movies/Create.cshtml.cs* sein:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippet)]

<a name="index"></a> Sehen Sie sich die Razor-Seite *Pages/Movies/Index.cshtml* an:

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml)]

Razor kann aus HTML in C# oder ein Razor-spezifisches Markup übergehen. Wenn auf ein `@`-Symbol ein für [Razor reserviertes Schlüsselwort](xref:mvc/views/razor#razor-reserved-keywords) folgt, wechselt es in ein Razor-spezifisches Markup. Andernfalls geht es in C# über.

### <a name="the-page-directive"></a>Die @page -Direktive

Die Razor-Anweisung `@page` wandelt die Datei in eine MVC-Aktion um. Das bedeutet, dass sie Anforderungen verarbeiten kann. `@page` muss die erste Razor-Anweisung auf einer Seite sein. `@page` ist ein Beispiel für einen Übergang in ein Razor-spezifisches Markup. Unter [Razor-Syntax](xref:mvc/views/razor#razor-syntax) finden Sie weitere Informationen.

Überprüfen Sie den Lambdaausdruck, der im folgenden HTML-Hilfsprogramm verwendet wird:

```cshtml
@Html.DisplayNameFor(model => model.Movie[0].Title)
```

Das HTML-Hilfsprogramm `DisplayNameFor` überprüft die Eigenschaft `Title`, auf die im Lambdaausdruck verwiesen wird, um den Anzeigenamen zu bestimmen. Der Lambdaausdruck wird überprüft und nicht ausgewertet. Das bedeutet, dass keine Zugriffsverletzung auftritt, wenn `model`, `model.Movie` oder `model.Movie[0]``null` oder leer ist. Wenn der Lambdaausdruck ausgewertet wird, (z.B. mit `@Html.DisplayFor(modelItem => item.Title)`), werden die Eigenschaftswerte ausgewertet.

<a name="md"></a>

### <a name="the-model-directive"></a>Die @model -Direktive

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml?range=1-2&highlight=2)]

Die `@model`-Anweisung gibt den Typ des Modells an, das an die Razor-Seite weitergegeben wird. Im vorherigen Beispiel macht die `@model`-Linie die von `PageModel` abgeleitete Klasse der Razor-Seite verfügbar. Das Modell wird in den [HTML-Hilfsprogrammen](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) `@Html.DisplayNameFor` und `@Html.DisplayFor` auf der Seite verwendet.

### <a name="the-layout-page"></a>Die Seite „Layout“

Wählen Sie die Menülinks aus ( **RazorMovie**, **Home**, **Datenschutz**). Auf jeder Seite wird dasselbe Menülayout gezeigt. Das Menülayout wird mithilfe der Datei *Pages/Shared/_Layout.cshtml* implementiert. Öffnen Sie die Datei *Pages/Shared/_Layout.cshtml*.

Durch [Layout](xref:mvc/views/layout)-Vorlagen kann das HTML-Containerlayout:

* An einem Ort angegeben werden.
* Auf mehreren Seiten der Website angewendet werden.

Suchen Sie die Zeile `@RenderBody()`. `RenderBody` ist ein Platzhalter, bei dem alle seitenspezifischen Ansichten von der Layoutseite *umschlossen* angezeigt werden. Wenn Sie beispielsweise den Link **Privacy** auswählen, wird die Ansicht *Pages/Privacy.cshtml* in der `RenderBody`-Methode gerendert.

<a name="vd"></a>

### <a name="viewdata-and-layout"></a>ViewData und Layout

Sehen Sie sich das folgenden Markup aus der Datei *Pages/Movies/Index.cshtml* an:

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml?range=1-6&highlight=4-999)]

Das oben markierte Markup ist ein Beispiel vom Übergang von Razor in C#. Die Zeichen `{` und `}` schließen einen Block mit C#-Code ein.

Die Basisklasse `PageModel` verfügt über eine `ViewData`-Wörterbucheigenschaft, die zum Übergeben von Daten an eine Ansicht verwendet werden kann. Mithilfe eines Schlüssel-Wert-Musters können dem `ViewData`-Wörterbuch Objekte hinzugefügt werden. Im vorherigen Beispiel wurde dem `"Title"`-Wörterbuch die Eigenschaft `ViewData` hinzugefügt.

Die Eigenschaft `"Title"` wird in der Datei *Pages/Shared/_Layout.cshtml* verwendet. Das folgende Markup zeigt die ersten Zeilen der Datei *Pages/_Layout.cshtml* an.

<!-- We need a snapshot copy of layout because we are changing in the next step. -->

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/NU/_Layout.cshtml?highlight=6)]

Die Zeile `@*Markup removed for brevity.*@` ist ein Razor-Kommentar. Im Gegensatz zu HTML-Kommentaren (`<!-- -->`) werden Razor-Kommentare nicht an den Client gesendet.

### <a name="update-the-layout"></a>Aktualisieren des Layouts

Ändern Sie das `<title>`-Element in der Datei *Pages/Shared/_Layout.cshtml*, um **Movie** anstelle von **RazorPagesMovie** anzuzeigen.

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/Pages/Shared/_Layout.cshtml?range=1-6&highlight=6)]

Suchen Sie in der Datei *Pages/Shared/_Layout.cshtml* das folgende Ankerelement.

```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Index">RazorPagesMovie</a>
```

Ersetzen Sie das vorhergehende Element durch das folgende Markup.

```cshtml
<a class="navbar-brand" asp-page="/Movies/Index">RpMovie</a>
```

Das vorangehende Ankerelement ist ein [Tag Helper (Taghilfsprogramm)](xref:mvc/views/tag-helpers/intro). In diesem Fall handelt es sich um ein [Anchor Tag Helper (Anchor-Taghilfsprogramm)](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper). Das Taghilfsattribut und der -wert `asp-page="/Movies/Index"` erstellen einen Link zur Razor-Seite `/Movies/Index`. Das `asp-area`-Attribut ist leer, daher wird der Bereich im Link nicht verwendet. Weitere Informationen finden Sie unter [Bereiche](xref:mvc/controllers/areas).

Speichern Sie Ihre Änderungen, und testen Sie die App, indem Sie auf den Link **RpMovie** klicken. Wenn Probleme auftreten, sehen Sie sich die Datei [_Layout.cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Shared/_Layout.cshtml) in GitHub an.

Testen Sie die anderen Links – **Home** (Startseite), **RpMovie** (Razor Pages-Film), **Create** (Erstellen), **Edit** (Bearbeiten) und **Delete** (Löschen). Jede Seite legt den Titel fest, der in der Browserregisterkarte angezeigt wird. Wenn Sie eine Seite mit einem Lesezeichen versehen, wird dieser Titel für das Lesezeichen verwendet.

> [!NOTE]
> Sie können unter Umständen in das Feld `Price` keine Kommas als Dezimaltrennzeichen eingeben. Zur Unterstützung der [jQuery-Validierung](https://jqueryvalidation.org/) für nicht englische Gebietsschemas, in denen ein Komma („,“) als Dezimaltrennzeichen verwendet wird, und Nicht-US-englische Datums- und Uhrzeitformate müssen Sie Schritte zur Globalisierung Ihrer App ausführen. In diesem [GitHub-Problem 4076](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) finden Sie Anweisungen zum Hinzufügen von Kommas als Dezimaltrennzeichen.

Die Eigenschaft `Layout` wird in der Datei *Pages/_ViewStart.cshtml* festgelegt:

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/Pages/_ViewStart.cshtml)]

Das vorhergehende Markup legt die Layoutdatei für alle Razor-Dateien unter dem Ordner *Pages* auf *Pages/Shared/_Layout.cshtml* fest. Weitere Informationen finden Sie unter [Layout](xref:razor-pages/index#layout).

### <a name="the-create-page-model"></a>Das Seitenmodell „Create“

Überprüfen Sie das Seitenmodell *Pages/Movies/Create.cshtml.cs*:

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippetALL)]

Die Methode `OnGet` initialisiert einen beliebigen Zustand, der für die Seite erforderlich ist. Die Seite „Create“ (Erstellen) verfügt nicht über einen initialisierbaren Zustand. Deshalb wird `Page` zurückgegeben. Später in diesem Tutorial wird ein Beispiel für `OnGet` zur Initialisierung des Zustands gezeigt. Die Methode `Page` erstellt ein `PageResult`-Objekt, das die Seite *Create.cshtml* rendert.

Die Eigenschaft `Movie` verwendet das `[BindProperty]`-Attribut, um die [Modellbindung](xref:mvc/models/model-binding) zu aktivieren. Wenn das Formular „Create“ die Formularwerte bereitstellt, bindet die ASP.NET Core-Laufzeit die bereitgestellten Werte an das `Movie`-Modell.

Die Methode `OnPostAsync` wird ausgeführt, wenn die Seite Formulardaten bereitstellt:

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippetPost)]

Wenn es zu Modellfehlern kommt, wird das Formular mit allen bereitgestellten Formulardaten erneut angezeigt. Die meisten Modellfehler können clientseitig abgefangen werden, bevor das Formular bereitgestellt wird. Ein Beispiel für einen Modellfehler ist die Bereitstellung eines Werts für das Datumsfeld, der nicht in ein Datum konvertiert werden kann. Die clientseitige Validierung und die Modellvalidierung werden später in diesem Tutorial erläutert.

Wenn keine Modellfehler auftreten, werden die Daten gespeichert und der Browser zur Indexseite umgeleitet.

### <a name="the-create-no-locrazor-page"></a>Razor-Seite „Erstellen“

Betrachten Sie die Datei *Pages/Movies/Create.cshtml* der Razor-Seite:

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml)]

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Visual Studio zeigt die folgenden Tags in einer bestimmten Schriftart an, die für Taghilfsprogramme verwendet wird:

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

![VS17-Ansicht der Create.cshtml-Seite](page/_static/th3.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Die folgenden Taghilfsprogramme werden im vorangehenden Markup angezeigt:

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

# <a name="visual-studio-for-mac"></a>[Visual Studio für Mac](#tab/visual-studio-mac)

Visual Studio zeigt die folgenden Tags in einer bestimmten Schriftart an, die für Taghilfsprogramme verwendet wird:

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

---

Das Element `<form method="post">` ist ein [Form Tag Helper (Hilfsprogramm für Formulartags)](xref:mvc/views/working-with-forms#the-form-tag-helper). Das Hilfsprogramm für Formulartags enthält automatisch ein [antiforgery token (Antifälschungstoken)](xref:security/anti-request-forgery).

Die Gerüstbau-Engine erstellt Razor-Markup für jedes Feld im Modell (ausgenommen die ID) ähnlich wie folgt:

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml?range=15-20)]

Das [Hilfsprogramm für Validierungstags](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` und `<span asp-validation-for`) zeigt Validierungsfehler. Die Validierung wird an späterer Stelle in dieser Reihe detaillierter beschrieben.

Das [Label Tag Helper (Taghilfsprogramm für Label)](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) generiert den Titel des Labels und das `for`-Attribut für die Eigenschaft `Title`.

Das [Hilfsprogramm für Eingabetags](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control">`) verwendet die Attribute von [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) und generiert HTML-Attribute, die auf der Clientseite für die jQuery-Validierung erforderlich sind.

Weitere Informationen zu Taghilfsprogrammen wie `<form method="post">` finden Sie unter [Taghilfsprogramme in ASP.NET Core](xref:mvc/views/tag-helpers/intro).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

> [!div class="step-by-step"]
> [Zurück: Hinzufügen eines Modells](xref:tutorials/razor-pages/model)
> [Nächster Schritt: Datenbank](xref:tutorials/razor-pages/sql)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Von [Rick Anderson](https://twitter.com/RickAndMSFT)

In diesem Tutorial werden die Razor Pages näher untersucht, die durch den Gerüstbau im [vorherigen Tutorial](xref:tutorials/razor-pages/model) erstellt wurden.

Beispiel [Anzeigen oder Herunterladen](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22).

## <a name="the-create-delete-details-and-edit-pages"></a>Die Seiten „Create“, „Delete“, „Details“ und „Edit“

Sehen Sie sich das Seitenmodell *Pages/Movies/Index.cshtml.cs* an:

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml.cs)]

Razor-Seiten werden von `PageModel` abgeleitet. Gemäß der Konvention wird die von `PageModel` abgeleitete Klasse `<PageName>Model` genannt. Der Konstruktor verwendet [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection), um `RazorPagesMovieContext` zur Seite hinzuzufügen. Alle per Gerüstbau erstellten Seiten folgen diesem Muster. Weitere Informationen zum asynchronen Programmieren mit Entity Framework finden Sie unter [Asynchroner Code](xref:data/ef-rp/intro#asynchronous-code).

Wenn eine Anforderung an die Seite erfolgt, gibt die Methode `OnGetAsync` eine Filmliste an die Razor-Seite zurück. `OnGetAsync` oder `OnGet` werden für eine Razor-Seite aufgerufen, um den Zustand der Seite zu initialisieren. In diesem Fall ruft `OnGetAsync` eine Liste von Filmen ab und zeigt diese an.

Wenn `OnGet``void` und `OnGetAsync``Task` zurückgibt, wird keine Rückgabemethode verwendet. Wenn der Rückgabetyp `IActionResult` oder `Task<IActionResult>` ist, muss eine Rückgabeanweisung bereitgestellt werden. Dies kann z. B. die `OnPostAsync`-Methode *Pages/Movies/Create.cshtml.cs* sein:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml.cs?name=snippet)]

<a name="index"></a> Sehen Sie sich die Razor-Seite *Pages/Movies/Index.cshtml* an:

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml)]

Razor kann aus HTML in C# oder ein Razor-spezifisches Markup übergehen. Wenn auf ein `@`-Symbol ein für [Razor reserviertes Schlüsselwort](xref:mvc/views/razor#razor-reserved-keywords) folgt, wechselt es in ein Razor-spezifisches Markup. Andernfalls geht es in C# über.

Die Razor-Anweisung `@page` wandelt die Datei in eine MVC-Aktion um. Das bedeutet, dass sie Anforderungen verarbeiten kann. `@page` muss die erste Razor-Anweisung auf einer Seite sein. `@page` ist ein Beispiel für einen Übergang in ein Razor-spezifisches Markup. Unter [Razor-Syntax](xref:mvc/views/razor#razor-syntax) finden Sie weitere Informationen.

Überprüfen Sie den Lambdaausdruck, der im folgenden HTML-Hilfsprogramm verwendet wird:

```cshtml
@Html.DisplayNameFor(model => model.Movie[0].Title)
```

Das HTML-Hilfsprogramm `DisplayNameFor` überprüft die Eigenschaft `Title`, auf die im Lambdaausdruck verwiesen wird, um den Anzeigenamen zu bestimmen. Der Lambdaausdruck wird überprüft und nicht ausgewertet. Das bedeutet, dass keine Zugriffsverletzung auftritt, wenn `model`, `model.Movie` oder `model.Movie[0]``null` oder leer sind. Wenn der Lambdaausdruck ausgewertet wird, (z.B. mit `@Html.DisplayFor(modelItem => item.Title)`), werden die Eigenschaftswerte ausgewertet.

<a name="md"></a>

### <a name="the-model-directive"></a>Die @model -Direktive

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?range=1-2&highlight=2)]

Die `@model`-Anweisung gibt den Typ des Modells an, das an die Razor-Seite weitergegeben wird. Im vorherigen Beispiel macht die `@model`-Linie die von `PageModel` abgeleitete Klasse der Razor-Seite verfügbar. Das Modell wird in den [HTML-Hilfsprogrammen](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) `@Html.DisplayNameFor` und `@Html.DisplayFor` auf der Seite verwendet.

### <a name="the-layout-page"></a>Die Seite „Layout“

Wählen Sie die Menülinks aus ( **RazorMovie**, **Home**, **Datenschutz**). Auf jeder Seite wird dasselbe Menülayout gezeigt. Das Menülayout wird mithilfe der Datei *Pages/Shared/_Layout.cshtml* implementiert. Öffnen Sie die Datei *Pages/Shared/_Layout.cshtml*.

[Layout](xref:mvc/views/layout)-Vorlagen ermöglichen Ihnen, das HTML-Containerlayout Ihrer Website zentral anzugeben und dann auf mehrere Seiten Ihrer Website anzuwenden. Suchen Sie die Zeile `@RenderBody()`. `RenderBody` ist ein Platzhalter, bei dem alle seitenspezifischen Ansichten, die Sie erstellen, von der Layoutseite *umschlossen* angezeigt werden. Wenn Sie beispielsweise den Link **Privacy** auswählen, wird die Ansicht **Pages/Privacy.cshtml** in der `RenderBody`-Methode gerendert.

<a name="vd"></a>

### <a name="viewdata-and-layout"></a>ViewData und Layout

Sehen Sie sich den folgenden Code aus der Datei *Pages/Movies/Index.cshtml* an:

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?range=1-6&highlight=4-999)]

Der oben markierte Code ist ein Beispiel vom Übergang von Razor in C#. Die Zeichen `{` und `}` schließen einen Block mit C#-Code ein.

Die Basisklasse `PageModel` verfügt über eine `ViewData`-Wörterbucheigenschaft, die zum Hinzufügen von Daten verwendet werden kann, die an eine Ansicht übergeben werden sollen. Mithilfe eines Schlüssel-Wert-Musters können dem `ViewData`-Wörterbuch Objekte hinzugefügt werden. Im vorherigen Beispiel wurde dem `ViewData`-Wörterbuch die Eigenschaft „Title“ hinzugefügt.

Die Eigenschaft „Title“ wird in der Datei *Pages/Shared/_Layout.cshtml* verwendet. Das folgende Markup zeigt die ersten Zeilen der Datei *Pages/_Layout.cshtml* an.

<!-- We need a snapshot copy of layout because we are changing in the next step. -->

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/NU/_Layout.cshtml?highlight=6-99)]

Die Zeile `@*Markup removed for brevity.*@` ist ein Razor-Kommentar, der in Ihrer Layoutdatei nicht angezeigt wird. Im Gegensatz zu HTML-Kommentaren (`<!-- -->`) werden Razor-Kommentare nicht an den Client gesendet.

### <a name="update-the-layout"></a>Aktualisieren des Layouts

Ändern Sie das `<title>`-Element in der Datei *Pages/Shared/_Layout.cshtml*, um **Movie** anstelle von **RazorPagesMovie** anzuzeigen.

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Shared/_Layout.cshtml?range=1-6&highlight=6)]

Suchen Sie in der Datei *Pages/Shared/_Layout.cshtml* das folgende Ankerelement.

```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Index">RazorPagesMovie</a>
```

Ersetzen Sie das vorhergehende Element durch das folgende Markup.

```cshtml
<a class="navbar-brand" asp-page="/Movies/Index">RpMovie</a>
```

Das vorangehende Ankerelement ist ein [Tag Helper (Taghilfsprogramm)](xref:mvc/views/tag-helpers/intro). In diesem Fall handelt es sich um ein [Anchor Tag Helper (Anchor-Taghilfsprogramm)](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper). Das Taghilfsattribut und der -wert `asp-page="/Movies/Index"` erstellen einen Link zur Razor-Seite `/Movies/Index`. Das `asp-area`-Attribut ist leer, daher wird der Bereich im Link nicht verwendet. Weitere Informationen finden Sie unter [Bereiche](xref:mvc/controllers/areas).

Speichern Sie Ihre Änderungen, und testen Sie die App, indem Sie auf den Link **RpMovie** klicken. Wenn Probleme auftreten, sehen Sie sich die Datei [_Layout.cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Shared/_Layout.cshtml) in GitHub an.

Testen Sie die anderen Links – **Home** (Startseite), **RpMovie** (Razor Pages-Film), **Create** (Erstellen), **Edit** (Bearbeiten) und **Delete** (Löschen). Jede Seite legt den Titel fest, der in der Browserregisterkarte angezeigt wird. Wenn Sie eine Seite mit einem Lesezeichen versehen, wird dieser Titel für das Lesezeichen verwendet.

> [!NOTE]
> Sie können unter Umständen in das Feld `Price` keine Kommas als Dezimaltrennzeichen eingeben. Zur Unterstützung der [jQuery-Validierung](https://jqueryvalidation.org/) für nicht englische Gebietsschemas, in denen ein Komma („,“) als Dezimaltrennzeichen verwendet wird, und Nicht-US-englische Datums- und Uhrzeitformate müssen Sie Schritte zur Globalisierung Ihrer App ausführen. In diesem [GitHub-Problem 4076](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) finden Sie Anweisungen zum Hinzufügen von Kommas als Dezimaltrennzeichen.

Die Eigenschaft `Layout` wird in der Datei *Pages/_ViewStart.cshtml* festgelegt:

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/_ViewStart.cshtml)]

Das vorhergehende Markup legt die Layoutdatei für alle Razor-Dateien unter dem Ordner *Pages* auf *Pages/Shared/_Layout.cshtml* fest. Weitere Informationen finden Sie unter [Layout](xref:razor-pages/index#layout).

### <a name="the-create-page-model"></a>Das Seitenmodell „Create“

Überprüfen Sie das Seitenmodell *Pages/Movies/Create.cshtml.cs*:

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml.cs?name=snippetALL)]

Die Methode `OnGet` initialisiert einen beliebigen Zustand, der für die Seite erforderlich ist. Die Seite „Create“ (Erstellen) verfügt nicht über einen initialisierbaren Zustand. Deshalb wird `Page` zurückgegeben. Im späteren Verlauf des Tutorials initialisiert die Methode `OnGet` einen Zustand. Die Methode `Page` erstellt ein `PageResult`-Objekt, das die Seite *Create.cshtml* rendert.

Die Eigenschaft `Movie` verwendet das `[BindProperty]`-Attribut, um die [Modellbindung](xref:mvc/models/model-binding) zu aktivieren. Wenn das Formular „Create“ die Formularwerte bereitstellt, bindet die ASP.NET Core-Laufzeit die bereitgestellten Werte an das `Movie`-Modell.

Die Methode `OnPostAsync` wird ausgeführt, wenn die Seite Formulardaten bereitstellt:

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml.cs?name=snippetPost)]

Wenn es zu Modellfehlern kommt, wird das Formular mit allen bereitgestellten Formulardaten erneut angezeigt. Die meisten Modellfehler können clientseitig abgefangen werden, bevor das Formular bereitgestellt wird. Ein Beispiel für einen Modellfehler ist die Bereitstellung eines Werts für das Datumsfeld, der nicht in ein Datum konvertiert werden kann. Die clientseitige Validierung und die Modellvalidierung werden später in diesem Tutorial erläutert.

Wenn keine Modellfehler auftreten, werden die Daten gespeichert und der Browser zur Indexseite umgeleitet.

### <a name="the-create-no-locrazor-page"></a>Razor-Seite „Erstellen“

Betrachten Sie die Datei *Pages/Movies/Create.cshtml* der Razor-Seite:

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml)]

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Visual Studio zeigt das `<form method="post">`-Tag in einer bestimmten Schriftart an, die für Taghilfsprogramme verwendet wird:

![VS17-Ansicht der Create.cshtml-Seite](page/_static/th.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Weitere Informationen zu Taghilfsprogrammen wie `<form method="post">` finden Sie unter [Taghilfsprogramme in ASP.NET Core](xref:mvc/views/tag-helpers/intro).

# <a name="visual-studio-for-mac"></a>[Visual Studio für Mac](#tab/visual-studio-mac)

Visual Studio für Mac zeigt das `<form method="post">`-Tag in einer bestimmten Schriftart an, die für Taghilfsprogramme verwendet wird.

---

Das Element `<form method="post">` ist ein [Form Tag Helper (Hilfsprogramm für Formulartags)](xref:mvc/views/working-with-forms#the-form-tag-helper). Das Hilfsprogramm für Formulartags enthält automatisch ein [antiforgery token (Antifälschungstoken)](xref:security/anti-request-forgery).

Die Gerüstbau-Engine erstellt Razor-Markup für jedes Feld im Modell (ausgenommen die ID) ähnlich wie folgt:

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml?range=15-20)]

Das [Hilfsprogramm für Validierungstags](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` und `<span asp-validation-for`) zeigt Validierungsfehler. Die Validierung wird an späterer Stelle in dieser Reihe detaillierter beschrieben.

Das [Label Tag Helper (Taghilfsprogramm für Label)](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) generiert den Titel des Labels und das `for`-Attribut für die Eigenschaft `Title`.

Das [Hilfsprogramm für Eingabetags](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control">`) verwendet die Attribute von [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) und generiert HTML-Attribute, die auf der Clientseite für die jQuery-Validierung erforderlich sind.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Dieses Tutorial auf YouTube](https://youtu.be/zxgKjPYnOMM)

> [!div class="step-by-step"]
> [Zurück: Hinzufügen eines Modells](xref:tutorials/razor-pages/model)
> [Nächster Schritt: Datenbank](xref:tutorials/razor-pages/sql)

::: moniker-end
