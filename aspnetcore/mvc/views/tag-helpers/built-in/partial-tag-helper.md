---
title: Hilfsprogramm für Teiltags in ASP.NET Core
author: scottaddie
description: Lernen Sie das ASP.NET Core-Hilfsprogramm für Teiltags und die Rolle seiner Attribute beim Rendern einer Teilansicht kennen.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 04/06/2019
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
uid: mvc/views/tag-helpers/builtin-th/partial-tag-helper
ms.openlocfilehash: 124f23caa4a757f63a80dfea627304204ba2cdca
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061430"
---
# <a name="partial-tag-helper-in-aspnet-core"></a><span data-ttu-id="42709-103">Hilfsprogramm für Teiltags in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="42709-103">Partial Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="42709-104">Von [Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="42709-104">By [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="42709-105">Eine Übersicht der Taghilfsprogramme finden Sie unter <xref:mvc/views/tag-helpers/intro>.</span><span class="sxs-lookup"><span data-stu-id="42709-105">For an overview of Tag Helpers, see <xref:mvc/views/tag-helpers/intro>.</span></span>

<span data-ttu-id="42709-106">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/tag-helpers/built-in/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="42709-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/tag-helpers/built-in/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview"></a><span data-ttu-id="42709-107">Überblick</span><span class="sxs-lookup"><span data-stu-id="42709-107">Overview</span></span>

<span data-ttu-id="42709-108">Das Hilfsprogramm für partielle Tags wird zum Rendern einer [Teilansicht](xref:mvc/views/partial) in :::no-loc(Razor)::: Seiten und MVC-Apps verwendet.</span><span class="sxs-lookup"><span data-stu-id="42709-108">The Partial Tag Helper is used for rendering a [partial view](xref:mvc/views/partial) in :::no-loc(Razor)::: Pages and MVC apps.</span></span> <span data-ttu-id="42709-109">Bedenken Sie dabei Folgendes:</span><span class="sxs-lookup"><span data-stu-id="42709-109">Consider that it:</span></span>

* <span data-ttu-id="42709-110">Das Programm erfordert ASP.NET Core 2.1 oder höher.</span><span class="sxs-lookup"><span data-stu-id="42709-110">Requires ASP.NET Core 2.1 or later.</span></span>
* <span data-ttu-id="42709-111">Es stellt eine Alternative zur [Syntax des HTML-Hilfsprogramms](xref:mvc/views/partial#reference-a-partial-view) dar.</span><span class="sxs-lookup"><span data-stu-id="42709-111">Is an alternative to [HTML Helper syntax](xref:mvc/views/partial#reference-a-partial-view).</span></span>
* <span data-ttu-id="42709-112">Es rendert die Teilansicht asynchron.</span><span class="sxs-lookup"><span data-stu-id="42709-112">Renders the partial view asynchronously.</span></span>

<span data-ttu-id="42709-113">Folgende zählen zu den Optionen des HTML-Hilfsprogramms für das Rendern einer Teilansicht:</span><span class="sxs-lookup"><span data-stu-id="42709-113">The HTML Helper options for rendering a partial view include:</span></span>

* [`@await Html.PartialAsync`](/dotnet/api/microsoft.aspnetcore.mvc.rendering.htmlhelperpartialextensions.partialasync)
* [`@await Html.RenderPartialAsync`](/dotnet/api/microsoft.aspnetcore.mvc.rendering.htmlhelperpartialextensions.renderpartialasync)
* [`@Html.Partial`](/dotnet/api/microsoft.aspnetcore.mvc.rendering.htmlhelperpartialextensions.partial)
* [`@Html.RenderPartial`](/dotnet/api/microsoft.aspnetcore.mvc.rendering.htmlhelperpartialextensions.renderpartial)

<span data-ttu-id="42709-114">Das Modell *Product* wird in den Beispielen in diesem Dokument verwendet:</span><span class="sxs-lookup"><span data-stu-id="42709-114">The *Product* model is used in samples throughout this document:</span></span>

[!code-csharp[](samples/TagHelpersBuiltIn/Models/Product.cs)]

<span data-ttu-id="42709-115">Eine Auflistung der im Hilfsprogramm für Teiltags enthaltenen Attribute folgt.</span><span class="sxs-lookup"><span data-stu-id="42709-115">An inventory of the Partial Tag Helper attributes follows.</span></span>

## <a name="name"></a><span data-ttu-id="42709-116">name</span><span class="sxs-lookup"><span data-stu-id="42709-116">name</span></span>

<span data-ttu-id="42709-117">Das `name`-Attribut ist erforderlich.</span><span class="sxs-lookup"><span data-stu-id="42709-117">The `name` attribute is required.</span></span> <span data-ttu-id="42709-118">Es gibt den Namen oder den Pfad der Teilansicht an, die gerendert werden soll.</span><span class="sxs-lookup"><span data-stu-id="42709-118">It indicates the name or the path of the partial view to be rendered.</span></span> <span data-ttu-id="42709-119">Wenn der Name einer Teilansicht bereitgestellt wird, wird der Prozess [Ansichtsermittlung](xref:mvc/views/overview#view-discovery) initiiert.</span><span class="sxs-lookup"><span data-stu-id="42709-119">When a partial view name is provided, the [view discovery](xref:mvc/views/overview#view-discovery) process is initiated.</span></span> <span data-ttu-id="42709-120">Dieser Prozess wird umgangen, wenn ein expliziter Pfad bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="42709-120">That process is bypassed when an explicit path is provided.</span></span> <span data-ttu-id="42709-121">Eine Übersicht über alle verfügbaren `name`-Werte finden Sie unter [Ermitteln von Teilansichten](xref:mvc/views/partial#partial-view-discovery).</span><span class="sxs-lookup"><span data-stu-id="42709-121">For all acceptable `name` values, see [Partial view discovery](xref:mvc/views/partial#partial-view-discovery).</span></span>

<span data-ttu-id="42709-122">Folgendes Markup verwendet einen expliziten Pfad, der angibt, dass *_ProductPartial.cshtml* aus dem Ordner *Shared* (Freigegeben) geladen werden soll.</span><span class="sxs-lookup"><span data-stu-id="42709-122">The following markup uses an explicit path, indicating that *_ProductPartial.cshtml* is to be loaded from the *Shared* folder.</span></span> <span data-ttu-id="42709-123">Wenn Sie das Attribut [for](#for) verwenden, wird an Modell zur Bindung an die Teilansicht übergeben.</span><span class="sxs-lookup"><span data-stu-id="42709-123">Using the [for](#for) attribute, a model is passed to the partial view for binding.</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Product.cshtml?name=snippet_Name)]

## <a name="for"></a><span data-ttu-id="42709-124">für</span><span class="sxs-lookup"><span data-stu-id="42709-124">for</span></span>

<span data-ttu-id="42709-125">Das `for`-Attribut weist ein [ModelExpression](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.modelexpression)-Element zu, das für das aktuelle Modell ausgewertet werden soll.</span><span class="sxs-lookup"><span data-stu-id="42709-125">The `for` attribute assigns a [ModelExpression](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.modelexpression) to be evaluated against the current model.</span></span> <span data-ttu-id="42709-126">Ein `ModelExpression`-Element leitet die `@Model.`-Syntax ab.</span><span class="sxs-lookup"><span data-stu-id="42709-126">A `ModelExpression` infers the `@Model.` syntax.</span></span> <span data-ttu-id="42709-127">`for="Product"` kann beispielsweise anstelle von `for="@Model.Product"` verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="42709-127">For example, `for="Product"` can be used instead of `for="@Model.Product"`.</span></span> <span data-ttu-id="42709-128">Dieses Standardverhalten für die Ableitung kann überschrieben werden, indem Sie das `@`-Symbol zum Definieren eines Inlineausdrucks verwenden.</span><span class="sxs-lookup"><span data-stu-id="42709-128">This default inference behavior is overridden by using the `@` symbol to define an inline expression.</span></span>

<span data-ttu-id="42709-129">Folgendes Markup lädt *_ProductPartial.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="42709-129">The following markup loads *_ProductPartial.cshtml* :</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Product.cshtml?name=snippet_For)]

<span data-ttu-id="42709-130">Die Teilansicht ist an die `Product`-Eigenschaft des zugehörigen Seitenmodells gebunden:</span><span class="sxs-lookup"><span data-stu-id="42709-130">The partial view is bound to the associated page model's `Product` property:</span></span>

[!code-csharp[](samples/TagHelpersBuiltIn/Pages/Product.cshtml.cs?highlight=8)]

## <a name="model"></a><span data-ttu-id="42709-131">model</span><span class="sxs-lookup"><span data-stu-id="42709-131">model</span></span>

<span data-ttu-id="42709-132">Das `model`-Attribut weist eine Modellinstanz zu, die an die Teilansicht übergeben werden soll.</span><span class="sxs-lookup"><span data-stu-id="42709-132">The `model` attribute assigns a model instance to pass to the partial view.</span></span> <span data-ttu-id="42709-133">Das `model`-Attribut kann nicht mit dem [for](#for)-Attribut verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="42709-133">The `model` attribute can't be used with the [for](#for) attribute.</span></span>

<span data-ttu-id="42709-134">Im folgenden Markup wird ein neues `Product`-Objekt instanziiert und an das `model`-Attribut zur Bindung übergeben:</span><span class="sxs-lookup"><span data-stu-id="42709-134">In the following markup, a new `Product` object is instantiated and passed to the `model` attribute for binding:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Product.cshtml?name=snippet_Model)]

## <a name="view-data"></a><span data-ttu-id="42709-135">view-data</span><span class="sxs-lookup"><span data-stu-id="42709-135">view-data</span></span>

<span data-ttu-id="42709-136">Das `view-data`-Attribut weist ein [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary)-Element zu, das an die Teilansicht übergeben werden soll.</span><span class="sxs-lookup"><span data-stu-id="42709-136">The `view-data` attribute assigns a [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) to pass to the partial view.</span></span> <span data-ttu-id="42709-137">Folgendes Markup stellt die gesamte ViewData-Auflistung für die Teilansicht zur Verfügung:</span><span class="sxs-lookup"><span data-stu-id="42709-137">The following markup makes the entire ViewData collection accessible to the partial view:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Product.cshtml?name=snippet_ViewData&highlight=5-)]

<span data-ttu-id="42709-138">Im vorangehenden Code wird der Schlüsselwert `IsNumberReadOnly` auf `true` festgelegt und zur ViewData-Auflistung hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="42709-138">In the preceding code, the `IsNumberReadOnly` key value is set to `true` and added to the ViewData collection.</span></span> <span data-ttu-id="42709-139">Folglich wird `ViewData["IsNumberReadOnly"]` innerhalb der folgenden Teilansicht zur Verfügung gestellt:</span><span class="sxs-lookup"><span data-stu-id="42709-139">Consequently, `ViewData["IsNumberReadOnly"]` is made accessible within the following partial view:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Shared/_ProductViewDataPartial.cshtml?highlight=5)]

<span data-ttu-id="42709-140">In diesem Beispiel bestimmt der Wert von `ViewData["IsNumberReadOnly"]`, ob das Feld *Number* (Anzahl) schreibgeschützt sein soll.</span><span class="sxs-lookup"><span data-stu-id="42709-140">In this example, the value of `ViewData["IsNumberReadOnly"]` determines whether the *Number* field is displayed as read only.</span></span>

## <a name="migrate-from-an-html-helper"></a><span data-ttu-id="42709-141">Migrieren von einem HTML-Hilfsprogramm</span><span class="sxs-lookup"><span data-stu-id="42709-141">Migrate from an HTML Helper</span></span>

<span data-ttu-id="42709-142">Sehen Sie sich das folgende Beispiel für das Hilfsprogramm für asynchrone HTML an.</span><span class="sxs-lookup"><span data-stu-id="42709-142">Consider the following asynchronous HTML Helper example.</span></span> <span data-ttu-id="42709-143">Eine Sammlung von Produkten wird durchlaufen und dargestellt.</span><span class="sxs-lookup"><span data-stu-id="42709-143">A collection of products is iterated and displayed.</span></span> <span data-ttu-id="42709-144">Laut dem ersten Parameter der `PartialAsync`-Methode wird die Teilansicht *_ProductPartial.cshtml* geladen.</span><span class="sxs-lookup"><span data-stu-id="42709-144">Per the `PartialAsync` method's first parameter, the *_ProductPartial.cshtml* partial view is loaded.</span></span> <span data-ttu-id="42709-145">Eine Instanz des `Product`-Modells wird für die Bindung an die Teilansicht übergeben.</span><span class="sxs-lookup"><span data-stu-id="42709-145">An instance of the `Product` model is passed to the partial view for binding.</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Products.cshtml?name=snippet_HtmlHelper&highlight=3)]

<span data-ttu-id="42709-146">Das folgende Teiltaghilfsprogramm erreicht dasselbe asynchrone Renderingverhalten wie das `PartialAsync`-HTML-Hilfsprogramm.</span><span class="sxs-lookup"><span data-stu-id="42709-146">The following Partial Tag Helper achieves the same asynchronous rendering behavior as the `PartialAsync` HTML Helper.</span></span> <span data-ttu-id="42709-147">Dem `model`-Attribut wird eine `Product`-Modellinstanz zum Binden an die Teilansicht zugewiesen.</span><span class="sxs-lookup"><span data-stu-id="42709-147">The `model` attribute is assigned a `Product` model instance for binding to the partial view.</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Products.cshtml?name=snippet_TagHelper&highlight=3)]

## <a name="additional-resources"></a><span data-ttu-id="42709-148">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="42709-148">Additional resources</span></span>

* <xref:mvc/views/partial>
* <xref:mvc/views/overview#weakly-typed-data-viewdata-viewdata-attribute-and-viewbag>
