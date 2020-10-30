---
title: Ansichtskomponenten in ASP.NET Core
author: rick-anderson
description: Erfahren Sie, wie Ansichtskomponenten in ASP.NET Core verwendet werden und wie sie Apps hinzugefügt werden.
ms.author: riande
ms.custom: mvc
ms.date: 12/18/2019
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
uid: mvc/views/view-components
ms.openlocfilehash: e0ff97b53d12fbf6c6a89e94704de1aee9d7f9e6
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060585"
---
# <a name="view-components-in-aspnet-core"></a><span data-ttu-id="fbf70-103">Ansichtskomponenten in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="fbf70-103">View components in ASP.NET Core</span></span>

<span data-ttu-id="fbf70-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="fbf70-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="fbf70-105">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/view-components/sample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="fbf70-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/view-components/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="view-components"></a><span data-ttu-id="fbf70-106">Ansichtskomponenten</span><span class="sxs-lookup"><span data-stu-id="fbf70-106">View components</span></span>

<span data-ttu-id="fbf70-107">Ansichtskomponenten ähneln zwar den Teilansichten, sind aber wesentlich leistungsstärker.</span><span class="sxs-lookup"><span data-stu-id="fbf70-107">View components are similar to partial views, but they're much more powerful.</span></span> <span data-ttu-id="fbf70-108">Ansichtskomponenten verwenden keine Modellbindungen und sind nur von den Daten abhängig, die bei ihrem Aufruf bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="fbf70-108">View components don't use model binding, and only depend on the data provided when calling into it.</span></span> <span data-ttu-id="fbf70-109">Dieser Artikel wurde mit Controllern und Ansichten geschrieben, aber Ansichts Komponenten funktionieren auch mit :::no-loc(Razor)::: Seiten.</span><span class="sxs-lookup"><span data-stu-id="fbf70-109">This article was written using controllers and views, but view components also work with :::no-loc(Razor)::: Pages.</span></span>

<span data-ttu-id="fbf70-110">Eine Ansichtskomponente:</span><span class="sxs-lookup"><span data-stu-id="fbf70-110">A view component:</span></span>

* <span data-ttu-id="fbf70-111">Rendert nur einen Block statt einer gesamten Antwort.</span><span class="sxs-lookup"><span data-stu-id="fbf70-111">Renders a chunk rather than a whole response.</span></span>
* <span data-ttu-id="fbf70-112">Umfasst die gleiche Trennung von Belangen und Vorzüge der Testbarkeit, die auch zwischen einem Controller und einer Ansicht bestehen.</span><span class="sxs-lookup"><span data-stu-id="fbf70-112">Includes the same separation-of-concerns and testability benefits found between a controller and view.</span></span>
* <span data-ttu-id="fbf70-113">Kann Parameter und Geschäftslogik aufweisen.</span><span class="sxs-lookup"><span data-stu-id="fbf70-113">Can have parameters and business logic.</span></span>
* <span data-ttu-id="fbf70-114">Wird normalerweise von einer Layoutseite aus aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="fbf70-114">Is typically invoked from a layout page.</span></span>

<span data-ttu-id="fbf70-115">Ansichtskomponenten wurden für wiederverwendbare Renderinglogik entwickelt, die für eine Teilansicht zu komplex ist. Dazu gehören:</span><span class="sxs-lookup"><span data-stu-id="fbf70-115">View components are intended anywhere you have reusable rendering logic that's too complex for a partial view, such as:</span></span>

* <span data-ttu-id="fbf70-116">Dynamische Navigationsmenüs</span><span class="sxs-lookup"><span data-stu-id="fbf70-116">Dynamic navigation menus</span></span>
* <span data-ttu-id="fbf70-117">Tag Cloud (dort, wo die Datenbank abgefragt wird)</span><span class="sxs-lookup"><span data-stu-id="fbf70-117">Tag cloud (where it queries the database)</span></span>
* <span data-ttu-id="fbf70-118">ein Anmeldebereich</span><span class="sxs-lookup"><span data-stu-id="fbf70-118">Login panel</span></span>
* <span data-ttu-id="fbf70-119">Einkaufswagen</span><span class="sxs-lookup"><span data-stu-id="fbf70-119">Shopping cart</span></span>
* <span data-ttu-id="fbf70-120">vor Kurzem veröffentlichte Artikel</span><span class="sxs-lookup"><span data-stu-id="fbf70-120">Recently published articles</span></span>
* <span data-ttu-id="fbf70-121">Inhalt in einer Seitenleiste auf einem klassischen Blog</span><span class="sxs-lookup"><span data-stu-id="fbf70-121">Sidebar content on a typical blog</span></span>
* <span data-ttu-id="fbf70-122">Ein Anmeldebereich, der auf jeder Seite gerendert wird und der die Links zum Abmelden bzw. Anmelden anzeigt, je nachdem, ob der Benutzer an- oder abgemeldet ist</span><span class="sxs-lookup"><span data-stu-id="fbf70-122">A login panel that would be rendered on every page and show either the links to log out or log in, depending on the log in state of the user</span></span>

<span data-ttu-id="fbf70-123">Eine Ansichtskomponenten besteht aus zwei Teilen: der Klasse (normalerweise von [ViewComponent](/dotnet/api/microsoft.aspnetcore.mvc.viewcomponent) abgeleitet) und dem von dieser Klasse zurückgegebenen Ergebnis (normalerweise eine Ansicht).</span><span class="sxs-lookup"><span data-stu-id="fbf70-123">A view component consists of two parts: the class (typically derived from [ViewComponent](/dotnet/api/microsoft.aspnetcore.mvc.viewcomponent)) and the result it returns (typically a view).</span></span> <span data-ttu-id="fbf70-124">Eine Ansichtskomponente kann, ähnlich wie Controller, ein POCO sein. Die meisten Entwickler sollten jedoch von den Methoden und Eigenschaften, die von `ViewComponent` abgeleitet werden, Gebrauch machen.</span><span class="sxs-lookup"><span data-stu-id="fbf70-124">Like controllers, a view component can be a POCO, but most developers will want to take advantage of the methods and properties available by deriving from `ViewComponent`.</span></span>

<span data-ttu-id="fbf70-125">Wenn Sie überlegen, ob Ansichts Komponenten die Spezifikationen einer APP erfüllen, erwägen Sie stattdessen die Verwendung von- :::no-loc(Razor)::: Komponenten.</span><span class="sxs-lookup"><span data-stu-id="fbf70-125">When considering if view components meet an app's specifications, consider using :::no-loc(Razor)::: Components instead.</span></span> <span data-ttu-id="fbf70-126">:::no-loc(Razor)::: Komponenten kombinieren auch Markup mit c#-Code, um wiederverwendbare UI-Einheiten zu schaffen</span><span class="sxs-lookup"><span data-stu-id="fbf70-126">:::no-loc(Razor)::: Components also combine markup with C# code to produce reusable UI units.</span></span> <span data-ttu-id="fbf70-127">:::no-loc(Razor)::: Komponenten sind für Entwickler Produktivität konzipiert, wenn Sie Client seitige Benutzeroberflächen Logik und Komposition bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="fbf70-127">:::no-loc(Razor)::: Components are designed for developer productivity when providing client-side UI logic and composition.</span></span> <span data-ttu-id="fbf70-128">Weitere Informationen finden Sie unter <xref:blazor/components/index>.</span><span class="sxs-lookup"><span data-stu-id="fbf70-128">For more information, see <xref:blazor/components/index>.</span></span>

## <a name="creating-a-view-component"></a><span data-ttu-id="fbf70-129">Erstellen einer Ansichtskomponente</span><span class="sxs-lookup"><span data-stu-id="fbf70-129">Creating a view component</span></span>

<span data-ttu-id="fbf70-130">In diesem Abschnitt werden die allgemeinen Anforderungen zum Erstellen einer Ansichtskomponente beschrieben.</span><span class="sxs-lookup"><span data-stu-id="fbf70-130">This section contains the high-level requirements to create a view component.</span></span> <span data-ttu-id="fbf70-131">Im Folgenden wird jeder Schritt ausführlich betrachtet, und Sie erstellen im Zuge dessen eine Ansichtskomponente.</span><span class="sxs-lookup"><span data-stu-id="fbf70-131">Later in the article, we'll examine each step in detail and create a view component.</span></span>

### <a name="the-view-component-class"></a><span data-ttu-id="fbf70-132">Die Ansichtskomponentenklasse</span><span class="sxs-lookup"><span data-stu-id="fbf70-132">The view component class</span></span>

<span data-ttu-id="fbf70-133">Eine Ansichtskomponentenklasse kann durch folgende Aktionen erstellt werden:</span><span class="sxs-lookup"><span data-stu-id="fbf70-133">A view component class can be created by any of the following:</span></span>

* <span data-ttu-id="fbf70-134">durch die Ableitung von *ViewComponent*</span><span class="sxs-lookup"><span data-stu-id="fbf70-134">Deriving from *ViewComponent*</span></span>
* <span data-ttu-id="fbf70-135">durch Ergänzen der Klasse mit dem Attribut `[ViewComponent]` oder durch das Ableiten von einer Klasse mit dem Attribut `[ViewComponent]`</span><span class="sxs-lookup"><span data-stu-id="fbf70-135">Decorating a class with the `[ViewComponent]` attribute, or deriving from a class with the `[ViewComponent]` attribute</span></span>
* <span data-ttu-id="fbf70-136">durch das Erstellen einer Klasse, deren Name mit dem Suffix *ViewComponent* endet</span><span class="sxs-lookup"><span data-stu-id="fbf70-136">Creating a class where the name ends with the suffix *ViewComponent*</span></span>

<span data-ttu-id="fbf70-137">Ansichtskomponenten müssen, genauso wie Controller, öffentliche, unverschachtelt und nicht abstrakte Klassen sein.</span><span class="sxs-lookup"><span data-stu-id="fbf70-137">Like controllers, view components must be public, non-nested, and non-abstract classes.</span></span> <span data-ttu-id="fbf70-138">Der Ansichtskomponentenname ist der Klassenname ohne den Suffix „ViewComponent“.</span><span class="sxs-lookup"><span data-stu-id="fbf70-138">The view component name is the class name with the "ViewComponent" suffix removed.</span></span> <span data-ttu-id="fbf70-139">Er kann zudem explizit mit der Eigenschaft `ViewComponentAttribute.Name` angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="fbf70-139">It can also be explicitly specified using the `ViewComponentAttribute.Name` property.</span></span>

<span data-ttu-id="fbf70-140">Eine Ansichtskomponentenklasse:</span><span class="sxs-lookup"><span data-stu-id="fbf70-140">A view component class:</span></span>

* <span data-ttu-id="fbf70-141">Unterstützt [Constructor Dependency Injection](../../fundamentals/dependency-injection.md) vollständig</span><span class="sxs-lookup"><span data-stu-id="fbf70-141">Fully supports constructor [dependency injection](../../fundamentals/dependency-injection.md)</span></span>

* <span data-ttu-id="fbf70-142">Ist nicht Bestandteil des Controllerlebenszyklus. Dies bedeutet, dass Sie keine [Filter](../controllers/filters.md) in Ansichtskomponenten verwenden können.</span><span class="sxs-lookup"><span data-stu-id="fbf70-142">Doesn't take part in the controller lifecycle, which means you can't use [filters](../controllers/filters.md) in a view component</span></span>

### <a name="view-component-methods"></a><span data-ttu-id="fbf70-143">Ansichtskomponentenmethoden</span><span class="sxs-lookup"><span data-stu-id="fbf70-143">View component methods</span></span>

<span data-ttu-id="fbf70-144">Eine Ansichtskomponente definiert ihre Logik in einer `InvokeAsync`-Methode, die ein `Task<IViewComponentResult>` zurückgibt, oder in einer synchronen `Invoke`-Methode, die ein `IViewComponentResult` zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="fbf70-144">A view component defines its logic in an `InvokeAsync` method that returns a `Task<IViewComponentResult>` or in a synchronous `Invoke` method that returns an `IViewComponentResult`.</span></span> <span data-ttu-id="fbf70-145">Parameter stammen direkt vom Aufruf der Ansichtskomponente und nicht von der Modellbindung.</span><span class="sxs-lookup"><span data-stu-id="fbf70-145">Parameters come directly from invocation of the view component, not from model binding.</span></span> <span data-ttu-id="fbf70-146">Eine Ansichtskomponente behandelt nie direkt eine Anfrage.</span><span class="sxs-lookup"><span data-stu-id="fbf70-146">A view component never directly handles a request.</span></span> <span data-ttu-id="fbf70-147">Normalerweise initialisiert eine Ansichtskomponente ein Modell und übergibt dieses an eine Ansicht, indem sie die `View`-Methode aufruft.</span><span class="sxs-lookup"><span data-stu-id="fbf70-147">Typically, a view component initializes a model and passes it to a view by calling the `View` method.</span></span> <span data-ttu-id="fbf70-148">Zusammengefasst bedeutet dies für Komponentenmethoden Folgendes:</span><span class="sxs-lookup"><span data-stu-id="fbf70-148">In summary, view component methods:</span></span>

* <span data-ttu-id="fbf70-149">Es wird eine `InvokeAsync`-Methode definiert, die ein `Task<IViewComponentResult>` zurückgibt, oder eine synchrone `Invoke`-Methode, die ein `IViewComponentResult` zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="fbf70-149">Define an `InvokeAsync` method that returns a `Task<IViewComponentResult>` or a synchronous `Invoke` method that returns an `IViewComponentResult`.</span></span>
* <span data-ttu-id="fbf70-150">Initialisiert in der Regel ein Modell und übergibt es an eine Ansicht, indem die-Methode aufgerufen wird `ViewComponent` `View` .</span><span class="sxs-lookup"><span data-stu-id="fbf70-150">Typically initializes a model and passes it to a view by calling the `ViewComponent` `View` method.</span></span>
* <span data-ttu-id="fbf70-151">Parameter stammen aus der aufrufenden Methode, nicht aus HTTP.</span><span class="sxs-lookup"><span data-stu-id="fbf70-151">Parameters come from the calling method, not HTTP.</span></span> <span data-ttu-id="fbf70-152">Es gibt keine Modellbindung.</span><span class="sxs-lookup"><span data-stu-id="fbf70-152">There's no model binding.</span></span>
* <span data-ttu-id="fbf70-153">Die Methoden sind nicht direkt als HTTP-Endpunkt erreichbar.</span><span class="sxs-lookup"><span data-stu-id="fbf70-153">Are not reachable directly as an HTTP endpoint.</span></span> <span data-ttu-id="fbf70-154">Sie werden von Ihrem Code aufgerufen (üblicherweise in einer Ansicht).</span><span class="sxs-lookup"><span data-stu-id="fbf70-154">They're invoked from your code (usually in a view).</span></span> <span data-ttu-id="fbf70-155">Eine Ansichtskomponente verarbeitet nie eine Anforderung.</span><span class="sxs-lookup"><span data-stu-id="fbf70-155">A view component never handles a request.</span></span>
* <span data-ttu-id="fbf70-156">Methoden werden in der Signatur überladen, nicht in Details der aktuellen HTTP-Anforderung.</span><span class="sxs-lookup"><span data-stu-id="fbf70-156">Are overloaded on the signature rather than any details from the current HTTP request.</span></span>

### <a name="view-search-path"></a><span data-ttu-id="fbf70-157">Anzeigen des Suchpfads</span><span class="sxs-lookup"><span data-stu-id="fbf70-157">View search path</span></span>

<span data-ttu-id="fbf70-158">Die Runtime sucht in den folgenden Pfaden nach der Ansicht:</span><span class="sxs-lookup"><span data-stu-id="fbf70-158">The runtime searches for the view in the following paths:</span></span>

* <span data-ttu-id="fbf70-159">/Views/{Controllername} /Components/{Ansichtskomponentenname}/{Ansichtsname}</span><span class="sxs-lookup"><span data-stu-id="fbf70-159">/Views/{Controller Name}/Components/{View Component Name}/{View Name}</span></span>
* <span data-ttu-id="fbf70-160">/Views/Shared/Components/{Ansichtskomponentenname}/{Ansichtsname}</span><span class="sxs-lookup"><span data-stu-id="fbf70-160">/Views/Shared/Components/{View Component Name}/{View Name}</span></span>
* <span data-ttu-id="fbf70-161">/Pages/Shared/Components/{Ansichtskomponentenname}/{Ansichtsname}</span><span class="sxs-lookup"><span data-stu-id="fbf70-161">/Pages/Shared/Components/{View Component Name}/{View Name}</span></span>

<span data-ttu-id="fbf70-162">Der Suchpfad gilt für Projekte mit Controllern und Ansichten und :::no-loc(Razor)::: Seiten.</span><span class="sxs-lookup"><span data-stu-id="fbf70-162">The search path applies to projects using controllers + views and :::no-loc(Razor)::: Pages.</span></span>

<span data-ttu-id="fbf70-163">Der Standardansichtsname für die Ansichtskomponente ist *Default* . Dies bedeutet, dass Ihre Ansichtsdatei normalerweise *Default.cshtml* heißt.</span><span class="sxs-lookup"><span data-stu-id="fbf70-163">The default view name for a view component is *Default* , which means your view file will typically be named *Default.cshtml* .</span></span> <span data-ttu-id="fbf70-164">Sie können einen anderen Ansichtsnamen angeben, wenn Sie die Ansichtskomponentenergebnisse erstellen oder die `View`-Methode aufrufen.</span><span class="sxs-lookup"><span data-stu-id="fbf70-164">You can specify a different view name when creating the view component result or when calling the `View` method.</span></span>

<span data-ttu-id="fbf70-165">Es wird empfohlen, dass Sie die Ansichtsdatei *Default.cshtml* nennen und den Pfad *View/Shared/Components/{Ansichtskomponentenname}/{Ansichtsname}* verwenden.</span><span class="sxs-lookup"><span data-stu-id="fbf70-165">We recommend you name the view file *Default.cshtml* and use the *Views/Shared/Components/{View Component Name}/{View Name}* path.</span></span> <span data-ttu-id="fbf70-166">Die Ansichtskomponente `PriorityList`, die in diesem Beispiel verwendet wird, verwendet *Views/Shared/Components/PriorityList/Default.cshtml* für die Ansichtskomponentenansicht.</span><span class="sxs-lookup"><span data-stu-id="fbf70-166">The `PriorityList` view component used in this sample uses *Views/Shared/Components/PriorityList/Default.cshtml* for the view component view.</span></span>

### <a name="customize-the-view-search-path"></a><span data-ttu-id="fbf70-167">Anpassen des Ansichtssuchpfads</span><span class="sxs-lookup"><span data-stu-id="fbf70-167">Customize the view search path</span></span>

<span data-ttu-id="fbf70-168">Um den Suchpfad für die Ansicht anzupassen, ändern Sie :::no-loc(Razor)::: die <xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.:::no-loc(Razor):::ViewEngineOptions.ViewLocationFormats> Sammlung.</span><span class="sxs-lookup"><span data-stu-id="fbf70-168">To customize the view search path, modify :::no-loc(Razor):::'s <xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.:::no-loc(Razor):::ViewEngineOptions.ViewLocationFormats> collection.</span></span> <span data-ttu-id="fbf70-169">Um z. B. nach Ansichten im Pfad „/Components/{Name der Ansichtskomponente}/{Name der Ansicht}“ zu suchen, fügen Sie der Auflistung ein neues Element hinzu:</span><span class="sxs-lookup"><span data-stu-id="fbf70-169">For example, to search for views within the path "/Components/{View Component Name}/{View Name}", add a new item to the collection:</span></span>

[!code-csharp[](view-components/samples_snapshot/2.x/Startup.cs?name=snippet_ViewLocationFormats&highlight=4)]

<span data-ttu-id="fbf70-170">Im vorangehenden Code stellt der Platzhalter „{0}“ den Pfad „Components/{Name der Ansichtskomponente}/{Name der Ansicht}“ dar.</span><span class="sxs-lookup"><span data-stu-id="fbf70-170">In the preceding code, the placeholder "{0}" represents the path "Components/{View Component Name}/{View Name}".</span></span>

## <a name="invoking-a-view-component"></a><span data-ttu-id="fbf70-171">Aufrufen einer Ansichtskomponente</span><span class="sxs-lookup"><span data-stu-id="fbf70-171">Invoking a view component</span></span>

<span data-ttu-id="fbf70-172">Rufen Sie Folgendes in der Ansicht auf, wenn Sie die Ansichtskomponente verwenden möchten:</span><span class="sxs-lookup"><span data-stu-id="fbf70-172">To use the view component, call the following inside a view:</span></span>

```cshtml
@await Component.InvokeAsync("Name of view component", {Anonymous Type Containing Parameters})
```

<span data-ttu-id="fbf70-173">Die Parameter werden an die `InvokeAsync`-Methode übergeben.</span><span class="sxs-lookup"><span data-stu-id="fbf70-173">The parameters will be passed to the `InvokeAsync` method.</span></span> <span data-ttu-id="fbf70-174">Die Ansichtskomponente `PriorityList`, die im Artikel entwickelt wurde, wird von der Ansichtsdatei *Views/ToDo/Index.cshtml* aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="fbf70-174">The `PriorityList` view component developed in the article is invoked from the *Views/ToDo/Index.cshtml* view file.</span></span> <span data-ttu-id="fbf70-175">Im folgenden Code wird die `InvokeAsync`-Methode mit zwei Parametern aufgerufen:</span><span class="sxs-lookup"><span data-stu-id="fbf70-175">In the following, the `InvokeAsync` method is called with two parameters:</span></span>

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexFinal.cshtml?range=35)]

::: moniker range=">= aspnetcore-1.1"

## <a name="invoking-a-view-component-as-a-tag-helper"></a><span data-ttu-id="fbf70-176">Aufrufen einer Ansichtskomponente als Taghilfsprogramm</span><span class="sxs-lookup"><span data-stu-id="fbf70-176">Invoking a view component as a Tag Helper</span></span>

<span data-ttu-id="fbf70-177">In ASP.NET Core 1.1 und höher können Sie eine Ansichtskomponente als [Taghilfsprogramm](xref:mvc/views/tag-helpers/intro) aufrufen.</span><span class="sxs-lookup"><span data-stu-id="fbf70-177">For ASP.NET Core 1.1 and higher, you can invoke a view component as a [Tag Helper](xref:mvc/views/tag-helpers/intro):</span></span>

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexTagHelper.cshtml?range=37-38)]

<span data-ttu-id="fbf70-178">Namen von Klassen und Methodenparameter für Taghilfsprogramme, die in Pascal-Schreibweise angegeben sind, werden in [Kebab-Schreibweise](https://stackoverflow.com/questions/11273282/whats-the-name-for-dash-separated-case/12273101) übersetzt.</span><span class="sxs-lookup"><span data-stu-id="fbf70-178">Pascal-cased class and method parameters for Tag Helpers are translated into their [kebab case](https://stackoverflow.com/questions/11273282/whats-the-name-for-dash-separated-case/12273101).</span></span> <span data-ttu-id="fbf70-179">Das Taghilfsprogramm zum Aufrufen einer Ansichtskomponente verwendet das `<vc></vc>`-Element.</span><span class="sxs-lookup"><span data-stu-id="fbf70-179">The Tag Helper to invoke a view component uses the `<vc></vc>` element.</span></span> <span data-ttu-id="fbf70-180">Die Ansichtskomponente wird wie folgt angegeben:</span><span class="sxs-lookup"><span data-stu-id="fbf70-180">The view component is specified as follows:</span></span>

```cshtml
<vc:[view-component-name]
  parameter1="parameter1 value"
  parameter2="parameter2 value">
</vc:[view-component-name]>
```

<span data-ttu-id="fbf70-181">Damit Sie eine Ansichtskomponente als Taghilfsprogramm verwenden können, registrieren Sie die Assembly, die die Ansichtskomponente enthält, mit der `@addTagHelper`-Anweisung.</span><span class="sxs-lookup"><span data-stu-id="fbf70-181">To use a view component as a Tag Helper, register the assembly containing the view component using the `@addTagHelper` directive.</span></span> <span data-ttu-id="fbf70-182">Wenn Ihre Ansichtskomponente eine Assembly mit dem Namen `MyWebApp` ist, fügen Sie die folgende Anweisung zu der Datei *_ViewImports.cshtml* hinzu:</span><span class="sxs-lookup"><span data-stu-id="fbf70-182">If your view component is in an assembly called `MyWebApp`, add the following directive to the *_ViewImports.cshtml* file:</span></span>

```cshtml
@addTagHelper *, MyWebApp
```

<span data-ttu-id="fbf70-183">Sie können eine Ansichtskomponente als Taghilfsprogramm für jede Datei registrieren, die auf die Ansichtskomponente verweist.</span><span class="sxs-lookup"><span data-stu-id="fbf70-183">You can register a view component as a Tag Helper to any file that references the view component.</span></span> <span data-ttu-id="fbf70-184">Weitere Informationen zum Registrieren eines Taghilfsprogramms finden Sie unter [Managing Tag Helper Scope (Verwalten des Bereichs des Taghilfsprogramms)](xref:mvc/views/tag-helpers/intro#managing-tag-helper-scope).</span><span class="sxs-lookup"><span data-stu-id="fbf70-184">See [Managing Tag Helper Scope](xref:mvc/views/tag-helpers/intro#managing-tag-helper-scope) for more information on how to register Tag Helpers.</span></span>

<span data-ttu-id="fbf70-185">Die `InvokeAsync`-Methode, die in diesem Tutorial verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="fbf70-185">The `InvokeAsync` method used in this tutorial:</span></span>

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexFinal.cshtml?range=35)]

<span data-ttu-id="fbf70-186">In Taghilfsprogramm-Markup:</span><span class="sxs-lookup"><span data-stu-id="fbf70-186">In Tag Helper markup:</span></span>

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexTagHelper.cshtml?range=37-38)]

<span data-ttu-id="fbf70-187">Im Beispiel oben stehenden Beispiel wird die Ansichtskomponente `PriorityList` zu `priority-list`.</span><span class="sxs-lookup"><span data-stu-id="fbf70-187">In the sample above, the `PriorityList` view component becomes `priority-list`.</span></span> <span data-ttu-id="fbf70-188">Die Parameter der Ansichtskomponente werden als Attribute in Kebab-Schreibweise übergeben.</span><span class="sxs-lookup"><span data-stu-id="fbf70-188">The parameters to the view component are passed as attributes in kebab case.</span></span>

::: moniker-end

### <a name="invoking-a-view-component-directly-from-a-controller"></a><span data-ttu-id="fbf70-189">Direkter Aufrufe einer Ansichtskomponente von einem Controller</span><span class="sxs-lookup"><span data-stu-id="fbf70-189">Invoking a view component directly from a controller</span></span>

<span data-ttu-id="fbf70-190">Ansichtskomponenten werden normalerweise von einer Ansicht aus aufgerufen, aber Sie können sie auch direkt von einer Controllermethode aus aufrufen.</span><span class="sxs-lookup"><span data-stu-id="fbf70-190">View components are typically invoked from a view, but you can invoke them directly from a controller method.</span></span> <span data-ttu-id="fbf70-191">Obwohl Ansichtskomponenten keine Endpunkte so wie Controller definieren, können Sie trotzdem eine Controlleraktion implementieren, die den Inhalt von `ViewComponentResult` zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="fbf70-191">While view components don't define endpoints like controllers, you can easily implement a controller action that returns the content of a `ViewComponentResult`.</span></span>

<span data-ttu-id="fbf70-192">In diesem Beispiel wird die Ansichtskomponente direkt von einem Controller aus aufgerufen:</span><span class="sxs-lookup"><span data-stu-id="fbf70-192">In this example, the view component is called directly from the controller:</span></span>

[!code-csharp[](view-components/sample/ViewCompFinal/Controllers/ToDoController.cs?name=snippet_IndexVC)]

## <a name="walkthrough-creating-a-simple-view-component"></a><span data-ttu-id="fbf70-193">Exemplarische Vorgehensweise: Erstellen einer einfachen Ansichtskomponente</span><span class="sxs-lookup"><span data-stu-id="fbf70-193">Walkthrough: Creating a simple view component</span></span>

<span data-ttu-id="fbf70-194">[Laden Sie den Startercode herunter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/view-components/sample), und erstellen und testen Sie diesen.</span><span class="sxs-lookup"><span data-stu-id="fbf70-194">[Download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/view-components/sample), build and test the starter code.</span></span> <span data-ttu-id="fbf70-195">Dabei handelt es sich um ein einfaches Projekt mit einem `ToDo`-Controller, in dem eine Liste von *ToDo* -Elementen angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="fbf70-195">It's a simple project with a `ToDo` controller that displays a list of *ToDo* items.</span></span>

![Liste mit ToDo-Elementen](view-components/_static/2dos.png)

### <a name="add-a-viewcomponent-class"></a><span data-ttu-id="fbf70-197">Hinzufügen einer ViewComponent-Klasse</span><span class="sxs-lookup"><span data-stu-id="fbf70-197">Add a ViewComponent class</span></span>

<span data-ttu-id="fbf70-198">Erstellen Sie einen *ViewComponents* -Ordner, und fügen Sie die folgende `PriorityListViewComponent`-Klasse hinzu:</span><span class="sxs-lookup"><span data-stu-id="fbf70-198">Create a *ViewComponents* folder and add the following `PriorityListViewComponent` class:</span></span>

[!code-csharp[](view-components/sample/ViewCompFinal/ViewComponents/PriorityListViewComponent1.cs?name=snippet1)]

<span data-ttu-id="fbf70-199">Bemerkungen zum Code:</span><span class="sxs-lookup"><span data-stu-id="fbf70-199">Notes on the code:</span></span>

* <span data-ttu-id="fbf70-200">Ansichtskomponentenklassen können sich in **jedem** Ordner im Projekt befinden.</span><span class="sxs-lookup"><span data-stu-id="fbf70-200">View component classes can be contained in **any** folder in the project.</span></span>
* <span data-ttu-id="fbf70-201">Da der Klassenname „PriorityList **ViewComponent** mit dem Suffix **ViewComponent** endet, verwendet die Runtime die Zeichenfolge „PriorityList“, wenn Sie von einer Ansicht aus auf die Klassenkomponente verweist.</span><span class="sxs-lookup"><span data-stu-id="fbf70-201">Because the class name PriorityList **ViewComponent** ends with the suffix **ViewComponent** , the runtime will use the string "PriorityList" when referencing the class component from a view.</span></span> <span data-ttu-id="fbf70-202">Dies wird im Folgenden noch ausführlicher erklärt.</span><span class="sxs-lookup"><span data-stu-id="fbf70-202">I'll explain that in more detail later.</span></span>
* <span data-ttu-id="fbf70-203">Das `[ViewComponent]`-Attribut kann den Namen ändern, der zum Verweis auf eine Ansichtskomponente verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="fbf70-203">The `[ViewComponent]` attribute can change the name used to reference a view component.</span></span> <span data-ttu-id="fbf70-204">Wir hätten die Klasse auch `XYZ` nennen und das `ViewComponent`-Attribut anwenden können:</span><span class="sxs-lookup"><span data-stu-id="fbf70-204">For example, we could've named the class `XYZ` and applied the `ViewComponent` attribute:</span></span>

  ```csharp
  [ViewComponent(Name = "PriorityList")]
     public class XYZ : ViewComponent
     ```

* <span data-ttu-id="fbf70-205">Das oben stehende `[ViewComponent]`-Attribut teilt dem Ansichtskomponentenselektor mit, den Namen `PriorityList` zu verwenden, wenn er nach den Ansichten sucht, die mit der Komponente verknüpft sind. Zudem wird er informiert, die Zeichenfolge „“ zu verwenden, wenn er von einer Ansicht aus auf die Klassenkomponente verweist.</span><span class="sxs-lookup"><span data-stu-id="fbf70-205">The `[ViewComponent]` attribute above tells the view component selector to use the name `PriorityList` when looking for the views associated with the component, and to use the string "PriorityList" when referencing the class component from a view.</span></span> <span data-ttu-id="fbf70-206">Dies wird im Folgenden noch ausführlicher erklärt.</span><span class="sxs-lookup"><span data-stu-id="fbf70-206">I'll explain that in more detail later.</span></span>
* <span data-ttu-id="fbf70-207">Die Komponente verwendet [Dependency Injection](../../fundamentals/dependency-injection.md), um den Datenkontext verfügbar zu machen.</span><span class="sxs-lookup"><span data-stu-id="fbf70-207">The component uses [dependency injection](../../fundamentals/dependency-injection.md) to make the data context available.</span></span>
* <span data-ttu-id="fbf70-208">`InvokeAsync` macht eine Methode verfügbar, die von einer Ansicht aus aufgerufen werden kann, und akzeptiert eine arbiträre Anzahl von Argumenten.</span><span class="sxs-lookup"><span data-stu-id="fbf70-208">`InvokeAsync` exposes a method which can be called from a view, and it can take an arbitrary number of arguments.</span></span>
* <span data-ttu-id="fbf70-209">Die `InvokeAsync`-Methode gibt mehrere `ToDo`-Elemente zurück, die die Bedingungen der Parameter `isDone` und `maxPriority` erfüllen.</span><span class="sxs-lookup"><span data-stu-id="fbf70-209">The `InvokeAsync` method returns the set of `ToDo` items that satisfy the `isDone` and `maxPriority` parameters.</span></span>

### <a name="create-the-view-component-no-locrazor-view"></a><span data-ttu-id="fbf70-210">Erstellen der Ansichts Komponenten :::no-loc(Razor)::: Ansicht</span><span class="sxs-lookup"><span data-stu-id="fbf70-210">Create the view component :::no-loc(Razor)::: view</span></span>

* <span data-ttu-id="fbf70-211">Erstellen Sie den Ordner *Views/Shared/Components* .</span><span class="sxs-lookup"><span data-stu-id="fbf70-211">Create the *Views/Shared/Components* folder.</span></span> <span data-ttu-id="fbf70-212">Diese Ordner **muss** den Namen *Components* besitzen.</span><span class="sxs-lookup"><span data-stu-id="fbf70-212">This folder **must** be named *Components* .</span></span>

* <span data-ttu-id="fbf70-213">Erstellen Sie den Ordner *Views/Shared/Components/PriorityList* .</span><span class="sxs-lookup"><span data-stu-id="fbf70-213">Create the *Views/Shared/Components/PriorityList* folder.</span></span> <span data-ttu-id="fbf70-214">Der Ordnername muss mit dem Namen der Ansichtskomponentenklasse oder mit dem Namen der Klasse ohne Suffix (wenn wir uns an die Konvention gehalten und *ViewComponent* als Suffix im Klassennamen verwendet haben) übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="fbf70-214">This folder name must match the name of the view component class, or the name of the class minus the suffix (if we followed convention and used the *ViewComponent* suffix in the class name).</span></span> <span data-ttu-id="fbf70-215">Wenn Sie das Attribut `ViewComponent` verwenden, muss der Klassenname mit der Attributbezeichnung übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="fbf70-215">If you used the `ViewComponent` attribute, the class name would need to match the attribute designation.</span></span>

* <span data-ttu-id="fbf70-216">Erstellen Sie eine *Sichten/Shared/Components/prioritylist/default. cshtml* - :::no-loc(Razor)::: Ansicht:</span><span class="sxs-lookup"><span data-stu-id="fbf70-216">Create a *Views/Shared/Components/PriorityList/Default.cshtml* :::no-loc(Razor)::: view:</span></span>


  [!code-cshtml[](view-components/sample/ViewCompFinal/Views/Shared/Components/PriorityList/Default1.cshtml)]

   <span data-ttu-id="fbf70-217">:::no-loc(Razor):::In der Ansicht wird eine Liste `TodoItem` von angezeigt und angezeigt.</span><span class="sxs-lookup"><span data-stu-id="fbf70-217">The :::no-loc(Razor)::: view takes a list of `TodoItem` and displays them.</span></span> <span data-ttu-id="fbf70-218">Wenn die `InvokeAsync`-Methode der Ansichtskomponente nicht den Namen der Ansicht übergibt (wie in unserem Beispiel), wird *Default* per Konvention für den Ansichtsnamen verwendet.</span><span class="sxs-lookup"><span data-stu-id="fbf70-218">If the view component `InvokeAsync` method doesn't pass the name of the view (as in our sample), *Default* is used for the view name by convention.</span></span> <span data-ttu-id="fbf70-219">Später in diesem Tutorial erfahren Sie, wie Sie den Namen der Ansicht übergeben.</span><span class="sxs-lookup"><span data-stu-id="fbf70-219">Later in the tutorial, I'll show you how to pass the name of the view.</span></span> <span data-ttu-id="fbf70-220">Fügen Sie eine Ansicht einem controllerspezifischen Ansichtsordner hinzu, um das Standardformat für einen spezifischen Controller zu überschreiben (z.B. *Views/ToDo/Components/PriorityList/Default.cshtml* ).</span><span class="sxs-lookup"><span data-stu-id="fbf70-220">To override the default styling for a specific controller, add a view to the controller-specific view folder (for example *Views/ToDo/Components/PriorityList/Default.cshtml)* .</span></span>

    <span data-ttu-id="fbf70-221">Wenn die Ansichtskomponente controllerspezifisch ist, können Sie sie dem controllerspezifischen Ordner hinzufügen ( *Views/ToDo/Components/PriorityList/Default.cshtml* ).</span><span class="sxs-lookup"><span data-stu-id="fbf70-221">If the view component is controller-specific, you can add it to the controller-specific folder ( *Views/ToDo/Components/PriorityList/Default.cshtml* ).</span></span>

* <span data-ttu-id="fbf70-222">Fügen Sie ein `div`-Objekt, das einen Aufruf an eine Prioritätslistenkomponente enthält, am Ende der Datei *Views/ToDo/index.cshtml* hinzu:</span><span class="sxs-lookup"><span data-stu-id="fbf70-222">Add a `div` containing a call to the priority list component to the bottom of the *Views/ToDo/index.cshtml* file:</span></span>

    [!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexFirst.cshtml?range=34-38)]

<span data-ttu-id="fbf70-223">Das Markup `@await Component.InvokeAsync` zeigt die Syntax für die aufrufenden Ansichtskomponenten an.</span><span class="sxs-lookup"><span data-stu-id="fbf70-223">The markup `@await Component.InvokeAsync` shows the syntax for calling view components.</span></span> <span data-ttu-id="fbf70-224">Das erste Argument ist der Name der aufzurufenden Komponente.</span><span class="sxs-lookup"><span data-stu-id="fbf70-224">The first argument is the name of the component we want to invoke or call.</span></span> <span data-ttu-id="fbf70-225">Darauffolgende Parameter werden an die Komponente übergeben.</span><span class="sxs-lookup"><span data-stu-id="fbf70-225">Subsequent parameters are passed to the component.</span></span> <span data-ttu-id="fbf70-226">`InvokeAsync` kann eine arbiträre Anzahl von Argumenten annehmen.</span><span class="sxs-lookup"><span data-stu-id="fbf70-226">`InvokeAsync` can take an arbitrary number of arguments.</span></span>

<span data-ttu-id="fbf70-227">Testen der App.</span><span class="sxs-lookup"><span data-stu-id="fbf70-227">Test the app.</span></span> <span data-ttu-id="fbf70-228">In der folgenden Abbildung werden die ToDo-Liste und die Elemente mit Priorität angezeigt:</span><span class="sxs-lookup"><span data-stu-id="fbf70-228">The following image shows the ToDo list and the priority items:</span></span>

![ToDo-Liste und Prioritätselemente](view-components/_static/pi.png)

<span data-ttu-id="fbf70-230">Sie können Sie Ansichtskomponente auch direkt vom Controller aus aufrufen:</span><span class="sxs-lookup"><span data-stu-id="fbf70-230">You can also call the view component directly from the controller:</span></span>

[!code-csharp[](view-components/sample/ViewCompFinal/Controllers/ToDoController.cs?name=snippet_IndexVC)]

![Prioritätselemente der IndexVC-Aktion](view-components/_static/indexvc.png)

### <a name="specifying-a-view-name"></a><span data-ttu-id="fbf70-232">Angeben eines Ansichtsnamens</span><span class="sxs-lookup"><span data-stu-id="fbf70-232">Specifying a view name</span></span>

<span data-ttu-id="fbf70-233">Eine komplexe Ansichtskomponente erfordert möglicherweise, dass unter bestimmten Umständen eine Ansicht angegeben wird, die nicht dem Standard entspricht.</span><span class="sxs-lookup"><span data-stu-id="fbf70-233">A complex view component might need to specify a non-default view under some conditions.</span></span> <span data-ttu-id="fbf70-234">Der folgende Code zeigt, wie die Ansicht „PVC“ von der `InvokeAsync`-Methode aus angegeben wird.</span><span class="sxs-lookup"><span data-stu-id="fbf70-234">The following code shows how to specify the "PVC" view  from the `InvokeAsync` method.</span></span> <span data-ttu-id="fbf70-235">Aktualisieren Sie die `InvokeAsync`-Methode in der `PriorityListViewComponent`-Klasse.</span><span class="sxs-lookup"><span data-stu-id="fbf70-235">Update the `InvokeAsync` method in the `PriorityListViewComponent` class.</span></span>

[!code-csharp[](../../mvc/views/view-components/sample/ViewCompFinal/ViewComponents/PriorityListViewComponentFinal.cs?highlight=4,5,6,7,8,9&range=28-39)]

<span data-ttu-id="fbf70-236">Kopieren Sie die Datei *Views/Shared/Components/PriorityList/Default.cshtml* in eine Ansicht mit dem Namen *Views/Shared/Components/PriorityList/PVC.cshtml* .</span><span class="sxs-lookup"><span data-stu-id="fbf70-236">Copy the *Views/Shared/Components/PriorityList/Default.cshtml* file to a view named *Views/Shared/Components/PriorityList/PVC.cshtml* .</span></span> <span data-ttu-id="fbf70-237">Fügen Sie eine Überschrift hinzu, um anzugeben, dass die PVC-Ansicht verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="fbf70-237">Add a heading to indicate the PVC view is being used.</span></span>

[!code-cshtml[](../../mvc/views/view-components/sample/ViewCompFinal/Views/Shared/Components/PriorityList/PVC.cshtml?highlight=3)]

<span data-ttu-id="fbf70-238">Aktualisieren Sie *Views/ToDo/Index.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="fbf70-238">Update *Views/ToDo/Index.cshtml* :</span></span>

<!-- Views/ToDo/Index.cshtml is never imported, so change to test tutorial -->

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexFinal.cshtml?range=35)]

<span data-ttu-id="fbf70-239">Führen Sie die App aus und überprüfen Sie die PVC-Ansicht.</span><span class="sxs-lookup"><span data-stu-id="fbf70-239">Run the app and verify PVC view.</span></span>

![Ansichtskomponente mit Priorität](view-components/_static/pvc.png)

<span data-ttu-id="fbf70-241">Wenn die PVC-Ansicht nicht gerendert wird, stellen Sie sicher, dass Sie die Ansichtskomponente mit einer Priorität von 4 oder höher aufrufen.</span><span class="sxs-lookup"><span data-stu-id="fbf70-241">If the PVC view isn't rendered, verify you are calling the view component with a priority of 4 or higher.</span></span>

### <a name="examine-the-view-path"></a><span data-ttu-id="fbf70-242">Untersuchen des Ansichtspfads</span><span class="sxs-lookup"><span data-stu-id="fbf70-242">Examine the view path</span></span>

* <span data-ttu-id="fbf70-243">Ändern Sie den Prioritätsparameter in drei oder weniger, damit die Prioritätsansicht nicht zurückgegeben wird.</span><span class="sxs-lookup"><span data-stu-id="fbf70-243">Change the priority parameter to three or less so the priority view isn't returned.</span></span>
* <span data-ttu-id="fbf70-244">Benennen Sie *Views/ToDo/Components/PriorityList/Default.cshtml* vorübergehend in *1Default.cshtml* um.</span><span class="sxs-lookup"><span data-stu-id="fbf70-244">Temporarily rename the *Views/ToDo/Components/PriorityList/Default.cshtml* to *1Default.cshtml* .</span></span>
* <span data-ttu-id="fbf70-245">Wenn Sie die App testen, erhalten Sie die folgende Fehlermeldung:</span><span class="sxs-lookup"><span data-stu-id="fbf70-245">Test the app, you'll get the following error:</span></span>

   ```
   An unhandled exception occurred while processing the request.
   InvalidOperationException: The view 'Components/PriorityList/Default' wasn't found. The following locations were searched:
   /Views/ToDo/Components/PriorityList/Default.cshtml
   /Views/Shared/Components/PriorityList/Default.cshtml
   EnsureSuccessful
   ```

* <span data-ttu-id="fbf70-246">Kopieren Sie *Views/ToDo/Components/PriorityList/1Default.cshtml* nach *Views/Shared/Components/PriorityList/Default.cshtml* .</span><span class="sxs-lookup"><span data-stu-id="fbf70-246">Copy *Views/ToDo/Components/PriorityList/1Default.cshtml* to *Views/Shared/Components/PriorityList/Default.cshtml* .</span></span>
* <span data-ttu-id="fbf70-247">Fügen Sie der ToDo-Ansichtskomponentenansicht *Shared* (Freigegeben) Markup hinzu, um anzugeben, dass die Ansicht aus dem Ordner *Shared* stammt.</span><span class="sxs-lookup"><span data-stu-id="fbf70-247">Add some markup to the *Shared* ToDo view component view to indicate the view is from the *Shared* folder.</span></span>
* <span data-ttu-id="fbf70-248">Testen Sie die Komponentenansicht **Shared** (Freigegeben).</span><span class="sxs-lookup"><span data-stu-id="fbf70-248">Test the **Shared** component view.</span></span>

![ToDo-Ausgabe mit Komponentenansicht „Shared“](view-components/_static/shared.png)

### <a name="avoiding-hard-coded-strings"></a><span data-ttu-id="fbf70-250">Vermeiden hart codierter Zeichenfolgen</span><span class="sxs-lookup"><span data-stu-id="fbf70-250">Avoiding hard-coded strings</span></span>

<span data-ttu-id="fbf70-251">Wenn Sie Sicherheit zu Kompilierzeit haben möchten, können Sie den hart codierten Komponentennamen durch den Klassennamen ersetzen.</span><span class="sxs-lookup"><span data-stu-id="fbf70-251">If you want compile time safety, you can replace the hard-coded view component name with the class name.</span></span> <span data-ttu-id="fbf70-252">Erstellen Sie die Ansichtskomponente ohne den Suffix „ViewComponent“:</span><span class="sxs-lookup"><span data-stu-id="fbf70-252">Create the view component without the "ViewComponent" suffix:</span></span>

[!code-csharp[](../../mvc/views/view-components/sample/ViewCompFinal/ViewComponents/PriorityList.cs?highlight=10&range=5-35)]

<span data-ttu-id="fbf70-253">Fügen Sie `using` :::no-loc(Razor)::: der Ansichts Datei eine-Anweisung hinzu, und verwenden Sie den- `nameof` Operator:</span><span class="sxs-lookup"><span data-stu-id="fbf70-253">Add a `using` statement to your :::no-loc(Razor)::: view file, and use the `nameof` operator:</span></span>

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexNameof.cshtml?range=1-6,35-)]

## <a name="perform-synchronous-work"></a><span data-ttu-id="fbf70-254">Ausführen synchroner Verarbeitung</span><span class="sxs-lookup"><span data-stu-id="fbf70-254">Perform synchronous work</span></span>

<span data-ttu-id="fbf70-255">Das Framework verarbeitet den Aufruf einer synchronen `Invoke`-Methode, wenn Sie keine asynchrone Verarbeitung durchführen müssen.</span><span class="sxs-lookup"><span data-stu-id="fbf70-255">The framework handles invoking a synchronous `Invoke` method if you don't need to perform asynchronous work.</span></span> <span data-ttu-id="fbf70-256">Die folgende Methode erstellt eine synchrone `Invoke`-Ansichtskomponente:</span><span class="sxs-lookup"><span data-stu-id="fbf70-256">The following method creates a synchronous `Invoke` view component:</span></span>

```csharp
public class PriorityList : ViewComponent
{
    public IViewComponentResult Invoke(int maxPriority, bool isDone)
    {
        var items = new List<string> { $"maxPriority: {maxPriority}", $"isDone: {isDone}" };
        return View(items);
    }
}
```

<span data-ttu-id="fbf70-257">Die Datei der Ansichts Komponente :::no-loc(Razor)::: Listet die an die Methode übergebenen Zeichen folgen auf `Invoke` ( *views/Home/Components/prioritylist/default. cshtml* ):</span><span class="sxs-lookup"><span data-stu-id="fbf70-257">The view component's :::no-loc(Razor)::: file lists the strings passed to the `Invoke` method ( *Views/Home/Components/PriorityList/Default.cshtml* ):</span></span>

```cshtml
@model List<string>

<h3>Priority Items</h3>
<ul>
    @foreach (var item in Model)
    {
        <li>@item</li>
    }
</ul>
```

::: moniker range=">= aspnetcore-1.1"

<span data-ttu-id="fbf70-258">Die Ansichts Komponente wird in einer :::no-loc(Razor)::: Datei (z. b. *views/Home/Index. cshtml* ) mit einem der folgenden Ansätze aufgerufen:</span><span class="sxs-lookup"><span data-stu-id="fbf70-258">The view component is invoked in a :::no-loc(Razor)::: file (for example, *Views/Home/Index.cshtml* ) using one of the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.IViewComponentHelper>
* [<span data-ttu-id="fbf70-259">Taghilfsprogramm</span><span class="sxs-lookup"><span data-stu-id="fbf70-259">Tag Helper</span></span>](xref:mvc/views/tag-helpers/intro)

<span data-ttu-id="fbf70-260">Rufen Sie `Component.InvokeAsync` auf, um das <xref:Microsoft.AspNetCore.Mvc.IViewComponentHelper>-Verfahren zu verwenden:</span><span class="sxs-lookup"><span data-stu-id="fbf70-260">To use the <xref:Microsoft.AspNetCore.Mvc.IViewComponentHelper> approach, call `Component.InvokeAsync`:</span></span>

::: moniker-end

::: moniker range="< aspnetcore-1.1"

<span data-ttu-id="fbf70-261">Die Ansichts Komponente wird in einer :::no-loc(Razor)::: Datei (z. b. *views/Home/Index. cshtml* ) mit aufgerufen <xref:Microsoft.AspNetCore.Mvc.IViewComponentHelper> .</span><span class="sxs-lookup"><span data-stu-id="fbf70-261">The view component is invoked in a :::no-loc(Razor)::: file (for example, *Views/Home/Index.cshtml* ) with <xref:Microsoft.AspNetCore.Mvc.IViewComponentHelper>.</span></span>

<span data-ttu-id="fbf70-262">Rufen Sie `Component.InvokeAsync` auf:</span><span class="sxs-lookup"><span data-stu-id="fbf70-262">Call `Component.InvokeAsync`:</span></span>

::: moniker-end

```cshtml
@await Component.InvokeAsync(nameof(PriorityList), new { maxPriority = 4, isDone = true })
```

::: moniker range=">= aspnetcore-1.1"

<span data-ttu-id="fbf70-263">Um das Taghilfsprogramm zu verwenden, registrieren Sie die Assembly, die die Ansichtskomponente enthält, mit der Anweisung `@addTagHelper` (die Ansichtskomponente befindet sich in einer Assembly namens `MyWebApp`):</span><span class="sxs-lookup"><span data-stu-id="fbf70-263">To use the Tag Helper, register the assembly containing the View Component using the `@addTagHelper` directive (the view component is in an assembly called `MyWebApp`):</span></span>

```cshtml
@addTagHelper *, MyWebApp
```

<span data-ttu-id="fbf70-264">Verwenden Sie das taghilfsprogramm für die Ansichts Komponente in der :::no-loc(Razor)::: Markup Datei:</span><span class="sxs-lookup"><span data-stu-id="fbf70-264">Use the view component Tag Helper in the :::no-loc(Razor)::: markup file:</span></span>

```cshtml
<vc:priority-list max-priority="999" is-done="false">
</vc:priority-list>
```

::: moniker-end

<span data-ttu-id="fbf70-265">Die Methoden Signatur von `PriorityList.Invoke` ist synchron, :::no-loc(Razor)::: findet jedoch die-Methode `Component.InvokeAsync` in der Markup Datei und ruft Sie auf.</span><span class="sxs-lookup"><span data-stu-id="fbf70-265">The method signature of `PriorityList.Invoke` is synchronous, but :::no-loc(Razor)::: finds and calls the method with `Component.InvokeAsync` in the markup file.</span></span>

## <a name="all-view-component-parameters-are-required"></a><span data-ttu-id="fbf70-266">Alle Parameter einer Ansichtskomponente sind erforderlich</span><span class="sxs-lookup"><span data-stu-id="fbf70-266">All view component parameters are required</span></span>

<span data-ttu-id="fbf70-267">Jeder Parameter in einer Ansichtskomponente ist ein erforderliches Attribut.</span><span class="sxs-lookup"><span data-stu-id="fbf70-267">Each parameter in a view component is a required attribute.</span></span> <span data-ttu-id="fbf70-268">Weitere Informationen finden Sie im entsprechenden [GitHub-Issue](https://github.com/dotnet/AspNetCore/issues/5011).</span><span class="sxs-lookup"><span data-stu-id="fbf70-268">See [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/5011).</span></span> <span data-ttu-id="fbf70-269">Wenn ein Parameter ausgelassen wird, geschieht Folgendes:</span><span class="sxs-lookup"><span data-stu-id="fbf70-269">If any  parameter is omitted:</span></span>

* <span data-ttu-id="fbf70-270">Für die Signatur der `InvokeAsync`-Methode kann keine Übereinstimmung ermittelt werden. Die Methode wird daher nicht ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="fbf70-270">The `InvokeAsync` method signature won't match, therefore the method won't execute.</span></span>
* <span data-ttu-id="fbf70-271">Das Markup des ViewComponent-Elements wird nicht gerendert.</span><span class="sxs-lookup"><span data-stu-id="fbf70-271">The ViewComponent won't render any markup.</span></span>
* <span data-ttu-id="fbf70-272">Es werden keine Fehler ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="fbf70-272">No errors will be thrown.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="fbf70-273">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="fbf70-273">Additional resources</span></span>

* [<span data-ttu-id="fbf70-274">Dependency Injection in Ansichten</span><span class="sxs-lookup"><span data-stu-id="fbf70-274">Dependency injection into views</span></span>](xref:mvc/views/dependency-injection)
