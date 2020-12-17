---
title: ASP.NET Core Blazor-Lebenszyklus
author: guardrex
description: Erfahren Sie, wie die Lebenszyklusmethoden von Razor-Komponenten in ASP.NET Core Blazor-Apps verwendet werden.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/06/2020
no-loc:
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
uid: blazor/components/lifecycle
ms.openlocfilehash: b01b1c70be010ba0ad9bbd2c1114e5d8341b3261
ms.sourcegitcommit: 6b87f2e064cea02e65dacd206394b44f5c604282
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/15/2020
ms.locfileid: "97506863"
---
# <a name="aspnet-core-no-locblazor-lifecycle"></a><span data-ttu-id="39a30-103">ASP.NET Core Blazor-Lebenszyklus</span><span class="sxs-lookup"><span data-stu-id="39a30-103">ASP.NET Core Blazor lifecycle</span></span>

<span data-ttu-id="39a30-104">Von [Luke Latham](https://github.com/guardrex) und [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="39a30-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="39a30-105">Das Blazor-Framework umfasst synchrone und asynchrone Lebenszyklusmethoden.</span><span class="sxs-lookup"><span data-stu-id="39a30-105">The Blazor framework includes synchronous and asynchronous lifecycle methods.</span></span> <span data-ttu-id="39a30-106">Setzen Sie Lebenszyklusmethoden außer Kraft, um während der Komponenteninitialisierung und des Renderings zusätzliche Vorgänge mit Komponenten durchzuführen.</span><span class="sxs-lookup"><span data-stu-id="39a30-106">Override lifecycle methods to perform additional operations on components during component initialization and rendering.</span></span>

<span data-ttu-id="39a30-107">Auf den folgenden Abbildungen wird der Blazor-Lebenszyklus veranschaulicht.</span><span class="sxs-lookup"><span data-stu-id="39a30-107">The following diagrams illustrate the Blazor lifecycle.</span></span> <span data-ttu-id="39a30-108">In den folgenden Abschnitten dieses Artikels werden die Lebenszyklusmethoden mit Beispielen definiert.</span><span class="sxs-lookup"><span data-stu-id="39a30-108">Lifecycle methods are defined with examples in the following sections of this article.</span></span>

<span data-ttu-id="39a30-109">Ereignisse des Komponentenlebenszyklus:</span><span class="sxs-lookup"><span data-stu-id="39a30-109">Component lifecycle events:</span></span>

1. <span data-ttu-id="39a30-110">Wenn die Komponente erstmalig ein Rendering für eine Anforderung ausführt, führen Sie folgende Schritte durch:</span><span class="sxs-lookup"><span data-stu-id="39a30-110">If the component is rendering for the first time on a request:</span></span>
   * <span data-ttu-id="39a30-111">Erstellen Sie die Instanz der Komponente.</span><span class="sxs-lookup"><span data-stu-id="39a30-111">Create the component's instance.</span></span>
   * <span data-ttu-id="39a30-112">Führen Sie eine Eigenschaftsinjektion durch.</span><span class="sxs-lookup"><span data-stu-id="39a30-112">Perform property injection.</span></span> <span data-ttu-id="39a30-113">Führen Sie [`SetParametersAsync`](#before-parameters-are-set) aus.</span><span class="sxs-lookup"><span data-stu-id="39a30-113">Run [`SetParametersAsync`](#before-parameters-are-set).</span></span>
   * <span data-ttu-id="39a30-114">Rufen Sie [`OnInitialized{Async}`](#component-initialization-methods) auf.</span><span class="sxs-lookup"><span data-stu-id="39a30-114">Call [`OnInitialized{Async}`](#component-initialization-methods).</span></span> <span data-ttu-id="39a30-115">Wenn <xref:System.Threading.Tasks.Task> zurückgegeben wird, wird auf <xref:System.Threading.Tasks.Task> gewartet, und die Komponente wird gerendert.</span><span class="sxs-lookup"><span data-stu-id="39a30-115">If a <xref:System.Threading.Tasks.Task> is returned, the <xref:System.Threading.Tasks.Task> is awaited and the component is rendered.</span></span> <span data-ttu-id="39a30-116">Wenn <xref:System.Threading.Tasks.Task> nicht zurückgegeben wird, wird die Komponente gerendert.</span><span class="sxs-lookup"><span data-stu-id="39a30-116">If a <xref:System.Threading.Tasks.Task> isn't returned, the component is rendered.</span></span>
1. <span data-ttu-id="39a30-117">Rufen Sie [`OnParametersSet{Async}`](#after-parameters-are-set) auf, und rendern Sie die Komponente.</span><span class="sxs-lookup"><span data-stu-id="39a30-117">Call [`OnParametersSet{Async}`](#after-parameters-are-set) and render the component.</span></span> <span data-ttu-id="39a30-118">Wenn <xref:System.Threading.Tasks.Task> aus `OnParametersSetAsync` zurückgegeben wird, wird auf <xref:System.Threading.Tasks.Task> gewartet. Anschließend wird die Komponente erneut gerendert.</span><span class="sxs-lookup"><span data-stu-id="39a30-118">If a <xref:System.Threading.Tasks.Task> is returned from `OnParametersSetAsync`, the <xref:System.Threading.Tasks.Task> is awaited and then the component is rerendered.</span></span>

![Lebenszyklusereignisse einer Razor-Komponente in Blazor](lifecycle/_static/lifecycle1.png)

<span data-ttu-id="39a30-120">Verarbeitung von DOM-Ereignissen (Document Object Model):</span><span class="sxs-lookup"><span data-stu-id="39a30-120">Document Object Model (DOM) event processing:</span></span>

1. <span data-ttu-id="39a30-121">Der Ereignishandler wird ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="39a30-121">The event handler is run.</span></span>
1. <span data-ttu-id="39a30-122">Wenn <xref:System.Threading.Tasks.Task> zurückgegeben wird, wird auf <xref:System.Threading.Tasks.Task> gewartet, und dann wird die Komponente gerendert.</span><span class="sxs-lookup"><span data-stu-id="39a30-122">If a <xref:System.Threading.Tasks.Task> is returned, the <xref:System.Threading.Tasks.Task> is awaited and then the component is rendered.</span></span> <span data-ttu-id="39a30-123">Wenn <xref:System.Threading.Tasks.Task> nicht zurückgegeben wird, wird die Komponente gerendert.</span><span class="sxs-lookup"><span data-stu-id="39a30-123">If a <xref:System.Threading.Tasks.Task> isn't returned, the component is rendered.</span></span>

![Verarbeitung von DOM-Ereignissen (Document Object Model)](lifecycle/_static/lifecycle2.png)

<span data-ttu-id="39a30-125">Der `Render`-Lebenszyklus:</span><span class="sxs-lookup"><span data-stu-id="39a30-125">The `Render` lifecycle:</span></span>

1. <span data-ttu-id="39a30-126">Wenn es sich beim Renderingvorgang nicht um das erste Rendering der Komponente handelt oder wenn [`ShouldRender`](#suppress-ui-refreshing) als `false` ausgewertet wird, führen Sie keine weiteren Vorgänge für die Komponente aus.</span><span class="sxs-lookup"><span data-stu-id="39a30-126">If this isn't the component's first render or [`ShouldRender`](#suppress-ui-refreshing) is evaluated as `false`, don't perform further operations on the component.</span></span>
1. <span data-ttu-id="39a30-127">Erstellen Sie das Diff (Unterschied) der Renderstruktur, und rendern Sie die Komponente.</span><span class="sxs-lookup"><span data-stu-id="39a30-127">Build the render tree diff (difference) and render the component.</span></span>
1. <span data-ttu-id="39a30-128">Warten Sie, bis das DOM aktualisiert wurde.</span><span class="sxs-lookup"><span data-stu-id="39a30-128">Await the DOM to update.</span></span>
1. <span data-ttu-id="39a30-129">Rufen Sie [`OnAfterRender{Async}`](#after-component-render) auf.</span><span class="sxs-lookup"><span data-stu-id="39a30-129">Call [`OnAfterRender{Async}`](#after-component-render).</span></span>

![Lebenszyklus von Rendervorgängen](lifecycle/_static/lifecycle3.png)

<span data-ttu-id="39a30-131">Wenn [`StateHasChanged`](#state-changes) von Entwicklern aufgerufen wird, führt dies zu einem Rendervorgang.</span><span class="sxs-lookup"><span data-stu-id="39a30-131">Developer calls to [`StateHasChanged`](#state-changes) result in a render.</span></span>

## <a name="lifecycle-methods"></a><span data-ttu-id="39a30-132">Lebenszyklusmethoden</span><span class="sxs-lookup"><span data-stu-id="39a30-132">Lifecycle methods</span></span>

### <a name="before-parameters-are-set"></a><span data-ttu-id="39a30-133">Bevor die Parameter festgelegt werden</span><span class="sxs-lookup"><span data-stu-id="39a30-133">Before parameters are set</span></span>

<span data-ttu-id="39a30-134"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> legt Parameter fest, die vom übergeordneten Element der Komponente in der Renderstruktur bereitgestellt werden:</span><span class="sxs-lookup"><span data-stu-id="39a30-134"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> sets parameters supplied by the component's parent in the render tree:</span></span>

```csharp
public override async Task SetParametersAsync(ParameterView parameters)
{
    await ...

    await base.SetParametersAsync(parameters);
}
```

<span data-ttu-id="39a30-135"><xref:Microsoft.AspNetCore.Components.ParameterView> enthält die Parameterwerte für die Komponente für jeden Aufruf von <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A>.</span><span class="sxs-lookup"><span data-stu-id="39a30-135"><xref:Microsoft.AspNetCore.Components.ParameterView> contains the set of parameter values for the component each time <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> is called.</span></span>

<span data-ttu-id="39a30-136">Die Standardimplementierung von <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> legt den Wert der einzelnen Eigenschaften mit dem Attribut [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) oder [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) fest, die einen entsprechenden Wert in <xref:Microsoft.AspNetCore.Components.ParameterView> aufweist.</span><span class="sxs-lookup"><span data-stu-id="39a30-136">The default implementation of <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> sets the value of each property with the [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) or [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) attribute that has a corresponding value in the <xref:Microsoft.AspNetCore.Components.ParameterView>.</span></span> <span data-ttu-id="39a30-137">Parameter, die keinen entsprechenden Wert in <xref:Microsoft.AspNetCore.Components.ParameterView> haben, bleiben unverändert.</span><span class="sxs-lookup"><span data-stu-id="39a30-137">Parameters that don't have a corresponding value in <xref:Microsoft.AspNetCore.Components.ParameterView> are left unchanged.</span></span>

<span data-ttu-id="39a30-138">Wenn [`base.SetParametersAsync`](xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A) nicht aufgerufen wird, kann der benutzerdefinierte Code den eingehenden Parameterwert in jeder gewünschten Weise interpretieren.</span><span class="sxs-lookup"><span data-stu-id="39a30-138">If [`base.SetParametersAsync`](xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A) isn't invoked, the custom code can interpret the incoming parameters value in any way required.</span></span> <span data-ttu-id="39a30-139">Beispielsweise ist es nicht erforderlich, die eingehenden Parameter den Eigenschaften der Klasse zuzuordnen.</span><span class="sxs-lookup"><span data-stu-id="39a30-139">For example, there's no requirement to assign the incoming parameters to the properties on the class.</span></span>

<span data-ttu-id="39a30-140">Wenn Ereignishandler eingerichtet wurden, sollten Sie ihre Einbindung bei der Beseitigung aufheben.</span><span class="sxs-lookup"><span data-stu-id="39a30-140">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="39a30-141">Weitere Informationen finden Sie im Abschnitt [Beseitigung von Komponenten mit `IDisposable`](#component-disposal-with-idisposable).</span><span class="sxs-lookup"><span data-stu-id="39a30-141">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="component-initialization-methods"></a><span data-ttu-id="39a30-142">Methoden zur Komponenteninitialisierung</span><span class="sxs-lookup"><span data-stu-id="39a30-142">Component initialization methods</span></span>

<span data-ttu-id="39a30-143"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> und <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> werden aufgerufen, wenn die Komponente initialisiert wird, nachdem sie ihre anfänglichen Parameter von ihrer übergeordneten Komponente in <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> erhalten hat.</span><span class="sxs-lookup"><span data-stu-id="39a30-143"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> are invoked when the component is initialized after having received its initial parameters from its parent component in <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A>.</span></span> 

<span data-ttu-id="39a30-144">Verwenden Sie <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>, wenn die Komponente einen asynchronen Vorgang ausführt und nach Abschluss des Vorgangs aktualisiert werden soll.</span><span class="sxs-lookup"><span data-stu-id="39a30-144">Use <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> when the component performs an asynchronous operation and should refresh when the operation is completed.</span></span>

<span data-ttu-id="39a30-145">Für einen synchronen Betrieb setzen Sie <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> außer Kraft:</span><span class="sxs-lookup"><span data-stu-id="39a30-145">For a synchronous operation, override <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>:</span></span>

```csharp
protected override void OnInitialized()
{
    ...
}
```

<span data-ttu-id="39a30-146">Überschreiben Sie <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>, und verwenden Sie den Operator [`await`](/dotnet/csharp/language-reference/operators/await) für den Vorgang, um einen asynchronen Vorgang durchzuführen:</span><span class="sxs-lookup"><span data-stu-id="39a30-146">To perform an asynchronous operation, override <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> and use the [`await`](/dotnet/csharp/language-reference/operators/await) operator on the operation:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

<span data-ttu-id="39a30-147">Blazor Server-Apps, die [ihren Inhalt vorab rendern](xref:blazor/fundamentals/additional-scenarios#render-mode) rufen <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> *zweimal* auf:</span><span class="sxs-lookup"><span data-stu-id="39a30-147">Blazor Server apps that [prerender their content](xref:blazor/fundamentals/additional-scenarios#render-mode) call <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> *twice*:</span></span>

* <span data-ttu-id="39a30-148">Einmal, wenn die Komponente anfänglich statisch als Teil der Seite gerendert wird.</span><span class="sxs-lookup"><span data-stu-id="39a30-148">Once when the component is initially rendered statically as part of the page.</span></span>
* <span data-ttu-id="39a30-149">Ein zweites Mal, wenn der Browser eine Verbindung mit dem Server herstellt.</span><span class="sxs-lookup"><span data-stu-id="39a30-149">A second time when the browser establishes a connection back to the server.</span></span>

<span data-ttu-id="39a30-150">Informationen zum Verhindern, dass Entwicklercode in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> zweimal ausgeführt wird, finden Sie im Abschnitt [Zustandsbehaftete erneute Verbindung nach dem Prerendering](#stateful-reconnection-after-prerendering).</span><span class="sxs-lookup"><span data-stu-id="39a30-150">To prevent developer code in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> from running twice, see the [Stateful reconnection after prerendering](#stateful-reconnection-after-prerendering) section.</span></span>

<span data-ttu-id="39a30-151">Während für eine Blazor Server-App ein Prerendering durchgeführt wird, sind bestimmte Aktionen (z. B. Aufrufe in JavaScript) nicht möglich, da noch keine Verbindung mit dem Browser hergestellt wurde.</span><span class="sxs-lookup"><span data-stu-id="39a30-151">While a Blazor Server app is prerendering, certain actions, such as calling into JavaScript, aren't possible because a connection with the browser hasn't been established.</span></span> <span data-ttu-id="39a30-152">Komponenten müssen wahrscheinlich unterschiedlich rendern, wenn dafür ein Prerendering durchgeführt wurde.</span><span class="sxs-lookup"><span data-stu-id="39a30-152">Components may need to render differently when prerendered.</span></span> <span data-ttu-id="39a30-153">Weitere Informationen finden Sie im Abschnitt [Ermitteln des Prerenderings einer App](#detect-when-the-app-is-prerendering).</span><span class="sxs-lookup"><span data-stu-id="39a30-153">For more information, see the [Detect when the app is prerendering](#detect-when-the-app-is-prerendering) section.</span></span>

<span data-ttu-id="39a30-154">Wenn Ereignishandler eingerichtet wurden, sollten Sie ihre Einbindung bei der Beseitigung aufheben.</span><span class="sxs-lookup"><span data-stu-id="39a30-154">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="39a30-155">Weitere Informationen finden Sie im Abschnitt [Beseitigung von Komponenten mit `IDisposable`](#component-disposal-with-idisposable).</span><span class="sxs-lookup"><span data-stu-id="39a30-155">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="after-parameters-are-set"></a><span data-ttu-id="39a30-156">Nachdem die Parameter festgelegt wurden</span><span class="sxs-lookup"><span data-stu-id="39a30-156">After parameters are set</span></span>

<span data-ttu-id="39a30-157"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> oder <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A> wird aufgerufen:</span><span class="sxs-lookup"><span data-stu-id="39a30-157"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> or <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A> are called:</span></span>

* <span data-ttu-id="39a30-158">Nachdem die Komponente in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> oder <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> initialisiert wurde.</span><span class="sxs-lookup"><span data-stu-id="39a30-158">After the component is initialized in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> or <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span>
* <span data-ttu-id="39a30-159">Wenn die übergeordnete Komponente neu gerendert wird und Folgendes bereitstellt:</span><span class="sxs-lookup"><span data-stu-id="39a30-159">When the parent component re-renders and supplies:</span></span>
  * <span data-ttu-id="39a30-160">Nur bekannte einfache, unveränderliche Typen, von denen sich mindestens ein Parameter geändert hat.</span><span class="sxs-lookup"><span data-stu-id="39a30-160">Only known primitive immutable types of which at least one parameter has changed.</span></span>
  * <span data-ttu-id="39a30-161">Beliebige Parameter mit komplexem Typ.</span><span class="sxs-lookup"><span data-stu-id="39a30-161">Any complex-typed parameters.</span></span> <span data-ttu-id="39a30-162">Das Framework kann nicht wissen, ob die Werte eines Parameters mit komplexem Typ intern mutiert sind, also behandelt es den Parametersatz als geändert.</span><span class="sxs-lookup"><span data-stu-id="39a30-162">The framework can't know whether the values of a complex-typed parameter have mutated internally, so it treats the parameter set as changed.</span></span>

```csharp
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

> [!NOTE]
> <span data-ttu-id="39a30-163">Die asynchrone Arbeit bei der Anwendung von Parametern und Eigenschaftswerten muss während des <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A>-Lebenszyklusereignisses erfolgen.</span><span class="sxs-lookup"><span data-stu-id="39a30-163">Asynchronous work when applying parameters and property values must occur during the <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> lifecycle event.</span></span>

```csharp
protected override void OnParametersSet()
{
    ...
}
```

<span data-ttu-id="39a30-164">Wenn Ereignishandler eingerichtet wurden, sollten Sie ihre Einbindung bei der Beseitigung aufheben.</span><span class="sxs-lookup"><span data-stu-id="39a30-164">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="39a30-165">Weitere Informationen finden Sie im Abschnitt [Beseitigung von Komponenten mit `IDisposable`](#component-disposal-with-idisposable).</span><span class="sxs-lookup"><span data-stu-id="39a30-165">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="after-component-render"></a><span data-ttu-id="39a30-166">Nach dem Rendern der Komponente</span><span class="sxs-lookup"><span data-stu-id="39a30-166">After component render</span></span>

<span data-ttu-id="39a30-167"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> und <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> werden aufgerufen, nachdem eine Komponente das Rendering beendet hat.</span><span class="sxs-lookup"><span data-stu-id="39a30-167"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> are called after a component has finished rendering.</span></span> <span data-ttu-id="39a30-168">Element- und Komponentenverweise werden an dieser Stelle aufgefüllt.</span><span class="sxs-lookup"><span data-stu-id="39a30-168">Element and component references are populated at this point.</span></span> <span data-ttu-id="39a30-169">In dieser Phase können Sie zusätzliche Initialisierungsschritte unter Verwendung des gerenderten Inhalts durchführen, z. B. die Aktivierung von JavaScript-Bibliotheken von Drittanbietern, die mit den gerenderten DOM-Elementen arbeiten.</span><span class="sxs-lookup"><span data-stu-id="39a30-169">Use this stage to perform additional initialization steps using the rendered content, such as activating third-party JavaScript libraries that operate on the rendered DOM elements.</span></span>

<span data-ttu-id="39a30-170">Der Parameter `firstRender` für <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> und <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A>:</span><span class="sxs-lookup"><span data-stu-id="39a30-170">The `firstRender` parameter for <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A>:</span></span>

* <span data-ttu-id="39a30-171">Wird auf `true` festgelegt, wenn die Komponenteninstanz zum ersten Mal gerendert wird.</span><span class="sxs-lookup"><span data-stu-id="39a30-171">Is set to `true` the first time that the component instance is rendered.</span></span>
* <span data-ttu-id="39a30-172">Kann verwendet werden, um sicherzustellen, dass die Initialisierung nur einmal durchgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="39a30-172">Can be used to ensure that initialization work is only performed once.</span></span>

```csharp
protected override async Task OnAfterRenderAsync(bool firstRender)
{
    if (firstRender)
    {
        await ...
    }
}
```

> [!NOTE]
> <span data-ttu-id="39a30-173">Die asynchrone Arbeit unmittelbar nach dem Rendering muss während des <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>-Lebenszyklusereignisses erfolgen.</span><span class="sxs-lookup"><span data-stu-id="39a30-173">Asynchronous work immediately after rendering must occur during the <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> lifecycle event.</span></span>
>
> <span data-ttu-id="39a30-174">Selbst wenn Sie ein <xref:System.Threading.Tasks.Task> von <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> zurückgeben, plant das Framework keinen weiteren Renderzyklus für Ihre Komponente ein, sobald diese Aufgabe abgeschlossen ist.</span><span class="sxs-lookup"><span data-stu-id="39a30-174">Even if you return a <xref:System.Threading.Tasks.Task> from <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>, the framework doesn't schedule a further render cycle for your component once that task completes.</span></span> <span data-ttu-id="39a30-175">Damit soll eine unendliche Renderschleife vermieden werden.</span><span class="sxs-lookup"><span data-stu-id="39a30-175">This is to avoid an infinite render loop.</span></span> <span data-ttu-id="39a30-176">Dies unterscheidet sich von den anderen Lebenszyklusmethoden, die einen weiteren Renderzyklus planen, sobald die zurückgegebene Aufgabe abgeschlossen ist.</span><span class="sxs-lookup"><span data-stu-id="39a30-176">It's different from the other lifecycle methods, which schedule a further render cycle once the returned task completes.</span></span>

```csharp
protected override void OnAfterRender(bool firstRender)
{
    if (firstRender)
    {
        ...
    }
}
```

<span data-ttu-id="39a30-177"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> und <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> *werden beim Vorabrendern auf dem Server nicht aufgerufen*.</span><span class="sxs-lookup"><span data-stu-id="39a30-177"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> *aren't called during the prerendering process on the server*.</span></span> <span data-ttu-id="39a30-178">Die Methoden werden aufgerufen, wenn die Komponente interaktiv gerendert wird, nachdem das Vorabrendern abgeschlossen wurde.</span><span class="sxs-lookup"><span data-stu-id="39a30-178">The methods are called when the component is rendered interactively after prerendering is finished.</span></span> <span data-ttu-id="39a30-179">Beim Vorabrendern der App:</span><span class="sxs-lookup"><span data-stu-id="39a30-179">When the app prerenders:</span></span>

1. <span data-ttu-id="39a30-180">Die Komponente wird auf dem Server ausgeführt, um in der HTTP-Antwort statisches HTML-Markup zu generieren.</span><span class="sxs-lookup"><span data-stu-id="39a30-180">The component executes on the server to produce some static HTML markup in the HTTP response.</span></span> <span data-ttu-id="39a30-181">Während dieser Phase werden <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> und <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> nicht aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="39a30-181">During this phase, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> aren't called.</span></span>
1. <span data-ttu-id="39a30-182">Wenn `blazor.server.js` oder `blazor.webassembly.js` im Browser gestartet werden, wird die Komponente in einem interaktiven Renderingmodus neu gestartet.</span><span class="sxs-lookup"><span data-stu-id="39a30-182">When `blazor.server.js` or `blazor.webassembly.js` start up in the browser, the component is restarted in an interactive rendering mode.</span></span> <span data-ttu-id="39a30-183">Nachdem eine Komponente neu gestartet wurde, **werden** <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> und <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> aufgerufen, da sich die App nicht mehr in der Phase des Vorabrenderns befindet.</span><span class="sxs-lookup"><span data-stu-id="39a30-183">After a component is restarted, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> **are** called because the app isn't inside the prerendering phase any longer.</span></span>

<span data-ttu-id="39a30-184">Wenn Ereignishandler eingerichtet wurden, sollten Sie ihre Einbindung bei der Beseitigung aufheben.</span><span class="sxs-lookup"><span data-stu-id="39a30-184">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="39a30-185">Weitere Informationen finden Sie im Abschnitt [Beseitigung von Komponenten mit `IDisposable`](#component-disposal-with-idisposable).</span><span class="sxs-lookup"><span data-stu-id="39a30-185">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="suppress-ui-refreshing"></a><span data-ttu-id="39a30-186">Unterdrücken der UI-Aktualisierung</span><span class="sxs-lookup"><span data-stu-id="39a30-186">Suppress UI refreshing</span></span>

<span data-ttu-id="39a30-187">Setzen Sie <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> außer Kraft, um die Aktualisierung der Benutzeroberfläche zu unterdrücken.</span><span class="sxs-lookup"><span data-stu-id="39a30-187">Override <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> to suppress UI refreshing.</span></span> <span data-ttu-id="39a30-188">Wenn die Implementierung `true` zurückgibt, wird die Benutzeroberfläche aktualisiert:</span><span class="sxs-lookup"><span data-stu-id="39a30-188">If the implementation returns `true`, the UI is refreshed:</span></span>

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

<span data-ttu-id="39a30-189"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> wird jedes Mal aufgerufen, wenn die Komponente gerendert wird.</span><span class="sxs-lookup"><span data-stu-id="39a30-189"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> is called each time the component is rendered.</span></span>

<span data-ttu-id="39a30-190">Selbst wenn <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> außer Kraft gesetzt wird, wird die Komponente immer anfänglich gerendert.</span><span class="sxs-lookup"><span data-stu-id="39a30-190">Even if <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> is overridden, the component is always initially rendered.</span></span>

<span data-ttu-id="39a30-191">Weitere Informationen finden Sie unter <xref:blazor/webassembly-performance-best-practices#avoid-unnecessary-rendering-of-component-subtrees>.</span><span class="sxs-lookup"><span data-stu-id="39a30-191">For more information, see <xref:blazor/webassembly-performance-best-practices#avoid-unnecessary-rendering-of-component-subtrees>.</span></span>

## <a name="state-changes"></a><span data-ttu-id="39a30-192">Statusänderungen</span><span class="sxs-lookup"><span data-stu-id="39a30-192">State changes</span></span>

<span data-ttu-id="39a30-193"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> benachrichtigt die Komponente, dass sich ihr Zustand geändert hat.</span><span class="sxs-lookup"><span data-stu-id="39a30-193"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> notifies the component that its state has changed.</span></span> <span data-ttu-id="39a30-194">Gegebenenfalls bewirkt der Aufruf von <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> das erneute Rendern der Komponente.</span><span class="sxs-lookup"><span data-stu-id="39a30-194">When applicable, calling <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> causes the component to be rerendered.</span></span>

<span data-ttu-id="39a30-195"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> wird für <xref:Microsoft.AspNetCore.Components.EventCallback>-Methoden automatisch aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="39a30-195"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called automatically for <xref:Microsoft.AspNetCore.Components.EventCallback> methods.</span></span> <span data-ttu-id="39a30-196">Weitere Informationen finden Sie unter <xref:blazor/components/event-handling#eventcallback>.</span><span class="sxs-lookup"><span data-stu-id="39a30-196">For more information, see <xref:blazor/components/event-handling#eventcallback>.</span></span>

## <a name="handle-incomplete-async-actions-at-render"></a><span data-ttu-id="39a30-197">Behandeln unvollständiger asynchroner Aktionen beim Rendern</span><span class="sxs-lookup"><span data-stu-id="39a30-197">Handle incomplete async actions at render</span></span>

<span data-ttu-id="39a30-198">Asynchrone Aktionen, die in Lebenszyklusereignissen ausgeführt werden, sind möglicherweise nicht abgeschlossen, bevor die Komponente gerendert wird.</span><span class="sxs-lookup"><span data-stu-id="39a30-198">Asynchronous actions performed in lifecycle events might not have completed before the component is rendered.</span></span> <span data-ttu-id="39a30-199">Objekte können während der Ausführung der Lebenszyklusmethode `null` oder unvollständig mit Daten gefüllt sein.</span><span class="sxs-lookup"><span data-stu-id="39a30-199">Objects might be `null` or incompletely populated with data while the lifecycle method is executing.</span></span> <span data-ttu-id="39a30-200">Stellen Sie eine Renderinglogik bereit, um zu bestätigen, dass die Objekte initialisiert sind.</span><span class="sxs-lookup"><span data-stu-id="39a30-200">Provide rendering logic to confirm that objects are initialized.</span></span> <span data-ttu-id="39a30-201">Rendern Sie UI-Elemente für Platzhalter (z. B. eine Nachricht zum Ladevorgang), während Objekte `null` sind.</span><span class="sxs-lookup"><span data-stu-id="39a30-201">Render placeholder UI elements (for example, a loading message) while objects are `null`.</span></span>

<span data-ttu-id="39a30-202">In der `FetchData`-Komponente der Blazor-Vorlagen wird <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> außer Kraft gesetzt, um Vorhersagedaten asynchron abzurufen (`forecasts`).</span><span class="sxs-lookup"><span data-stu-id="39a30-202">In the `FetchData` component of the Blazor templates, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> is overridden to asynchronously receive forecast data (`forecasts`).</span></span> <span data-ttu-id="39a30-203">Wenn `forecasts` gleich `null` ist, wird dem Benutzer eine Nachricht zum Ladevorgang angezeigt.</span><span class="sxs-lookup"><span data-stu-id="39a30-203">When `forecasts` is `null`, a loading message is displayed to the user.</span></span> <span data-ttu-id="39a30-204">Nachdem die von <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> zurückgegebene `Task` abgeschlossen ist, wird die Komponente mit dem aktualisierten Zustand neu gerendert.</span><span class="sxs-lookup"><span data-stu-id="39a30-204">After the `Task` returned by <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> completes, the component is rerendered with the updated state.</span></span>

<span data-ttu-id="39a30-205">`Pages/FetchData.razor` in der Blazor Server-Vorlage:</span><span class="sxs-lookup"><span data-stu-id="39a30-205">`Pages/FetchData.razor` in the Blazor Server template:</span></span>

[!code-razor[](lifecycle/samples_snapshot/FetchData.razor?highlight=9,21,25)]

## <a name="handle-errors"></a><span data-ttu-id="39a30-206">Behandeln von Fehlern</span><span class="sxs-lookup"><span data-stu-id="39a30-206">Handle errors</span></span>

<span data-ttu-id="39a30-207">Informationen zur Behandlung von Fehlern während der Ausführung von Lebenszyklusmethoden finden Sie unter <xref:blazor/fundamentals/handle-errors#lifecycle-methods>.</span><span class="sxs-lookup"><span data-stu-id="39a30-207">For information on handling errors during lifecycle method execution, see <xref:blazor/fundamentals/handle-errors#lifecycle-methods>.</span></span>

## <a name="stateful-reconnection-after-prerendering"></a><span data-ttu-id="39a30-208">Zustandsbehaftete erneute Verbindung nach dem Prerendering</span><span class="sxs-lookup"><span data-stu-id="39a30-208">Stateful reconnection after prerendering</span></span>

<span data-ttu-id="39a30-209">Wenn in einer Blazor Server-App <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> gleich <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> ist, wird die Komponente zunächst statisch als Teil der Seite gerendert.</span><span class="sxs-lookup"><span data-stu-id="39a30-209">In a Blazor Server app when <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> is <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered>, the component is initially rendered statically as part of the page.</span></span> <span data-ttu-id="39a30-210">Sobald der Browser eine Verbindung mit dem Server herstellt, wird die Komponente *wieder* gerendert, und die Komponente ist nun interaktiv.</span><span class="sxs-lookup"><span data-stu-id="39a30-210">Once the browser establishes a connection back to the server, the component is rendered *again*, and the component is now interactive.</span></span> <span data-ttu-id="39a30-211">Wenn die [`OnInitialized{Async}`](#component-initialization-methods)-Lebenszyklusmethode zur Initialisierung der Komponente vorhanden ist, wird die Methode *zweimal* ausgeführt:</span><span class="sxs-lookup"><span data-stu-id="39a30-211">If the [`OnInitialized{Async}`](#component-initialization-methods) lifecycle method for initializing the component is present, the method is executed *twice*:</span></span>

* <span data-ttu-id="39a30-212">Wenn die Komponente statisch vorab gerendert ist.</span><span class="sxs-lookup"><span data-stu-id="39a30-212">When the component is prerendered statically.</span></span>
* <span data-ttu-id="39a30-213">Nachdem die Serververbindung hergestellt wurde.</span><span class="sxs-lookup"><span data-stu-id="39a30-213">After the server connection has been established.</span></span>

<span data-ttu-id="39a30-214">Dies kann zu einer spürbaren Änderung der auf der Benutzeroberfläche angezeigten Daten führen, wenn die Komponente schließlich gerendert wird.</span><span class="sxs-lookup"><span data-stu-id="39a30-214">This can result in a noticeable change in the data displayed in the UI when the component is finally rendered.</span></span>

<span data-ttu-id="39a30-215">So vermeiden Sie das Szenario des doppelten Renderings in einer Blazor Server-App:</span><span class="sxs-lookup"><span data-stu-id="39a30-215">To avoid the double-rendering scenario in a Blazor Server app:</span></span>

* <span data-ttu-id="39a30-216">Übergeben Sie einen Bezeichner, der zum Zwischenspeichern des Status während des Prerenderings und zum Abrufen des Zustands nach dem Neustart der App verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="39a30-216">Pass in an identifier that can be used to cache the state during prerendering and to retrieve the state after the app restarts.</span></span>
* <span data-ttu-id="39a30-217">Verwenden Sie den Bezeichner während des Prerenderings, um den Zustand der Komponenten zu speichern.</span><span class="sxs-lookup"><span data-stu-id="39a30-217">Use the identifier during prerendering to save component state.</span></span>
* <span data-ttu-id="39a30-218">Verwenden Sie den Bezeichner nach dem Prerendering, um den zwischengespeicherten Zustand abzurufen.</span><span class="sxs-lookup"><span data-stu-id="39a30-218">Use the identifier after prerendering to retrieve the cached state.</span></span>

<span data-ttu-id="39a30-219">Der folgende Code veranschaulicht eine aktualisierte `WeatherForecastService` in einer vorlagenbasierten Blazor Server-App, die das doppelte Rendering vermeidet:</span><span class="sxs-lookup"><span data-stu-id="39a30-219">The following code demonstrates an updated `WeatherForecastService` in a template-based Blazor Server app that avoids the double rendering:</span></span>

```csharp
public class WeatherForecastService
{
    private static readonly string[] summaries = new[]
    {
        "Freezing", "Bracing", "Chilly", "Cool", "Mild",
        "Warm", "Balmy", "Hot", "Sweltering", "Scorching"
    };
    
    public WeatherForecastService(IMemoryCache memoryCache)
    {
        MemoryCache = memoryCache;
    }
    
    public IMemoryCache MemoryCache { get; }

    public Task<WeatherForecast[]> GetForecastAsync(DateTime startDate)
    {
        return MemoryCache.GetOrCreateAsync(startDate, async e =>
        {
            e.SetOptions(new MemoryCacheEntryOptions
            {
                AbsoluteExpirationRelativeToNow = 
                    TimeSpan.FromSeconds(30)
            });

            var rng = new Random();

            await Task.Delay(TimeSpan.FromSeconds(10));

            return Enumerable.Range(1, 5).Select(index => new WeatherForecast
            {
                Date = startDate.AddDays(index),
                TemperatureC = rng.Next(-20, 55),
                Summary = summaries[rng.Next(summaries.Length)]
            }).ToArray();
        });
    }
}
```

<span data-ttu-id="39a30-220">Weitere Informationen zum <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> finden Sie unter <xref:blazor/fundamentals/additional-scenarios#render-mode>.</span><span class="sxs-lookup"><span data-stu-id="39a30-220">For more information on the <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode>, see <xref:blazor/fundamentals/additional-scenarios#render-mode>.</span></span>

## <a name="detect-when-the-app-is-prerendering"></a><span data-ttu-id="39a30-221">Erkennen, wenn für die App ein Prerendering durchgeführt wird</span><span class="sxs-lookup"><span data-stu-id="39a30-221">Detect when the app is prerendering</span></span>

[!INCLUDE[](~/blazor/includes/prerendering.md)]

## <a name="component-disposal-with-idisposable"></a><span data-ttu-id="39a30-222">Beseitigung von Komponenten mit IDisposable</span><span class="sxs-lookup"><span data-stu-id="39a30-222">Component disposal with IDisposable</span></span>

<span data-ttu-id="39a30-223">Wenn eine Komponente <xref:System.IDisposable> implementiert, wird die [`Dispose`-Methode](/dotnet/standard/garbage-collection/implementing-dispose) aufgerufen, wenn die Komponente aus der Benutzeroberfläche entfernt wird.</span><span class="sxs-lookup"><span data-stu-id="39a30-223">If a component implements <xref:System.IDisposable>, the [`Dispose` method](/dotnet/standard/garbage-collection/implementing-dispose) is called when the component is removed from the UI.</span></span> <span data-ttu-id="39a30-224">Das Entfernen kann jederzeit erfolgen, auch während der [Initialisierung von Komponenten](#component-initialization-methods).</span><span class="sxs-lookup"><span data-stu-id="39a30-224">Disposal can occur at any time, including during [component initialization](#component-initialization-methods).</span></span> <span data-ttu-id="39a30-225">Die folgende Komponente verwendet `@implements IDisposable` und die `Dispose`-Methode:</span><span class="sxs-lookup"><span data-stu-id="39a30-225">The following component uses `@implements IDisposable` and the `Dispose` method:</span></span>

```razor
@using System
@implements IDisposable

...

@code {
    public void Dispose()
    {
        ...
    }
}
```

> [!NOTE]
> <span data-ttu-id="39a30-226">Der Aufruf von <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> in `Dispose` wird nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="39a30-226">Calling <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> in `Dispose` isn't supported.</span></span> <span data-ttu-id="39a30-227"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> könnte im Rahmen des Beendens des Renderers aufgerufen werden, sodass die Anforderung von UI-Updates an diesem Punkt nicht unterstützt wird.</span><span class="sxs-lookup"><span data-stu-id="39a30-227"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> might be invoked as part of tearing down the renderer, so requesting UI updates at that point isn't supported.</span></span>

<span data-ttu-id="39a30-228">Kündigen Sie die .NET-Ereignisabonnements der Ereignishandler.</span><span class="sxs-lookup"><span data-stu-id="39a30-228">Unsubscribe event handlers from .NET events.</span></span> <span data-ttu-id="39a30-229">Die folgenden [Blazor-Formularbeispiele](xref:blazor/forms-validation) veranschaulichen das Aufheben der Einbindung eines Ereignishandlers in der `Dispose`-Methode:</span><span class="sxs-lookup"><span data-stu-id="39a30-229">The following [Blazor form](xref:blazor/forms-validation) examples show how to unhook an event handler in the `Dispose` method:</span></span>

* <span data-ttu-id="39a30-230">Ansatz mit einem privatem Feld und Lambdaausdruck</span><span class="sxs-lookup"><span data-stu-id="39a30-230">Private field and lambda approach</span></span>

  [!code-razor[](lifecycle/samples_snapshot/event-handler-disposal-1.razor?highlight=23,28)]

* <span data-ttu-id="39a30-231">Ansatz mit einer privaten Methode</span><span class="sxs-lookup"><span data-stu-id="39a30-231">Private method approach</span></span>

  [!code-razor[](lifecycle/samples_snapshot/event-handler-disposal-2.razor?highlight=16,26)]

## <a name="cancelable-background-work"></a><span data-ttu-id="39a30-232">Abbrechbare Hintergrundarbeit</span><span class="sxs-lookup"><span data-stu-id="39a30-232">Cancelable background work</span></span>

<span data-ttu-id="39a30-233">Komponenten führen häufig Hintergrundaufgaben aus, die lange dauern, zum Beispiel die Durchführung von Netzwerkaufrufen (<xref:System.Net.Http.HttpClient>) und die Interaktion mit Datenbanken.</span><span class="sxs-lookup"><span data-stu-id="39a30-233">Components often perform long-running background work, such as making network calls (<xref:System.Net.Http.HttpClient>) and interacting with databases.</span></span> <span data-ttu-id="39a30-234">Es ist wünschenswert, die Hintergrundarbeit zu unterbinden, um Systemressourcen in mehreren Situationen zu sparen.</span><span class="sxs-lookup"><span data-stu-id="39a30-234">It's desirable to stop the background work to conserve system resources in several situations.</span></span> <span data-ttu-id="39a30-235">Beispielsweise werden asynchrone Hintergrundvorgänge nicht automatisch beendet, wenn ein Benutzer von einer Komponente wegnavigiert.</span><span class="sxs-lookup"><span data-stu-id="39a30-235">For example, background asynchronous operations don't automatically stop when a user navigates away from a component.</span></span>

<span data-ttu-id="39a30-236">Andere Gründe, warum Arbeitselemente, die im Hintergrund ausgeführt werden, unterbrochen werden müssen, sind die folgenden:</span><span class="sxs-lookup"><span data-stu-id="39a30-236">Other reasons why background work items might require cancellation include:</span></span>

* <span data-ttu-id="39a30-237">Eine ausgeführte Hintergrundaufgabe wurde mit fehlerhaften Eingabedaten oder Verarbeitungsparametern gestartet.</span><span class="sxs-lookup"><span data-stu-id="39a30-237">An executing background task was started with faulty input data or processing parameters.</span></span>
* <span data-ttu-id="39a30-238">Die aktuellen Arbeitselemente, die im Hintergrund ausgeführt werden, müssen durch neue Arbeitselemente ersetzt werden.</span><span class="sxs-lookup"><span data-stu-id="39a30-238">The current set of executing background work items must be replaced with a new set of work items.</span></span>
* <span data-ttu-id="39a30-239">Die Priorität der aktuell ausgeführten Aufgaben muss geändert werden.</span><span class="sxs-lookup"><span data-stu-id="39a30-239">The priority of currently executing tasks must be changed.</span></span>
* <span data-ttu-id="39a30-240">Die App muss herunterfahren werden, um sie erneut auf dem Server bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="39a30-240">The app has to be shut down in order to redeploy it to the server.</span></span>
* <span data-ttu-id="39a30-241">Serverressourcen werden eingeschränkt und erfordern die Neuplanung von Arbeitselementen, die im Hintergrund ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="39a30-241">Server resources become limited, necessitating the rescheduling of background work items.</span></span>

<span data-ttu-id="39a30-242">So implementieren Sie ein abbrechbares Hintergrundarbeitsmuster in einer Komponente:</span><span class="sxs-lookup"><span data-stu-id="39a30-242">To implement a cancelable background work pattern in a component:</span></span>

* <span data-ttu-id="39a30-243">Verwenden Sie eine <xref:System.Threading.CancellationTokenSource>-Klasse und eine <xref:System.Threading.CancellationToken>-Struktur.</span><span class="sxs-lookup"><span data-stu-id="39a30-243">Use a <xref:System.Threading.CancellationTokenSource> and <xref:System.Threading.CancellationToken>.</span></span>
* <span data-ttu-id="39a30-244">Es ist gewünscht, dass der Abbruch bei der [Löschung der Komponente](#component-disposal-with-idisposable) und zu jedem Zeitpunkt durch manuellen Abbruch des Tokens durchgeführt wird. Rufen Sie dazu [`CancellationTokenSource.Cancel`](xref:System.Threading.CancellationTokenSource.Cancel%2A) auf, um zu signalisieren, dass die Hintergrundarbeit abgebrochen werden soll.</span><span class="sxs-lookup"><span data-stu-id="39a30-244">On [disposal of the component](#component-disposal-with-idisposable) and at any point cancellation is desired by manually cancelling the token, call [`CancellationTokenSource.Cancel`](xref:System.Threading.CancellationTokenSource.Cancel%2A) to signal that the background work should be cancelled.</span></span>
* <span data-ttu-id="39a30-245">Rufen Sie nach Rückgabe des asynchronen Aufrufs <xref:System.Threading.CancellationToken.ThrowIfCancellationRequested%2A> für das Token auf.</span><span class="sxs-lookup"><span data-stu-id="39a30-245">After the asynchronous call returns, call <xref:System.Threading.CancellationToken.ThrowIfCancellationRequested%2A> on the token.</span></span>

<span data-ttu-id="39a30-246">Im folgenden Beispiel:</span><span class="sxs-lookup"><span data-stu-id="39a30-246">In the following example:</span></span>

* <span data-ttu-id="39a30-247">`await Task.Delay(5000, cts.Token);` stellt asynchrone Hintergrundaufgaben mit langer Ausführungszeit dar.</span><span class="sxs-lookup"><span data-stu-id="39a30-247">`await Task.Delay(5000, cts.Token);` represents long-running asynchronous background work.</span></span>
* <span data-ttu-id="39a30-248">`BackgroundResourceMethod` stellt eine Hintergrundmethode mit langer Ausführungszeit dar, die nicht gestartet werden sollte, wenn die `Resource` vor dem Aufruf der Methode verworfen wird.</span><span class="sxs-lookup"><span data-stu-id="39a30-248">`BackgroundResourceMethod` represents a long-running background method that shouldn't start if the `Resource` is disposed before the method is called.</span></span>

```razor
@implements IDisposable
@using System.Threading

<button @onclick="LongRunningWork">Trigger long running work</button>

@code {
    private Resource resource = new Resource();
    private CancellationTokenSource cts = new CancellationTokenSource();

    protected async Task LongRunningWork()
    {
        await Task.Delay(5000, cts.Token);

        cts.Token.ThrowIfCancellationRequested();
        resource.BackgroundResourceMethod();
    }

    public void Dispose()
    {
        cts.Cancel();
        cts.Dispose();
        resource.Dispose();
    }

    private class Resource : IDisposable
    {
        private bool disposed;

        public void BackgroundResourceMethod()
        {
            if (disposed)
            {
                throw new ObjectDisposedException(nameof(Resource));
            }
            
            ...
        }
        
        public void Dispose()
        {
            disposed = true;
        }
    }
}
```

## <a name="no-locblazor-server-reconnection-events"></a><span data-ttu-id="39a30-249">Blazor Server-Ereignisse zur Wiederherstellung von Verbindungen</span><span class="sxs-lookup"><span data-stu-id="39a30-249">Blazor Server reconnection events</span></span>

<span data-ttu-id="39a30-250">Die in diesem Artikel behandelten Ereignisse zum Komponentenlebenszyklus werden getrennt von den [Blazor Server-Ereignishandlern zur Wiederherstellung von Verbindungen](xref:blazor/fundamentals/additional-scenarios#reflect-the-connection-state-in-the-ui) ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="39a30-250">The component lifecycle events covered in this article operate separately from [Blazor Server's reconnection event handlers](xref:blazor/fundamentals/additional-scenarios#reflect-the-connection-state-in-the-ui).</span></span> <span data-ttu-id="39a30-251">Wenn bei einer Blazor Server-App die SignalR-Verbindung mit dem Client getrennt wird, werden nur Updates der Benutzeroberfläche unterbrochen.</span><span class="sxs-lookup"><span data-stu-id="39a30-251">When a Blazor Server app loses its SignalR connection to the client, only UI updates are interrupted.</span></span> <span data-ttu-id="39a30-252">Updates der Benutzeroberfläche werden fortgesetzt, sobald die Verbindung wiederhergestellt wurde.</span><span class="sxs-lookup"><span data-stu-id="39a30-252">UI updates are resumed when the connection is re-established.</span></span> <span data-ttu-id="39a30-253">Weitere Informationen zu Verbindungshandlerereignissen und zur Konfiguration finden Sie unter <xref:blazor/fundamentals/additional-scenarios>.</span><span class="sxs-lookup"><span data-stu-id="39a30-253">For more information on circuit handler events and configuration, see <xref:blazor/fundamentals/additional-scenarios>.</span></span>
