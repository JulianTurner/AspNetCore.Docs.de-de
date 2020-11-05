---
title: Filtermethoden für :::no-loc(Razor):::-Seiten in ASP-NET Core
author: Rick-Anderson
description: Erfahren Sie, wie Sie Filtermethoden für :::no-loc(Razor):::-Seiten in ASP.NET Core erstellen.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 2/18/2020
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
uid: razor-pages/filter
ms.openlocfilehash: a6d25c1b88e09560c1aad9aefd9148f7fe293909
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93056828"
---
# <a name="filter-methods-for-no-locrazor-pages-in-aspnet-core"></a><span data-ttu-id="7073c-103">Filtermethoden für :::no-loc(Razor):::-Seiten in ASP-NET Core</span><span class="sxs-lookup"><span data-stu-id="7073c-103">Filter methods for :::no-loc(Razor)::: Pages in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="7073c-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="7073c-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="7073c-105">Eine :::no-loc(Razor):::-Seite filtert [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0). [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) erlaubt :::no-loc(Razor):::-Seiten Code auszuführen, bevor und nachdem ein Handler für :::no-loc(Razor):::-Seiten ausgeführt wurde.</span><span class="sxs-lookup"><span data-stu-id="7073c-105">:::no-loc(Razor)::: Page filters [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) and [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) allow :::no-loc(Razor)::: Pages to run code before and after a :::no-loc(Razor)::: Page handler is run.</span></span> <span data-ttu-id="7073c-106">Filter für :::no-loc(Razor):::-Seiten ähneln [ASP.NET Core MVC-Aktionsfiltern](xref:mvc/controllers/filters#action-filters), können allerdings nicht auf einzelne Seitenhandlermethoden angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="7073c-106">:::no-loc(Razor)::: Page filters are similar to [ASP.NET Core MVC action filters](xref:mvc/controllers/filters#action-filters), except they can't be applied to individual page handler methods.</span></span>

<span data-ttu-id="7073c-107">Filter für :::no-loc(Razor)::: Pages:</span><span class="sxs-lookup"><span data-stu-id="7073c-107">:::no-loc(Razor)::: Page filters:</span></span>

* <span data-ttu-id="7073c-108">Führen Code aus, nachdem eine Handlermethode ausgewählt wurde, aber bevor die Modellbindung erfolgt</span><span class="sxs-lookup"><span data-stu-id="7073c-108">Run code after a handler method has been selected, but before model binding occurs.</span></span>
* <span data-ttu-id="7073c-109">Führen Code aus, bevor die Handlermethode ausgeführt wird, nachdem die Modellbindung abgeschlossen ist</span><span class="sxs-lookup"><span data-stu-id="7073c-109">Run code before the handler method executes, after model binding is complete.</span></span>
* <span data-ttu-id="7073c-110">Führen Code aus, nachdem die Handlermethode ausgeführt wird</span><span class="sxs-lookup"><span data-stu-id="7073c-110">Run code after the handler method executes.</span></span>
* <span data-ttu-id="7073c-111">Können auf einer Seite oder global implementiert werden</span><span class="sxs-lookup"><span data-stu-id="7073c-111">Can be implemented on a page or globally.</span></span>
* <span data-ttu-id="7073c-112">Können nicht auf seitenspezifische Handlermethoden angewendet werden</span><span class="sxs-lookup"><span data-stu-id="7073c-112">Cannot be applied to specific page handler methods.</span></span>
* <span data-ttu-id="7073c-113">Alle Konstruktorabhängigkeiten werden durch die [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) aufgefüllt.</span><span class="sxs-lookup"><span data-stu-id="7073c-113">Can have constructor dependencies populated by [Dependency Injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="7073c-114">Weitere Informationen finden Sie unter [ServiceFilterAttribute](../mvc/controllers/filters.md#servicefilterattribute) und [TypeFilterAttribute](../mvc/controllers/filters.md#typefilterattribute).</span><span class="sxs-lookup"><span data-stu-id="7073c-114">For more information, see [ServiceFilterAttribute](../mvc/controllers/filters.md#servicefilterattribute) and [TypeFilterAttribute](../mvc/controllers/filters.md#typefilterattribute).</span></span>

<span data-ttu-id="7073c-115">Während Seitenkonstruktoren und Middleware die Ausführung von benutzerdefiniertem Code vor der Ausführung einer Handlermethode ermöglichen, gestatten nur :::no-loc(Razor)::: Page-Filter den Zugriff auf <xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::Pages.PageModel.HttpContext> und die Seite.</span><span class="sxs-lookup"><span data-stu-id="7073c-115">While page constructors and middleware enable executing custom code before a handler method executes, only :::no-loc(Razor)::: Page filters enable access to <xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::Pages.PageModel.HttpContext> and the page.</span></span> <span data-ttu-id="7073c-116">Middleware hat Zugriff auf den `HttpContext`, aber nicht auf den „Seitenkontext“.</span><span class="sxs-lookup"><span data-stu-id="7073c-116">Middleware has access to the `HttpContext`, but not to the "page context".</span></span> <span data-ttu-id="7073c-117">Filter verfügen über einen Parameter, der von <xref:Microsoft.AspNetCore.Mvc.Filters.FilterContext> abgeleitet ist, um Zugriff auf `HttpContext` zu ermöglichen.</span><span class="sxs-lookup"><span data-stu-id="7073c-117">Filters have a <xref:Microsoft.AspNetCore.Mvc.Filters.FilterContext> derived parameter, which provides access to `HttpContext`.</span></span> <span data-ttu-id="7073c-118">Im Folgenden finden Sie ein Beispiel für einen Seitenfilter: [Implementieren eines Filterattributs](#ifa) fügt der Antwort einen Header hinzu. Dies kann nicht über Konstruktoren oder Middleware erfolgen.</span><span class="sxs-lookup"><span data-stu-id="7073c-118">Here's a sample for a page filter: [Implement a filter attribute](#ifa) that adds a header to the response, something that can't be done with constructors or middleware.</span></span> <span data-ttu-id="7073c-119">Der Zugriff auf den Seitenkontext, der den Zugriff auf die Instanzen der Seite und ihr Modell enthält, ist nur verfügbar, wenn Filter, Handler oder der Text einer :::no-loc(Razor):::-Seite ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="7073c-119">Access to the page context, which includes access to the instances of the page and it's model, are only available when executing filters, handlers, or the body of a :::no-loc(Razor)::: Page.</span></span>

<span data-ttu-id="7073c-120">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/3.1sample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="7073c-120">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/3.1sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="7073c-121">Filter für :::no-loc(Razor):::-Seiten bieten die folgenden Methoden, die global oder auf Seitenebene angewendet werden können:</span><span class="sxs-lookup"><span data-stu-id="7073c-121">:::no-loc(Razor)::: Page filters provide the following methods, which can be applied globally or at the page level:</span></span>

* <span data-ttu-id="7073c-122">Synchrone Methoden:</span><span class="sxs-lookup"><span data-stu-id="7073c-122">Synchronous methods:</span></span>

  * <span data-ttu-id="7073c-123">[OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0): Wird aufgerufen, nachdem eine Handlermethode ausgewählt wurde, aber bevor die Modellbindung erfolgt.</span><span class="sxs-lookup"><span data-stu-id="7073c-123">[OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0) : Called after a handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="7073c-124">[OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0): Wird aufgerufen, bevor die Handlermethode ausgeführt wird, nachdem die Modellbindung abgeschlossen ist.</span><span class="sxs-lookup"><span data-stu-id="7073c-124">[OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0) : Called before the handler method executes, after model binding is complete.</span></span>
  * <span data-ttu-id="7073c-125">[OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0): Wird aufgerufen, nachdem die Handlermethode ausgeführt wird, bevor das Aktionsergebnis angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="7073c-125">[OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0) : Called after the handler method executes, before the action result.</span></span>

* <span data-ttu-id="7073c-126">Asynchrone Methoden:</span><span class="sxs-lookup"><span data-stu-id="7073c-126">Asynchronous methods:</span></span>

  * <span data-ttu-id="7073c-127">[OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0): Wird asynchron aufgerufen, nachdem die Handlermethode ausgewählt wurde, aber bevor die Modellbindung erfolgt.</span><span class="sxs-lookup"><span data-stu-id="7073c-127">[OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0) : Called asynchronously after the handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="7073c-128">[OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0): Wird asynchron aufgerufen, bevor die Handlermethode aufgerufen wird, nachdem die Modellbindung abgeschlossen ist.</span><span class="sxs-lookup"><span data-stu-id="7073c-128">[OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0) : Called asynchronously before the handler method is invoked, after model binding is complete.</span></span>

<span data-ttu-id="7073c-129">Implementieren Sie **entweder** die synchrone oder asynchrone Version einer Filterschnittstelle, aber **nicht** beide.</span><span class="sxs-lookup"><span data-stu-id="7073c-129">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="7073c-130">Das Framework prüft zuerst, ob der Filter die asynchrone Schnittstelle implementiert, und wenn dies der Fall ist, ruft es sie auf.</span><span class="sxs-lookup"><span data-stu-id="7073c-130">The framework checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="7073c-131">Wenn dies nicht der Fall ist, ruft es die Methode(n) der synchronen Schnittstelle auf.</span><span class="sxs-lookup"><span data-stu-id="7073c-131">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="7073c-132">Wenn beide Schnittstellen implementiert werden, werden nur die asynchronen Methoden aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="7073c-132">If both interfaces are implemented, only the async methods are called.</span></span> <span data-ttu-id="7073c-133">Die gleiche Regel gilt für Überschreibungen in Seiten. Implementieren Sie die synchrone oder asynchrone Version der Überschreibung, nicht beide.</span><span class="sxs-lookup"><span data-stu-id="7073c-133">The same rule applies to overrides in pages, implement the synchronous or the async version of the override, not both.</span></span>

## <a name="implement-no-locrazor-page-filters-globally"></a><span data-ttu-id="7073c-134">Globales implementieren von Filtern für :::no-loc(Razor):::-Seiten</span><span class="sxs-lookup"><span data-stu-id="7073c-134">Implement :::no-loc(Razor)::: Page filters globally</span></span>

<span data-ttu-id="7073c-135">Mit dem folgenden Code wird das `IAsyncPageFilter`-Element implementiert:</span><span class="sxs-lookup"><span data-stu-id="7073c-135">The following code implements `IAsyncPageFilter`:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Filters/SampleAsyncPageFilter.cs?name=snippet1)]

<span data-ttu-id="7073c-136">Im vorhergehenden Code ist `ProcessUserAgent.Write` der vom Benutzer bereitgestellte Code, der mit der Benutzer-Agent-Zeichenfolge arbeitet.</span><span class="sxs-lookup"><span data-stu-id="7073c-136">In the preceding code, `ProcessUserAgent.Write` is user supplied code that works with the user agent string.</span></span>

<span data-ttu-id="7073c-137">Der folgende Code aktiviert `SampleAsyncPageFilter` in der `Startup`-Klasse:</span><span class="sxs-lookup"><span data-stu-id="7073c-137">The following code enables the `SampleAsyncPageFilter` in the `Startup` class:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Startup.cs?name=snippet2)]

<span data-ttu-id="7073c-138">Der folgende Code ruft <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> auf, damit `SampleAsyncPageFilter` nur auf Seiten in */Movies* angewendet wird:</span><span class="sxs-lookup"><span data-stu-id="7073c-138">The following code calls <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> to apply the `SampleAsyncPageFilter` to only pages in */Movies* :</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Startup2.cs?name=snippet2)]

<span data-ttu-id="7073c-139">Im folgenden Code wird `IPageFilter` synchron implementiert:</span><span class="sxs-lookup"><span data-stu-id="7073c-139">The following code implements the synchronous `IPageFilter`:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Filters/SamplePageFilter.cs?name=snippet1)]

<span data-ttu-id="7073c-140">Der folgende Code aktiviert `SamplePageFilter`:</span><span class="sxs-lookup"><span data-stu-id="7073c-140">The following code enables the `SamplePageFilter`:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/StartupSync.cs?name=snippet2)]

## <a name="implement-no-locrazor-page-filters-by-overriding-filter-methods"></a><span data-ttu-id="7073c-141">Implementieren von Filtern für :::no-loc(Razor):::-Seiten durch Überschreiben von Filtermethoden</span><span class="sxs-lookup"><span data-stu-id="7073c-141">Implement :::no-loc(Razor)::: Page filters by overriding filter methods</span></span>

<span data-ttu-id="7073c-142">Der folgende Code überschreibt die asynchronen :::no-loc(Razor)::: Page-Filter:</span><span class="sxs-lookup"><span data-stu-id="7073c-142">The following code overrides the asynchronous :::no-loc(Razor)::: Page filters:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Pages/Index.cshtml.cs?name=snippet)]

<a name="ifa"></a>

## <a name="implement-a-filter-attribute"></a><span data-ttu-id="7073c-143">Implementieren eines Filterattributs</span><span class="sxs-lookup"><span data-stu-id="7073c-143">Implement a filter attribute</span></span>

<span data-ttu-id="7073c-144">Der integrierte attributbasierte Filter <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter.OnResultExecutionAsync*> kann als Unterklasse definiert werden.</span><span class="sxs-lookup"><span data-stu-id="7073c-144">The built-in attribute-based filter <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter.OnResultExecutionAsync*> filter can be subclassed.</span></span> <span data-ttu-id="7073c-145">Der folgende Filter fügt der Antwort einen Header hinzu:</span><span class="sxs-lookup"><span data-stu-id="7073c-145">The following filter adds a header to the response:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Filters/AddHeaderAttribute.cs)]

<span data-ttu-id="7073c-146">Der folgende Code wendet das Attribut `AddHeader` an:</span><span class="sxs-lookup"><span data-stu-id="7073c-146">The following code applies the `AddHeader` attribute:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Pages/Movies/Test.cshtml.cs)]

<span data-ttu-id="7073c-147">Verwenden Sie ein Tool wie die Browser-Entwicklertools, um die Header zu untersuchen.</span><span class="sxs-lookup"><span data-stu-id="7073c-147">Use a tool such as the browser developer tools to examine the headers.</span></span> <span data-ttu-id="7073c-148">Unter **Antwortheader** wird `author: Rick` angezeigt.</span><span class="sxs-lookup"><span data-stu-id="7073c-148">Under **Response Headers** , `author: Rick` is displayed.</span></span>

<span data-ttu-id="7073c-149">Informationen zum Überschreiben der Reihenfolge finden Sie unter [Überschreiben der Standardreihenfolge](xref:mvc/controllers/filters#overriding-the-default-order).</span><span class="sxs-lookup"><span data-stu-id="7073c-149">See [Overriding the default order](xref:mvc/controllers/filters#overriding-the-default-order) for instructions on overriding the order.</span></span>

<span data-ttu-id="7073c-150">Informationen zum Kurzschließen der Filterpipeline eines Filters finden Sie unter [Abbrechen und Kurzschließen](xref:mvc/controllers/filters#cancellation-and-short-circuiting).</span><span class="sxs-lookup"><span data-stu-id="7073c-150">See [Cancellation and short circuiting](xref:mvc/controllers/filters#cancellation-and-short-circuiting) for instructions to short-circuit the filter pipeline from a filter.</span></span>

<a name="auth"></a>

## <a name="authorize-filter-attribute"></a><span data-ttu-id="7073c-151">Autorisieren eines Filterattributs</span><span class="sxs-lookup"><span data-stu-id="7073c-151">Authorize filter attribute</span></span>

<span data-ttu-id="7073c-152">Das Attribut [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) kann auf ein `PageModel` angewendet werden:</span><span class="sxs-lookup"><span data-stu-id="7073c-152">The [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) attribute can be applied to a `PageModel`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/ModelWithAuthFilter.cshtml.cs?highlight=7)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="7073c-153">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="7073c-153">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="7073c-154">Eine :::no-loc(Razor):::-Seite filtert [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0). [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) erlaubt :::no-loc(Razor):::-Seiten Code auszuführen, bevor und nachdem ein Handler für :::no-loc(Razor):::-Seiten ausgeführt wurde.</span><span class="sxs-lookup"><span data-stu-id="7073c-154">:::no-loc(Razor)::: Page filters [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) and [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) allow :::no-loc(Razor)::: Pages to run code before and after a :::no-loc(Razor)::: Page handler is run.</span></span> <span data-ttu-id="7073c-155">Filter für :::no-loc(Razor):::-Seiten ähneln [ASP.NET Core MVC-Aktionsfiltern](xref:mvc/controllers/filters#action-filters), können allerdings nicht auf einzelne Seitenhandlermethoden angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="7073c-155">:::no-loc(Razor)::: Page filters are similar to [ASP.NET Core MVC action filters](xref:mvc/controllers/filters#action-filters), except they can't be applied to individual page handler methods.</span></span>

<span data-ttu-id="7073c-156">Filter für :::no-loc(Razor)::: Pages:</span><span class="sxs-lookup"><span data-stu-id="7073c-156">:::no-loc(Razor)::: Page filters:</span></span>

* <span data-ttu-id="7073c-157">Führen Code aus, nachdem eine Handlermethode ausgewählt wurde, aber bevor die Modellbindung erfolgt</span><span class="sxs-lookup"><span data-stu-id="7073c-157">Run code after a handler method has been selected, but before model binding occurs.</span></span>
* <span data-ttu-id="7073c-158">Führen Code aus, bevor die Handlermethode ausgeführt wird, nachdem die Modellbindung abgeschlossen ist</span><span class="sxs-lookup"><span data-stu-id="7073c-158">Run code before the handler method executes, after model binding is complete.</span></span>
* <span data-ttu-id="7073c-159">Führen Code aus, nachdem die Handlermethode ausgeführt wird</span><span class="sxs-lookup"><span data-stu-id="7073c-159">Run code after the handler method executes.</span></span>
* <span data-ttu-id="7073c-160">Können auf einer Seite oder global implementiert werden</span><span class="sxs-lookup"><span data-stu-id="7073c-160">Can be implemented on a page or globally.</span></span>
* <span data-ttu-id="7073c-161">Können nicht auf seitenspezifische Handlermethoden angewendet werden</span><span class="sxs-lookup"><span data-stu-id="7073c-161">Cannot be applied to specific page handler methods.</span></span>

<span data-ttu-id="7073c-162">Code kann ausgeführt werden, bevor eine Handlermethode ausgeführt wird, indem man den Seitenkonstruktor oder Middleware benutzt, aber nur Filter für :::no-loc(Razor):::-Seiten haben Zugriff auf [HttpContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.httpcontext?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_:::no-loc(Razor):::Pages_PageModel_HttpContext).</span><span class="sxs-lookup"><span data-stu-id="7073c-162">Code can be run before a handler method executes using the page constructor or middleware, but only :::no-loc(Razor)::: Page filters have access to [HttpContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.httpcontext?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_:::no-loc(Razor):::Pages_PageModel_HttpContext).</span></span> <span data-ttu-id="7073c-163">Filter verfügen über einen Parameter, der von [FilterContext](/dotnet/api/microsoft.aspnetcore.mvc.filters.filtercontext?view=aspnetcore-2.0) abgeleitet ist, um Zugriff auf `HttpContext` zu ermöglichen.</span><span class="sxs-lookup"><span data-stu-id="7073c-163">Filters have a [FilterContext](/dotnet/api/microsoft.aspnetcore.mvc.filters.filtercontext?view=aspnetcore-2.0) derived parameter, which provides access to `HttpContext`.</span></span> <span data-ttu-id="7073c-164">Das Beispiel [Implementieren eines Filterattributs](#ifa) fügt der Antwort z.B. einen Header hinzu. Dies kann nicht über Konstruktoren oder Middleware erfolgen.</span><span class="sxs-lookup"><span data-stu-id="7073c-164">For example, the [Implement a filter attribute](#ifa) sample adds a header to the response, something that can't be done with constructors or middleware.</span></span>

<span data-ttu-id="7073c-165">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/sample/PageFilter) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="7073c-165">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/sample/PageFilter) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="7073c-166">Filter für :::no-loc(Razor):::-Seiten bieten die folgenden Methoden, die global oder auf Seitenebene angewendet werden können:</span><span class="sxs-lookup"><span data-stu-id="7073c-166">:::no-loc(Razor)::: Page filters provide the following methods, which can be applied globally or at the page level:</span></span>

* <span data-ttu-id="7073c-167">Synchrone Methoden:</span><span class="sxs-lookup"><span data-stu-id="7073c-167">Synchronous methods:</span></span>

  * <span data-ttu-id="7073c-168">[OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0): Wird aufgerufen, nachdem eine Handlermethode ausgewählt wurde, aber bevor die Modellbindung erfolgt.</span><span class="sxs-lookup"><span data-stu-id="7073c-168">[OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0) : Called after a handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="7073c-169">[OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0): Wird aufgerufen, bevor die Handlermethode ausgeführt wird, nachdem die Modellbindung abgeschlossen ist.</span><span class="sxs-lookup"><span data-stu-id="7073c-169">[OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0) : Called before the handler method executes, after model binding is complete.</span></span>
  * <span data-ttu-id="7073c-170">[OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0): Wird aufgerufen, nachdem die Handlermethode ausgeführt wird, bevor das Aktionsergebnis angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="7073c-170">[OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0) : Called after the handler method executes, before the action result.</span></span>

* <span data-ttu-id="7073c-171">Asynchrone Methoden:</span><span class="sxs-lookup"><span data-stu-id="7073c-171">Asynchronous methods:</span></span>

  * <span data-ttu-id="7073c-172">[OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0): Wird asynchron aufgerufen, nachdem die Handlermethode ausgewählt wurde, aber bevor die Modellbindung erfolgt.</span><span class="sxs-lookup"><span data-stu-id="7073c-172">[OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0) : Called asynchronously after the handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="7073c-173">[OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0): Wird asynchron aufgerufen, bevor die Handlermethode aufgerufen wird, nachdem die Modellbindung abgeschlossen ist.</span><span class="sxs-lookup"><span data-stu-id="7073c-173">[OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0) : Called asynchronously before the handler method is invoked, after model binding is complete.</span></span>

> [!NOTE]
> <span data-ttu-id="7073c-174">Implementieren Sie **entweder** die synchrone oder asynchrone Version einer Filterschnittstelle, aber nicht beide.</span><span class="sxs-lookup"><span data-stu-id="7073c-174">Implement **either** the synchronous or the async version of a filter interface, not both.</span></span> <span data-ttu-id="7073c-175">Das Framework prüft zuerst, ob der Filter die asynchrone Schnittstelle implementiert, und wenn dies der Fall ist, ruft es sie auf.</span><span class="sxs-lookup"><span data-stu-id="7073c-175">The framework checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="7073c-176">Wenn dies nicht der Fall ist, ruft es die Methode(n) der synchronen Schnittstelle auf.</span><span class="sxs-lookup"><span data-stu-id="7073c-176">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="7073c-177">Wenn beide Schnittstellen implementiert werden, werden nur die asynchronen Methoden aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="7073c-177">If both interfaces are implemented, only the async methods are called.</span></span> <span data-ttu-id="7073c-178">Die gleiche Regel gilt für Überschreibungen in Seiten. Implementieren Sie die synchrone oder asynchrone Version der Überschreibung, nicht beide.</span><span class="sxs-lookup"><span data-stu-id="7073c-178">The same rule applies to overrides in pages, implement the synchronous or the async version of the override, not both.</span></span>

## <a name="implement-no-locrazor-page-filters-globally"></a><span data-ttu-id="7073c-179">Globales implementieren von Filtern für :::no-loc(Razor):::-Seiten</span><span class="sxs-lookup"><span data-stu-id="7073c-179">Implement :::no-loc(Razor)::: Page filters globally</span></span>

<span data-ttu-id="7073c-180">Mit dem folgenden Code wird das `IAsyncPageFilter`-Element implementiert:</span><span class="sxs-lookup"><span data-stu-id="7073c-180">The following code implements `IAsyncPageFilter`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Filters/SampleAsyncPageFilter.cs?name=snippet1)]

<span data-ttu-id="7073c-181">Im vorhergehenden Code wird [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger?view=aspnetcore-2.0) nicht benötigt.</span><span class="sxs-lookup"><span data-stu-id="7073c-181">In the preceding code, [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger?view=aspnetcore-2.0) is not required.</span></span> <span data-ttu-id="7073c-182">Es wird im Beispiel verwendet, um Überwachungsinformationen für die Anwendung bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="7073c-182">It's used in the sample to provide trace information for the application.</span></span>

<span data-ttu-id="7073c-183">Der folgende Code aktiviert `SampleAsyncPageFilter` in der `Startup`-Klasse:</span><span class="sxs-lookup"><span data-stu-id="7073c-183">The following code enables the `SampleAsyncPageFilter` in the `Startup` class:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Startup.cs?name=snippet2&highlight=11)]

<span data-ttu-id="7073c-184">Der folgende Code veranschaulicht die vollständige `Startup`-Klasse:</span><span class="sxs-lookup"><span data-stu-id="7073c-184">The following code shows the complete `Startup` class:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Startup.cs?name=snippet1)]

<span data-ttu-id="7073c-185">Der folgende Code ruft `AddFolderApplicationModelConvention` auf, damit `SampleAsyncPageFilter` nur auf Seiten in */subFolder* angewendet wird:</span><span class="sxs-lookup"><span data-stu-id="7073c-185">The following code calls `AddFolderApplicationModelConvention` to apply the `SampleAsyncPageFilter` to only pages in */subFolder* :</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Startup2.cs?name=snippet2)]

<span data-ttu-id="7073c-186">Im folgenden Code wird `IPageFilter` synchron implementiert:</span><span class="sxs-lookup"><span data-stu-id="7073c-186">The following code implements the synchronous `IPageFilter`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Filters/SamplePageFilter.cs?name=snippet1)]

<span data-ttu-id="7073c-187">Der folgende Code aktiviert `SamplePageFilter`:</span><span class="sxs-lookup"><span data-stu-id="7073c-187">The following code enables the `SamplePageFilter`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/StartupSync.cs?name=snippet2&highlight=11)]

## <a name="implement-no-locrazor-page-filters-by-overriding-filter-methods"></a><span data-ttu-id="7073c-188">Implementieren von Filtern für :::no-loc(Razor):::-Seiten durch Überschreiben von Filtermethoden</span><span class="sxs-lookup"><span data-stu-id="7073c-188">Implement :::no-loc(Razor)::: Page filters by overriding filter methods</span></span>

<span data-ttu-id="7073c-189">Der folgende Code überschreibt die synchronen :::no-loc(Razor)::: Page-Filter:</span><span class="sxs-lookup"><span data-stu-id="7073c-189">The following code overrides the synchronous :::no-loc(Razor)::: Page filters:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/Index.cshtml.cs)]

<a name="ifa"></a>

## <a name="implement-a-filter-attribute"></a><span data-ttu-id="7073c-190">Implementieren eines Filterattributs</span><span class="sxs-lookup"><span data-stu-id="7073c-190">Implement a filter attribute</span></span>

<span data-ttu-id="7073c-191">Der integrierte attributbasierte Filter [OnResultExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncresultfilter.onresultexecutionasync?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Filters_IAsyncResultFilter_OnResultExecutionAsync_Microsoft_AspNetCore_Mvc_Filters_ResultExecutingContext_Microsoft_AspNetCore_Mvc_Filters_ResultExecutionDelegate_) kann als Unterklasse definiert werden.</span><span class="sxs-lookup"><span data-stu-id="7073c-191">The built-in attribute-based filter [OnResultExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncresultfilter.onresultexecutionasync?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Filters_IAsyncResultFilter_OnResultExecutionAsync_Microsoft_AspNetCore_Mvc_Filters_ResultExecutingContext_Microsoft_AspNetCore_Mvc_Filters_ResultExecutionDelegate_) filter can be subclassed.</span></span> <span data-ttu-id="7073c-192">Der folgende Filter fügt der Antwort einen Header hinzu:</span><span class="sxs-lookup"><span data-stu-id="7073c-192">The following filter adds a header to the response:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Filters/AddHeaderAttribute.cs)]

<span data-ttu-id="7073c-193">Der folgende Code wendet das Attribut `AddHeader` an:</span><span class="sxs-lookup"><span data-stu-id="7073c-193">The following code applies the `AddHeader` attribute:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/Contact.cshtml.cs?name=snippet1)]

<span data-ttu-id="7073c-194">Informationen zum Überschreiben der Reihenfolge finden Sie unter [Überschreiben der Standardreihenfolge](xref:mvc/controllers/filters#overriding-the-default-order).</span><span class="sxs-lookup"><span data-stu-id="7073c-194">See [Overriding the default order](xref:mvc/controllers/filters#overriding-the-default-order) for instructions on overriding the order.</span></span>

<span data-ttu-id="7073c-195">Informationen zum Kurzschließen der Filterpipeline eines Filters finden Sie unter [Abbrechen und Kurzschließen](xref:mvc/controllers/filters#cancellation-and-short-circuiting).</span><span class="sxs-lookup"><span data-stu-id="7073c-195">See [Cancellation and short circuiting](xref:mvc/controllers/filters#cancellation-and-short-circuiting) for instructions to short-circuit the filter pipeline from a filter.</span></span> 

<a name="auth"></a>

## <a name="authorize-filter-attribute"></a><span data-ttu-id="7073c-196">Autorisieren eines Filterattributs</span><span class="sxs-lookup"><span data-stu-id="7073c-196">Authorize filter attribute</span></span>

<span data-ttu-id="7073c-197">Das Attribut [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) kann auf ein `PageModel` angewendet werden:</span><span class="sxs-lookup"><span data-stu-id="7073c-197">The [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) attribute can be applied to a `PageModel`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/ModelWithAuthFilter.cshtml.cs?highlight=7)]

::: moniker-end