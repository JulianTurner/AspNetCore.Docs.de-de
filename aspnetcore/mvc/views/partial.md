---
title: Verwenden von Teilansichten in ASP.NET Core
author: ardalis
description: Erfahren Sie, wie Sie Teilansichten verwenden können, um große Markupdateien aufzuteilen und die Duplizierung von allgemeinem Markup auf Webseiten in ASP.NET Core-Anwendungen zu verringern.
ms.author: riande
ms.custom: mvc
ms.date: 06/12/2019
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
uid: mvc/views/partial
ms.openlocfilehash: 01fb87205f7855f0995cbcd135b6b01f15835e3b
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060598"
---
# <a name="partial-views-in-aspnet-core"></a><span data-ttu-id="1805a-103">Verwenden von Teilansichten in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1805a-103">Partial views in ASP.NET Core</span></span>

<span data-ttu-id="1805a-104">Von [Steve Smith](https://ardalis.com/), [Maher JENDOUBI](https://twitter.com/maherjend), [Rick Anderson](https://twitter.com/RickAndMSFT) und [Scott Sauber](https://twitter.com/scottsauber)</span><span class="sxs-lookup"><span data-stu-id="1805a-104">By [Steve Smith](https://ardalis.com/), [Maher JENDOUBI](https://twitter.com/maherjend), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Scott Sauber](https://twitter.com/scottsauber)</span></span>

<span data-ttu-id="1805a-105">Eine Teilansicht ist eine [:::no-loc(Razor):::](xref:mvc/views/razor) Markup Datei ( *. cshtml* ) ohne eine- [`@page`](xref:mvc/views/razor#page) Direktive, die die HTML-Ausgabe *innerhalb* der gerenderten Ausgabe einer anderen Markup Datei rendert.</span><span class="sxs-lookup"><span data-stu-id="1805a-105">A partial view is a [:::no-loc(Razor):::](xref:mvc/views/razor) markup file ( *.cshtml* ) without an [`@page`](xref:mvc/views/razor#page) directive that renders HTML output *within* another markup file's rendered output.</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="1805a-106">Der Begriff " *Teilansicht* " wird verwendet, wenn eine MVC-app entwickelt wird, in der Markup Dateien als *Sichten* bezeichnet werden, oder eine :::no-loc(Razor)::: pages-APP, bei der Markup Dateien als *Seiten* bezeichnet werden.</span><span class="sxs-lookup"><span data-stu-id="1805a-106">The term *partial view* is used when developing either an MVC app, where markup files are called *views* , or a :::no-loc(Razor)::: Pages app, where markup files are called *pages* .</span></span> <span data-ttu-id="1805a-107">Dieses Thema verweist generisch auf MVC-Ansichten und :::no-loc(Razor)::: Seiten Seiten als *Markup Dateien* .</span><span class="sxs-lookup"><span data-stu-id="1805a-107">This topic generically refers to MVC views and :::no-loc(Razor)::: Pages pages as *markup files* .</span></span>

::: moniker-end

<span data-ttu-id="1805a-108">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/partial/sample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="1805a-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/partial/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-partial-views"></a><span data-ttu-id="1805a-109">Wann werden Teilansichten verwendet?</span><span class="sxs-lookup"><span data-stu-id="1805a-109">When to use partial views</span></span>

<span data-ttu-id="1805a-110">Teilansichten bieten eine effektive Möglichkeit, um Folgendes zu erreichen:</span><span class="sxs-lookup"><span data-stu-id="1805a-110">Partial views are an effective way to:</span></span>

* <span data-ttu-id="1805a-111">Aufteilen großer Markupdateien in kleinere Komponenten.</span><span class="sxs-lookup"><span data-stu-id="1805a-111">Break up large markup files into smaller components.</span></span>

  <span data-ttu-id="1805a-112">In einer großen, komplexen Markupdatei, die aus mehreren logischen Teilen besteht, bringt es Vorteile mit sich, mit jeder einzelnen Komponente isoliert in einer Teilansicht zu arbeiten.</span><span class="sxs-lookup"><span data-stu-id="1805a-112">In a large, complex markup file composed of several logical pieces, there's an advantage to working with each piece isolated into a partial view.</span></span> <span data-ttu-id="1805a-113">Der Code in der Markupdatei ist verwaltbar, weil das Markup nur die allgemeine Seitenstruktur und Verweise auf Teilansichten enthält.</span><span class="sxs-lookup"><span data-stu-id="1805a-113">The code in the markup file is manageable because the markup only contains the overall page structure and references to partial views.</span></span>
* <span data-ttu-id="1805a-114">Verringern der Duplizierung von allgemeinem Markupinhalt in Markupdateien.</span><span class="sxs-lookup"><span data-stu-id="1805a-114">Reduce the duplication of common markup content across markup files.</span></span>

  <span data-ttu-id="1805a-115">Wenn die gleichen Markupelemente in verschiedenen Markupdateien verwendet werden, entfernt eine Teilansicht die Duplizierung von Markupinhalten in einer Teilansichtsdatei.</span><span class="sxs-lookup"><span data-stu-id="1805a-115">When the same markup elements are used across markup files, a partial view removes the duplication of markup content into one partial view file.</span></span> <span data-ttu-id="1805a-116">Wenn das Markup in der Teilansicht geändert wird, wird die gerenderte Ausgabe der Markupdateien aktualisiert, die die Teilansicht verwenden.</span><span class="sxs-lookup"><span data-stu-id="1805a-116">When the markup is changed in the partial view, it updates the rendered output of the markup files that use the partial view.</span></span>

<span data-ttu-id="1805a-117">Teilansichten sollte nicht verwendet werden, um allgemeine Layoutelemente zu verwalten.</span><span class="sxs-lookup"><span data-stu-id="1805a-117">Partial views shouldn't be used to maintain common layout elements.</span></span> <span data-ttu-id="1805a-118">Häufig verwendete Layoutelemente müssen in [_Layout.cshtml](xref:mvc/views/layout)-Dateien angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="1805a-118">Common layout elements should be specified in [_Layout.cshtml](xref:mvc/views/layout) files.</span></span>

<span data-ttu-id="1805a-119">Verwenden Sie keine Teilansicht, wenn für das Rendern des Markups eine komplexe Renderinglogik oder Codeausführung erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="1805a-119">Don't use a partial view where complex rendering logic or code execution is required to render the markup.</span></span> <span data-ttu-id="1805a-120">Verwenden Sie anstelle einer Teilansicht eine [Ansichtskomponente](xref:mvc/views/view-components).</span><span class="sxs-lookup"><span data-stu-id="1805a-120">Instead of a partial view, use a [view component](xref:mvc/views/view-components).</span></span>

## <a name="declare-partial-views"></a><span data-ttu-id="1805a-121">Deklarieren von Teilansichten</span><span class="sxs-lookup"><span data-stu-id="1805a-121">Declare partial views</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="1805a-122">Eine Teilansicht ist eine *cshtml* -Markup Datei ohne eine- [`@page`](xref:mvc/views/razor#page) Direktive, die im Ordner *views* (MVC) oder im Ordner *pages* ( :::no-loc(Razor)::: Seiten) beibehalten wird.</span><span class="sxs-lookup"><span data-stu-id="1805a-122">A partial view is a *.cshtml* markup file without an [`@page`](xref:mvc/views/razor#page) directive maintained within the *Views* folder (MVC) or *Pages* folder (:::no-loc(Razor)::: Pages).</span></span>

<span data-ttu-id="1805a-123">In ASP.NET Core MVC kann <xref:Microsoft.AspNetCore.Mvc.ViewResult> eines Controllers eine Ansicht oder eine Teilansicht zurückgeben.</span><span class="sxs-lookup"><span data-stu-id="1805a-123">In ASP.NET Core MVC, a controller's <xref:Microsoft.AspNetCore.Mvc.ViewResult> is capable of returning either a view or a partial view.</span></span> <span data-ttu-id="1805a-124">In :::no-loc(Razor)::: Seiten kann eine <xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::Pages.PageModel> eine partielle Ansicht zurückgeben, die als-Objekt dargestellt wird <xref:Microsoft.AspNetCore.Mvc.PartialViewResult> .</span><span class="sxs-lookup"><span data-stu-id="1805a-124">In :::no-loc(Razor)::: Pages, a <xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::Pages.PageModel> can return a partial view represented as a <xref:Microsoft.AspNetCore.Mvc.PartialViewResult> object.</span></span> <span data-ttu-id="1805a-125">Das Verweisen auf und Rendern von Teilansichten wird im Abschnitt [Verweisen auf eine Teilansicht](#reference-a-partial-view) beschrieben.</span><span class="sxs-lookup"><span data-stu-id="1805a-125">Referencing and rendering partial views is described in the [Reference a partial view](#reference-a-partial-view) section.</span></span>

<span data-ttu-id="1805a-126">Im Gegensatz zu MVC-Ansichten oder Seitenrendering führt eine Teilansicht *_ViewStart.cshtml* nicht aus.</span><span class="sxs-lookup"><span data-stu-id="1805a-126">Unlike MVC view or page rendering, a partial view doesn't run *_ViewStart.cshtml* .</span></span> <span data-ttu-id="1805a-127">Weitere Informationen zu *_ViewStart.cshtml* finden Sie unter <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="1805a-127">For more information on *_ViewStart.cshtml* , see <xref:mvc/views/layout>.</span></span>

<span data-ttu-id="1805a-128">Dateinamen von Teilansichten beginnen häufig mit einem Unterstrich (`_`).</span><span class="sxs-lookup"><span data-stu-id="1805a-128">Partial view file names often begin with an underscore (`_`).</span></span> <span data-ttu-id="1805a-129">Diese Namenskonvention ist keine Voraussetzung, sie hilft aber dabei, Teilansichten von Ansichten und Seiten visuell zu unterscheiden.</span><span class="sxs-lookup"><span data-stu-id="1805a-129">This naming convention isn't required, but it helps to visually differentiate partial views from views and pages.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="1805a-130">Eine Teilansicht ist eine *CSHTML* -Markupdatei, die innerhalb des Ordners *Views* verwaltet wird.</span><span class="sxs-lookup"><span data-stu-id="1805a-130">A partial view is a *.cshtml* markup file maintained within the *Views* folder.</span></span>

<span data-ttu-id="1805a-131"><xref:Microsoft.AspNetCore.Mvc.ViewResult> eines Controllers kann eine Ansicht oder eine Teilansicht zurückgeben.</span><span class="sxs-lookup"><span data-stu-id="1805a-131">A controller's <xref:Microsoft.AspNetCore.Mvc.ViewResult> is capable of returning either a view or a partial view.</span></span> <span data-ttu-id="1805a-132">Das Verweisen auf und Rendern von Teilansichten wird im Abschnitt [Verweisen auf eine Teilansicht](#reference-a-partial-view) beschrieben.</span><span class="sxs-lookup"><span data-stu-id="1805a-132">Referencing and rendering partial views is described in the [Reference a partial view](#reference-a-partial-view) section.</span></span>

<span data-ttu-id="1805a-133">Im Gegensatz zu MVC-Ansichten oder Rendering führt eine Teilansicht *_ViewStart.cshtml* nicht aus.</span><span class="sxs-lookup"><span data-stu-id="1805a-133">Unlike MVC view rendering, a partial view doesn't run *_ViewStart.cshtml* .</span></span> <span data-ttu-id="1805a-134">Weitere Informationen zu *_ViewStart.cshtml* finden Sie unter <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="1805a-134">For more information on *_ViewStart.cshtml* , see <xref:mvc/views/layout>.</span></span>

<span data-ttu-id="1805a-135">Dateinamen von Teilansichten beginnen häufig mit einem Unterstrich (`_`).</span><span class="sxs-lookup"><span data-stu-id="1805a-135">Partial view file names often begin with an underscore (`_`).</span></span> <span data-ttu-id="1805a-136">Diese Namenskonvention ist keine Voraussetzung, sie hilft aber dabei, Teilansichten von Ansichten visuell zu unterscheiden.</span><span class="sxs-lookup"><span data-stu-id="1805a-136">This naming convention isn't required, but it helps to visually differentiate partial views from views.</span></span>

::: moniker-end

## <a name="reference-a-partial-view"></a><span data-ttu-id="1805a-137">Verweisen auf eine Teilansicht</span><span class="sxs-lookup"><span data-stu-id="1805a-137">Reference a partial view</span></span>

::: moniker range=">= aspnetcore-2.0"

### <a name="use-a-partial-view-in-a-no-locrazor-pages-pagemodel"></a><span data-ttu-id="1805a-138">Verwenden einer Teilansicht in :::no-loc(Razor)::: Seiten Modellen für Seiten</span><span class="sxs-lookup"><span data-stu-id="1805a-138">Use a partial view in a :::no-loc(Razor)::: Pages PageModel</span></span>

<span data-ttu-id="1805a-139">In ASP.net Core 2,0 oder 2,1 rendert die folgende Handlermethode die Teilansicht *\_ authorpartialrp. cshtml* für die Antwort:</span><span class="sxs-lookup"><span data-stu-id="1805a-139">In ASP.NET Core 2.0 or 2.1, the following handler method renders the *\_AuthorPartialRP.cshtml* partial view to the response:</span></span>

```csharp
public IActionResult OnGetPartial() =>
    new PartialViewResult
    {
        ViewName = "_AuthorPartialRP",
        ViewData = ViewData,
    };
```

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="1805a-140">In ASP.NET Core 2.2 oder höher kann eine Handlermethode alternativ die <xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::Pages.PageBase.Partial*>-Methode aufrufen, um ein `PartialViewResult`-Objekt zu erzeugen:</span><span class="sxs-lookup"><span data-stu-id="1805a-140">In ASP.NET Core 2.2 or later, a handler method can alternatively call the <xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::Pages.PageBase.Partial*> method to produce a `PartialViewResult` object:</span></span>

[!code-csharp[](partial/sample/PartialViewsSample/Pages/DiscoveryRP.cshtml.cs?name=snippet_OnGetPartial)]

::: moniker-end

### <a name="use-a-partial-view-in-a-markup-file"></a><span data-ttu-id="1805a-141">Verwenden einer Teilansicht in einer Markupdatei</span><span class="sxs-lookup"><span data-stu-id="1805a-141">Use a partial view in a markup file</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="1805a-142">Innerhalb einer Markupdatei gibt es mehrere Möglichkeiten, auf eine Teilansicht zu verweisen.</span><span class="sxs-lookup"><span data-stu-id="1805a-142">Within a markup file, there are several ways to reference a partial view.</span></span> <span data-ttu-id="1805a-143">Es wird empfohlen, dass Apps einen der folgenden Ansätze für asynchrones Rendering verwenden:</span><span class="sxs-lookup"><span data-stu-id="1805a-143">We recommend that apps use one of the following asynchronous rendering approaches:</span></span>

* [<span data-ttu-id="1805a-144">Hilfsprogramm für Teiltags</span><span class="sxs-lookup"><span data-stu-id="1805a-144">Partial Tag Helper</span></span>](#partial-tag-helper)
* [<span data-ttu-id="1805a-145">Asynchrones HTML-Hilfsprogramm</span><span class="sxs-lookup"><span data-stu-id="1805a-145">Asynchronous HTML Helper</span></span>](#asynchronous-html-helper)

::: moniker-end

::: moniker range="< aspnetcore-2.1"

<span data-ttu-id="1805a-146">Innerhalb einer Markupdatei gibt es zwei Möglichkeiten, auf eine Teilansicht zu verweisen:</span><span class="sxs-lookup"><span data-stu-id="1805a-146">Within a markup file, there are two ways to reference a partial view:</span></span>

* [<span data-ttu-id="1805a-147">Asynchrones HTML-Hilfsprogramm</span><span class="sxs-lookup"><span data-stu-id="1805a-147">Asynchronous HTML Helper</span></span>](#asynchronous-html-helper)
* [<span data-ttu-id="1805a-148">Synchrones HTML-Hilfsprogramm</span><span class="sxs-lookup"><span data-stu-id="1805a-148">Synchronous HTML Helper</span></span>](#synchronous-html-helper)

<span data-ttu-id="1805a-149">Es wird empfohlen, dass Apps das [asynchrone HTML-Hilfsprogramm](#asynchronous-html-helper) verwenden.</span><span class="sxs-lookup"><span data-stu-id="1805a-149">We recommend that apps use the [Asynchronous HTML Helper](#asynchronous-html-helper).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

### <a name="partial-tag-helper"></a><span data-ttu-id="1805a-150">Hilfsprogramm für Teiltags</span><span class="sxs-lookup"><span data-stu-id="1805a-150">Partial Tag Helper</span></span>

<span data-ttu-id="1805a-151">Das [Hilfsprogramm für Teiltags](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper) erfordert ASP.NET Core 2.1 oder höher.</span><span class="sxs-lookup"><span data-stu-id="1805a-151">The [Partial Tag Helper](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper) requires ASP.NET Core 2.1 or later.</span></span>

<span data-ttu-id="1805a-152">Das Hilfsprogramm für Teiltags rendert Inhalte asynchron und verwendet eine HTML-ähnliche Syntax:</span><span class="sxs-lookup"><span data-stu-id="1805a-152">The Partial Tag Helper renders content asynchronously and uses an HTML-like syntax:</span></span>

```cshtml
<partial name="_PartialName" />
```

<span data-ttu-id="1805a-153">Wenn eine Dateierweiterung vorhanden ist, verweist das Hilfsprogramm für Teiltags auf eine Teilansicht, die sich im gleichen Ordner wie die Markupdatei befinden muss, die die Teilansicht aufruft:</span><span class="sxs-lookup"><span data-stu-id="1805a-153">When a file extension is present, the Tag Helper references a partial view that must be in the same folder as the markup file calling the partial view:</span></span>

```cshtml
<partial name="_PartialName.cshtml" />
```

<span data-ttu-id="1805a-154">Im folgende Beispiel wird aus dem Stammverzeichnis der App auf eine Teilansicht verwiesen.</span><span class="sxs-lookup"><span data-stu-id="1805a-154">The following example references a partial view from the app root.</span></span> <span data-ttu-id="1805a-155">Pfade, die mit einer Tilde und einem Schrägstrich (`~/`) oder einem Schrägstrich (`/`) beginnen, verweisen auf den Stamm der App:</span><span class="sxs-lookup"><span data-stu-id="1805a-155">Paths that start with a tilde-slash (`~/`) or a slash (`/`) refer to the app root:</span></span>

<span data-ttu-id="1805a-156">**:::no-loc(Razor)::: Seiten**</span><span class="sxs-lookup"><span data-stu-id="1805a-156">**:::no-loc(Razor)::: Pages**</span></span>

```cshtml
<partial name="~/Pages/Folder/_PartialName.cshtml" />
<partial name="/Pages/Folder/_PartialName.cshtml" />
```

<span data-ttu-id="1805a-157">**MVC**</span><span class="sxs-lookup"><span data-stu-id="1805a-157">**MVC**</span></span>

```cshtml
<partial name="~/Views/Folder/_PartialName.cshtml" />
<partial name="/Views/Folder/_PartialName.cshtml" />
```

<span data-ttu-id="1805a-158">Das folgende Beispiel verweist auf eine Teilansicht mit einem relativen Pfad:</span><span class="sxs-lookup"><span data-stu-id="1805a-158">The following example references a partial view with a relative path:</span></span>

```cshtml
<partial name="../Account/_PartialName.cshtml" />
```

<span data-ttu-id="1805a-159">Weitere Informationen finden Sie unter <xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="1805a-159">For more information, see <xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper>.</span></span>

::: moniker-end

### <a name="asynchronous-html-helper"></a><span data-ttu-id="1805a-160">Hilfsprogramm für asynchrone HTML</span><span class="sxs-lookup"><span data-stu-id="1805a-160">Asynchronous HTML Helper</span></span>

<span data-ttu-id="1805a-161">Wenn Sie ein HTML-Hilfsprogramm verwenden, stellt das Verwenden von <xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.PartialAsync*> eine bewährte Methode dar.</span><span class="sxs-lookup"><span data-stu-id="1805a-161">When using an HTML Helper, the best practice is to use <xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.PartialAsync*>.</span></span> <span data-ttu-id="1805a-162">`PartialAsync` gibt einen <xref:Microsoft.AspNetCore.Html.IHtmlContent>-Typ in einem <xref:System.Threading.Tasks.Task%601> als Wrapper zurück.</span><span class="sxs-lookup"><span data-stu-id="1805a-162">`PartialAsync` returns an <xref:Microsoft.AspNetCore.Html.IHtmlContent> type wrapped in a <xref:System.Threading.Tasks.Task%601>.</span></span> <span data-ttu-id="1805a-163">Sie können auf die Methode verweisen, indem Sie dem erwarteten Aufruf das Zeichen `@` als Präfix voranstellen:</span><span class="sxs-lookup"><span data-stu-id="1805a-163">The method is referenced by prefixing the awaited call with an `@` character:</span></span>

```cshtml
@await Html.PartialAsync("_PartialName")
```

<span data-ttu-id="1805a-164">Wenn die Dateierweiterung vorhanden ist, verweist das HTML-Hilfsprogramm auf eine Teilansicht, die sich im gleichen Ordner wie die Markupdatei befinden muss, die die Teilansicht aufruft:</span><span class="sxs-lookup"><span data-stu-id="1805a-164">When the file extension is present, the HTML Helper references a partial view that must be in the same folder as the markup file calling the partial view:</span></span>

```cshtml
@await Html.PartialAsync("_PartialName.cshtml")
```

<span data-ttu-id="1805a-165">Im folgende Beispiel wird aus dem Stammverzeichnis der App auf eine Teilansicht verwiesen.</span><span class="sxs-lookup"><span data-stu-id="1805a-165">The following example references a partial view from the app root.</span></span> <span data-ttu-id="1805a-166">Pfade, die mit einer Tilde und einem Schrägstrich (`~/`) oder einem Schrägstrich (`/`) beginnen, verweisen auf den Stamm der App:</span><span class="sxs-lookup"><span data-stu-id="1805a-166">Paths that start with a tilde-slash (`~/`) or a slash (`/`) refer to the app root:</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="1805a-167">**:::no-loc(Razor)::: Seiten**</span><span class="sxs-lookup"><span data-stu-id="1805a-167">**:::no-loc(Razor)::: Pages**</span></span>

```cshtml
@await Html.PartialAsync("~/Pages/Folder/_PartialName.cshtml")
@await Html.PartialAsync("/Pages/Folder/_PartialName.cshtml")
```

<span data-ttu-id="1805a-168">**MVC**</span><span class="sxs-lookup"><span data-stu-id="1805a-168">**MVC**</span></span>

::: moniker-end

```cshtml
@await Html.PartialAsync("~/Views/Folder/_PartialName.cshtml")
@await Html.PartialAsync("/Views/Folder/_PartialName.cshtml")
```

<span data-ttu-id="1805a-169">Das folgende Beispiel verweist auf eine Teilansicht mit einem relativen Pfad:</span><span class="sxs-lookup"><span data-stu-id="1805a-169">The following example references a partial view with a relative path:</span></span>

```cshtml
@await Html.PartialAsync("../Account/_LoginPartial.cshtml")
```

<span data-ttu-id="1805a-170">Alternativ können Sie eine Teilansicht auch mit <xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.RenderPartialAsync*> rendern.</span><span class="sxs-lookup"><span data-stu-id="1805a-170">Alternatively, you can render a partial view with <xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.RenderPartialAsync*>.</span></span> <span data-ttu-id="1805a-171">Diese Methode gibt keinen <xref:Microsoft.AspNetCore.Html.IHtmlContent> zurück.</span><span class="sxs-lookup"><span data-stu-id="1805a-171">This method doesn't return an <xref:Microsoft.AspNetCore.Html.IHtmlContent>.</span></span> <span data-ttu-id="1805a-172">Sie streamt die gerenderte Ausgabe direkt an die Antwort.</span><span class="sxs-lookup"><span data-stu-id="1805a-172">It streams the rendered output directly to the response.</span></span> <span data-ttu-id="1805a-173">Da die Methode kein Ergebnis zurückgibt, muss Sie in einem :::no-loc(Razor)::: Codeblock aufgerufen werden:</span><span class="sxs-lookup"><span data-stu-id="1805a-173">Because the method doesn't return a result, it must be called within a :::no-loc(Razor)::: code block:</span></span>

[!code-cshtml[](partial/sample/PartialViewsSample/Views/Home/Discovery.cshtml?name=snippet_RenderPartialAsync)]

<span data-ttu-id="1805a-174">Da `RenderPartialAsync` gerenderte Inhalte streamt, bietet diese Vorgehensweise in einigen Szenarien eine bessere Leistung.</span><span class="sxs-lookup"><span data-stu-id="1805a-174">Since `RenderPartialAsync` streams rendered content, it provides better performance in some scenarios.</span></span> <span data-ttu-id="1805a-175">In leistungskritischen Situationen sollten Sie die Seite mit beiden Ansätzen einem Benchmarktest unterziehen und den Ansatz verwenden, der eine schnellere Antwort generiert.</span><span class="sxs-lookup"><span data-stu-id="1805a-175">In performance-critical situations, benchmark the page using both approaches and use the approach that generates a faster response.</span></span>

### <a name="synchronous-html-helper"></a><span data-ttu-id="1805a-176">Hilfsprogramm für synchrone HTML</span><span class="sxs-lookup"><span data-stu-id="1805a-176">Synchronous HTML Helper</span></span>

<span data-ttu-id="1805a-177"><xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.Partial*> und <xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.RenderPartial*> sind die synchronen Entsprechungen von `PartialAsync` bzw. `RenderPartialAsync`.</span><span class="sxs-lookup"><span data-stu-id="1805a-177"><xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.Partial*> and <xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.RenderPartial*> are the synchronous equivalents of `PartialAsync` and `RenderPartialAsync`, respectively.</span></span> <span data-ttu-id="1805a-178">Diese synchronen Entsprechungen werden nicht empfohlen, da sie in manchen Szenarien zu einem Deadlock führen können.</span><span class="sxs-lookup"><span data-stu-id="1805a-178">The synchronous equivalents aren't recommended because there are scenarios in which they deadlock.</span></span> <span data-ttu-id="1805a-179">Die synchronen Methoden sollen in einem zukünftigen Release entfernt werden.</span><span class="sxs-lookup"><span data-stu-id="1805a-179">The synchronous methods are targeted for removal in a future release.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="1805a-180">Wenn Sie Code ausführen müssen, verwenden Sie anstelle von Teilansichten [Ansichtskomponenten](xref:mvc/views/view-components).</span><span class="sxs-lookup"><span data-stu-id="1805a-180">If you need to execute code, use a [view component](xref:mvc/views/view-components) instead of a partial view.</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="1805a-181">Das Aufrufen von `Partial` oder `RenderPartial` führt zu einer Visual Studio Analyzer-Warnung.</span><span class="sxs-lookup"><span data-stu-id="1805a-181">Calling `Partial` or `RenderPartial` results in a Visual Studio analyzer warning.</span></span> <span data-ttu-id="1805a-182">Beispielsweise führt das Vorhandensein von `Partial` zu folgender Warnmeldung:</span><span class="sxs-lookup"><span data-stu-id="1805a-182">For example, the presence of `Partial` yields the following warning message:</span></span>

> <span data-ttu-id="1805a-183">Die Verwendung von IHtmlHelper.Partial kann zu Anwendungsdeadlocks führen.</span><span class="sxs-lookup"><span data-stu-id="1805a-183">Use of IHtmlHelper.Partial may result in application deadlocks.</span></span> <span data-ttu-id="1805a-184">Erwägen Sie die Verwendung des Hilfsprogramms für &lt;Teiltags&gt; oder von IHtmlHelper.PartialAsync.</span><span class="sxs-lookup"><span data-stu-id="1805a-184">Consider using &lt;partial&gt; Tag Helper or IHtmlHelper.PartialAsync.</span></span>

<span data-ttu-id="1805a-185">Ersetzen Sie Aufrufe von `@Html.Partial` durch `@await Html.PartialAsync` oder durch das [Hilfsprogramm für Teiltags](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="1805a-185">Replace calls to `@Html.Partial` with `@await Html.PartialAsync` or the [Partial Tag Helper](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper).</span></span> <span data-ttu-id="1805a-186">Weiter Informationen zur Migration des Teiltaghilfsprogramms finden Sie unter [Migrate from an HTML Helper (Migration von einem HTML-Hilfsprogramm)](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper#migrate-from-an-html-helper)</span><span class="sxs-lookup"><span data-stu-id="1805a-186">For more information on Partial Tag Helper migration, see [Migrate from an HTML Helper](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper#migrate-from-an-html-helper).</span></span>

::: moniker-end

## <a name="partial-view-discovery"></a><span data-ttu-id="1805a-187">Ermittlung von Teilansichten</span><span class="sxs-lookup"><span data-stu-id="1805a-187">Partial view discovery</span></span>

<span data-ttu-id="1805a-188">Wenn auf eine Teilansicht anhand des Namens ohne eine Dateierweiterung verwiesen wird, werden die folgenden Speicherorte in der angegebenen Reihenfolge durchsucht:</span><span class="sxs-lookup"><span data-stu-id="1805a-188">When a partial view is referenced by name without a file extension, the following locations are searched in the stated order:</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="1805a-189">**:::no-loc(Razor)::: Seiten**</span><span class="sxs-lookup"><span data-stu-id="1805a-189">**:::no-loc(Razor)::: Pages**</span></span>

1. <span data-ttu-id="1805a-190">Der Ordner der aktuell ausgeführten Seite</span><span class="sxs-lookup"><span data-stu-id="1805a-190">Currently executing page's folder</span></span>
1. <span data-ttu-id="1805a-191">Der Verzeichnisgraph über dem Ordner der Seite</span><span class="sxs-lookup"><span data-stu-id="1805a-191">Directory graph above the page's folder</span></span>
1. `/Shared`
1. `/Pages/Shared`
1. `/Views/Shared`

<span data-ttu-id="1805a-192">**MVC**</span><span class="sxs-lookup"><span data-stu-id="1805a-192">**MVC**</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

1. `/Areas/<Area-Name>/Views/<Controller-Name>`
1. `/Areas/<Area-Name>/Views/Shared`
1. `/Views/Shared`
1. `/Pages/Shared`

::: moniker-end

::: moniker range="< aspnetcore-2.0"

1. `/Areas/<Area-Name>/Views/<Controller-Name>`
1. `/Areas/<Area-Name>/Views/Shared`
1. `/Views/Shared`

::: moniker-end

<span data-ttu-id="1805a-193">Für die Ermittlung von Teilansichten gelten die folgenden Konventionen:</span><span class="sxs-lookup"><span data-stu-id="1805a-193">The following conventions apply to partial view discovery:</span></span>

* <span data-ttu-id="1805a-194">Unterschiedliche Teilansichten mit dem gleichen Dateinamen sind zulässig, wenn sich die Teilansichten in verschiedenen Ordnern befinden.</span><span class="sxs-lookup"><span data-stu-id="1805a-194">Different partial views with the same file name are allowed when the partial views are in different folders.</span></span>
* <span data-ttu-id="1805a-195">Wenn auf eine Teilansicht über den Namen ohne Dateierweiterung verwiesen wird und die Teilansicht sowohl im Ordner des Aufrufers als auch im Ordner *Shared* vorhanden ist, stellt die Teilansicht im Ordner des Aufrufers die Teilansicht bereit.</span><span class="sxs-lookup"><span data-stu-id="1805a-195">When referencing a partial view by name without a file extension and the partial view is present in both the caller's folder and the *Shared* folder, the partial view in the caller's folder supplies the partial view.</span></span> <span data-ttu-id="1805a-196">Wenn die Teilansicht nicht im Ordner des Aufrufers vorhanden ist, wird die Teilansicht aus dem Ordner *Shared* bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="1805a-196">If the partial view isn't present in the caller's folder, the partial view is provided from the *Shared* folder.</span></span> <span data-ttu-id="1805a-197">Teilansichten im Ordner *Shared* werden als *Freigegebene Teilansichten* oder *Standardteilansichten* bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="1805a-197">Partial views in the *Shared* folder are called *shared partial views* or *default partial views* .</span></span>
* <span data-ttu-id="1805a-198">Teilansichten können *verkettet* werden &mdash; . eine partielle Ansicht kann eine andere partielle Sicht aufrufen, wenn ein Zirkel Verweis nicht durch die Aufrufe gebildet wird.</span><span class="sxs-lookup"><span data-stu-id="1805a-198">Partial views can be *chained*&mdash;a partial view can call another partial view if a circular reference isn't formed by the calls.</span></span> <span data-ttu-id="1805a-199">Relative Pfade sind immer relativ zur aktuellen Datei, nicht zum Stamm- oder übergeordneten Verzeichnis der Datei.</span><span class="sxs-lookup"><span data-stu-id="1805a-199">Relative paths are always relative to the current file, not to the root or parent of the file.</span></span>

> [!NOTE]
> <span data-ttu-id="1805a-200">Ein [:::no-loc(Razor):::](xref:mvc/views/razor) `section` , der in einer Teilansicht definiert ist, ist für übergeordnete Markup Dateien nicht sichtbar.</span><span class="sxs-lookup"><span data-stu-id="1805a-200">A [:::no-loc(Razor):::](xref:mvc/views/razor) `section` defined in a partial view is invisible to parent markup files.</span></span> <span data-ttu-id="1805a-201">Die `section`-Anweisung ist nur für die Teilansicht, in der sie definiert ist, sichtbar.</span><span class="sxs-lookup"><span data-stu-id="1805a-201">The `section` is only visible to the partial view in which it's defined.</span></span>

## <a name="access-data-from-partial-views"></a><span data-ttu-id="1805a-202">Zugriff auf Daten aus Teilansichten</span><span class="sxs-lookup"><span data-stu-id="1805a-202">Access data from partial views</span></span>

<span data-ttu-id="1805a-203">Wenn eine Teilansicht instanziiert wird, erhält sie eine *Kopie* des `ViewData`-Wörterbuchs der übergeordneten Ansicht.</span><span class="sxs-lookup"><span data-stu-id="1805a-203">When a partial view is instantiated, it receives a *copy* of the parent's `ViewData` dictionary.</span></span> <span data-ttu-id="1805a-204">An den Daten vorgenommene Änderungen in der Teilansicht werden in der übergeordneten Ansicht nicht beibehalten.</span><span class="sxs-lookup"><span data-stu-id="1805a-204">Updates made to the data within the partial view aren't persisted to the parent view.</span></span> <span data-ttu-id="1805a-205">Ein `ViewData`-Wörterbuch, das in einer Teilansicht verändert wird, geht verloren, wenn die Teilansicht eine Rückgabe ausgibt.</span><span class="sxs-lookup"><span data-stu-id="1805a-205">`ViewData` changes in a partial view are lost when the partial view returns.</span></span>

<span data-ttu-id="1805a-206">Das folgende Beispiel zeigt, wie eine Instanz von [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) an eine Teilansicht übergeben wird:</span><span class="sxs-lookup"><span data-stu-id="1805a-206">The following example demonstrates how to pass an instance of [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) to a partial view:</span></span>

```cshtml
@await Html.PartialAsync("_PartialName", customViewData)
```

<span data-ttu-id="1805a-207">Außerdem können Sie ein Modell an eine Teilansicht übergeben.</span><span class="sxs-lookup"><span data-stu-id="1805a-207">You can pass a model into a partial view.</span></span> <span data-ttu-id="1805a-208">Das Modell kann ein benutzerdefiniertes Objekt sein.</span><span class="sxs-lookup"><span data-stu-id="1805a-208">The model can be a custom object.</span></span> <span data-ttu-id="1805a-209">Sie können ein Modell mit `PartialAsync` (rendert einen Inhaltsblock für den Aufrufer) oder `RenderPartialAsync` (streamt den Inhalt in die Ausgabe) übergeben:</span><span class="sxs-lookup"><span data-stu-id="1805a-209">You can pass a model with `PartialAsync` (renders a block of content to the caller) or `RenderPartialAsync` (streams the content to the output):</span></span>

```cshtml
@await Html.PartialAsync("_PartialName", model)
```

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="1805a-210">**:::no-loc(Razor)::: Seiten**</span><span class="sxs-lookup"><span data-stu-id="1805a-210">**:::no-loc(Razor)::: Pages**</span></span>

<span data-ttu-id="1805a-211">Das folgende Markup in der Beispiel-App stammt von der Seite *Pages/ArticlesRP/ReadRP.cshtml* .</span><span class="sxs-lookup"><span data-stu-id="1805a-211">The following markup in the sample app is from the *Pages/ArticlesRP/ReadRP.cshtml* page.</span></span> <span data-ttu-id="1805a-212">Diese Seite enthält zwei Teilansichten.</span><span class="sxs-lookup"><span data-stu-id="1805a-212">The page contains two partial views.</span></span> <span data-ttu-id="1805a-213">Die zweite Teilansicht übergibt ein Modell und `ViewData` an die Teilansicht.</span><span class="sxs-lookup"><span data-stu-id="1805a-213">The second partial view passes in a model and `ViewData` to the partial view.</span></span> <span data-ttu-id="1805a-214">Die `ViewDataDictionary`-Konstruktorüberladung wird verwendet, um ein neues `ViewData`-Wörterbuch zu übergeben, während das vorhandene `ViewData`-Wörterbuch beibehalten wird.</span><span class="sxs-lookup"><span data-stu-id="1805a-214">The `ViewDataDictionary` constructor overload is used to pass a new `ViewData` dictionary while retaining the existing `ViewData` dictionary.</span></span>

[!code-cshtml[](partial/sample/PartialViewsSample/Pages/ArticlesRP/ReadRP.cshtml?name=snippet_ReadPartialViewRP&highlight=5,15-20)]

<span data-ttu-id="1805a-215">*Pages/Shared/_AuthorPartialRP.cshtml* ist die erste Teilansicht, auf die durch die Markupdatei *ReadRP.cshtml* verwiesen wird:</span><span class="sxs-lookup"><span data-stu-id="1805a-215">*Pages/Shared/_AuthorPartialRP.cshtml* is the first partial view referenced by the *ReadRP.cshtml* markup file:</span></span>

[!code-cshtml[](partial/sample/PartialViewsSample/Pages/Shared/_AuthorPartialRP.cshtml)]

<span data-ttu-id="1805a-216">*Pages/ArticlesRP/_ArticleSectionRP.cshtml* ist die zweite Teilansicht, auf die durch die Markupdatei *ReadRP.cshtml* verwiesen wird:</span><span class="sxs-lookup"><span data-stu-id="1805a-216">*Pages/ArticlesRP/_ArticleSectionRP.cshtml* is the second partial view referenced by the *ReadRP.cshtml* markup file:</span></span>

[!code-cshtml[](partial/sample/PartialViewsSample/Pages/ArticlesRP/_ArticleSectionRP.cshtml)]

<span data-ttu-id="1805a-217">**MVC**</span><span class="sxs-lookup"><span data-stu-id="1805a-217">**MVC**</span></span>

::: moniker-end

<span data-ttu-id="1805a-218">Das folgende Markup in der Beispiel-App zeigt die Ansicht *Views/Articles/Read.cshtml* .</span><span class="sxs-lookup"><span data-stu-id="1805a-218">The following markup in the sample app shows the *Views/Articles/Read.cshtml* view.</span></span> <span data-ttu-id="1805a-219">Die Ansicht enthält die zwei Teilansichten.</span><span class="sxs-lookup"><span data-stu-id="1805a-219">The view contains two partial views.</span></span> <span data-ttu-id="1805a-220">Die zweite Teilansicht übergibt ein Modell und `ViewData` an die Teilansicht.</span><span class="sxs-lookup"><span data-stu-id="1805a-220">The second partial view passes in a model and `ViewData` to the partial view.</span></span> <span data-ttu-id="1805a-221">Die `ViewDataDictionary`-Konstruktorüberladung wird verwendet, um ein neues `ViewData`-Wörterbuch zu übergeben, während das vorhandene `ViewData`-Wörterbuch beibehalten wird.</span><span class="sxs-lookup"><span data-stu-id="1805a-221">The `ViewDataDictionary` constructor overload is used to pass a new `ViewData` dictionary while retaining the existing `ViewData` dictionary.</span></span>

[!code-cshtml[](partial/sample/PartialViewsSample/Views/Articles/Read.cshtml?name=snippet_ReadPartialView&highlight=5,15-20)]

<span data-ttu-id="1805a-222">*Views/Shared/_AuthorPartial.cshtml* ist die erste Teilansicht, auf die durch die Markupdatei *Read.cshtml* verwiesen wird:</span><span class="sxs-lookup"><span data-stu-id="1805a-222">*Views/Shared/_AuthorPartial.cshtml* is the first partial view referenced by the *Read.cshtml* markup file:</span></span>

[!code-cshtml[](partial/sample/PartialViewsSample/Views/Shared/_AuthorPartial.cshtml)]

<span data-ttu-id="1805a-223">*Views/Articles/_ArticleSection.cshtml* ist die zweite Teilansicht, auf die durch die Markupdatei *ReadRP.cshtml* verwiesen wird:</span><span class="sxs-lookup"><span data-stu-id="1805a-223">*Views/Articles/_ArticleSection.cshtml* is the second partial view referenced by the *Read.cshtml* markup file:</span></span>

[!code-cshtml[](partial/sample/PartialViewsSample/Views/Articles/_ArticleSection.cshtml)]

<span data-ttu-id="1805a-224">Die Teilansichten werden zur Laufzeit in der gerenderten Ausgabe der übergeordneten Markupdatei gerendert, die wiederum in der freigegebenen Datei *_Layout.cshtml* gerendert wird.</span><span class="sxs-lookup"><span data-stu-id="1805a-224">At runtime, the partials are rendered into the parent markup file's rendered output, which itself is rendered within the shared *_Layout.cshtml* .</span></span> <span data-ttu-id="1805a-225">Die erste Teilansicht rendert den Namen des Artikelautors und das Erscheinungsdatum:</span><span class="sxs-lookup"><span data-stu-id="1805a-225">The first partial view renders the article author's name and publication date:</span></span>

> <span data-ttu-id="1805a-226">Abraham Lincoln</span><span class="sxs-lookup"><span data-stu-id="1805a-226">Abraham Lincoln</span></span>
>
> <span data-ttu-id="1805a-227">Diese Teilansicht aus dem &lt;Dateipfad der freigegebenen Teilansicht&gt;.</span><span class="sxs-lookup"><span data-stu-id="1805a-227">This partial view from &lt;shared partial view file path&gt;.</span></span>
> <span data-ttu-id="1805a-228">11/19/1863 12:00:00 AM</span><span class="sxs-lookup"><span data-stu-id="1805a-228">11/19/1863 12:00:00 AM</span></span>

<span data-ttu-id="1805a-229">Die zweite Teilansicht rendert die Abschnitte des Artikels:</span><span class="sxs-lookup"><span data-stu-id="1805a-229">The second partial view renders the article's sections:</span></span>

> <span data-ttu-id="1805a-230">Section One Index: 0</span><span class="sxs-lookup"><span data-stu-id="1805a-230">Section One Index: 0</span></span>
>
> <span data-ttu-id="1805a-231">Four score and seven years ago ...</span><span class="sxs-lookup"><span data-stu-id="1805a-231">Four score and seven years ago ...</span></span>
>
> <span data-ttu-id="1805a-232">Section Two Index: 1</span><span class="sxs-lookup"><span data-stu-id="1805a-232">Section Two Index: 1</span></span>
>
> <span data-ttu-id="1805a-233">Now we are engaged in a great civil war, testing ...</span><span class="sxs-lookup"><span data-stu-id="1805a-233">Now we are engaged in a great civil war, testing ...</span></span>
>
> <span data-ttu-id="1805a-234">Section Three Index: 2</span><span class="sxs-lookup"><span data-stu-id="1805a-234">Section Three Index: 2</span></span>
>
> <span data-ttu-id="1805a-235">But, in a larger sense, we can not dedicate ...</span><span class="sxs-lookup"><span data-stu-id="1805a-235">But, in a larger sense, we can not dedicate ...</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1805a-236">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="1805a-236">Additional resources</span></span>

::: moniker range=">= aspnetcore-2.1"

* <xref:mvc/views/razor>
* <xref:mvc/views/tag-helpers/intro>
* <xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper>
* <xref:mvc/views/view-components>
* <xref:mvc/controllers/areas>

::: moniker-end

::: moniker range="< aspnetcore-2.1"

* <xref:mvc/views/razor>
* <xref:mvc/views/view-components>
* <xref:mvc/controllers/areas>

::: moniker-end
