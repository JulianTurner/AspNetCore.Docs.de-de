---
title: Filter in ASP.NET Core
author: Rick-Anderson
description: Erfahren Sie, wie Filter funktionieren und wie Sie sie in ASP.NET Core verwenden.
ms.author: riande
ms.custom: mvc
ms.date: 02/04/2020
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
uid: mvc/controllers/filters
ms.openlocfilehash: eeae167286e793ecd5a547cea0142cf7d8014ece
ms.sourcegitcommit: c0a15ab8549cb729731a0fdf1d7da0b7feaa11ff
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/02/2020
ms.locfileid: "91671781"
---
# <a name="filters-in-aspnet-core"></a><span data-ttu-id="2da33-103">Filter in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2da33-103">Filters in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="2da33-104">Von [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/) und [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="2da33-104">By [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="2da33-105">*Filter* ermöglichen es in ASP.NET Core, Code vor oder nach bestimmten Phasen der Anforderungsverarbeitungspipeline auszuführen.</span><span class="sxs-lookup"><span data-stu-id="2da33-105">*Filters* in ASP.NET Core allow code to be run before or after specific stages in the request processing pipeline.</span></span>

<span data-ttu-id="2da33-106">Integrierte Filter sind für folgende Aufgaben zuständig:</span><span class="sxs-lookup"><span data-stu-id="2da33-106">Built-in filters handle tasks such as:</span></span>

* <span data-ttu-id="2da33-107">Autorisierung (der Zugriff auf Ressourcen, für die ein Benutzer nicht autorisiert ist, wird verhindert)</span><span class="sxs-lookup"><span data-stu-id="2da33-107">Authorization (preventing access to resources a user isn't authorized for).</span></span>
* <span data-ttu-id="2da33-108">Zwischenspeicherung von Antworten (die Anforderungspipeline wird unterbrochen, damit eine zwischengespeicherte Antwort zurückgegeben wird)</span><span class="sxs-lookup"><span data-stu-id="2da33-108">Response caching (short-circuiting the request pipeline to return a cached response).</span></span>

<span data-ttu-id="2da33-109">Durch die Erstellung benutzerdefinierter Filter kann mit aktionsübergreifenden Problemen umgegangen werden.</span><span class="sxs-lookup"><span data-stu-id="2da33-109">Custom filters can be created to handle cross-cutting concerns.</span></span> <span data-ttu-id="2da33-110">Zu solchen übergreifenden Problemen gehören beispielsweise Fehlerbehandlung, Caching, Konfiguration, Autorisierung und Protokollierung.</span><span class="sxs-lookup"><span data-stu-id="2da33-110">Examples of cross-cutting concerns include error handling, caching, configuration, authorization, and logging.</span></span>  <span data-ttu-id="2da33-111">Mit Filtern lässt sich die Duplizierung von Code vermeiden.</span><span class="sxs-lookup"><span data-stu-id="2da33-111">Filters avoid duplicating code.</span></span> <span data-ttu-id="2da33-112">Sie können zum Beispiel die Fehlerbehandlung in einem Ausnahmefilter konsolidieren.</span><span class="sxs-lookup"><span data-stu-id="2da33-112">For example, an error handling exception filter could consolidate error handling.</span></span>

<span data-ttu-id="2da33-113">Dieses Dokument gilt für Razor Seiten, API-Controller und Controller mit Ansichten.</span><span class="sxs-lookup"><span data-stu-id="2da33-113">This document applies to Razor Pages, API controllers, and controllers with views.</span></span> <span data-ttu-id="2da33-114">Filter funktionieren nicht direkt mit- [ Razor Komponenten](xref:blazor/components/index).</span><span class="sxs-lookup"><span data-stu-id="2da33-114">Filters don't work directly with [Razor components](xref:blazor/components/index).</span></span> <span data-ttu-id="2da33-115">Ein Filter kann nur indirekt Einfluss auf eine Komponente haben, wenn Folgendes gilt:</span><span class="sxs-lookup"><span data-stu-id="2da33-115">A filter can only indirectly affect a component when:</span></span>

* <span data-ttu-id="2da33-116">Die Komponente ist in eine Seite oder Ansicht eingebettet.</span><span class="sxs-lookup"><span data-stu-id="2da33-116">The component is embedded in a page or view.</span></span>
* <span data-ttu-id="2da33-117">Die Seite oder der Controller/die Ansicht verwendet den Filter.</span><span class="sxs-lookup"><span data-stu-id="2da33-117">The page or controller/view uses the filter.</span></span>

<span data-ttu-id="2da33-118">[Zeigen Sie ein Beispiel an, oder laden Sie es herunter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="2da33-118">[View or download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="how-filters-work"></a><span data-ttu-id="2da33-119">Die Funktionsweise von Filtern</span><span class="sxs-lookup"><span data-stu-id="2da33-119">How filters work</span></span>

<span data-ttu-id="2da33-120">Filter werden in der *ASP.NET Core-Aktionsaufrufpipeline* ausgeführt, die manchmal auch als *Filterpipeline* bezeichnet wird.</span><span class="sxs-lookup"><span data-stu-id="2da33-120">Filters run within the *ASP.NET Core action invocation pipeline*, sometimes referred to as the *filter pipeline*.</span></span> <span data-ttu-id="2da33-121">Die Filterpipeline wird ausgeführt, nachdem ASP.NET Core die auszuführende Aktion ausgewählt hat.</span><span class="sxs-lookup"><span data-stu-id="2da33-121">The filter pipeline runs after ASP.NET Core selects the action to execute.</span></span>

![Die Anforderung wird von weiterer Middleware, Routingmiddleware, Aktionsauswahl und der Aktionsaufrufpipeline verarbeitet.](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a><span data-ttu-id="2da33-124">Filtertypen</span><span class="sxs-lookup"><span data-stu-id="2da33-124">Filter types</span></span>

<span data-ttu-id="2da33-125">Jeder Filtertyp wird in einer anderen Phase der Filterpipeline ausgeführt:</span><span class="sxs-lookup"><span data-stu-id="2da33-125">Each filter type is executed at a different stage in the filter pipeline:</span></span>

* <span data-ttu-id="2da33-126">[Autorisierungsfilter](#authorization-filters) werden zuerst ausgeführt und dienen dazu, festzustellen, ob der Benutzer für die Anforderung berechtigt ist.</span><span class="sxs-lookup"><span data-stu-id="2da33-126">[Authorization filters](#authorization-filters) run first and are used to determine whether the user is authorized for the request.</span></span> <span data-ttu-id="2da33-127">Autorisierungsfilter schließen die Pipeline kurz, wenn die Anforderung nicht autorisiert ist.</span><span class="sxs-lookup"><span data-stu-id="2da33-127">Authorization filters short-circuit the pipeline if the request is not authorized.</span></span>

* <span data-ttu-id="2da33-128">[Ressourcen Filter](#resource-filters):</span><span class="sxs-lookup"><span data-stu-id="2da33-128">[Resource filters](#resource-filters):</span></span>

  * <span data-ttu-id="2da33-129">Werden nach der Autorisierung ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="2da33-129">Run after authorization.</span></span>  
  * <span data-ttu-id="2da33-130"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> führt Code vor dem Rest der Filterpipeline aus.</span><span class="sxs-lookup"><span data-stu-id="2da33-130"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> runs code before the rest of the filter pipeline.</span></span> <span data-ttu-id="2da33-131">Beispielsweise führt `OnResourceExecuting` Code vor der Modellbindung aus.</span><span class="sxs-lookup"><span data-stu-id="2da33-131">For example, `OnResourceExecuting` runs code before model binding.</span></span>
  * <span data-ttu-id="2da33-132"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> führt Code aus, nachdem der Rest der Pipeline abgeschlossen wurde.</span><span class="sxs-lookup"><span data-stu-id="2da33-132"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> runs code after the rest of the pipeline has completed.</span></span>

* <span data-ttu-id="2da33-133">[Aktionsfilter](#action-filters):</span><span class="sxs-lookup"><span data-stu-id="2da33-133">[Action filters](#action-filters):</span></span>

  * <span data-ttu-id="2da33-134">Sie führen Code unmittelbar vor und nach dem Aufruf einer Aktionsmethode aus.</span><span class="sxs-lookup"><span data-stu-id="2da33-134">Run code immediately before and after an action method is called.</span></span>
  * <span data-ttu-id="2da33-135">Sie können die an eine Aktion übergebenen Argumente ändern.</span><span class="sxs-lookup"><span data-stu-id="2da33-135">Can change the arguments passed into an action.</span></span>
  * <span data-ttu-id="2da33-136">Sie können das von der Aktion zurückgegebene Ergebnis ändern.</span><span class="sxs-lookup"><span data-stu-id="2da33-136">Can change the result returned from the action.</span></span>
  * <span data-ttu-id="2da33-137">Werden in Seiten **nicht** unterstützt Razor .</span><span class="sxs-lookup"><span data-stu-id="2da33-137">Are **not** supported in Razor Pages.</span></span>

* <span data-ttu-id="2da33-138">[Ausnahmefilter](#exception-filters) wenden globale Richtlinien auf unbehandelte Ausnahmen an, die auftreten, bevor etwas in den Antworttext geschrieben wurde.</span><span class="sxs-lookup"><span data-stu-id="2da33-138">[Exception filters](#exception-filters) apply global policies to unhandled exceptions that occur before the response body has been written to.</span></span>

* <span data-ttu-id="2da33-139">[Ergebnisfilter](#result-filters) führen Code unmittelbar vor und nach der Ausführung von Aktionsergebnissen aus.</span><span class="sxs-lookup"><span data-stu-id="2da33-139">[Result filters](#result-filters) run code immediately before and after the execution of action results.</span></span> <span data-ttu-id="2da33-140">Sie werden nur ausgeführt, wenn die Aktionsmethode erfolgreich ausgeführt wurde.</span><span class="sxs-lookup"><span data-stu-id="2da33-140">They run only when the action method has executed successfully.</span></span> <span data-ttu-id="2da33-141">Ergebnisfilter sind hilfreich für Logik, die die Ausführung von Ansichten oder Formatierern umfasst.</span><span class="sxs-lookup"><span data-stu-id="2da33-141">They are useful for logic that must surround view or formatter execution.</span></span>

<span data-ttu-id="2da33-142">Das folgende Diagramm veranschaulicht, wie die Filtertypen mit der Filterpipeline interagieren.</span><span class="sxs-lookup"><span data-stu-id="2da33-142">The following diagram shows how filter types interact in the filter pipeline.</span></span>

![Die Anforderung wird von Autorisierungsfilter, Ressourcenfilter, Modellbindung, Aktionsfilter, Aktionsausführung sowie Aktionsergebniskonvertierung, Ausnahmefilter, Ergebnisfilter und Ergebnisausführung verarbeitet.](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a><span data-ttu-id="2da33-145">Implementierung</span><span class="sxs-lookup"><span data-stu-id="2da33-145">Implementation</span></span>

<span data-ttu-id="2da33-146">Filter unterstützen sowohl synchrone als auch asynchrone Implementierungen über verschiedene Schnittstellendefinitionen.</span><span class="sxs-lookup"><span data-stu-id="2da33-146">Filters support both synchronous and asynchronous implementations through different interface definitions.</span></span>

<span data-ttu-id="2da33-147">Synchrone Filter führen Code vor und nach der jeweiligen Pipelinephase aus.</span><span class="sxs-lookup"><span data-stu-id="2da33-147">Synchronous filters run code before and after their pipeline stage.</span></span> <span data-ttu-id="2da33-148"><xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*> beispielsweise wird aufgerufen, bevor die Aktionsmethode aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="2da33-148">For example, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*> is called before the action method is called.</span></span> <span data-ttu-id="2da33-149"><xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*> wird nach der Rückgabe der Aktionsmethode aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="2da33-149"><xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*> is called after the action method returns.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="2da33-150">Im vorangehenden Code ist [mydebug](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/controllers/filters/3.1sample/FiltersSample/Helper/MyDebug.cs) eine Utility-Funktion im [Beispiel Download](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/controllers/filters/3.1sample/FiltersSample/Helper/MyDebug.cs).</span><span class="sxs-lookup"><span data-stu-id="2da33-150">In the preceding code, [MyDebug](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/controllers/filters/3.1sample/FiltersSample/Helper/MyDebug.cs) is a utility function in the [sample download](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/controllers/filters/3.1sample/FiltersSample/Helper/MyDebug.cs).</span></span>

<span data-ttu-id="2da33-151">Asynchrone Filter definieren eine `On-Stage-ExecutionAsync`-Methode.</span><span class="sxs-lookup"><span data-stu-id="2da33-151">Asynchronous filters define an `On-Stage-ExecutionAsync` method.</span></span> <span data-ttu-id="2da33-152">Beispiel: <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*>:</span><span class="sxs-lookup"><span data-stu-id="2da33-152">For example, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*>:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/SampleAsyncActionFilter.cs?name=snippet)]

<span data-ttu-id="2da33-153">Im oben stehenden Code weist der `SampleAsyncActionFilter` einen <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`) auf, der die Aktionsmethode ausführt.</span><span class="sxs-lookup"><span data-stu-id="2da33-153">In the preceding code, the `SampleAsyncActionFilter` has an <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`) that executes the action method.</span></span>

### <a name="multiple-filter-stages"></a><span data-ttu-id="2da33-154">Mehrere Filterphasen</span><span class="sxs-lookup"><span data-stu-id="2da33-154">Multiple filter stages</span></span>

<span data-ttu-id="2da33-155">Schnittstellen für mehrere Filterphasen können in einer einzigen Klasse implementiert werden.</span><span class="sxs-lookup"><span data-stu-id="2da33-155">Interfaces for multiple filter stages can be implemented in a single class.</span></span> <span data-ttu-id="2da33-156">Beispielsweise implementiert die <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>-Klasse:</span><span class="sxs-lookup"><span data-stu-id="2da33-156">For example, the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> class implements:</span></span>

* <span data-ttu-id="2da33-157">Synchron: <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> und <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter></span><span class="sxs-lookup"><span data-stu-id="2da33-157">Synchronous: <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> and  <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter></span></span>
* <span data-ttu-id="2da33-158">Asynchron: <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> und <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="2da33-158">Asynchronous: <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
* <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>

<span data-ttu-id="2da33-159">Implementieren Sie **entweder** die synchrone oder asynchrone Version einer Filterschnittstelle, aber **nicht** beide.</span><span class="sxs-lookup"><span data-stu-id="2da33-159">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="2da33-160">Die Runtime prüft zuerst, ob der Filter die asynchrone Schnittstelle implementiert, und wenn dies der Fall ist, ruft die Runtime diese Schnittstelle auf.</span><span class="sxs-lookup"><span data-stu-id="2da33-160">The runtime checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="2da33-161">Wenn dies nicht der Fall ist, ruft es die Methode(n) der synchronen Schnittstelle auf.</span><span class="sxs-lookup"><span data-stu-id="2da33-161">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="2da33-162">Wenn die asynchrone und die synchrone Schnittstelle in einer einzigen Klasse implementiert sind, wird nur die asynchrone Methode aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="2da33-162">If both asynchronous and synchronous interfaces are implemented in one class, only the async method is called.</span></span> <span data-ttu-id="2da33-163">Wenn abstrakte Klassen wie verwendet <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> werden, müssen nur die synchronen Methoden oder die asynchronen Methoden für jeden Filtertyp überschrieben werden.</span><span class="sxs-lookup"><span data-stu-id="2da33-163">When using abstract classes like <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>, override only the synchronous methods or the asynchronous methods for each filter type.</span></span>

### <a name="built-in-filter-attributes"></a><span data-ttu-id="2da33-164">Integrierte Filterattribute</span><span class="sxs-lookup"><span data-stu-id="2da33-164">Built-in filter attributes</span></span>

<span data-ttu-id="2da33-165">ASP.NET Core enthält integrierte attributbasierte Filter, die als Unterklasse erstellt und angepasst werden können.</span><span class="sxs-lookup"><span data-stu-id="2da33-165">ASP.NET Core includes built-in attribute-based filters that can be subclassed and customized.</span></span> <span data-ttu-id="2da33-166">Zum Beispiel fügt der folgende Ergebnisfilter der Antwort einen Header hinzu:</span><span class="sxs-lookup"><span data-stu-id="2da33-166">For example, the following result filter adds a header to the response:</span></span>

<a name="add-header-attribute"></a>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/AddHeaderAttribute.cs?name=snippet)]

<span data-ttu-id="2da33-167">Wie im obigen Beispiel gezeigt, können Filter mithilfe von Attributen Argumente akzeptieren.</span><span class="sxs-lookup"><span data-stu-id="2da33-167">Attributes allow filters to accept arguments, as shown in the preceding example.</span></span> <span data-ttu-id="2da33-168">Wenden Sie das `AddHeaderAttribute` auf einen Controller oder eine Aktionsmethode an, und geben Sie den Namen und den Wert des HTTP-Headers an:</span><span class="sxs-lookup"><span data-stu-id="2da33-168">Apply the `AddHeaderAttribute` to a controller or action method and specify the name and value of the HTTP header:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

<span data-ttu-id="2da33-169">Verwenden Sie ein Tool wie die [Browser Entwicklertools](https://developer.mozilla.org/docs/Learn/Common_questions/What_are_browser_developer_tools) , um die Header zu untersuchen.</span><span class="sxs-lookup"><span data-stu-id="2da33-169">Use a tool such as the [browser developer tools](https://developer.mozilla.org/docs/Learn/Common_questions/What_are_browser_developer_tools) to examine the headers.</span></span> <span data-ttu-id="2da33-170">Unter **Antwortheader** wird `author: Rick Anderson` angezeigt.</span><span class="sxs-lookup"><span data-stu-id="2da33-170">Under **Response Headers**, `author: Rick Anderson` is displayed.</span></span>

<span data-ttu-id="2da33-171">Mit dem folgenden Code wird ein `ActionFilterAttribute`-Element implementiert, das diese Aktionen ausführt:</span><span class="sxs-lookup"><span data-stu-id="2da33-171">The following code implements an `ActionFilterAttribute` that:</span></span>

* <span data-ttu-id="2da33-172">Liest den Titel und den Namen aus dem Konfigurationssystem.</span><span class="sxs-lookup"><span data-stu-id="2da33-172">Reads the title and name from the configuration system.</span></span> <span data-ttu-id="2da33-173">Im Gegensatz zum vorherigen Beispiel erfordert der folgende Code nicht, dass dem Code Filterparameter hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="2da33-173">Unlike the previous sample, the following code doesn't require filter parameters to be added to the code.</span></span>
* <span data-ttu-id="2da33-174">Fügt dem Antwortheader den Titel und den Namen hinzu.</span><span class="sxs-lookup"><span data-stu-id="2da33-174">Adds the title and name to the response header.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MyActionFilterAttribute.cs?name=snippet)]

<span data-ttu-id="2da33-175">Die Konfigurationsoptionen werden vom [Konfigurationssystem](xref:fundamentals/configuration/index) mithilfe der [Optionsmuster](xref:fundamentals/configuration/options) bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="2da33-175">The configuration options are provided from the [configuration system](xref:fundamentals/configuration/index) using the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="2da33-176">Beispielsweise aus der Datei *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="2da33-176">For example, from the *appsettings.json* file:</span></span>

[!code-json[](filters/3.1sample/FiltersSample/appsettings.json)]

<span data-ttu-id="2da33-177">In `StartUp.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="2da33-177">In the `StartUp.ConfigureServices`:</span></span>

* <span data-ttu-id="2da33-178">Die `PositionOptions`-Klasse wird dem Dienstcontainer mit dem Konfigurationsbereich `"Position"` hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="2da33-178">The `PositionOptions` class is added to the service container with the `"Position"` configuration area.</span></span>
* <span data-ttu-id="2da33-179">`MyActionFilterAttribute` wird dem Dienstcontainer hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="2da33-179">The `MyActionFilterAttribute` is added to the service container.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupAF.cs?name=snippet)]

<span data-ttu-id="2da33-180">Der folgende Code zeigt die `PositionOptions`-Klasse:</span><span class="sxs-lookup"><span data-stu-id="2da33-180">The following code shows the `PositionOptions` class:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Helper/PositionOptions.cs?name=snippet)]

<span data-ttu-id="2da33-181">Der folgende Code wendet das `MyActionFilterAttribute` auf die Methode `Index2` an:</span><span class="sxs-lookup"><span data-stu-id="2da33-181">The following code applies the `MyActionFilterAttribute` to the `Index2` method:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet2&highlight=9)]

<span data-ttu-id="2da33-182">Unter den **Antwort Headern** `author: Rick Anderson` `Editor: Joe Smith` wird und angezeigt, wenn der `Sample/Index2` Endpunkt aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="2da33-182">Under **Response Headers**, `author: Rick Anderson`, and `Editor: Joe Smith` is displayed when the `Sample/Index2` endpoint is called.</span></span>

<span data-ttu-id="2da33-183">Der folgende Code wendet `MyActionFilterAttribute` und `AddHeaderAttribute` auf die Seite an Razor :</span><span class="sxs-lookup"><span data-stu-id="2da33-183">The following code applies the `MyActionFilterAttribute` and the `AddHeaderAttribute` to the Razor Page:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Pages/Movies/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="2da33-184">Filter können nicht auf Razor seitenhandlermethoden angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="2da33-184">Filters cannot be applied to Razor Page handler methods.</span></span> <span data-ttu-id="2da33-185">Sie können entweder auf das Razor Seiten Modell oder Global angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="2da33-185">They can be applied either to the Razor Page model or globally.</span></span>

<span data-ttu-id="2da33-186">Einige der Filterschnittstellen besitzen entsprechende Attribute, die als Basisklassen für benutzerdefinierte Implementierungen verwendet werden können.</span><span class="sxs-lookup"><span data-stu-id="2da33-186">Several of the filter interfaces have corresponding attributes that can be used as base classes for custom implementations.</span></span>

<span data-ttu-id="2da33-187">Filterattribute:</span><span class="sxs-lookup"><span data-stu-id="2da33-187">Filter attributes:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ExceptionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>

## <a name="filter-scopes-and-order-of-execution"></a><span data-ttu-id="2da33-188">Filterbereiche und Reihenfolge der Ausführung</span><span class="sxs-lookup"><span data-stu-id="2da33-188">Filter scopes and order of execution</span></span>

<span data-ttu-id="2da33-189">Der Pipeline kann in einem von drei *Bereichen* ein Filter hinzugefügt werden:</span><span class="sxs-lookup"><span data-stu-id="2da33-189">A filter can be added to the pipeline at one of three *scopes*:</span></span>

* <span data-ttu-id="2da33-190">Verwenden eines Attributs für eine Controller-Aktion.</span><span class="sxs-lookup"><span data-stu-id="2da33-190">Using an attribute on a controller action.</span></span> <span data-ttu-id="2da33-191">Filter Attribute können nicht auf Razor Seiten Handlermethoden angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="2da33-191">Filter attributes cannot be applied to Razor Pages handler methods.</span></span>
* <span data-ttu-id="2da33-192">Verwenden eines Attributs auf einem Controller oder einer Razor Seite.</span><span class="sxs-lookup"><span data-stu-id="2da33-192">Using an attribute on a controller or Razor Page.</span></span>
* <span data-ttu-id="2da33-193">Global für alle Controller, Aktionen und Razor Seiten, wie im folgenden Code gezeigt:</span><span class="sxs-lookup"><span data-stu-id="2da33-193">Globally for all controllers, actions, and Razor Pages as shown in the following code:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder.cs?name=snippet)]

### <a name="default-order-of-execution"></a><span data-ttu-id="2da33-194">Standardreihenfolge der Ausführung</span><span class="sxs-lookup"><span data-stu-id="2da33-194">Default order of execution</span></span>

<span data-ttu-id="2da33-195">Wenn es mehrere Filter für eine bestimmte Stufe der Pipeline gibt, bestimmt der Bereich die Standardreihenfolge der Filterausführung.</span><span class="sxs-lookup"><span data-stu-id="2da33-195">When there are multiple filters for a particular stage of the pipeline, scope determines the default order of filter execution.</span></span>  <span data-ttu-id="2da33-196">Globale Filter umfassen Klassenfilter, welche hingegen Methodenfilter umfassen.</span><span class="sxs-lookup"><span data-stu-id="2da33-196">Global filters surround class filters, which in turn surround method filters.</span></span>

<span data-ttu-id="2da33-197">Als Ergebnis der Filterschachtelung wird der *nach* einer Pipelinephase auszuführende Code in umgekehrter Reihenfolge zum Code *vor* einer Pipelinephase ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="2da33-197">As a result of filter nesting, the *after* code of filters runs in the reverse order of the *before* code.</span></span> <span data-ttu-id="2da33-198">Filterreihenfolge:</span><span class="sxs-lookup"><span data-stu-id="2da33-198">The filter sequence:</span></span>

* <span data-ttu-id="2da33-199">Der Code von globalen Filtern, der *vor* einer Pipelinephase ausgeführt werden soll</span><span class="sxs-lookup"><span data-stu-id="2da33-199">The *before* code of global filters.</span></span>
  * <span data-ttu-id="2da33-200">Der *vor* -Code von Controller-und Razor Seiten filtern.</span><span class="sxs-lookup"><span data-stu-id="2da33-200">The *before* code of controller and Razor Page filters.</span></span>
    * <span data-ttu-id="2da33-201">Der Code von Aktionsmethodenfiltern, der *vor* einer Pipelinephase ausgeführt werden soll</span><span class="sxs-lookup"><span data-stu-id="2da33-201">The *before* code of action method filters.</span></span>
    * <span data-ttu-id="2da33-202">Der Code von Aktionsmethodenfiltern, der *nach* einer Pipelinephase ausgeführt werden soll</span><span class="sxs-lookup"><span data-stu-id="2da33-202">The *after* code of action method filters.</span></span>
  * <span data-ttu-id="2da33-203">Der *after* -Code von Controller-und Razor Seiten filtern.</span><span class="sxs-lookup"><span data-stu-id="2da33-203">The *after* code of controller and Razor Page filters.</span></span>
* <span data-ttu-id="2da33-204">Der Code von globalen Filtern, der *nach* einer Pipelinephase ausgeführt werden soll</span><span class="sxs-lookup"><span data-stu-id="2da33-204">The *after* code of global filters.</span></span>
  
<span data-ttu-id="2da33-205">Das folgende Beispiel veranschaulicht die Reihenfolge, in der Filtermethoden für synchrone Aktionsfilter aufgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="2da33-205">The following example that illustrates the order in which filter methods are called for synchronous action filters.</span></span>

| <span data-ttu-id="2da33-206">Sequenz</span><span class="sxs-lookup"><span data-stu-id="2da33-206">Sequence</span></span> | <span data-ttu-id="2da33-207">Filterbereich</span><span class="sxs-lookup"><span data-stu-id="2da33-207">Filter scope</span></span> | <span data-ttu-id="2da33-208">Filtermethode</span><span class="sxs-lookup"><span data-stu-id="2da33-208">Filter method</span></span> |
|:--------:|:------------:|:-------------:|
| <span data-ttu-id="2da33-209">1</span><span class="sxs-lookup"><span data-stu-id="2da33-209">1</span></span> | <span data-ttu-id="2da33-210">Global</span><span class="sxs-lookup"><span data-stu-id="2da33-210">Global</span></span> | `OnActionExecuting` |
| <span data-ttu-id="2da33-211">2</span><span class="sxs-lookup"><span data-stu-id="2da33-211">2</span></span> | <span data-ttu-id="2da33-212">Controller oder Razor Seite</span><span class="sxs-lookup"><span data-stu-id="2da33-212">Controller or Razor Page</span></span>| `OnActionExecuting` |
| <span data-ttu-id="2da33-213">3</span><span class="sxs-lookup"><span data-stu-id="2da33-213">3</span></span> | <span data-ttu-id="2da33-214">Methode</span><span class="sxs-lookup"><span data-stu-id="2da33-214">Method</span></span> | `OnActionExecuting` |
| <span data-ttu-id="2da33-215">4</span><span class="sxs-lookup"><span data-stu-id="2da33-215">4</span></span> | <span data-ttu-id="2da33-216">Methode</span><span class="sxs-lookup"><span data-stu-id="2da33-216">Method</span></span> | `OnActionExecuted` |
| <span data-ttu-id="2da33-217">5</span><span class="sxs-lookup"><span data-stu-id="2da33-217">5</span></span> | <span data-ttu-id="2da33-218">Controller oder Razor Seite</span><span class="sxs-lookup"><span data-stu-id="2da33-218">Controller or Razor Page</span></span> | `OnActionExecuted` |
| <span data-ttu-id="2da33-219">6</span><span class="sxs-lookup"><span data-stu-id="2da33-219">6</span></span> | <span data-ttu-id="2da33-220">Global</span><span class="sxs-lookup"><span data-stu-id="2da33-220">Global</span></span> | `OnActionExecuted` |

### <a name="controller-level-filters"></a><span data-ttu-id="2da33-221">Filter auf Controllerebene</span><span class="sxs-lookup"><span data-stu-id="2da33-221">Controller level filters</span></span>

<span data-ttu-id="2da33-222">Jeder Controller, der von der Basisklasse <xref:Microsoft.AspNetCore.Mvc.Controller> erbt, enthält die Methoden [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*), [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*) und [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted`.</span><span class="sxs-lookup"><span data-stu-id="2da33-222">Every controller that inherits from the <xref:Microsoft.AspNetCore.Mvc.Controller> base class includes [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*),  [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*), and [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted` methods.</span></span> <span data-ttu-id="2da33-223">Diese Methoden führen Folgendes aus:</span><span class="sxs-lookup"><span data-stu-id="2da33-223">These methods:</span></span>

* <span data-ttu-id="2da33-224">Die Filter, die für eine bestimmte Aktion ausgeführt werden, werden umschlossen.</span><span class="sxs-lookup"><span data-stu-id="2da33-224">Wrap the filters that run for a given action.</span></span>
* <span data-ttu-id="2da33-225">`OnActionExecuting` wird vor allen Filtern der Aktion aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="2da33-225">`OnActionExecuting` is called before any of the action's filters.</span></span>
* <span data-ttu-id="2da33-226">`OnActionExecuted` wird nach allen Filtern der Aktion aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="2da33-226">`OnActionExecuted` is called after all of the action filters.</span></span>
* <span data-ttu-id="2da33-227">`OnActionExecutionAsync` wird vor allen Filtern der Aktion aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="2da33-227">`OnActionExecutionAsync` is called before any of the action's filters.</span></span> <span data-ttu-id="2da33-228">Code im Filter nach `next` wird nach der Aktionsmethode ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="2da33-228">Code in the filter after `next` runs after the action method.</span></span>

<span data-ttu-id="2da33-229">Im Downloadbeispiel wird `MySampleActionFilter` global beim Start angewendet.</span><span class="sxs-lookup"><span data-stu-id="2da33-229">For example, in the download sample, `MySampleActionFilter` is applied globally in startup.</span></span>

<span data-ttu-id="2da33-230">Die `TestController`:</span><span class="sxs-lookup"><span data-stu-id="2da33-230">The `TestController`:</span></span>

* <span data-ttu-id="2da33-231">Wendet das `SampleActionFilterAttribute` (`[SampleActionFilter]`) auf die Aktion `FilterTest2` an.</span><span class="sxs-lookup"><span data-stu-id="2da33-231">Applies the `SampleActionFilterAttribute` (`[SampleActionFilter]`) to the `FilterTest2` action.</span></span>
* <span data-ttu-id="2da33-232">Überschreibt `OnActionExecuting` und `OnActionExecuted`:</span><span class="sxs-lookup"><span data-stu-id="2da33-232">Overrides `OnActionExecuting` and `OnActionExecuted`.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/TestController.cs?name=snippet)]

[!INCLUDE[](~/includes/MyDisplayRouteInfo.md)]

<!-- test via  webBuilder.UseStartup<Startup>(); -->

<span data-ttu-id="2da33-233">Durch Navigieren zu `https://localhost:5001/Test/FilterTest2` wird der folgende Code ausgeführt:</span><span class="sxs-lookup"><span data-stu-id="2da33-233">Navigating to `https://localhost:5001/Test/FilterTest2` runs the following code:</span></span>

* `TestController.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `SampleActionFilterAttribute.OnActionExecuting`
      * `TestController.FilterTest2`
    * `SampleActionFilterAttribute.OnActionExecuted`
  * `MySampleActionFilter.OnActionExecuted`
* `TestController.OnActionExecuted`

<span data-ttu-id="2da33-234">Filter auf Controllerebene legen die Eigenschaft [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) auf `int.MinValue` fest.</span><span class="sxs-lookup"><span data-stu-id="2da33-234">Controller level filters set the [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) property to `int.MinValue`.</span></span> <span data-ttu-id="2da33-235">Für Filter auf Controllerebene kann die Ausführung nach Filtern, die auf Methoden angewendet werden **nicht** festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="2da33-235">Controller level filters can **not** be set to run after filters applied to methods.</span></span> <span data-ttu-id="2da33-236">Die Reihenfolge wird im nächsten Abschnitt erläutert.</span><span class="sxs-lookup"><span data-stu-id="2da33-236">Order is explained in the next section.</span></span>

<span data-ttu-id="2da33-237">Informationen zu Razor Seiten finden Sie unter [Implementieren von Seiten Razor Filtern durch Überschreiben von Filter Methoden](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span><span class="sxs-lookup"><span data-stu-id="2da33-237">For Razor Pages, see [Implement Razor Page filters by overriding filter methods](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span></span>

### <a name="overriding-the-default-order"></a><span data-ttu-id="2da33-238">Überschreiben der Standardreihenfolge</span><span class="sxs-lookup"><span data-stu-id="2da33-238">Overriding the default order</span></span>

<span data-ttu-id="2da33-239">Die Standardreihenfolge der Ausführung kann durch Implementieren von <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter> überschrieben werden.</span><span class="sxs-lookup"><span data-stu-id="2da33-239">The default sequence of execution can be overridden by implementing <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span></span> <span data-ttu-id="2da33-240">`IOrderedFilter` macht die Eigenschaft <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> verfügbar, die bei der Bestimmung der Ausführungsreihenfolge Vorrang vor dem Bereich hat.</span><span class="sxs-lookup"><span data-stu-id="2da33-240">`IOrderedFilter` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> property that takes precedence over scope to determine the order of execution.</span></span> <span data-ttu-id="2da33-241">Für einen Filter mit geringerem `Order`-Wert gilt:</span><span class="sxs-lookup"><span data-stu-id="2da33-241">A filter with a lower `Order` value:</span></span>

* <span data-ttu-id="2da33-242">Führt den Code, der *vor* einer Pipelinephase ausgeführt werden soll, vor dem Code eines Filters mit einem höheren Wert für `Order` aus.</span><span class="sxs-lookup"><span data-stu-id="2da33-242">Runs the *before* code before that of a filter with a higher value of `Order`.</span></span>
* <span data-ttu-id="2da33-243">Führt den Code, der *nach* einer Pipelinephase ausgeführt werden soll, nach dem Code eines Filters mit einem höheren Wert für `Order` aus.</span><span class="sxs-lookup"><span data-stu-id="2da33-243">Runs the *after* code after that of a filter with a higher `Order` value.</span></span>

<span data-ttu-id="2da33-244">Die Eigenschaft `Order` wird mit einem Konstruktorparameter festgelegt:</span><span class="sxs-lookup"><span data-stu-id="2da33-244">The `Order` property is set with a constructor parameter:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test3Controller.cs?name=snippet)]

<span data-ttu-id="2da33-245">Sehen Sie sich die zwei Aktionsfilter im folgenden Controller an:</span><span class="sxs-lookup"><span data-stu-id="2da33-245">Consider the two action filters in the following controller:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test2Controller.cs?name=snippet)]

<span data-ttu-id="2da33-246">In `StartUp.ConfigureServices` wird ein globaler Filter hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="2da33-246">A global filter is added in `StartUp.ConfigureServices`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder.cs?name=snippet)]

<span data-ttu-id="2da33-247">Die 3 Filter werden in der folgenden Reihenfolge ausgeführt:</span><span class="sxs-lookup"><span data-stu-id="2da33-247">The 3 filters run in the following order:</span></span>

* `Test2Controller.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `MyAction2FilterAttribute.OnActionExecuting`
      * `Test2Controller.FilterTest2`
    * `MyAction2FilterAttribute.OnResultExecuting`
  * `MySampleActionFilter.OnActionExecuted`
* `Test2Controller.OnActionExecuted`

<span data-ttu-id="2da33-248">Die Eigenschaft `Order` hat bei der Bestimmung der Reihenfolge, in der Filter ausgeführt werden, Vorrang vor dem Bereich.</span><span class="sxs-lookup"><span data-stu-id="2da33-248">The `Order` property overrides scope when determining the order in which filters run.</span></span> <span data-ttu-id="2da33-249">Filter werden erst nach der Reihenfolge sortiert, und dann werden Gleichstände über den Bereich aufgelöst.</span><span class="sxs-lookup"><span data-stu-id="2da33-249">Filters are sorted first by order, then scope is used to break ties.</span></span> <span data-ttu-id="2da33-250">Alle integrierten Filter implementieren `IOrderedFilter` und legen den Standartwert von `Order` auf 0 fest.</span><span class="sxs-lookup"><span data-stu-id="2da33-250">All of the built-in filters implement `IOrderedFilter` and set the default `Order` value to 0.</span></span> <span data-ttu-id="2da33-251">Wie zuvor erwähnt, legen Filter auf Controllerebene die [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17)-Eigenschaft auf `int.MinValue` fest. Bei integrierten Filtern bestimmt der Umfang die Reihenfolge, es sei denn, `Order` ist auf einen Wert ungleich null festgelegt.</span><span class="sxs-lookup"><span data-stu-id="2da33-251">As mentioned previously, controller level filters set the [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) property to `int.MinValue` For built-in filters, scope determines order unless `Order` is set to a non-zero value.</span></span>

<span data-ttu-id="2da33-252">Im vorstehenden Code hat `MySampleActionFilter` globalen Umfang und wird daher vor `MyAction2FilterAttribute` ausgeführt, der Controllerumfang aufweist.</span><span class="sxs-lookup"><span data-stu-id="2da33-252">In the preceding code, `MySampleActionFilter` has global scope so it runs before `MyAction2FilterAttribute`, which has controller scope.</span></span> <span data-ttu-id="2da33-253">Um `MyAction2FilterAttribute` zuerst ausführen zu lassen, legen Sie die Reihenfolge auf `int.MinValue` fest:</span><span class="sxs-lookup"><span data-stu-id="2da33-253">To make `MyAction2FilterAttribute` run first, set the order to `int.MinValue`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test2Controller.cs?name=snippet2)]

<span data-ttu-id="2da33-254">Um zuerst den globalen Filter `MySampleActionFilter` ausführen zu lassen, legen Sie `Order` auf `int.MinValue` fest:</span><span class="sxs-lookup"><span data-stu-id="2da33-254">To make the global filter `MySampleActionFilter` run first, set `Order` to `int.MinValue`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder2.cs?name=snippet&highlight=6)]

## <a name="cancellation-and-short-circuiting"></a><span data-ttu-id="2da33-255">Abbrechen und Kurzschließen</span><span class="sxs-lookup"><span data-stu-id="2da33-255">Cancellation and short-circuiting</span></span>

<span data-ttu-id="2da33-256">Die Filterpipeline kann kurzgeschlossen werden, indem die Eigenschaft <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> auf den Parameter <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> festgelegt wird, der in der Filtermethode angegeben ist.</span><span class="sxs-lookup"><span data-stu-id="2da33-256">The filter pipeline can be short-circuited by setting the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> property on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parameter provided to the filter method.</span></span> <span data-ttu-id="2da33-257">Zum Beispiel verhindert der folgende Ressourcenfilter, dass der Rest der Pipeline ausgeführt wird:</span><span class="sxs-lookup"><span data-stu-id="2da33-257">For instance, the following Resource filter prevents the rest of the pipeline from executing:</span></span>

<a name="short-circuiting-resource-filter"></a>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?name=snippet)]

<span data-ttu-id="2da33-258">Im folgenden Code verwenden sowohl der Filter `ShortCircuitingResourceFilter` und der Filter `AddHeader` die Aktionsmethode `SomeResource` als Ziel.</span><span class="sxs-lookup"><span data-stu-id="2da33-258">In the following code, both the `ShortCircuitingResourceFilter` and the `AddHeader` filter target the `SomeResource` action method.</span></span> <span data-ttu-id="2da33-259">Die `ShortCircuitingResourceFilter`:</span><span class="sxs-lookup"><span data-stu-id="2da33-259">The `ShortCircuitingResourceFilter`:</span></span>

* <span data-ttu-id="2da33-260">Der Filter wird zuerst ausgeführt, da es sich um einen Ressourcenfilter handelt und `AddHeader` ein Aktionsfilter ist.</span><span class="sxs-lookup"><span data-stu-id="2da33-260">Runs first, because it's a Resource Filter and `AddHeader` is an Action Filter.</span></span>
* <span data-ttu-id="2da33-261">Der Filter unterbricht alle verbleibenden Pipelineschritte.</span><span class="sxs-lookup"><span data-stu-id="2da33-261">Short-circuits the rest of the pipeline.</span></span>

<span data-ttu-id="2da33-262">Der `AddHeader`-Filter wird daher nie für die `SomeResource`-Aktion ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="2da33-262">Therefore the `AddHeader` filter never runs for the `SomeResource` action.</span></span> <span data-ttu-id="2da33-263">Dasselbe Verhalten würde auch dann auftreten, wenn beide Filter auf Aktionsmethodenebene angewendet würden, wenn `ShortCircuitingResourceFilter` zuerst ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="2da33-263">This behavior would be the same if both filters were applied at the action method level, provided the `ShortCircuitingResourceFilter` ran first.</span></span> <span data-ttu-id="2da33-264">Der `ShortCircuitingResourceFilter` wird aufgrund seines Filtertyps oder durch die explizite Verwendung der `Order`-Eigenschaft zuerst ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="2da33-264">The `ShortCircuitingResourceFilter` runs first because of its filter type, or by explicit use of `Order` property.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

## <a name="dependency-injection"></a><span data-ttu-id="2da33-265">Dependency Injection</span><span class="sxs-lookup"><span data-stu-id="2da33-265">Dependency injection</span></span>

<span data-ttu-id="2da33-266">Filter können nach Typ oder Instanz hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="2da33-266">Filters can be added by type or by instance.</span></span> <span data-ttu-id="2da33-267">Wenn eine Instanz hinzugefügt wird, wird diese Instanz für jede Anforderung verwendet.</span><span class="sxs-lookup"><span data-stu-id="2da33-267">If an instance is added, that instance is used for every request.</span></span> <span data-ttu-id="2da33-268">Wenn ein Typ hinzugefügt wird, ist der Filter typaktiviert.</span><span class="sxs-lookup"><span data-stu-id="2da33-268">If a type is added, it's type-activated.</span></span> <span data-ttu-id="2da33-269">Ein typaktivierter Filter bedeutet Folgendes:</span><span class="sxs-lookup"><span data-stu-id="2da33-269">A type-activated filter means:</span></span>

* <span data-ttu-id="2da33-270">Für jede Anforderung wird eine Instanz erstellt.</span><span class="sxs-lookup"><span data-stu-id="2da33-270">An instance is created for each request.</span></span>
* <span data-ttu-id="2da33-271">Alle Konstruktorabhängigkeiten werden durch [Dependency Injection](xref:fundamentals/dependency-injection) (DI) aufgefüllt.</span><span class="sxs-lookup"><span data-stu-id="2da33-271">Any constructor dependencies are populated by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span>

<span data-ttu-id="2da33-272">Filter, die als Attribute implementiert und Controllerklassen oder Aktionsmethoden direkt hinzugefügt werden, können keine Konstruktorabhängigkeiten von [Dependency Injection](xref:fundamentals/dependency-injection) (DI) erhalten.</span><span class="sxs-lookup"><span data-stu-id="2da33-272">Filters that are implemented as attributes and added directly to controller classes or action methods cannot have constructor dependencies provided by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="2da33-273">Konstruktorabhängigkeiten können aus folgenden Gründen von DI nicht bereitgestellt werden:</span><span class="sxs-lookup"><span data-stu-id="2da33-273">Constructor dependencies cannot be provided by DI because:</span></span>

* <span data-ttu-id="2da33-274">Für Attribute müssen Konstruktorparameter bereitgestellt werden, wenn sie angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="2da33-274">Attributes must have their constructor parameters supplied where they're applied.</span></span> 
* <span data-ttu-id="2da33-275">Dies ist eine Einschränkung der Funktionsweise von Attributen.</span><span class="sxs-lookup"><span data-stu-id="2da33-275">This is a limitation of how attributes work.</span></span>

<span data-ttu-id="2da33-276">Die folgenden Filter unterstützen von DI bereitgestellte Konstruktorabhängigkeiten:</span><span class="sxs-lookup"><span data-stu-id="2da33-276">The following filters support constructor dependencies provided from DI:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>
* <span data-ttu-id="2da33-277"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>, im Attribut implementiert</span><span class="sxs-lookup"><span data-stu-id="2da33-277"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implemented on the attribute.</span></span>

<span data-ttu-id="2da33-278">Die oben genannten Filter können auf einen Controller oder eine Aktionsmethode angewendet werden:</span><span class="sxs-lookup"><span data-stu-id="2da33-278">The preceding filters can be applied to a controller or action method:</span></span>

<span data-ttu-id="2da33-279">DI bietet Protokollierungen.</span><span class="sxs-lookup"><span data-stu-id="2da33-279">Loggers are available from DI.</span></span> <span data-ttu-id="2da33-280">Vermeiden Sie es jedoch, Filter nur zum Zweck der Protokollierung zu erstellen und zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="2da33-280">However, avoid creating and using filters purely for logging purposes.</span></span> <span data-ttu-id="2da33-281">Die integrierte [Frameworkprotokollierung](xref:fundamentals/logging/index) bietet in der Regel alle Elemente, die für die Protokollierung erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="2da33-281">The [built-in framework logging](xref:fundamentals/logging/index) typically provides what's needed for logging.</span></span> <span data-ttu-id="2da33-282">Wenn Protokollierung zu Filtern hinzugefügt wird, gilt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="2da33-282">Logging added to filters:</span></span>

* <span data-ttu-id="2da33-283">Die Protokollierung sollte mit Schwerpunkt auf geschäftlichen Aspekten oder verhaltensspezifisch für den Filter erfolgen.</span><span class="sxs-lookup"><span data-stu-id="2da33-283">Should focus on business domain concerns or behavior specific to the filter.</span></span>
* <span data-ttu-id="2da33-284">Aktionen oder andere Frameworkereignisse sollten **nicht** protokolliert werden.</span><span class="sxs-lookup"><span data-stu-id="2da33-284">Should **not** log actions or other framework events.</span></span> <span data-ttu-id="2da33-285">Die integrierten Filter protokollieren Aktionen und Frameworkereignisse.</span><span class="sxs-lookup"><span data-stu-id="2da33-285">The built-in filters log actions and framework events.</span></span>

### <a name="servicefilterattribute"></a><span data-ttu-id="2da33-286">ServiceFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="2da33-286">ServiceFilterAttribute</span></span>

<span data-ttu-id="2da33-287">Die Typen der Dienstfilterimplementierung werden in `ConfigureServices` registriert.</span><span class="sxs-lookup"><span data-stu-id="2da33-287">Service filter implementation types are registered in `ConfigureServices`.</span></span> <span data-ttu-id="2da33-288">Ein <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> ruft eine Instanz des Filter von DI ab.</span><span class="sxs-lookup"><span data-stu-id="2da33-288">A <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> retrieves an instance of the filter from DI.</span></span>

<span data-ttu-id="2da33-289">Der folgende Code zeigt den `AddHeaderResultServiceFilter`:</span><span class="sxs-lookup"><span data-stu-id="2da33-289">The following code shows the `AddHeaderResultServiceFilter`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="2da33-290">Im folgenden Code wird `AddHeaderResultServiceFilter` zum DI-Container hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="2da33-290">In the following code, `AddHeaderResultServiceFilter` is added to the DI container:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Startup.cs?name=snippet&highlight=4)]

<span data-ttu-id="2da33-291">Im folgenden Code ruft das `ServiceFilter`-Attribut eine Instanz des `AddHeaderResultServiceFilter`-Filters aus DI ab:</span><span class="sxs-lookup"><span data-stu-id="2da33-291">In the following code, the `ServiceFilter` attribute retrieves an instance of the `AddHeaderResultServiceFilter` filter from DI:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

<span data-ttu-id="2da33-292">Beim Verwenden von `ServiceFilterAttribute` wird [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable) festgelegt:</span><span class="sxs-lookup"><span data-stu-id="2da33-292">When using `ServiceFilterAttribute`, setting [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span></span>

* <span data-ttu-id="2da33-293">Gibt einen Hinweis darauf, dass die Filterinstanz *möglicherweise* außerhalb des Anforderungsbereichs, in dem sie erstellt wurde, wiederverwendet wird.</span><span class="sxs-lookup"><span data-stu-id="2da33-293">Provides a hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="2da33-294">Die ASP.NET Core-Runtime bietet keine Garantie für Folgendes:</span><span class="sxs-lookup"><span data-stu-id="2da33-294">The ASP.NET Core runtime doesn't guarantee:</span></span>

  * <span data-ttu-id="2da33-295">Eine einzelne Instanz des Filters wird erstellt.</span><span class="sxs-lookup"><span data-stu-id="2da33-295">That a single instance of the filter will be created.</span></span>
  * <span data-ttu-id="2da33-296">Der Filter wird nicht zu einem späteren Zeitpunkt vom DI-Container erneut angefordert.</span><span class="sxs-lookup"><span data-stu-id="2da33-296">The filter will not be re-requested from the DI container at some later point.</span></span>

* <span data-ttu-id="2da33-297">Sollte nicht mit einem Filter verwendet werden, der von Diensten mit einer anderen Lebensdauer als der eines Singletons abhängig ist.</span><span class="sxs-lookup"><span data-stu-id="2da33-297">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

 <span data-ttu-id="2da33-298"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implementiert <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span><span class="sxs-lookup"><span data-stu-id="2da33-298"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="2da33-299">`IFilterFactory` macht die <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*>-Methode zum Erstellen einer <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>-Instanz verfügbar.</span><span class="sxs-lookup"><span data-stu-id="2da33-299">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="2da33-300">`CreateInstance` lädt den angegebenen Typ aus DI.</span><span class="sxs-lookup"><span data-stu-id="2da33-300">`CreateInstance` loads the specified type from DI.</span></span>

### <a name="typefilterattribute"></a><span data-ttu-id="2da33-301">TypeFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="2da33-301">TypeFilterAttribute</span></span>

<span data-ttu-id="2da33-302"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> ähnelt <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>. Der zugehörige Typ wird allerdings nicht direkt vom DI-Container aufgelöst.</span><span class="sxs-lookup"><span data-stu-id="2da33-302"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> is similar to <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, but its type isn't resolved directly from the DI container.</span></span> <span data-ttu-id="2da33-303">Stattdessen wird der Typ mithilfe von <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName> instanziiert.</span><span class="sxs-lookup"><span data-stu-id="2da33-303">It instantiates the type by using <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span></span>

<span data-ttu-id="2da33-304">Da `TypeFilterAttribute`-Typen nicht direkt vom DI-Container aufgelöst werden, gilt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="2da33-304">Because `TypeFilterAttribute` types aren't resolved directly from the DI container:</span></span>

* <span data-ttu-id="2da33-305">Typen, auf die mithilfe von `TypeFilterAttribute` verwiesen wird, müssen nicht beim DI-Container registriert werden.</span><span class="sxs-lookup"><span data-stu-id="2da33-305">Types that are referenced using the `TypeFilterAttribute` don't need to be registered with the DI container.</span></span>  <span data-ttu-id="2da33-306">Ihre Abhängigkeiten werden vom DI-Container erfüllt.</span><span class="sxs-lookup"><span data-stu-id="2da33-306">They do have their dependencies fulfilled by the DI container.</span></span>
* <span data-ttu-id="2da33-307">`TypeFilterAttribute` kann optional Konstruktorargumente für den Typ akzeptieren.</span><span class="sxs-lookup"><span data-stu-id="2da33-307">`TypeFilterAttribute` can optionally accept constructor arguments for the type.</span></span>

<span data-ttu-id="2da33-308">Beim Verwenden von `TypeFilterAttribute` wird [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable) festgelegt:</span><span class="sxs-lookup"><span data-stu-id="2da33-308">When using `TypeFilterAttribute`, setting [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span></span>
* <span data-ttu-id="2da33-309">Gibt einen Hinweis darauf, dass die Filterinstanz *möglicherweise* außerhalb des Anforderungsbereichs wiederverwendet wird, in dem sie erstellt wurde.</span><span class="sxs-lookup"><span data-stu-id="2da33-309">Provides hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="2da33-310">Die ASP.NET Core-Runtime bietet keine Garantie dafür, dass eine einzelne Instanz des Filters erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="2da33-310">The ASP.NET Core runtime provides no guarantees that a single instance of the filter will be created.</span></span>

* <span data-ttu-id="2da33-311">Sollte nicht mit einem Filter verwendet werden, der von Diensten mit einer anderen Lebensdauer als der eines Singletons abhängig ist.</span><span class="sxs-lookup"><span data-stu-id="2da33-311">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

<span data-ttu-id="2da33-312">Das folgende Beispiel zeigt, wie Sie Argumente durch Verwendung von `TypeFilterAttribute` an einen Typ übergeben:</span><span class="sxs-lookup"><span data-stu-id="2da33-312">The following example shows how to pass arguments to a type using `TypeFilterAttribute`:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]

<!-- 
https://localhost:5001/home/hi?name=joe
VS debug window shows 
FiltersSample.Filters.LogConstantFilter:Information: Method 'Hi' called
-->

## <a name="authorization-filters"></a><span data-ttu-id="2da33-313">Autorisierungsfilter</span><span class="sxs-lookup"><span data-stu-id="2da33-313">Authorization filters</span></span>

<span data-ttu-id="2da33-314">Für Autorisierungsfilter gilt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="2da33-314">Authorization filters:</span></span>

* <span data-ttu-id="2da33-315">Sie werden als erste Filter in der Filterpipeline ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="2da33-315">Are the first filters run in the filter pipeline.</span></span>
* <span data-ttu-id="2da33-316">Sie steuern den Zugriff auf Aktionsmethoden.</span><span class="sxs-lookup"><span data-stu-id="2da33-316">Control access to action methods.</span></span>
* <span data-ttu-id="2da33-317">Sie verfügen nur über eine Methode, die vor der Aktion ausgeführt wird, nicht jedoch über eine Methode, die nach der Aktion ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="2da33-317">Have a before method, but no after method.</span></span>

<span data-ttu-id="2da33-318">Benutzerdefinierte Autorisierungsfilter erfordern ein benutzerdefiniertes Autorisierungsframework.</span><span class="sxs-lookup"><span data-stu-id="2da33-318">Custom authorization filters require a custom authorization framework.</span></span> <span data-ttu-id="2da33-319">Statt einen benutzerdefinierten Filter zu schreiben, sollten Sie die Autorisierungsrichtlinien konfigurieren oder eine benutzerdefinierte Autorisierungsrichtlinie schreiben.</span><span class="sxs-lookup"><span data-stu-id="2da33-319">Prefer configuring the authorization policies or writing a custom authorization policy over writing a custom filter.</span></span> <span data-ttu-id="2da33-320">Für den integrierten Autorisierungsfilter gilt:</span><span class="sxs-lookup"><span data-stu-id="2da33-320">The built-in authorization filter:</span></span>

* <span data-ttu-id="2da33-321">Er ruft das Autorisierungssystem auf.</span><span class="sxs-lookup"><span data-stu-id="2da33-321">Calls the authorization system.</span></span>
* <span data-ttu-id="2da33-322">Er autorisiert keine Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="2da33-322">Does not authorize requests.</span></span>

<span data-ttu-id="2da33-323">Lösen Sie **keine** Ausnahmen in Autorisierungsfiltern aus:</span><span class="sxs-lookup"><span data-stu-id="2da33-323">Do **not** throw exceptions within authorization filters:</span></span>

* <span data-ttu-id="2da33-324">Die Ausnahme wird nicht behandelt.</span><span class="sxs-lookup"><span data-stu-id="2da33-324">The exception will not be handled.</span></span>
* <span data-ttu-id="2da33-325">Ausnahmefilter behandeln die Ausnahme nicht.</span><span class="sxs-lookup"><span data-stu-id="2da33-325">Exception filters will not handle the exception.</span></span>

<span data-ttu-id="2da33-326">Beim Auftreten einer Ausnahme in einem Autorisierungsfilter sollten Sie eine Abfrage erwägen.</span><span class="sxs-lookup"><span data-stu-id="2da33-326">Consider issuing a challenge when an exception occurs in an authorization filter.</span></span>

<span data-ttu-id="2da33-327">Weitere Informationen finden Sie unter [Autorisierung](xref:security/authorization/introduction).</span><span class="sxs-lookup"><span data-stu-id="2da33-327">Learn more about [Authorization](xref:security/authorization/introduction).</span></span>

## <a name="resource-filters"></a><span data-ttu-id="2da33-328">Ressourcenfilter</span><span class="sxs-lookup"><span data-stu-id="2da33-328">Resource filters</span></span>

<span data-ttu-id="2da33-329">Für Ressourcenfilter gilt:</span><span class="sxs-lookup"><span data-stu-id="2da33-329">Resource filters:</span></span>

* <span data-ttu-id="2da33-330">Sie implementieren entweder die Schnittstelle <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> oder <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter>.</span><span class="sxs-lookup"><span data-stu-id="2da33-330">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> interface.</span></span>
* <span data-ttu-id="2da33-331">Die Ausführung umschließt den größten Teil der Filterpipeline.</span><span class="sxs-lookup"><span data-stu-id="2da33-331">Execution wraps most of the filter pipeline.</span></span>
* <span data-ttu-id="2da33-332">Nur [Autorisierungsfilter](#authorization-filters) werden vor Ressourcenfiltern ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="2da33-332">Only [Authorization filters](#authorization-filters) run before resource filters.</span></span>

<span data-ttu-id="2da33-333">Ressourcenfilter sind hilfreich, um den größten Teil der Pipeline kurzzuschließen.</span><span class="sxs-lookup"><span data-stu-id="2da33-333">Resource filters are useful to short-circuit most of the pipeline.</span></span> <span data-ttu-id="2da33-334">Ein Cachingfilter kann beispielsweise bei einem Cachetreffer den Rest der Pipeline überspringen.</span><span class="sxs-lookup"><span data-stu-id="2da33-334">For example, a caching filter can avoid the rest of the pipeline on a cache hit.</span></span>

<span data-ttu-id="2da33-335">Beispiele für Ressourcenfilter:</span><span class="sxs-lookup"><span data-stu-id="2da33-335">Resource filter examples:</span></span>

* <span data-ttu-id="2da33-336">Der oben gezeigte [Ressourcenfilter zum Kurzschließen](#short-circuiting-resource-filter).</span><span class="sxs-lookup"><span data-stu-id="2da33-336">[The short-circuiting resource filter](#short-circuiting-resource-filter) shown previously.</span></span>
* <span data-ttu-id="2da33-337">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span><span class="sxs-lookup"><span data-stu-id="2da33-337">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span></span>

  * <span data-ttu-id="2da33-338">Verhindert, dass die Modellbindung auf die Formulardaten zugreift.</span><span class="sxs-lookup"><span data-stu-id="2da33-338">Prevents model binding from accessing the form data.</span></span>
  * <span data-ttu-id="2da33-339">Wird bei großen Dateiuploads verwendet, um zu verhindern, dass die Formulardaten in den Arbeitsspeicher eingelesen werden.</span><span class="sxs-lookup"><span data-stu-id="2da33-339">Used for large file uploads to prevent the form data from being read into memory.</span></span>

## <a name="action-filters"></a><span data-ttu-id="2da33-340">Aktionsfilter</span><span class="sxs-lookup"><span data-stu-id="2da33-340">Action filters</span></span>

<span data-ttu-id="2da33-341">Aktionsfilter gelten **nicht** für Razor Seiten.</span><span class="sxs-lookup"><span data-stu-id="2da33-341">Action filters do **not** apply to Razor Pages.</span></span> <span data-ttu-id="2da33-342">Razor -Seiten unterstützen <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> und <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span><span class="sxs-lookup"><span data-stu-id="2da33-342">Razor Pages supports <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span></span> <span data-ttu-id="2da33-343">Weitere Informationen finden Sie unter [Filtermethoden für Razor Pages](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="2da33-343">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

<span data-ttu-id="2da33-344">Für Aktionsfilter gilt:</span><span class="sxs-lookup"><span data-stu-id="2da33-344">Action filters:</span></span>

* <span data-ttu-id="2da33-345">Sie implementieren entweder die Schnittstelle <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> oder <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter>.</span><span class="sxs-lookup"><span data-stu-id="2da33-345">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> interface.</span></span>
* <span data-ttu-id="2da33-346">Ihre Ausführung umfasst die Ausführung von Aktionsmethoden.</span><span class="sxs-lookup"><span data-stu-id="2da33-346">Their execution surrounds the execution of action methods.</span></span>

<span data-ttu-id="2da33-347">Der folgende Code zeigt einen Beispielaktionsfilter:</span><span class="sxs-lookup"><span data-stu-id="2da33-347">The following code shows a sample action filter:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="2da33-348"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> bietet folgende Eigenschaften:</span><span class="sxs-lookup"><span data-stu-id="2da33-348">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> provides the following properties:</span></span>

* <span data-ttu-id="2da33-349"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> ermöglicht das Lesen der Eingaben für eine Aktionsmethode.</span><span class="sxs-lookup"><span data-stu-id="2da33-349"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - enables reading the inputs to an action method.</span></span>
* <span data-ttu-id="2da33-350"><xref:Microsoft.AspNetCore.Mvc.Controller> ermöglicht das Ändern der Controllerinstanz.</span><span class="sxs-lookup"><span data-stu-id="2da33-350"><xref:Microsoft.AspNetCore.Mvc.Controller> - enables manipulating the controller instance.</span></span>
* <span data-ttu-id="2da33-351"><xref:System.Web.Mvc.ActionExecutingContext.Result>: Die Festlegung von `Result` schließt die Ausführung der Aktionsmethode und der nachfolgenden Aktionsfilter kurz.</span><span class="sxs-lookup"><span data-stu-id="2da33-351"><xref:System.Web.Mvc.ActionExecutingContext.Result> - setting `Result` short-circuits execution of the action method and subsequent action filters.</span></span>

<span data-ttu-id="2da33-352">Durch Auslösen einer Ausnahme in einer Aktionsmethode geschieht Folgendes:</span><span class="sxs-lookup"><span data-stu-id="2da33-352">Throwing an exception in an action method:</span></span>

* <span data-ttu-id="2da33-353">Die Ausführung nachfolgender Filter wird verhindert.</span><span class="sxs-lookup"><span data-stu-id="2da33-353">Prevents running of subsequent filters.</span></span>
* <span data-ttu-id="2da33-354">Im Gegensatz zum Festlegen von `Result` wird die ausgelöste Ausnahme wird nicht als erfolgreiches Ergebnis, sondern als Fehler behandelt.</span><span class="sxs-lookup"><span data-stu-id="2da33-354">Unlike setting `Result`, is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="2da33-355"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> bietet `Controller` und `Result` sowie die folgenden Eigenschaften:</span><span class="sxs-lookup"><span data-stu-id="2da33-355">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> provides `Controller` and `Result` plus the following properties:</span></span>

* <span data-ttu-id="2da33-356"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> ist TRUE, wenn die Aktionsausführung durch einen anderen Filter kurzgeschlossen wurde.</span><span class="sxs-lookup"><span data-stu-id="2da33-356"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> - True if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="2da33-357"><xref:System.Web.Mvc.ActionExecutedContext.Exception> ist ungleich NULL, wenn die Aktion oder ein zuvor ausgeführter Aktionsfilter eine Ausnahme ausgelöst hat.</span><span class="sxs-lookup"><span data-stu-id="2da33-357"><xref:System.Web.Mvc.ActionExecutedContext.Exception> - Non-null if the action or a previously run action filter threw an exception.</span></span> <span data-ttu-id="2da33-358">Durch Festlegen dieser Eigenschaft auf NULL geschieht Folgendes:</span><span class="sxs-lookup"><span data-stu-id="2da33-358">Setting this property to null:</span></span>

  * <span data-ttu-id="2da33-359">Die Ausnahme wird effektiv behandelt.</span><span class="sxs-lookup"><span data-stu-id="2da33-359">Effectively handles the exception.</span></span>
  * <span data-ttu-id="2da33-360">`Result` wird so ausgeführt, als wäre das Ergebnis von der Aktionsmethode zurückgegeben worden.</span><span class="sxs-lookup"><span data-stu-id="2da33-360">`Result` is executed as if it was returned from the action method.</span></span>

<span data-ttu-id="2da33-361">Bei einem `IAsyncActionFilter` hat der Aufruf von <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> folgende Auswirkungen:</span><span class="sxs-lookup"><span data-stu-id="2da33-361">For an `IAsyncActionFilter`, a call to the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span></span>

* <span data-ttu-id="2da33-362">Alle nachfolgenden Aktionsfilter und die Aktionsmethode werden ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="2da33-362">Executes any subsequent action filters and the action method.</span></span>
* <span data-ttu-id="2da33-363">Gibt `ActionExecutedContext`zurück.</span><span class="sxs-lookup"><span data-stu-id="2da33-363">Returns `ActionExecutedContext`.</span></span>

<span data-ttu-id="2da33-364">Weisen Sie einer Ergebnisinstanz <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> zu, und rufen Sie nicht `next` (den `ActionExecutionDelegate`) auf, um die Pipeline kurzzuschließen.</span><span class="sxs-lookup"><span data-stu-id="2da33-364">To short-circuit, assign <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> to a result instance and don't call `next` (the `ActionExecutionDelegate`).</span></span>

<span data-ttu-id="2da33-365">Das Framework stellt ein abstraktes <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> bereit, das als Unterklasse verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="2da33-365">The framework provides an abstract <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> that can be subclassed.</span></span>

<span data-ttu-id="2da33-366">Der Aktionsfilter `OnActionExecuting` kann für Folgendes verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="2da33-366">The `OnActionExecuting` action filter can be used to:</span></span>

* <span data-ttu-id="2da33-367">Überprüfen des Modellzustands.</span><span class="sxs-lookup"><span data-stu-id="2da33-367">Validate model state.</span></span>
* <span data-ttu-id="2da33-368">Zurückgeben eines Fehlers, wenn der Zustand ungültig ist.</span><span class="sxs-lookup"><span data-stu-id="2da33-368">Return an error if the state is invalid.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet)]

> [!NOTE]
> <span data-ttu-id="2da33-369">Controller, die mit dem-Attribut kommentiert werden, überprüfen `[ApiController]` automatisch den Modell Zustand und geben eine 400-Antwort zurück.</span><span class="sxs-lookup"><span data-stu-id="2da33-369">Controllers annotated with the `[ApiController]` attribute automatically validate model state and return a 400 response.</span></span> <span data-ttu-id="2da33-370">Weitere Informationen finden Sie unter [Automatische HTTP 400-Antworten](xref:web-api/index#automatic-http-400-responses).</span><span class="sxs-lookup"><span data-stu-id="2da33-370">For more information, see [Automatic HTTP 400 responses](xref:web-api/index#automatic-http-400-responses).</span></span>

<span data-ttu-id="2da33-371">Die Methode `OnActionExecuted` wird nach der Aktionsmethode ausgeführt. Es gilt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="2da33-371">The `OnActionExecuted` method runs after the action method:</span></span>

* <span data-ttu-id="2da33-372">Die Methode kann die Ergebnisse der Aktion durch die Eigenschaft <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> sehen und ändern.</span><span class="sxs-lookup"><span data-stu-id="2da33-372">And can see and manipulate the results of the action through the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> property.</span></span>
* <span data-ttu-id="2da33-373"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> wird auf TRUE festgelegt, wenn die Ausführung der Aktion durch einen anderen Filter kurzgeschlossen wurde.</span><span class="sxs-lookup"><span data-stu-id="2da33-373"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> is set to true if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="2da33-374"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> wird auf einen Wert ungleich NULL festgelegt, wenn die Aktion oder ein nachfolgender Aktionsfilter eine Ausnahme ausgelöst hat.</span><span class="sxs-lookup"><span data-stu-id="2da33-374"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> is set to a non-null value if the action or a subsequent action filter threw an exception.</span></span> <span data-ttu-id="2da33-375">Wenn für `Exception` NULL festgelegt wird,</span><span class="sxs-lookup"><span data-stu-id="2da33-375">Setting `Exception` to null:</span></span>

  * <span data-ttu-id="2da33-376">werden Ausnahmen effektiv behandelt,</span><span class="sxs-lookup"><span data-stu-id="2da33-376">Effectively handles an exception.</span></span>
  * <span data-ttu-id="2da33-377">und `ActionExecutedContext.Result` wird so ausgeführt, als würde die Eigenschaft normal von der Aktionsmethode zurückgegeben werden.</span><span class="sxs-lookup"><span data-stu-id="2da33-377">`ActionExecutedContext.Result` is executed as if it were returned normally from the action method.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet2&higlight=12-99)]

## <a name="exception-filters"></a><span data-ttu-id="2da33-378">Ausnahmefilter</span><span class="sxs-lookup"><span data-stu-id="2da33-378">Exception filters</span></span>

<span data-ttu-id="2da33-379">Für Ausnahmefilter gilt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="2da33-379">Exception filters:</span></span>

* <span data-ttu-id="2da33-380">Sie implementieren <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> oder <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span><span class="sxs-lookup"><span data-stu-id="2da33-380">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span></span>
* <span data-ttu-id="2da33-381">Sie können verwendet werden, um gängige Fehlerbehandlungsrichtlinien zu implementieren.</span><span class="sxs-lookup"><span data-stu-id="2da33-381">Can be used to implement common error handling policies.</span></span>

<span data-ttu-id="2da33-382">Der folgende Beispielausnahmefilter verwendet eine benutzerdefinierte Fehleransicht, um Details zu Ausnahmen anzuzeigen, die auftreten, wenn die App sich in der Entwicklung befindet:</span><span class="sxs-lookup"><span data-stu-id="2da33-382">The following sample exception filter uses a custom error view to display details about exceptions that occur when the app is in development:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/CustomExceptionFilter.cs?name=snippet_ExceptionFilter&highlight=16-19)]

<span data-ttu-id="2da33-383">Mit dem folgenden Code wird der Ausnahmefilter getestet:</span><span class="sxs-lookup"><span data-stu-id="2da33-383">The following code tests the exception filter:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Controllers/FailingController.cs?name=snippet)]

<span data-ttu-id="2da33-384">Für Ausnahmefilter gilt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="2da33-384">Exception filters:</span></span>

* <span data-ttu-id="2da33-385">Sie verfügen nicht über vorangehende oder darauffolgende Ereignisse.</span><span class="sxs-lookup"><span data-stu-id="2da33-385">Don't have before and after events.</span></span>
* <span data-ttu-id="2da33-386">Sie implementieren <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> oder <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span><span class="sxs-lookup"><span data-stu-id="2da33-386">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span></span>
* <span data-ttu-id="2da33-387">Behandeln von nicht behandelten Ausnahmen, die bei der Razor Erstellung von Seiten oder Controllern, [Modell Bindungen](xref:mvc/models/model-binding), Aktions filtern oder Aktionsmethoden auftreten.</span><span class="sxs-lookup"><span data-stu-id="2da33-387">Handle unhandled exceptions that occur in Razor Page or controller creation, [model binding](xref:mvc/models/model-binding), action filters, or action methods.</span></span>
* <span data-ttu-id="2da33-388">Sie erfassen **keine** Ausnahmen, die in Ressourcenfiltern, Ergebnisfiltern oder bei der Ausführung von MVC-Ergebnissen auftreten.</span><span class="sxs-lookup"><span data-stu-id="2da33-388">Do **not** catch exceptions that occur in resource filters, result filters, or MVC result execution.</span></span>

<span data-ttu-id="2da33-389">Um eine Ausnahme zu behandeln, muss die Eigenschaft <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> auf `true` festgelegt oder eine Antwort geschrieben werden.</span><span class="sxs-lookup"><span data-stu-id="2da33-389">To handle an exception, set the <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> property to `true` or write a response.</span></span> <span data-ttu-id="2da33-390">Dadurch wird die Weitergabe der Ausnahme beendet.</span><span class="sxs-lookup"><span data-stu-id="2da33-390">This stops propagation of the exception.</span></span> <span data-ttu-id="2da33-391">Ein Ausnahmefilter kann eine Ausnahme nicht in ein „erfolgreiches Ergebnis“ umwandeln.</span><span class="sxs-lookup"><span data-stu-id="2da33-391">An exception filter can't turn an exception into a "success".</span></span> <span data-ttu-id="2da33-392">Nur ein Aktionsfilter kann das.</span><span class="sxs-lookup"><span data-stu-id="2da33-392">Only an action filter can do that.</span></span>

<span data-ttu-id="2da33-393">Für Ausnahmefilter gilt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="2da33-393">Exception filters:</span></span>

* <span data-ttu-id="2da33-394">Sie eignen sich für das Abfangen von Ausnahmen, die in Aktionen auftreten.</span><span class="sxs-lookup"><span data-stu-id="2da33-394">Are good for trapping exceptions that occur within actions.</span></span>
* <span data-ttu-id="2da33-395">Sie sind im Vergleich zu Middleware für die Fehlerbehandlung weniger flexibel.</span><span class="sxs-lookup"><span data-stu-id="2da33-395">Are not as flexible as error handling middleware.</span></span>

<span data-ttu-id="2da33-396">Sie sollten bei der Ausnahmebehandlung vorzugsweise Middleware verwenden.</span><span class="sxs-lookup"><span data-stu-id="2da33-396">Prefer middleware for exception handling.</span></span> <span data-ttu-id="2da33-397">Verwenden Sie Ausnahmefilter nur, wenn sich die Fehlerbehandlung *unterscheidet*, je nachdem, welche Aktionsmethode aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="2da33-397">Use exception filters only where error handling *differs* based on which action method is called.</span></span> <span data-ttu-id="2da33-398">Zum Beispiel verfügt eine App möglicherweise über Aktionsmethoden für beide API-Endpunkte und für Ansichten bzw. HTML.</span><span class="sxs-lookup"><span data-stu-id="2da33-398">For example, an app might have action methods for both API endpoints and for views/HTML.</span></span> <span data-ttu-id="2da33-399">Die API-Endpunkte können Fehlerinformationen als JSON zurückgeben, während ansichtsbasierte Aktionen eine Fehlerseite als HTML zurückgeben können.</span><span class="sxs-lookup"><span data-stu-id="2da33-399">The API endpoints could return error information as JSON, while the view-based actions could return an error page as HTML.</span></span>

## <a name="result-filters"></a><span data-ttu-id="2da33-400">Ergebnisfilter</span><span class="sxs-lookup"><span data-stu-id="2da33-400">Result filters</span></span>

<span data-ttu-id="2da33-401">Für Ergebnisfilter gilt:</span><span class="sxs-lookup"><span data-stu-id="2da33-401">Result filters:</span></span>

* <span data-ttu-id="2da33-402">Implementieren einer Schnittstelle:</span><span class="sxs-lookup"><span data-stu-id="2da33-402">Implement an interface:</span></span>
  * <span data-ttu-id="2da33-403"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> oder <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="2da33-403"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
  * <span data-ttu-id="2da33-404"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> oder <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span><span class="sxs-lookup"><span data-stu-id="2da33-404"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span></span>
* <span data-ttu-id="2da33-405">Ihre Ausführung umfasst die Ausführung von Aktionsergebnissen.</span><span class="sxs-lookup"><span data-stu-id="2da33-405">Their execution surrounds the execution of action results.</span></span>

### <a name="iresultfilter-and-iasyncresultfilter"></a><span data-ttu-id="2da33-406">IResultFilter und IAsyncResultFilter</span><span class="sxs-lookup"><span data-stu-id="2da33-406">IResultFilter and IAsyncResultFilter</span></span>

<span data-ttu-id="2da33-407">Der folgende Code zeigt einen Ergebnisfilter, der einen HTTP-Header hinzufügt:</span><span class="sxs-lookup"><span data-stu-id="2da33-407">The following code shows a result filter that adds an HTTP header:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="2da33-408">Die Art des Ergebnisses, das ausgeführt wird, hängt von der jeweiligen Aktion ab.</span><span class="sxs-lookup"><span data-stu-id="2da33-408">The kind of result being executed depends on the action.</span></span> <span data-ttu-id="2da33-409">Eine Aktion, die eine Ansicht zurückgibt, beinhaltet die gesamte Razor-Verarbeitung als Teil der Ausführung von <xref:Microsoft.AspNetCore.Mvc.ViewResult>.</span><span class="sxs-lookup"><span data-stu-id="2da33-409">An action returning a view includes all razor processing as part of the <xref:Microsoft.AspNetCore.Mvc.ViewResult> being executed.</span></span> <span data-ttu-id="2da33-410">Eine API-Methode führt möglicherweise eine Art Serialisierung als Teil der Ausführung des Ergebnisses durch.</span><span class="sxs-lookup"><span data-stu-id="2da33-410">An API method might perform some serialization as part of the execution of the result.</span></span> <span data-ttu-id="2da33-411">Erfahren Sie mehr über [Aktions Ergebnisse](xref:mvc/controllers/actions).</span><span class="sxs-lookup"><span data-stu-id="2da33-411">Learn more about [action results](xref:mvc/controllers/actions).</span></span>

<span data-ttu-id="2da33-412">Ergebnisfilter werden nur ausgeführt, wenn eine Aktion oder ein Aktionsfilter ein Aktionsergebnis liefert.</span><span class="sxs-lookup"><span data-stu-id="2da33-412">Result filters are only executed when an action or action filter produces an action result.</span></span> <span data-ttu-id="2da33-413">Ergebnisfilter werden in folgenden Fällen nicht ausgeführt:</span><span class="sxs-lookup"><span data-stu-id="2da33-413">Result filters are not executed when:</span></span>

* <span data-ttu-id="2da33-414">Ein Autorisierungs- oder Ressourcenfilter schließt die Pipeline kurz.</span><span class="sxs-lookup"><span data-stu-id="2da33-414">An authorization filter or resource filter short-circuits the pipeline.</span></span>
* <span data-ttu-id="2da33-415">Ein Ausnahmefilter behandelt eine Ausnahme durch Erzeugen eines Aktionsergebnisses.</span><span class="sxs-lookup"><span data-stu-id="2da33-415">An exception filter handles an exception by producing an action result.</span></span>

<span data-ttu-id="2da33-416">Die Methode <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> kann die Ausführung des Aktionsergebnisses und der nachfolgenden Ergebnisfilter durch Festlegen von <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> auf `true` kurzschließen.</span><span class="sxs-lookup"><span data-stu-id="2da33-416">The <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> method can short-circuit execution of the action result and subsequent result filters by setting <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> to `true`.</span></span> <span data-ttu-id="2da33-417">Schreiben Sie beim Kurzschließen in das Antwortobjekt, um zu verhindern, dass eine leere Antwort generiert wird.</span><span class="sxs-lookup"><span data-stu-id="2da33-417">Write to the response object when short-circuiting to avoid generating an empty response.</span></span> <span data-ttu-id="2da33-418">Das Auslösen einer Ausnahme in `IResultFilter.OnResultExecuting` bewirkt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="2da33-418">Throwing an exception in `IResultFilter.OnResultExecuting`:</span></span>

* <span data-ttu-id="2da33-419">Die Ausführung des Aktionsergebnisses und der nachfolgenden Filter wird verhindert.</span><span class="sxs-lookup"><span data-stu-id="2da33-419">Prevents execution of the action result and subsequent filters.</span></span>
* <span data-ttu-id="2da33-420">Die ausgelöste Ausnahme wird nicht als erfolgreiches Ergebnis, sondern als Fehler behandelt.</span><span class="sxs-lookup"><span data-stu-id="2da33-420">Is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="2da33-421">Wenn die <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName>-Methode ausgeführt wird, wurde die Antwort wahrscheinlich bereits an den Client gesendet.</span><span class="sxs-lookup"><span data-stu-id="2da33-421">When the <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> method runs, the response has probably already been sent to the client.</span></span> <span data-ttu-id="2da33-422">Wenn die Antwort bereits an den Client gesendet wurde, kann sie nicht geändert werden.</span><span class="sxs-lookup"><span data-stu-id="2da33-422">If the response has already been sent to the client, it cannot be changed.</span></span>

<span data-ttu-id="2da33-423">`ResultExecutedContext.Canceled` wird auf `true` festgelegt, wenn die Ausführung des Aktionsergebnisses durch einen anderen Filter kurzgeschlossen wurde.</span><span class="sxs-lookup"><span data-stu-id="2da33-423">`ResultExecutedContext.Canceled` is set to `true` if the action result execution was short-circuited by another filter.</span></span>

<span data-ttu-id="2da33-424">`ResultExecutedContext.Exception` wird auf einen Wert ungleich NULL festgelegt, wenn das Aktionsergebnis oder ein nachfolgender Ergebnisfilter eine Ausnahme ausgelöst hat.</span><span class="sxs-lookup"><span data-stu-id="2da33-424">`ResultExecutedContext.Exception` is set to a non-null value if the action result or a subsequent result filter threw an exception.</span></span> <span data-ttu-id="2da33-425">Das Festlegen von `Exception` auf NULL behandelt eine Ausnahme und verhindert, dass die Ausnahme zu einem späteren Zeitpunkt in der Pipeline ein weiteres Mal ausgelöst wird.</span><span class="sxs-lookup"><span data-stu-id="2da33-425">Setting `Exception` to null effectively handles an exception and prevents the exception from being thrown again later in the pipeline.</span></span> <span data-ttu-id="2da33-426">Beim Behandeln einer Ausnahme in einem Ergebnisfilter gibt es keine zuverlässige Möglichkeit, Daten in eine Antwort zu schreiben.</span><span class="sxs-lookup"><span data-stu-id="2da33-426">There is no reliable way to write data to a response when handling an exception in a result filter.</span></span> <span data-ttu-id="2da33-427">Wenn ein Aktionsergebnis eine Ausnahme auslöst und die Header bereits an den Client übergeben wurden, gibt es keinen zuverlässigen Mechanismus, einen Fehlercode zu senden.</span><span class="sxs-lookup"><span data-stu-id="2da33-427">If the headers have been flushed to the client when an action result throws an exception, there's no reliable mechanism to send a failure code.</span></span>

<span data-ttu-id="2da33-428">Bei einem <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter> führt ein Aufruf von `await next` im <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> alle nachfolgenden Ergebnisfilter und das Aktionsergebnis aus.</span><span class="sxs-lookup"><span data-stu-id="2da33-428">For an <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, a call to `await next` on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> executes any subsequent result filters and the action result.</span></span> <span data-ttu-id="2da33-429">Zum Kurzschließen legen Sie [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) auf `true` fest und rufen den `ResultExecutionDelegate` nicht auf:</span><span class="sxs-lookup"><span data-stu-id="2da33-429">To short-circuit, set [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) to `true` and don't call the `ResultExecutionDelegate`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MyAsyncResponseFilter.cs?name=snippet)]

<span data-ttu-id="2da33-430">Das Framework stellt ein abstraktes `ResultFilterAttribute` bereit, das als Unterklasse verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="2da33-430">The framework provides an abstract `ResultFilterAttribute` that can be subclassed.</span></span> <span data-ttu-id="2da33-431">Die weiter oben gezeigte Klasse [AddHeaderAttribute](#add-header-attribute) ist ein Beispiel für ein Ergebnisfilterattribut.</span><span class="sxs-lookup"><span data-stu-id="2da33-431">The [AddHeaderAttribute](#add-header-attribute) class shown previously is an example of a result filter attribute.</span></span>

### <a name="ialwaysrunresultfilter-and-iasyncalwaysrunresultfilter"></a><span data-ttu-id="2da33-432">IAlwaysRunResultFilter und IAsyncAlwaysRunResultFilter</span><span class="sxs-lookup"><span data-stu-id="2da33-432">IAlwaysRunResultFilter and IAsyncAlwaysRunResultFilter</span></span>

<span data-ttu-id="2da33-433">Die Schnittstellen <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> und <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> deklarieren eine <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter>-Implementierung, die für alle Aktionsergebnisse ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="2da33-433">The <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> interfaces declare an <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> implementation that runs for all action results.</span></span> <span data-ttu-id="2da33-434">Dies schließt Aktionsergebnisse ein, die von folgenden Filtern generiert werden:</span><span class="sxs-lookup"><span data-stu-id="2da33-434">This includes action results produced by:</span></span>

* <span data-ttu-id="2da33-435">Autorisierungs- und Ressourcenfilter, die einen Kurzschluss bewirken</span><span class="sxs-lookup"><span data-stu-id="2da33-435">Authorization filters and resource filters that short-circuit.</span></span>
* <span data-ttu-id="2da33-436">Ausnahmefilter</span><span class="sxs-lookup"><span data-stu-id="2da33-436">Exception filters.</span></span>

<span data-ttu-id="2da33-437">Beispielsweise wird der folgende Filter immer ausgeführt und legt ein Aktionsergebnis (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) mit dem Statuscode *422 – Einheit kann nicht bearbeitet werden* fest, wenn bei der Inhaltsaushandlung ein Fehler auftritt:</span><span class="sxs-lookup"><span data-stu-id="2da33-437">For example, the following filter always runs and sets an action result (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) with a *422 Unprocessable Entity* status code when content negotiation fails:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/UnprocessableResultFilter.cs?name=snippet)]

### <a name="ifilterfactory"></a><span data-ttu-id="2da33-438">IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="2da33-438">IFilterFactory</span></span>

<span data-ttu-id="2da33-439"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implementiert <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="2da33-439"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span></span> <span data-ttu-id="2da33-440">Deshalb kann eine `IFilterFactory`-Instanz an einer beliebigen Stelle in der Filterpipeline als `IFilterMetadata`-Instanz verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="2da33-440">Therefore, an `IFilterFactory` instance can be used as an `IFilterMetadata` instance anywhere in the filter pipeline.</span></span> <span data-ttu-id="2da33-441">Wenn die Runtime den Aufruf des Filters vorbereitet, versucht sie, ihn in eine `IFilterFactory` umzuwandeln.</span><span class="sxs-lookup"><span data-stu-id="2da33-441">When the runtime prepares to invoke the filter, it attempts to cast it to an `IFilterFactory`.</span></span> <span data-ttu-id="2da33-442">Wenn diese Umwandlung gelingt, wird die Methode <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> aufgerufen, um die Instanz `IFilterMetadata` für den Aufruf zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="2da33-442">If that cast succeeds, the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method is called to create the `IFilterMetadata` instance that is invoked.</span></span> <span data-ttu-id="2da33-443">Da die exakte Filterpipeline beim Start der Anwendung nicht explizit festgelegt werden muss, wird dadurch ein sehr flexibles Design ermöglicht.</span><span class="sxs-lookup"><span data-stu-id="2da33-443">This provides a flexible design, since the precise filter pipeline doesn't need to be set explicitly when the app starts.</span></span>

<span data-ttu-id="2da33-444">Als weiteres Verfahren zum Erstellen von Filtern kann `IFilterFactory` mithilfe von benutzerdefinierten Attributimplementierungen implementiert werden:</span><span class="sxs-lookup"><span data-stu-id="2da33-444">`IFilterFactory` can be implemented using custom attribute implementations as another approach to creating filters:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

<span data-ttu-id="2da33-445">Der Filter wird im folgenden Code angewendet:</span><span class="sxs-lookup"><span data-stu-id="2da33-445">The filter is applied in the following code:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet3&highlight=21)]

<span data-ttu-id="2da33-446">Testen Sie den vorangehenden Code durch Ausführen des [Downloadbeispiels](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample):</span><span class="sxs-lookup"><span data-stu-id="2da33-446">Test the preceding code by running the [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample):</span></span>

* <span data-ttu-id="2da33-447">Rufen Sie die F12-Entwicklungstools auf.</span><span class="sxs-lookup"><span data-stu-id="2da33-447">Invoke the F12 developer tools.</span></span>
* <span data-ttu-id="2da33-448">Navigieren Sie zu `https://localhost:5001/Sample/HeaderWithFactory`.</span><span class="sxs-lookup"><span data-stu-id="2da33-448">Navigate to `https://localhost:5001/Sample/HeaderWithFactory`.</span></span>

<span data-ttu-id="2da33-449">Die F12-Entwicklungstools zeigen folgende Antwortheader an, die vom Beispielcode hinzugefügt wurden:</span><span class="sxs-lookup"><span data-stu-id="2da33-449">The F12 developer tools display the following response headers added by the sample code:</span></span>

* <span data-ttu-id="2da33-450">**Autor:**`Rick Anderson`</span><span class="sxs-lookup"><span data-stu-id="2da33-450">**author:** `Rick Anderson`</span></span>
* <span data-ttu-id="2da33-451">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span><span class="sxs-lookup"><span data-stu-id="2da33-451">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span></span>
* <span data-ttu-id="2da33-452">**intern:**`My header`</span><span class="sxs-lookup"><span data-stu-id="2da33-452">**internal:** `My header`</span></span>

<span data-ttu-id="2da33-453">Der oben stehende Code erstellt den Antwortheader „**internal:** `My header`“.</span><span class="sxs-lookup"><span data-stu-id="2da33-453">The preceding code creates the **internal:** `My header` response header.</span></span>

### <a name="ifilterfactory-implemented-on-an-attribute"></a><span data-ttu-id="2da33-454">In einem Attribut implementierte IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="2da33-454">IFilterFactory implemented on an attribute</span></span>

<!-- Review 
This section needs to be rewritten.
What's a non-named attribute?
-->

<span data-ttu-id="2da33-455">Filter, die `IFilterFactory` implementieren, eignen sich in folgenden Fällen:</span><span class="sxs-lookup"><span data-stu-id="2da33-455">Filters that implement `IFilterFactory` are useful for filters that:</span></span>

* <span data-ttu-id="2da33-456">Sie erfordern kein Übergeben von Parametern.</span><span class="sxs-lookup"><span data-stu-id="2da33-456">Don't require passing parameters.</span></span>
* <span data-ttu-id="2da33-457">Sie verfügen über Konstruktorabhängigkeiten, die durch DI erfüllt werden müssen.</span><span class="sxs-lookup"><span data-stu-id="2da33-457">Have constructor dependencies that need to be filled by DI.</span></span>

<span data-ttu-id="2da33-458"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implementiert <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span><span class="sxs-lookup"><span data-stu-id="2da33-458"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="2da33-459">`IFilterFactory` macht die <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*>-Methode zum Erstellen einer <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>-Instanz verfügbar.</span><span class="sxs-lookup"><span data-stu-id="2da33-459">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="2da33-460">`CreateInstance` lädt den angegebenen Typ aus dem Dienstecontainer (DI).</span><span class="sxs-lookup"><span data-stu-id="2da33-460">`CreateInstance` loads the specified type from the services container (DI).</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

<span data-ttu-id="2da33-461">Der folgende Code zeigt drei Ansätze zum Anwenden von `[SampleActionFilter]`:</span><span class="sxs-lookup"><span data-stu-id="2da33-461">The following code shows three approaches to applying the `[SampleActionFilter]`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet&highlight=1)]

<span data-ttu-id="2da33-462">Im oben stehenden Code ist das Ergänzen der Methode um `[SampleActionFilter]` der bevorzugte Ansatz zum Anwenden von `SampleActionFilter`.</span><span class="sxs-lookup"><span data-stu-id="2da33-462">In the preceding code, decorating the method with `[SampleActionFilter]` is the preferred approach to applying the `SampleActionFilter`.</span></span>

## <a name="using-middleware-in-the-filter-pipeline"></a><span data-ttu-id="2da33-463">Verwenden von Middleware in der Filterpipeline</span><span class="sxs-lookup"><span data-stu-id="2da33-463">Using middleware in the filter pipeline</span></span>

<span data-ttu-id="2da33-464">Ressourcenfilter funktionieren wie [Middleware](xref:fundamentals/middleware/index), sie umschließen alle Ausführungen, die später in der Pipeline enthalten sind.</span><span class="sxs-lookup"><span data-stu-id="2da33-464">Resource filters work like [middleware](xref:fundamentals/middleware/index) in that they surround the execution of everything that comes later in the pipeline.</span></span> <span data-ttu-id="2da33-465">Filter unterscheiden sich jedoch insofern von Middleware, dass sie Teil der Runtime sind, was bedeutet, dass sie Zugriff auf Kontext und Konstrukte haben.</span><span class="sxs-lookup"><span data-stu-id="2da33-465">But filters differ from middleware in that they're part of the runtime, which means that they have access to context and constructs.</span></span>

<span data-ttu-id="2da33-466">Um Middleware als Filter zu verwenden, erstellen Sie einen Typ mit einer `Configure`-Methode, die die Middleware festlegt, die in die Filterpipeline eingefügt werden soll.</span><span class="sxs-lookup"><span data-stu-id="2da33-466">To use middleware as a filter, create a type with a `Configure` method that specifies the middleware to inject into the filter pipeline.</span></span> <span data-ttu-id="2da33-467">Das folgende Beispiel verwendet die Lokalisierungsmiddleware, um die aktuelle Kultur einer Anforderung einzurichten:</span><span class="sxs-lookup"><span data-stu-id="2da33-467">The following example uses the localization middleware to establish the current culture for a request:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,22)]

<span data-ttu-id="2da33-468">Verwenden Sie das <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> zum Ausführen der Middleware:</span><span class="sxs-lookup"><span data-stu-id="2da33-468">Use the <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> to run the middleware:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

<span data-ttu-id="2da33-469">Middlewarefilter werden zum selben Zeitpunkt in der Filterpipeline wie Ressourcenfilter, vor der Modellbindung und nach dem Rest der Pipeline ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="2da33-469">Middleware filters run at the same stage of the filter pipeline as Resource filters, before model binding and after the rest of the pipeline.</span></span>

## <a name="next-actions"></a><span data-ttu-id="2da33-470">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="2da33-470">Next actions</span></span>

* <span data-ttu-id="2da33-471">Weitere Informationen finden Sie unter [Filter Methoden für Razor Seiten](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="2da33-471">See [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>
* <span data-ttu-id="2da33-472">Um Filter näher kennenzulernen, [laden Sie das GitHub-Beispiel herunter, und testen und bearbeiten Sie es](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample).</span><span class="sxs-lookup"><span data-stu-id="2da33-472">To experiment with filters, [download, test, and modify the GitHub sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="2da33-473">Von [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/) und [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="2da33-473">By [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="2da33-474">*Filter* ermöglichen es in ASP.NET Core, Code vor oder nach bestimmten Phasen der Anforderungsverarbeitungspipeline auszuführen.</span><span class="sxs-lookup"><span data-stu-id="2da33-474">*Filters* in ASP.NET Core allow code to be run before or after specific stages in the request processing pipeline.</span></span>

<span data-ttu-id="2da33-475">Integrierte Filter sind für folgende Aufgaben zuständig:</span><span class="sxs-lookup"><span data-stu-id="2da33-475">Built-in filters handle tasks such as:</span></span>

* <span data-ttu-id="2da33-476">Autorisierung (der Zugriff auf Ressourcen, für die ein Benutzer nicht autorisiert ist, wird verhindert)</span><span class="sxs-lookup"><span data-stu-id="2da33-476">Authorization (preventing access to resources a user isn't authorized for).</span></span>
* <span data-ttu-id="2da33-477">Zwischenspeicherung von Antworten (die Anforderungspipeline wird unterbrochen, damit eine zwischengespeicherte Antwort zurückgegeben wird)</span><span class="sxs-lookup"><span data-stu-id="2da33-477">Response caching (short-circuiting the request pipeline to return a cached response).</span></span>

<span data-ttu-id="2da33-478">Durch die Erstellung benutzerdefinierter Filter kann mit aktionsübergreifenden Problemen umgegangen werden.</span><span class="sxs-lookup"><span data-stu-id="2da33-478">Custom filters can be created to handle cross-cutting concerns.</span></span> <span data-ttu-id="2da33-479">Zu solchen übergreifenden Problemen gehören beispielsweise Fehlerbehandlung, Caching, Konfiguration, Autorisierung und Protokollierung.</span><span class="sxs-lookup"><span data-stu-id="2da33-479">Examples of cross-cutting concerns include error handling, caching, configuration, authorization, and logging.</span></span>  <span data-ttu-id="2da33-480">Mit Filtern lässt sich die Duplizierung von Code vermeiden.</span><span class="sxs-lookup"><span data-stu-id="2da33-480">Filters avoid duplicating code.</span></span> <span data-ttu-id="2da33-481">Sie können zum Beispiel die Fehlerbehandlung in einem Ausnahmefilter konsolidieren.</span><span class="sxs-lookup"><span data-stu-id="2da33-481">For example, an error handling exception filter could consolidate error handling.</span></span>

<span data-ttu-id="2da33-482">Dieses Dokument gilt für Razor Seiten, API-Controller und Controller mit Ansichten.</span><span class="sxs-lookup"><span data-stu-id="2da33-482">This document applies to Razor Pages, API controllers, and controllers with views.</span></span>

<span data-ttu-id="2da33-483">[Zeigen Sie ein Beispiel an, oder laden Sie es herunter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="2da33-483">[View or download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="how-filters-work"></a><span data-ttu-id="2da33-484">Die Funktionsweise von Filtern</span><span class="sxs-lookup"><span data-stu-id="2da33-484">How filters work</span></span>

<span data-ttu-id="2da33-485">Filter werden in der *ASP.NET Core-Aktionsaufrufpipeline* ausgeführt, die manchmal auch als *Filterpipeline* bezeichnet wird.</span><span class="sxs-lookup"><span data-stu-id="2da33-485">Filters run within the *ASP.NET Core action invocation pipeline*, sometimes referred to as the *filter pipeline*.</span></span>  <span data-ttu-id="2da33-486">Die Filterpipeline wird ausgeführt, nachdem ASP.NET Core die auszuführende Aktion ausgewählt hat.</span><span class="sxs-lookup"><span data-stu-id="2da33-486">The filter pipeline runs after ASP.NET Core selects the action to execute.</span></span>

![Die Anforderung wird von weiterer Middleware, Routingmiddleware, Aktionsauswahl und der ASP.NET Core-Aktionsaufrufpipeline verarbeitet.](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a><span data-ttu-id="2da33-489">Filtertypen</span><span class="sxs-lookup"><span data-stu-id="2da33-489">Filter types</span></span>

<span data-ttu-id="2da33-490">Jeder Filtertyp wird in einer anderen Phase der Filterpipeline ausgeführt:</span><span class="sxs-lookup"><span data-stu-id="2da33-490">Each filter type is executed at a different stage in the filter pipeline:</span></span>

* <span data-ttu-id="2da33-491">[Autorisierungsfilter](#authorization-filters) werden zuerst ausgeführt und dienen dazu, festzustellen, ob der Benutzer für die Anforderung berechtigt ist.</span><span class="sxs-lookup"><span data-stu-id="2da33-491">[Authorization filters](#authorization-filters) run first and are used to determine whether the user is authorized for the request.</span></span> <span data-ttu-id="2da33-492">Autorisierungsfilter schließen die Pipeline kurz, wenn die Anforderung nicht autorisiert ist.</span><span class="sxs-lookup"><span data-stu-id="2da33-492">Authorization filters short-circuit the pipeline if the request is unauthorized.</span></span>

* <span data-ttu-id="2da33-493">[Ressourcen Filter](#resource-filters):</span><span class="sxs-lookup"><span data-stu-id="2da33-493">[Resource filters](#resource-filters):</span></span>

  * <span data-ttu-id="2da33-494">Werden nach der Autorisierung ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="2da33-494">Run after authorization.</span></span>  
  * <span data-ttu-id="2da33-495"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> kann Code vor dem Rest der Filterpipeline ausführen.</span><span class="sxs-lookup"><span data-stu-id="2da33-495"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> can run code before the rest of the filter pipeline.</span></span> <span data-ttu-id="2da33-496">Beispielsweise kann `OnResourceExecuting` Code vor der Modellbindung ausführen.</span><span class="sxs-lookup"><span data-stu-id="2da33-496">For example, `OnResourceExecuting` can run code before model binding.</span></span>
  * <span data-ttu-id="2da33-497"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> kann Code nach Abschluss der restlichen Pipeline ausführen.</span><span class="sxs-lookup"><span data-stu-id="2da33-497"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> can run code after the rest of the pipeline has completed.</span></span>

* <span data-ttu-id="2da33-498">[Aktionsfilter](#action-filters) können, vor und nach dem Aufruf einer individuelle Aktionsmethode, Code ausführen.</span><span class="sxs-lookup"><span data-stu-id="2da33-498">[Action filters](#action-filters) can run code immediately before and after an individual action method is called.</span></span> <span data-ttu-id="2da33-499">Sie können zur Bearbeitung der Argumente, die an eine Aktion übermittelt werden, und des Ergebnisses, das von der Aktion zurückgegeben wird, verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="2da33-499">They can be used to manipulate the arguments passed into an action and the result returned from the action.</span></span> <span data-ttu-id="2da33-500">Aktionsfilter werden in Seiten **nicht** unterstützt Razor .</span><span class="sxs-lookup"><span data-stu-id="2da33-500">Action filters are **not** supported in Razor Pages.</span></span>

* <span data-ttu-id="2da33-501">[Ausnahmefilter](#exception-filters) werden dazu verwendet, globale Richtlinien auf unbehandelte Ausnahmen anzuwenden, die auftreten, bevor etwas in den Antworttext geschrieben wurde.</span><span class="sxs-lookup"><span data-stu-id="2da33-501">[Exception filters](#exception-filters) are used to apply global policies to unhandled exceptions that occur before anything has been written to the response body.</span></span>

* <span data-ttu-id="2da33-502">[Ergebnisfilter](#result-filters) können, vor und nach dem Ergebnis einer ausgeführten individuellen Aktion, Code ausführen.</span><span class="sxs-lookup"><span data-stu-id="2da33-502">[Result filters](#result-filters) can run code immediately before and after the execution of individual action results.</span></span> <span data-ttu-id="2da33-503">Sie werden nur ausgeführt, wenn die Aktionsmethode erfolgreich ausgeführt wurde.</span><span class="sxs-lookup"><span data-stu-id="2da33-503">They run only when the action method has executed successfully.</span></span> <span data-ttu-id="2da33-504">Ergebnisfilter sind hilfreich für Logik, die die Ausführung von Ansichten oder Formatierern umfasst.</span><span class="sxs-lookup"><span data-stu-id="2da33-504">They are useful for logic that must surround view or formatter execution.</span></span>

<span data-ttu-id="2da33-505">Das folgende Diagramm veranschaulicht, wie die Filtertypen mit der Filterpipeline interagieren.</span><span class="sxs-lookup"><span data-stu-id="2da33-505">The following diagram shows how filter types interact in the filter pipeline.</span></span>

![Die Anforderung wird von Autorisierungsfilter, Ressourcenfilter, Modellbindung, Aktionsfilter, Aktionsausführung sowie Aktionsergebniskonvertierung, Ausnahmefilter, Ergebnisfilter und Ergebnisausführung verarbeitet.](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a><span data-ttu-id="2da33-508">Implementierung</span><span class="sxs-lookup"><span data-stu-id="2da33-508">Implementation</span></span>

<span data-ttu-id="2da33-509">Filter unterstützen sowohl synchrone als auch asynchrone Implementierungen über verschiedene Schnittstellendefinitionen.</span><span class="sxs-lookup"><span data-stu-id="2da33-509">Filters support both synchronous and asynchronous implementations through different interface definitions.</span></span>

<span data-ttu-id="2da33-510">Synchrone Filter können Code vor (`On-Stage-Executing`) und nach (`On-Stage-Executed`) der jeweiligen Pipelinephase ausführen.</span><span class="sxs-lookup"><span data-stu-id="2da33-510">Synchronous filters can run code before (`On-Stage-Executing`) and after (`On-Stage-Executed`) their pipeline stage.</span></span> <span data-ttu-id="2da33-511">`OnActionExecuting` beispielsweise wird aufgerufen, bevor die Aktionsmethode aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="2da33-511">For example, `OnActionExecuting` is called before the action method is called.</span></span> <span data-ttu-id="2da33-512">`OnActionExecuted` wird nach der Rückgabe der Aktionsmethode aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="2da33-512">`OnActionExecuted` is called after the action method returns.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="2da33-513">Asynchrone Filter definieren eine `On-Stage-ExecutionAsync`-Methode:</span><span class="sxs-lookup"><span data-stu-id="2da33-513">Asynchronous filters define an `On-Stage-ExecutionAsync` method:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/SampleAsyncActionFilter.cs?name=snippet)]

<span data-ttu-id="2da33-514">Im oben stehenden Code weist der `SampleAsyncActionFilter` einen <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`) auf, der die Aktionsmethode ausführt.</span><span class="sxs-lookup"><span data-stu-id="2da33-514">In the preceding code, the `SampleAsyncActionFilter` has an <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`)  that executes the action method.</span></span>  <span data-ttu-id="2da33-515">Jede der `On-Stage-ExecutionAsync`-Methoden akzeptiert einen `FilterType-ExecutionDelegate`, der die Pipelinephase des Filters ausführt.</span><span class="sxs-lookup"><span data-stu-id="2da33-515">Each of the `On-Stage-ExecutionAsync` methods take a `FilterType-ExecutionDelegate` that executes the filter's pipeline stage.</span></span>

### <a name="multiple-filter-stages"></a><span data-ttu-id="2da33-516">Mehrere Filterphasen</span><span class="sxs-lookup"><span data-stu-id="2da33-516">Multiple filter stages</span></span>

<span data-ttu-id="2da33-517">Schnittstellen für mehrere Filterphasen können in einer einzigen Klasse implementiert werden.</span><span class="sxs-lookup"><span data-stu-id="2da33-517">Interfaces for multiple filter stages can be implemented in a single class.</span></span> <span data-ttu-id="2da33-518">Beispielsweise implementiert die Klasse <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>`IActionFilter`, `IResultFilter`, und deren asynchrone Gegenstücke.</span><span class="sxs-lookup"><span data-stu-id="2da33-518">For example, the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> class implements `IActionFilter`, `IResultFilter`, and their async equivalents.</span></span>

<span data-ttu-id="2da33-519">Implementieren Sie **entweder** die synchrone oder asynchrone Version einer Filterschnittstelle, aber **nicht** beide.</span><span class="sxs-lookup"><span data-stu-id="2da33-519">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="2da33-520">Die Runtime prüft zuerst, ob der Filter die asynchrone Schnittstelle implementiert, und wenn dies der Fall ist, ruft die Runtime diese Schnittstelle auf.</span><span class="sxs-lookup"><span data-stu-id="2da33-520">The runtime checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="2da33-521">Wenn dies nicht der Fall ist, ruft es die Methode(n) der synchronen Schnittstelle auf.</span><span class="sxs-lookup"><span data-stu-id="2da33-521">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="2da33-522">Wenn die asynchrone und die synchrone Schnittstelle in einer einzigen Klasse implementiert sind, wird nur die asynchrone Methode aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="2da33-522">If both asynchronous and synchronous interfaces are implemented in one class, only the async method is called.</span></span> <span data-ttu-id="2da33-523">Bei Verwendung von abstrakten Klassen wie <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> überschreiben Sie nur die synchronen Methoden oder die asynchrone Methode für jeden Filtertyp.</span><span class="sxs-lookup"><span data-stu-id="2da33-523">When using abstract classes like <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> override only the synchronous methods or the async method for each filter type.</span></span>

### <a name="built-in-filter-attributes"></a><span data-ttu-id="2da33-524">Integrierte Filterattribute</span><span class="sxs-lookup"><span data-stu-id="2da33-524">Built-in filter attributes</span></span>

<span data-ttu-id="2da33-525">ASP.NET Core enthält integrierte attributbasierte Filter, die als Unterklasse erstellt und angepasst werden können.</span><span class="sxs-lookup"><span data-stu-id="2da33-525">ASP.NET Core includes built-in attribute-based filters that can be subclassed and customized.</span></span> <span data-ttu-id="2da33-526">Zum Beispiel fügt der folgende Ergebnisfilter der Antwort einen Header hinzu:</span><span class="sxs-lookup"><span data-stu-id="2da33-526">For example, the following result filter adds a header to the response:</span></span>

<a name="add-header-attribute"></a>

[!code-csharp[](./filters/sample/FiltersSample/Filters/AddHeaderAttribute.cs?name=snippet)]

<span data-ttu-id="2da33-527">Wie im obigen Beispiel gezeigt, können Filter mithilfe von Attributen Argumente akzeptieren.</span><span class="sxs-lookup"><span data-stu-id="2da33-527">Attributes allow filters to accept arguments, as shown in the preceding example.</span></span> <span data-ttu-id="2da33-528">Wenden Sie das `AddHeaderAttribute` auf einen Controller oder eine Aktionsmethode an, und geben Sie den Namen und den Wert des HTTP-Headers an:</span><span class="sxs-lookup"><span data-stu-id="2da33-528">Apply the `AddHeaderAttribute` to a controller or action method and specify the name and value of the HTTP header:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

<!-- `https://localhost:5001/Sample` -->

<span data-ttu-id="2da33-529">Einige der Filterschnittstellen besitzen entsprechende Attribute, die als Basisklassen für benutzerdefinierte Implementierungen verwendet werden können.</span><span class="sxs-lookup"><span data-stu-id="2da33-529">Several of the filter interfaces have corresponding attributes that can be used as base classes for custom implementations.</span></span>

<span data-ttu-id="2da33-530">Filterattribute:</span><span class="sxs-lookup"><span data-stu-id="2da33-530">Filter attributes:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ExceptionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>

## <a name="filter-scopes-and-order-of-execution"></a><span data-ttu-id="2da33-531">Filterbereiche und Reihenfolge der Ausführung</span><span class="sxs-lookup"><span data-stu-id="2da33-531">Filter scopes and order of execution</span></span>

<span data-ttu-id="2da33-532">Der Pipeline kann in einem von drei *Bereichen* ein Filter hinzugefügt werden:</span><span class="sxs-lookup"><span data-stu-id="2da33-532">A filter can be added to the pipeline at one of three *scopes*:</span></span>

* <span data-ttu-id="2da33-533">Verwenden eines Attributs in einer Aktion</span><span class="sxs-lookup"><span data-stu-id="2da33-533">Using an attribute on an action.</span></span>
* <span data-ttu-id="2da33-534">Verwenden eines Attributs in einem Controller</span><span class="sxs-lookup"><span data-stu-id="2da33-534">Using an attribute on a controller.</span></span>
* <span data-ttu-id="2da33-535">Global für alle Controller und Aktionen, wie in folgendem Code gezeigt:</span><span class="sxs-lookup"><span data-stu-id="2da33-535">Globally for all controllers and actions as shown in the following code:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/StartupGF.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="2da33-536">Der oben stehende Code fügt mithilfe der Sammlung [MvcOptions.Filters](xref:Microsoft.AspNetCore.Mvc.MvcOptions.Filters) global drei Filter hinzu.</span><span class="sxs-lookup"><span data-stu-id="2da33-536">The preceding code adds three filters globally using the [MvcOptions.Filters](xref:Microsoft.AspNetCore.Mvc.MvcOptions.Filters) collection.</span></span>

### <a name="default-order-of-execution"></a><span data-ttu-id="2da33-537">Standardreihenfolge der Ausführung</span><span class="sxs-lookup"><span data-stu-id="2da33-537">Default order of execution</span></span>

<span data-ttu-id="2da33-538">Wenn mehrere Filter *des gleichen Typs* vorhanden sind, bestimmt der Umfang die Standardreihenfolge ihrer Ausführung.</span><span class="sxs-lookup"><span data-stu-id="2da33-538">When there are multiple filters *of the same type*, scope determines the default order of filter execution.</span></span>  <span data-ttu-id="2da33-539">Globale Filter umschließen Klassenfilter.</span><span class="sxs-lookup"><span data-stu-id="2da33-539">Global filters surround class filters.</span></span> <span data-ttu-id="2da33-540">Klassenfilter umschließen Methodenfilter.</span><span class="sxs-lookup"><span data-stu-id="2da33-540">Class filters surround method filters.</span></span>

<span data-ttu-id="2da33-541">Als Ergebnis der Filterschachtelung wird der *nach* einer Pipelinephase auszuführende Code in umgekehrter Reihenfolge zum Code *vor* einer Pipelinephase ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="2da33-541">As a result of filter nesting, the *after* code of filters runs in the reverse order of the *before* code.</span></span> <span data-ttu-id="2da33-542">Filterreihenfolge:</span><span class="sxs-lookup"><span data-stu-id="2da33-542">The filter sequence:</span></span>

* <span data-ttu-id="2da33-543">Der Code von globalen Filtern, der *vor* einer Pipelinephase ausgeführt werden soll</span><span class="sxs-lookup"><span data-stu-id="2da33-543">The *before* code of global filters.</span></span>
  * <span data-ttu-id="2da33-544">Der Code von Controllerfiltern, der *vor* einer Pipelinephase ausgeführt werden soll</span><span class="sxs-lookup"><span data-stu-id="2da33-544">The *before* code of controller filters.</span></span>
    * <span data-ttu-id="2da33-545">Der Code von Aktionsmethodenfiltern, der *vor* einer Pipelinephase ausgeführt werden soll</span><span class="sxs-lookup"><span data-stu-id="2da33-545">The *before* code of action method filters.</span></span>
    * <span data-ttu-id="2da33-546">Der Code von Aktionsmethodenfiltern, der *nach* einer Pipelinephase ausgeführt werden soll</span><span class="sxs-lookup"><span data-stu-id="2da33-546">The *after* code of action method filters.</span></span>
  * <span data-ttu-id="2da33-547">Der Code von Controllerfiltern, der *nach* einer Pipelinephase ausgeführt werden soll</span><span class="sxs-lookup"><span data-stu-id="2da33-547">The *after* code of controller filters.</span></span>
* <span data-ttu-id="2da33-548">Der Code von globalen Filtern, der *nach* einer Pipelinephase ausgeführt werden soll</span><span class="sxs-lookup"><span data-stu-id="2da33-548">The *after* code of global filters.</span></span>
  
<span data-ttu-id="2da33-549">Das folgende Beispiel veranschaulicht die Reihenfolge, in der Filtermethoden für synchrone Aktionsfilter aufgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="2da33-549">The following example that illustrates the order in which filter methods are called for synchronous action filters.</span></span>

| <span data-ttu-id="2da33-550">Sequenz</span><span class="sxs-lookup"><span data-stu-id="2da33-550">Sequence</span></span> | <span data-ttu-id="2da33-551">Filterbereich</span><span class="sxs-lookup"><span data-stu-id="2da33-551">Filter scope</span></span> | <span data-ttu-id="2da33-552">Filtermethode</span><span class="sxs-lookup"><span data-stu-id="2da33-552">Filter method</span></span> |
|:--------:|:------------:|:-------------:|
| <span data-ttu-id="2da33-553">1</span><span class="sxs-lookup"><span data-stu-id="2da33-553">1</span></span> | <span data-ttu-id="2da33-554">Global</span><span class="sxs-lookup"><span data-stu-id="2da33-554">Global</span></span> | `OnActionExecuting` |
| <span data-ttu-id="2da33-555">2</span><span class="sxs-lookup"><span data-stu-id="2da33-555">2</span></span> | <span data-ttu-id="2da33-556">Controller</span><span class="sxs-lookup"><span data-stu-id="2da33-556">Controller</span></span> | `OnActionExecuting` |
| <span data-ttu-id="2da33-557">3</span><span class="sxs-lookup"><span data-stu-id="2da33-557">3</span></span> | <span data-ttu-id="2da33-558">Methode</span><span class="sxs-lookup"><span data-stu-id="2da33-558">Method</span></span> | `OnActionExecuting` |
| <span data-ttu-id="2da33-559">4</span><span class="sxs-lookup"><span data-stu-id="2da33-559">4</span></span> | <span data-ttu-id="2da33-560">Methode</span><span class="sxs-lookup"><span data-stu-id="2da33-560">Method</span></span> | `OnActionExecuted` |
| <span data-ttu-id="2da33-561">5</span><span class="sxs-lookup"><span data-stu-id="2da33-561">5</span></span> | <span data-ttu-id="2da33-562">Controller</span><span class="sxs-lookup"><span data-stu-id="2da33-562">Controller</span></span> | `OnActionExecuted` |
| <span data-ttu-id="2da33-563">6</span><span class="sxs-lookup"><span data-stu-id="2da33-563">6</span></span> | <span data-ttu-id="2da33-564">Global</span><span class="sxs-lookup"><span data-stu-id="2da33-564">Global</span></span> | `OnActionExecuted` |

<span data-ttu-id="2da33-565">Diese Sequenz veranschaulicht Folgendes:</span><span class="sxs-lookup"><span data-stu-id="2da33-565">This sequence shows:</span></span>

* <span data-ttu-id="2da33-566">Der Methodenfilter ist innerhalb des Controllerfilters geschachtelt.</span><span class="sxs-lookup"><span data-stu-id="2da33-566">The method filter is nested within the controller filter.</span></span>
* <span data-ttu-id="2da33-567">Der Controllerfilter ist innerhalb des globalen Filters geschachtelt.</span><span class="sxs-lookup"><span data-stu-id="2da33-567">The controller filter is nested within the global filter.</span></span>

### <a name="controller-and-no-locrazor-page-level-filters"></a><span data-ttu-id="2da33-568">Filter auf Controller-und Razor Seitenebene</span><span class="sxs-lookup"><span data-stu-id="2da33-568">Controller and Razor Page level filters</span></span>

<span data-ttu-id="2da33-569">Jeder Controller, der von der Basisklasse <xref:Microsoft.AspNetCore.Mvc.Controller> erbt, enthält die Methoden [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*), [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*) und [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted`.</span><span class="sxs-lookup"><span data-stu-id="2da33-569">Every controller that inherits from the <xref:Microsoft.AspNetCore.Mvc.Controller> base class includes [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*),  [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*), and [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted` methods.</span></span> <span data-ttu-id="2da33-570">Diese Methoden führen Folgendes aus:</span><span class="sxs-lookup"><span data-stu-id="2da33-570">These methods:</span></span>

* <span data-ttu-id="2da33-571">Die Filter, die für eine bestimmte Aktion ausgeführt werden, werden umschlossen.</span><span class="sxs-lookup"><span data-stu-id="2da33-571">Wrap the filters that run for a given action.</span></span>
* <span data-ttu-id="2da33-572">`OnActionExecuting` wird vor allen Filtern der Aktion aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="2da33-572">`OnActionExecuting` is called before any of the action's filters.</span></span>
* <span data-ttu-id="2da33-573">`OnActionExecuted` wird nach allen Filtern der Aktion aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="2da33-573">`OnActionExecuted` is called after all of the action filters.</span></span>
* <span data-ttu-id="2da33-574">`OnActionExecutionAsync` wird vor allen Filtern der Aktion aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="2da33-574">`OnActionExecutionAsync` is called before any of the action's filters.</span></span> <span data-ttu-id="2da33-575">Code im Filter nach `next` wird nach der Aktionsmethode ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="2da33-575">Code in the filter after `next` runs after the action method.</span></span>

<span data-ttu-id="2da33-576">Im Downloadbeispiel wird `MySampleActionFilter` global beim Start angewendet.</span><span class="sxs-lookup"><span data-stu-id="2da33-576">For example, in the download sample, `MySampleActionFilter` is applied globally in startup.</span></span>

<span data-ttu-id="2da33-577">Die `TestController`:</span><span class="sxs-lookup"><span data-stu-id="2da33-577">The `TestController`:</span></span>

* <span data-ttu-id="2da33-578">Wendet das `SampleActionFilterAttribute` (`[SampleActionFilter]`) auf die Aktion `FilterTest2` an.</span><span class="sxs-lookup"><span data-stu-id="2da33-578">Applies the `SampleActionFilterAttribute` (`[SampleActionFilter]`) to the `FilterTest2` action.</span></span>
* <span data-ttu-id="2da33-579">Überschreibt `OnActionExecuting` und `OnActionExecuted`:</span><span class="sxs-lookup"><span data-stu-id="2da33-579">Overrides `OnActionExecuting` and `OnActionExecuted`.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/TestController.cs?name=snippet)]

<span data-ttu-id="2da33-580">Durch Navigieren zu `https://localhost:5001/Test/FilterTest2` wird der folgende Code ausgeführt:</span><span class="sxs-lookup"><span data-stu-id="2da33-580">Navigating to `https://localhost:5001/Test/FilterTest2` runs the following code:</span></span>

* `TestController.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `SampleActionFilterAttribute.OnActionExecuting`
      * `TestController.FilterTest2`
    * `SampleActionFilterAttribute.OnActionExecuted`
  * `MySampleActionFilter.OnActionExecuted`
* `TestController.OnActionExecuted`

<span data-ttu-id="2da33-581">Informationen zu Razor Seiten finden Sie unter [Implementieren von Seiten Razor Filtern durch Überschreiben von Filter Methoden](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span><span class="sxs-lookup"><span data-stu-id="2da33-581">For Razor Pages, see [Implement Razor Page filters by overriding filter methods](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span></span>

### <a name="overriding-the-default-order"></a><span data-ttu-id="2da33-582">Überschreiben der Standardreihenfolge</span><span class="sxs-lookup"><span data-stu-id="2da33-582">Overriding the default order</span></span>

<span data-ttu-id="2da33-583">Die Standardreihenfolge der Ausführung kann durch Implementieren von <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter> überschrieben werden.</span><span class="sxs-lookup"><span data-stu-id="2da33-583">The default sequence of execution can be overridden by implementing <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span></span> <span data-ttu-id="2da33-584">`IOrderedFilter` macht die Eigenschaft <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> verfügbar, die bei der Bestimmung der Ausführungsreihenfolge Vorrang vor dem Bereich hat.</span><span class="sxs-lookup"><span data-stu-id="2da33-584">`IOrderedFilter` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> property that takes precedence over scope to determine the order of execution.</span></span> <span data-ttu-id="2da33-585">Für einen Filter mit geringerem `Order`-Wert gilt:</span><span class="sxs-lookup"><span data-stu-id="2da33-585">A filter with a lower `Order` value:</span></span>

* <span data-ttu-id="2da33-586">Führt den Code, der *vor* einer Pipelinephase ausgeführt werden soll, vor dem Code eines Filters mit einem höheren Wert für `Order` aus.</span><span class="sxs-lookup"><span data-stu-id="2da33-586">Runs the *before* code before that of a filter with a higher value of `Order`.</span></span>
* <span data-ttu-id="2da33-587">Führt den Code, der *nach* einer Pipelinephase ausgeführt werden soll, nach dem Code eines Filters mit einem höheren Wert für `Order` aus.</span><span class="sxs-lookup"><span data-stu-id="2da33-587">Runs the *after* code after that of a filter with a higher `Order` value.</span></span>

<span data-ttu-id="2da33-588">Die Eigenschaft `Order` kann mit einem Konstruktorparameter festgelegt werden:</span><span class="sxs-lookup"><span data-stu-id="2da33-588">The `Order` property can be set with a constructor parameter:</span></span>

```csharp
[MyFilter(Name = "Controller Level Attribute", Order=1)]
```

<span data-ttu-id="2da33-589">Sehen Sie sich die drei Aktionsfilter an, die im vorherigen Beispiel gezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="2da33-589">Consider the same 3 action filters shown in the preceding example.</span></span> <span data-ttu-id="2da33-590">Wenn die `Order`-Eigenschaft des Controllers und der globalen Filter auf 1 bzw. 2 festgelegt ist, wird die Reihenfolge der Ausführung umgekehrt.</span><span class="sxs-lookup"><span data-stu-id="2da33-590">If the `Order` property of the controller and global filters is set to 1 and 2 respectively, the order of execution is reversed.</span></span>

| <span data-ttu-id="2da33-591">Sequenz</span><span class="sxs-lookup"><span data-stu-id="2da33-591">Sequence</span></span> | <span data-ttu-id="2da33-592">Filterbereich</span><span class="sxs-lookup"><span data-stu-id="2da33-592">Filter scope</span></span> | <span data-ttu-id="2da33-593">`Order` -Eigenschaft</span><span class="sxs-lookup"><span data-stu-id="2da33-593">`Order` property</span></span> | <span data-ttu-id="2da33-594">Filtermethode</span><span class="sxs-lookup"><span data-stu-id="2da33-594">Filter method</span></span> |
|:--------:|:------------:|:-----------------:|:-------------:|
| <span data-ttu-id="2da33-595">1</span><span class="sxs-lookup"><span data-stu-id="2da33-595">1</span></span> | <span data-ttu-id="2da33-596">Methode</span><span class="sxs-lookup"><span data-stu-id="2da33-596">Method</span></span> | <span data-ttu-id="2da33-597">0</span><span class="sxs-lookup"><span data-stu-id="2da33-597">0</span></span> | `OnActionExecuting` |
| <span data-ttu-id="2da33-598">2</span><span class="sxs-lookup"><span data-stu-id="2da33-598">2</span></span> | <span data-ttu-id="2da33-599">Controller</span><span class="sxs-lookup"><span data-stu-id="2da33-599">Controller</span></span> | <span data-ttu-id="2da33-600">1</span><span class="sxs-lookup"><span data-stu-id="2da33-600">1</span></span>  | `OnActionExecuting` |
| <span data-ttu-id="2da33-601">3</span><span class="sxs-lookup"><span data-stu-id="2da33-601">3</span></span> | <span data-ttu-id="2da33-602">Global</span><span class="sxs-lookup"><span data-stu-id="2da33-602">Global</span></span> | <span data-ttu-id="2da33-603">2</span><span class="sxs-lookup"><span data-stu-id="2da33-603">2</span></span>  | `OnActionExecuting` |
| <span data-ttu-id="2da33-604">4</span><span class="sxs-lookup"><span data-stu-id="2da33-604">4</span></span> | <span data-ttu-id="2da33-605">Global</span><span class="sxs-lookup"><span data-stu-id="2da33-605">Global</span></span> | <span data-ttu-id="2da33-606">2</span><span class="sxs-lookup"><span data-stu-id="2da33-606">2</span></span>  | `OnActionExecuted` |
| <span data-ttu-id="2da33-607">5</span><span class="sxs-lookup"><span data-stu-id="2da33-607">5</span></span> | <span data-ttu-id="2da33-608">Controller</span><span class="sxs-lookup"><span data-stu-id="2da33-608">Controller</span></span> | <span data-ttu-id="2da33-609">1</span><span class="sxs-lookup"><span data-stu-id="2da33-609">1</span></span>  | `OnActionExecuted` |
| <span data-ttu-id="2da33-610">6</span><span class="sxs-lookup"><span data-stu-id="2da33-610">6</span></span> | <span data-ttu-id="2da33-611">Methode</span><span class="sxs-lookup"><span data-stu-id="2da33-611">Method</span></span> | <span data-ttu-id="2da33-612">0</span><span class="sxs-lookup"><span data-stu-id="2da33-612">0</span></span>  | `OnActionExecuted` |

<span data-ttu-id="2da33-613">Die Eigenschaft `Order` hat bei der Bestimmung der Reihenfolge, in der Filter ausgeführt werden, Vorrang vor dem Bereich.</span><span class="sxs-lookup"><span data-stu-id="2da33-613">The `Order` property overrides scope when determining the order in which filters run.</span></span> <span data-ttu-id="2da33-614">Filter werden erst nach der Reihenfolge sortiert, und dann werden Gleichstände über den Bereich aufgelöst.</span><span class="sxs-lookup"><span data-stu-id="2da33-614">Filters are sorted first by order, then scope is used to break ties.</span></span> <span data-ttu-id="2da33-615">Alle integrierten Filter implementieren `IOrderedFilter` und legen den Standartwert von `Order` auf 0 fest.</span><span class="sxs-lookup"><span data-stu-id="2da33-615">All of the built-in filters implement `IOrderedFilter` and set the default `Order` value to 0.</span></span> <span data-ttu-id="2da33-616">Bei integrierten Filtern bestimmt der Bereich die Reihenfolge, falls `Order` nicht auf einen Wert ungleich 0 festgelegt ist.</span><span class="sxs-lookup"><span data-stu-id="2da33-616">For built-in filters, scope determines order unless `Order` is set to a non-zero value.</span></span>

## <a name="cancellation-and-short-circuiting"></a><span data-ttu-id="2da33-617">Abbrechen und Kurzschließen</span><span class="sxs-lookup"><span data-stu-id="2da33-617">Cancellation and short-circuiting</span></span>

<span data-ttu-id="2da33-618">Die Filterpipeline kann kurzgeschlossen werden, indem die Eigenschaft <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> auf den Parameter <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> festgelegt wird, der in der Filtermethode angegeben ist.</span><span class="sxs-lookup"><span data-stu-id="2da33-618">The filter pipeline can be short-circuited by setting the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> property on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parameter provided to the filter method.</span></span> <span data-ttu-id="2da33-619">Zum Beispiel verhindert der folgende Ressourcenfilter, dass der Rest der Pipeline ausgeführt wird:</span><span class="sxs-lookup"><span data-stu-id="2da33-619">For instance, the following Resource filter prevents the rest of the pipeline from executing:</span></span>

<a name="short-circuiting-resource-filter"></a>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?name=snippet)]

<span data-ttu-id="2da33-620">Im folgenden Code verwenden sowohl der Filter `ShortCircuitingResourceFilter` und der Filter `AddHeader` die Aktionsmethode `SomeResource` als Ziel.</span><span class="sxs-lookup"><span data-stu-id="2da33-620">In the following code, both the `ShortCircuitingResourceFilter` and the `AddHeader` filter target the `SomeResource` action method.</span></span> <span data-ttu-id="2da33-621">Die `ShortCircuitingResourceFilter`:</span><span class="sxs-lookup"><span data-stu-id="2da33-621">The `ShortCircuitingResourceFilter`:</span></span>

* <span data-ttu-id="2da33-622">Der Filter wird zuerst ausgeführt, da es sich um einen Ressourcenfilter handelt und `AddHeader` ein Aktionsfilter ist.</span><span class="sxs-lookup"><span data-stu-id="2da33-622">Runs first, because it's a Resource Filter and `AddHeader` is an Action Filter.</span></span>
* <span data-ttu-id="2da33-623">Der Filter unterbricht alle verbleibenden Pipelineschritte.</span><span class="sxs-lookup"><span data-stu-id="2da33-623">Short-circuits the rest of the pipeline.</span></span>

<span data-ttu-id="2da33-624">Der `AddHeader`-Filter wird daher nie für die `SomeResource`-Aktion ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="2da33-624">Therefore the `AddHeader` filter never runs for the `SomeResource` action.</span></span> <span data-ttu-id="2da33-625">Dasselbe Verhalten würde auch dann auftreten, wenn beide Filter auf Aktionsmethodenebene angewendet würden, wenn `ShortCircuitingResourceFilter` zuerst ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="2da33-625">This behavior would be the same if both filters were applied at the action method level, provided the `ShortCircuitingResourceFilter` ran first.</span></span> <span data-ttu-id="2da33-626">Der `ShortCircuitingResourceFilter` wird aufgrund seines Filtertyps oder durch die explizite Verwendung der `Order`-Eigenschaft zuerst ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="2da33-626">The `ShortCircuitingResourceFilter` runs first because of its filter type, or by explicit use of `Order` property.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1,9)]

## <a name="dependency-injection"></a><span data-ttu-id="2da33-627">Dependency Injection</span><span class="sxs-lookup"><span data-stu-id="2da33-627">Dependency injection</span></span>

<span data-ttu-id="2da33-628">Filter können nach Typ oder Instanz hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="2da33-628">Filters can be added by type or by instance.</span></span> <span data-ttu-id="2da33-629">Wenn eine Instanz hinzugefügt wird, wird diese Instanz für jede Anforderung verwendet.</span><span class="sxs-lookup"><span data-stu-id="2da33-629">If an instance is added, that instance is used for every request.</span></span> <span data-ttu-id="2da33-630">Wenn ein Typ hinzugefügt wird, ist der Filter typaktiviert.</span><span class="sxs-lookup"><span data-stu-id="2da33-630">If a type is added, it's type-activated.</span></span> <span data-ttu-id="2da33-631">Ein typaktivierter Filter bedeutet Folgendes:</span><span class="sxs-lookup"><span data-stu-id="2da33-631">A type-activated filter means:</span></span>

* <span data-ttu-id="2da33-632">Für jede Anforderung wird eine Instanz erstellt.</span><span class="sxs-lookup"><span data-stu-id="2da33-632">An instance is created for each request.</span></span>
* <span data-ttu-id="2da33-633">Alle Konstruktorabhängigkeiten werden durch [Dependency Injection](xref:fundamentals/dependency-injection) (DI) aufgefüllt.</span><span class="sxs-lookup"><span data-stu-id="2da33-633">Any constructor dependencies are populated by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span>

<span data-ttu-id="2da33-634">Filter, die als Attribute implementiert und Controllerklassen oder Aktionsmethoden direkt hinzugefügt werden, können keine Konstruktorabhängigkeiten von [Dependency Injection](xref:fundamentals/dependency-injection) (DI) erhalten.</span><span class="sxs-lookup"><span data-stu-id="2da33-634">Filters that are implemented as attributes and added directly to controller classes or action methods cannot have constructor dependencies provided by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="2da33-635">Konstruktorabhängigkeiten können aus folgenden Gründen von DI nicht bereitgestellt werden:</span><span class="sxs-lookup"><span data-stu-id="2da33-635">Constructor dependencies cannot be provided by DI because:</span></span>

* <span data-ttu-id="2da33-636">Für Attribute müssen Konstruktorparameter bereitgestellt werden, wenn sie angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="2da33-636">Attributes must have their constructor parameters supplied where they're applied.</span></span> 
* <span data-ttu-id="2da33-637">Dies ist eine Einschränkung der Funktionsweise von Attributen.</span><span class="sxs-lookup"><span data-stu-id="2da33-637">This is a limitation of how attributes work.</span></span>

<span data-ttu-id="2da33-638">Die folgenden Filter unterstützen von DI bereitgestellte Konstruktorabhängigkeiten:</span><span class="sxs-lookup"><span data-stu-id="2da33-638">The following filters support constructor dependencies provided from DI:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>
* <span data-ttu-id="2da33-639"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>, im Attribut implementiert</span><span class="sxs-lookup"><span data-stu-id="2da33-639"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implemented on the attribute.</span></span>

<span data-ttu-id="2da33-640">Die oben genannten Filter können auf einen Controller oder eine Aktionsmethode angewendet werden:</span><span class="sxs-lookup"><span data-stu-id="2da33-640">The preceding filters can be applied to a controller or action method:</span></span>

<span data-ttu-id="2da33-641">DI bietet Protokollierungen.</span><span class="sxs-lookup"><span data-stu-id="2da33-641">Loggers are available from DI.</span></span> <span data-ttu-id="2da33-642">Vermeiden Sie es jedoch, Filter nur zum Zweck der Protokollierung zu erstellen und zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="2da33-642">However, avoid creating and using filters purely for logging purposes.</span></span> <span data-ttu-id="2da33-643">Die integrierte [Frameworkprotokollierung](xref:fundamentals/logging/index) bietet in der Regel alle Elemente, die für die Protokollierung erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="2da33-643">The [built-in framework logging](xref:fundamentals/logging/index) typically provides what's needed for logging.</span></span> <span data-ttu-id="2da33-644">Wenn Protokollierung zu Filtern hinzugefügt wird, gilt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="2da33-644">Logging added to filters:</span></span>

* <span data-ttu-id="2da33-645">Die Protokollierung sollte mit Schwerpunkt auf geschäftlichen Aspekten oder verhaltensspezifisch für den Filter erfolgen.</span><span class="sxs-lookup"><span data-stu-id="2da33-645">Should focus on business domain concerns or behavior specific to the filter.</span></span>
* <span data-ttu-id="2da33-646">Aktionen oder andere Frameworkereignisse sollten **nicht** protokolliert werden.</span><span class="sxs-lookup"><span data-stu-id="2da33-646">Should **not** log actions or other framework events.</span></span> <span data-ttu-id="2da33-647">Die integrierten Filter protokollieren Aktionen und Frameworkereignisse.</span><span class="sxs-lookup"><span data-stu-id="2da33-647">The built in filters log actions and framework events.</span></span>

### <a name="servicefilterattribute"></a><span data-ttu-id="2da33-648">ServiceFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="2da33-648">ServiceFilterAttribute</span></span>

<span data-ttu-id="2da33-649">Die Typen der Dienstfilterimplementierung werden in `ConfigureServices` registriert.</span><span class="sxs-lookup"><span data-stu-id="2da33-649">Service filter implementation types are registered in `ConfigureServices`.</span></span> <span data-ttu-id="2da33-650">Ein <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> ruft eine Instanz des Filter von DI ab.</span><span class="sxs-lookup"><span data-stu-id="2da33-650">A <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> retrieves an instance of the filter from DI.</span></span>

<span data-ttu-id="2da33-651">Der folgende Code zeigt den `AddHeaderResultServiceFilter`:</span><span class="sxs-lookup"><span data-stu-id="2da33-651">The following code shows the `AddHeaderResultServiceFilter`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="2da33-652">Im folgenden Code wird `AddHeaderResultServiceFilter` zum DI-Container hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="2da33-652">In the following code, `AddHeaderResultServiceFilter` is added to the DI container:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

<span data-ttu-id="2da33-653">Im folgenden Code ruft das `ServiceFilter`-Attribut eine Instanz des `AddHeaderResultServiceFilter`-Filters aus DI ab:</span><span class="sxs-lookup"><span data-stu-id="2da33-653">In the following code, the `ServiceFilter` attribute retrieves an instance of the `AddHeaderResultServiceFilter` filter from DI:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

<span data-ttu-id="2da33-654">Beim Verwenden von `ServiceFilterAttribute` wird [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable) festgelegt:</span><span class="sxs-lookup"><span data-stu-id="2da33-654">When using `ServiceFilterAttribute`, setting [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span></span>

* <span data-ttu-id="2da33-655">Gibt einen Hinweis darauf, dass die Filterinstanz *möglicherweise* außerhalb des Anforderungsbereichs, in dem sie erstellt wurde, wiederverwendet wird.</span><span class="sxs-lookup"><span data-stu-id="2da33-655">Provides a hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="2da33-656">Die ASP.NET Core-Runtime bietet keine Garantie für Folgendes:</span><span class="sxs-lookup"><span data-stu-id="2da33-656">The ASP.NET Core runtime doesn't guarantee:</span></span>

  * <span data-ttu-id="2da33-657">Eine einzelne Instanz des Filters wird erstellt.</span><span class="sxs-lookup"><span data-stu-id="2da33-657">That a single instance of the filter will be created.</span></span>
  * <span data-ttu-id="2da33-658">Der Filter wird nicht zu einem späteren Zeitpunkt vom DI-Container erneut angefordert.</span><span class="sxs-lookup"><span data-stu-id="2da33-658">The filter will not be re-requested from the DI container at some later point.</span></span>

* <span data-ttu-id="2da33-659">Sollte nicht mit einem Filter verwendet werden, der von Diensten mit einer anderen Lebensdauer als der eines Singletons abhängig ist.</span><span class="sxs-lookup"><span data-stu-id="2da33-659">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

 <span data-ttu-id="2da33-660"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implementiert <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span><span class="sxs-lookup"><span data-stu-id="2da33-660"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="2da33-661">`IFilterFactory` macht die <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*>-Methode zum Erstellen einer <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>-Instanz verfügbar.</span><span class="sxs-lookup"><span data-stu-id="2da33-661">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="2da33-662">`CreateInstance` lädt den angegebenen Typ aus DI.</span><span class="sxs-lookup"><span data-stu-id="2da33-662">`CreateInstance` loads the specified type from DI.</span></span>

### <a name="typefilterattribute"></a><span data-ttu-id="2da33-663">TypeFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="2da33-663">TypeFilterAttribute</span></span>

<span data-ttu-id="2da33-664"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> ähnelt <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>. Der zugehörige Typ wird allerdings nicht direkt vom DI-Container aufgelöst.</span><span class="sxs-lookup"><span data-stu-id="2da33-664"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> is similar to <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, but its type isn't resolved directly from the DI container.</span></span> <span data-ttu-id="2da33-665">Stattdessen wird der Typ mithilfe von <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName> instanziiert.</span><span class="sxs-lookup"><span data-stu-id="2da33-665">It instantiates the type by using <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span></span>

<span data-ttu-id="2da33-666">Da `TypeFilterAttribute`-Typen nicht direkt vom DI-Container aufgelöst werden, gilt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="2da33-666">Because `TypeFilterAttribute` types aren't resolved directly from the DI container:</span></span>

* <span data-ttu-id="2da33-667">Typen, auf die mithilfe von `TypeFilterAttribute` verwiesen wird, müssen nicht beim DI-Container registriert werden.</span><span class="sxs-lookup"><span data-stu-id="2da33-667">Types that are referenced using the `TypeFilterAttribute` don't need to be registered with the DI container.</span></span>  <span data-ttu-id="2da33-668">Ihre Abhängigkeiten werden vom DI-Container erfüllt.</span><span class="sxs-lookup"><span data-stu-id="2da33-668">They do have their dependencies fulfilled by the DI container.</span></span>
* <span data-ttu-id="2da33-669">`TypeFilterAttribute` kann optional Konstruktorargumente für den Typ akzeptieren.</span><span class="sxs-lookup"><span data-stu-id="2da33-669">`TypeFilterAttribute` can optionally accept constructor arguments for the type.</span></span>

<span data-ttu-id="2da33-670">Beim Verwenden von `TypeFilterAttribute` wird [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable) festgelegt:</span><span class="sxs-lookup"><span data-stu-id="2da33-670">When using `TypeFilterAttribute`, setting [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span></span>
* <span data-ttu-id="2da33-671">Gibt einen Hinweis darauf, dass die Filterinstanz *möglicherweise* außerhalb des Anforderungsbereichs wiederverwendet wird, in dem sie erstellt wurde.</span><span class="sxs-lookup"><span data-stu-id="2da33-671">Provides hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="2da33-672">Die ASP.NET Core-Runtime bietet keine Garantie dafür, dass eine einzelne Instanz des Filters erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="2da33-672">The ASP.NET Core runtime provides no guarantees that a single instance of the filter will be created.</span></span>

* <span data-ttu-id="2da33-673">Sollte nicht mit einem Filter verwendet werden, der von Diensten mit einer anderen Lebensdauer als der eines Singletons abhängig ist.</span><span class="sxs-lookup"><span data-stu-id="2da33-673">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

<span data-ttu-id="2da33-674">Das folgende Beispiel zeigt, wie Sie Argumente durch Verwendung von `TypeFilterAttribute` an einen Typ übergeben:</span><span class="sxs-lookup"><span data-stu-id="2da33-674">The following example shows how to pass arguments to a type using `TypeFilterAttribute`:</span></span>

[!code-csharp[](../../mvc/controllers/filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]
[!code-csharp[](../../mvc/controllers/filters/sample/FiltersSample/Filters/LogConstantFilter.cs?name=snippet_TypeFilter_Implementation&highlight=6)]

<!-- 
https://localhost:5001/home/hi?name=joe
VS debug window shows 
FiltersSample.Filters.LogConstantFilter:Information: Method 'Hi' called
-->

## <a name="authorization-filters"></a><span data-ttu-id="2da33-675">Autorisierungsfilter</span><span class="sxs-lookup"><span data-stu-id="2da33-675">Authorization filters</span></span>

<span data-ttu-id="2da33-676">Für Autorisierungsfilter gilt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="2da33-676">Authorization filters:</span></span>

* <span data-ttu-id="2da33-677">Sie werden als erste Filter in der Filterpipeline ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="2da33-677">Are the first filters run in the filter pipeline.</span></span>
* <span data-ttu-id="2da33-678">Sie steuern den Zugriff auf Aktionsmethoden.</span><span class="sxs-lookup"><span data-stu-id="2da33-678">Control access to action methods.</span></span>
* <span data-ttu-id="2da33-679">Sie verfügen nur über eine Methode, die vor der Aktion ausgeführt wird, nicht jedoch über eine Methode, die nach der Aktion ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="2da33-679">Have a before method, but no after method.</span></span>

<span data-ttu-id="2da33-680">Benutzerdefinierte Autorisierungsfilter erfordern ein benutzerdefiniertes Autorisierungsframework.</span><span class="sxs-lookup"><span data-stu-id="2da33-680">Custom authorization filters require a custom authorization framework.</span></span> <span data-ttu-id="2da33-681">Statt einen benutzerdefinierten Filter zu schreiben, sollten Sie die Autorisierungsrichtlinien konfigurieren oder eine benutzerdefinierte Autorisierungsrichtlinie schreiben.</span><span class="sxs-lookup"><span data-stu-id="2da33-681">Prefer configuring the authorization policies or writing a custom authorization policy over writing a custom filter.</span></span> <span data-ttu-id="2da33-682">Für den integrierten Autorisierungsfilter gilt:</span><span class="sxs-lookup"><span data-stu-id="2da33-682">The built-in authorization filter:</span></span>

* <span data-ttu-id="2da33-683">Er ruft das Autorisierungssystem auf.</span><span class="sxs-lookup"><span data-stu-id="2da33-683">Calls the authorization system.</span></span>
* <span data-ttu-id="2da33-684">Er autorisiert keine Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="2da33-684">Does not authorize requests.</span></span>

<span data-ttu-id="2da33-685">Lösen Sie **keine** Ausnahmen in Autorisierungsfiltern aus:</span><span class="sxs-lookup"><span data-stu-id="2da33-685">Do **not** throw exceptions within authorization filters:</span></span>

* <span data-ttu-id="2da33-686">Die Ausnahme wird nicht behandelt.</span><span class="sxs-lookup"><span data-stu-id="2da33-686">The exception will not be handled.</span></span>
* <span data-ttu-id="2da33-687">Ausnahmefilter behandeln die Ausnahme nicht.</span><span class="sxs-lookup"><span data-stu-id="2da33-687">Exception filters will not handle the exception.</span></span>

<span data-ttu-id="2da33-688">Beim Auftreten einer Ausnahme in einem Autorisierungsfilter sollten Sie eine Abfrage erwägen.</span><span class="sxs-lookup"><span data-stu-id="2da33-688">Consider issuing a challenge when an exception occurs in an authorization filter.</span></span>

<span data-ttu-id="2da33-689">Weitere Informationen finden Sie unter [Autorisierung](xref:security/authorization/introduction).</span><span class="sxs-lookup"><span data-stu-id="2da33-689">Learn more about [Authorization](xref:security/authorization/introduction).</span></span>

## <a name="resource-filters"></a><span data-ttu-id="2da33-690">Ressourcenfilter</span><span class="sxs-lookup"><span data-stu-id="2da33-690">Resource filters</span></span>

<span data-ttu-id="2da33-691">Für Ressourcenfilter gilt:</span><span class="sxs-lookup"><span data-stu-id="2da33-691">Resource filters:</span></span>

* <span data-ttu-id="2da33-692">Sie implementieren entweder die Schnittstelle <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> oder <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter>.</span><span class="sxs-lookup"><span data-stu-id="2da33-692">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> interface.</span></span>
* <span data-ttu-id="2da33-693">Die Ausführung umschließt den größten Teil der Filterpipeline.</span><span class="sxs-lookup"><span data-stu-id="2da33-693">Execution wraps most of the filter pipeline.</span></span>
* <span data-ttu-id="2da33-694">Nur [Autorisierungsfilter](#authorization-filters) werden vor Ressourcenfiltern ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="2da33-694">Only [Authorization filters](#authorization-filters) run before resource filters.</span></span>

<span data-ttu-id="2da33-695">Ressourcenfilter sind hilfreich, um den größten Teil der Pipeline kurzzuschließen.</span><span class="sxs-lookup"><span data-stu-id="2da33-695">Resource filters are useful to short-circuit most of the pipeline.</span></span> <span data-ttu-id="2da33-696">Ein Cachingfilter kann beispielsweise bei einem Cachetreffer den Rest der Pipeline überspringen.</span><span class="sxs-lookup"><span data-stu-id="2da33-696">For example, a caching filter can avoid the rest of the pipeline on a cache hit.</span></span>

<span data-ttu-id="2da33-697">Beispiele für Ressourcenfilter:</span><span class="sxs-lookup"><span data-stu-id="2da33-697">Resource filter examples:</span></span>

* <span data-ttu-id="2da33-698">Der oben gezeigte [Ressourcenfilter zum Kurzschließen](#short-circuiting-resource-filter).</span><span class="sxs-lookup"><span data-stu-id="2da33-698">[The short-circuiting resource filter](#short-circuiting-resource-filter) shown previously.</span></span>
* <span data-ttu-id="2da33-699">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span><span class="sxs-lookup"><span data-stu-id="2da33-699">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span></span>

  * <span data-ttu-id="2da33-700">Verhindert, dass die Modellbindung auf die Formulardaten zugreift.</span><span class="sxs-lookup"><span data-stu-id="2da33-700">Prevents model binding from accessing the form data.</span></span>
  * <span data-ttu-id="2da33-701">Wird bei großen Dateiuploads verwendet, um zu verhindern, dass die Formulardaten in den Arbeitsspeicher eingelesen werden.</span><span class="sxs-lookup"><span data-stu-id="2da33-701">Used for large file uploads to prevent the form data from being read into memory.</span></span>

## <a name="action-filters"></a><span data-ttu-id="2da33-702">Aktionsfilter</span><span class="sxs-lookup"><span data-stu-id="2da33-702">Action filters</span></span>

> [!IMPORTANT]
> <span data-ttu-id="2da33-703">Aktionsfilter gelten **nicht** für Razor Seiten.</span><span class="sxs-lookup"><span data-stu-id="2da33-703">Action filters do **not** apply to Razor Pages.</span></span> <span data-ttu-id="2da33-704">Razor -Seiten unterstützen <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> und <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span><span class="sxs-lookup"><span data-stu-id="2da33-704">Razor Pages supports <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span></span> <span data-ttu-id="2da33-705">Weitere Informationen finden Sie unter [Filtermethoden für Razor Pages](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="2da33-705">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

<span data-ttu-id="2da33-706">Für Aktionsfilter gilt:</span><span class="sxs-lookup"><span data-stu-id="2da33-706">Action filters:</span></span>

* <span data-ttu-id="2da33-707">Sie implementieren entweder die Schnittstelle <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> oder <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter>.</span><span class="sxs-lookup"><span data-stu-id="2da33-707">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> interface.</span></span>
* <span data-ttu-id="2da33-708">Ihre Ausführung umfasst die Ausführung von Aktionsmethoden.</span><span class="sxs-lookup"><span data-stu-id="2da33-708">Their execution surrounds the execution of action methods.</span></span>

<span data-ttu-id="2da33-709">Der folgende Code zeigt einen Beispielaktionsfilter:</span><span class="sxs-lookup"><span data-stu-id="2da33-709">The following code shows a sample action filter:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="2da33-710"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> bietet folgende Eigenschaften:</span><span class="sxs-lookup"><span data-stu-id="2da33-710">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> provides the following properties:</span></span>

* <span data-ttu-id="2da33-711"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> ermöglicht das Lesen der Eingaben für eine Aktionsmethode.</span><span class="sxs-lookup"><span data-stu-id="2da33-711"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - enables the inputs to an action method be read.</span></span>
* <span data-ttu-id="2da33-712"><xref:Microsoft.AspNetCore.Mvc.Controller> ermöglicht das Ändern der Controllerinstanz.</span><span class="sxs-lookup"><span data-stu-id="2da33-712"><xref:Microsoft.AspNetCore.Mvc.Controller> - enables manipulating the controller instance.</span></span>
* <span data-ttu-id="2da33-713"><xref:System.Web.Mvc.ActionExecutingContext.Result>: Die Festlegung von `Result` schließt die Ausführung der Aktionsmethode und der nachfolgenden Aktionsfilter kurz.</span><span class="sxs-lookup"><span data-stu-id="2da33-713"><xref:System.Web.Mvc.ActionExecutingContext.Result> - setting `Result` short-circuits execution of the action method and subsequent action filters.</span></span>

<span data-ttu-id="2da33-714">Durch Auslösen einer Ausnahme in einer Aktionsmethode geschieht Folgendes:</span><span class="sxs-lookup"><span data-stu-id="2da33-714">Throwing an exception in an action method:</span></span>

* <span data-ttu-id="2da33-715">Die Ausführung nachfolgender Filter wird verhindert.</span><span class="sxs-lookup"><span data-stu-id="2da33-715">Prevents running of subsequent filters.</span></span>
* <span data-ttu-id="2da33-716">Im Gegensatz zum Festlegen von `Result` wird die ausgelöste Ausnahme wird nicht als erfolgreiches Ergebnis, sondern als Fehler behandelt.</span><span class="sxs-lookup"><span data-stu-id="2da33-716">Unlike setting `Result`, is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="2da33-717"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> bietet `Controller` und `Result` sowie die folgenden Eigenschaften:</span><span class="sxs-lookup"><span data-stu-id="2da33-717">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> provides `Controller` and `Result` plus the following properties:</span></span>

* <span data-ttu-id="2da33-718"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> ist TRUE, wenn die Aktionsausführung durch einen anderen Filter kurzgeschlossen wurde.</span><span class="sxs-lookup"><span data-stu-id="2da33-718"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> - True if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="2da33-719"><xref:System.Web.Mvc.ActionExecutedContext.Exception> ist ungleich NULL, wenn die Aktion oder ein zuvor ausgeführter Aktionsfilter eine Ausnahme ausgelöst hat.</span><span class="sxs-lookup"><span data-stu-id="2da33-719"><xref:System.Web.Mvc.ActionExecutedContext.Exception> - Non-null if the action or a previously run action filter threw an exception.</span></span> <span data-ttu-id="2da33-720">Durch Festlegen dieser Eigenschaft auf NULL geschieht Folgendes:</span><span class="sxs-lookup"><span data-stu-id="2da33-720">Setting this property to null:</span></span>

  * <span data-ttu-id="2da33-721">Die Ausnahme wird effektiv behandelt.</span><span class="sxs-lookup"><span data-stu-id="2da33-721">Effectively handles the exception.</span></span>
  * <span data-ttu-id="2da33-722">`Result` wird so ausgeführt, als wäre das Ergebnis von der Aktionsmethode zurückgegeben worden.</span><span class="sxs-lookup"><span data-stu-id="2da33-722">`Result` is executed as if it was returned from the action method.</span></span>

<span data-ttu-id="2da33-723">Bei einem `IAsyncActionFilter` hat der Aufruf von <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> folgende Auswirkungen:</span><span class="sxs-lookup"><span data-stu-id="2da33-723">For an `IAsyncActionFilter`, a call to the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span></span>

* <span data-ttu-id="2da33-724">Alle nachfolgenden Aktionsfilter und die Aktionsmethode werden ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="2da33-724">Executes any subsequent action filters and the action method.</span></span>
* <span data-ttu-id="2da33-725">Gibt `ActionExecutedContext`zurück.</span><span class="sxs-lookup"><span data-stu-id="2da33-725">Returns `ActionExecutedContext`.</span></span>

<span data-ttu-id="2da33-726">Weisen Sie einer Ergebnisinstanz <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> zu, und rufen Sie nicht `next` (den `ActionExecutionDelegate`) auf, um die Pipeline kurzzuschließen.</span><span class="sxs-lookup"><span data-stu-id="2da33-726">To short-circuit, assign <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> to a result instance and don't call `next` (the `ActionExecutionDelegate`).</span></span>

<span data-ttu-id="2da33-727">Das Framework stellt ein abstraktes <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> bereit, das als Unterklasse verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="2da33-727">The framework provides an abstract <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> that can be subclassed.</span></span>

<span data-ttu-id="2da33-728">Der Aktionsfilter `OnActionExecuting` kann für Folgendes verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="2da33-728">The `OnActionExecuting` action filter can be used to:</span></span>

* <span data-ttu-id="2da33-729">Überprüfen des Modellzustands.</span><span class="sxs-lookup"><span data-stu-id="2da33-729">Validate model state.</span></span>
* <span data-ttu-id="2da33-730">Zurückgeben eines Fehlers, wenn der Zustand ungültig ist.</span><span class="sxs-lookup"><span data-stu-id="2da33-730">Return an error if the state is invalid.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet)]

<span data-ttu-id="2da33-731">Die Methode `OnActionExecuted` wird nach der Aktionsmethode ausgeführt. Es gilt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="2da33-731">The `OnActionExecuted` method runs after the action method:</span></span>

* <span data-ttu-id="2da33-732">Die Methode kann die Ergebnisse der Aktion durch die Eigenschaft <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> sehen und ändern.</span><span class="sxs-lookup"><span data-stu-id="2da33-732">And can see and manipulate the results of the action through the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> property.</span></span>
* <span data-ttu-id="2da33-733"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> wird auf TRUE festgelegt, wenn die Ausführung der Aktion durch einen anderen Filter kurzgeschlossen wurde.</span><span class="sxs-lookup"><span data-stu-id="2da33-733"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> is set to true if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="2da33-734"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> wird auf einen Wert ungleich NULL festgelegt, wenn die Aktion oder ein nachfolgender Aktionsfilter eine Ausnahme ausgelöst hat.</span><span class="sxs-lookup"><span data-stu-id="2da33-734"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> is set to a non-null value if the action or a subsequent action filter threw an exception.</span></span> <span data-ttu-id="2da33-735">Wenn für `Exception` NULL festgelegt wird,</span><span class="sxs-lookup"><span data-stu-id="2da33-735">Setting `Exception` to null:</span></span>

  * <span data-ttu-id="2da33-736">werden Ausnahmen effektiv behandelt,</span><span class="sxs-lookup"><span data-stu-id="2da33-736">Effectively handles an exception.</span></span>
  * <span data-ttu-id="2da33-737">und `ActionExecutedContext.Result` wird so ausgeführt, als würde die Eigenschaft normal von der Aktionsmethode zurückgegeben werden.</span><span class="sxs-lookup"><span data-stu-id="2da33-737">`ActionExecutedContext.Result` is executed as if it were returned normally from the action method.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet2&higlight=12-99)]

## <a name="exception-filters"></a><span data-ttu-id="2da33-738">Ausnahmefilter</span><span class="sxs-lookup"><span data-stu-id="2da33-738">Exception filters</span></span>

<span data-ttu-id="2da33-739">Für Ausnahmefilter gilt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="2da33-739">Exception filters:</span></span>

* <span data-ttu-id="2da33-740">Sie implementieren <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> oder <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span><span class="sxs-lookup"><span data-stu-id="2da33-740">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span></span> 
* <span data-ttu-id="2da33-741">Sie können verwendet werden, um gängige Fehlerbehandlungsrichtlinien zu implementieren.</span><span class="sxs-lookup"><span data-stu-id="2da33-741">Can be used to implement common error handling policies.</span></span>

<span data-ttu-id="2da33-742">Der folgende Beispielausnahmefilter verwendet eine benutzerdefinierte Fehleransicht, um Details zu Ausnahmen anzuzeigen, die auftreten, wenn die App sich in der Entwicklung befindet:</span><span class="sxs-lookup"><span data-stu-id="2da33-742">The following sample exception filter uses a custom error view to display details about exceptions that occur when the app is in development:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/CustomExceptionFilter.cs?name=snippet_ExceptionFilter&highlight=16-19)]

<span data-ttu-id="2da33-743">Für Ausnahmefilter gilt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="2da33-743">Exception filters:</span></span>

* <span data-ttu-id="2da33-744">Sie verfügen nicht über vorangehende oder darauffolgende Ereignisse.</span><span class="sxs-lookup"><span data-stu-id="2da33-744">Don't have before and after events.</span></span>
* <span data-ttu-id="2da33-745">Sie implementieren <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> oder <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span><span class="sxs-lookup"><span data-stu-id="2da33-745">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span></span>
* <span data-ttu-id="2da33-746">Behandeln von nicht behandelten Ausnahmen, die bei der Razor Erstellung von Seiten oder Controllern, [Modell Bindungen](xref:mvc/models/model-binding), Aktions filtern oder Aktionsmethoden auftreten.</span><span class="sxs-lookup"><span data-stu-id="2da33-746">Handle unhandled exceptions that occur in Razor Page or controller creation, [model binding](xref:mvc/models/model-binding), action filters, or action methods.</span></span>
* <span data-ttu-id="2da33-747">Sie erfassen **keine** Ausnahmen, die in Ressourcenfiltern, Ergebnisfiltern oder bei der Ausführung von MVC-Ergebnissen auftreten.</span><span class="sxs-lookup"><span data-stu-id="2da33-747">Do **not** catch exceptions that occur in resource filters, result filters, or MVC result execution.</span></span>

<span data-ttu-id="2da33-748">Um eine Ausnahme zu behandeln, muss die Eigenschaft <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> auf `true` festgelegt oder eine Antwort geschrieben werden.</span><span class="sxs-lookup"><span data-stu-id="2da33-748">To handle an exception, set the <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> property to `true` or write a response.</span></span> <span data-ttu-id="2da33-749">Dadurch wird die Weitergabe der Ausnahme beendet.</span><span class="sxs-lookup"><span data-stu-id="2da33-749">This stops propagation of the exception.</span></span> <span data-ttu-id="2da33-750">Ein Ausnahmefilter kann eine Ausnahme nicht in ein „erfolgreiches Ergebnis“ umwandeln.</span><span class="sxs-lookup"><span data-stu-id="2da33-750">An exception filter can't turn an exception into a "success".</span></span> <span data-ttu-id="2da33-751">Nur ein Aktionsfilter kann das.</span><span class="sxs-lookup"><span data-stu-id="2da33-751">Only an action filter can do that.</span></span>

<span data-ttu-id="2da33-752">Für Ausnahmefilter gilt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="2da33-752">Exception filters:</span></span>

* <span data-ttu-id="2da33-753">Sie eignen sich für das Abfangen von Ausnahmen, die in Aktionen auftreten.</span><span class="sxs-lookup"><span data-stu-id="2da33-753">Are good for trapping exceptions that occur within actions.</span></span>
* <span data-ttu-id="2da33-754">Sie sind im Vergleich zu Middleware für die Fehlerbehandlung weniger flexibel.</span><span class="sxs-lookup"><span data-stu-id="2da33-754">Are not as flexible as error handling middleware.</span></span>

<span data-ttu-id="2da33-755">Sie sollten bei der Ausnahmebehandlung vorzugsweise Middleware verwenden.</span><span class="sxs-lookup"><span data-stu-id="2da33-755">Prefer middleware for exception handling.</span></span> <span data-ttu-id="2da33-756">Verwenden Sie Ausnahmefilter nur, wenn sich die Fehlerbehandlung *unterscheidet*, je nachdem, welche Aktionsmethode aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="2da33-756">Use exception filters only where error handling *differs* based on which action method is called.</span></span> <span data-ttu-id="2da33-757">Zum Beispiel verfügt eine App möglicherweise über Aktionsmethoden für beide API-Endpunkte und für Ansichten bzw. HTML.</span><span class="sxs-lookup"><span data-stu-id="2da33-757">For example, an app might have action methods for both API endpoints and for views/HTML.</span></span> <span data-ttu-id="2da33-758">Die API-Endpunkte können Fehlerinformationen als JSON zurückgeben, während ansichtsbasierte Aktionen eine Fehlerseite als HTML zurückgeben können.</span><span class="sxs-lookup"><span data-stu-id="2da33-758">The API endpoints could return error information as JSON, while the view-based actions could return an error page as HTML.</span></span>

## <a name="result-filters"></a><span data-ttu-id="2da33-759">Ergebnisfilter</span><span class="sxs-lookup"><span data-stu-id="2da33-759">Result filters</span></span>

<span data-ttu-id="2da33-760">Für Ergebnisfilter gilt:</span><span class="sxs-lookup"><span data-stu-id="2da33-760">Result filters:</span></span>

* <span data-ttu-id="2da33-761">Implementieren einer Schnittstelle:</span><span class="sxs-lookup"><span data-stu-id="2da33-761">Implement an interface:</span></span>
  * <span data-ttu-id="2da33-762"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> oder <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="2da33-762"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
  * <span data-ttu-id="2da33-763"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> oder <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span><span class="sxs-lookup"><span data-stu-id="2da33-763"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span></span>
* <span data-ttu-id="2da33-764">Ihre Ausführung umfasst die Ausführung von Aktionsergebnissen.</span><span class="sxs-lookup"><span data-stu-id="2da33-764">Their execution surrounds the execution of action results.</span></span>

### <a name="iresultfilter-and-iasyncresultfilter"></a><span data-ttu-id="2da33-765">IResultFilter und IAsyncResultFilter</span><span class="sxs-lookup"><span data-stu-id="2da33-765">IResultFilter and IAsyncResultFilter</span></span>

<span data-ttu-id="2da33-766">Der folgende Code zeigt einen Ergebnisfilter, der einen HTTP-Header hinzufügt:</span><span class="sxs-lookup"><span data-stu-id="2da33-766">The following code shows a result filter that adds an HTTP header:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="2da33-767">Die Art des Ergebnisses, das ausgeführt wird, hängt von der jeweiligen Aktion ab.</span><span class="sxs-lookup"><span data-stu-id="2da33-767">The kind of result being executed depends on the action.</span></span> <span data-ttu-id="2da33-768">Eine Aktion, die eine Ansicht zurückgibt, würde die gesamte Razor-Verarbeitung als Teil der Ausführung von <xref:Microsoft.AspNetCore.Mvc.ViewResult> beinhalten.</span><span class="sxs-lookup"><span data-stu-id="2da33-768">An action returning a view would include all razor processing as part of the <xref:Microsoft.AspNetCore.Mvc.ViewResult> being executed.</span></span> <span data-ttu-id="2da33-769">Eine API-Methode führt möglicherweise eine Art Serialisierung als Teil der Ausführung des Ergebnisses durch.</span><span class="sxs-lookup"><span data-stu-id="2da33-769">An API method might perform some serialization as part of the execution of the result.</span></span> <span data-ttu-id="2da33-770">Erfahren Sie mehr über [Aktions Ergebnisse](xref:mvc/controllers/actions).</span><span class="sxs-lookup"><span data-stu-id="2da33-770">Learn more about [action results](xref:mvc/controllers/actions).</span></span>

<span data-ttu-id="2da33-771">Ergebnisfilter werden nur ausgeführt, wenn eine Aktion oder ein Aktionsfilter ein Aktionsergebnis liefert.</span><span class="sxs-lookup"><span data-stu-id="2da33-771">Result filters are only executed when an action or action filter produces an action result.</span></span> <span data-ttu-id="2da33-772">Ergebnisfilter werden in folgenden Fällen nicht ausgeführt:</span><span class="sxs-lookup"><span data-stu-id="2da33-772">Result filters are not executed when:</span></span>

* <span data-ttu-id="2da33-773">Ein Autorisierungs- oder Ressourcenfilter schließt die Pipeline kurz.</span><span class="sxs-lookup"><span data-stu-id="2da33-773">An authorization filter or resource filter short-circuits the pipeline.</span></span>
* <span data-ttu-id="2da33-774">Ein Ausnahmefilter behandelt eine Ausnahme durch Erzeugen eines Aktionsergebnisses.</span><span class="sxs-lookup"><span data-stu-id="2da33-774">An exception filter handles an exception by producing an action result.</span></span>

<span data-ttu-id="2da33-775">Die Methode <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> kann die Ausführung des Aktionsergebnisses und der nachfolgenden Ergebnisfilter durch Festlegen von <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> auf `true` kurzschließen.</span><span class="sxs-lookup"><span data-stu-id="2da33-775">The <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> method can short-circuit execution of the action result and subsequent result filters by setting <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> to `true`.</span></span> <span data-ttu-id="2da33-776">Schreiben Sie beim Kurzschließen in das Antwortobjekt, um zu verhindern, dass eine leere Antwort generiert wird.</span><span class="sxs-lookup"><span data-stu-id="2da33-776">Write to the response object when short-circuiting to avoid generating an empty response.</span></span> <span data-ttu-id="2da33-777">Beim Auslösen einer Ausnahme in `IResultFilter.OnResultExecuting` geschieht Folgendes:</span><span class="sxs-lookup"><span data-stu-id="2da33-777">Throwing an exception in `IResultFilter.OnResultExecuting` will:</span></span>

* <span data-ttu-id="2da33-778">Die Ausführung des Aktionsergebnisses und der nachfolgenden Filter wird verhindert.</span><span class="sxs-lookup"><span data-stu-id="2da33-778">Prevent execution of the action result and subsequent filters.</span></span>
* <span data-ttu-id="2da33-779">Die ausgelöste Ausnahme wird nicht als erfolgreiches Ergebnis, sondern als Fehler behandelt.</span><span class="sxs-lookup"><span data-stu-id="2da33-779">Be treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="2da33-780">Wenn die <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName>-Methode ausgeführt wird, wurde die Antwort wahrscheinlich bereits an den Client gesendet.</span><span class="sxs-lookup"><span data-stu-id="2da33-780">When the <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> method runs, the response has likely already been sent to the client.</span></span> <span data-ttu-id="2da33-781">Wenn die Antwort bereits an den Client gesendet wurde, kann sie nicht mehr geändert werden.</span><span class="sxs-lookup"><span data-stu-id="2da33-781">If the response has already been sent to the client, it cannot be changed further.</span></span>

<span data-ttu-id="2da33-782">`ResultExecutedContext.Canceled` wird auf `true` festgelegt, wenn die Ausführung des Aktionsergebnisses durch einen anderen Filter kurzgeschlossen wurde.</span><span class="sxs-lookup"><span data-stu-id="2da33-782">`ResultExecutedContext.Canceled` is set to `true` if the action result execution was short-circuited by another filter.</span></span>

<span data-ttu-id="2da33-783">`ResultExecutedContext.Exception` wird auf einen Wert ungleich NULL festgelegt, wenn das Aktionsergebnis oder ein nachfolgender Ergebnisfilter eine Ausnahme ausgelöst hat.</span><span class="sxs-lookup"><span data-stu-id="2da33-783">`ResultExecutedContext.Exception` is set to a non-null value if the action result or a subsequent result filter threw an exception.</span></span> <span data-ttu-id="2da33-784">Das Festlegen von `Exception` auf NULL behandelt eine Ausnahme effektiv und verhindert, dass die Ausnahme später in der Pipeline erneut von ASP.NET Core ausgelöst wird.</span><span class="sxs-lookup"><span data-stu-id="2da33-784">Setting `Exception` to null effectively handles an exception and prevents the exception from being rethrown by ASP.NET Core later in the pipeline.</span></span> <span data-ttu-id="2da33-785">Beim Behandeln einer Ausnahme in einem Ergebnisfilter gibt es keine zuverlässige Möglichkeit, Daten in eine Antwort zu schreiben.</span><span class="sxs-lookup"><span data-stu-id="2da33-785">There is no reliable way to write data to a response when handling an exception in a result filter.</span></span> <span data-ttu-id="2da33-786">Wenn ein Aktionsergebnis eine Ausnahme auslöst und die Header bereits an den Client übergeben wurden, gibt es keinen zuverlässigen Mechanismus, einen Fehlercode zu senden.</span><span class="sxs-lookup"><span data-stu-id="2da33-786">If the headers have been flushed to the client when an action result throws an exception, there's no reliable mechanism to send a failure code.</span></span>

<span data-ttu-id="2da33-787">Bei einem <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter> führt ein Aufruf von `await next` im <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> alle nachfolgenden Ergebnisfilter und das Aktionsergebnis aus.</span><span class="sxs-lookup"><span data-stu-id="2da33-787">For an <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, a call to `await next` on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> executes any subsequent result filters and the action result.</span></span> <span data-ttu-id="2da33-788">Zum Kurzschließen legen Sie [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) auf `true` fest und rufen den `ResultExecutionDelegate` nicht auf:</span><span class="sxs-lookup"><span data-stu-id="2da33-788">To short-circuit, set [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) to `true` and don't call the `ResultExecutionDelegate`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MyAsyncResponseFilter.cs?name=snippet)]

<span data-ttu-id="2da33-789">Das Framework stellt ein abstraktes `ResultFilterAttribute` bereit, das als Unterklasse verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="2da33-789">The framework provides an abstract `ResultFilterAttribute` that can be subclassed.</span></span> <span data-ttu-id="2da33-790">Die weiter oben gezeigte Klasse [AddHeaderAttribute](#add-header-attribute) ist ein Beispiel für ein Ergebnisfilterattribut.</span><span class="sxs-lookup"><span data-stu-id="2da33-790">The [AddHeaderAttribute](#add-header-attribute) class shown previously is an example of a result filter attribute.</span></span>

### <a name="ialwaysrunresultfilter-and-iasyncalwaysrunresultfilter"></a><span data-ttu-id="2da33-791">IAlwaysRunResultFilter und IAsyncAlwaysRunResultFilter</span><span class="sxs-lookup"><span data-stu-id="2da33-791">IAlwaysRunResultFilter and IAsyncAlwaysRunResultFilter</span></span>

<span data-ttu-id="2da33-792">Die Schnittstellen <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> und <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> deklarieren eine <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter>-Implementierung, die für alle Aktionsergebnisse ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="2da33-792">The <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> interfaces declare an <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> implementation that runs for all action results.</span></span> <span data-ttu-id="2da33-793">Dies schließt Aktionsergebnisse ein, die von folgenden Filtern generiert werden:</span><span class="sxs-lookup"><span data-stu-id="2da33-793">This includes action results produced by:</span></span>

* <span data-ttu-id="2da33-794">Autorisierungs- und Ressourcenfilter, die einen Kurzschluss bewirken</span><span class="sxs-lookup"><span data-stu-id="2da33-794">Authorization filters and resource filters that short-circuit.</span></span>
* <span data-ttu-id="2da33-795">Ausnahmefilter</span><span class="sxs-lookup"><span data-stu-id="2da33-795">Exception filters.</span></span>

<span data-ttu-id="2da33-796">Beispielsweise wird der folgende Filter immer ausgeführt und legt ein Aktionsergebnis (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) mit dem Statuscode *422 – Einheit kann nicht bearbeitet werden* fest, wenn bei der Inhaltsaushandlung ein Fehler auftritt:</span><span class="sxs-lookup"><span data-stu-id="2da33-796">For example, the following filter always runs and sets an action result (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) with a *422 Unprocessable Entity* status code when content negotiation fails:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/UnprocessableResultFilter.cs?name=snippet)]

### <a name="ifilterfactory"></a><span data-ttu-id="2da33-797">IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="2da33-797">IFilterFactory</span></span>

<span data-ttu-id="2da33-798"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implementiert <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="2da33-798"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span></span> <span data-ttu-id="2da33-799">Deshalb kann eine `IFilterFactory`-Instanz an einer beliebigen Stelle in der Filterpipeline als `IFilterMetadata`-Instanz verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="2da33-799">Therefore, an `IFilterFactory` instance can be used as an `IFilterMetadata` instance anywhere in the filter pipeline.</span></span> <span data-ttu-id="2da33-800">Wenn die Runtime den Aufruf des Filters vorbereitet, versucht sie, ihn in eine `IFilterFactory` umzuwandeln.</span><span class="sxs-lookup"><span data-stu-id="2da33-800">When the runtime prepares to invoke the filter, it attempts to cast it to an `IFilterFactory`.</span></span> <span data-ttu-id="2da33-801">Wenn diese Umwandlung gelingt, wird die Methode <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> aufgerufen, um die Instanz `IFilterMetadata` für den Aufruf zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="2da33-801">If that cast succeeds, the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method is called to create the `IFilterMetadata` instance that is invoked.</span></span> <span data-ttu-id="2da33-802">Da die exakte Filterpipeline beim Start der Anwendung nicht explizit festgelegt werden muss, wird dadurch ein sehr flexibles Design ermöglicht.</span><span class="sxs-lookup"><span data-stu-id="2da33-802">This provides a flexible design, since the precise filter pipeline doesn't need to be set explicitly when the app starts.</span></span>

<span data-ttu-id="2da33-803">Als weiteres Verfahren zum Erstellen von Filtern kann `IFilterFactory` mithilfe von benutzerdefinierten Attributimplementierungen implementiert werden:</span><span class="sxs-lookup"><span data-stu-id="2da33-803">`IFilterFactory` can be implemented using custom attribute implementations as another approach to creating filters:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

<span data-ttu-id="2da33-804">Der oben stehende Code kann durch Ausführen des [Downloadbeispiels](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample) getestet werden:</span><span class="sxs-lookup"><span data-stu-id="2da33-804">The preceding code can be tested by running the [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample):</span></span>

* <span data-ttu-id="2da33-805">Rufen Sie die F12-Entwicklungstools auf.</span><span class="sxs-lookup"><span data-stu-id="2da33-805">Invoke the F12 developer tools.</span></span>
* <span data-ttu-id="2da33-806">Navigieren Sie zu `https://localhost:5001/Sample/HeaderWithFactory`.</span><span class="sxs-lookup"><span data-stu-id="2da33-806">Navigate to `https://localhost:5001/Sample/HeaderWithFactory`.</span></span>

<span data-ttu-id="2da33-807">Die F12-Entwicklungstools zeigen folgende Antwortheader an, die vom Beispielcode hinzugefügt wurden:</span><span class="sxs-lookup"><span data-stu-id="2da33-807">The F12 developer tools display the following response headers added by the sample code:</span></span>

* <span data-ttu-id="2da33-808">**Autor:**`Joe Smith`</span><span class="sxs-lookup"><span data-stu-id="2da33-808">**author:** `Joe Smith`</span></span>
* <span data-ttu-id="2da33-809">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span><span class="sxs-lookup"><span data-stu-id="2da33-809">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span></span>
* <span data-ttu-id="2da33-810">**intern:**`My header`</span><span class="sxs-lookup"><span data-stu-id="2da33-810">**internal:** `My header`</span></span>

<span data-ttu-id="2da33-811">Der oben stehende Code erstellt den Antwortheader „**internal:** `My header`“.</span><span class="sxs-lookup"><span data-stu-id="2da33-811">The preceding code creates the **internal:** `My header` response header.</span></span>

### <a name="ifilterfactory-implemented-on-an-attribute"></a><span data-ttu-id="2da33-812">In einem Attribut implementierte IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="2da33-812">IFilterFactory implemented on an attribute</span></span>

<!-- Review 
This section needs to be rewritten.
What's a non-named attribute?
-->

<span data-ttu-id="2da33-813">Filter, die `IFilterFactory` implementieren, eignen sich in folgenden Fällen:</span><span class="sxs-lookup"><span data-stu-id="2da33-813">Filters that implement `IFilterFactory` are useful for filters that:</span></span>

* <span data-ttu-id="2da33-814">Sie erfordern kein Übergeben von Parametern.</span><span class="sxs-lookup"><span data-stu-id="2da33-814">Don't require passing parameters.</span></span>
* <span data-ttu-id="2da33-815">Sie verfügen über Konstruktorabhängigkeiten, die durch DI erfüllt werden müssen.</span><span class="sxs-lookup"><span data-stu-id="2da33-815">Have constructor dependencies that need to be filled by DI.</span></span>

<span data-ttu-id="2da33-816"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implementiert <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span><span class="sxs-lookup"><span data-stu-id="2da33-816"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="2da33-817">`IFilterFactory` macht die <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*>-Methode zum Erstellen einer <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>-Instanz verfügbar.</span><span class="sxs-lookup"><span data-stu-id="2da33-817">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="2da33-818">`CreateInstance` lädt den angegebenen Typ aus dem Dienstecontainer (DI).</span><span class="sxs-lookup"><span data-stu-id="2da33-818">`CreateInstance` loads the specified type from the services container (DI).</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

<span data-ttu-id="2da33-819">Der folgende Code zeigt drei Ansätze zum Anwenden von `[SampleActionFilter]`:</span><span class="sxs-lookup"><span data-stu-id="2da33-819">The following code shows three approaches to applying the `[SampleActionFilter]`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet&highlight=1)]

<span data-ttu-id="2da33-820">Im oben stehenden Code ist das Ergänzen der Methode um `[SampleActionFilter]` der bevorzugte Ansatz zum Anwenden von `SampleActionFilter`.</span><span class="sxs-lookup"><span data-stu-id="2da33-820">In the preceding code, decorating the method with `[SampleActionFilter]` is the preferred approach to applying the `SampleActionFilter`.</span></span>

## <a name="using-middleware-in-the-filter-pipeline"></a><span data-ttu-id="2da33-821">Verwenden von Middleware in der Filterpipeline</span><span class="sxs-lookup"><span data-stu-id="2da33-821">Using middleware in the filter pipeline</span></span>

<span data-ttu-id="2da33-822">Ressourcenfilter funktionieren wie [Middleware](xref:fundamentals/middleware/index), sie umschließen alle Ausführungen, die später in der Pipeline enthalten sind.</span><span class="sxs-lookup"><span data-stu-id="2da33-822">Resource filters work like [middleware](xref:fundamentals/middleware/index) in that they surround the execution of everything that comes later in the pipeline.</span></span> <span data-ttu-id="2da33-823">Filter unterscheiden sich jedoch in der Hinsicht von Middleware, dass sie Teil der ASP.NET Core-Runtime sind, was bedeutet, dass sie Zugriff auf ASP.NET Core-Kontext und -Konstrukte haben.</span><span class="sxs-lookup"><span data-stu-id="2da33-823">But filters differ from middleware in that they're part of the ASP.NET Core runtime, which means that they have access to ASP.NET Core context and constructs.</span></span>

<span data-ttu-id="2da33-824">Um Middleware als Filter zu verwenden, erstellen Sie einen Typ mit einer `Configure`-Methode, die die Middleware festlegt, die in die Filterpipeline eingefügt werden soll.</span><span class="sxs-lookup"><span data-stu-id="2da33-824">To use middleware as a filter, create a type with a `Configure` method that specifies the middleware to inject into the filter pipeline.</span></span> <span data-ttu-id="2da33-825">Das folgende Beispiel verwendet die Lokalisierungsmiddleware, um die aktuelle Kultur einer Anforderung einzurichten:</span><span class="sxs-lookup"><span data-stu-id="2da33-825">The following example uses the localization middleware to establish the current culture for a request:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,22)]

<span data-ttu-id="2da33-826">Verwenden Sie das <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> zum Ausführen der Middleware:</span><span class="sxs-lookup"><span data-stu-id="2da33-826">Use the <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> to run the middleware:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

<span data-ttu-id="2da33-827">Middlewarefilter werden zum selben Zeitpunkt in der Filterpipeline wie Ressourcenfilter, vor der Modellbindung und nach dem Rest der Pipeline ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="2da33-827">Middleware filters run at the same stage of the filter pipeline as Resource filters, before model binding and after the rest of the pipeline.</span></span>

## <a name="next-actions"></a><span data-ttu-id="2da33-828">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="2da33-828">Next actions</span></span>

* <span data-ttu-id="2da33-829">Weitere Informationen finden Sie unter [Filter Methoden für Razor Seiten](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="2da33-829">See [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>
* <span data-ttu-id="2da33-830">Um Filter näher kennenzulernen, [laden Sie das GitHub-Beispiel herunter, und testen und bearbeiten Sie es](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample).</span><span class="sxs-lookup"><span data-stu-id="2da33-830">To experiment with filters, [download, test, and modify the GitHub sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample).</span></span>

::: moniker-end
