---
title: Blazor-Komponentenrendering in ASP.NET Core
author: guardrex
description: In diesem Artikel erhalten Sie Informationen zum Razor-Komponentenrendering in Blazor-Apps in ASP.NET Core, einschließlich Informationen dazu, wann StateHasChanged aufgerufen wird.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2021
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
uid: blazor/components/rendering
ms.openlocfilehash: e1222981d4af3f4e233cdc0c57bb96a71972af15
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280038"
---
# <a name="aspnet-core-blazor-component-rendering"></a><span data-ttu-id="0f8fd-103">Blazor-Komponentenrendering in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0f8fd-103">ASP.NET Core Blazor component rendering</span></span>

<span data-ttu-id="0f8fd-104">Komponenten *müssen* gerendert werden, wenn sie erstmalig in der Komponentenhierarchie von ihrer übergeordneten Komponente hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="0f8fd-104">Components *must* render when they're first added to the component hierarchy by their parent component.</span></span> <span data-ttu-id="0f8fd-105">Dies ist der einzige Fall, in dem eine Komponente kompromisslos gerendert werden muss.</span><span class="sxs-lookup"><span data-stu-id="0f8fd-105">This is the only time that a component strictly must render.</span></span>

<span data-ttu-id="0f8fd-106">Komponenten *können* jederzeit gerendert werden. Dies erfolgt gemäß ihrer eigenen Logik und Konventionen.</span><span class="sxs-lookup"><span data-stu-id="0f8fd-106">Components *may* choose to render at any other time according to their own logic and conventions.</span></span>

## <a name="conventions-for-componentbase"></a><span data-ttu-id="0f8fd-107">Konventionen für `ComponentBase`</span><span class="sxs-lookup"><span data-stu-id="0f8fd-107">Conventions for `ComponentBase`</span></span>

<span data-ttu-id="0f8fd-108">Razor-Komponenten (`.razor`) erben standardmäßig von der <xref:Microsoft.AspNetCore.Components.ComponentBase>-Basisklasse. Diese enthält Logik, die das erneute Rendern zu folgenden Zeitpunkten auslöst:</span><span class="sxs-lookup"><span data-stu-id="0f8fd-108">By default, Razor components (`.razor`) inherit from the <xref:Microsoft.AspNetCore.Components.ComponentBase> base class, which contains logic to trigger rerendering at the following times:</span></span>

* <span data-ttu-id="0f8fd-109">Nach Anwenden aktualisierter Parameter einer übergeordneten Komponente</span><span class="sxs-lookup"><span data-stu-id="0f8fd-109">After applying an updated set of parameters from a parent component.</span></span>
* <span data-ttu-id="0f8fd-110">Nach Anwenden eines aktualisierten Werts für einen kaskadierenden Parameter</span><span class="sxs-lookup"><span data-stu-id="0f8fd-110">After applying an updated value for a cascading parameter.</span></span>
* <span data-ttu-id="0f8fd-111">Nach Benachrichtigung zu einem Ereignis und Aufrufen eines der dazugehörigen Ereignishandler</span><span class="sxs-lookup"><span data-stu-id="0f8fd-111">After notification of an event and invoking one of its own event handlers.</span></span>
* <span data-ttu-id="0f8fd-112">Nach einem Aufruf der dazugehörigen <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>-Methode</span><span class="sxs-lookup"><span data-stu-id="0f8fd-112">After a call to its own <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> method.</span></span>

<span data-ttu-id="0f8fd-113">Von <xref:Microsoft.AspNetCore.Components.ComponentBase> geerbte Komponenten überspringen wiederholte Rendervorgänge aufgrund von Parameteraktualisierungen, wenn eine der folgenden Aussagen zutrifft:</span><span class="sxs-lookup"><span data-stu-id="0f8fd-113">Components inherited from <xref:Microsoft.AspNetCore.Components.ComponentBase> skip rerenders due to parameter updates if either of the following are true:</span></span>

* <span data-ttu-id="0f8fd-114">Alle Parameterwerte weisen einen bekannten unveränderlichen primitiven Typ auf (z. B. `int`, `string`, `DateTime`) und haben sich seit Festlegung der vorherigen Parameter nicht geändert.</span><span class="sxs-lookup"><span data-stu-id="0f8fd-114">All of the parameter values are of known immutable primitive types (for example, `int`, `string`, `DateTime`) and haven't changed since the previous set of parameters were set.</span></span>
* <span data-ttu-id="0f8fd-115">Die <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>-Methode der Komponente gibt `false` zurück.</span><span class="sxs-lookup"><span data-stu-id="0f8fd-115">The component's <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> method returns `false`.</span></span>

<span data-ttu-id="0f8fd-116">Weitere Informationen zu <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> finden Sie unter <xref:blazor/webassembly-performance-best-practices#use-of-shouldrender>.</span><span class="sxs-lookup"><span data-stu-id="0f8fd-116">For more information on <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>, see <xref:blazor/webassembly-performance-best-practices#use-of-shouldrender>.</span></span>

## <a name="control-the-rendering-flow"></a><span data-ttu-id="0f8fd-117">Steuern des Renderingflows</span><span class="sxs-lookup"><span data-stu-id="0f8fd-117">Control the rendering flow</span></span>

<span data-ttu-id="0f8fd-118">In den meisten Fällen führen <xref:Microsoft.AspNetCore.Components.ComponentBase>-Konventionen zu erneuten Renderingvorgängen für die richtige Teilmenge der Komponenten, nachdem ein Ereignis auftritt.</span><span class="sxs-lookup"><span data-stu-id="0f8fd-118">In most cases, <xref:Microsoft.AspNetCore.Components.ComponentBase> conventions result in the correct subset of component rerenders after an event occurs.</span></span> <span data-ttu-id="0f8fd-119">Entwickler müssen in der Regel keine manuelle Logik bereitstellen, damit das Framework die Information erhält, welche Komponenten erneut gerendert werden müssen und wann sie erneut gerendert werden müssen.</span><span class="sxs-lookup"><span data-stu-id="0f8fd-119">Developers aren't usually required to provide manual logic to tell the framework which components to rerender and when to rerender them.</span></span> <span data-ttu-id="0f8fd-120">Der Gesamteffekt der Konventionen des Frameworks besteht darin, dass die Komponente, für die ein Ereignis auftritt, sich selbst erneut rendert. Dies löst rekursiv ein erneutes Rendern der nachfolgenden Komponenten aus, deren Parameterwerte sich möglicherweise geändert haben.</span><span class="sxs-lookup"><span data-stu-id="0f8fd-120">The overall effect of the framework's conventions is that the component receiving an event rerenders itself, which recursively triggers rerendering of descendant components whose parameter values may have changed.</span></span>

<span data-ttu-id="0f8fd-121">Weitere Informationen zu den Auswirkungen auf die Leistung der Konventionen des Frameworks und dazu, wie Sie die Komponentenhierarchie einer App optimieren können, finden Sie unter <xref:blazor/webassembly-performance-best-practices#optimize-rendering-speed>.</span><span class="sxs-lookup"><span data-stu-id="0f8fd-121">For more information on the performance implications of the framework's conventions and how to optimize an app's component hierarchy, see <xref:blazor/webassembly-performance-best-practices#optimize-rendering-speed>.</span></span>

## <a name="when-to-call-statehaschanged"></a><span data-ttu-id="0f8fd-122">Gründe für einen Aufruf von `StateHasChanged`</span><span class="sxs-lookup"><span data-stu-id="0f8fd-122">When to call `StateHasChanged`</span></span>

<span data-ttu-id="0f8fd-123">Die <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A?displayProperty=nameWithType>-Methode ermöglicht es Ihnen, jederzeit ein Rendering auszulösen.</span><span class="sxs-lookup"><span data-stu-id="0f8fd-123">The <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A?displayProperty=nameWithType> method allows you to trigger a render at any time.</span></span> <span data-ttu-id="0f8fd-124">Achten Sie jedoch darauf, <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> nicht unnötigerweise aufzurufen. Dies ist ein häufiger Fehler, da so unnötige Renderingkosten verursacht werden.</span><span class="sxs-lookup"><span data-stu-id="0f8fd-124">However, be careful not to call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> unnecessarily, which is a common mistake, because it imposes unnecessary rendering costs.</span></span>

<span data-ttu-id="0f8fd-125">In folgenden Fällen sollte <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> *nicht* aufgerufen werden müssen:</span><span class="sxs-lookup"><span data-stu-id="0f8fd-125">You should *not* need to call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> when:</span></span>

* <span data-ttu-id="0f8fd-126">Routinemäßige Verarbeitung von Ereignissen (synchron und asynchron), da <xref:Microsoft.AspNetCore.Components.ComponentBase> ein Rendering für die meisten Routingereignishandler auslöst</span><span class="sxs-lookup"><span data-stu-id="0f8fd-126">Routinely handling events, whether synchronously or asynchronously, since <xref:Microsoft.AspNetCore.Components.ComponentBase> triggers a render for most routine event handlers.</span></span>
* <span data-ttu-id="0f8fd-127">Implementieren typischer Lebenszykluslogik (synchron und asynchron), z. B. [`OnInitialized`](xref:blazor/components/lifecycle#component-initialization-methods) oder [`OnParametersSetAsync`](xref:blazor/components/lifecycle#after-parameters-are-set), da <xref:Microsoft.AspNetCore.Components.ComponentBase> ein Rendering für typische Lebenszyklusereignisse auslöst</span><span class="sxs-lookup"><span data-stu-id="0f8fd-127">Implementing typical lifecycle logic, such as [`OnInitialized`](xref:blazor/components/lifecycle#component-initialization-methods) or [`OnParametersSetAsync`](xref:blazor/components/lifecycle#after-parameters-are-set), whether synchonrously or asynchronously, since <xref:Microsoft.AspNetCore.Components.ComponentBase> triggers a render for typical lifecycle events.</span></span>

<span data-ttu-id="0f8fd-128">In den in den folgenden Abschnitten beschriebenen Fällen ergibt es jedoch Sinn, <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> aufzurufen:</span><span class="sxs-lookup"><span data-stu-id="0f8fd-128">However, it might make sense to call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> in the cases described by the following sections:</span></span>

* [<span data-ttu-id="0f8fd-129">Ein asynchroner Handler beinhaltet mehrere asynchrone Phasen.</span><span class="sxs-lookup"><span data-stu-id="0f8fd-129">An asynchronous handler involves multiple asynchronous phases</span></span>](#an-asynchronous-handler-involves-multiple-asynchronous-phases)
* [<span data-ttu-id="0f8fd-130">Ein Aufruf von einer externen Quelle an das Blazor-Renderingsystem bzw. -Ereignisverarbeitungssystem wird empfangen.</span><span class="sxs-lookup"><span data-stu-id="0f8fd-130">Receiving a call from something external to the Blazor rendering and event handling system</span></span>](#receiving-a-call-from-something-external-to-the-blazor-rendering-and-event-handling-system)
* [<span data-ttu-id="0f8fd-131">Eine Komponente soll außerhalb der untergeordneten Struktur gerendert werden, die von einem bestimmten Ereignis erneut gerendert wird.</span><span class="sxs-lookup"><span data-stu-id="0f8fd-131">To render component outside the subtree that is rerendered by a particular event</span></span>](#to-render-a-component-outside-the-subtree-thats-rerendered-by-a-particular-event)

### <a name="an-asynchronous-handler-involves-multiple-asynchronous-phases"></a><span data-ttu-id="0f8fd-132">Ein asynchroner Handler beinhaltet mehrere asynchrone Phasen</span><span class="sxs-lookup"><span data-stu-id="0f8fd-132">An asynchronous handler involves multiple asynchronous phases</span></span>

<span data-ttu-id="0f8fd-133">Berücksichtigen Sie die folgende `Counter`-Komponente, die die Anzahl pro Klick viermal aktualisiert.</span><span class="sxs-lookup"><span data-stu-id="0f8fd-133">Consider the following `Counter` component, which updates the count four times on each click.</span></span>

<span data-ttu-id="0f8fd-134">`Pages/Counter.razor`:</span><span class="sxs-lookup"><span data-stu-id="0f8fd-134">`Pages/Counter.razor`:</span></span>

```razor
@page "/counter"

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    private async Task IncrementCount()
    {
        currentCount++;
        // Renders here automatically

        await Task.Delay(1000);
        currentCount++;
        StateHasChanged();

        await Task.Delay(1000);
        currentCount++;
        StateHasChanged();

        await Task.Delay(1000);
        currentCount++;
        // Renders here automatically
    }
}
```

<span data-ttu-id="0f8fd-135">Aufgrund der Art, wie Aufgaben in .NET definiert werden, kann ein Empfänger von <xref:System.Threading.Tasks.Task> nur den endgültigen Abschluss beobachten, keine asynchronen Zwischenzustände.</span><span class="sxs-lookup"><span data-stu-id="0f8fd-135">Due to the way that tasks are defined in .NET, a receiver of a <xref:System.Threading.Tasks.Task> can only observe its final completion, not intermediate asynchronous states.</span></span> <span data-ttu-id="0f8fd-136">Deshalb kann <xref:Microsoft.AspNetCore.Components.ComponentBase> nur ein erneutes Rendering auslösen, wenn <xref:System.Threading.Tasks.Task> zuerst zurückgegeben wird und wenn <xref:System.Threading.Tasks.Task> endgültig abgeschlossen ist.</span><span class="sxs-lookup"><span data-stu-id="0f8fd-136">Therefore, <xref:Microsoft.AspNetCore.Components.ComponentBase> can only trigger rerendering when the <xref:System.Threading.Tasks.Task> is first returned and when the <xref:System.Threading.Tasks.Task> finally completes.</span></span> <span data-ttu-id="0f8fd-137">Die Information, dass an anderen Zwischenpunkten ein erneutes Rendering durchgeführt werden soll, kann nicht gegeben werden.</span><span class="sxs-lookup"><span data-stu-id="0f8fd-137">It can't know to rerender at other intermediate points.</span></span> <span data-ttu-id="0f8fd-138">Wenn Sie an Zwischenpunkten ein erneutes Rendering durchführen möchten, verwenden Sie <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>.</span><span class="sxs-lookup"><span data-stu-id="0f8fd-138">If you want to rerender at intermediate points, use <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>.</span></span>

### <a name="receiving-a-call-from-something-external-to-the-blazor-rendering-and-event-handling-system"></a><span data-ttu-id="0f8fd-139">Ein Aufruf von einer externen Quelle an das Blazor-Renderingsystem bzw. -Ereignisverarbeitungssystem wird empfangen</span><span class="sxs-lookup"><span data-stu-id="0f8fd-139">Receiving a call from something external to the Blazor rendering and event handling system</span></span>

<span data-ttu-id="0f8fd-140"><xref:Microsoft.AspNetCore.Components.ComponentBase> kennt nur die eigenen Lebenszyklusmethoden und von Blazor ausgelösten Ereignisse.</span><span class="sxs-lookup"><span data-stu-id="0f8fd-140"><xref:Microsoft.AspNetCore.Components.ComponentBase> only knows about its own lifecycle methods and Blazor-triggered events.</span></span> <span data-ttu-id="0f8fd-141"><xref:Microsoft.AspNetCore.Components.ComponentBase> hat keine Informationen zu anderen Ereignissen, die möglicherweise in Ihrem Code auftreten.</span><span class="sxs-lookup"><span data-stu-id="0f8fd-141"><xref:Microsoft.AspNetCore.Components.ComponentBase> doesn't know about other events that may occur in your code.</span></span> <span data-ttu-id="0f8fd-142">Alle C#-Ereignisse, die von einem benutzerdefinierten Datenspeicher ausgelöst werden, sind beispielsweise für Blazor nicht bekannt.</span><span class="sxs-lookup"><span data-stu-id="0f8fd-142">For example, any C# events raised by a custom data store are unknown to Blazor.</span></span> <span data-ttu-id="0f8fd-143">Damit solche Ereignisse ein erneutes Rendering auslösen, damit aktualisierte Werte auf der Benutzeroberfläche angezeigt werden, verwenden Sie <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>.</span><span class="sxs-lookup"><span data-stu-id="0f8fd-143">In order for such events to trigger rerendering to display updated values in the UI, use <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>.</span></span>

<span data-ttu-id="0f8fd-144">In einem anderen Anwendungsfall sollten Sie die folgende `Counter`-Komponente berücksichtigen, die <xref:System.Timers.Timer?displayProperty=fullName> verwendet, um die Anzahl in regelmäßigen Abständen zu aktualisieren, und <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> aufruft, um die Benutzeroberfläche zu aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="0f8fd-144">In another use case, consider the following `Counter` component that uses <xref:System.Timers.Timer?displayProperty=fullName> to update the count at a regular interval and calls <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> to update the UI.</span></span>

<span data-ttu-id="0f8fd-145">`Pages/CounterWithTimerDisposal.razor`:</span><span class="sxs-lookup"><span data-stu-id="0f8fd-145">`Pages/CounterWithTimerDisposal.razor`:</span></span>

```razor
@page "/counter-with-timer-disposal"
@using System.Timers
@implements IDisposable

<h1>Counter with <code>Timer</code> disposal</h1>

<p>Current count: @currentCount</p>

@code {
    private int currentCount = 0;
    private Timer timer = new Timer(1000);

    protected override void OnInitialized()
    {
        timer.Elapsed += (sender, eventArgs) => OnTimerCallback();
        timer.Start();
    }

    private void OnTimerCallback()
    {
        _ = InvokeAsync(() =>
        {
            currentCount++;
            StateHasChanged();
        });
    }

    public void IDisposable.Dispose() => timer.Dispose();
}
```

<span data-ttu-id="0f8fd-146">Im vorherigen Beispiel:</span><span class="sxs-lookup"><span data-stu-id="0f8fd-146">In the preceding example:</span></span>

* <span data-ttu-id="0f8fd-147">`OnTimerCallback` muss <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> aufrufen, da Blazor die Änderungen an `currentCount` im Rückruf nicht bekannt sind.</span><span class="sxs-lookup"><span data-stu-id="0f8fd-147">`OnTimerCallback` must call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> because Blazor isn't aware of the changes to `currentCount` in the callback.</span></span> <span data-ttu-id="0f8fd-148">`OnTimerCallback` wird außerhalb eines von Blazor verwalteten Renderingflows oder einer Ereignisbenachrichtigung ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="0f8fd-148">`OnTimerCallback` runs outside of any Blazor-managed rendering flow or event notification.</span></span>
* <span data-ttu-id="0f8fd-149">Die Komponente implementiert <xref:System.IDisposable>, wobei <xref:System.Timers.Timer> verworfen wird, wenn das Framework die `Dispose`-Methode aufruft.</span><span class="sxs-lookup"><span data-stu-id="0f8fd-149">The component implements <xref:System.IDisposable>, where the <xref:System.Timers.Timer> is disposed when the framework calls the `Dispose` method.</span></span> <span data-ttu-id="0f8fd-150">Weitere Informationen finden Sie unter <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span><span class="sxs-lookup"><span data-stu-id="0f8fd-150">For more information, see <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span></span>

<span data-ttu-id="0f8fd-151">Auf ähnliche Weise ist es erforderlich, die Logik in Blazor zu umschließen, da der Rückruf außerhalb des Synchronisierungskontexts von <xref:Microsoft.AspNetCore.Components.ComponentBase.InvokeAsync%2A?displayProperty=nameWithType> aufgerufen wird, damit sie in den Synchronisierungskontext des Renderers verschoben wird.</span><span class="sxs-lookup"><span data-stu-id="0f8fd-151">Similarly, because the callback is invoked outside Blazor's synchronization context, it's necessary to wrap the logic in <xref:Microsoft.AspNetCore.Components.ComponentBase.InvokeAsync%2A?displayProperty=nameWithType> to move it onto the renderer's synchronization context.</span></span> <span data-ttu-id="0f8fd-152"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> kann nur im Synchronisierungskontext des Renderers aufgerufen werden und gibt andernfalls eine Ausnahme zurück.</span><span class="sxs-lookup"><span data-stu-id="0f8fd-152"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> can only be called from the renderer's synchronization context and throws an exception otherwise.</span></span> <span data-ttu-id="0f8fd-153">Dies ist äquivalent zum Marshallen des Benutzeroberflächenthreads in anderen Benutzeroberflächenframeworks.</span><span class="sxs-lookup"><span data-stu-id="0f8fd-153">This is equivalent to marshalling to the UI thread in other UI frameworks.</span></span>

### <a name="to-render-a-component-outside-the-subtree-thats-rerendered-by-a-particular-event"></a><span data-ttu-id="0f8fd-154">Eine Komponente soll außerhalb der untergeordneten Struktur gerendert werden, die von einem bestimmten Ereignis erneut gerendert wird</span><span class="sxs-lookup"><span data-stu-id="0f8fd-154">To render a component outside the subtree that's rerendered by a particular event</span></span>

<span data-ttu-id="0f8fd-155">Möglicherweise ist es für Ihre Benutzeroberfläche erforderlich, ein Ereignis an eine Komponente zu senden, einen Status zu ändern und eine andere Komponente erneut zu rendern, die kein Nachfolger der Komponente ist, die das Ereignis empfängt.</span><span class="sxs-lookup"><span data-stu-id="0f8fd-155">Your UI might involve dispatching an event to one component, changing some state, and needing to rerender a completely different component that isn't a descendant of the one receiving the event.</span></span>

<span data-ttu-id="0f8fd-156">Eine Möglichkeit, mit diesem Szenario umzugehen, ist die Verwendung einer Klasse für die *Statusverwaltung*, z. B. in Form eines DI-Diensts, die in verschiedene Komponenten injiziert wird.</span><span class="sxs-lookup"><span data-stu-id="0f8fd-156">One way to deal with this scenario is to have a *state management* class, perhaps as a DI service, injected into multiple components.</span></span> <span data-ttu-id="0f8fd-157">Wenn eine Komponente eine Methode für den Status-Manager aufruft, kann der Status-Manager ein C#-Ereignis auslösen, das dann von einer unabhängigen Komponente empfangen wird.</span><span class="sxs-lookup"><span data-stu-id="0f8fd-157">When one component calls a method on the state manager, the state manager can raise a C# event that's then received by an independent component.</span></span>

<span data-ttu-id="0f8fd-158">Da sich diese C#-Ereignisse außerhalb der Blazor-Renderingpipeline befinden, müssen Sie <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> für andere Komponenten aufrufen, die als Reaktion auf Ereignisse des Status-Managers gerendert werden sollen.</span><span class="sxs-lookup"><span data-stu-id="0f8fd-158">Since these C# events are outside the Blazor rendering pipeline, call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> on other components you wish to render in response to the state manager's events.</span></span>

<span data-ttu-id="0f8fd-159">Dies ähnelt dem vorherigen Fall mit <xref:System.Timers.Timer?displayProperty=fullName> im [vorherigen Abschnitt](#receiving-a-call-from-something-external-to-the-blazor-rendering-and-event-handling-system).</span><span class="sxs-lookup"><span data-stu-id="0f8fd-159">This is similar to the earlier case with <xref:System.Timers.Timer?displayProperty=fullName> in the [previous section](#receiving-a-call-from-something-external-to-the-blazor-rendering-and-event-handling-system) section.</span></span> <span data-ttu-id="0f8fd-160">Da die Ausführungsaufrufliste in der Regel im Synchronisierungskontext des Renderers verbleibt, ist <xref:Microsoft.AspNetCore.Components.ComponentBase.InvokeAsync%2A> normalerweise nicht erforderlich.</span><span class="sxs-lookup"><span data-stu-id="0f8fd-160">Since the execution call stack typically remains on the renderer's synchronization context, <xref:Microsoft.AspNetCore.Components.ComponentBase.InvokeAsync%2A> isn't normally required.</span></span> <span data-ttu-id="0f8fd-161"><xref:Microsoft.AspNetCore.Components.ComponentBase.InvokeAsync%2A> ist nur erforderlich, wenn sich die Logik außerhalb des Synchronisierungskontexts befindet, z. B. wenn <xref:System.Threading.Tasks.Task.ContinueWith%2A> für <xref:System.Threading.Tasks.Task> aufgerufen wird oder <xref:System.Threading.Tasks.Task> mit [`ConfigureAwait(false)`](xref:System.Threading.Tasks.Task.ConfigureAwait%2A) erwartet wird.</span><span class="sxs-lookup"><span data-stu-id="0f8fd-161"><xref:Microsoft.AspNetCore.Components.ComponentBase.InvokeAsync%2A> is only required if the logic escapes the synchronization context, such as calling <xref:System.Threading.Tasks.Task.ContinueWith%2A> on a <xref:System.Threading.Tasks.Task> or awaiting a <xref:System.Threading.Tasks.Task> with [`ConfigureAwait(false)`](xref:System.Threading.Tasks.Task.ConfigureAwait%2A).</span></span>
