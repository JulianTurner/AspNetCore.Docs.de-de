---
title: Best Practices zur Blazor WebAssembly-Leistung in ASP.NET Core
author: pranavkm
description: In diesem Artikel erhalten Sie Tipps für das Steigern der Leistung von Blazor WebAssembly-Apps in ASP.NET Core und das Vermeiden von häufig auftretenden Leistungsproblemen.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc, devx-track-js
ms.date: 10/09/2020
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
uid: blazor/webassembly-performance-best-practices
ms.openlocfilehash: cc090b4e56745e6b010e4a7ee17332b0d3a95560
ms.sourcegitcommit: aa85f2911792a1e4783bcabf0da3b3e7e218f63a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/23/2020
ms.locfileid: "95417382"
---
# <a name="aspnet-core-no-locblazor-webassembly-performance-best-practices"></a><span data-ttu-id="d7c17-103">Best Practices zur Blazor WebAssembly-Leistung in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d7c17-103">ASP.NET Core Blazor WebAssembly performance best practices</span></span>

<span data-ttu-id="d7c17-104">Von [Pranav Krishnamoorthy](https://github.com/pranavkm) und [Steve Sanderson](https://github.com/SteveSandersonMS)</span><span class="sxs-lookup"><span data-stu-id="d7c17-104">By [Pranav Krishnamoorthy](https://github.com/pranavkm) and [Steve Sanderson](https://github.com/SteveSandersonMS)</span></span>

<span data-ttu-id="d7c17-105">Blazor WebAssembly wurde sorgfältig entworfen und optimiert, um hohe Leistung in den realistischsten Anwendungsbenutzeroberflächen-Szenarien zu ermöglichen.</span><span class="sxs-lookup"><span data-stu-id="d7c17-105">Blazor WebAssembly is carefully designed and optimized to enable high performance in most realistic application UI scenarios.</span></span> <span data-ttu-id="d7c17-106">Die besten Ergebnisse zu erzielen, hängt jedoch davon ab, dass die Entwickler die richtigen Muster und Features verwenden.</span><span class="sxs-lookup"><span data-stu-id="d7c17-106">However, producing the best results depends on developers using the right patterns and features.</span></span> <span data-ttu-id="d7c17-107">Berücksichtigen Sie folgende Aspekte:</span><span class="sxs-lookup"><span data-stu-id="d7c17-107">Consider the following aspects:</span></span>

* <span data-ttu-id="d7c17-108">**Laufzeitdurchsatz**: Der .NET-Code wird in einem Interpreter innerhalb der WebAssembly-Laufzeit ausgeführt, sodass der CPU-Durchsatz begrenzt ist.</span><span class="sxs-lookup"><span data-stu-id="d7c17-108">**Runtime throughput**: The .NET code runs on an interpreter within the WebAssembly runtime, so CPU throughput is limited.</span></span> <span data-ttu-id="d7c17-109">In anspruchsvollen Szenarien profitiert die App vom [Optimieren der Renderinggeschwindigkeit](#optimize-rendering-speed).</span><span class="sxs-lookup"><span data-stu-id="d7c17-109">In demanding scenarios, the app benefits from [optimizing rendering speed](#optimize-rendering-speed).</span></span>
* <span data-ttu-id="d7c17-110">**Startzeit**: Die App überträgt eine .NET-Laufzeit an den Browser. Daher ist es wichtig, Features zu verwenden, die die [Größe des Anwendungsdownloads minimieren](#minimize-app-download-size).</span><span class="sxs-lookup"><span data-stu-id="d7c17-110">**Startup time**: The app transfers a .NET runtime to the browser, so it's important to use features that [minimize the application download size](#minimize-app-download-size).</span></span>

## <a name="optimize-rendering-speed"></a><span data-ttu-id="d7c17-111">Optimieren der Renderinggeschwindigkeit</span><span class="sxs-lookup"><span data-stu-id="d7c17-111">Optimize rendering speed</span></span>

<span data-ttu-id="d7c17-112">Die folgenden Abschnitte enthalten Empfehlungen zum Minimieren der Renderingworkload und zur Verbesserung der Reaktionsfähigkeit der Benutzeroberfläche.</span><span class="sxs-lookup"><span data-stu-id="d7c17-112">The following sections provide recommendations to minimize rendering workload and improve UI responsiveness.</span></span> <span data-ttu-id="d7c17-113">Wenn Sie diese Ratschläge befolgen, können Sie mühelos *mindestens eine zehnfache Verbesserung* der Geschwindigkeit des Renderings der Benutzeroberfläche erreichen.</span><span class="sxs-lookup"><span data-stu-id="d7c17-113">Following this advice could easily make a *ten-fold or higher improvement* in UI rendering speeds.</span></span>

### <a name="avoid-unnecessary-rendering-of-component-subtrees"></a><span data-ttu-id="d7c17-114">Vermeiden von unnötigem Rendering der Komponentenunterstrukturen</span><span class="sxs-lookup"><span data-stu-id="d7c17-114">Avoid unnecessary rendering of component subtrees</span></span>

<span data-ttu-id="d7c17-115">Zur Laufzeit sind Komponenten als Hierarchie vorhanden.</span><span class="sxs-lookup"><span data-stu-id="d7c17-115">At runtime, components exist as a hierarchy.</span></span> <span data-ttu-id="d7c17-116">Eine Stammkomponente weist untergeordnete Komponenten auf.</span><span class="sxs-lookup"><span data-stu-id="d7c17-116">A root component has child components.</span></span> <span data-ttu-id="d7c17-117">Die untergeordneten Elemente des Stamms verfügen wiederum über eigene untergeordnete Komponenten usw.</span><span class="sxs-lookup"><span data-stu-id="d7c17-117">In turn, the root's children have their own child components, and so on.</span></span> <span data-ttu-id="d7c17-118">Wenn ein Ereignis auftritt, z. B. ein Benutzer eine Schaltfläche auswählt, entscheidet Blazor folgendermaßen, welche Komponenten erneut gerendert werden:</span><span class="sxs-lookup"><span data-stu-id="d7c17-118">When an event occurs, such as a user selecting a button, this is how Blazor decides which components to rerender:</span></span>

 1. <span data-ttu-id="d7c17-119">Das Ereignis selbst wird an den Ereignishandler der gerenderten Komponente gesendet.</span><span class="sxs-lookup"><span data-stu-id="d7c17-119">The event itself is dispatched to whichever component rendered the event's handler.</span></span> <span data-ttu-id="d7c17-120">Nach Ausführung des Ereignishandlers wird diese Komponente erneut gerendert.</span><span class="sxs-lookup"><span data-stu-id="d7c17-120">After executing the event handler, that component is rerendered.</span></span>
 1. <span data-ttu-id="d7c17-121">Jedes Mal, wenn eine Komponente erneut gerendert wird, wird eine neue Kopie der Parameterwerte für jede ihrer untergeordneten Komponenten bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="d7c17-121">Whenever any component is rerendered, it supplies a new copy of the parameter values to each of its child components.</span></span>
 1. <span data-ttu-id="d7c17-122">Beim Empfang eines neuen Satzes von Parameterwerten wählt jede Komponente aus, ob sie erneut gerendert werden muss.</span><span class="sxs-lookup"><span data-stu-id="d7c17-122">When receiving a new set of parameter values, each component chooses whether to rerender.</span></span> <span data-ttu-id="d7c17-123">Standardmäßig werden Komponenten erneut gerendert, wenn die Parameterwerte geändert wurden (wenn sie z. B. veränderbare Objekte sind).</span><span class="sxs-lookup"><span data-stu-id="d7c17-123">By default, components rerender if the parameter values may have changed (for example, if they are mutable objects).</span></span>

<span data-ttu-id="d7c17-124">Die letzten beiden Schritte dieser Sequenz werden rekursiv die Komponentenhierarchie hinab fortgesetzt.</span><span class="sxs-lookup"><span data-stu-id="d7c17-124">The last two steps of this sequence continue recursively down the component hierarchy.</span></span> <span data-ttu-id="d7c17-125">In vielen Fällen wird die gesamte Unterstruktur erneut gerendert.</span><span class="sxs-lookup"><span data-stu-id="d7c17-125">In many cases, the entire subtree is rerendered.</span></span> <span data-ttu-id="d7c17-126">Dies bedeutet, dass Ereignisse, die auf Komponenten auf hoher Ebene gerichtet sind, aufwändige erneute Renderingvorgänge verursachen können, da alles unterhalb dieses Punkts erneut gerendert werden muss.</span><span class="sxs-lookup"><span data-stu-id="d7c17-126">This means that events targeting high-level components can cause expensive rerendering processes because everything below that point must be rerendered.</span></span>

<span data-ttu-id="d7c17-127">Wenn Sie diesen Vorgang unterbrechen und die Renderingrekursion in einer bestimmten Unterstruktur unterbrechen möchten, können Sie Folgendes tun:</span><span class="sxs-lookup"><span data-stu-id="d7c17-127">If you want to interrupt this process and prevent rendering recursion into a particular subtree, then you can either:</span></span>

 * <span data-ttu-id="d7c17-128">Stellen Sie sicher, dass alle Parameter für eine bestimmte Komponente primitive unveränderliche Typen sind (z. B. `string`, `int`, `bool`, `DateTime` und andere).</span><span class="sxs-lookup"><span data-stu-id="d7c17-128">Ensure that all parameters to a certain component are of primitive immutable types (for example, `string`, `int`, `bool`, `DateTime`, and others).</span></span> <span data-ttu-id="d7c17-129">Die integrierte Logik zum automatischen Erkennen von Änderungen überspringt das erneute Rendern, wenn keiner dieser Parameterwerte geändert wurde.</span><span class="sxs-lookup"><span data-stu-id="d7c17-129">The built-in logic for detecting changes automatically skips rerendering if none of these parameter values have changed.</span></span> <span data-ttu-id="d7c17-130">Wenn Sie eine untergeordnete Komponente mit `<Customer CustomerId="@item.CustomerId" />` rendern, wobei `CustomerId` ein `int`-Wert ist, wird sie nur dann erneut gerendert, wenn `item.CustomerId` sich ändert.</span><span class="sxs-lookup"><span data-stu-id="d7c17-130">If you render a child component with `<Customer CustomerId="@item.CustomerId" />`, where `CustomerId` is an `int` value, then it isn't rerendered except when `item.CustomerId` changes.</span></span>
 * <span data-ttu-id="d7c17-131">Wenn Sie nicht primitive Parameterwerte wie benutzerdefinierte Modelltypen, Ereignisrückrufe oder <xref:Microsoft.AspNetCore.Components.RenderFragment>-Werte akzeptieren müssen, können Sie <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> überschreiben, um die Entscheidung über das Rendern zu steuern, was im Abschnitt [Verwendung von `ShouldRender`](#use-of-shouldrender) beschrieben wird.</span><span class="sxs-lookup"><span data-stu-id="d7c17-131">If you need to accept nonprimitive parameter values, such as custom model types, event callbacks, or <xref:Microsoft.AspNetCore.Components.RenderFragment> values, then you can override <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> to control the decision about whether to render, which is described in the [Use of `ShouldRender`](#use-of-shouldrender) section.</span></span>

<span data-ttu-id="d7c17-132">Indem Sie die erneute Ausführung ganzer Unterstrukturen überspringen, können Sie möglicherweise den größten Teil des Renderingaufwands beim Auftreten eines Ereignisses vermeiden.</span><span class="sxs-lookup"><span data-stu-id="d7c17-132">By skipping rerendering of whole subtrees, you may be able to remove the vast majority of the rendering cost when an event occurs.</span></span>

<span data-ttu-id="d7c17-133">Sie können untergeordnete Komponenten speziell ausklammern, damit Sie das erneute Rendern dieses Teils der Benutzeroberfläche überspringen können.</span><span class="sxs-lookup"><span data-stu-id="d7c17-133">You may wish to factor out child components specifically so that you can skip rerendering that part of the UI.</span></span> <span data-ttu-id="d7c17-134">Dies ist eine gültige Methode zum Reduzieren des Aufwands für das Rendern einer übergeordneten Komponente.</span><span class="sxs-lookup"><span data-stu-id="d7c17-134">This is a valid way to reduce the rendering cost of a parent component.</span></span>

#### <a name="use-of-shouldrender"></a><span data-ttu-id="d7c17-135">Verwendung von `ShouldRender`</span><span class="sxs-lookup"><span data-stu-id="d7c17-135">Use of `ShouldRender`</span></span>

<span data-ttu-id="d7c17-136">Wenn eine nur auf die Benutzeroberfläche bezogene Komponente erstellt wird, die sich nach dem ursprünglichen Rendern (unabhängig von Parameterwerten) nicht mehr ändert, konfigurieren Sie <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> so, dass `false` zurückgegeben wird:</span><span class="sxs-lookup"><span data-stu-id="d7c17-136">If authoring a UI-only component that never changes after the initial render (regardless of any parameter values), configure <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> to return `false`:</span></span>

```razor
@code {
    protected override bool ShouldRender() => false;
}
```

<span data-ttu-id="d7c17-137">Wenn die Komponente nur ein erneutes Rendern erfordert, wenn die Parameterwerte auf bestimmte Weise geändert werden, können Sie die erforderlichen Informationen mit privaten Feldern nachverfolgen, um Änderungen zu erkennen.</span><span class="sxs-lookup"><span data-stu-id="d7c17-137">If the component only requires rerendering when its parameter values mutate in particular ways, then you can use private fields to track the necessary information to detect changes.</span></span> <span data-ttu-id="d7c17-138">Im folgenden Beispiel basiert `shouldRender` auf der Überprüfung auf beliebige Änderungen oder Mutationen, die ein erneutes Rendern auslösen sollten.</span><span class="sxs-lookup"><span data-stu-id="d7c17-138">In the following example, `shouldRender` is based on checking for any kind of change or mutation that should prompt a rerender.</span></span> <span data-ttu-id="d7c17-139">`prevOutboundFlightId` und `prevInboundFlightId` verfolgen Informationen für das nächste mögliche Update nach:</span><span class="sxs-lookup"><span data-stu-id="d7c17-139">`prevOutboundFlightId` and `prevInboundFlightId` track information for the next potential update:</span></span>

```razor
@code {
    [Parameter]
    public FlightInfo OutboundFlight { get; set; }
    
    [Parameter]
    public FlightInfo InboundFlight { get; set; }

    private int prevOutboundFlightId;
    private int prevInboundFlightId;
    private bool shouldRender;

    protected override void OnParametersSet()
    {
        shouldRender = OutboundFlight.FlightId != prevOutboundFlightId
            || InboundFlight.FlightId != prevInboundFlightId;

        prevOutboundFlightId = OutboundFlight.FlightId;
        prevInboundFlightId = InboundFlight.FlightId;
    }

    protected override void ShouldRender() => shouldRender;

    // Note that 
}
```

<span data-ttu-id="d7c17-140">Im vorhergehenden Code kann ein Ereignishandler auch `shouldRender` auf `true` festlegen, sodass die Komponente nach dem Ereignis erneut gerendert wird.</span><span class="sxs-lookup"><span data-stu-id="d7c17-140">In the preceding code, an event handler may also set `shouldRender` to `true` so that the component is rerendered after the event.</span></span>

<span data-ttu-id="d7c17-141">Bei den meisten Komponenten ist dieses Maß an manueller Steuerung nicht erforderlich.</span><span class="sxs-lookup"><span data-stu-id="d7c17-141">For most components, this level of manual control isn't necessary.</span></span> <span data-ttu-id="d7c17-142">Sie sollten das Überspringen des Renderns von Unterstrukturen nur dann in Erwägung ziehen, wenn das Rendern dieser Unterstrukturen besonders aufwändig ist und in der Benutzeroberfläche Verzögerungen verursacht.</span><span class="sxs-lookup"><span data-stu-id="d7c17-142">You should only be concerned about skipping rendering subtrees if those subtrees are particularly expensive to render and are causing UI lag.</span></span>

<span data-ttu-id="d7c17-143">Weitere Informationen finden Sie unter <xref:blazor/components/lifecycle>.</span><span class="sxs-lookup"><span data-stu-id="d7c17-143">For more information, see <xref:blazor/components/lifecycle>.</span></span>

::: moniker range=">= aspnetcore-5.0"

### <a name="virtualization"></a><span data-ttu-id="d7c17-144">Virtualisierung</span><span class="sxs-lookup"><span data-stu-id="d7c17-144">Virtualization</span></span>

<span data-ttu-id="d7c17-145">Wenn große Teile der Benutzeroberfläche in einer Schleife gerendert werden, z. B. Listen oder Raster mit Tausenden von Einträgen, kann die schiere Menge an Renderingvorgängen zu einer Verzögerung beim Rendern der Benutzeroberfläche und somit zu einer negativen Benutzererfahrung führen.</span><span class="sxs-lookup"><span data-stu-id="d7c17-145">When rendering large amounts of UI within a loop, for example a list or grid with thousands of entries, the sheer quantity of rendering operations can lead to a lag in UI rendering and thus a poor user experience.</span></span> <span data-ttu-id="d7c17-146">Da der Benutzer ohne Scrollen nur eine kleine Anzahl von Elementen gleichzeitig sehen kann, ist es offenbar Verschwendung, so viel Zeit in das Rendern von Elementen zu investieren, die derzeit nicht sichtbar sind.</span><span class="sxs-lookup"><span data-stu-id="d7c17-146">Given that the user can only see a small number of elements at once without scrolling, it seems wasteful to spend so much time rendering elements that aren't currently visible.</span></span>

<span data-ttu-id="d7c17-147">Darum stellt Blazor eine integrierte [`<Virtualize>`-Komponente](xref:blazor/components/virtualization) bereit, die das Aussehen und Scrollverhalten einer beliebig großen Liste definiert, aber tatsächlich nur die Listenelemente rendert, die sich innerhalb des aktuellen Scrollviewports befinden.</span><span class="sxs-lookup"><span data-stu-id="d7c17-147">To address this, Blazor provides a built-in [`<Virtualize>` component](xref:blazor/components/virtualization) that creates the appearance and scroll behaviors of an arbitrarily-large list but actually only renders the list items that are within the current scroll viewport.</span></span> <span data-ttu-id="d7c17-148">Dies bedeutet beispielsweise, dass nur der Aufwand zum Rendern von 20 Elementen erforderlich ist, die gleichzeitig sichtbar sind, wenn die App über eine Liste mit 100.000 Einträgen verfügt.</span><span class="sxs-lookup"><span data-stu-id="d7c17-148">For example, this means that the app can have a list with 100,000 entries but only pay the rendering cost of 20 items that are visible at any one time.</span></span> <span data-ttu-id="d7c17-149">Mit Verwendung der `<Virtualize>`-Komponente können Sie die Leistung der Benutzeroberfläche um Größenordnungen hochskalieren.</span><span class="sxs-lookup"><span data-stu-id="d7c17-149">Use of the `<Virtualize>` component can scale up UI performance by orders of magnitude.</span></span>

<span data-ttu-id="d7c17-150">`<Virtualize>` kann in folgenden Fällen verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="d7c17-150">`<Virtualize>` can be used when:</span></span>

 * <span data-ttu-id="d7c17-151">Rendern eines Satzes von Datenelementen in einer Schleife.</span><span class="sxs-lookup"><span data-stu-id="d7c17-151">Rendering a set of data items in a loop.</span></span>
 * <span data-ttu-id="d7c17-152">Die meisten Elemente sind beim Scrollen nicht sichtbar.</span><span class="sxs-lookup"><span data-stu-id="d7c17-152">Most of the items aren't visible due to scrolling.</span></span>
 * <span data-ttu-id="d7c17-153">Die gerenderten Elemente haben exakt die gleiche Größe.</span><span class="sxs-lookup"><span data-stu-id="d7c17-153">The rendered items are exactly the same size.</span></span> <span data-ttu-id="d7c17-154">Wenn der Benutzer zu einem beliebigen Punkt scrollt, kann die Komponente die anzuzeigenden sichtbaren Elemente berechnen.</span><span class="sxs-lookup"><span data-stu-id="d7c17-154">When the user scrolls to an arbitrary point, the component can calculate the visible items to show.</span></span>

<span data-ttu-id="d7c17-155">Es folgt ein Beispiel für eine nicht virtualisierte Liste:</span><span class="sxs-lookup"><span data-stu-id="d7c17-155">The following shows an example of a non-virtualized list:</span></span>

```razor
<div class="all-flights" style="height:500px;overflow-y:scroll">
    @foreach (var flight in allFlights)
    {
        <FlightSummary @key="flight.FlightId" Flight="@flight" />
    }
</div>
```

<span data-ttu-id="d7c17-156">Wenn die `allFlights`-Sammlung 10.000 Elemente enthält, instanziiert und rendert sie 10.000 `<FlightSummary>`-Komponenteninstanzen.</span><span class="sxs-lookup"><span data-stu-id="d7c17-156">If the `allFlights` collection holds 10,000 items, it instantiates and renders 10,000 `<FlightSummary>` component instances.</span></span> <span data-ttu-id="d7c17-157">Im Vergleich dazu ein Beispiel für eine virtualisierte Liste:</span><span class="sxs-lookup"><span data-stu-id="d7c17-157">In comparison, the following shows an example of a virtualized list:</span></span>

```razor
<div class="all-flights" style="height:500px;overflow-y:scroll">
    <Virtualize Items="@allFlights" Context="flight">
        <FlightSummary @key="flight.FlightId" Flight="@flight" />
    </Virtualize>
</div>
```

<span data-ttu-id="d7c17-158">Obwohl die resultierende Benutzeroberfläche für einen Benutzer gleich aussieht, instanziiert und rendert die Komponente im Hintergrund nur so viele `<FlightSummary>`-Instanzen, wie zum Ausfüllen des scrollbaren Bereichs benötigt werden.</span><span class="sxs-lookup"><span data-stu-id="d7c17-158">Even though the resulting UI looks the same to a user, behind the scenes the component only instantiates and renders as many `<FlightSummary>` instances as are required to fill the scrollable region.</span></span> <span data-ttu-id="d7c17-159">Der angezeigte Satz von `<FlightSummary>`-Instanzen wird neu berechnet und gerendert, wenn der Benutzer scrollt.</span><span class="sxs-lookup"><span data-stu-id="d7c17-159">The set of `<FlightSummary>` instances displayed is recalculated and rendered as the user scrolls.</span></span>

<span data-ttu-id="d7c17-160">`<Virtualize>` hat noch weitere Vorteile.</span><span class="sxs-lookup"><span data-stu-id="d7c17-160">`<Virtualize>` has other benefits, too.</span></span> <span data-ttu-id="d7c17-161">Wenn eine Komponente z. B. Daten von einer externen API anfordert, ermöglicht `<Virtualize>` der Komponente, nur das Segment von Datensätzen abzurufen, die dem aktuell sichtbaren Bereich entsprechen, anstatt alle Daten aus der Sammlung herunterzuladen.</span><span class="sxs-lookup"><span data-stu-id="d7c17-161">For example when a component requests data from an external API, `<Virtualize>` permits the component to only fetch the slice of records that correspond to the current visible region, instead of downloading all the data from the collection.</span></span>

<span data-ttu-id="d7c17-162">Weitere Informationen finden Sie unter <xref:blazor/components/virtualization>.</span><span class="sxs-lookup"><span data-stu-id="d7c17-162">For more information, see <xref:blazor/components/virtualization>.</span></span>

::: moniker-end

### <a name="create-lightweight-optimized-components"></a><span data-ttu-id="d7c17-163">Erstellen von einfachen optimierten Komponenten</span><span class="sxs-lookup"><span data-stu-id="d7c17-163">Create lightweight, optimized components</span></span>

<span data-ttu-id="d7c17-164">Die meisten Blazor-Komponenten erfordern keine aggressiven Optimierungsbemühungen.</span><span class="sxs-lookup"><span data-stu-id="d7c17-164">Most Blazor components don't require aggressive optimization efforts.</span></span> <span data-ttu-id="d7c17-165">Dies liegt daran, dass die meisten Komponenten sich nicht häufig in der Benutzeroberfläche wiederholen und nicht oft neu gerendert werden.</span><span class="sxs-lookup"><span data-stu-id="d7c17-165">This is because most components don't often repeat in the UI and don't rerender at high frequency.</span></span> <span data-ttu-id="d7c17-166">Beispielsweise werden `@page`-Komponenten und Komponenten, die Benutzeroberflächenelemente auf hoher Ebene darstellen (z. B. Dialoge oder Formulare) höchstwahrscheinlich nur einzeln und nur als Antwort auf eine Benutzeraktion erneut gerendert.</span><span class="sxs-lookup"><span data-stu-id="d7c17-166">For example, `@page` components and components representing high-level UI pieces such as dialogs or forms, most likely appear only one at a time and only rerender in response to a user gesture.</span></span> <span data-ttu-id="d7c17-167">Diese Komponenten verursachen keine großen Renderingworkloads, sodass Sie eine beliebige Kombination von Frameworkfeatures verwenden können, ohne sich besonders um die Renderleistung sorgen zu müssen.</span><span class="sxs-lookup"><span data-stu-id="d7c17-167">These components don't create a high rendering workload, so you can freely use any combination of framework features you want without worrying much about rendering performance.</span></span>

<span data-ttu-id="d7c17-168">Es gibt jedoch auch gängige Szenarien, in denen Sie Komponenten erstellen, die im großen Stil wiederholt werden müssen.</span><span class="sxs-lookup"><span data-stu-id="d7c17-168">However, there are also common scenarios where you build components that need to be repeated at scale.</span></span> <span data-ttu-id="d7c17-169">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="d7c17-169">For example:</span></span>

 * <span data-ttu-id="d7c17-170">Umfangreiche geschachtelte Formen können aus Hunderten von einzelnen Eingaben, Bezeichnungen und anderen Elementen bestehen.</span><span class="sxs-lookup"><span data-stu-id="d7c17-170">Large nested forms may have hundreds of individual inputs, labels, and other elements.</span></span>
 * <span data-ttu-id="d7c17-171">Raster verfügen möglicherweise über Tausende von Zellen.</span><span class="sxs-lookup"><span data-stu-id="d7c17-171">Grids may have thousands of cells.</span></span>
 * <span data-ttu-id="d7c17-172">Streudiagramme können Millionen von Datenpunkten enthalten.</span><span class="sxs-lookup"><span data-stu-id="d7c17-172">Scatter plots may have millions of data points.</span></span>

<span data-ttu-id="d7c17-173">Wenn jede Einheit als separate Komponenteninstanz modelliert wird, entstehen so viele, dass Probleme bei der Renderingleistung auftreten können.</span><span class="sxs-lookup"><span data-stu-id="d7c17-173">If modelling each unit as separate component instances, there will be so many of them that their rendering performance does become critical.</span></span> <span data-ttu-id="d7c17-174">In diesem Abschnitt wird erläutert, wie Sie solche Komponenten vereinfachen, sodass die Benutzeroberfläche schnell und reaktionsfähig bleibt.</span><span class="sxs-lookup"><span data-stu-id="d7c17-174">This section provides advice on making such components lightweight so that the UI remains fast and responsive.</span></span>

#### <a name="avoid-thousands-of-component-instances"></a><span data-ttu-id="d7c17-175">Vermeiden von Tausenden von Komponenteninstanzen</span><span class="sxs-lookup"><span data-stu-id="d7c17-175">Avoid thousands of component instances</span></span>

<span data-ttu-id="d7c17-176">Jede Komponente ist eine separate Insel, die unabhängig von ihren übergeordneten und untergeordneten Elementen gerendert werden kann.</span><span class="sxs-lookup"><span data-stu-id="d7c17-176">Each component is a separate island that can render independently of its parents and children.</span></span> <span data-ttu-id="d7c17-177">Indem Sie auswählen, wie die Benutzeroberfläche in eine Hierarchie von Komponenten aufgeteilt werden soll, bestimmen Sie die Granularität des Renderings der Benutzeroberfläche.</span><span class="sxs-lookup"><span data-stu-id="d7c17-177">By choosing how to split up the UI into a hierarchy of components, you are taking control over the granularity of UI rendering.</span></span> <span data-ttu-id="d7c17-178">Dies kann für die Leistung entweder gut oder schlecht sein.</span><span class="sxs-lookup"><span data-stu-id="d7c17-178">This can be either good or bad for performance.</span></span>

 * <span data-ttu-id="d7c17-179">Wenn Sie die Benutzeroberfläche in mehr Komponenten aufteilen, können kleinere Teile der Benutzeroberfläche erneut gerendert werden, wenn Ereignisse auftreten.</span><span class="sxs-lookup"><span data-stu-id="d7c17-179">By splitting the UI into more components, you can have smaller portions of the UI rerender when events occur.</span></span> <span data-ttu-id="d7c17-180">Wenn ein Benutzer z. B. auf eine Schaltfläche in einer Tabellenzeile klickt, können Sie vielleicht nur diese einzelne Zeile anstelle der gesamten Seite oder Tabelle erneut rendern lassen.</span><span class="sxs-lookup"><span data-stu-id="d7c17-180">For example when a user clicks a button in a table row, you may be able to have only that single row rerender instead of the whole page or table.</span></span>
 * <span data-ttu-id="d7c17-181">Jede zusätzliche Komponente benötigt jedoch zusätzlichen Arbeitsspeicher und CPU-Mehraufwand, um den unabhängigen Zustand und den Renderinglebenszyklus zu verwalten.</span><span class="sxs-lookup"><span data-stu-id="d7c17-181">However, each extra component involves some extra memory and CPU overhead to deal with its independent state and rendering lifecycle.</span></span>

<span data-ttu-id="d7c17-182">Beim Optimieren der Leistung von Blazor WebAssembly in .NET 5 haben wir einen Renderingmehraufwand von ca. 0,06 ms pro Komponenteninstanz gemessen.</span><span class="sxs-lookup"><span data-stu-id="d7c17-182">When tuning the performance of Blazor WebAssembly on .NET 5, we measured a rendering overhead of around 0.06 ms per component instance.</span></span> <span data-ttu-id="d7c17-183">Dies basiert bei Ausführung auf einem typischen Laptop auf einer einfachen Komponente, die drei Parameter akzeptiert.</span><span class="sxs-lookup"><span data-stu-id="d7c17-183">This is based on a simple component that accepts three parameters running on a typical laptop.</span></span> <span data-ttu-id="d7c17-184">Intern entsteht der Mehraufwand größtenteils durch das Abrufen des Status pro Komponente aus Wörterbüchern und Übergabe und Empfang von Parametern.</span><span class="sxs-lookup"><span data-stu-id="d7c17-184">Internally, the overhead is largely due to retrieving per-component state from dictionaries and passing and receiving parameters.</span></span> <span data-ttu-id="d7c17-185">Beim Hinzufügen 2.000 zusätzlicher Komponenteninstanzen ergibt sich durch Multiplikation, dass die Renderingzeit um 0,12 Sekunden verlängert und die Benutzeroberfläche den Benutzern langsam erscheinen würde.</span><span class="sxs-lookup"><span data-stu-id="d7c17-185">By multiplication, you can see that adding 2,000 extra component instances would add 0.12 seconds to the rendering time and the UI would begin feeling slow to users.</span></span>

<span data-ttu-id="d7c17-186">Komponenten können einfacher gestaltet werden, sodass Sie mehr von Ihnen haben können, aber häufig besteht die leistungsfähigere Methode darin, nicht so viele Komponenten zu haben.</span><span class="sxs-lookup"><span data-stu-id="d7c17-186">It's possible to make components more lightweight so that you can have more of them, but often the more powerful technique is not to have so many components.</span></span> <span data-ttu-id="d7c17-187">In den folgenden Abschnitten werden zwei Ansätze beschrieben.</span><span class="sxs-lookup"><span data-stu-id="d7c17-187">The following sections describe two approaches.</span></span>

##### <a name="inline-child-components-into-their-parents"></a><span data-ttu-id="d7c17-188">Inline-Einfügung untergeordneter Komponenten in ihre übergeordneten Komponenten</span><span class="sxs-lookup"><span data-stu-id="d7c17-188">Inline child components into their parents</span></span>

<span data-ttu-id="d7c17-189">Sehen Sie sich die folgende Komponente an, die eine Sequenz von untergeordneten Komponenten rendert:</span><span class="sxs-lookup"><span data-stu-id="d7c17-189">Consider the following component that renders a sequence of child components:</span></span>

```razor
<div class="chat">
    @foreach (var message in messages)
    {
        <ChatMessageDisplay Message="@message" />
    }
</div>
```

<span data-ttu-id="d7c17-190">Für den vorhergehenden Beispielcode wird die `<ChatMessageDisplay>`-Komponente in einer Datei `ChatMessageDisplay.razor` definiert, die Folgendes enthält:</span><span class="sxs-lookup"><span data-stu-id="d7c17-190">For the preceding example code, the `<ChatMessageDisplay>` component is defined in a file `ChatMessageDisplay.razor` containing:</span></span>

```razor
<div class="chat-message">
    <span class="author">@Message.Author</span>
    <span class="text">@Message.Text</span>
</div>

@code {
    [Parameter]
    public ChatMessage Message { get; set; }
}
```

<span data-ttu-id="d7c17-191">Das vorhergehende Beispiel funktioniert problemlos und mit guter Leistung, solange nicht Tausende von Nachrichten gleichzeitig angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="d7c17-191">The preceding example works fine and performs well as long as thousands of messages aren't shown at once.</span></span> <span data-ttu-id="d7c17-192">Um Tausende von Nachrichten gleichzeitig anzuzeigen, sollten Sie die separate `ChatMessageDisplay`-Komponente *nicht* ausklammern.</span><span class="sxs-lookup"><span data-stu-id="d7c17-192">To show thousands of messages at once, consider *not* factoring out the separate `ChatMessageDisplay` component.</span></span> <span data-ttu-id="d7c17-193">Nehmen Sie stattdessen eine direkte Inline-Einfügung des Renderings in das übergeordnete Element vor:</span><span class="sxs-lookup"><span data-stu-id="d7c17-193">Instead, inline the rendering directly into the parent:</span></span>

```razor
<div class="chat">
    @foreach (var message in messages)
    {
        <div class="chat-message">
            <span class="author">@message.Author</span>
            <span class="text">@message.Text</span>
        </div>
    }
</div>
```

<span data-ttu-id="d7c17-194">Dadurch wird der Renderingmehraufwand pro Komponente für so viele untergeordnete Komponenten vermieden zu dem Preis, sie nicht unabhängig voneinander einzeln erneut rendern zu können.</span><span class="sxs-lookup"><span data-stu-id="d7c17-194">This avoids the per-component overhead of rendering so many child components at the cost of not being able to rerender each of them independently.</span></span>

##### <a name="define-reusable-renderfragments-in-code"></a><span data-ttu-id="d7c17-195">Definieren wiederverwendbarer `RenderFragments` im Code</span><span class="sxs-lookup"><span data-stu-id="d7c17-195">Define reusable `RenderFragments` in code</span></span>

<span data-ttu-id="d7c17-196">Sie können untergeordnete Komponenten ausschließlich als Möglichkeit zur Wiederverwendung von Renderinglogik ausklammern.</span><span class="sxs-lookup"><span data-stu-id="d7c17-196">You may be factoring out child components purely as a way of reusing rendering logic.</span></span> <span data-ttu-id="d7c17-197">Wenn dies der Fall ist, können Sie weiterhin die Renderinglogik wiederverwenden, ohne die tatsächlichen Komponenten zu deklarieren.</span><span class="sxs-lookup"><span data-stu-id="d7c17-197">If that's the case, it's still possible to reuse rendering logic without declaring actual components.</span></span> <span data-ttu-id="d7c17-198">Im `@code`-Block einer Komponente können Sie ein <xref:Microsoft.AspNetCore.Components.RenderFragment> definieren, das die Benutzeroberfläche ausgibt und von überall aus aufgerufen werden kann:</span><span class="sxs-lookup"><span data-stu-id="d7c17-198">In any component's `@code` block, you can define a <xref:Microsoft.AspNetCore.Components.RenderFragment> that emits UI and can be called from anywhere:</span></span>

```razor
<h1>Hello, world!</h1>

@RenderWelcomeInfo

@code {
    RenderFragment RenderWelcomeInfo = __builder =>
    {
        <div>
            <p>Welcome to your new app!</p>

            <SurveyPrompt Title="How is Blazor working for you?" />
        </div>
    };
}
```

<span data-ttu-id="d7c17-199">Wie im vorhergehenden Beispiel gezeigt, können Komponenten Markup aus Code in ihrem `@code`-Block und außerhalb davon ausgeben.</span><span class="sxs-lookup"><span data-stu-id="d7c17-199">As demonstated in the preceding example, components can emit markup from code within their `@code` block and outside it.</span></span> <span data-ttu-id="d7c17-200">Diese Vorgehensweise definiert einen <xref:Microsoft.AspNetCore.Components.RenderFragment>-Delegaten, den Sie in der normalen Renderingausgabe der Komponente optional an mehreren Stellen rendern können.</span><span class="sxs-lookup"><span data-stu-id="d7c17-200">This approach defines a <xref:Microsoft.AspNetCore.Components.RenderFragment> delegate that you can render inside the component's normal render output, optionally in multiple places.</span></span> <span data-ttu-id="d7c17-201">Der Delegat muss einen Parameter mit dem Namen `__builder` vom Typ <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> akzeptieren, damit der Razor-Compiler Renderinganweisungen für ihn erzeugen kann.</span><span class="sxs-lookup"><span data-stu-id="d7c17-201">It's necessary for the delegate to accept a parameter called `__builder` of type <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> so that the Razor compiler can produce rendering instructions for it.</span></span>

<span data-ttu-id="d7c17-202">Wenn dieser für mehrere Komponenten wiederverwendbar sein soll, sollten Sie ihn als `public static`-Member deklarieren:</span><span class="sxs-lookup"><span data-stu-id="d7c17-202">If you want to make this reusable across multiple components, consider declaring it as a `public static` member:</span></span>

```razor
public static RenderFragment SayHello = __builder =>
{
    <h1>Hello!</h1>
};
```

<span data-ttu-id="d7c17-203">Dieser könnte nun von einer nicht verknüpften Komponente aus aufgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="d7c17-203">This could now be invoked from an unrelated component.</span></span> <span data-ttu-id="d7c17-204">Dieses Verfahren eignet sich für das Erstellen von Bibliotheken von wiederverwendbaren Markupausschnitten, die ohne Mehraufwand pro Komponente gerendert werden können.</span><span class="sxs-lookup"><span data-stu-id="d7c17-204">This technique is useful for building libraries of reusable markup snippets that render without any per-component overhead.</span></span>

<span data-ttu-id="d7c17-205"><xref:Microsoft.AspNetCore.Components.RenderFragment>-Delegate können auch Parameter akzeptieren.</span><span class="sxs-lookup"><span data-stu-id="d7c17-205"><xref:Microsoft.AspNetCore.Components.RenderFragment> delegates can also accept parameters.</span></span> <span data-ttu-id="d7c17-206">So erstellen Sie das Äquivalent der `ChatMessageDisplay`-Komponente aus dem vorherigen Beispiel:</span><span class="sxs-lookup"><span data-stu-id="d7c17-206">To create the equivalent of the `ChatMessageDisplay` component from the earlier example:</span></span>

```razor
<div class="chat">
    @foreach (var message in messages)
    {
        @DisplayChatMessage(message)
    }
</div>

@code {
    RenderFragment<ChatMessage> DisplayChatMessage = message => __builder =>
    {
        <div class="chat-message">
            <span class="author">@message.Author</span>
            <span class="text">@message.Text</span>
        </div>
    };
}
```

<span data-ttu-id="d7c17-207">Dieser Ansatz bietet den Vorteil, dass Renderinglogik ohne Mehraufwand pro Komponente gerendert werden kann.</span><span class="sxs-lookup"><span data-stu-id="d7c17-207">This approach provides the benefit of reusing rendering logic without per-component overhead.</span></span> <span data-ttu-id="d7c17-208">Allerdings hat er weder den Vorteil, dass seine Unterstruktur der Benutzeroberfläche unabhängig aktualisiert werden kann, noch kann das Rendering dieser Unterstruktur der Benutzeroberfläche übersprungen werden, wenn ihr übergeordnetes Element gerendert wird, da es keine Komponentenbegrenzung gibt.</span><span class="sxs-lookup"><span data-stu-id="d7c17-208">However, it doesn't have the benefit of being able to refresh its subtree of the UI independently, nor does it have the ability to skip rendering that subtree of the UI when its parent renders, since there's no component boundary.</span></span>

#### <a name="dont-receive-too-many-parameters"></a><span data-ttu-id="d7c17-209">Nicht zu viele Parameter empfangen</span><span class="sxs-lookup"><span data-stu-id="d7c17-209">Don't receive too many parameters</span></span>

<span data-ttu-id="d7c17-210">Wenn eine Komponente extrem häufig wiederholt wird, z. B. Hunderte oder Tausende Mal, sollten Sie bedenken, dass der Mehraufwand für das Übergeben und Empfangen der einzelnen Parameter kumuliert.</span><span class="sxs-lookup"><span data-stu-id="d7c17-210">If a component repeats extremely often, for example hundreds or thousands of times, then bear in mind that the overhead of passing and receiving each parameter builds up.</span></span>

<span data-ttu-id="d7c17-211">Es ist selten, dass zu viele Parameter die Leistung erheblich einschränken, aber es kann ein Faktor sein.</span><span class="sxs-lookup"><span data-stu-id="d7c17-211">It's rare that too many parameters severely restricts performance, but it can be a factor.</span></span> <span data-ttu-id="d7c17-212">Bei einer `<TableCell>`-Komponente, die 1.000 mal innerhalb eines Rasters gerendert wird, könnte jeder zusätzliche übergebene Parameter den gesamten Renderingaufwand um ungefähr 15 ms erhöhen.</span><span class="sxs-lookup"><span data-stu-id="d7c17-212">For a `<TableCell>` component that renders 1,000 times within a grid, each extra parameter passed to it could add around 15 ms to the total rendering cost.</span></span> <span data-ttu-id="d7c17-213">Wenn jede Zelle 10 Parameter akzeptiert, erfordert die Parameterübergabe ungefähr 150 ms pro Komponentenrendering, somit möglicherweise 150.000 ms (150 Sekunden) und führt von sich aus zu einer langsamen Benutzeroberfläche.</span><span class="sxs-lookup"><span data-stu-id="d7c17-213">If each cell accepted 10 parameters, parameter passing takes around 150 ms per component render and  thus perhaps 150,000 ms (150 seconds) and on its own cause a laggy UI.</span></span>

<span data-ttu-id="d7c17-214">Um diese Auslastung zu reduzieren, könnten Sie mehrere Parameter über benutzerdefinierte Klassen bündeln.</span><span class="sxs-lookup"><span data-stu-id="d7c17-214">To reduce this load, you could bundle together multiple parameters via custom classes.</span></span> <span data-ttu-id="d7c17-215">Beispielsweise könnte eine `<TableCell>`-Komponente Folgendes akzeptieren:</span><span class="sxs-lookup"><span data-stu-id="d7c17-215">For example, a `<TableCell>` component might accept:</span></span>

```razor
@typeparam TItem

...

@code {
    [Parameter]
    public TItem Data { get; set; }
    
    [Parameter]
    public GridOptions Options { get; set; }
}
```

<span data-ttu-id="d7c17-216">Im vorherigen Beispiel ist `Data` für jede Zelle anders, aber `Options` ist für alle gleich.</span><span class="sxs-lookup"><span data-stu-id="d7c17-216">In the preceding example, `Data` is different for every cell, but `Options` is common across all of them.</span></span> <span data-ttu-id="d7c17-217">Natürlich kann es eine Verbesserung sein, keine `<TableCell>`-Komponente zu haben und stattdessen ihre Logik inline in die übergeordnete Komponente einzufügen.</span><span class="sxs-lookup"><span data-stu-id="d7c17-217">Of course, it might be an improvement not to have a `<TableCell>` component and instead inline its logic into the parent component.</span></span>

#### <a name="ensure-cascading-parameters-are-fixed"></a><span data-ttu-id="d7c17-218">Sicherstellen, dass kaskadierende Parameter korrigiert sind</span><span class="sxs-lookup"><span data-stu-id="d7c17-218">Ensure cascading parameters are fixed</span></span>

<span data-ttu-id="d7c17-219">Die `<CascadingValue>`-Komponente hat einen optionalen Parameter namens `IsFixed`.</span><span class="sxs-lookup"><span data-stu-id="d7c17-219">The `<CascadingValue>` component has an optional parameter called `IsFixed`.</span></span>

 * <span data-ttu-id="d7c17-220">Wenn der `IsFixed`-Wert `false` ist (Standard), richtet jeder Empfänger des kaskadierenden Werts ein Abonnement ein, um Änderungsbenachrichtigungen zu empfangen.</span><span class="sxs-lookup"><span data-stu-id="d7c17-220">If the `IsFixed` value is `false` (the default), then every recipient of the cascaded value sets up a subscription to receive change notifications.</span></span> <span data-ttu-id="d7c17-221">In diesem Fall erfordert jeder `[CascadingParameter]` aufgrund der Abonnementnachverfolgung **erheblich mehr Aufwand** als ein regulärer `[Parameter]`.</span><span class="sxs-lookup"><span data-stu-id="d7c17-221">In this case, each `[CascadingParameter]` is **substantially more expensive** than a regular `[Parameter]` due to the subscription tracking.</span></span>
 * <span data-ttu-id="d7c17-222">Wenn der `IsFixed`-Wert `true` ist (z. B. `<CascadingValue Value="@someValue" IsFixed="true">`), dann empfangen Empfänger den Anfangswert, richten aber *kein* Abonnement für den Empfang von Updates ein.</span><span class="sxs-lookup"><span data-stu-id="d7c17-222">If the `IsFixed` value is `true` (for example, `<CascadingValue Value="@someValue" IsFixed="true">`), then receipients receive the initial value but do *not* set up any subscription to receive updates.</span></span> <span data-ttu-id="d7c17-223">In diesem Fall ist jeder `[CascadingParameter]` einfach und **nicht aufwändiger** als ein regulärer `[Parameter]`.</span><span class="sxs-lookup"><span data-stu-id="d7c17-223">In this case, each `[CascadingParameter]` is lightweight and **no more expensive** than a regular `[Parameter]`.</span></span>

<span data-ttu-id="d7c17-224">Daher sollten Sie bei kaskadierenden Werten nach Möglichkeit `IsFixed="true"` verwenden.</span><span class="sxs-lookup"><span data-stu-id="d7c17-224">So wherever possible, you should use `IsFixed="true"` on cascaded values.</span></span> <span data-ttu-id="d7c17-225">Dies ist möglich, wenn sich der angegebene Wert nicht im Laufe der Zeit ändert.</span><span class="sxs-lookup"><span data-stu-id="d7c17-225">You can do this whenever the value being supplied doesn't change over time.</span></span> <span data-ttu-id="d7c17-226">In dem allgemeinen Muster, bei dem eine Komponente `this` als kaskadierenden Wert übergibt, sollten Sie `IsFixed="true"` verwenden:</span><span class="sxs-lookup"><span data-stu-id="d7c17-226">In the common pattern where a component passes `this` as a cascaded value, you should use `IsFixed="true"`:</span></span>

```razor
<CascadingValue Value="this" IsFixed="true">
    <SomeOtherComponents>
</CascadingValue>
```

<span data-ttu-id="d7c17-227">Dies ist ein großer Unterschied, wenn eine große Anzahl anderer Komponenten vorhanden ist, die den kaskadierenden Wert empfangen.</span><span class="sxs-lookup"><span data-stu-id="d7c17-227">This makes a huge difference if there are a large number of other components that receive the cascaded value.</span></span> <span data-ttu-id="d7c17-228">Weitere Informationen finden Sie unter <xref:blazor/components/cascading-values-and-parameters>.</span><span class="sxs-lookup"><span data-stu-id="d7c17-228">For more information, see <xref:blazor/components/cascading-values-and-parameters>.</span></span>

#### <a name="avoid-attribute-splatting-with-captureunmatchedvalues"></a><span data-ttu-id="d7c17-229">Vermeiden des Attribut-Splattings mit `CaptureUnmatchedValues`</span><span class="sxs-lookup"><span data-stu-id="d7c17-229">Avoid attribute splatting with `CaptureUnmatchedValues`</span></span>

<span data-ttu-id="d7c17-230">Komponenten können mithilfe des <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues>-Flags auswählen, „nicht übereinstimmende“ Parameterwerte zu empfangen:</span><span class="sxs-lookup"><span data-stu-id="d7c17-230">Components can elect to receive "unmatched" parameter values using the <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> flag:</span></span>

```razor
<div @attributes="OtherAttributes">...</div>

@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public IDictionary<string, object> OtherAttributes { get; set; }
}
```

<span data-ttu-id="d7c17-231">Dieser Ansatz ermöglicht, beliebige zusätzliche Attribute an das Element zu übergeben.</span><span class="sxs-lookup"><span data-stu-id="d7c17-231">This approach allows passing through arbitrary additional attributes to the element.</span></span> <span data-ttu-id="d7c17-232">Er ist aber auch sehr aufwändig, da der Renderer Folgendes ausführen muss:</span><span class="sxs-lookup"><span data-stu-id="d7c17-232">However, it is also quite expensive because the renderer must:</span></span>

* <span data-ttu-id="d7c17-233">Abgleichen aller angegebenen Parameter mit dem Satz bekannter Parameter, um ein Wörterbuch zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="d7c17-233">Match all of the supplied parameters against the set of known parameters to build a dictionary.</span></span>
* <span data-ttu-id="d7c17-234">Nachverfolgen, wie viele Kopien desselben Attributs sich gegenseitig überschreiben.</span><span class="sxs-lookup"><span data-stu-id="d7c17-234">Keep track of how multiple copies of the same attribute overwrite each other.</span></span>

<span data-ttu-id="d7c17-235">Verwenden Sie <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> für nicht leistungskritische Komponenten, z. B. solche, die nicht häufig wiederholt werden.</span><span class="sxs-lookup"><span data-stu-id="d7c17-235">Feel free to use <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> on non-performance-critical components, such as ones that are not repeated frequently.</span></span> <span data-ttu-id="d7c17-236">Versuchen Sie jedoch bei Komponenten, die in großem Umfang gerendert werden, wie z. B. alle Elemente in einer umfangreichen Liste oder Zellen in einem Raster, das Attribut-Splatting zu vermeiden.</span><span class="sxs-lookup"><span data-stu-id="d7c17-236">However for components that render at scale, such as each items in a large list or cells in a grid, try to avoid attribute splatting.</span></span>

<span data-ttu-id="d7c17-237">Weitere Informationen finden Sie unter <xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters>.</span><span class="sxs-lookup"><span data-stu-id="d7c17-237">For more information, see <xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters>.</span></span>

#### <a name="implement-setparametersasync-manually"></a><span data-ttu-id="d7c17-238">Manuelles Implementieren von `SetParametersAsync`</span><span class="sxs-lookup"><span data-stu-id="d7c17-238">Implement `SetParametersAsync` manually</span></span>

<span data-ttu-id="d7c17-239">Einer der Hauptaspekte des Renderingmehraufwands pro Komponente ist das Schreiben eingehender Parameterwerte in die `[Parameter]`-Eigenschaften.</span><span class="sxs-lookup"><span data-stu-id="d7c17-239">One of the main aspects of the per-component rendering overhead is writing incoming parameter values to the `[Parameter]` properties.</span></span> <span data-ttu-id="d7c17-240">Der Renderer muss hierfür die Reflektion verwenden.</span><span class="sxs-lookup"><span data-stu-id="d7c17-240">The renderer has to use reflection to do this.</span></span> <span data-ttu-id="d7c17-241">Obwohl dies etwas optimiert ist, erzwingt das Fehlen der JIT-Unterstützung für die WebAssembly-Laufzeit Grenzwerte.</span><span class="sxs-lookup"><span data-stu-id="d7c17-241">Even though this is somewhat optimized, the absence of JIT support on the WebAssembly runtime imposes limits.</span></span>

<span data-ttu-id="d7c17-242">In einigen Extremfällen möchten Sie möglicherweise die Reflektion vermeiden und ihre eigene Logik für die Parametereinstellung manuell implementieren.</span><span class="sxs-lookup"><span data-stu-id="d7c17-242">In some extreme cases, you may wish to avoid the reflection and implement your own parameter setting logic manually.</span></span> <span data-ttu-id="d7c17-243">Dies kann unter folgenden Umständen anwendbar sein:</span><span class="sxs-lookup"><span data-stu-id="d7c17-243">This may be applicable when:</span></span>

 * <span data-ttu-id="d7c17-244">Sie verfügen über eine Komponente, die sehr häufig gerendert wird (z. B. gibt es Hunderte oder Tausende Kopien davon in der Benutzeroberfläche).</span><span class="sxs-lookup"><span data-stu-id="d7c17-244">You have a component that renders extremely often (for example, there are hundreds or thousands of copies of it in the UI).</span></span>
 * <span data-ttu-id="d7c17-245">Sie akzeptiert viele Parameter.</span><span class="sxs-lookup"><span data-stu-id="d7c17-245">It accepts many parameters.</span></span>
 * <span data-ttu-id="d7c17-246">Sie finden, dass der Mehraufwand für das Empfangen von Parametern die Reaktionsfähigkeit der Benutzeroberfläche merklich beeinträchtigt.</span><span class="sxs-lookup"><span data-stu-id="d7c17-246">You find that the overhead of receiving parameters has an observable impact on UI responsiveness.</span></span>

<span data-ttu-id="d7c17-247">In diesen Fällen können Sie die virtuelle <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A>-Methode der Komponente überschreiben und ihre eigene komponentenspezifische Logik implementieren.</span><span class="sxs-lookup"><span data-stu-id="d7c17-247">In these cases, you can override the component's virtual <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> method and implement your own component-specific logic.</span></span> <span data-ttu-id="d7c17-248">Im folgenden Beispiel werden absichtlich Wörterbuch-Suchvorgänge vermieden:</span><span class="sxs-lookup"><span data-stu-id="d7c17-248">The following example deliberately avoids any dictionary lookups:</span></span>

```razor
@code {
    [Parameter]
    public int MessageId { get; set; }

    [Parameter]
    public string Text { get; set; }

    [Parameter]
    public EventCallback<string> TextChanged { get; set; }

    [Parameter]
    public Theme CurrentTheme { get; set; }

    public override Task SetParametersAsync(ParameterView parameters)
    {
        foreach (var parameter in parameters)
        {
            switch (parameter.Name)
            {
                case nameof(MessageId):
                    MessageId = (int)parameter.Value;
                    break;
                case nameof(Text):
                    Text = (string)parameter.Value;
                    break;
                case nameof(TextChanged):
                    TextChanged = (EventCallback<string>)parameter.Value;
                    break;
                case nameof(CurrentTheme):
                    CurrentTheme = (Theme)parameter.Value;
                    break;
                default:
                    throw new ArgumentException($"Unknown parameter: {parameter.Name}");
            }
        }

        return base.SetParametersAsync(ParameterView.Empty);
    }
}
```

<span data-ttu-id="d7c17-249">Im vorhergehenden Code führt die Rückgabe der Basisklasse <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> die normalen Lebenszyklusmethoden aus, ohne dass Parameter erneut zugewiesen werden.</span><span class="sxs-lookup"><span data-stu-id="d7c17-249">In the preceding code, returning the base class <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> runs the normal lifecycle methods without assigning parameters again.</span></span>

<span data-ttu-id="d7c17-250">Wie Sie im vorhergehenden Code sehen können, ist das Überschreiben von <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> und das Bereitstellen benutzerdefinierter Logik kompliziert und aufwändig, daher wird dieser Ansatz im Allgemeinen nicht empfohlen.</span><span class="sxs-lookup"><span data-stu-id="d7c17-250">As you can see in the preceding code, overriding <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> and supplying custom logic is complicated and laborious, so we don't recommend this approach in general.</span></span> <span data-ttu-id="d7c17-251">In Extremfällen kann die Renderingleistung um 20-25 % verbessert werden, aber Sie sollten diesen Ansatz nur in den zuvor aufgeführten Szenarien berücksichtigen.</span><span class="sxs-lookup"><span data-stu-id="d7c17-251">In extreme cases, it can improve rendering performance by 20-25%, but you should only consider this approach in the scenarios listed earlier.</span></span>

### <a name="dont-trigger-events-too-rapidly"></a><span data-ttu-id="d7c17-252">Ereignisse nicht zu schnell auslösen</span><span class="sxs-lookup"><span data-stu-id="d7c17-252">Don't trigger events too rapidly</span></span>

<span data-ttu-id="d7c17-253">Einige Browserereignisse wie `onmousemove` und `onscroll` werden sehr häufig ausgelöst, Dutzende oder Hunderte Mal pro Sekunde.</span><span class="sxs-lookup"><span data-stu-id="d7c17-253">Some browser events fire extremely frequently, for example `onmousemove` and `onscroll`, which can fire tens or hundreds of times per second.</span></span> <span data-ttu-id="d7c17-254">In den meisten Fällen muss die Aktualisierung der Benutzeroberfläche nicht so häufig ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="d7c17-254">In most cases, you don't need to perform UI updates this frequently.</span></span> <span data-ttu-id="d7c17-255">Wenn Sie dies versuchen, können Sie die Reaktionsfähigkeit der Benutzeroberfläche beeinträchtigen oder übermäßig viel CPU-Zeit verbrauchen.</span><span class="sxs-lookup"><span data-stu-id="d7c17-255">If you try to do so, you may harm UI responsiveness or consume excessive CPU time.</span></span>

<span data-ttu-id="d7c17-256">Anstatt native `@onmousemove`- oder `@onscroll`-Ereignisse zu verwenden, sollten Sie vorzugsweise JS-Interop verwenden, um einen Rückruf zu registrieren, der seltener ausgelöst wird.</span><span class="sxs-lookup"><span data-stu-id="d7c17-256">Rather than using native `@onmousemove` or `@onscroll` events, you may prefer to use JS interop to register a callback that fires less frequently.</span></span> <span data-ttu-id="d7c17-257">Beispielsweise zeigt die folgende Komponente (`MyComponent.razor`) die Position der Maus an, aktualisiert jedoch nur höchstens einmal alle 500 ms:</span><span class="sxs-lookup"><span data-stu-id="d7c17-257">For example, the following component (`MyComponent.razor`) displays the position of the mouse but only updates at most once every 500 ms:</span></span>

```razor
@inject IJSRuntime JS
@implements IDisposable

<h1>@message</h1>

<div @ref="myMouseMoveElement" style="border:1px dashed red;height:200px;">
    Move mouse here
</div>

@code {
    ElementReference myMouseMoveElement;
    DotNetObjectReference<MyComponent> selfReference;
    private string message = "Move the mouse in the box";

    [JSInvokable]
    public void HandleMouseMove(int x, int y)
    {
        message = $"Mouse move at {x}, {y}";
        StateHasChanged();
    }

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            selfReference = DotNetObjectReference.Create(this);
            var minInterval = 500; // Only notify every 500 ms
            await JS.InvokeVoidAsync("onThrottledMouseMove", 
                myMouseMoveElement, selfReference, minInterval);
        }
    }

    public void Dispose() => selfReference?.Dispose();
}
```

<span data-ttu-id="d7c17-258">Der entsprechende JavaScript-Code, der auf der Seite `index.html` platziert oder als ES6-Modul geladen werden kann, registriert den eigentlichen DOM-Ereignislistener.</span><span class="sxs-lookup"><span data-stu-id="d7c17-258">The corresponding JavaScript code, which can be placed in the `index.html` page or loaded as an ES6 module, registers the actual DOM event listener.</span></span> <span data-ttu-id="d7c17-259">In diesem Beispiel verwendet der Ereignislistener die [`throttle`-Funktion von Lodash](https://lodash.com/docs/4.17.15#throttle), um die Rate der Aufrufe einzuschränken:</span><span class="sxs-lookup"><span data-stu-id="d7c17-259">In this example, the event listener uses [Lodash's `throttle` function](https://lodash.com/docs/4.17.15#throttle) to limit the rate of invocations:</span></span>

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/lodash.js/4.17.20/lodash.min.js"></script>
<script>
  function onThrottledMouseMove(elem, component, interval) {
    elem.addEventListener('mousemove', _.throttle(e => {
      component.invokeMethodAsync('HandleMouseMove', e.offsetX, e.offsetY);
    }, interval));
  }
</script>
```

<span data-ttu-id="d7c17-260">Diese Technik kann für Blazor Server sogar noch wichtiger sein, weil jeder Ereignisaufruf mit dem Senden einer Nachricht über das Netzwerk verbunden ist.</span><span class="sxs-lookup"><span data-stu-id="d7c17-260">This technique can be even more important for Blazor Server, since each event invocation involves delivering a message over the network.</span></span> <span data-ttu-id="d7c17-261">Sie ist für Blazor WebAssembly nützlich, da sie den Renderingaufwand erheblich reduzieren kann.</span><span class="sxs-lookup"><span data-stu-id="d7c17-261">It's valuable for Blazor WebAssembly because it can greatly reduce the amount of rendering work.</span></span>

## <a name="optimize-javascript-interop-speed"></a><span data-ttu-id="d7c17-262">Optimieren der JavaScript-Interop-Geschwindigkeit</span><span class="sxs-lookup"><span data-stu-id="d7c17-262">Optimize JavaScript interop speed</span></span>

<span data-ttu-id="d7c17-263">Aufrufe zwischen .NET und JavaScript sind aus folgenden Gründen mit zusätzlichem Mehraufwand verbunden:</span><span class="sxs-lookup"><span data-stu-id="d7c17-263">Calls between .NET and JavaScript involve some additional overhead because:</span></span>

 * <span data-ttu-id="d7c17-264">Standardmäßig sind Aufrufe asynchron.</span><span class="sxs-lookup"><span data-stu-id="d7c17-264">By default, calls are asynchronous.</span></span>
 * <span data-ttu-id="d7c17-265">Standardmäßig werden Parameter und Rückgabewerte JSON-serialisiert.</span><span class="sxs-lookup"><span data-stu-id="d7c17-265">By default, parameters and return values are JSON-serialized.</span></span> <span data-ttu-id="d7c17-266">Dies soll einen leicht verständlichen Konvertierungsmechanismus zwischen .NET- und JavaScript-Typen bieten.</span><span class="sxs-lookup"><span data-stu-id="d7c17-266">This is to provide an easy-to-understand conversion mechanism between .NET and JavaScript types.</span></span>

<span data-ttu-id="d7c17-267">Zusätzlich werden diese Aufrufe auf Blazor Server über das Netzwerk übergeben.</span><span class="sxs-lookup"><span data-stu-id="d7c17-267">Additionally on Blazor Server, these calls are passed across the network.</span></span>

### <a name="avoid-excessively-fine-grained-calls"></a><span data-ttu-id="d7c17-268">Vermeiden übermäßig differenzierter Aufrufe</span><span class="sxs-lookup"><span data-stu-id="d7c17-268">Avoid excessively fine-grained calls</span></span>

<span data-ttu-id="d7c17-269">Da jeder Aufruf einen gewissen Mehraufwand erfordert, kann es hilfreich sein, die Anzahl der Aufrufe zu verringern.</span><span class="sxs-lookup"><span data-stu-id="d7c17-269">Since each call involves some overhead, it can be valuable to reduce the number of calls.</span></span> <span data-ttu-id="d7c17-270">Beachten Sie den folgenden Code, der eine Sammlung von Elementen im `localStorage`-Speicher des Browsers speichert:</span><span class="sxs-lookup"><span data-stu-id="d7c17-270">Consider the following code, which stores a collection of items in the browser's `localStorage` store:</span></span>

```csharp
private async Task StoreAllInLocalStorage(IEnumerable<TodoItem> items)
{
    foreach (var item in items)
    {
        await JS.InvokeVoidAsync("localStorage.setItem", item.Id, 
            JsonSerializer.Serialize(item));
    }
}
```

<span data-ttu-id="d7c17-271">Im vorhergehenden Beispiel wird für jedes Element ein separater JS-Interop-Aufruf durchgeführt.</span><span class="sxs-lookup"><span data-stu-id="d7c17-271">The preceding example makes a separate JS interop call for each item.</span></span> <span data-ttu-id="d7c17-272">Stattdessen reduziert der folgende Ansatz das JS-Interop auf einen einzelnen Aufruf:</span><span class="sxs-lookup"><span data-stu-id="d7c17-272">Instead, the following approach reduces the JS interop to a single call:</span></span>

```csharp
private async Task StoreAllInLocalStorage(IEnumerable<TodoItem> items)
{
    await JS.InvokeVoidAsync("storeAllInLocalStorage", items);
}
```

<span data-ttu-id="d7c17-273">Die entsprechende JavaScript-Funktion wird wie folgt definiert:</span><span class="sxs-lookup"><span data-stu-id="d7c17-273">The corresponding JavaScript function defined as follows:</span></span>

```javascript
function storeAllInLocalStorage(items) {
  items.forEach(item => {
    localStorage.setItem(item.id, JSON.stringify(item));
  });
}
```

<span data-ttu-id="d7c17-274">Für Blazor WebAssembly ist dies in der Regel nur wichtig, wenn Sie eine große Anzahl von JS-Interop-Aufrufen durchführen.</span><span class="sxs-lookup"><span data-stu-id="d7c17-274">For Blazor WebAssembly, this usually only matters if you're making a large number of JS interop calls.</span></span>

### <a name="consider-making-synchronous-calls"></a><span data-ttu-id="d7c17-275">Erwägen synchroner Aufrufe</span><span class="sxs-lookup"><span data-stu-id="d7c17-275">Consider making synchronous calls</span></span>

<span data-ttu-id="d7c17-276">JavaScript-Interop-Aufrufe sind standardmäßig asynchron, unabhängig davon, ob der aufgerufene Code synchron oder asynchron ist.</span><span class="sxs-lookup"><span data-stu-id="d7c17-276">JavaScript interop calls are asynchronous by default, regardless of whether the code being called is synchronous or asynchronous.</span></span> <span data-ttu-id="d7c17-277">Dadurch wird sichergestellt, dass die Komponenten mit Blazor WebAssembly und Blazor Server kompatibel sind.</span><span class="sxs-lookup"><span data-stu-id="d7c17-277">This is to ensure components are compatible with both Blazor WebAssembly and Blazor Server.</span></span> <span data-ttu-id="d7c17-278">Auf Blazor Server müssen alle JavaScript-Interop-Aufrufe asynchron sein, da sie über eine Netzwerkverbindung gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="d7c17-278">On Blazor Server, all JavaScript interop calls must be asynchronous because they are sent over a network connection.</span></span>

<span data-ttu-id="d7c17-279">Wenn Sie sicher sind, dass Ihre App nur auf Blazor WebAssembly ausgeführt wird, können Sie synchrone JavaScript-Interop-Aufrufe durchführen.</span><span class="sxs-lookup"><span data-stu-id="d7c17-279">If you know for certain that your app only ever runs on Blazor WebAssembly, you can choose to make synchronous JavaScript interop calls.</span></span> <span data-ttu-id="d7c17-280">Dies ist mit etwas weniger Mehraufwand verbunden als asynchrone Aufrufe und kann zu weniger Renderingzyklen führen, da es keinen Zwischenzustand gibt, während auf die Ergebnisse gewartet wird.</span><span class="sxs-lookup"><span data-stu-id="d7c17-280">This has slightly less overhead than making asynchronous calls and can result in fewer render cycles because there is no intermediate state while awaiting results.</span></span>

<span data-ttu-id="d7c17-281">Um einen synchronen Aufruf von .NET an JavaScript durchführen zu können, wandeln Sie <xref:Microsoft.JSInterop.IJSRuntime> in <xref:Microsoft.JSInterop.IJSInProcessRuntime> um:</span><span class="sxs-lookup"><span data-stu-id="d7c17-281">To make a synchronous call from .NET to JavaScript, cast <xref:Microsoft.JSInterop.IJSRuntime> to <xref:Microsoft.JSInterop.IJSInProcessRuntime>:</span></span>

```razor
@inject IJSRuntime JS

...

@code {
    protected override void HandleSomeEvent()
    {
        var jsInProcess = (IJSInProcessRuntime)JS;
        var value = jsInProcess.Invoke<string>("javascriptFunctionIdentifier");
    }
}
```

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="d7c17-282">Wenn Sie mit `IJSObjectReference` arbeiten, können Sie einen synchronen Aufruf durch Umwandeln in `IJSInProcessObjectReference` durchführen.</span><span class="sxs-lookup"><span data-stu-id="d7c17-282">When working with `IJSObjectReference`, you can make a synchronous call by casting to `IJSInProcessObjectReference`.</span></span>

::: moniker-end

<span data-ttu-id="d7c17-283">Um einen synchronen Aufruf von JavaScript an .NET durchzuführen, verwenden Sie `DotNet.invokeMethod` anstelle von `DotNet.invokeMethodAsync`.</span><span class="sxs-lookup"><span data-stu-id="d7c17-283">To make a synchronous call from JavaScript to .NET, use `DotNet.invokeMethod` instead of `DotNet.invokeMethodAsync`.</span></span>

<span data-ttu-id="d7c17-284">Synchrone Aufrufe funktionieren unter folgenden Voraussetzungen:</span><span class="sxs-lookup"><span data-stu-id="d7c17-284">Synchronous calls work if:</span></span>

* <span data-ttu-id="d7c17-285">Die App wird auf Blazor WebAssembly, nicht Blazor Server ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="d7c17-285">The app is running on Blazor WebAssembly, not Blazor Server.</span></span>
* <span data-ttu-id="d7c17-286">Die aufgerufene Funktion gibt einen Wert synchron zurück (es handelt sich nicht um eine `async`-Methode, und es wird weder ein .NET-<xref:System.Threading.Tasks.Task> noch ein JavaScript-`Promise`) zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="d7c17-286">The called function returns a value synchronously (it isn't an `async` method and doesn't return a .NET <xref:System.Threading.Tasks.Task> or JavaScript `Promise`).</span></span>

<span data-ttu-id="d7c17-287">Weitere Informationen finden Sie unter <xref:blazor/call-javascript-from-dotnet>.</span><span class="sxs-lookup"><span data-stu-id="d7c17-287">For more information, see <xref:blazor/call-javascript-from-dotnet>.</span></span>

::: moniker range=">= aspnetcore-5.0"
 
### <a name="consider-making-unmarshalled-calls"></a><span data-ttu-id="d7c17-288">Erwägen der Durchführung unmarshallter Aufrufe</span><span class="sxs-lookup"><span data-stu-id="d7c17-288">Consider making unmarshalled calls</span></span>

<span data-ttu-id="d7c17-289">Bei der Ausführung auf Blazor WebAssembly können unmarshallte Aufrufe von .NET an JavaScript durchgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="d7c17-289">When running on Blazor WebAssembly, it's possible to make unmarshalled calls from .NET to JavaScript.</span></span> <span data-ttu-id="d7c17-290">Dabei handelt es sich um synchrone Aufrufe, die keine JSON-Serialisierung von Argumenten oder Rückgabewerten ausführen.</span><span class="sxs-lookup"><span data-stu-id="d7c17-290">These are synchronous calls that don't perform JSON serialization of arguments or return values.</span></span> <span data-ttu-id="d7c17-291">Alle Aspekte der Speicherverwaltung und Übersetzungen zwischen .NET- und JavaScript-Darstellungen werden dem Entwickler überlassen.</span><span class="sxs-lookup"><span data-stu-id="d7c17-291">All aspects of memory management and translations between .NET and JavaScript representations are left up to the developer.</span></span>

> [!WARNING]
> <span data-ttu-id="d7c17-292">Während die Verwendung von `IJSUnmarshalledRuntime` den geringsten Aufwand der JS-Interop-Ansätze hat, gibt es für die JavaScript-APIs, die erforderlich sind, um mit diesen APIs zu interagieren, aktuell keine Dokumentation. Außerdem werden in diesem Bereich im Rahmen zukünftiger Releases Breaking Changes vorgenommen.</span><span class="sxs-lookup"><span data-stu-id="d7c17-292">While using `IJSUnmarshalledRuntime` has the least overhead of the JS interop approaches, the JavaScript APIs required to interact with these APIs are currently undocumented and subject to breaking changes in future releases.</span></span>

```javascript
function jsInteropCall() {
    return BINDING.js_to_mono_obj("Hello world");
}
```

```razor
@inject IJSRuntime JS

@code {
    protected override void OnInitialized()
    {
        var unmarshalledJs = (IJSUnmarshalledRuntime)JS;
        var value = unmarshalledJs.InvokeUnmarshalled<string>("jsInteropCall");
    }
}
```

::: moniker-end

## <a name="minimize-app-download-size"></a><span data-ttu-id="d7c17-293">Minimieren der Größe des Anwendungsdownloads</span><span class="sxs-lookup"><span data-stu-id="d7c17-293">Minimize app download size</span></span>

::: moniker range=">= aspnetcore-5.0"

### <a name="intermediate-language-il-trimming"></a><span data-ttu-id="d7c17-294">IL-Kürzung (Intermediate Language, Zwischensprache)</span><span class="sxs-lookup"><span data-stu-id="d7c17-294">Intermediate Language (IL) trimming</span></span>

<span data-ttu-id="d7c17-295">[Das Kürzen nicht verwendeter Assemblys über eine Blazor WebAssembly-App](xref:blazor/host-and-deploy/configure-trimmer) reduziert die Größe der App, indem nicht genutzter Code in den Binärdateien der App entfernt wird.</span><span class="sxs-lookup"><span data-stu-id="d7c17-295">[Trimming unused assemblies from a Blazor WebAssembly app](xref:blazor/host-and-deploy/configure-trimmer) reduces the app's size by removing unused code in the app's binaries.</span></span> <span data-ttu-id="d7c17-296">Der Trimmer wird standardmäßig beim Veröffentlichen einer Anwendung ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="d7c17-296">By default, the Trimmer is executed when publishing an application.</span></span> <span data-ttu-id="d7c17-297">Veröffentlichen Sie die App für die Bereitstellung mithilfe des Befehls [`dotnet publish`](/dotnet/core/tools/dotnet-publish) mit auf `Release` festgelegter Option [-c|--configuration](/dotnet/core/tools/dotnet-publish#options), um von der Kürzung zu profitieren:</span><span class="sxs-lookup"><span data-stu-id="d7c17-297">To benefit from trimming, publish the app for deployment using the [`dotnet publish`](/dotnet/core/tools/dotnet-publish) command with the [-c|--configuration](/dotnet/core/tools/dotnet-publish#options) option set to `Release`:</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

### <a name="intermediate-language-il-linking"></a><span data-ttu-id="d7c17-298">Intermediate Language-Verknüpfung (IL)</span><span class="sxs-lookup"><span data-stu-id="d7c17-298">Intermediate Language (IL) linking</span></span>

<span data-ttu-id="d7c17-299">[Das Konfigurieren eines Linkers für eine Blazor WebAssembly-App](xref:blazor/host-and-deploy/configure-linker) reduziert die Größe der App, indem nicht genutzter Code in den Binärdateien der App gekürzt wird.</span><span class="sxs-lookup"><span data-stu-id="d7c17-299">[Linking a Blazor WebAssembly app](xref:blazor/host-and-deploy/configure-linker) reduces the app's size by trimming unused code in the app's binaries.</span></span> <span data-ttu-id="d7c17-300">Standardmäßig ist der IL-Linker (Intermediate Language, Zwischensprache) nur aktiviert, wenn der Erstellvorgang in der `Release`-Konfiguration ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="d7c17-300">By default, the Intermediate Language (IL) Linker is only enabled when building in `Release` configuration.</span></span> <span data-ttu-id="d7c17-301">Veröffentlichen Sie die App für die Bereitstellung mithilfe des Befehls [`dotnet publish`](/dotnet/core/tools/dotnet-publish) mit auf `Release` festgelegter Option [-c|--configuration](/dotnet/core/tools/dotnet-publish#options), um diesen Vorteil zu nutzen:</span><span class="sxs-lookup"><span data-stu-id="d7c17-301">To benefit from this, publish the app for deployment using the [`dotnet publish`](/dotnet/core/tools/dotnet-publish) command with the [-c|--configuration](/dotnet/core/tools/dotnet-publish#options) option set to `Release`:</span></span>

::: moniker-end

```dotnetcli
dotnet publish -c Release
```

### <a name="use-systemtextjson"></a><span data-ttu-id="d7c17-302">Verwenden von System.Text.Json</span><span class="sxs-lookup"><span data-stu-id="d7c17-302">Use System.Text.Json</span></span>

<span data-ttu-id="d7c17-303">Die JS-Interop-Implementierung von Blazor basiert auf <xref:System.Text.Json>. Dabei handelt es sich um eine JSON-Hochleistungsserialisierungsbibliothek mit niedriger Arbeitsspeicherzuteilung.</span><span class="sxs-lookup"><span data-stu-id="d7c17-303">Blazor's JS interop implementation relies on <xref:System.Text.Json>, which is a high-performance JSON serialization library with low memory allocation.</span></span> <span data-ttu-id="d7c17-304">Das Verwenden von <xref:System.Text.Json> führt nicht zu einer zusätzlichen Nutzlastgröße für die App, wenn eine oder mehrere alternative JSON-Bibliotheken hinzufügt werden.</span><span class="sxs-lookup"><span data-stu-id="d7c17-304">Using <xref:System.Text.Json> doesn't result in additional app payload size over adding one or more alternate JSON libraries.</span></span>

<span data-ttu-id="d7c17-305">Eine Anleitung zur Migration finden Sie unter [Migration von `Newtonsoft.Json` zu `System.Text.Json`](/dotnet/standard/serialization/system-text-json-migrate-from-newtonsoft-how-to).</span><span class="sxs-lookup"><span data-stu-id="d7c17-305">For migration guidance, see [How to migrate from `Newtonsoft.Json` to `System.Text.Json`](/dotnet/standard/serialization/system-text-json-migrate-from-newtonsoft-how-to).</span></span>

### <a name="lazy-load-assemblies"></a><span data-ttu-id="d7c17-306">Lazy Loading-Assemblys</span><span class="sxs-lookup"><span data-stu-id="d7c17-306">Lazy load assemblies</span></span>

<span data-ttu-id="d7c17-307">Laden Sie Assemblys zur Laufzeit, wenn die Assemblys von einer Route benötigt werden.</span><span class="sxs-lookup"><span data-stu-id="d7c17-307">Load assemblies at runtime when the assemblies are required by a route.</span></span> <span data-ttu-id="d7c17-308">Weitere Informationen finden Sie unter <xref:blazor/webassembly-lazy-load-assemblies>.</span><span class="sxs-lookup"><span data-stu-id="d7c17-308">For more information, see <xref:blazor/webassembly-lazy-load-assemblies>.</span></span>

### <a name="compression"></a><span data-ttu-id="d7c17-309">Komprimierung</span><span class="sxs-lookup"><span data-stu-id="d7c17-309">Compression</span></span>

<span data-ttu-id="d7c17-310">Wenn eine Blazor WebAssembly-App veröffentlicht wird, wird die Ausgabe bei der Veröffentlichung statisch komprimiert, um die App-Größe zu verringern und den Aufwand für eine Laufzeitkomprimierung zu beseitigen.</span><span class="sxs-lookup"><span data-stu-id="d7c17-310">When a Blazor WebAssembly app is published, the output is statically compressed during publish to reduce the app's size and remove the overhead for runtime compression.</span></span> <span data-ttu-id="d7c17-311">Blazor basiert darauf, dass der Server eine Inhaltsaushandlung ausführt und statisch komprimierte Dateien bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="d7c17-311">Blazor relies on the server to perform content negotation and serve statically-compressed files.</span></span>

<span data-ttu-id="d7c17-312">Nach der Bereitstellung einer App überprüfen Sie, ob die App komprimierte Dateien bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="d7c17-312">After an app is deployed, verify that the app serves compressed files.</span></span> <span data-ttu-id="d7c17-313">Sehen Sie sich die Netzwerkregisterkarte der Entwicklertools eines Browsers an, und überprüfen Sie, ob die Dateien mit `Content-Encoding: br` oder `Content-Encoding: gz` bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="d7c17-313">Inspect the Network tab in a browser's Developer Tools and verify that the files are served with `Content-Encoding: br` or `Content-Encoding: gz`.</span></span> <span data-ttu-id="d7c17-314">Wenn der Host keine komprimierten Dateien bereitstellt, befolgen Sie die Anweisungen in <xref:blazor/host-and-deploy/webassembly#compression>.</span><span class="sxs-lookup"><span data-stu-id="d7c17-314">If the host isn't serving compressed files, follow the instructions in <xref:blazor/host-and-deploy/webassembly#compression>.</span></span>

### <a name="disable-unused-features"></a><span data-ttu-id="d7c17-315">Deaktivieren nicht genutzter Features</span><span class="sxs-lookup"><span data-stu-id="d7c17-315">Disable unused features</span></span>

<span data-ttu-id="d7c17-316">Die Blazor WebAssembly-Runtime schließt die folgenden .NET-Features ein, die deaktiviert werden können, wenn die App sie aufgrund einer kleineren Nutzlastgröße nicht benötigt:</span><span class="sxs-lookup"><span data-stu-id="d7c17-316">Blazor WebAssembly's runtime includes the following .NET features that can be disabled if the app doesn't require them for a smaller payload size:</span></span>

* <span data-ttu-id="d7c17-317">Eine Datendatei wird eingeschlossen, damit die Zeitzoneninformationen korrekt sind.</span><span class="sxs-lookup"><span data-stu-id="d7c17-317">A data file is included to make timezone information correct.</span></span> <span data-ttu-id="d7c17-318">Wenn die App dieses Feature nicht benötigt, ziehen Sie in Betracht, es zu deaktivieren, indem Sie die `BlazorEnableTimeZoneSupport`-MSBuild-Eigenschaft in der Projektdatei der App auf `false` festlegen:</span><span class="sxs-lookup"><span data-stu-id="d7c17-318">If the app doesn't require this feature, consider disabling it by setting the `BlazorEnableTimeZoneSupport` MSBuild property in the app's project file to `false`:</span></span>

  ```xml
  <PropertyGroup>
    <BlazorEnableTimeZoneSupport>false</BlazorEnableTimeZoneSupport>
  </PropertyGroup>
  ```

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="d7c17-319">Blazor WebAssembly enthält standardmäßig Globalisierungsressourcen, die zum Anzeigen von Werten wie Datums- und Währungsangaben in der Kultur des Benutzers erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="d7c17-319">By default, Blazor WebAssembly carries globalization resources required to display values, such as dates and currency, in the user's culture.</span></span> <span data-ttu-id="d7c17-320">Wenn für die App keine Lokalisierung erforderlich ist, können Sie [die App so konfigurieren, dass die invariante Kultur unterstützt wird](xref:blazor/globalization-localization), die auf der `en-US`-Kultur basiert:</span><span class="sxs-lookup"><span data-stu-id="d7c17-320">If the app doesn't require localization, you may [configure the app to support the invariant culture](xref:blazor/globalization-localization), which is based on the `en-US` culture:</span></span>

  ```xml
  <PropertyGroup>
    <InvariantGlobalization>true</InvariantGlobalization>
  </PropertyGroup>
  ```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="d7c17-321">Die Sortierungsinformationen werden eingeschlossen, damit APIs wie <xref:System.StringComparison.InvariantCultureIgnoreCase?displayProperty=nameWithType> ordnungsgemäß funktionieren.</span><span class="sxs-lookup"><span data-stu-id="d7c17-321">Collation information is included to make APIs such as <xref:System.StringComparison.InvariantCultureIgnoreCase?displayProperty=nameWithType> work correctly.</span></span> <span data-ttu-id="d7c17-322">Wenn Sie sicher sind, dass die App die Sortierungsinformationen nicht benötigt, ziehen Sie in Betracht, sie zu deaktivieren, indem Sie die `BlazorWebAssemblyPreserveCollationData`-MSBuild-Eigenschaft in der Projektdatei der App auf `false` festlegen:</span><span class="sxs-lookup"><span data-stu-id="d7c17-322">If you're certain that the app doesn't require the collation data, consider disabling it by setting the `BlazorWebAssemblyPreserveCollationData` MSBuild property in the app's project file to `false`:</span></span>

  ```xml
  <PropertyGroup>
    <BlazorWebAssemblyPreserveCollationData>false</BlazorWebAssemblyPreserveCollationData>
  </PropertyGroup>
  ```

::: moniker-end
