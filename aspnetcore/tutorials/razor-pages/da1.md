---
title: 'Teil 5: Aktualisieren der generierten Seiten'
author: rick-anderson
description: Dies ist Teil 5 der Tutorialreihe zu Razor Pages.
ms.author: riande
ms.date: 09/20/2020
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
ms.openlocfilehash: 460950413d1dd2d3539c1d62b0eb11f6bb5144a9
ms.sourcegitcommit: db0a6eb0be7bd7f22810a71fe9bf30e957fd116a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96419966"
---
# <a name="part-5-update-the-generated-pages-in-an-aspnet-core-app"></a><span data-ttu-id="c281c-103">Teil 5: Aktualisieren der generierten Seiten in einer ASP.NET Core-App</span><span class="sxs-lookup"><span data-stu-id="c281c-103">Part 5, update the generated pages in an ASP.NET Core app</span></span>

<span data-ttu-id="c281c-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="c281c-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c281c-105">Für den Anfang ist die mit einem Gerüst erstellte Movie-App schon recht ansprechend, doch es gibt Raum für Verbesserungen.</span><span class="sxs-lookup"><span data-stu-id="c281c-105">The scaffolded movie app has a good start, but the presentation isn't ideal.</span></span> <span data-ttu-id="c281c-106">**ReleaseDate** sollte **Release Date** lauten (zwei Wörter).</span><span class="sxs-lookup"><span data-stu-id="c281c-106">**ReleaseDate** should be two words, **Release Date**.</span></span>

![In Chrome geöffnete Movie-Anwendung](sql/_static/5/m55.png)

## <a name="update-the-generated-code"></a><span data-ttu-id="c281c-108">Aktualisieren des generierten Codes</span><span class="sxs-lookup"><span data-stu-id="c281c-108">Update the generated code</span></span>

<span data-ttu-id="c281c-109">Öffnen Sie die Datei *Models/Movie.cs*, und fügen Sie die im folgenden Code gezeigten markierten Zeilen hinzu:</span><span class="sxs-lookup"><span data-stu-id="c281c-109">Open the *Models/Movie.cs* file and add the highlighted lines shown in the following code:</span></span>

[!code-csharp[Main](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateFixed.cs?name=snippet_1&highlight=3,12,17)]

<span data-ttu-id="c281c-110">Im vorherigen Code:</span><span class="sxs-lookup"><span data-stu-id="c281c-110">In the previous code:</span></span>

* <span data-ttu-id="c281c-111">Mit der Datenanmerkung `[Column(TypeName = "decimal(18, 2)")]` kann Entity Framework Core `Price` ordnungsgemäß einer Währung in der Datenbank zuordnen.</span><span class="sxs-lookup"><span data-stu-id="c281c-111">The `[Column(TypeName = "decimal(18, 2)")]` data annotation enables Entity Framework Core to correctly map `Price` to currency in the database.</span></span> <span data-ttu-id="c281c-112">Weitere Informationen finden Sie unter [Datentypen](/ef/core/modeling/relational/data-types).</span><span class="sxs-lookup"><span data-stu-id="c281c-112">For more information, see [Data Types](/ef/core/modeling/relational/data-types).</span></span>
* <span data-ttu-id="c281c-113">Das Attribut [[Display]](xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DisplayMetadata) gibt den Anzeigenamen eines Felds an.</span><span class="sxs-lookup"><span data-stu-id="c281c-113">The [[Display]](xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DisplayMetadata) attribute specifies the display name of a field.</span></span> <span data-ttu-id="c281c-114">Im vorherigen Code „Release Date“ anstelle von ReleaseDate.</span><span class="sxs-lookup"><span data-stu-id="c281c-114">In the preceding code, "Release Date" instead of "ReleaseDate".</span></span>
* <span data-ttu-id="c281c-115">Das Attribut [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) gibt den Typ der Daten (`Date`) an.</span><span class="sxs-lookup"><span data-stu-id="c281c-115">The [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="c281c-116">Die im Feld gespeicherten Zeitinformationen werden nicht angezeigt.</span><span class="sxs-lookup"><span data-stu-id="c281c-116">The time information stored in the field isn't displayed.</span></span>

<span data-ttu-id="c281c-117">[Datenanmerkungen](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) werden im nächsten Tutorial behandelt.</span><span class="sxs-lookup"><span data-stu-id="c281c-117">[DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) is covered in the next tutorial.</span></span>

<span data-ttu-id="c281c-118">Navigieren Sie zu *Pages/Movies*, und bewegen Sie den Mauszeiger über dem Link **Bearbeiten**, um die Ziel-URL anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="c281c-118">Browse to *Pages/Movies* and hover over an **Edit** link to see the target URL.</span></span>

![Browserfenster mit Maus über dem Link „Bearbeiten“ und der angezeigten Link-URL von https://localhost:1234/Movies/Edit/5](~/tutorials/razor-pages/da1/edit7.png)

<span data-ttu-id="c281c-120">Die Links **Edit**, **Details** und **Delete** werden mithilfe des [Hilfsprogramms für Ankertags](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) in der Datei *Pages/Movies/Index.cshtml* generiert.</span><span class="sxs-lookup"><span data-stu-id="c281c-120">The **Edit**, **Details**, and **Delete** links are generated by the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) in the *Pages/Movies/Index.cshtml* file.</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?highlight=16-18&range=32-)]

<span data-ttu-id="c281c-121">[Taghilfsprogramme](xref:mvc/views/tag-helpers/intro) ermöglichen serverseitigem Code das Mitwirken am Erstellen und Rendern von HTML-Elementen in Razor-Dateien.</span><span class="sxs-lookup"><span data-stu-id="c281c-121">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span>

<span data-ttu-id="c281c-122">Im vorherige Code generiert das [Hilfsprogramm für Ankertags](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) dynamisch den Wert des HTML-Attributs `href` auf der Razor Page (die Route ist relativ), das Element `asp-page` und die Routen-ID (`asp-route-id`).</span><span class="sxs-lookup"><span data-stu-id="c281c-122">In the preceding code, the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) dynamically generates the HTML `href` attribute value from the Razor Page (the route is relative), the `asp-page`, and the route identifier (`asp-route-id`).</span></span> <span data-ttu-id="c281c-123">Weitere Informationen finden Sie unter [URL-Generierung für Seiten](xref:razor-pages/index#url-generation-for-pages).</span><span class="sxs-lookup"><span data-stu-id="c281c-123">For more information, see [URL generation for Pages](xref:razor-pages/index#url-generation-for-pages).</span></span>

<span data-ttu-id="c281c-124">Rufen Sie in einem Browser **Quelltext anzeigen** auf, um das generierte Markup zu untersuchen.</span><span class="sxs-lookup"><span data-stu-id="c281c-124">Use **View Source** from a browser to examine the generated markup.</span></span> <span data-ttu-id="c281c-125">Ein Teil des generierten HTML-Codes wird unten gezeigt:</span><span class="sxs-lookup"><span data-stu-id="c281c-125">A portion of the generated HTML is shown below:</span></span>

```html
<td>
  <a href="/Movies/Edit?id=1">Edit</a> |
  <a href="/Movies/Details?id=1">Details</a> |
  <a href="/Movies/Delete?id=1">Delete</a>
</td>
```

   <span data-ttu-id="c281c-126">Die dynamisch generierten Links übergeben die Film-ID mit einer Abfragezeichenfolge.</span><span class="sxs-lookup"><span data-stu-id="c281c-126">The dynamically generated links pass the movie ID with a query string.</span></span> <span data-ttu-id="c281c-127">Beispiel: `?id=1` in `https://localhost:5001/Movies/Details?id=1`.</span><span class="sxs-lookup"><span data-stu-id="c281c-127">For example, the `?id=1` in `https://localhost:5001/Movies/Details?id=1`.</span></span>

### <a name="add-route-template"></a><span data-ttu-id="c281c-128">Hinzufügen der Routenvorlage</span><span class="sxs-lookup"><span data-stu-id="c281c-128">Add route template</span></span>

<span data-ttu-id="c281c-129">Aktualisieren Sie die Razor Pages „Edit“ (Bearbeiten), „Details“ und „Delete“ (Löschen) so, dass die Routenvorlage `{id:int}` verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="c281c-129">Update the Edit, Details, and Delete Razor Pages to use the `{id:int}` route template.</span></span> <span data-ttu-id="c281c-130">Ändern Sie die „page“-Direktive für jede dieser Seiten aus `@page` in `@page "{id:int}"`.</span><span class="sxs-lookup"><span data-stu-id="c281c-130">Change the page directive for each of these pages from `@page` to `@page "{id:int}"`.</span></span> <span data-ttu-id="c281c-131">Führen Sie die App aus, und zeigen Sie dann den Quelltext an.</span><span class="sxs-lookup"><span data-stu-id="c281c-131">Run the app and then view source.</span></span>

<span data-ttu-id="c281c-132">Der generierte HTML-Code fügt die ID dem Pfadteil der URL hinzu:</span><span class="sxs-lookup"><span data-stu-id="c281c-132">The generated HTML adds the ID to the path portion of the URL:</span></span>

```html
<td>
  <a href="/Movies/Edit/1">Edit</a> |
  <a href="/Movies/Details/1">Details</a> |
  <a href="/Movies/Delete/1">Delete</a>
</td>
```

<span data-ttu-id="c281c-133">Eine Anforderung an die Seite mit der Routenvorlage `{id:int}`, die **nicht** die ganze Zahl enthält, gibt den HTTP-Fehler 404 (Nicht gefunden) zurück.</span><span class="sxs-lookup"><span data-stu-id="c281c-133">A request to the page with the `{id:int}` route template that does **not** include the integer will return an HTTP 404 (not found) error.</span></span> <span data-ttu-id="c281c-134">`https://localhost:5001/Movies/Details` gibt beispielsweise den Fehler 404 zurück.</span><span class="sxs-lookup"><span data-stu-id="c281c-134">For example, `https://localhost:5001/Movies/Details` will return a 404 error.</span></span> <span data-ttu-id="c281c-135">Um die ID optional zu machen, fügen Sie `?` an die Routeneinschränkung an:</span><span class="sxs-lookup"><span data-stu-id="c281c-135">To make the ID optional, append `?` to the route constraint:</span></span>

```cshtml
@page "{id:int?}"
```

<span data-ttu-id="c281c-136">Testen Sie das Verhalten von `@page "{id:int?}"`:</span><span class="sxs-lookup"><span data-stu-id="c281c-136">Test the behavior of `@page "{id:int?}"`:</span></span>

1. <span data-ttu-id="c281c-137">Legen Sie die page-Anweisung in *Pages/Movies/Details.cshtml* auf `@page "{id:int?}"` fest.</span><span class="sxs-lookup"><span data-stu-id="c281c-137">Set the page directive in *Pages/Movies/Details.cshtml* to `@page "{id:int?}"`.</span></span>
1. <span data-ttu-id="c281c-138">Setzen Sie einen Haltepunkt in `public async Task<IActionResult> OnGetAsync(int? id)` (in *Pages/Movies/Details.cshtml.cs*).</span><span class="sxs-lookup"><span data-stu-id="c281c-138">Set a break point in `public async Task<IActionResult> OnGetAsync(int? id)`, in *Pages/Movies/Details.cshtml.cs*.</span></span>
1. <span data-ttu-id="c281c-139">Navigieren Sie zu `https://localhost:5001/Movies/Details/`.</span><span class="sxs-lookup"><span data-stu-id="c281c-139">Navigate to `https://localhost:5001/Movies/Details/`.</span></span>

<span data-ttu-id="c281c-140">Mit der `@page "{id:int}"`-Direktive wird der Haltepunkt nie erreicht.</span><span class="sxs-lookup"><span data-stu-id="c281c-140">With the `@page "{id:int}"` directive, the break point is never hit.</span></span> <span data-ttu-id="c281c-141">Die Routing-Engine gibt den HTTP-Fehler 404 zurück.</span><span class="sxs-lookup"><span data-stu-id="c281c-141">The routing engine returns HTTP 404.</span></span> <span data-ttu-id="c281c-142">Bei Verwendung von `@page "{id:int?}"` gibt die `OnGetAsync`-Methode `NotFound` zurück (HTTP 404):</span><span class="sxs-lookup"><span data-stu-id="c281c-142">Using `@page "{id:int?}"`, the `OnGetAsync` method returns `NotFound` (HTTP 404):</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Pages/Movies/Details.cshtml.cs?name=snippet1&highlight=10-13)]

### <a name="review-concurrency-exception-handling"></a><span data-ttu-id="c281c-143">Überprüfen der Behandlung von Ausnahmen bei Parallelität</span><span class="sxs-lookup"><span data-stu-id="c281c-143">Review concurrency exception handling</span></span>

<span data-ttu-id="c281c-144">Überprüfen Sie die `OnPostAsync`-Methode in der Datei *Pages/Movies/Edit.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="c281c-144">Review the `OnPostAsync` method in the *Pages/Movies/Edit.cshtml.cs* file:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Edit.cshtml.cs?name=snippet)]

<span data-ttu-id="c281c-145">Der obige Code erkennt Parallelitätsausnahmen, wenn ein Client den Film löscht und der andere Client Änderungen am Film übermittelt.</span><span class="sxs-lookup"><span data-stu-id="c281c-145">The previous code detects concurrency exceptions when one client deletes the movie and the other client posts changes to the movie.</span></span>

<span data-ttu-id="c281c-146">So testen Sie den Block `catch`</span><span class="sxs-lookup"><span data-stu-id="c281c-146">To test the `catch` block:</span></span>

1. <span data-ttu-id="c281c-147">Legen Sie einen Haltepunkt bei `catch (DbUpdateConcurrencyException)` fest.</span><span class="sxs-lookup"><span data-stu-id="c281c-147">Set a breakpoint on `catch (DbUpdateConcurrencyException)`.</span></span>
1. <span data-ttu-id="c281c-148">Wählen Sie **Edit** für einen Film aus, nehmen Sie Änderungen vor, aber geben Sie nicht **Save** ein.</span><span class="sxs-lookup"><span data-stu-id="c281c-148">Select **Edit** for a movie, make changes, but don't enter **Save**.</span></span>
1. <span data-ttu-id="c281c-149">Klicken Sie in einem anderen Browserfenster auf den Link **Delete** für denselben Film, und löschen Sie dann den Film.</span><span class="sxs-lookup"><span data-stu-id="c281c-149">In another browser window, select the **Delete** link for the same movie, and then delete the movie.</span></span>
1. <span data-ttu-id="c281c-150">Übermitteln Sie im vorherigen Browserfenster Änderungen am Film.</span><span class="sxs-lookup"><span data-stu-id="c281c-150">In the previous browser window, post changes to the movie.</span></span>

<span data-ttu-id="c281c-151">Der Produktionscode sollte Nebenläufigkeitskonflikte erkennen.</span><span class="sxs-lookup"><span data-stu-id="c281c-151">Production code may want to detect concurrency conflicts.</span></span> <span data-ttu-id="c281c-152">Weitere Informationen finden Sie unter [Verarbeiten von Nebenläufigkeitskonflikten](xref:data/ef-rp/concurrency).</span><span class="sxs-lookup"><span data-stu-id="c281c-152">See [Handle concurrency conflicts](xref:data/ef-rp/concurrency) for more information.</span></span>

### <a name="posting-and-binding-review"></a><span data-ttu-id="c281c-153">Überprüfen der Bereitstellung und Bindung</span><span class="sxs-lookup"><span data-stu-id="c281c-153">Posting and binding review</span></span>

<span data-ttu-id="c281c-154">Sehen Sie sich die Datei *Pages/Movies/Edit.cshtml.cs* an:</span><span class="sxs-lookup"><span data-stu-id="c281c-154">Examine the *Pages/Movies/Edit.cshtml.cs* file:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/SnapShots/Edit.cshtml.cs?name=snippet2)]

<span data-ttu-id="c281c-155">Wenn eine HTTP GET-Anforderung an die Seite „Movies/Edit“ gerichtet wird (z. B. `https://localhost:5001/Movies/Edit/3`):</span><span class="sxs-lookup"><span data-stu-id="c281c-155">When an HTTP GET request is made to the Movies/Edit page, for example, `https://localhost:5001/Movies/Edit/3`:</span></span>

* <span data-ttu-id="c281c-156">Die `OnGetAsync`-Methode ruft den Film aus der Datenbank ab und gibt die `Page`-Methode zurück.</span><span class="sxs-lookup"><span data-stu-id="c281c-156">The `OnGetAsync` method fetches the movie from the database and returns the `Page` method.</span></span>
* <span data-ttu-id="c281c-157">Die `Page`-Methode rendert die Seite  *Pages/Movies/Edit.cshtml* Razor.</span><span class="sxs-lookup"><span data-stu-id="c281c-157">The `Page` method renders the *Pages/Movies/Edit.cshtml* Razor Page.</span></span> <span data-ttu-id="c281c-158">Die Datei *Pages/Movies/Edit.cshtml* enthält die Modellanweisung `@model RazorPagesMovie.Pages.Movies.EditModel`, die das Filmmodell auf der Seite verfügbar macht.</span><span class="sxs-lookup"><span data-stu-id="c281c-158">The *Pages/Movies/Edit.cshtml* file contains the model directive `@model RazorPagesMovie.Pages.Movies.EditModel`, which makes the movie model available on the page.</span></span>
* <span data-ttu-id="c281c-159">Das Bearbeitungsformular wird mit den Werten aus dem Film angezeigt.</span><span class="sxs-lookup"><span data-stu-id="c281c-159">The Edit form is displayed with the values from the movie.</span></span>

<span data-ttu-id="c281c-160">Wenn die Seite „Filme/Bearbeiten“ bereitgestellt wird:</span><span class="sxs-lookup"><span data-stu-id="c281c-160">When the Movies/Edit page is posted:</span></span>

* <span data-ttu-id="c281c-161">Die Formularwerte auf der Seite sind an die `Movie`-Eigenschaft gebunden.</span><span class="sxs-lookup"><span data-stu-id="c281c-161">The form values on the page are bound to the `Movie` property.</span></span> <span data-ttu-id="c281c-162">Das `[BindProperty]`-Attribut ermöglicht die [Modellbindung](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="c281c-162">The `[BindProperty]` attribute enables [Model binding](xref:mvc/models/model-binding).</span></span>

  ```csharp
  [BindProperty]
  public Movie Movie { get; set; }
  ```

* <span data-ttu-id="c281c-163">Bei Fehlern beim Modellstatus (Beispiel: `ReleaseDate` kann nicht in ein Datum konvertiert werden) wird das Formular mit den übermittelten Werten erneut angezeigt.</span><span class="sxs-lookup"><span data-stu-id="c281c-163">If there are errors in the model state, for example, `ReleaseDate` cannot be converted to a date, the form is redisplayed with the submitted values.</span></span>
* <span data-ttu-id="c281c-164">Wenn keine Modellfehler vorhanden sind, wird der Film gespeichert.</span><span class="sxs-lookup"><span data-stu-id="c281c-164">If there are no model errors, the movie is saved.</span></span>

<span data-ttu-id="c281c-165">Die HTTP GET-Methoden auf den Razor Pages Index „Create“ und „Delete“ folgen einem ähnlichen Muster.</span><span class="sxs-lookup"><span data-stu-id="c281c-165">The HTTP GET methods in the Index, Create, and Delete Razor pages follow a similar pattern.</span></span> <span data-ttu-id="c281c-166">Die HTTP-POST-Methode `OnPostAsync` auf der Razor Page „Create“ folgt einem ähnlichen Muster wie die `OnPostAsync`-Methode auf der Razor Page „Edit“.</span><span class="sxs-lookup"><span data-stu-id="c281c-166">The HTTP POST `OnPostAsync` method in the Create Razor Page follows a similar pattern to the `OnPostAsync` method in the Edit Razor Page.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c281c-167">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="c281c-167">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="c281c-168">[Zurück: Arbeiten mit einer Datenbank](xref:tutorials/razor-pages/sql)
> [Weiter: Hinzufügen der Suche](xref:tutorials/razor-pages/search)</span><span class="sxs-lookup"><span data-stu-id="c281c-168">[Previous: Work with a database](xref:tutorials/razor-pages/sql)
[Next: Add search](xref:tutorials/razor-pages/search)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="c281c-169">Für den Anfang ist die mit einem Gerüst erstellte Movie-App schon recht ansprechend, doch es gibt Raum für Verbesserungen.</span><span class="sxs-lookup"><span data-stu-id="c281c-169">The scaffolded movie app has a good start, but the presentation isn't ideal.</span></span> <span data-ttu-id="c281c-170">**ReleaseDate** sollte **Release Date** lauten (zwei Wörter).</span><span class="sxs-lookup"><span data-stu-id="c281c-170">**ReleaseDate** should be two words, **Release Date**.</span></span>

![In Chrome geöffnete Movie-Anwendung](sql/_static/m55https.png)

## <a name="update-the-generated-code"></a><span data-ttu-id="c281c-172">Aktualisieren des generierten Codes</span><span class="sxs-lookup"><span data-stu-id="c281c-172">Update the generated code</span></span>

<span data-ttu-id="c281c-173">Öffnen Sie die Datei *Models/Movie.cs*, und fügen Sie die im folgenden Code gezeigten markierten Zeilen hinzu:</span><span class="sxs-lookup"><span data-stu-id="c281c-173">Open the *Models/Movie.cs* file and add the highlighted lines shown in the following code:</span></span>

[!code-csharp[Main](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/MovieDateFixed.cs?name=snippet_1&highlight=3,12,17)]

<span data-ttu-id="c281c-174">Mit der Datenanmerkung `[Column(TypeName = "decimal(18, 2)")]` kann Entity Framework Core `Price` ordnungsgemäß einer Währung in der Datenbank zuordnen.</span><span class="sxs-lookup"><span data-stu-id="c281c-174">The `[Column(TypeName = "decimal(18, 2)")]` data annotation enables Entity Framework Core to correctly map `Price` to currency in the database.</span></span> <span data-ttu-id="c281c-175">Weitere Informationen finden Sie unter [Datentypen](/ef/core/modeling/relational/data-types).</span><span class="sxs-lookup"><span data-stu-id="c281c-175">For more information, see [Data Types](/ef/core/modeling/relational/data-types).</span></span>

<span data-ttu-id="c281c-176">[Datenanmerkungen](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) werden im nächsten Tutorial behandelt.</span><span class="sxs-lookup"><span data-stu-id="c281c-176">[DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) is covered in the next tutorial.</span></span> <span data-ttu-id="c281c-177">Das Attribut [[Display]](xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DisplayMetadata) gibt an, was für den Namen eines Felds angezeigt werden soll (in diesem Fall „Release Date“ statt „ReleaseDate“).</span><span class="sxs-lookup"><span data-stu-id="c281c-177">The [[Display]](xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DisplayMetadata) attribute specifies what to display for the name of a field, in this case "Release Date" instead of "ReleaseDate".</span></span> <span data-ttu-id="c281c-178">Das Attribut [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) gibt den Typ der Daten (`Date`) an, damit die im Feld gespeicherten Zeitinformationen nicht angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="c281c-178">The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`), so the time information stored in the field isn't displayed.</span></span>

<span data-ttu-id="c281c-179">Navigieren Sie zu „Pages/Movies“, und bewegen Sie den Mauszeiger über dem Link **Bearbeiten**, um die Ziel-URL anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="c281c-179">Browse to Pages/Movies and  hover over an **Edit** link to see the target URL.</span></span>

![Browserfenster mit Maus über dem Link „Bearbeiten“ und der angezeigten Link-URL von http://localhost:1234/Movies/Edit/5](~/tutorials/razor-pages/da1/edit7.png)

<span data-ttu-id="c281c-181">Die Links **Edit**, **Details** und **Delete** werden mithilfe des [Hilfsprogramms für Ankertags](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) in der Datei *Pages/Movies/Index.cshtml* generiert.</span><span class="sxs-lookup"><span data-stu-id="c281c-181">The **Edit**, **Details**, and **Delete** links are generated by the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) in the *Pages/Movies/Index.cshtml* file.</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?highlight=16-18&range=32-)]

<span data-ttu-id="c281c-182">[Taghilfsprogramme](xref:mvc/views/tag-helpers/intro) ermöglichen serverseitigem Code das Mitwirken am Erstellen und Rendern von HTML-Elementen in Razor-Dateien.</span><span class="sxs-lookup"><span data-stu-id="c281c-182">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span> <span data-ttu-id="c281c-183">Im vorangehenden Code generiert das `AnchorTagHelper` dynamisch den Wert des HTML-Attributs `href` auf der Razor Page (die Route ist relativ), das `asp-page`-Element und die Routen-ID (`asp-route-id`).</span><span class="sxs-lookup"><span data-stu-id="c281c-183">In the preceding code, the `AnchorTagHelper` dynamically generates the HTML `href` attribute value from the Razor Page (the route is relative), the `asp-page`, and the route id (`asp-route-id`).</span></span> <span data-ttu-id="c281c-184">Weitere Informationen finden Sie unter [URL-Generierung für Seiten](xref:razor-pages/index#url-generation-for-pages).</span><span class="sxs-lookup"><span data-stu-id="c281c-184">See [URL generation for Pages](xref:razor-pages/index#url-generation-for-pages) for more information.</span></span>

<span data-ttu-id="c281c-185">Rufen Sie in einem Browser **Quelltext anzeigen** auf, um das generierte Markup zu untersuchen.</span><span class="sxs-lookup"><span data-stu-id="c281c-185">Use **View Source** from a browser to examine the generated markup.</span></span> <span data-ttu-id="c281c-186">Ein Teil des generierten HTML-Codes wird unten gezeigt:</span><span class="sxs-lookup"><span data-stu-id="c281c-186">A portion of the generated HTML is shown below:</span></span>

```html
<td>
  <a href="/Movies/Edit?id=1">Edit</a> |
  <a href="/Movies/Details?id=1">Details</a> |
  <a href="/Movies/Delete?id=1">Delete</a>
</td>
```

<span data-ttu-id="c281c-187">Die dynamisch generierten Links übergeben die Film-ID mit einer Abfragezeichenfolge.</span><span class="sxs-lookup"><span data-stu-id="c281c-187">The dynamically generated links pass the movie ID with a query string.</span></span> <span data-ttu-id="c281c-188">Beispiel: `?id=1` in `https://localhost:5001/Movies/Details?id=1`.</span><span class="sxs-lookup"><span data-stu-id="c281c-188">For example, the `?id=1` in  `https://localhost:5001/Movies/Details?id=1`.</span></span>

<span data-ttu-id="c281c-189">Aktualisieren Sie die Razor Pages „Edit“, „Details“ und „Delete“ so, dass die Routenvorlage „{id:int}“ verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="c281c-189">Update the Edit, Details, and Delete Razor Pages to use the "{id:int}" route template.</span></span> <span data-ttu-id="c281c-190">Ändern Sie die „page“-Direktive für jede dieser Seiten aus `@page` in `@page "{id:int}"`.</span><span class="sxs-lookup"><span data-stu-id="c281c-190">Change the page directive for each of these pages from `@page` to `@page "{id:int}"`.</span></span> <span data-ttu-id="c281c-191">Führen Sie die App aus, und zeigen Sie dann den Quelltext an.</span><span class="sxs-lookup"><span data-stu-id="c281c-191">Run the app and then view source.</span></span> <span data-ttu-id="c281c-192">Der generierte HTML-Code fügt die ID dem Pfadteil der URL hinzu:</span><span class="sxs-lookup"><span data-stu-id="c281c-192">The generated HTML adds the ID to the path portion of the URL:</span></span>

```html
<td>
  <a href="/Movies/Edit/1">Edit</a> |
  <a href="/Movies/Details/1">Details</a> |
  <a href="/Movies/Delete/1">Delete</a>
</td>
```

<span data-ttu-id="c281c-193">Eine Anforderung an die Seite mit der Routenvorlage „{id:int}“, die **nicht** die ganze Zahl enthält, gibt den HTTP-Fehler 404 (Nicht gefunden) zurück.</span><span class="sxs-lookup"><span data-stu-id="c281c-193">A request to the page with the "{id:int}" route template that does **not** include the integer will return an HTTP 404 (not found) error.</span></span> <span data-ttu-id="c281c-194">`https://localhost:5001/Movies/Details` gibt beispielsweise den Fehler 404 zurück.</span><span class="sxs-lookup"><span data-stu-id="c281c-194">For example, `https://localhost:5001/Movies/Details` will return a 404 error.</span></span> <span data-ttu-id="c281c-195">Um die ID optional zu machen, fügen Sie `?` an die Routeneinschränkung an:</span><span class="sxs-lookup"><span data-stu-id="c281c-195">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="c281c-196">So testen Sie das Verhalten von `@page "{id:int?}"`:</span><span class="sxs-lookup"><span data-stu-id="c281c-196">To test the behavior of `@page "{id:int?}"`:</span></span>

* <span data-ttu-id="c281c-197">Legen Sie die page-Anweisung in *Pages/Movies/Details.cshtml* auf `@page "{id:int?}"` fest.</span><span class="sxs-lookup"><span data-stu-id="c281c-197">Set the page directive in *Pages/Movies/Details.cshtml* to `@page "{id:int?}"`.</span></span>
* <span data-ttu-id="c281c-198">Setzen Sie einen Haltepunkt in `public async Task<IActionResult> OnGetAsync(int? id)` (in *Pages/Movies/Details.cshtml.cs*).</span><span class="sxs-lookup"><span data-stu-id="c281c-198">Set a break point in `public async Task<IActionResult> OnGetAsync(int? id)`, in *Pages/Movies/Details.cshtml.cs*.</span></span>
* <span data-ttu-id="c281c-199">Navigieren Sie zu `https://localhost:5001/Movies/Details/`.</span><span class="sxs-lookup"><span data-stu-id="c281c-199">Navigate to `https://localhost:5001/Movies/Details/`.</span></span>

<span data-ttu-id="c281c-200">Mit der `@page "{id:int}"`-Direktive wird der Haltepunkt nie erreicht.</span><span class="sxs-lookup"><span data-stu-id="c281c-200">With the `@page "{id:int}"` directive, the break point is never hit.</span></span> <span data-ttu-id="c281c-201">Die Routing-Engine gibt den HTTP-Fehler 404 zurück.</span><span class="sxs-lookup"><span data-stu-id="c281c-201">The routing engine returns HTTP 404.</span></span> <span data-ttu-id="c281c-202">Bei Verwendung von `@page "{id:int?}"` gibt die `OnGetAsync`-Methode `NotFound` zurück (HTTP 404):</span><span class="sxs-lookup"><span data-stu-id="c281c-202">Using `@page "{id:int?}"`, the `OnGetAsync` method returns `NotFound` (HTTP 404):</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Details.cshtml.cs?name=snippet1&highlight=10-13)]

### <a name="review-concurrency-exception-handling"></a><span data-ttu-id="c281c-203">Überprüfen der Behandlung von Ausnahmen bei Parallelität</span><span class="sxs-lookup"><span data-stu-id="c281c-203">Review concurrency exception handling</span></span>

<span data-ttu-id="c281c-204">Überprüfen Sie die `OnPostAsync`-Methode in der Datei *Pages/Movies/Edit.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="c281c-204">Review the `OnPostAsync` method in the *Pages/Movies/Edit.cshtml.cs* file:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Edit.cshtml.cs?name=snippet)]

<span data-ttu-id="c281c-205">Der obige Code erkennt Parallelitätsausnahmen, wenn ein Client den Film löscht und der andere Client Änderungen am Film übermittelt.</span><span class="sxs-lookup"><span data-stu-id="c281c-205">The previous code detects concurrency exceptions when the one client deletes the movie and the other client posts changes to the movie.</span></span>

<span data-ttu-id="c281c-206">So testen Sie den Block `catch`</span><span class="sxs-lookup"><span data-stu-id="c281c-206">To test the `catch` block:</span></span>

* <span data-ttu-id="c281c-207">Legen Sie einen Haltepunkt auf `catch (DbUpdateConcurrencyException)`fest.</span><span class="sxs-lookup"><span data-stu-id="c281c-207">Set a breakpoint on `catch (DbUpdateConcurrencyException)`</span></span>
* <span data-ttu-id="c281c-208">Wählen Sie **Edit** für einen Film aus, nehmen Sie Änderungen vor, aber geben Sie nicht **Save** ein.</span><span class="sxs-lookup"><span data-stu-id="c281c-208">Select **Edit** for a movie, make changes, but don't enter **Save**.</span></span>
* <span data-ttu-id="c281c-209">Klicken Sie in einem anderen Browserfenster auf den Link **Delete** für denselben Film, und löschen Sie dann den Film.</span><span class="sxs-lookup"><span data-stu-id="c281c-209">In another browser window, select the **Delete** link for the same movie, and then delete the movie.</span></span>
* <span data-ttu-id="c281c-210">Übermitteln Sie im vorherigen Browserfenster Änderungen am Film.</span><span class="sxs-lookup"><span data-stu-id="c281c-210">In the previous browser window, post changes to the movie.</span></span>

<span data-ttu-id="c281c-211">Der Produktionscode sollte Nebenläufigkeitskonflikte erkennen.</span><span class="sxs-lookup"><span data-stu-id="c281c-211">Production code may want to detect concurrency conflicts.</span></span> <span data-ttu-id="c281c-212">Weitere Informationen finden Sie unter [Verarbeiten von Nebenläufigkeitskonflikten](xref:data/ef-rp/concurrency).</span><span class="sxs-lookup"><span data-stu-id="c281c-212">See [Handle concurrency conflicts](xref:data/ef-rp/concurrency) for more information.</span></span>

### <a name="posting-and-binding-review"></a><span data-ttu-id="c281c-213">Überprüfen der Bereitstellung und Bindung</span><span class="sxs-lookup"><span data-stu-id="c281c-213">Posting and binding review</span></span>

<span data-ttu-id="c281c-214">Sehen Sie sich die Datei *Pages/Movies/Edit.cshtml.cs* an:</span><span class="sxs-lookup"><span data-stu-id="c281c-214">Examine the *Pages/Movies/Edit.cshtml.cs* file:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Edit21.cshtml.cs?name=snippet2)]

<span data-ttu-id="c281c-215">Wenn eine HTTP GET-Anforderung an die Seite „Movies/Edit“ gerichtet wird (z. B. `https://localhost:5001/Movies/Edit/3`):</span><span class="sxs-lookup"><span data-stu-id="c281c-215">When an HTTP GET request is made to the Movies/Edit page, for example, `https://localhost:5001/Movies/Edit/3`:</span></span>

* <span data-ttu-id="c281c-216">Die `OnGetAsync`-Methode ruft den Film aus der Datenbank ab und gibt die `Page`-Methode zurück.</span><span class="sxs-lookup"><span data-stu-id="c281c-216">The `OnGetAsync` method fetches the movie from the database and returns the `Page` method.</span></span> 
* <span data-ttu-id="c281c-217">Die `Page`-Methode rendert die Seite  *Pages/Movies/Edit.cshtml* Razor.</span><span class="sxs-lookup"><span data-stu-id="c281c-217">The `Page` method renders the *Pages/Movies/Edit.cshtml* Razor Page.</span></span> <span data-ttu-id="c281c-218">Die Datei *Pages/Movies/Edit.cshtml* enthält die Modellanweisung `@model RazorPagesMovie.Pages.Movies.EditModel`, die das Filmmodell auf der Seite verfügbar macht.</span><span class="sxs-lookup"><span data-stu-id="c281c-218">The *Pages/Movies/Edit.cshtml* file contains the model directive `@model RazorPagesMovie.Pages.Movies.EditModel`, which makes the movie model available on the page.</span></span>
* <span data-ttu-id="c281c-219">Das Bearbeitungsformular wird mit den Werten aus dem Film angezeigt.</span><span class="sxs-lookup"><span data-stu-id="c281c-219">The Edit form is displayed with the values from the movie.</span></span>

<span data-ttu-id="c281c-220">Wenn die Seite „Filme/Bearbeiten“ bereitgestellt wird:</span><span class="sxs-lookup"><span data-stu-id="c281c-220">When the Movies/Edit page is posted:</span></span>

* <span data-ttu-id="c281c-221">Die Formularwerte auf der Seite sind an die `Movie`-Eigenschaft gebunden.</span><span class="sxs-lookup"><span data-stu-id="c281c-221">The form values on the page are bound to the `Movie` property.</span></span> <span data-ttu-id="c281c-222">Das `[BindProperty]`-Attribut ermöglicht die [Modellbindung](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="c281c-222">The `[BindProperty]` attribute enables [Model binding](xref:mvc/models/model-binding).</span></span>

  ```csharp
  [BindProperty]
  public Movie Movie { get; set; }
  ```

* <span data-ttu-id="c281c-223">Bei Fehlern beim Modellstatus (Beispiel: `ReleaseDate` kann nicht in ein Datum konvertiert werden) wird das Formular mit den übermittelten Werten angezeigt.</span><span class="sxs-lookup"><span data-stu-id="c281c-223">If there are errors in the model state, for example, `ReleaseDate` cannot be converted to a date, the form is displayed with the submitted values.</span></span>
* <span data-ttu-id="c281c-224">Wenn keine Modellfehler vorhanden sind, wird der Film gespeichert.</span><span class="sxs-lookup"><span data-stu-id="c281c-224">If there are no model errors, the movie is saved.</span></span>

<span data-ttu-id="c281c-225">Die HTTP GET-Methoden auf den Razor Pages Index „Create“ und „Delete“ folgen einem ähnlichen Muster.</span><span class="sxs-lookup"><span data-stu-id="c281c-225">The HTTP GET methods in the Index, Create, and Delete Razor pages follow a similar pattern.</span></span> <span data-ttu-id="c281c-226">Die HTTP-POST-Methode `OnPostAsync` auf der Razor Page „Create“ folgt einem ähnlichen Muster wie die `OnPostAsync`-Methode auf der Razor Page „Edit“.</span><span class="sxs-lookup"><span data-stu-id="c281c-226">The HTTP POST `OnPostAsync` method in the Create Razor Page follows a similar pattern to the `OnPostAsync` method in the Edit Razor Page.</span></span>

<span data-ttu-id="c281c-227">Die Suche wird im nächsten Tutorial hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="c281c-227">Search is added in the next tutorial.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c281c-228">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="c281c-228">Additional resources</span></span>

* [<span data-ttu-id="c281c-229">Dieses Tutorial auf YouTube</span><span class="sxs-lookup"><span data-stu-id="c281c-229">YouTube version of this tutorial</span></span>](https://youtu.be/yLnnleREMtQ)

> [!div class="step-by-step"]
> <span data-ttu-id="c281c-230">[Zurück: Arbeiten mit einer Datenbank](xref:tutorials/razor-pages/sql)
> [Weiter: Hinzufügen der Suche](xref:tutorials/razor-pages/search)</span><span class="sxs-lookup"><span data-stu-id="c281c-230">[Previous: Work with a database](xref:tutorials/razor-pages/sql)
[Next: Add search](xref:tutorials/razor-pages/search)</span></span>

::: moniker-end
