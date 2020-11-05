---
title: 'Part 3: Gerüstbau mit :::no-loc(Razor)::: Pages in ASP.NET Core'
author: rick-anderson
description: 'Dies ist Teil 3 der Tutorialreihe zu :::no-loc(Razor)::: Pages.'
ms.author: riande
ms.date: 08/17/2019
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: tutorials/razor-pages/page
ms.openlocfilehash: 7d0085e1d444de30ca124ef544668122ab350c93
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060052"
---
# <a name="part-3-scaffolded-no-locrazor-pages-in-aspnet-core"></a><span data-ttu-id="03aff-103">Part 3: Gerüstbau mit :::no-loc(Razor)::: Pages in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="03aff-103">Part 3, scaffolded :::no-loc(Razor)::: Pages in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="03aff-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="03aff-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="03aff-105">In diesem Tutorial werden die :::no-loc(Razor)::: Pages näher untersucht, die durch den Gerüstbau im [vorherigen Tutorial](xref:tutorials/razor-pages/model) erstellt wurden.</span><span class="sxs-lookup"><span data-stu-id="03aff-105">This tutorial examines the :::no-loc(Razor)::: Pages created by scaffolding in the [previous tutorial](xref:tutorials/razor-pages/model).</span></span>

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

## <a name="the-create-delete-details-and-edit-pages"></a><span data-ttu-id="03aff-106">Die Seiten „Create“, „Delete“, „Details“ und „Edit“</span><span class="sxs-lookup"><span data-stu-id="03aff-106">The Create, Delete, Details, and Edit pages</span></span>

<span data-ttu-id="03aff-107">Sehen Sie sich das Seitenmodell *Pages/Movies/Index.cshtml.cs* an:</span><span class="sxs-lookup"><span data-stu-id="03aff-107">Examine the *Pages/Movies/Index.cshtml.cs* Page Model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample3/:::no-loc(Razor):::PagesMovie30/Pages/Movies/Index.cshtml.cs)]

<span data-ttu-id="03aff-108">:::no-loc(Razor):::-Seiten werden von `PageModel` abgeleitet.</span><span class="sxs-lookup"><span data-stu-id="03aff-108">:::no-loc(Razor)::: Pages are derived from `PageModel`.</span></span> <span data-ttu-id="03aff-109">Gemäß der Konvention wird die von `PageModel` abgeleitete Klasse `<PageName>Model` genannt.</span><span class="sxs-lookup"><span data-stu-id="03aff-109">By convention, the `PageModel`-derived class is called `<PageName>Model`.</span></span> <span data-ttu-id="03aff-110">Der Konstruktor verwendet [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection), um `:::no-loc(Razor):::PagesMovieContext` zur Seite hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="03aff-110">The constructor uses [dependency injection](xref:fundamentals/dependency-injection) to add the `:::no-loc(Razor):::PagesMovieContext` to the page.</span></span> <span data-ttu-id="03aff-111">Alle per Gerüstbau erstellten Seiten folgen diesem Muster.</span><span class="sxs-lookup"><span data-stu-id="03aff-111">All the scaffolded pages follow this pattern.</span></span> <span data-ttu-id="03aff-112">Weitere Informationen zum asynchronen Programmieren mit Entity Framework finden Sie unter [Asynchroner Code](xref:data/ef-rp/intro#asynchronous-code).</span><span class="sxs-lookup"><span data-stu-id="03aff-112">See [Asynchronous code](xref:data/ef-rp/intro#asynchronous-code) for more information on asynchronous programming with Entity Framework.</span></span>

<span data-ttu-id="03aff-113">Wenn eine Anforderung an die Seite erfolgt, gibt die Methode `OnGetAsync` eine Filmliste an die :::no-loc(Razor):::-Seite zurück.</span><span class="sxs-lookup"><span data-stu-id="03aff-113">When a request is made for the page, the `OnGetAsync` method returns a list of movies to the :::no-loc(Razor)::: Page.</span></span> <span data-ttu-id="03aff-114">`OnGetAsync` oder `OnGet` wird aufgerufen, um den Status der Seite zu initialisieren.</span><span class="sxs-lookup"><span data-stu-id="03aff-114">`OnGetAsync` or `OnGet` is called to initialize the state of the page.</span></span> <span data-ttu-id="03aff-115">In diesem Fall ruft `OnGetAsync` eine Liste von Filmen ab und zeigt diese an.</span><span class="sxs-lookup"><span data-stu-id="03aff-115">In this case, `OnGetAsync` gets a list of movies and displays them.</span></span>

<span data-ttu-id="03aff-116">Wenn `OnGet``void` oder `OnGetAsync``Task` zurückgibt, wird keine Rückgabeanweisung verwendet.</span><span class="sxs-lookup"><span data-stu-id="03aff-116">When `OnGet` returns `void` or `OnGetAsync` returns`Task`, no return statement is used.</span></span> <span data-ttu-id="03aff-117">Wenn der Rückgabetyp `IActionResult` oder `Task<IActionResult>` ist, muss eine Rückgabeanweisung bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="03aff-117">When the return type is `IActionResult` or `Task<IActionResult>`, a return statement must be provided.</span></span> <span data-ttu-id="03aff-118">Dies kann z. B. die `OnPostAsync`-Methode *Pages/Movies/Create.cshtml.cs* sein:</span><span class="sxs-lookup"><span data-stu-id="03aff-118">For example, the *Pages/Movies/Create.cshtml.cs* `OnPostAsync` method:</span></span>

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippet)]

<a name="index"></a> <span data-ttu-id="03aff-119">Sehen Sie sich die :::no-loc(Razor):::-Seite *Pages/Movies/Index.cshtml* an:</span><span class="sxs-lookup"><span data-stu-id="03aff-119">Examine the *Pages/Movies/Index.cshtml* :::no-loc(Razor)::: Page:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/:::no-loc(Razor):::PagesMovie30/Pages/Movies/Index.cshtml)]

<span data-ttu-id="03aff-120">:::no-loc(Razor)::: kann aus HTML in C# oder ein :::no-loc(Razor):::-spezifisches Markup übergehen.</span><span class="sxs-lookup"><span data-stu-id="03aff-120">:::no-loc(Razor)::: can transition from HTML into C# or into :::no-loc(Razor):::-specific markup.</span></span> <span data-ttu-id="03aff-121">Wenn auf ein `@`-Symbol ein für [:::no-loc(Razor)::: reserviertes Schlüsselwort](xref:mvc/views/razor#razor-reserved-keywords) folgt, wechselt es in ein :::no-loc(Razor):::-spezifisches Markup. Andernfalls geht es in C# über.</span><span class="sxs-lookup"><span data-stu-id="03aff-121">When an `@` symbol is followed by a [:::no-loc(Razor)::: reserved keyword](xref:mvc/views/razor#razor-reserved-keywords), it transitions into :::no-loc(Razor):::-specific markup, otherwise it transitions into C#.</span></span>

### <a name="the-page-directive"></a><span data-ttu-id="03aff-122">Die @page -Direktive</span><span class="sxs-lookup"><span data-stu-id="03aff-122">The @page directive</span></span>

<span data-ttu-id="03aff-123">Die :::no-loc(Razor):::-Anweisung `@page` wandelt die Datei in eine MVC-Aktion um. Das bedeutet, dass sie Anforderungen verarbeiten kann.</span><span class="sxs-lookup"><span data-stu-id="03aff-123">The `@page` :::no-loc(Razor)::: directive makes the file an MVC action, which means that it can handle requests.</span></span> <span data-ttu-id="03aff-124">`@page` muss die erste :::no-loc(Razor):::-Anweisung auf einer Seite sein.</span><span class="sxs-lookup"><span data-stu-id="03aff-124">`@page` must be the first :::no-loc(Razor)::: directive on a page.</span></span> <span data-ttu-id="03aff-125">`@page` ist ein Beispiel für einen Übergang in ein :::no-loc(Razor):::-spezifisches Markup.</span><span class="sxs-lookup"><span data-stu-id="03aff-125">`@page` is an example of transitioning into :::no-loc(Razor):::-specific markup.</span></span> <span data-ttu-id="03aff-126">Unter [:::no-loc(Razor):::-Syntax](xref:mvc/views/razor#razor-syntax) finden Sie weitere Informationen.</span><span class="sxs-lookup"><span data-stu-id="03aff-126">See [:::no-loc(Razor)::: syntax](xref:mvc/views/razor#razor-syntax) for more information.</span></span>

<span data-ttu-id="03aff-127">Überprüfen Sie den Lambdaausdruck, der im folgenden HTML-Hilfsprogramm verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="03aff-127">Examine the lambda expression used in the following HTML Helper:</span></span>

```cshtml
@Html.DisplayNameFor(model => model.Movie[0].Title)
```

<span data-ttu-id="03aff-128">Das HTML-Hilfsprogramm `DisplayNameFor` überprüft die Eigenschaft `Title`, auf die im Lambdaausdruck verwiesen wird, um den Anzeigenamen zu bestimmen.</span><span class="sxs-lookup"><span data-stu-id="03aff-128">The `DisplayNameFor` HTML Helper inspects the `Title` property referenced in the lambda expression to determine the display name.</span></span> <span data-ttu-id="03aff-129">Der Lambdaausdruck wird überprüft und nicht ausgewertet.</span><span class="sxs-lookup"><span data-stu-id="03aff-129">The lambda expression is inspected rather than evaluated.</span></span> <span data-ttu-id="03aff-130">Das bedeutet, dass keine Zugriffsverletzung auftritt, wenn `model`, `model.Movie` oder `model.Movie[0]``null` oder leer ist.</span><span class="sxs-lookup"><span data-stu-id="03aff-130">That means there is no access violation when `model`, `model.Movie`, or `model.Movie[0]` is `null` or empty.</span></span> <span data-ttu-id="03aff-131">Wenn der Lambdaausdruck ausgewertet wird, (z.B. mit `@Html.DisplayFor(modelItem => item.Title)`), werden die Eigenschaftswerte ausgewertet.</span><span class="sxs-lookup"><span data-stu-id="03aff-131">When the lambda expression is evaluated (for example, with `@Html.DisplayFor(modelItem => item.Title)`), the model's property values are evaluated.</span></span>

<a name="md"></a>

### <a name="the-model-directive"></a><span data-ttu-id="03aff-132">Die @model -Direktive</span><span class="sxs-lookup"><span data-stu-id="03aff-132">The @model directive</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/:::no-loc(Razor):::PagesMovie30/Pages/Movies/Index.cshtml?range=1-2&highlight=2)]

<span data-ttu-id="03aff-133">Die `@model`-Anweisung gibt den Typ des Modells an, das an die :::no-loc(Razor):::-Seite weitergegeben wird.</span><span class="sxs-lookup"><span data-stu-id="03aff-133">The `@model` directive specifies the type of the model passed to the :::no-loc(Razor)::: Page.</span></span> <span data-ttu-id="03aff-134">Im vorherigen Beispiel macht die `@model`-Linie die von `PageModel` abgeleitete Klasse der :::no-loc(Razor):::-Seite verfügbar.</span><span class="sxs-lookup"><span data-stu-id="03aff-134">In the preceding example, the `@model` line makes the `PageModel`-derived class available to the :::no-loc(Razor)::: Page.</span></span> <span data-ttu-id="03aff-135">Das Modell wird in den [HTML-Hilfsprogrammen](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) `@Html.DisplayNameFor` und `@Html.DisplayFor` auf der Seite verwendet.</span><span class="sxs-lookup"><span data-stu-id="03aff-135">The model is used in the `@Html.DisplayNameFor` and `@Html.DisplayFor` [HTML Helpers](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) on the page.</span></span>

### <a name="the-layout-page"></a><span data-ttu-id="03aff-136">Die Seite „Layout“</span><span class="sxs-lookup"><span data-stu-id="03aff-136">The layout page</span></span>

<span data-ttu-id="03aff-137">Wählen Sie die Menülinks aus ( **:::no-loc(Razor):::Movie** , **Home** , **Datenschutz** ).</span><span class="sxs-lookup"><span data-stu-id="03aff-137">Select the menu links ( **:::no-loc(Razor):::PagesMovie** , **Home** , and **Privacy** ).</span></span> <span data-ttu-id="03aff-138">Auf jeder Seite wird dasselbe Menülayout gezeigt.</span><span class="sxs-lookup"><span data-stu-id="03aff-138">Each page shows the same menu layout.</span></span> <span data-ttu-id="03aff-139">Das Menülayout wird mithilfe der Datei *Pages/Shared/_Layout.cshtml* implementiert.</span><span class="sxs-lookup"><span data-stu-id="03aff-139">The menu layout is implemented in the *Pages/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="03aff-140">Öffnen Sie die Datei *Pages/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="03aff-140">Open the *Pages/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="03aff-141">Durch [Layout](xref:mvc/views/layout)-Vorlagen kann das HTML-Containerlayout:</span><span class="sxs-lookup"><span data-stu-id="03aff-141">[Layout](xref:mvc/views/layout) templates allow the HTML container layout to be:</span></span>

* <span data-ttu-id="03aff-142">An einem Ort angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="03aff-142">Specified in one place.</span></span>
* <span data-ttu-id="03aff-143">Auf mehreren Seiten der Website angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="03aff-143">Applied in multiple pages in the site.</span></span>

<span data-ttu-id="03aff-144">Suchen Sie die Zeile `@RenderBody()`.</span><span class="sxs-lookup"><span data-stu-id="03aff-144">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="03aff-145">`RenderBody` ist ein Platzhalter, bei dem alle seitenspezifischen Ansichten von der Layoutseite *umschlossen* angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="03aff-145">`RenderBody` is a placeholder where all the page-specific views show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="03aff-146">Wenn Sie beispielsweise den Link **Privacy** auswählen, wird die Ansicht *Pages/Privacy.cshtml* in der `RenderBody`-Methode gerendert.</span><span class="sxs-lookup"><span data-stu-id="03aff-146">For example, select the **Privacy** link and the *Pages/Privacy.cshtml* view is rendered inside the `RenderBody` method.</span></span>

<a name="vd"></a>

### <a name="viewdata-and-layout"></a><span data-ttu-id="03aff-147">ViewData und Layout</span><span class="sxs-lookup"><span data-stu-id="03aff-147">ViewData and layout</span></span>

<span data-ttu-id="03aff-148">Sehen Sie sich das folgenden Markup aus der Datei *Pages/Movies/Index.cshtml* an:</span><span class="sxs-lookup"><span data-stu-id="03aff-148">Consider the following markup from the *Pages/Movies/Index.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/:::no-loc(Razor):::PagesMovie30/Pages/Movies/Index.cshtml?range=1-6&highlight=4-999)]

<span data-ttu-id="03aff-149">Das oben markierte Markup ist ein Beispiel vom Übergang von :::no-loc(Razor)::: in C#.</span><span class="sxs-lookup"><span data-stu-id="03aff-149">The preceding highlighted markup is an example of :::no-loc(Razor)::: transitioning into C#.</span></span> <span data-ttu-id="03aff-150">Die Zeichen `{` und `}` schließen einen Block mit C#-Code ein.</span><span class="sxs-lookup"><span data-stu-id="03aff-150">The `{` and `}` characters enclose a block of C# code.</span></span>

<span data-ttu-id="03aff-151">Die Basisklasse `PageModel` verfügt über eine `ViewData`-Wörterbucheigenschaft, die zum Übergeben von Daten an eine Ansicht verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="03aff-151">The `PageModel` base class contains a `ViewData` dictionary property that can be used to pass data to a View.</span></span> <span data-ttu-id="03aff-152">Mithilfe eines Schlüssel-Wert-Musters können dem `ViewData`-Wörterbuch Objekte hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="03aff-152">Objects are added to the `ViewData` dictionary using a key/value pattern.</span></span> <span data-ttu-id="03aff-153">Im vorherigen Beispiel wurde dem `"Title"`-Wörterbuch die Eigenschaft `ViewData` hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="03aff-153">In the preceding sample, the `"Title"` property is added to the `ViewData` dictionary.</span></span>

<span data-ttu-id="03aff-154">Die Eigenschaft `"Title"` wird in der Datei *Pages/Shared/_Layout.cshtml* verwendet.</span><span class="sxs-lookup"><span data-stu-id="03aff-154">The `"Title"` property is used in the *Pages/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="03aff-155">Das folgende Markup zeigt die ersten Zeilen der Datei *Pages/_Layout.cshtml* an.</span><span class="sxs-lookup"><span data-stu-id="03aff-155">The following markup shows the first few lines of the *_Layout.cshtml* file.</span></span>

<!-- We need a snapshot copy of layout because we are changing in the next step. -->

[!code-cshtml[](razor-pages-start/snapshot_sample/:::no-loc(Razor):::PagesMovie/Pages/NU/_Layout.cshtml?highlight=6)]

<span data-ttu-id="03aff-156">Die Zeile `@*Markup removed for brevity.*@` ist ein :::no-loc(Razor):::-Kommentar.</span><span class="sxs-lookup"><span data-stu-id="03aff-156">The line `@*Markup removed for brevity.*@` is a :::no-loc(Razor)::: comment.</span></span> <span data-ttu-id="03aff-157">Im Gegensatz zu HTML-Kommentaren (`<!-- -->`) werden :::no-loc(Razor):::-Kommentare nicht an den Client gesendet.</span><span class="sxs-lookup"><span data-stu-id="03aff-157">Unlike HTML comments (`<!-- -->`), :::no-loc(Razor)::: comments are not sent to the client.</span></span>

### <a name="update-the-layout"></a><span data-ttu-id="03aff-158">Aktualisieren des Layouts</span><span class="sxs-lookup"><span data-stu-id="03aff-158">Update the layout</span></span>

<span data-ttu-id="03aff-159">Ändern Sie das `<title>`-Element in der Datei *Pages/Shared/_Layout.cshtml* , um **Movie** anstelle von **:::no-loc(Razor):::PagesMovie** anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="03aff-159">Change the `<title>` element in the *Pages/Shared/_Layout.cshtml* file to display **Movie** rather than **:::no-loc(Razor):::PagesMovie**.</span></span>

[!code-cshtml[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Pages/Shared/_Layout.cshtml?range=1-6&highlight=6)]

<span data-ttu-id="03aff-160">Suchen Sie in der Datei *Pages/Shared/_Layout.cshtml* das folgende Ankerelement.</span><span class="sxs-lookup"><span data-stu-id="03aff-160">Find the following anchor element in the *Pages/Shared/_Layout.cshtml* file.</span></span>

```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Index">:::no-loc(Razor):::PagesMovie</a>
```

<span data-ttu-id="03aff-161">Ersetzen Sie das vorhergehende Element durch das folgende Markup.</span><span class="sxs-lookup"><span data-stu-id="03aff-161">Replace the preceding element with the following markup:</span></span>

```cshtml
<a class="navbar-brand" asp-page="/Movies/Index">RpMovie</a>
```

<span data-ttu-id="03aff-162">Das vorangehende Ankerelement ist ein [Tag Helper (Taghilfsprogramm)](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="03aff-162">The preceding anchor element is a [Tag Helper](xref:mvc/views/tag-helpers/intro).</span></span> <span data-ttu-id="03aff-163">In diesem Fall handelt es sich um ein [Anchor Tag Helper (Anchor-Taghilfsprogramm)](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="03aff-163">In this case, it's the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).</span></span> <span data-ttu-id="03aff-164">Das Taghilfsattribut und der -wert `asp-page="/Movies/Index"` erstellen einen Link zur :::no-loc(Razor):::-Seite `/Movies/Index`.</span><span class="sxs-lookup"><span data-stu-id="03aff-164">The `asp-page="/Movies/Index"` Tag Helper attribute and value creates a link to the `/Movies/Index` :::no-loc(Razor)::: Page.</span></span> <span data-ttu-id="03aff-165">Das `asp-area`-Attribut ist leer, daher wird der Bereich im Link nicht verwendet.</span><span class="sxs-lookup"><span data-stu-id="03aff-165">The `asp-area` attribute value is empty, so the area isn't used in the link.</span></span> <span data-ttu-id="03aff-166">Weitere Informationen finden Sie unter [Bereiche](xref:mvc/controllers/areas).</span><span class="sxs-lookup"><span data-stu-id="03aff-166">See [Areas](xref:mvc/controllers/areas) for more information.</span></span>

<span data-ttu-id="03aff-167">Speichern Sie Ihre Änderungen, und testen Sie die App, indem Sie auf den Link **RpMovie** klicken.</span><span class="sxs-lookup"><span data-stu-id="03aff-167">Save your changes, and test the app by clicking on the **RpMovie** link.</span></span> <span data-ttu-id="03aff-168">Wenn Probleme auftreten, sehen Sie sich die Datei [_Layout.cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Pages/Shared/_Layout.cshtml) in GitHub an.</span><span class="sxs-lookup"><span data-stu-id="03aff-168">See the [_Layout.cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Pages/Shared/_Layout.cshtml) file in GitHub if you have any problems.</span></span>

<span data-ttu-id="03aff-169">Testen Sie die anderen Links – **Home** (Startseite), **RpMovie** (Razor Pages-Film), **Create** (Erstellen), **Edit** (Bearbeiten) und **Delete** (Löschen).</span><span class="sxs-lookup"><span data-stu-id="03aff-169">Test the other links ( **Home** , **RpMovie** , **Create** , **Edit** , and **Delete** ).</span></span> <span data-ttu-id="03aff-170">Jede Seite legt den Titel fest, der in der Browserregisterkarte angezeigt wird. Wenn Sie eine Seite mit einem Lesezeichen versehen, wird dieser Titel für das Lesezeichen verwendet.</span><span class="sxs-lookup"><span data-stu-id="03aff-170">Each page sets the title, which you can see in the browser tab. When you bookmark a page, the title is used for the bookmark.</span></span>

> [!NOTE]
> <span data-ttu-id="03aff-171">Sie können unter Umständen in das Feld `Price` keine Kommas als Dezimaltrennzeichen eingeben.</span><span class="sxs-lookup"><span data-stu-id="03aff-171">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="03aff-172">Zur Unterstützung der [jQuery-Validierung](https://jqueryvalidation.org/) für nicht englische Gebietsschemas, in denen ein Komma („,“) als Dezimaltrennzeichen verwendet wird, und Nicht-US-englische Datums- und Uhrzeitformate müssen Sie Schritte zur Globalisierung Ihrer App ausführen.</span><span class="sxs-lookup"><span data-stu-id="03aff-172">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point, and non US-English date formats, you must take steps to globalize your app.</span></span> <span data-ttu-id="03aff-173">In diesem [GitHub-Problem 4076](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) finden Sie Anweisungen zum Hinzufügen von Kommas als Dezimaltrennzeichen.</span><span class="sxs-lookup"><span data-stu-id="03aff-173">See this [GitHub issue 4076](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) for instructions on adding decimal comma.</span></span>

<span data-ttu-id="03aff-174">Die Eigenschaft `Layout` wird in der Datei *Pages/_ViewStart.cshtml* festgelegt:</span><span class="sxs-lookup"><span data-stu-id="03aff-174">The `Layout` property is set in the *Pages/_ViewStart.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Pages/_ViewStart.cshtml)]

<span data-ttu-id="03aff-175">Das vorhergehende Markup legt die Layoutdatei für alle :::no-loc(Razor):::-Dateien unter dem Ordner *Pages* auf *Pages/Shared/_Layout.cshtml* fest.</span><span class="sxs-lookup"><span data-stu-id="03aff-175">The preceding markup sets the layout file to *Pages/Shared/_Layout.cshtml* for all :::no-loc(Razor)::: files under the *Pages* folder.</span></span> <span data-ttu-id="03aff-176">Weitere Informationen finden Sie unter [Layout](xref:razor-pages/index#layout).</span><span class="sxs-lookup"><span data-stu-id="03aff-176">See [Layout](xref:razor-pages/index#layout) for more information.</span></span>

### <a name="the-create-page-model"></a><span data-ttu-id="03aff-177">Das Seitenmodell „Create“</span><span class="sxs-lookup"><span data-stu-id="03aff-177">The Create page model</span></span>

<span data-ttu-id="03aff-178">Überprüfen Sie das Seitenmodell *Pages/Movies/Create.cshtml.cs* :</span><span class="sxs-lookup"><span data-stu-id="03aff-178">Examine the *Pages/Movies/Create.cshtml.cs* page model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample3/:::no-loc(Razor):::PagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippetALL)]

<span data-ttu-id="03aff-179">Die Methode `OnGet` initialisiert einen beliebigen Zustand, der für die Seite erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="03aff-179">The `OnGet` method initializes any state needed for the page.</span></span> <span data-ttu-id="03aff-180">Die Seite „Create“ (Erstellen) verfügt nicht über einen initialisierbaren Zustand. Deshalb wird `Page` zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="03aff-180">The Create page doesn't have any state to initialize, so `Page` is returned.</span></span> <span data-ttu-id="03aff-181">Später in diesem Tutorial wird ein Beispiel für `OnGet` zur Initialisierung des Zustands gezeigt.</span><span class="sxs-lookup"><span data-stu-id="03aff-181">Later in the tutorial, an example of `OnGet` initializing state is shown.</span></span> <span data-ttu-id="03aff-182">Die Methode `Page` erstellt ein `PageResult`-Objekt, das die Seite *Create.cshtml* rendert.</span><span class="sxs-lookup"><span data-stu-id="03aff-182">The `Page` method creates a `PageResult` object that renders the *Create.cshtml* page.</span></span>

<span data-ttu-id="03aff-183">Die Eigenschaft `Movie` verwendet das `[BindProperty]`-Attribut, um die [Modellbindung](xref:mvc/models/model-binding) zu aktivieren.</span><span class="sxs-lookup"><span data-stu-id="03aff-183">The `Movie` property uses the `[BindProperty]` attribute to opt-in to [model binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="03aff-184">Wenn das Formular „Create“ die Formularwerte bereitstellt, bindet die ASP.NET Core-Laufzeit die bereitgestellten Werte an das `Movie`-Modell.</span><span class="sxs-lookup"><span data-stu-id="03aff-184">When the Create form posts the form values, the ASP.NET Core runtime binds the posted values to the `Movie` model.</span></span>

<span data-ttu-id="03aff-185">Die Methode `OnPostAsync` wird ausgeführt, wenn die Seite Formulardaten bereitstellt:</span><span class="sxs-lookup"><span data-stu-id="03aff-185">The `OnPostAsync` method is run when the page posts form data:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample3/:::no-loc(Razor):::PagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippetPost)]

<span data-ttu-id="03aff-186">Wenn es zu Modellfehlern kommt, wird das Formular mit allen bereitgestellten Formulardaten erneut angezeigt.</span><span class="sxs-lookup"><span data-stu-id="03aff-186">If there are any model errors, the form is redisplayed, along with any form data posted.</span></span> <span data-ttu-id="03aff-187">Die meisten Modellfehler können clientseitig abgefangen werden, bevor das Formular bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="03aff-187">Most model errors can be caught on the client-side before the form is posted.</span></span> <span data-ttu-id="03aff-188">Ein Beispiel für einen Modellfehler ist die Bereitstellung eines Werts für das Datumsfeld, der nicht in ein Datum konvertiert werden kann.</span><span class="sxs-lookup"><span data-stu-id="03aff-188">An example of a model error is posting a value for the date field that cannot be converted to a date.</span></span> <span data-ttu-id="03aff-189">Die clientseitige Validierung und die Modellvalidierung werden später in diesem Tutorial erläutert.</span><span class="sxs-lookup"><span data-stu-id="03aff-189">Client-side validation and model validation are discussed later in the tutorial.</span></span>

<span data-ttu-id="03aff-190">Wenn keine Modellfehler auftreten, werden die Daten gespeichert und der Browser zur Indexseite umgeleitet.</span><span class="sxs-lookup"><span data-stu-id="03aff-190">If there are no model errors, the data is saved, and the browser is redirected to the Index page.</span></span>

### <a name="the-create-no-locrazor-page"></a><span data-ttu-id="03aff-191">:::no-loc(Razor):::-Seite „Erstellen“</span><span class="sxs-lookup"><span data-stu-id="03aff-191">The Create :::no-loc(Razor)::: Page</span></span>

<span data-ttu-id="03aff-192">Betrachten Sie die Datei *Pages/Movies/Create.cshtml* der :::no-loc(Razor):::-Seite:</span><span class="sxs-lookup"><span data-stu-id="03aff-192">Examine the *Pages/Movies/Create.cshtml* :::no-loc(Razor)::: Page file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/:::no-loc(Razor):::PagesMovie30/Pages/Movies/Create.cshtml)]

# <a name="visual-studio"></a>[<span data-ttu-id="03aff-193">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="03aff-193">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="03aff-194">Visual Studio zeigt die folgenden Tags in einer bestimmten Schriftart an, die für Taghilfsprogramme verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="03aff-194">Visual Studio displays the following tags in a distinctive bold font used for Tag Helpers:</span></span>

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

![VS17-Ansicht der Create.cshtml-Seite](page/_static/th3.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="03aff-196">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="03aff-196">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="03aff-197">Die folgenden Taghilfsprogramme werden im vorangehenden Markup angezeigt:</span><span class="sxs-lookup"><span data-stu-id="03aff-197">The following Tag Helpers are shown in the preceding markup:</span></span>

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="03aff-198">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="03aff-198">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="03aff-199">Visual Studio zeigt die folgenden Tags in einer bestimmten Schriftart an, die für Taghilfsprogramme verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="03aff-199">Visual Studio displays the following tags in a distinctive bold font used for Tag Helpers:</span></span>

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

---

<span data-ttu-id="03aff-200">Das Element `<form method="post">` ist ein [Form Tag Helper (Hilfsprogramm für Formulartags)](xref:mvc/views/working-with-forms#the-form-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="03aff-200">The `<form method="post">` element is a [Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper).</span></span> <span data-ttu-id="03aff-201">Das Hilfsprogramm für Formulartags enthält automatisch ein [antiforgery token (Antifälschungstoken)](xref:security/anti-request-forgery).</span><span class="sxs-lookup"><span data-stu-id="03aff-201">The Form Tag Helper automatically includes an [antiforgery token](xref:security/anti-request-forgery).</span></span>

<span data-ttu-id="03aff-202">Die Gerüstbau-Engine erstellt :::no-loc(Razor):::-Markup für jedes Feld im Modell (ausgenommen die ID) ähnlich wie folgt:</span><span class="sxs-lookup"><span data-stu-id="03aff-202">The scaffolding engine creates :::no-loc(Razor)::: markup for each field in the model (except the ID) similar to the following:</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample3/:::no-loc(Razor):::PagesMovie30/Pages/Movies/Create.cshtml?range=15-20)]

<span data-ttu-id="03aff-203">Das [Hilfsprogramm für Validierungstags](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` und `<span asp-validation-for`) zeigt Validierungsfehler.</span><span class="sxs-lookup"><span data-stu-id="03aff-203">The [Validation Tag Helpers](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` and `<span asp-validation-for`) display validation errors.</span></span> <span data-ttu-id="03aff-204">Die Validierung wird an späterer Stelle in dieser Reihe detaillierter beschrieben.</span><span class="sxs-lookup"><span data-stu-id="03aff-204">Validation is covered in more detail later in this series.</span></span>

<span data-ttu-id="03aff-205">Das [Label Tag Helper (Taghilfsprogramm für Label)](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) generiert den Titel des Labels und das `for`-Attribut für die Eigenschaft `Title`.</span><span class="sxs-lookup"><span data-stu-id="03aff-205">The [Label Tag Helper](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) generates the label caption and `for` attribute for the `Title` property.</span></span>

<span data-ttu-id="03aff-206">Das [Hilfsprogramm für Eingabetags](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control">`) verwendet die Attribute von [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) und generiert HTML-Attribute, die auf der Clientseite für die jQuery-Validierung erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="03aff-206">The [Input Tag Helper](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control">`) uses the [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) attributes and produces HTML attributes needed for jQuery Validation on the client-side.</span></span>

<span data-ttu-id="03aff-207">Weitere Informationen zu Taghilfsprogrammen wie `<form method="post">` finden Sie unter [Taghilfsprogramme in ASP.NET Core](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="03aff-207">For more information on Tag Helpers such as `<form method="post">`, see [Tag Helpers in ASP.NET Core](xref:mvc/views/tag-helpers/intro).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="03aff-208">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="03aff-208">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="03aff-209">[Zurück: Hinzufügen eines Modells](xref:tutorials/razor-pages/model)
> [Nächster Schritt: Datenbank](xref:tutorials/razor-pages/sql)</span><span class="sxs-lookup"><span data-stu-id="03aff-209">[Previous: Adding a model](xref:tutorials/razor-pages/model)
[Next: Database](xref:tutorials/razor-pages/sql)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="03aff-210">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="03aff-210">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="03aff-211">In diesem Tutorial werden die :::no-loc(Razor)::: Pages näher untersucht, die durch den Gerüstbau im [vorherigen Tutorial](xref:tutorials/razor-pages/model) erstellt wurden.</span><span class="sxs-lookup"><span data-stu-id="03aff-211">This tutorial examines the :::no-loc(Razor)::: Pages created by scaffolding in the [previous tutorial](xref:tutorials/razor-pages/model).</span></span>

<span data-ttu-id="03aff-212">Beispiel [Anzeigen oder Herunterladen](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22).</span><span class="sxs-lookup"><span data-stu-id="03aff-212">[View or download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22) sample.</span></span>

## <a name="the-create-delete-details-and-edit-pages"></a><span data-ttu-id="03aff-213">Die Seiten „Create“, „Delete“, „Details“ und „Edit“</span><span class="sxs-lookup"><span data-stu-id="03aff-213">The Create, Delete, Details, and Edit pages</span></span>

<span data-ttu-id="03aff-214">Sehen Sie sich das Seitenmodell *Pages/Movies/Index.cshtml.cs* an:</span><span class="sxs-lookup"><span data-stu-id="03aff-214">Examine the *Pages/Movies/Index.cshtml.cs* Page Model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample/:::no-loc(Razor):::PagesMovie/Pages/Movies/Index.cshtml.cs)]

<span data-ttu-id="03aff-215">:::no-loc(Razor):::-Seiten werden von `PageModel` abgeleitet.</span><span class="sxs-lookup"><span data-stu-id="03aff-215">:::no-loc(Razor)::: Pages are derived from `PageModel`.</span></span> <span data-ttu-id="03aff-216">Gemäß der Konvention wird die von `PageModel` abgeleitete Klasse `<PageName>Model` genannt.</span><span class="sxs-lookup"><span data-stu-id="03aff-216">By convention, the `PageModel`-derived class is called `<PageName>Model`.</span></span> <span data-ttu-id="03aff-217">Der Konstruktor verwendet [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection), um `:::no-loc(Razor):::PagesMovieContext` zur Seite hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="03aff-217">The constructor uses [dependency injection](xref:fundamentals/dependency-injection) to add the `:::no-loc(Razor):::PagesMovieContext` to the page.</span></span> <span data-ttu-id="03aff-218">Alle per Gerüstbau erstellten Seiten folgen diesem Muster.</span><span class="sxs-lookup"><span data-stu-id="03aff-218">All the scaffolded pages follow this pattern.</span></span> <span data-ttu-id="03aff-219">Weitere Informationen zum asynchronen Programmieren mit Entity Framework finden Sie unter [Asynchroner Code](xref:data/ef-rp/intro#asynchronous-code).</span><span class="sxs-lookup"><span data-stu-id="03aff-219">See [Asynchronous code](xref:data/ef-rp/intro#asynchronous-code) for more information on asynchronous programming with Entity Framework.</span></span>

<span data-ttu-id="03aff-220">Wenn eine Anforderung an die Seite erfolgt, gibt die Methode `OnGetAsync` eine Filmliste an die :::no-loc(Razor):::-Seite zurück.</span><span class="sxs-lookup"><span data-stu-id="03aff-220">When a request is made for the page, the `OnGetAsync` method returns a list of movies to the :::no-loc(Razor)::: Page.</span></span> <span data-ttu-id="03aff-221">`OnGetAsync` oder `OnGet` werden für eine :::no-loc(Razor):::-Seite aufgerufen, um den Zustand der Seite zu initialisieren.</span><span class="sxs-lookup"><span data-stu-id="03aff-221">`OnGetAsync` or `OnGet` is called on a :::no-loc(Razor)::: Page to initialize the state for the page.</span></span> <span data-ttu-id="03aff-222">In diesem Fall ruft `OnGetAsync` eine Liste von Filmen ab und zeigt diese an.</span><span class="sxs-lookup"><span data-stu-id="03aff-222">In this case, `OnGetAsync` gets a list of movies and displays them.</span></span>

<span data-ttu-id="03aff-223">Wenn `OnGet``void` und `OnGetAsync``Task` zurückgibt, wird keine Rückgabemethode verwendet.</span><span class="sxs-lookup"><span data-stu-id="03aff-223">When `OnGet` returns `void` or `OnGetAsync` returns`Task`, no return method is used.</span></span> <span data-ttu-id="03aff-224">Wenn der Rückgabetyp `IActionResult` oder `Task<IActionResult>` ist, muss eine Rückgabeanweisung bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="03aff-224">When the return type is `IActionResult` or `Task<IActionResult>`, a return statement must be provided.</span></span> <span data-ttu-id="03aff-225">Dies kann z. B. die `OnPostAsync`-Methode *Pages/Movies/Create.cshtml.cs* sein:</span><span class="sxs-lookup"><span data-stu-id="03aff-225">For example, the *Pages/Movies/Create.cshtml.cs* `OnPostAsync` method:</span></span>

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Pages/Movies/Create.cshtml.cs?name=snippet)]

<a name="index"></a> <span data-ttu-id="03aff-226">Sehen Sie sich die :::no-loc(Razor):::-Seite *Pages/Movies/Index.cshtml* an:</span><span class="sxs-lookup"><span data-stu-id="03aff-226">Examine the *Pages/Movies/Index.cshtml* :::no-loc(Razor)::: Page:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/:::no-loc(Razor):::PagesMovie/Pages/Movies/Index.cshtml)]

<span data-ttu-id="03aff-227">:::no-loc(Razor)::: kann aus HTML in C# oder ein :::no-loc(Razor):::-spezifisches Markup übergehen.</span><span class="sxs-lookup"><span data-stu-id="03aff-227">:::no-loc(Razor)::: can transition from HTML into C# or into :::no-loc(Razor):::-specific markup.</span></span> <span data-ttu-id="03aff-228">Wenn auf ein `@`-Symbol ein für [:::no-loc(Razor)::: reserviertes Schlüsselwort](xref:mvc/views/razor#razor-reserved-keywords) folgt, wechselt es in ein :::no-loc(Razor):::-spezifisches Markup. Andernfalls geht es in C# über.</span><span class="sxs-lookup"><span data-stu-id="03aff-228">When an `@` symbol is followed by a [:::no-loc(Razor)::: reserved keyword](xref:mvc/views/razor#razor-reserved-keywords), it transitions into :::no-loc(Razor):::-specific markup, otherwise it transitions into C#.</span></span>

<span data-ttu-id="03aff-229">Die :::no-loc(Razor):::-Anweisung `@page` wandelt die Datei in eine MVC-Aktion um. Das bedeutet, dass sie Anforderungen verarbeiten kann.</span><span class="sxs-lookup"><span data-stu-id="03aff-229">The `@page` :::no-loc(Razor)::: directive makes the file into an MVC action, which means that it can handle requests.</span></span> <span data-ttu-id="03aff-230">`@page` muss die erste :::no-loc(Razor):::-Anweisung auf einer Seite sein.</span><span class="sxs-lookup"><span data-stu-id="03aff-230">`@page` must be the first :::no-loc(Razor)::: directive on a page.</span></span> <span data-ttu-id="03aff-231">`@page` ist ein Beispiel für einen Übergang in ein :::no-loc(Razor):::-spezifisches Markup.</span><span class="sxs-lookup"><span data-stu-id="03aff-231">`@page` is an example of transitioning into :::no-loc(Razor):::-specific markup.</span></span> <span data-ttu-id="03aff-232">Unter [:::no-loc(Razor):::-Syntax](xref:mvc/views/razor#razor-syntax) finden Sie weitere Informationen.</span><span class="sxs-lookup"><span data-stu-id="03aff-232">See [:::no-loc(Razor)::: syntax](xref:mvc/views/razor#razor-syntax) for more information.</span></span>

<span data-ttu-id="03aff-233">Überprüfen Sie den Lambdaausdruck, der im folgenden HTML-Hilfsprogramm verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="03aff-233">Examine the lambda expression used in the following HTML Helper:</span></span>

```cshtml
@Html.DisplayNameFor(model => model.Movie[0].Title)
```

<span data-ttu-id="03aff-234">Das HTML-Hilfsprogramm `DisplayNameFor` überprüft die Eigenschaft `Title`, auf die im Lambdaausdruck verwiesen wird, um den Anzeigenamen zu bestimmen.</span><span class="sxs-lookup"><span data-stu-id="03aff-234">The `DisplayNameFor` HTML Helper inspects the `Title` property referenced in the lambda expression to determine the display name.</span></span> <span data-ttu-id="03aff-235">Der Lambdaausdruck wird überprüft und nicht ausgewertet.</span><span class="sxs-lookup"><span data-stu-id="03aff-235">The lambda expression is inspected rather than evaluated.</span></span> <span data-ttu-id="03aff-236">Das bedeutet, dass keine Zugriffsverletzung auftritt, wenn `model`, `model.Movie` oder `model.Movie[0]``null` oder leer sind.</span><span class="sxs-lookup"><span data-stu-id="03aff-236">That means there is no access violation when `model`, `model.Movie`, or `model.Movie[0]` are `null` or empty.</span></span> <span data-ttu-id="03aff-237">Wenn der Lambdaausdruck ausgewertet wird, (z.B. mit `@Html.DisplayFor(modelItem => item.Title)`), werden die Eigenschaftswerte ausgewertet.</span><span class="sxs-lookup"><span data-stu-id="03aff-237">When the lambda expression is evaluated (for example, with `@Html.DisplayFor(modelItem => item.Title)`), the model's property values are evaluated.</span></span>

<a name="md"></a>

### <a name="the-model-directive"></a><span data-ttu-id="03aff-238">Die @model -Direktive</span><span class="sxs-lookup"><span data-stu-id="03aff-238">The @model directive</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/:::no-loc(Razor):::PagesMovie/Pages/Movies/Index.cshtml?range=1-2&highlight=2)]

<span data-ttu-id="03aff-239">Die `@model`-Anweisung gibt den Typ des Modells an, das an die :::no-loc(Razor):::-Seite weitergegeben wird.</span><span class="sxs-lookup"><span data-stu-id="03aff-239">The `@model` directive specifies the type of the model passed to the :::no-loc(Razor)::: Page.</span></span> <span data-ttu-id="03aff-240">Im vorherigen Beispiel macht die `@model`-Linie die von `PageModel` abgeleitete Klasse der :::no-loc(Razor):::-Seite verfügbar.</span><span class="sxs-lookup"><span data-stu-id="03aff-240">In the preceding example, the `@model` line makes the `PageModel`-derived class available to the :::no-loc(Razor)::: Page.</span></span> <span data-ttu-id="03aff-241">Das Modell wird in den [HTML-Hilfsprogrammen](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) `@Html.DisplayNameFor` und `@Html.DisplayFor` auf der Seite verwendet.</span><span class="sxs-lookup"><span data-stu-id="03aff-241">The model is used in the `@Html.DisplayNameFor` and `@Html.DisplayFor` [HTML Helpers](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) on the page.</span></span>

### <a name="the-layout-page"></a><span data-ttu-id="03aff-242">Die Seite „Layout“</span><span class="sxs-lookup"><span data-stu-id="03aff-242">The layout page</span></span>

<span data-ttu-id="03aff-243">Wählen Sie die Menülinks aus ( **:::no-loc(Razor):::Movie** , **Home** , **Datenschutz** ).</span><span class="sxs-lookup"><span data-stu-id="03aff-243">Select the menu links ( **:::no-loc(Razor):::PagesMovie** , **Home** , and **Privacy** ).</span></span> <span data-ttu-id="03aff-244">Auf jeder Seite wird dasselbe Menülayout gezeigt.</span><span class="sxs-lookup"><span data-stu-id="03aff-244">Each page shows the same menu layout.</span></span> <span data-ttu-id="03aff-245">Das Menülayout wird mithilfe der Datei *Pages/Shared/_Layout.cshtml* implementiert.</span><span class="sxs-lookup"><span data-stu-id="03aff-245">The menu layout is implemented in the *Pages/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="03aff-246">Öffnen Sie die Datei *Pages/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="03aff-246">Open the *Pages/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="03aff-247">[Layout](xref:mvc/views/layout)-Vorlagen ermöglichen Ihnen, das HTML-Containerlayout Ihrer Website zentral anzugeben und dann auf mehrere Seiten Ihrer Website anzuwenden.</span><span class="sxs-lookup"><span data-stu-id="03aff-247">[Layout](xref:mvc/views/layout) templates allow you to specify the HTML container layout of your site in one place and then apply it across multiple pages in your site.</span></span> <span data-ttu-id="03aff-248">Suchen Sie die Zeile `@RenderBody()`.</span><span class="sxs-lookup"><span data-stu-id="03aff-248">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="03aff-249">`RenderBody` ist ein Platzhalter, bei dem alle seitenspezifischen Ansichten, die Sie erstellen, von der Layoutseite *umschlossen* angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="03aff-249">`RenderBody` is a placeholder where all the page-specific views you create show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="03aff-250">Wenn Sie beispielsweise den Link **Privacy** auswählen, wird die Ansicht **Pages/Privacy.cshtml** in der `RenderBody`-Methode gerendert.</span><span class="sxs-lookup"><span data-stu-id="03aff-250">For example, if you select the **Privacy** link, the **Pages/Privacy.cshtml** view is rendered inside the `RenderBody` method.</span></span>

<a name="vd"></a>

### <a name="viewdata-and-layout"></a><span data-ttu-id="03aff-251">ViewData und Layout</span><span class="sxs-lookup"><span data-stu-id="03aff-251">ViewData and layout</span></span>

<span data-ttu-id="03aff-252">Sehen Sie sich den folgenden Code aus der Datei *Pages/Movies/Index.cshtml* an:</span><span class="sxs-lookup"><span data-stu-id="03aff-252">Consider the following code from the *Pages/Movies/Index.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/:::no-loc(Razor):::PagesMovie/Pages/Movies/Index.cshtml?range=1-6&highlight=4-999)]

<span data-ttu-id="03aff-253">Der oben markierte Code ist ein Beispiel vom Übergang von :::no-loc(Razor)::: in C#.</span><span class="sxs-lookup"><span data-stu-id="03aff-253">The preceding highlighted code is an example of :::no-loc(Razor)::: transitioning into C#.</span></span> <span data-ttu-id="03aff-254">Die Zeichen `{` und `}` schließen einen Block mit C#-Code ein.</span><span class="sxs-lookup"><span data-stu-id="03aff-254">The `{` and `}` characters enclose a block of C# code.</span></span>

<span data-ttu-id="03aff-255">Die Basisklasse `PageModel` verfügt über eine `ViewData`-Wörterbucheigenschaft, die zum Hinzufügen von Daten verwendet werden kann, die an eine Ansicht übergeben werden sollen.</span><span class="sxs-lookup"><span data-stu-id="03aff-255">The `PageModel` base class has a `ViewData` dictionary property that can be used to add data that you want to pass to a View.</span></span> <span data-ttu-id="03aff-256">Mithilfe eines Schlüssel-Wert-Musters können dem `ViewData`-Wörterbuch Objekte hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="03aff-256">You add objects into the `ViewData` dictionary using a key/value pattern.</span></span> <span data-ttu-id="03aff-257">Im vorherigen Beispiel wurde dem `ViewData`-Wörterbuch die Eigenschaft „Title“ hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="03aff-257">In the preceding sample, the "Title" property is added to the `ViewData` dictionary.</span></span>

<span data-ttu-id="03aff-258">Die Eigenschaft „Title“ wird in der Datei *Pages/Shared/_Layout.cshtml* verwendet.</span><span class="sxs-lookup"><span data-stu-id="03aff-258">The "Title" property is used in the *Pages/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="03aff-259">Das folgende Markup zeigt die ersten Zeilen der Datei *Pages/_Layout.cshtml* an.</span><span class="sxs-lookup"><span data-stu-id="03aff-259">The following markup shows the first few lines of the *_Layout.cshtml* file.</span></span>

<!-- We need a snapshot copy of layout because we are changing in the next step. -->

[!code-cshtml[](razor-pages-start/snapshot_sample/:::no-loc(Razor):::PagesMovie/Pages/NU/_Layout.cshtml?highlight=6-99)]

<span data-ttu-id="03aff-260">Die Zeile `@*Markup removed for brevity.*@` ist ein :::no-loc(Razor):::-Kommentar, der in Ihrer Layoutdatei nicht angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="03aff-260">The line `@*Markup removed for brevity.*@` is a :::no-loc(Razor)::: comment which doesn't appear in your layout file.</span></span> <span data-ttu-id="03aff-261">Im Gegensatz zu HTML-Kommentaren (`<!-- -->`) werden :::no-loc(Razor):::-Kommentare nicht an den Client gesendet.</span><span class="sxs-lookup"><span data-stu-id="03aff-261">Unlike HTML comments (`<!-- -->`), :::no-loc(Razor)::: comments are not sent to the client.</span></span>

### <a name="update-the-layout"></a><span data-ttu-id="03aff-262">Aktualisieren des Layouts</span><span class="sxs-lookup"><span data-stu-id="03aff-262">Update the layout</span></span>

<span data-ttu-id="03aff-263">Ändern Sie das `<title>`-Element in der Datei *Pages/Shared/_Layout.cshtml* , um **Movie** anstelle von **:::no-loc(Razor):::PagesMovie** anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="03aff-263">Change the `<title>` element in the *Pages/Shared/_Layout.cshtml* file to display **Movie** rather than **:::no-loc(Razor):::PagesMovie**.</span></span>

[!code-cshtml[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Pages/Shared/_Layout.cshtml?range=1-6&highlight=6)]

<span data-ttu-id="03aff-264">Suchen Sie in der Datei *Pages/Shared/_Layout.cshtml* das folgende Ankerelement.</span><span class="sxs-lookup"><span data-stu-id="03aff-264">Find the following anchor element in the *Pages/Shared/_Layout.cshtml* file.</span></span>

```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Index">:::no-loc(Razor):::PagesMovie</a>
```

<span data-ttu-id="03aff-265">Ersetzen Sie das vorhergehende Element durch das folgende Markup.</span><span class="sxs-lookup"><span data-stu-id="03aff-265">Replace the preceding element with the following markup.</span></span>

```cshtml
<a class="navbar-brand" asp-page="/Movies/Index">RpMovie</a>
```

<span data-ttu-id="03aff-266">Das vorangehende Ankerelement ist ein [Tag Helper (Taghilfsprogramm)](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="03aff-266">The preceding anchor element is a [Tag Helper](xref:mvc/views/tag-helpers/intro).</span></span> <span data-ttu-id="03aff-267">In diesem Fall handelt es sich um ein [Anchor Tag Helper (Anchor-Taghilfsprogramm)](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="03aff-267">In this case, it's the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).</span></span> <span data-ttu-id="03aff-268">Das Taghilfsattribut und der -wert `asp-page="/Movies/Index"` erstellen einen Link zur :::no-loc(Razor):::-Seite `/Movies/Index`.</span><span class="sxs-lookup"><span data-stu-id="03aff-268">The `asp-page="/Movies/Index"` Tag Helper attribute and value creates a link to the `/Movies/Index` :::no-loc(Razor)::: Page.</span></span> <span data-ttu-id="03aff-269">Das `asp-area`-Attribut ist leer, daher wird der Bereich im Link nicht verwendet.</span><span class="sxs-lookup"><span data-stu-id="03aff-269">The `asp-area` attribute value is empty, so the area isn't used in the link.</span></span> <span data-ttu-id="03aff-270">Weitere Informationen finden Sie unter [Bereiche](xref:mvc/controllers/areas).</span><span class="sxs-lookup"><span data-stu-id="03aff-270">See [Areas](xref:mvc/controllers/areas) for more information.</span></span>

<span data-ttu-id="03aff-271">Speichern Sie Ihre Änderungen, und testen Sie die App, indem Sie auf den Link **RpMovie** klicken.</span><span class="sxs-lookup"><span data-stu-id="03aff-271">Save your changes, and test the app by clicking on the **RpMovie** link.</span></span> <span data-ttu-id="03aff-272">Wenn Probleme auftreten, sehen Sie sich die Datei [_Layout.cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Pages/Shared/_Layout.cshtml) in GitHub an.</span><span class="sxs-lookup"><span data-stu-id="03aff-272">See the [_Layout.cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Pages/Shared/_Layout.cshtml) file in GitHub if you have any problems.</span></span>

<span data-ttu-id="03aff-273">Testen Sie die anderen Links – **Home** (Startseite), **RpMovie** (Razor Pages-Film), **Create** (Erstellen), **Edit** (Bearbeiten) und **Delete** (Löschen).</span><span class="sxs-lookup"><span data-stu-id="03aff-273">Test the other links ( **Home** , **RpMovie** , **Create** , **Edit** , and **Delete** ).</span></span> <span data-ttu-id="03aff-274">Jede Seite legt den Titel fest, der in der Browserregisterkarte angezeigt wird. Wenn Sie eine Seite mit einem Lesezeichen versehen, wird dieser Titel für das Lesezeichen verwendet.</span><span class="sxs-lookup"><span data-stu-id="03aff-274">Each page sets the title, which you can see in the browser tab. When you bookmark a page, the title is used for the bookmark.</span></span>

> [!NOTE]
> <span data-ttu-id="03aff-275">Sie können unter Umständen in das Feld `Price` keine Kommas als Dezimaltrennzeichen eingeben.</span><span class="sxs-lookup"><span data-stu-id="03aff-275">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="03aff-276">Zur Unterstützung der [jQuery-Validierung](https://jqueryvalidation.org/) für nicht englische Gebietsschemas, in denen ein Komma („,“) als Dezimaltrennzeichen verwendet wird, und Nicht-US-englische Datums- und Uhrzeitformate müssen Sie Schritte zur Globalisierung Ihrer App ausführen.</span><span class="sxs-lookup"><span data-stu-id="03aff-276">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point, and non US-English date formats, you must take steps to globalize your app.</span></span> <span data-ttu-id="03aff-277">In diesem [GitHub-Problem 4076](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) finden Sie Anweisungen zum Hinzufügen von Kommas als Dezimaltrennzeichen.</span><span class="sxs-lookup"><span data-stu-id="03aff-277">This [GitHub issue 4076](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) for instructions on adding decimal comma.</span></span>

<span data-ttu-id="03aff-278">Die Eigenschaft `Layout` wird in der Datei *Pages/_ViewStart.cshtml* festgelegt:</span><span class="sxs-lookup"><span data-stu-id="03aff-278">The `Layout` property is set in the *Pages/_ViewStart.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Pages/_ViewStart.cshtml)]

<span data-ttu-id="03aff-279">Das vorhergehende Markup legt die Layoutdatei für alle :::no-loc(Razor):::-Dateien unter dem Ordner *Pages* auf *Pages/Shared/_Layout.cshtml* fest.</span><span class="sxs-lookup"><span data-stu-id="03aff-279">The preceding markup sets the layout file to *Pages/Shared/_Layout.cshtml* for all :::no-loc(Razor)::: files under the *Pages* folder.</span></span> <span data-ttu-id="03aff-280">Weitere Informationen finden Sie unter [Layout](xref:razor-pages/index#layout).</span><span class="sxs-lookup"><span data-stu-id="03aff-280">See [Layout](xref:razor-pages/index#layout) for more information.</span></span>

### <a name="the-create-page-model"></a><span data-ttu-id="03aff-281">Das Seitenmodell „Create“</span><span class="sxs-lookup"><span data-stu-id="03aff-281">The Create page model</span></span>

<span data-ttu-id="03aff-282">Überprüfen Sie das Seitenmodell *Pages/Movies/Create.cshtml.cs* :</span><span class="sxs-lookup"><span data-stu-id="03aff-282">Examine the *Pages/Movies/Create.cshtml.cs* page model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample/:::no-loc(Razor):::PagesMovie/Pages/Movies/Create.cshtml.cs?name=snippetALL)]

<span data-ttu-id="03aff-283">Die Methode `OnGet` initialisiert einen beliebigen Zustand, der für die Seite erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="03aff-283">The `OnGet` method initializes any state needed for the page.</span></span> <span data-ttu-id="03aff-284">Die Seite „Create“ (Erstellen) verfügt nicht über einen initialisierbaren Zustand. Deshalb wird `Page` zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="03aff-284">The Create page doesn't have any state to initialize, so `Page` is returned.</span></span> <span data-ttu-id="03aff-285">Im späteren Verlauf des Tutorials initialisiert die Methode `OnGet` einen Zustand.</span><span class="sxs-lookup"><span data-stu-id="03aff-285">Later in the tutorial you see `OnGet` method initialize state.</span></span> <span data-ttu-id="03aff-286">Die Methode `Page` erstellt ein `PageResult`-Objekt, das die Seite *Create.cshtml* rendert.</span><span class="sxs-lookup"><span data-stu-id="03aff-286">The `Page` method creates a `PageResult` object that renders the *Create.cshtml* page.</span></span>

<span data-ttu-id="03aff-287">Die Eigenschaft `Movie` verwendet das `[BindProperty]`-Attribut, um die [Modellbindung](xref:mvc/models/model-binding) zu aktivieren.</span><span class="sxs-lookup"><span data-stu-id="03aff-287">The `Movie` property uses the `[BindProperty]` attribute to opt-in to [model binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="03aff-288">Wenn das Formular „Create“ die Formularwerte bereitstellt, bindet die ASP.NET Core-Laufzeit die bereitgestellten Werte an das `Movie`-Modell.</span><span class="sxs-lookup"><span data-stu-id="03aff-288">When the Create form posts the form values, the ASP.NET Core runtime binds the posted values to the `Movie` model.</span></span>

<span data-ttu-id="03aff-289">Die Methode `OnPostAsync` wird ausgeführt, wenn die Seite Formulardaten bereitstellt:</span><span class="sxs-lookup"><span data-stu-id="03aff-289">The `OnPostAsync` method is run when the page posts form data:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample/:::no-loc(Razor):::PagesMovie/Pages/Movies/Create.cshtml.cs?name=snippetPost)]

<span data-ttu-id="03aff-290">Wenn es zu Modellfehlern kommt, wird das Formular mit allen bereitgestellten Formulardaten erneut angezeigt.</span><span class="sxs-lookup"><span data-stu-id="03aff-290">If there are any model errors, the form is redisplayed, along with any form data posted.</span></span> <span data-ttu-id="03aff-291">Die meisten Modellfehler können clientseitig abgefangen werden, bevor das Formular bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="03aff-291">Most model errors can be caught on the client-side before the form is posted.</span></span> <span data-ttu-id="03aff-292">Ein Beispiel für einen Modellfehler ist die Bereitstellung eines Werts für das Datumsfeld, der nicht in ein Datum konvertiert werden kann.</span><span class="sxs-lookup"><span data-stu-id="03aff-292">An example of a model error is posting a value for the date field that cannot be converted to a date.</span></span> <span data-ttu-id="03aff-293">Die clientseitige Validierung und die Modellvalidierung werden später in diesem Tutorial erläutert.</span><span class="sxs-lookup"><span data-stu-id="03aff-293">Client-side validation and model validation are discussed later in the tutorial.</span></span>

<span data-ttu-id="03aff-294">Wenn keine Modellfehler auftreten, werden die Daten gespeichert und der Browser zur Indexseite umgeleitet.</span><span class="sxs-lookup"><span data-stu-id="03aff-294">If there are no model errors, the data is saved, and the browser is redirected to the Index page.</span></span>

### <a name="the-create-no-locrazor-page"></a><span data-ttu-id="03aff-295">:::no-loc(Razor):::-Seite „Erstellen“</span><span class="sxs-lookup"><span data-stu-id="03aff-295">The Create :::no-loc(Razor)::: Page</span></span>

<span data-ttu-id="03aff-296">Betrachten Sie die Datei *Pages/Movies/Create.cshtml* der :::no-loc(Razor):::-Seite:</span><span class="sxs-lookup"><span data-stu-id="03aff-296">Examine the *Pages/Movies/Create.cshtml* :::no-loc(Razor)::: Page file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/:::no-loc(Razor):::PagesMovie/Pages/Movies/Create.cshtml)]

# <a name="visual-studio"></a>[<span data-ttu-id="03aff-297">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="03aff-297">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="03aff-298">Visual Studio zeigt das `<form method="post">`-Tag in einer bestimmten Schriftart an, die für Taghilfsprogramme verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="03aff-298">Visual Studio displays the `<form method="post">` tag in a distinctive bold font used for Tag Helpers:</span></span>

![VS17-Ansicht der Create.cshtml-Seite](page/_static/th.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="03aff-300">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="03aff-300">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="03aff-301">Weitere Informationen zu Taghilfsprogrammen wie `<form method="post">` finden Sie unter [Taghilfsprogramme in ASP.NET Core](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="03aff-301">For more information on Tag Helpers such as `<form method="post">`, see [Tag Helpers in ASP.NET Core](xref:mvc/views/tag-helpers/intro).</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="03aff-302">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="03aff-302">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="03aff-303">Visual Studio für Mac zeigt das `<form method="post">`-Tag in einer bestimmten Schriftart an, die für Taghilfsprogramme verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="03aff-303">Visual Studio for Mac displays the `<form method="post">` tag in a distinctive bold font used for Tag Helpers.</span></span>

---

<span data-ttu-id="03aff-304">Das Element `<form method="post">` ist ein [Form Tag Helper (Hilfsprogramm für Formulartags)](xref:mvc/views/working-with-forms#the-form-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="03aff-304">The `<form method="post">` element is a [Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper).</span></span> <span data-ttu-id="03aff-305">Das Hilfsprogramm für Formulartags enthält automatisch ein [antiforgery token (Antifälschungstoken)](xref:security/anti-request-forgery).</span><span class="sxs-lookup"><span data-stu-id="03aff-305">The Form Tag Helper automatically includes an [antiforgery token](xref:security/anti-request-forgery).</span></span>

<span data-ttu-id="03aff-306">Die Gerüstbau-Engine erstellt :::no-loc(Razor):::-Markup für jedes Feld im Modell (ausgenommen die ID) ähnlich wie folgt:</span><span class="sxs-lookup"><span data-stu-id="03aff-306">The scaffolding engine creates :::no-loc(Razor)::: markup for each field in the model (except the ID) similar to the following:</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/:::no-loc(Razor):::PagesMovie/Pages/Movies/Create.cshtml?range=15-20)]

<span data-ttu-id="03aff-307">Das [Hilfsprogramm für Validierungstags](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` und `<span asp-validation-for`) zeigt Validierungsfehler.</span><span class="sxs-lookup"><span data-stu-id="03aff-307">The [Validation Tag Helpers](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` and `<span asp-validation-for`) display validation errors.</span></span> <span data-ttu-id="03aff-308">Die Validierung wird an späterer Stelle in dieser Reihe detaillierter beschrieben.</span><span class="sxs-lookup"><span data-stu-id="03aff-308">Validation is covered in more detail later in this series.</span></span>

<span data-ttu-id="03aff-309">Das [Label Tag Helper (Taghilfsprogramm für Label)](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) generiert den Titel des Labels und das `for`-Attribut für die Eigenschaft `Title`.</span><span class="sxs-lookup"><span data-stu-id="03aff-309">The [Label Tag Helper](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) generates the label caption and `for` attribute for the `Title` property.</span></span>

<span data-ttu-id="03aff-310">Das [Hilfsprogramm für Eingabetags](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control">`) verwendet die Attribute von [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) und generiert HTML-Attribute, die auf der Clientseite für die jQuery-Validierung erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="03aff-310">The [Input Tag Helper](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control">`) uses the [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) attributes and produces HTML attributes needed for jQuery Validation on the client-side.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="03aff-311">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="03aff-311">Additional resources</span></span>

* [<span data-ttu-id="03aff-312">Dieses Tutorial auf YouTube</span><span class="sxs-lookup"><span data-stu-id="03aff-312">YouTube version of this tutorial</span></span>](https://youtu.be/zxgKjPYnOMM)

> [!div class="step-by-step"]
> <span data-ttu-id="03aff-313">[Zurück: Hinzufügen eines Modells](xref:tutorials/razor-pages/model)
> [Nächster Schritt: Datenbank](xref:tutorials/razor-pages/sql)</span><span class="sxs-lookup"><span data-stu-id="03aff-313">[Previous: Adding a model](xref:tutorials/razor-pages/model)
[Next: Database](xref:tutorials/razor-pages/sql)</span></span>

::: moniker-end
