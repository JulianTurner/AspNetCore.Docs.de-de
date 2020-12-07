---
title: 'Teil 3: Gerüstbau mit Razor Pages'
author: rick-anderson
description: Dies ist Teil 3 der Tutorialreihe zu Razor Pages.
ms.author: riande
ms.date: 09/25/2020
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
uid: tutorials/razor-pages/page
ms.openlocfilehash: 4a5369b9e40de89ac9a1895466e7bdd7afb9d32e
ms.sourcegitcommit: db0a6eb0be7bd7f22810a71fe9bf30e957fd116a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96420031"
---
# <a name="part-3-scaffolded-no-locrazor-pages-in-aspnet-core"></a><span data-ttu-id="7daee-103">Part 3: Gerüstbau mit Razor Pages in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7daee-103">Part 3, scaffolded Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="7daee-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="7daee-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="7daee-105">In diesem Tutorial werden die Razor Pages näher untersucht, die durch den Gerüstbau im [vorherigen Tutorial](xref:tutorials/razor-pages/model) erstellt wurden.</span><span class="sxs-lookup"><span data-stu-id="7daee-105">This tutorial examines the Razor Pages created by scaffolding in the [previous tutorial](xref:tutorials/razor-pages/model).</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="7daee-106">[Zeigen Sie Beispielcode an, oder laden Sie diesen herunter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="7daee-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0 >= aspnetcore-3.0"

<span data-ttu-id="7daee-107">[Zeigen Sie Beispielcode an, oder laden Sie diesen herunter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="7daee-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="the-create-delete-details-and-edit-pages"></a><span data-ttu-id="7daee-108">Die Seiten „Create“, „Delete“, „Details“ und „Edit“</span><span class="sxs-lookup"><span data-stu-id="7daee-108">The Create, Delete, Details, and Edit pages</span></span>

<span data-ttu-id="7daee-109">Überprüfen Sie das Seitenmodell *Pages/Movies/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="7daee-109">Examine the *Pages/Movies/Index.cshtml.cs* Page Model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs)]

<span data-ttu-id="7daee-110">Razor-Seiten werden von `PageModel` abgeleitet.</span><span class="sxs-lookup"><span data-stu-id="7daee-110">Razor Pages are derived from `PageModel`.</span></span> <span data-ttu-id="7daee-111">Gemäß der Konvention wird die von `PageModel` abgeleitete Klasse `<PageName>Model` genannt.</span><span class="sxs-lookup"><span data-stu-id="7daee-111">By convention, the `PageModel`-derived class is named `<PageName>Model`.</span></span> <span data-ttu-id="7daee-112">Der Konstruktor verwendet [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection), um `RazorPagesMovieContext` zur Seite hinzuzufügen:</span><span class="sxs-lookup"><span data-stu-id="7daee-112">The constructor uses [dependency injection](xref:fundamentals/dependency-injection) to add the `RazorPagesMovieContext` to the page:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet1&highlight=5)]

<span data-ttu-id="7daee-113">Weitere Informationen zum asynchronen Programmieren mit Entity Framework finden Sie unter [Asynchroner Code](xref:data/ef-rp/intro#asynchronous-code).</span><span class="sxs-lookup"><span data-stu-id="7daee-113">See [Asynchronous code](xref:data/ef-rp/intro#asynchronous-code) for more information on asynchronous programming with Entity Framework.</span></span>

<span data-ttu-id="7daee-114">Wenn eine Anforderung an die Seite erfolgt, gibt die Methode `OnGetAsync` eine Filmliste an die Razor-Seite zurück.</span><span class="sxs-lookup"><span data-stu-id="7daee-114">When a request is made for the page, the `OnGetAsync` method returns a list of movies to the Razor Page.</span></span> <span data-ttu-id="7daee-115">Auf einer Razor Page wird `OnGetAsync` oder `OnGet` aufgerufen, um den Status der Seite zu initialisieren.</span><span class="sxs-lookup"><span data-stu-id="7daee-115">On a Razor Page, `OnGetAsync` or `OnGet` is called to initialize the state of the page.</span></span> <span data-ttu-id="7daee-116">In diesem Fall ruft `OnGetAsync` eine Liste von Filmen ab und zeigt diese an.</span><span class="sxs-lookup"><span data-stu-id="7daee-116">In this case, `OnGetAsync` gets a list of movies and displays them.</span></span>

<span data-ttu-id="7daee-117">Wenn `void` von `OnGet` oder `Task`von `OnGetAsync` zurückgegeben wird, wird keine Rückgabeanweisung verwendet.</span><span class="sxs-lookup"><span data-stu-id="7daee-117">When `OnGet` returns `void` or `OnGetAsync` returns `Task`, no return statement is used.</span></span> <span data-ttu-id="7daee-118">Beispiel: die Seite „Datenschutz“:</span><span class="sxs-lookup"><span data-stu-id="7daee-118">For example the Privacy Page:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Privacy.cshtml.cs?name=snippet)]

<span data-ttu-id="7daee-119">Wenn der Rückgabetyp `IActionResult` oder `Task<IActionResult>` ist, muss eine Rückgabeanweisung bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="7daee-119">When the return type is `IActionResult` or `Task<IActionResult>`, a return statement must be provided.</span></span> <span data-ttu-id="7daee-120">Dies kann z. B. die `OnPostAsync`-Methode *Pages/Movies/Create.cshtml.cs* sein:</span><span class="sxs-lookup"><span data-stu-id="7daee-120">For example, the *Pages/Movies/Create.cshtml.cs* `OnPostAsync` method:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippet)]

<a name="index"></a> <span data-ttu-id="7daee-121">Sehen Sie sich die Razor-Seite *Pages/Movies/Index.cshtml* an:</span><span class="sxs-lookup"><span data-stu-id="7daee-121">Examine the *Pages/Movies/Index.cshtml* Razor Page:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml)]

<span data-ttu-id="7daee-122">Razor kann aus HTML in C# oder ein Razor-spezifisches Markup übergehen.</span><span class="sxs-lookup"><span data-stu-id="7daee-122">Razor can transition from HTML into C# or into Razor-specific markup.</span></span> <span data-ttu-id="7daee-123">Wenn auf ein `@`-Symbol ein für [Razor reserviertes Schlüsselwort](xref:mvc/views/razor#razor-reserved-keywords) folgt, wechselt es in ein Razor-spezifisches Markup. Andernfalls geht es in C# über.</span><span class="sxs-lookup"><span data-stu-id="7daee-123">When an `@` symbol is followed by a [Razor reserved keyword](xref:mvc/views/razor#razor-reserved-keywords), it transitions into Razor-specific markup, otherwise it transitions into C#.</span></span>

### <a name="the-page-directive"></a><span data-ttu-id="7daee-124">Die @page -Direktive</span><span class="sxs-lookup"><span data-stu-id="7daee-124">The @page directive</span></span>

<span data-ttu-id="7daee-125">Die Razor-Anweisung `@page` wandelt die Datei in eine MVC-Aktion um. Das bedeutet, dass sie Anforderungen verarbeiten kann.</span><span class="sxs-lookup"><span data-stu-id="7daee-125">The `@page` Razor directive makes the file an MVC action, which means that it can handle requests.</span></span> <span data-ttu-id="7daee-126">`@page` muss die erste Razor-Anweisung auf einer Seite sein.</span><span class="sxs-lookup"><span data-stu-id="7daee-126">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="7daee-127">`@page` und `@model` sind Beispiele für einen Übergang in ein für Razor spezifisches Markup.</span><span class="sxs-lookup"><span data-stu-id="7daee-127">`@page` and `@model` are examples of transitioning into Razor-specific markup.</span></span> <span data-ttu-id="7daee-128">Unter [Razor-Syntax](xref:mvc/views/razor#razor-syntax) finden Sie weitere Informationen.</span><span class="sxs-lookup"><span data-stu-id="7daee-128">See [Razor syntax](xref:mvc/views/razor#razor-syntax) for more information.</span></span>

<a name="md"></a>

### <a name="the-model-directive"></a><span data-ttu-id="7daee-129">Die @model -Direktive</span><span class="sxs-lookup"><span data-stu-id="7daee-129">The @model directive</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml?range=1-2&highlight=2)]

<span data-ttu-id="7daee-130">Die `@model`-Anweisung gibt den Typ des Modells an, das an die Razor-Seite weitergegeben wird.</span><span class="sxs-lookup"><span data-stu-id="7daee-130">The `@model` directive specifies the type of the model passed to the Razor Page.</span></span> <span data-ttu-id="7daee-131">Im vorherigen Beispiel macht die `@model`-Linie die von `PageModel` abgeleitete Klasse der Razor-Seite verfügbar.</span><span class="sxs-lookup"><span data-stu-id="7daee-131">In the preceding example, the `@model` line makes the `PageModel`-derived class available to the Razor Page.</span></span> <span data-ttu-id="7daee-132">Das Modell wird in den [HTML-Hilfsprogrammen](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) `@Html.DisplayNameFor` und `@Html.DisplayFor` auf der Seite verwendet.</span><span class="sxs-lookup"><span data-stu-id="7daee-132">The model is used in the `@Html.DisplayNameFor` and `@Html.DisplayFor` [HTML Helpers](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) on the page.</span></span>


<span data-ttu-id="7daee-133">Überprüfen Sie den Lambdaausdruck, der im folgenden HTML-Hilfsprogramm verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="7daee-133">Examine the lambda expression used in the following HTML Helper:</span></span>

```cshtml
@Html.DisplayNameFor(model => model.Movie[0].Title)
```

<span data-ttu-id="7daee-134">Das HTML-Hilfsprogramm <xref:System.Web.Mvc.Html.DisplayNameExtensions.DisplayNameFor%2A?displayProperty=nameWithType> überprüft die Eigenschaft `Title`, auf die im Lambdaausdruck verwiesen wird, um den Anzeigenamen zu bestimmen.</span><span class="sxs-lookup"><span data-stu-id="7daee-134">The <xref:System.Web.Mvc.Html.DisplayNameExtensions.DisplayNameFor%2A?displayProperty=nameWithType> HTML Helper inspects the `Title` property referenced in the lambda expression to determine the display name.</span></span> <span data-ttu-id="7daee-135">Der Lambdaausdruck wird überprüft und nicht ausgewertet.</span><span class="sxs-lookup"><span data-stu-id="7daee-135">The lambda expression is inspected rather than evaluated.</span></span> <span data-ttu-id="7daee-136">Das bedeutet, dass keine Zugriffsverletzung auftritt, wenn `model`, `model.Movie` oder `model.Movie[0]``null` oder leer ist.</span><span class="sxs-lookup"><span data-stu-id="7daee-136">That means there is no access violation when `model`, `model.Movie`, or `model.Movie[0]` is `null` or empty.</span></span> <span data-ttu-id="7daee-137">Wenn der Lambdaausdruck ausgewertet wird, (z.B. mit `@Html.DisplayFor(modelItem => item.Title)`), werden die Eigenschaftswerte ausgewertet.</span><span class="sxs-lookup"><span data-stu-id="7daee-137">When the lambda expression is evaluated, for example, with `@Html.DisplayFor(modelItem => item.Title)`, the model's property values are evaluated.</span></span>

### <a name="the-layout-page"></a><span data-ttu-id="7daee-138">Die Seite „Layout“</span><span class="sxs-lookup"><span data-stu-id="7daee-138">The layout page</span></span>

<span data-ttu-id="7daee-139">Wählen Sie die Menülinks aus ( **RazorPagesMovie**, **Home** und **Datenschutz**) aus.</span><span class="sxs-lookup"><span data-stu-id="7daee-139">Select the menu links **RazorPagesMovie**, **Home**, and **Privacy**.</span></span> <span data-ttu-id="7daee-140">Auf jeder Seite wird dasselbe Menülayout gezeigt.</span><span class="sxs-lookup"><span data-stu-id="7daee-140">Each page shows the same menu layout.</span></span> <span data-ttu-id="7daee-141">Das Menülayout wird mithilfe der Datei *Pages/Shared/_Layout.cshtml* implementiert.</span><span class="sxs-lookup"><span data-stu-id="7daee-141">The menu layout is implemented in the *Pages/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="7daee-142">Öffnen und untersuchen Sie die Datei *Pages/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="7daee-142">Open and examine the *Pages/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="7daee-143">Durch [Layout](xref:mvc/views/layout)-Vorlagen kann das HTML-Containerlayout:</span><span class="sxs-lookup"><span data-stu-id="7daee-143">[Layout](xref:mvc/views/layout) templates allow the HTML container layout to be:</span></span>

* <span data-ttu-id="7daee-144">An einem Ort angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="7daee-144">Specified in one place.</span></span>
* <span data-ttu-id="7daee-145">Auf mehreren Seiten der Website angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="7daee-145">Applied in multiple pages in the site.</span></span>

<span data-ttu-id="7daee-146">Suchen Sie die Zeile `@RenderBody()`.</span><span class="sxs-lookup"><span data-stu-id="7daee-146">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="7daee-147">`RenderBody` ist ein Platzhalter, bei dem alle seitenspezifischen Ansichten von der Layoutseite *umschlossen* angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="7daee-147">`RenderBody` is a placeholder where all the page-specific views show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="7daee-148">Wenn Sie beispielsweise den Link **Privacy** auswählen, wird die Ansicht *Pages/Privacy.cshtml* in der `RenderBody`-Methode gerendert.</span><span class="sxs-lookup"><span data-stu-id="7daee-148">For example, select the **Privacy** link and the *Pages/Privacy.cshtml* view is rendered inside the `RenderBody` method.</span></span>

<a name="vd"></a>

### <a name="viewdata-and-layout"></a><span data-ttu-id="7daee-149">ViewData und Layout</span><span class="sxs-lookup"><span data-stu-id="7daee-149">ViewData and layout</span></span>

<span data-ttu-id="7daee-150">Sehen Sie sich das folgenden Markup in der Datei *Pages/Movies/Index.cshtml* an:</span><span class="sxs-lookup"><span data-stu-id="7daee-150">Consider the following markup from the *Pages/Movies/Index.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml?range=1-6&highlight=4-999)]

<span data-ttu-id="7daee-151">Das oben markierte Markup ist ein Beispiel vom Übergang von Razor in C#.</span><span class="sxs-lookup"><span data-stu-id="7daee-151">The preceding highlighted markup is an example of Razor transitioning into C#.</span></span> <span data-ttu-id="7daee-152">Die Zeichen `{` und `}` schließen einen Block mit C#-Code ein.</span><span class="sxs-lookup"><span data-stu-id="7daee-152">The `{` and `}` characters enclose a block of C# code.</span></span>

<span data-ttu-id="7daee-153">Die Basisklasse `PageModel` verfügt über eine `ViewData`-Wörterbucheigenschaft, die zum Übergeben von Daten an eine Ansicht verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="7daee-153">The `PageModel` base class contains a `ViewData` dictionary property that can be used to pass data to a View.</span></span> <span data-ttu-id="7daee-154">Mithilfe eines **Schlüssel-Wert**-Musters können dem `ViewData`-Wörterbuch Objekte hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="7daee-154">Objects are added to the `ViewData` dictionary using a \***key value** _ pattern.</span></span> <span data-ttu-id="7daee-155">Im vorherigen Beispiel wurde dem `Title`-Wörterbuch die Eigenschaft `ViewData` hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="7daee-155">In the preceding sample, the `Title` property is added to the `ViewData` dictionary.</span></span>

<span data-ttu-id="7daee-156">Die Eigenschaft `Title` wird in der Datei _Pages/Shared/_Layout.cshtml\* verwendet.</span><span class="sxs-lookup"><span data-stu-id="7daee-156">The `Title` property is used in the _Pages/Shared/_Layout.cshtml\* file.</span></span> <span data-ttu-id="7daee-157">Das folgende Markup zeigt die ersten Zeilen der Datei *Pages/_Layout.cshtml* an.</span><span class="sxs-lookup"><span data-stu-id="7daee-157">The following markup shows the first few lines of the *_Layout.cshtml* file.</span></span>

<!-- We need a snapshot copy of layout because we are changing in the next step. -->

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/NU/_Layout.cshtml?highlight=6)]

<span data-ttu-id="7daee-158">Die Zeile `@*Markup removed for brevity.*@` ist ein Razor-Kommentar.</span><span class="sxs-lookup"><span data-stu-id="7daee-158">The line `@*Markup removed for brevity.*@` is a Razor comment.</span></span> <span data-ttu-id="7daee-159">Im Gegensatz zu HTML-Kommentaren (`<!-- -->`) werden Razor-Kommentare nicht an den Client gesendet.</span><span class="sxs-lookup"><span data-stu-id="7daee-159">Unlike HTML comments `<!-- -->`, Razor comments are not sent to the client.</span></span> <span data-ttu-id="7daee-160">Weitere Informationen finden Sie unter [MDN-Webdokumentation: Erste Schritte mit HTML](https://developer.mozilla.org/docs/Learn/HTML/Introduction_to_HTML/Getting_started#HTML_comments).</span><span class="sxs-lookup"><span data-stu-id="7daee-160">See [MDN web docs: Getting started with HTML](https://developer.mozilla.org/docs/Learn/HTML/Introduction_to_HTML/Getting_started#HTML_comments) for more information.</span></span>

### <a name="update-the-layout"></a><span data-ttu-id="7daee-161">Aktualisieren des Layouts</span><span class="sxs-lookup"><span data-stu-id="7daee-161">Update the layout</span></span>

1. <span data-ttu-id="7daee-162">Ändern Sie das `<title>`-Element in der Datei *Pages/Shared/_Layout.cshtml*, um **Movie** anstelle von **RazorPagesMovie** anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="7daee-162">Change the `<title>` element in the *Pages/Shared/_Layout.cshtml* file to display **Movie** rather than **RazorPagesMovie**.</span></span>

   [!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/Pages/Shared/_Layout.cshtml?range=1-6&highlight=6)]

1. <span data-ttu-id="7daee-163">Suchen Sie in der Datei *Pages/Shared/_Layout.cshtml* das folgende Ankerelement.</span><span class="sxs-lookup"><span data-stu-id="7daee-163">Find the following anchor element in the *Pages/Shared/_Layout.cshtml* file.</span></span>

   ```cshtml
   <a class="navbar-brand" asp-area="" asp-page="/Index">RazorPagesMovie</a>
   ```

1. <span data-ttu-id="7daee-164">Ersetzen Sie das vorhergehende Element durch das folgende Markup.</span><span class="sxs-lookup"><span data-stu-id="7daee-164">Replace the preceding element with the following markup:</span></span>

   ```cshtml
   <a class="navbar-brand" asp-page="/Movies/Index">RpMovie</a>
   ```

   <span data-ttu-id="7daee-165">Das vorangehende Ankerelement ist ein [Tag Helper (Taghilfsprogramm)](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="7daee-165">The preceding anchor element is a [Tag Helper](xref:mvc/views/tag-helpers/intro).</span></span> <span data-ttu-id="7daee-166">In diesem Fall handelt es sich um ein [Anchor Tag Helper (Anchor-Taghilfsprogramm)](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="7daee-166">In this case, it's the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).</span></span> <span data-ttu-id="7daee-167">Das Taghilfsattribut und der -wert `asp-page="/Movies/Index"` erstellen einen Link zur Razor-Seite `/Movies/Index`.</span><span class="sxs-lookup"><span data-stu-id="7daee-167">The `asp-page="/Movies/Index"` Tag Helper attribute and value creates a link to the `/Movies/Index` Razor Page.</span></span> <span data-ttu-id="7daee-168">Das `asp-area`-Attribut ist leer, daher wird der Bereich im Link nicht verwendet.</span><span class="sxs-lookup"><span data-stu-id="7daee-168">The `asp-area` attribute value is empty, so the area isn't used in the link.</span></span> <span data-ttu-id="7daee-169">Weitere Informationen finden Sie unter [Bereiche](xref:mvc/controllers/areas).</span><span class="sxs-lookup"><span data-stu-id="7daee-169">See [Areas](xref:mvc/controllers/areas) for more information.</span></span>

1. <span data-ttu-id="7daee-170">Speichern Sie Ihre Änderungen, und testen Sie die App, indem Sie den Link **RpMovie** auswählen.</span><span class="sxs-lookup"><span data-stu-id="7daee-170">Save the changes and test the app by selecting the **RpMovie** link.</span></span> <span data-ttu-id="7daee-171">Wenn Probleme auftreten, sehen Sie sich die Datei [_Layout.cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Shared/_Layout.cshtml) in GitHub an.</span><span class="sxs-lookup"><span data-stu-id="7daee-171">See the [_Layout.cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Shared/_Layout.cshtml) file in GitHub if you have any problems.</span></span>

1. <span data-ttu-id="7daee-172">Testen Sie die folgenden Links: **Home** (Startseite), **RpMovie** (Razor Pages-Film), **Create** (Erstellen), **Edit** (Bearbeiten) und **Delete** (Löschen).</span><span class="sxs-lookup"><span data-stu-id="7daee-172">Test the **Home**, **RpMovie**, **Create**, **Edit**, and **Delete** links.</span></span> <span data-ttu-id="7daee-173">Jede Seite legt den Titel fest, der in der Browserregisterkarte angezeigt wird. Wenn Sie eine Seite mit einem Lesezeichen versehen, wird dieser Titel für das Lesezeichen verwendet.</span><span class="sxs-lookup"><span data-stu-id="7daee-173">Each page sets the title, which you can see in the browser tab. When you bookmark a page, the title is used for the bookmark.</span></span>

> [!NOTE]
> <span data-ttu-id="7daee-174">Sie können unter Umständen in das Feld `Price` keine Kommas als Dezimaltrennzeichen eingeben.</span><span class="sxs-lookup"><span data-stu-id="7daee-174">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="7daee-175">Zur Unterstützung der [jQuery-Validierung](https://jqueryvalidation.org/) für nicht englische Gebietsschemas, in denen ein Komma („,“) als Dezimaltrennzeichen verwendet wird, und Nicht-US-englische Datums- und Uhrzeitformate müssen Sie Schritte zur Globalisierung der App ausführen.</span><span class="sxs-lookup"><span data-stu-id="7daee-175">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point, and non US-English date formats, you must take steps to globalize the app.</span></span> <span data-ttu-id="7daee-176">In diesem [GitHub-Problem 4076](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) finden Sie Anweisungen zum Hinzufügen von Kommas als Dezimaltrennzeichen.</span><span class="sxs-lookup"><span data-stu-id="7daee-176">See this [GitHub issue 4076](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) for instructions on adding decimal comma.</span></span>

<span data-ttu-id="7daee-177">Die Eigenschaft `Layout` wird in der Datei *Pages/_ViewStart.cshtml* festgelegt:</span><span class="sxs-lookup"><span data-stu-id="7daee-177">The `Layout` property is set in the *Pages/_ViewStart.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/Pages/_ViewStart.cshtml)]

<span data-ttu-id="7daee-178">Das vorhergehende Markup legt die Layoutdatei für alle Razor-Dateien unter dem Ordner *Pages* auf *Pages/Shared/_Layout.cshtml* fest.</span><span class="sxs-lookup"><span data-stu-id="7daee-178">The preceding markup sets the layout file to *Pages/Shared/_Layout.cshtml* for all Razor files under the *Pages* folder.</span></span> <span data-ttu-id="7daee-179">Weitere Informationen finden Sie unter [Layout](xref:razor-pages/index#layout).</span><span class="sxs-lookup"><span data-stu-id="7daee-179">See [Layout](xref:razor-pages/index#layout) for more information.</span></span>

### <a name="the-create-page-model"></a><span data-ttu-id="7daee-180">Das Seitenmodell „Create“</span><span class="sxs-lookup"><span data-stu-id="7daee-180">The Create page model</span></span>

<span data-ttu-id="7daee-181">Überprüfen Sie das Seitenmodell *Pages/Movies/Create.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="7daee-181">Examine the *Pages/Movies/Create.cshtml.cs* page model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippetALL)]

<span data-ttu-id="7daee-182">Die Methode `OnGet` initialisiert einen beliebigen Zustand, der für die Seite erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="7daee-182">The `OnGet` method initializes any state needed for the page.</span></span> <span data-ttu-id="7daee-183">Die Seite „Create“ (Erstellen) verfügt nicht über einen initialisierbaren Zustand. Deshalb wird `Page` zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="7daee-183">The Create page doesn't have any state to initialize, so `Page` is returned.</span></span> <span data-ttu-id="7daee-184">Später in diesem Tutorial wird ein Beispiel für `OnGet` zur Initialisierung des Zustands gezeigt.</span><span class="sxs-lookup"><span data-stu-id="7daee-184">Later in the tutorial, an example of `OnGet` initializing state is shown.</span></span> <span data-ttu-id="7daee-185">Die Methode `Page` erstellt ein `PageResult`-Objekt, das die Seite *Create.cshtml* rendert.</span><span class="sxs-lookup"><span data-stu-id="7daee-185">The `Page` method creates a `PageResult` object that renders the *Create.cshtml* page.</span></span>

<span data-ttu-id="7daee-186">Die Eigenschaft `Movie` verwendet das Attribut [[BindProperty]](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute), um die [Modellbindung](xref:mvc/models/model-binding) zu aktivieren.</span><span class="sxs-lookup"><span data-stu-id="7daee-186">The `Movie` property uses the [[BindProperty]](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) attribute to opt-in to [model binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="7daee-187">Wenn das Formular „Create“ die Formularwerte bereitstellt, bindet die ASP.NET Core-Laufzeit die bereitgestellten Werte an das `Movie`-Modell.</span><span class="sxs-lookup"><span data-stu-id="7daee-187">When the Create form posts the form values, the ASP.NET Core runtime binds the posted values to the `Movie` model.</span></span>

<span data-ttu-id="7daee-188">Die Methode `OnPostAsync` wird ausgeführt, wenn die Seite Formulardaten bereitstellt:</span><span class="sxs-lookup"><span data-stu-id="7daee-188">The `OnPostAsync` method is run when the page posts form data:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippetPost)]

<span data-ttu-id="7daee-189">Wenn es zu Modellfehlern kommt, wird das Formular mit allen bereitgestellten Formulardaten erneut angezeigt.</span><span class="sxs-lookup"><span data-stu-id="7daee-189">If there are any model errors, the form is redisplayed, along with any form data posted.</span></span> <span data-ttu-id="7daee-190">Die meisten Modellfehler können clientseitig abgefangen werden, bevor das Formular bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="7daee-190">Most model errors can be caught on the client-side before the form is posted.</span></span> <span data-ttu-id="7daee-191">Ein Beispiel für einen Modellfehler ist die Bereitstellung eines Werts für das Datumsfeld, der nicht in ein Datum konvertiert werden kann.</span><span class="sxs-lookup"><span data-stu-id="7daee-191">An example of a model error is posting a value for the date field that cannot be converted to a date.</span></span> <span data-ttu-id="7daee-192">Die clientseitige Validierung und die Modellvalidierung werden später in diesem Tutorial erläutert.</span><span class="sxs-lookup"><span data-stu-id="7daee-192">Client-side validation and model validation are discussed later in the tutorial.</span></span>

<span data-ttu-id="7daee-193">Wenn keine Modellfehler vorliegen:</span><span class="sxs-lookup"><span data-stu-id="7daee-193">If there are no model errors:</span></span>

* <span data-ttu-id="7daee-194">Die Daten werden gespeichert.</span><span class="sxs-lookup"><span data-stu-id="7daee-194">The data is saved.</span></span>
* <span data-ttu-id="7daee-195">Der Browser wird zur Seite Index umgeleitet.</span><span class="sxs-lookup"><span data-stu-id="7daee-195">The browser is redirected to the Index page.</span></span>

### <a name="the-create-no-locrazor-page"></a><span data-ttu-id="7daee-196">Razor-Seite „Erstellen“</span><span class="sxs-lookup"><span data-stu-id="7daee-196">The Create Razor Page</span></span>

<span data-ttu-id="7daee-197">Betrachten Sie die Datei *Pages/Movies/Create.cshtml* der Razor-Seite:</span><span class="sxs-lookup"><span data-stu-id="7daee-197">Examine the *Pages/Movies/Create.cshtml* Razor Page file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml)]

# <a name="visual-studio"></a>[<span data-ttu-id="7daee-198">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7daee-198">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="7daee-199">Visual Studio zeigt die folgenden Tags in einer bestimmten Schriftart an, die für Taghilfsprogramme verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="7daee-199">Visual Studio displays the following tags in a distinctive bold font used for Tag Helpers:</span></span>

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

![VS17-Ansicht der Create.cshtml-Seite](page/_static/th3.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="7daee-201">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="7daee-201">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="7daee-202">Die folgenden Taghilfsprogramme werden im vorangehenden Markup angezeigt:</span><span class="sxs-lookup"><span data-stu-id="7daee-202">The following Tag Helpers are shown in the preceding markup:</span></span>

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

---

<span data-ttu-id="7daee-203">Das Element `<form method="post">` ist ein [Form Tag Helper (Hilfsprogramm für Formulartags)](xref:mvc/views/working-with-forms#the-form-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="7daee-203">The `<form method="post">` element is a [Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper).</span></span> <span data-ttu-id="7daee-204">Das Hilfsprogramm für Formulartags enthält automatisch ein [antiforgery token (Antifälschungstoken)](xref:security/anti-request-forgery).</span><span class="sxs-lookup"><span data-stu-id="7daee-204">The Form Tag Helper automatically includes an [antiforgery token](xref:security/anti-request-forgery).</span></span>

<span data-ttu-id="7daee-205">Die Gerüstbau-Engine erstellt Razor-Markup für jedes Feld im Modell (ausgenommen die ID) ähnlich wie folgt:</span><span class="sxs-lookup"><span data-stu-id="7daee-205">The scaffolding engine creates Razor markup for each field in the model, except the ID, similar to the following:</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml?range=15-20)]

<span data-ttu-id="7daee-206">Das [Hilfsprogramm für Validierungstags](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` und `<span asp-validation-for`) zeigt Validierungsfehler.</span><span class="sxs-lookup"><span data-stu-id="7daee-206">The [Validation Tag Helpers](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` and `<span asp-validation-for`) display validation errors.</span></span> <span data-ttu-id="7daee-207">Die Validierung wird an späterer Stelle in dieser Reihe detaillierter beschrieben.</span><span class="sxs-lookup"><span data-stu-id="7daee-207">Validation is covered in more detail later in this series.</span></span>

<span data-ttu-id="7daee-208">Das [Label Tag Helper (Taghilfsprogramm für Label)](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) generiert den Titel des Labels und das `[for]`-Attribut für die Eigenschaft `Title`.</span><span class="sxs-lookup"><span data-stu-id="7daee-208">The [Label Tag Helper](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) generates the label caption and `[for]` attribute for the `Title` property.</span></span>

<span data-ttu-id="7daee-209">Das [Hilfsprogramm für Eingabetags](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control">`) verwendet die Attribute von [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) und generiert HTML-Attribute, die auf der Clientseite für die jQuery-Validierung erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="7daee-209">The [Input Tag Helper](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control">`) uses the [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) attributes and produces HTML attributes needed for jQuery Validation on the client-side.</span></span>

<span data-ttu-id="7daee-210">Weitere Informationen zu Taghilfsprogrammen wie `<form method="post">` finden Sie unter [Taghilfsprogramme in ASP.NET Core](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="7daee-210">For more information on Tag Helpers such as `<form method="post">`, see [Tag Helpers in ASP.NET Core](xref:mvc/views/tag-helpers/intro).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7daee-211">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="7daee-211">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="7daee-212">[Zurück: Hinzufügen eines Modells](xref:tutorials/razor-pages/model)
> [Weiter: Arbeiten mit einer Datenbank](xref:tutorials/razor-pages/sql)</span><span class="sxs-lookup"><span data-stu-id="7daee-212">[Previous: Add a model](xref:tutorials/razor-pages/model)
[Next: Work with a database](xref:tutorials/razor-pages/sql)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="the-create-delete-details-and-edit-pages"></a><span data-ttu-id="7daee-213">Die Seiten „Create“, „Delete“, „Details“ und „Edit“</span><span class="sxs-lookup"><span data-stu-id="7daee-213">The Create, Delete, Details, and Edit pages</span></span>

<span data-ttu-id="7daee-214">Überprüfen Sie das Seitenmodell *Pages/Movies/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="7daee-214">Examine the *Pages/Movies/Index.cshtml.cs* Page Model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml.cs)]

<span data-ttu-id="7daee-215">Razor-Seiten werden von `PageModel` abgeleitet.</span><span class="sxs-lookup"><span data-stu-id="7daee-215">Razor Pages are derived from `PageModel`.</span></span> <span data-ttu-id="7daee-216">Gemäß der Konvention wird die von `PageModel` abgeleitete Klasse `<PageName>Model` genannt.</span><span class="sxs-lookup"><span data-stu-id="7daee-216">By convention, the `PageModel`-derived class is named `<PageName>Model`.</span></span> <span data-ttu-id="7daee-217">Der Konstruktor verwendet [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection), um `RazorPagesMovieContext` zur Seite hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="7daee-217">The constructor uses [dependency injection](xref:fundamentals/dependency-injection) to add the `RazorPagesMovieContext` to the page.</span></span> <span data-ttu-id="7daee-218">Die per Gerüstbau erstellten Seiten folgen diesem Muster.</span><span class="sxs-lookup"><span data-stu-id="7daee-218">The scaffolded pages follow this pattern.</span></span> <span data-ttu-id="7daee-219">Weitere Informationen zum asynchronen Programmieren mit Entity Framework finden Sie unter [Asynchroner Code](xref:data/ef-rp/intro#asynchronous-code).</span><span class="sxs-lookup"><span data-stu-id="7daee-219">See [Asynchronous code](xref:data/ef-rp/intro#asynchronous-code) for more information on asynchronous programming with Entity Framework.</span></span>

<span data-ttu-id="7daee-220">Wenn eine Anforderung an die Seite erfolgt, gibt die Methode `OnGetAsync` eine Filmliste an die Razor-Seite zurück.</span><span class="sxs-lookup"><span data-stu-id="7daee-220">When a request is made for the page, the `OnGetAsync` method returns a list of movies to the Razor Page.</span></span> <span data-ttu-id="7daee-221">`OnGetAsync` oder `OnGet` werden für eine Razor-Seite aufgerufen, um den Zustand der Seite zu initialisieren.</span><span class="sxs-lookup"><span data-stu-id="7daee-221">`OnGetAsync` or `OnGet` is called on a Razor Page to initialize the state for the page.</span></span> <span data-ttu-id="7daee-222">In diesem Fall ruft `OnGetAsync` eine Liste von Filmen ab und zeigt diese an.</span><span class="sxs-lookup"><span data-stu-id="7daee-222">In this case, `OnGetAsync` gets a list of movies and displays them.</span></span>

<span data-ttu-id="7daee-223">Wenn `void` von `OnGet` oder `Task` von `OnGetAsync` zurückgegeben wird, wird keine Rückgabemethode verwendet.</span><span class="sxs-lookup"><span data-stu-id="7daee-223">When `OnGet` returns `void` or `OnGetAsync` returns `Task`, no return method is used.</span></span> <span data-ttu-id="7daee-224">Wenn der Rückgabetyp `IActionResult` oder `Task<IActionResult>` ist, muss eine Rückgabeanweisung bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="7daee-224">When the return type is `IActionResult` or `Task<IActionResult>`, a return statement must be provided.</span></span> <span data-ttu-id="7daee-225">Dies kann z. B. die `OnPostAsync`-Methode *Pages/Movies/Create.cshtml.cs* sein:</span><span class="sxs-lookup"><span data-stu-id="7daee-225">For example, the *Pages/Movies/Create.cshtml.cs* `OnPostAsync` method:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml.cs?name=snippet)]

<a name="index"></a> <span data-ttu-id="7daee-226">Sehen Sie sich die Razor-Seite *Pages/Movies/Index.cshtml* an:</span><span class="sxs-lookup"><span data-stu-id="7daee-226">Examine the *Pages/Movies/Index.cshtml* Razor Page:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml)]

<span data-ttu-id="7daee-227">Razor kann aus HTML in C# oder ein Razor-spezifisches Markup übergehen.</span><span class="sxs-lookup"><span data-stu-id="7daee-227">Razor can transition from HTML into C# or into Razor-specific markup.</span></span> <span data-ttu-id="7daee-228">Wenn auf ein `@`-Symbol ein für [Razor reserviertes Schlüsselwort](xref:mvc/views/razor#razor-reserved-keywords) folgt, wechselt es in ein Razor-spezifisches Markup. Andernfalls geht es in C# über.</span><span class="sxs-lookup"><span data-stu-id="7daee-228">When an `@` symbol is followed by a [Razor reserved keyword](xref:mvc/views/razor#razor-reserved-keywords), it transitions into Razor-specific markup, otherwise it transitions into C#.</span></span>

<span data-ttu-id="7daee-229">Die Razor-Anweisung `@page` wandelt die Datei in eine MVC-Aktion um. Das bedeutet, dass sie Anforderungen verarbeiten kann.</span><span class="sxs-lookup"><span data-stu-id="7daee-229">The `@page` Razor directive makes the file into an MVC action, which means that it can handle requests.</span></span> <span data-ttu-id="7daee-230">`@page` muss die erste Razor-Anweisung auf einer Seite sein.</span><span class="sxs-lookup"><span data-stu-id="7daee-230">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="7daee-231">`@page` ist ein Beispiel für einen Übergang in ein Razor-spezifisches Markup.</span><span class="sxs-lookup"><span data-stu-id="7daee-231">`@page` is an example of transitioning into Razor-specific markup.</span></span> <span data-ttu-id="7daee-232">Unter [Razor-Syntax](xref:mvc/views/razor#razor-syntax) finden Sie weitere Informationen.</span><span class="sxs-lookup"><span data-stu-id="7daee-232">See [Razor syntax](xref:mvc/views/razor#razor-syntax) for more information.</span></span>

<a name="md"></a>

### <a name="the-model-directive"></a><span data-ttu-id="7daee-233">Die @model -Direktive</span><span class="sxs-lookup"><span data-stu-id="7daee-233">The @model directive</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?range=1-2&highlight=2)]

<span data-ttu-id="7daee-234">Die `@model`-Anweisung gibt den Typ des Modells an, das an die Razor-Seite weitergegeben wird.</span><span class="sxs-lookup"><span data-stu-id="7daee-234">The `@model` directive specifies the type of the model passed to the Razor Page.</span></span> <span data-ttu-id="7daee-235">Im vorherigen Beispiel macht die `@model`-Linie die von `PageModel` abgeleitete Klasse der Razor-Seite verfügbar.</span><span class="sxs-lookup"><span data-stu-id="7daee-235">In the preceding example, the `@model` line makes the `PageModel`-derived class available to the Razor Page.</span></span> <span data-ttu-id="7daee-236">Das Modell wird in den [HTML-Hilfsprogrammen](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) `@Html.DisplayNameFor` und `@Html.DisplayFor` auf der Seite verwendet.</span><span class="sxs-lookup"><span data-stu-id="7daee-236">The model is used in the `@Html.DisplayNameFor` and `@Html.DisplayFor` [HTML Helpers](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) on the page.</span></span>

### <a name="html-helpers"></a><span data-ttu-id="7daee-237">HTML-Hilfsprogramme</span><span class="sxs-lookup"><span data-stu-id="7daee-237">HTML Helpers</span></span>

<span data-ttu-id="7daee-238">Überprüfen Sie den Lambdaausdruck, der im folgenden HTML-Hilfsprogramm verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="7daee-238">Examine the lambda expression used in the following HTML Helper:</span></span>

```cshtml
@Html.DisplayNameFor(model => model.Movie[0].Title)
```

<span data-ttu-id="7daee-239">Das HTML-Hilfsprogramm <xref:System.Web.Mvc.Html.DisplayNameExtensions.DisplayNameFor%2A?displayProperty=nameWithType> überprüft die Eigenschaft `Title`, auf die im Lambdaausdruck verwiesen wird, um den Anzeigenamen zu bestimmen.</span><span class="sxs-lookup"><span data-stu-id="7daee-239">The <xref:System.Web.Mvc.Html.DisplayNameExtensions.DisplayNameFor%2A?displayProperty=nameWithType> HTML Helper inspects the `Title` property referenced in the lambda expression to determine the display name.</span></span> <span data-ttu-id="7daee-240">Der Lambdaausdruck wird überprüft und nicht ausgewertet.</span><span class="sxs-lookup"><span data-stu-id="7daee-240">The lambda expression is inspected rather than evaluated.</span></span> <span data-ttu-id="7daee-241">Das bedeutet, dass keine Zugriffsverletzung auftritt, wenn `model`, `model.Movie` oder `model.Movie[0]``null` oder leer sind.</span><span class="sxs-lookup"><span data-stu-id="7daee-241">That means there is no access violation when `model`, `model.Movie`, or `model.Movie[0]` are `null` or empty.</span></span> <span data-ttu-id="7daee-242">Wenn der Lambdaausdruck ausgewertet wird, (z. B. mit `@Html.DisplayFor(modelItem => item.Title)`), werden die Eigenschaftswerte ausgewertet.</span><span class="sxs-lookup"><span data-stu-id="7daee-242">When the lambda expression is evaluated, for example, with `@Html.DisplayFor(modelItem => item.Title)`, the model's property values are evaluated.</span></span>
### <a name="the-layout-page"></a><span data-ttu-id="7daee-243">Die Seite „Layout“</span><span class="sxs-lookup"><span data-stu-id="7daee-243">The layout page</span></span>

<span data-ttu-id="7daee-244">Wählen Sie die Menülinks aus ( **RazorPagesMovie**, **Home** und **Datenschutz**) aus.</span><span class="sxs-lookup"><span data-stu-id="7daee-244">Select the menu links **RazorPagesMovie**, **Home**, and **Privacy**.</span></span> <span data-ttu-id="7daee-245">Auf jeder Seite wird dasselbe Menülayout gezeigt.</span><span class="sxs-lookup"><span data-stu-id="7daee-245">Each page shows the same menu layout.</span></span> <span data-ttu-id="7daee-246">Das Menülayout wird mithilfe der Datei *Pages/Shared/_Layout.cshtml* implementiert.</span><span class="sxs-lookup"><span data-stu-id="7daee-246">The menu layout is implemented in the *Pages/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="7daee-247">Öffnen und untersuchen Sie die Datei *Pages/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="7daee-247">Open and examine the *Pages/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="7daee-248">[Layout](xref:mvc/views/layout)-Vorlagen ermöglichen Ihnen, das HTML-Containerlayout einer Website zentral anzugeben und dann auf mehrere Seiten der Website anzuwenden.</span><span class="sxs-lookup"><span data-stu-id="7daee-248">[Layout](xref:mvc/views/layout) templates allow you to specify the HTML container layout of a site in one place and then apply it across multiple pages in the site.</span></span> <span data-ttu-id="7daee-249">Suchen Sie die Zeile `@RenderBody()`.</span><span class="sxs-lookup"><span data-stu-id="7daee-249">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="7daee-250">`RenderBody` ist ein Platzhalter, bei dem alle seitenspezifischen Ansichten, die Sie erstellen, von der Layoutseite *umschlossen* angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="7daee-250">`RenderBody` is a placeholder where all the page-specific views you create show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="7daee-251">Wenn Sie beispielsweise den Link **Privacy** auswählen, wird die Ansicht **Pages/Privacy.cshtml** in der `RenderBody`-Methode gerendert.</span><span class="sxs-lookup"><span data-stu-id="7daee-251">For example, if you select the **Privacy** link, the **Pages/Privacy.cshtml** view is rendered inside the `RenderBody` method.</span></span>

<a name="vd"></a>

### <a name="viewdata-and-layout"></a><span data-ttu-id="7daee-252">ViewData und Layout</span><span class="sxs-lookup"><span data-stu-id="7daee-252">ViewData and layout</span></span>

<span data-ttu-id="7daee-253">Sehen Sie sich den folgenden Code aus der Datei *Pages/Movies/Index.cshtml* an:</span><span class="sxs-lookup"><span data-stu-id="7daee-253">Consider the following code from the *Pages/Movies/Index.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?range=1-6&highlight=4-999)]

<span data-ttu-id="7daee-254">Der oben markierte Code ist ein Beispiel vom Übergang von Razor in C#.</span><span class="sxs-lookup"><span data-stu-id="7daee-254">The preceding highlighted code is an example of Razor transitioning into C#.</span></span> <span data-ttu-id="7daee-255">Die Zeichen `{` und `}` schließen einen Block mit C#-Code ein.</span><span class="sxs-lookup"><span data-stu-id="7daee-255">The `{` and `}` characters enclose a block of C# code.</span></span>

<span data-ttu-id="7daee-256">Die Basisklasse `PageModel` verfügt über eine `ViewData`-Wörterbucheigenschaft, die zum Hinzufügen von Daten verwendet werden kann, die an eine Ansicht übergeben werden sollen.</span><span class="sxs-lookup"><span data-stu-id="7daee-256">The `PageModel` base class has a `ViewData` dictionary property that can be used to add data that you want to pass to a View.</span></span> <span data-ttu-id="7daee-257">Mithilfe eines Schlüssel-Wert-Musters können dem `ViewData`-Wörterbuch Objekte hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="7daee-257">You add objects into the `ViewData` dictionary using a key/value pattern.</span></span> <span data-ttu-id="7daee-258">Im vorherigen Beispiel wurde dem `Title`-Wörterbuch die Eigenschaft `ViewData` hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="7daee-258">In the preceding sample, the `Title` property is added to the `ViewData` dictionary.</span></span>

<span data-ttu-id="7daee-259">Die Eigenschaft `Title` wird in der Datei *Pages/Shared/_Layout.cshtml* verwendet.</span><span class="sxs-lookup"><span data-stu-id="7daee-259">The `Title` property is used in the *Pages/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="7daee-260">Das folgende Markup zeigt die ersten Zeilen der Datei *Pages/_Layout.cshtml* an.</span><span class="sxs-lookup"><span data-stu-id="7daee-260">The following markup shows the first few lines of the *_Layout.cshtml* file.</span></span>

<!-- We need a snapshot copy of layout because we are changing in the next step. -->

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/NU/_Layout.cshtml?highlight=6-99)]

<span data-ttu-id="7daee-261">Die Zeile `@*Markup removed for brevity.*@` ist ein Razor-Kommentar.</span><span class="sxs-lookup"><span data-stu-id="7daee-261">The line `@*Markup removed for brevity.*@` is a Razor comment.</span></span> <span data-ttu-id="7daee-262">Im Gegensatz zu HTML-Kommentaren (`<!-- -->`) werden Razor-Kommentare nicht an den Client gesendet.</span><span class="sxs-lookup"><span data-stu-id="7daee-262">Unlike HTML comments `<!-- -->`, Razor comments are not sent to the client.</span></span> <span data-ttu-id="7daee-263">Weitere Informationen finden Sie unter [MDN-Webdokumentation: Erste Schritte mit HTML](https://developer.mozilla.org/docs/Learn/HTML/Introduction_to_HTML/Getting_started#HTML_comments).</span><span class="sxs-lookup"><span data-stu-id="7daee-263">See [MDN web docs: Getting started with HTML](https://developer.mozilla.org/docs/Learn/HTML/Introduction_to_HTML/Getting_started#HTML_comments) for more information.</span></span>

### <a name="update-the-layout"></a><span data-ttu-id="7daee-264">Aktualisieren des Layouts</span><span class="sxs-lookup"><span data-stu-id="7daee-264">Update the layout</span></span>

<span data-ttu-id="7daee-265">Ändern Sie das `<title>`-Element in der Datei *Pages/Shared/_Layout.cshtml*, um **Movie** anstelle von **RazorPagesMovie** anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="7daee-265">Change the `<title>` element in the *Pages/Shared/_Layout.cshtml* file to display **Movie** rather than **RazorPagesMovie**.</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Shared/_Layout.cshtml?range=1-6&highlight=6)]

<span data-ttu-id="7daee-266">Suchen Sie in der Datei *Pages/Shared/_Layout.cshtml* das folgende Ankerelement.</span><span class="sxs-lookup"><span data-stu-id="7daee-266">Find the following anchor element in the *Pages/Shared/_Layout.cshtml* file.</span></span>

```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Index">RazorPagesMovie</a>
```

<span data-ttu-id="7daee-267">Ersetzen Sie das vorhergehende Element durch das folgende Markup.</span><span class="sxs-lookup"><span data-stu-id="7daee-267">Replace the preceding element with the following markup.</span></span>

```cshtml
<a class="navbar-brand" asp-page="/Movies/Index">RpMovie</a>
```

<span data-ttu-id="7daee-268">Das vorangehende Ankerelement ist ein [Tag Helper (Taghilfsprogramm)](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="7daee-268">The preceding anchor element is a [Tag Helper](xref:mvc/views/tag-helpers/intro).</span></span> <span data-ttu-id="7daee-269">In diesem Fall handelt es sich um ein [Anchor Tag Helper (Anchor-Taghilfsprogramm)](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="7daee-269">In this case, it's the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).</span></span> <span data-ttu-id="7daee-270">Das Taghilfsattribut und der -wert `asp-page="/Movies/Index"` erstellen einen Link zur Razor-Seite `/Movies/Index`.</span><span class="sxs-lookup"><span data-stu-id="7daee-270">The `asp-page="/Movies/Index"` Tag Helper attribute and value creates a link to the `/Movies/Index` Razor Page.</span></span> <span data-ttu-id="7daee-271">Das `asp-area`-Attribut ist leer, daher wird der Bereich im Link nicht verwendet.</span><span class="sxs-lookup"><span data-stu-id="7daee-271">The `asp-area` attribute value is empty, so the area isn't used in the link.</span></span> <span data-ttu-id="7daee-272">Weitere Informationen finden Sie unter [Bereiche](xref:mvc/controllers/areas).</span><span class="sxs-lookup"><span data-stu-id="7daee-272">See [Areas](xref:mvc/controllers/areas) for more information.</span></span>

<span data-ttu-id="7daee-273">Speichern Sie Ihre Änderungen, und testen Sie die App, indem Sie auf den Link **RpMovie** klicken.</span><span class="sxs-lookup"><span data-stu-id="7daee-273">Save your changes, and test the app by clicking on the **RpMovie** link.</span></span> <span data-ttu-id="7daee-274">Wenn Probleme auftreten, sehen Sie sich die Datei [_Layout.cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Shared/_Layout.cshtml) in GitHub an.</span><span class="sxs-lookup"><span data-stu-id="7daee-274">See the [_Layout.cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Shared/_Layout.cshtml) file in GitHub if you have any problems.</span></span>

<span data-ttu-id="7daee-275">Testen Sie die anderen Links – **Home** (Startseite), **RpMovie** (Razor Pages-Film), **Create** (Erstellen), **Edit** (Bearbeiten) und **Delete** (Löschen).</span><span class="sxs-lookup"><span data-stu-id="7daee-275">Test the other links (**Home**, **RpMovie**, **Create**, **Edit**, and **Delete**).</span></span> <span data-ttu-id="7daee-276">Jede Seite legt den Titel fest, der in der Browserregisterkarte angezeigt wird. Wenn Sie eine Seite mit einem Lesezeichen versehen, wird dieser Titel für das Lesezeichen verwendet.</span><span class="sxs-lookup"><span data-stu-id="7daee-276">Each page sets the title, which you can see in the browser tab. When you bookmark a page, the title is used for the bookmark.</span></span>

> [!NOTE]
> <span data-ttu-id="7daee-277">Sie können unter Umständen in das Feld `Price` keine Kommas als Dezimaltrennzeichen eingeben.</span><span class="sxs-lookup"><span data-stu-id="7daee-277">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="7daee-278">Zur Unterstützung der [jQuery-Validierung](https://jqueryvalidation.org/) für nicht englische Gebietsschemas, in denen ein Komma („,“) als Dezimaltrennzeichen verwendet wird, und Nicht-US-englische Datums- und Uhrzeitformate müssen Sie Schritte zur Globalisierung der App ausführen.</span><span class="sxs-lookup"><span data-stu-id="7daee-278">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point, and non US-English date formats, you must take steps to globalize the app.</span></span> <span data-ttu-id="7daee-279">In diesem [GitHub-Problem 4076](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) finden Sie Anweisungen zum Hinzufügen von Kommas als Dezimaltrennzeichen.</span><span class="sxs-lookup"><span data-stu-id="7daee-279">This [GitHub issue 4076](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) for instructions on adding decimal comma.</span></span>

<span data-ttu-id="7daee-280">Die Eigenschaft `Layout` wird in der Datei *Pages/_ViewStart.cshtml* festgelegt:</span><span class="sxs-lookup"><span data-stu-id="7daee-280">The `Layout` property is set in the *Pages/_ViewStart.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/_ViewStart.cshtml)]

<span data-ttu-id="7daee-281">Das vorhergehende Markup legt die Layoutdatei für alle Razor-Dateien unter dem Ordner *Pages* auf *Pages/Shared/_Layout.cshtml* fest.</span><span class="sxs-lookup"><span data-stu-id="7daee-281">The preceding markup sets the layout file to *Pages/Shared/_Layout.cshtml* for all Razor files under the *Pages* folder.</span></span> <span data-ttu-id="7daee-282">Weitere Informationen finden Sie unter [Layout](xref:razor-pages/index#layout).</span><span class="sxs-lookup"><span data-stu-id="7daee-282">See [Layout](xref:razor-pages/index#layout) for more information.</span></span>

### <a name="the-create-page-model"></a><span data-ttu-id="7daee-283">Das Seitenmodell „Create“</span><span class="sxs-lookup"><span data-stu-id="7daee-283">The Create page model</span></span>

<span data-ttu-id="7daee-284">Überprüfen Sie das Seitenmodell *Pages/Movies/Create.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="7daee-284">Examine the *Pages/Movies/Create.cshtml.cs* page model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml.cs?name=snippetALL)]

<span data-ttu-id="7daee-285">Die Methode `OnGet` initialisiert einen beliebigen Zustand, der für die Seite erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="7daee-285">The `OnGet` method initializes any state needed for the page.</span></span> <span data-ttu-id="7daee-286">Die Seite „Create“ (Erstellen) verfügt nicht über einen initialisierbaren Zustand. Deshalb wird `Page` zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="7daee-286">The Create page doesn't have any state to initialize, so `Page` is returned.</span></span> <span data-ttu-id="7daee-287">Im späteren Verlauf des Tutorials initialisiert die Methode `OnGet` einen Zustand.</span><span class="sxs-lookup"><span data-stu-id="7daee-287">Later in the tutorial you see `OnGet` method initialize state.</span></span> <span data-ttu-id="7daee-288">Die Methode `Page` erstellt ein `PageResult`-Objekt, das die Seite *Create.cshtml* rendert.</span><span class="sxs-lookup"><span data-stu-id="7daee-288">The `Page` method creates a `PageResult` object that renders the *Create.cshtml* page.</span></span>

<span data-ttu-id="7daee-289">Die Eigenschaft `Movie` verwendet das Attribut [[BindProperty]<xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute>, um die [Modellbindung](xref:mvc/models/model-binding) zu aktivieren.</span><span class="sxs-lookup"><span data-stu-id="7daee-289">The `Movie` property uses the [[BindProperty]]<xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute> attribute to opt-in to [model binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="7daee-290">Wenn das Formular „Create“ die Formularwerte bereitstellt, bindet die ASP.NET Core-Laufzeit die bereitgestellten Werte an das `Movie`-Modell.</span><span class="sxs-lookup"><span data-stu-id="7daee-290">When the Create form posts the form values, the ASP.NET Core runtime binds the posted values to the `Movie` model.</span></span>

<span data-ttu-id="7daee-291">Die Methode `OnPostAsync` wird ausgeführt, wenn die Seite Formulardaten bereitstellt:</span><span class="sxs-lookup"><span data-stu-id="7daee-291">The `OnPostAsync` method is run when the page posts form data:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml.cs?name=snippetPost)]

<span data-ttu-id="7daee-292">Wenn es zu Modellfehlern kommt, wird das Formular mit allen bereitgestellten Formulardaten erneut angezeigt.</span><span class="sxs-lookup"><span data-stu-id="7daee-292">If there are any model errors, the form is redisplayed, along with any form data posted.</span></span> <span data-ttu-id="7daee-293">Die meisten Modellfehler können clientseitig abgefangen werden, bevor das Formular bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="7daee-293">Most model errors can be caught on the client-side before the form is posted.</span></span> <span data-ttu-id="7daee-294">Ein Beispiel für einen Modellfehler ist die Bereitstellung eines Werts für das Datumsfeld, der nicht in ein Datum konvertiert werden kann.</span><span class="sxs-lookup"><span data-stu-id="7daee-294">An example of a model error is posting a value for the date field that cannot be converted to a date.</span></span> <span data-ttu-id="7daee-295">Die clientseitige Validierung und die Modellvalidierung werden später in diesem Tutorial erläutert.</span><span class="sxs-lookup"><span data-stu-id="7daee-295">Client-side validation and model validation are discussed later in the tutorial.</span></span>

<span data-ttu-id="7daee-296">Wenn keine Modellfehler auftreten, werden die Daten gespeichert und der Browser zur Seite Index umgeleitet.</span><span class="sxs-lookup"><span data-stu-id="7daee-296">If there are no model errors, the data is saved, and the browser is redirected to the Index page.</span></span>

### <a name="the-create-no-locrazor-page"></a><span data-ttu-id="7daee-297">Razor-Seite „Erstellen“</span><span class="sxs-lookup"><span data-stu-id="7daee-297">The Create Razor Page</span></span>

<span data-ttu-id="7daee-298">Betrachten Sie die Datei *Pages/Movies/Create.cshtml* der Razor-Seite:</span><span class="sxs-lookup"><span data-stu-id="7daee-298">Examine the *Pages/Movies/Create.cshtml* Razor Page file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml)]

# <a name="visual-studio"></a>[<span data-ttu-id="7daee-299">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7daee-299">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="7daee-300">Visual Studio zeigt das `<form method="post">`-Tag in einer bestimmten Schriftart an, die für Taghilfsprogramme verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="7daee-300">Visual Studio displays the `<form method="post">` tag in a distinctive bold font used for Tag Helpers:</span></span>

![VS17-Ansicht der Create.cshtml-Seite](page/_static/th.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="7daee-302">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7daee-302">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="7daee-303">Weitere Informationen zu Taghilfsprogrammen wie `<form method="post">` finden Sie unter [Taghilfsprogramme in ASP.NET Core](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="7daee-303">For more information on Tag Helpers such as `<form method="post">`, see [Tag Helpers in ASP.NET Core](xref:mvc/views/tag-helpers/intro).</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="7daee-304">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="7daee-304">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="7daee-305">Visual Studio für Mac zeigt das `<form method="post">`-Tag in einer bestimmten Schriftart an, die für Taghilfsprogramme verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="7daee-305">Visual Studio for Mac displays the `<form method="post">` tag in a distinctive bold font used for Tag Helpers.</span></span>

---

<span data-ttu-id="7daee-306">Das Element `<form method="post">` ist ein [Form Tag Helper (Hilfsprogramm für Formulartags)](xref:mvc/views/working-with-forms#the-form-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="7daee-306">The `<form method="post">` element is a [Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper).</span></span> <span data-ttu-id="7daee-307">Das Hilfsprogramm für Formulartags enthält automatisch ein [antiforgery token (Antifälschungstoken)](xref:security/anti-request-forgery).</span><span class="sxs-lookup"><span data-stu-id="7daee-307">The Form Tag Helper automatically includes an [antiforgery token](xref:security/anti-request-forgery).</span></span>

<span data-ttu-id="7daee-308">Die Gerüstbau-Engine erstellt Razor-Markup für jedes Feld im Modell (ausgenommen die ID) ähnlich wie folgt:</span><span class="sxs-lookup"><span data-stu-id="7daee-308">The scaffolding engine creates Razor markup for each field in the model, except the ID, similar to the following:</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml?range=15-20)]

<span data-ttu-id="7daee-309">Das [Hilfsprogramm für Validierungstags](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` und `<span asp-validation-for`) zeigt Validierungsfehler.</span><span class="sxs-lookup"><span data-stu-id="7daee-309">The [Validation Tag Helpers](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` and `<span asp-validation-for`) display validation errors.</span></span> <span data-ttu-id="7daee-310">Die Validierung wird an späterer Stelle in dieser Reihe detaillierter beschrieben.</span><span class="sxs-lookup"><span data-stu-id="7daee-310">Validation is covered in more detail later in this series.</span></span>

<span data-ttu-id="7daee-311">Das [Label Tag Helper (Taghilfsprogramm für Label)](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) generiert den Titel des Labels und das `[for]`-Attribut für die Eigenschaft `Title`.</span><span class="sxs-lookup"><span data-stu-id="7daee-311">The [Label Tag Helper](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) generates the label caption and `[for]` attribute for the `Title` property.</span></span>

<span data-ttu-id="7daee-312">Das [Hilfsprogramm für Eingabetags](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control">`) verwendet die Attribute von [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) und generiert HTML-Attribute, die auf der Clientseite für die jQuery-Validierung erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="7daee-312">The [Input Tag Helper](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control">`) uses the [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) attributes and produces HTML attributes needed for jQuery Validation on the client-side.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7daee-313">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="7daee-313">Additional resources</span></span>

* [<span data-ttu-id="7daee-314">Dieses Tutorial auf YouTube</span><span class="sxs-lookup"><span data-stu-id="7daee-314">YouTube version of this tutorial</span></span>](https://youtu.be/zxgKjPYnOMM)

> [!div class="step-by-step"]
> <span data-ttu-id="7daee-315">[Zurück: Hinzufügen eines Modells](xref:tutorials/razor-pages/model)
> [Weiter: Arbeiten mit einer Datenbank](xref:tutorials/razor-pages/sql)</span><span class="sxs-lookup"><span data-stu-id="7daee-315">[Previous: Add a model](xref:tutorials/razor-pages/model)
[Next: Work with a database](xref:tutorials/razor-pages/sql)</span></span>

::: moniker-end
