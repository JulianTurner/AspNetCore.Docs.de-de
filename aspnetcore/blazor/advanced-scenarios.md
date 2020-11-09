---
title: Erweiterte ASP.NET Core-Blazor-Szenarios
author: guardrex
description: 'Informieren Sie sich über erweiterte Szenarios in Blazor einschließlich der Integration manueller RenderTreeBuilder-Logik in eine App.'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/18/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: blazor/advanced-scenarios
ms.openlocfilehash: 95714b3c0d21d3b348a9a8a984e2a42e7708499e
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93056555"
---
# <a name="aspnet-core-no-locblazor-advanced-scenarios"></a><span data-ttu-id="3151d-103">Erweiterte ASP.NET Core-Blazor-Szenarios</span><span class="sxs-lookup"><span data-stu-id="3151d-103">ASP.NET Core Blazor advanced scenarios</span></span>

<span data-ttu-id="3151d-104">Von [Luke Latham](https://github.com/guardrex) und [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="3151d-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

## <a name="no-locblazor-server-circuit-handler"></a><span data-ttu-id="3151d-105">Blazor Server-Verbindungshandler</span><span class="sxs-lookup"><span data-stu-id="3151d-105">Blazor Server circuit handler</span></span>

<span data-ttu-id="3151d-106">In Blazor Server kann mithilfe von Code ein *Verbindungshandler* definiert werden, mit dem Code für Zustandsänderungen einer Benutzerverbindung ausgeführt werden kann.</span><span class="sxs-lookup"><span data-stu-id="3151d-106">Blazor Server allows code to define a *circuit handler* , which allows running code on changes to the state of a user's circuit.</span></span> <span data-ttu-id="3151d-107">Ein Verbindungshandler wird durch das Ableiten von `CircuitHandler` und Registrieren der Klasse im Dienstcontainer der App implementiert.</span><span class="sxs-lookup"><span data-stu-id="3151d-107">A circuit handler is implemented by deriving from `CircuitHandler` and registering the class in the app's service container.</span></span> <span data-ttu-id="3151d-108">Im folgenden Beispiel eines Verbindungshandlers werden geöffnete SignalR-Verbindungen nachverfolgt:</span><span class="sxs-lookup"><span data-stu-id="3151d-108">The following example of a circuit handler tracks open SignalR connections:</span></span>

```csharp
using System.Collections.Generic;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.Server.Circuits;

public class TrackingCircuitHandler : CircuitHandler
{
    private HashSet<Circuit> circuits = new HashSet<Circuit>();

    public override Task OnConnectionUpAsync(Circuit circuit, 
        CancellationToken cancellationToken)
    {
        circuits.Add(circuit);

        return Task.CompletedTask;
    }

    public override Task OnConnectionDownAsync(Circuit circuit, 
        CancellationToken cancellationToken)
    {
        circuits.Remove(circuit);

        return Task.CompletedTask;
    }

    public int ConnectedCircuits => circuits.Count;
}
```

<span data-ttu-id="3151d-109">Verbindungshandler werden mithilfe von DI registriert.</span><span class="sxs-lookup"><span data-stu-id="3151d-109">Circuit handlers are registered using DI.</span></span> <span data-ttu-id="3151d-110">Bereichsbezogene Instanzen werden pro Verbindungsinstanz erstellt.</span><span class="sxs-lookup"><span data-stu-id="3151d-110">Scoped instances are created per instance of a circuit.</span></span> <span data-ttu-id="3151d-111">Mithilfe von `TrackingCircuitHandler` aus dem vorherigen Beispiel wird ein Singletondienst erstellt, da der Zustand aller Verbindungen nachverfolgt werden muss:</span><span class="sxs-lookup"><span data-stu-id="3151d-111">Using the `TrackingCircuitHandler` in the preceding example, a singleton service is created because the state of all circuits must be tracked:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...
    services.AddSingleton<CircuitHandler, TrackingCircuitHandler>();
}
```

<span data-ttu-id="3151d-112">Wenn die Methoden eines benutzerdefinierten Verbindungshandlers einen Ausnahmefehler auslösen, ist dieser Ausnahmefehler für die Blazor Server-Verbindung schwerwiegend.</span><span class="sxs-lookup"><span data-stu-id="3151d-112">If a custom circuit handler's methods throw an unhandled exception, the exception is fatal to the Blazor Server circuit.</span></span> <span data-ttu-id="3151d-113">Umschließen Sie den Code in einer oder mehreren [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch)-Anweisungen mit Fehlerbehandlung und -protokollierung, um Ausnahmen im Code oder in aufgerufenen Methoden eines Handlers zu tolerieren.</span><span class="sxs-lookup"><span data-stu-id="3151d-113">To tolerate exceptions in a handler's code or called methods, wrap the code in one or more [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statements with error handling and logging.</span></span>

<span data-ttu-id="3151d-114">Wenn eine Verbindung beendet wird, weil ein Benutzer die Verbindung getrennt hat und das Framework den Verbindungsstatus bereinigt, gibt das Framework den DI-Bereich der Verbindung frei.</span><span class="sxs-lookup"><span data-stu-id="3151d-114">When a circuit ends because a user has disconnected and the framework is cleaning up the circuit state, the framework disposes of the circuit's DI scope.</span></span> <span data-ttu-id="3151d-115">Wenn der Bereich freigegeben wird, werden alle Dienste im Verbindungsbereich verworfen, die <xref:System.IDisposable?displayProperty=fullName> implementieren.</span><span class="sxs-lookup"><span data-stu-id="3151d-115">Disposing the scope disposes any circuit-scoped DI services that implement <xref:System.IDisposable?displayProperty=fullName>.</span></span> <span data-ttu-id="3151d-116">Wenn ein DI-Dienst während der Freigabe einen Ausnahmefehler auslöst, protokolliert das Framework die Ausnahme.</span><span class="sxs-lookup"><span data-stu-id="3151d-116">If any DI service throws an unhandled exception during disposal, the framework logs the exception.</span></span>

## <a name="manual-rendertreebuilder-logic"></a><span data-ttu-id="3151d-117">Manuelle RenderTreeBuilder-Logik</span><span class="sxs-lookup"><span data-stu-id="3151d-117">Manual RenderTreeBuilder logic</span></span>

<span data-ttu-id="3151d-118"><xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> stellt Methoden zum Bearbeiten von Komponenten und Elementen bereit. Dazu gehört auch das manuelle Erstellen von Komponenten in C#-Code.</span><span class="sxs-lookup"><span data-stu-id="3151d-118"><xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> provides methods for manipulating components and elements, including building components manually in C# code.</span></span>

> [!NOTE]
> <span data-ttu-id="3151d-119">Die Verwendung von <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> zum Erstellen von Komponenten ist ein erweitertes Szenario.</span><span class="sxs-lookup"><span data-stu-id="3151d-119">Use of <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> to create components is an advanced scenario.</span></span> <span data-ttu-id="3151d-120">Eine falsch formatierte Komponente (z. B. ein nicht geschlossenes Markuptag) kann zu undefiniertem Verhalten führen.</span><span class="sxs-lookup"><span data-stu-id="3151d-120">A malformed component (for example, an unclosed markup tag) can result in undefined behavior.</span></span>

<span data-ttu-id="3151d-121">Beachten Sie die folgende `PetDetails`-Komponente, die manuell in eine andere Komponente integriert werden kann.</span><span class="sxs-lookup"><span data-stu-id="3151d-121">Consider the following `PetDetails` component, which can be manually built into another component:</span></span>

```razor
<h2>Pet Details Component</h2>

<p>@PetDetailsQuote</p>

@code
{
    [Parameter]
    public string PetDetailsQuote { get; set; }
}
```

<span data-ttu-id="3151d-122">Im folgenden Beispiel generiert die Schleife in der `CreateComponent`-Methode drei `PetDetails`-Komponenten.</span><span class="sxs-lookup"><span data-stu-id="3151d-122">In the following example, the loop in the `CreateComponent` method generates three `PetDetails` components.</span></span> <span data-ttu-id="3151d-123">In <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder>-Methoden mit einer Sequenznummer sind die Sequenznummern Zeilennummern des Quellcodes.</span><span class="sxs-lookup"><span data-stu-id="3151d-123">In <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> methods with a sequence number, sequence numbers are source code line numbers.</span></span> <span data-ttu-id="3151d-124">Der diff-Algorithmus von Blazor basiert auf den Sequenznummern, die unterschiedlichen Codezeilen und nicht unterschiedlichen Aufrufen entsprechen.</span><span class="sxs-lookup"><span data-stu-id="3151d-124">The Blazor difference algorithm relies on the sequence numbers corresponding to distinct lines of code, not distinct call invocations.</span></span> <span data-ttu-id="3151d-125">Hartcodieren Sie die Argumente für Sequenznummern, wenn Sie eine Komponente mit <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder>-Methoden erstellen.</span><span class="sxs-lookup"><span data-stu-id="3151d-125">When creating a component with <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> methods, hardcode the arguments for sequence numbers.</span></span> <span data-ttu-id="3151d-126">**Die Verwendung einer Berechnung oder eines Leistungszählers zum Generieren der Sequenznummer kann zu schlechter Leistung führen.**</span><span class="sxs-lookup"><span data-stu-id="3151d-126">**Using a calculation or counter to generate the sequence number can lead to poor performance.**</span></span> <span data-ttu-id="3151d-127">Weitere Informationen finden Sie im Abschnitt [Auf Codezeilennummern und nicht auf die Ausführungsreihenfolge bezogene Sequenznummern](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order).</span><span class="sxs-lookup"><span data-stu-id="3151d-127">For more information, see the [Sequence numbers relate to code line numbers and not execution order](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order) section.</span></span>

<span data-ttu-id="3151d-128">`BuiltContent`-Komponente:</span><span class="sxs-lookup"><span data-stu-id="3151d-128">`BuiltContent` component:</span></span>

```razor
@page "/BuiltContent"

<h1>Build a component</h1>

@CustomRender

<button type="button" @onclick="RenderComponent">
    Create three Pet Details components
</button>

@code {
    private RenderFragment CustomRender { get; set; }
    
    private RenderFragment CreateComponent() => builder =>
    {
        for (var i = 0; i < 3; i++) 
        {
            builder.OpenComponent(0, typeof(PetDetails));
            builder.AddAttribute(1, "PetDetailsQuote", "Someone's best friend!");
            builder.CloseComponent();
        }
    };    
    
    private void RenderComponent()
    {
        CustomRender = CreateComponent();
    }
}
```

> [!WARNING]
> <span data-ttu-id="3151d-129">Die Typen in <xref:Microsoft.AspNetCore.Components.RenderTree> ermöglichen die Verarbeitung der *Ergebnisse* von Renderingvorgängen.</span><span class="sxs-lookup"><span data-stu-id="3151d-129">The types in <xref:Microsoft.AspNetCore.Components.RenderTree> allow processing of the *results* of rendering operations.</span></span> <span data-ttu-id="3151d-130">Hierbei handelt es sich um interne Details der Blazor-Frameworkimplementierung.</span><span class="sxs-lookup"><span data-stu-id="3151d-130">These are internal details of the Blazor framework implementation.</span></span> <span data-ttu-id="3151d-131">Diese Typen sollten als *instabil* betrachtet werden und in zukünftigen Versionen geändert werden.</span><span class="sxs-lookup"><span data-stu-id="3151d-131">These types should be considered *unstable* and subject to change in future releases.</span></span>

### <a name="sequence-numbers-relate-to-code-line-numbers-and-not-execution-order"></a><span data-ttu-id="3151d-132">Auf Codezeilennummern und nicht auf die Ausführungsreihenfolge bezogene Sequenznummern</span><span class="sxs-lookup"><span data-stu-id="3151d-132">Sequence numbers relate to code line numbers and not execution order</span></span>

<span data-ttu-id="3151d-133">Razor-Komponentendateien (`.razor`) werden stets kompiliert.</span><span class="sxs-lookup"><span data-stu-id="3151d-133">Razor component files (`.razor`) are always compiled.</span></span> <span data-ttu-id="3151d-134">Die Kompilierung stellt gegenüber der Interpretation von Code einen Vorteil dar, da der Kompilierungsschritt zum Einfügen von Informationen verwendet werden kann, die die App-Leistung zur Laufzeit erhöhen können.</span><span class="sxs-lookup"><span data-stu-id="3151d-134">Compilation is a potential advantage over interpreting code because the compile step can be used to inject information that improves app performance at runtime.</span></span>

<span data-ttu-id="3151d-135">Ein wichtiges Beispiel für diese Verbesserungen sind *Sequenznummern*.</span><span class="sxs-lookup"><span data-stu-id="3151d-135">A key example of these improvements involves *sequence numbers*.</span></span> <span data-ttu-id="3151d-136">Sequenznummern geben der Laufzeit an, welche Ausgaben aus den unterschiedlichen und geordneten Codezeilen stammen.</span><span class="sxs-lookup"><span data-stu-id="3151d-136">Sequence numbers indicate to the runtime which outputs came from which distinct and ordered lines of code.</span></span> <span data-ttu-id="3151d-137">Diese Informationen werden von der Laufzeit verwendet, um effiziente und zeitlich lineare Strukturunterschiede zu generieren. Diese Methode ist viel schneller als es für einen allgemeinen diff-Strukturalgorithmus normalerweise möglich ist.</span><span class="sxs-lookup"><span data-stu-id="3151d-137">The runtime uses this information to generate efficient tree diffs in linear time, which is far faster than is normally possible for a general tree diff algorithm.</span></span>

<span data-ttu-id="3151d-138">Betrachten Sie die Datei der folgenden Razor-Komponente (`.razor`):</span><span class="sxs-lookup"><span data-stu-id="3151d-138">Consider the following Razor component (`.razor`) file:</span></span>

```razor
@if (someFlag)
{
    <text>First</text>
}

Second
```

<span data-ttu-id="3151d-139">Der vorangehende Code wird in etwa wie folgt kompiliert:</span><span class="sxs-lookup"><span data-stu-id="3151d-139">The preceding code compiles to something like the following:</span></span>

```csharp
if (someFlag)
{
    builder.AddContent(0, "First");
}

builder.AddContent(1, "Second");
```

<span data-ttu-id="3151d-140">Wenn der Code zum ersten Mal ausgeführt wird, erhält der Generator Folgendes, wenn `someFlag` `true` ist:</span><span class="sxs-lookup"><span data-stu-id="3151d-140">When the code executes for the first time, if `someFlag` is `true`, the builder receives:</span></span>

| <span data-ttu-id="3151d-141">Sequenz</span><span class="sxs-lookup"><span data-stu-id="3151d-141">Sequence</span></span> | <span data-ttu-id="3151d-142">Typ</span><span class="sxs-lookup"><span data-stu-id="3151d-142">Type</span></span>      | <span data-ttu-id="3151d-143">Daten</span><span class="sxs-lookup"><span data-stu-id="3151d-143">Data</span></span>   |
| :------: | --------- | :----: |
| <span data-ttu-id="3151d-144">0</span><span class="sxs-lookup"><span data-stu-id="3151d-144">0</span></span>        | <span data-ttu-id="3151d-145">Textknoten</span><span class="sxs-lookup"><span data-stu-id="3151d-145">Text node</span></span> | <span data-ttu-id="3151d-146">First</span><span class="sxs-lookup"><span data-stu-id="3151d-146">First</span></span>  |
| <span data-ttu-id="3151d-147">1</span><span class="sxs-lookup"><span data-stu-id="3151d-147">1</span></span>        | <span data-ttu-id="3151d-148">Textknoten</span><span class="sxs-lookup"><span data-stu-id="3151d-148">Text node</span></span> | <span data-ttu-id="3151d-149">Second</span><span class="sxs-lookup"><span data-stu-id="3151d-149">Second</span></span> |

<span data-ttu-id="3151d-150">Stellen Sie sich vor, dass `someFlag` `false` wird und das Markup wieder gerendert wird.</span><span class="sxs-lookup"><span data-stu-id="3151d-150">Imagine that `someFlag` becomes `false`, and the markup is rendered again.</span></span> <span data-ttu-id="3151d-151">Dieses Mal empfängt der Generator Folgendes:</span><span class="sxs-lookup"><span data-stu-id="3151d-151">This time, the builder receives:</span></span>

| <span data-ttu-id="3151d-152">Sequenz</span><span class="sxs-lookup"><span data-stu-id="3151d-152">Sequence</span></span> | <span data-ttu-id="3151d-153">Typ</span><span class="sxs-lookup"><span data-stu-id="3151d-153">Type</span></span>       | <span data-ttu-id="3151d-154">Daten</span><span class="sxs-lookup"><span data-stu-id="3151d-154">Data</span></span>   |
| :------: | ---------- | :----: |
| <span data-ttu-id="3151d-155">1</span><span class="sxs-lookup"><span data-stu-id="3151d-155">1</span></span>        | <span data-ttu-id="3151d-156">Textknoten</span><span class="sxs-lookup"><span data-stu-id="3151d-156">Text node</span></span>  | <span data-ttu-id="3151d-157">Second</span><span class="sxs-lookup"><span data-stu-id="3151d-157">Second</span></span> |

<span data-ttu-id="3151d-158">Wenn die Laufzeit einen diff-Algorithmus ausführt, wird angezeigt, dass das Element bei Sequenz `0` entfernt wurde. Daraufhin wird das folgende triviale *Bearbeitungsskript* generiert:</span><span class="sxs-lookup"><span data-stu-id="3151d-158">When the runtime performs a diff, it sees that the item at sequence `0` was removed, so it generates the following trivial *edit script* :</span></span>

* <span data-ttu-id="3151d-159">Entfernen Sie den ersten Textknoten.</span><span class="sxs-lookup"><span data-stu-id="3151d-159">Remove the first text node.</span></span>

### <a name="the-problem-with-generating-sequence-numbers-programmatically"></a><span data-ttu-id="3151d-160">Problem beim programmgesteuerten Generieren von Sequenznummern</span><span class="sxs-lookup"><span data-stu-id="3151d-160">The problem with generating sequence numbers programmatically</span></span>

<span data-ttu-id="3151d-161">Angenommen, Sie haben stattdessen die folgende Buildlogik für die Renderstruktur geschrieben:</span><span class="sxs-lookup"><span data-stu-id="3151d-161">Imagine instead that you wrote the following render tree builder logic:</span></span>

```csharp
var seq = 0;

if (someFlag)
{
    builder.AddContent(seq++, "First");
}

builder.AddContent(seq++, "Second");
```

<span data-ttu-id="3151d-162">Die erste Ausgabe lautet nun wie folgt:</span><span class="sxs-lookup"><span data-stu-id="3151d-162">Now, the first output is:</span></span>

| <span data-ttu-id="3151d-163">Sequenz</span><span class="sxs-lookup"><span data-stu-id="3151d-163">Sequence</span></span> | <span data-ttu-id="3151d-164">Typ</span><span class="sxs-lookup"><span data-stu-id="3151d-164">Type</span></span>      | <span data-ttu-id="3151d-165">Daten</span><span class="sxs-lookup"><span data-stu-id="3151d-165">Data</span></span>   |
| :------: | --------- | :----: |
| <span data-ttu-id="3151d-166">0</span><span class="sxs-lookup"><span data-stu-id="3151d-166">0</span></span>        | <span data-ttu-id="3151d-167">Textknoten</span><span class="sxs-lookup"><span data-stu-id="3151d-167">Text node</span></span> | <span data-ttu-id="3151d-168">First</span><span class="sxs-lookup"><span data-stu-id="3151d-168">First</span></span>  |
| <span data-ttu-id="3151d-169">1</span><span class="sxs-lookup"><span data-stu-id="3151d-169">1</span></span>        | <span data-ttu-id="3151d-170">Textknoten</span><span class="sxs-lookup"><span data-stu-id="3151d-170">Text node</span></span> | <span data-ttu-id="3151d-171">Second</span><span class="sxs-lookup"><span data-stu-id="3151d-171">Second</span></span> |

<span data-ttu-id="3151d-172">Dieses Ergebnis ist mit dem des vorherigen Falls identisch, sodass keine negativen Probleme aufgetreten sind.</span><span class="sxs-lookup"><span data-stu-id="3151d-172">This outcome is identical to the prior case, so no negative issues exist.</span></span> <span data-ttu-id="3151d-173">`someFlag` ist beim zweiten Rendering `false`, und die Ausgabe lautet wie folgt:</span><span class="sxs-lookup"><span data-stu-id="3151d-173">`someFlag` is `false` on the second rendering, and the output is:</span></span>

| <span data-ttu-id="3151d-174">Sequenz</span><span class="sxs-lookup"><span data-stu-id="3151d-174">Sequence</span></span> | <span data-ttu-id="3151d-175">Typ</span><span class="sxs-lookup"><span data-stu-id="3151d-175">Type</span></span>      | <span data-ttu-id="3151d-176">Daten</span><span class="sxs-lookup"><span data-stu-id="3151d-176">Data</span></span>   |
| :------: | --------- | ------ |
| <span data-ttu-id="3151d-177">0</span><span class="sxs-lookup"><span data-stu-id="3151d-177">0</span></span>        | <span data-ttu-id="3151d-178">Textknoten</span><span class="sxs-lookup"><span data-stu-id="3151d-178">Text node</span></span> | <span data-ttu-id="3151d-179">Second</span><span class="sxs-lookup"><span data-stu-id="3151d-179">Second</span></span> |

<span data-ttu-id="3151d-180">Dieses Mal zeigt der diff-Algorithmus an, dass *zwei* Änderungen aufgetreten sind, und der Algorithmus generiert das folgende Bearbeitungsskript:</span><span class="sxs-lookup"><span data-stu-id="3151d-180">This time, the diff algorithm sees that *two* changes have occurred, and the algorithm generates the following edit script:</span></span>

* <span data-ttu-id="3151d-181">Ändern Sie den Wert des ersten Textknotens in `Second`.</span><span class="sxs-lookup"><span data-stu-id="3151d-181">Change the value of the first text node to `Second`.</span></span>
* <span data-ttu-id="3151d-182">Entfernen Sie den zweiten Textknoten.</span><span class="sxs-lookup"><span data-stu-id="3151d-182">Remove the second text node.</span></span>

<span data-ttu-id="3151d-183">Durch das Erzeugen der Sequenznummern sind alle nützlichen Informationen dazu verloren gegangen, wo die `if/else`-Branches und -Schleifen im ursprünglichen Code vorhanden waren.</span><span class="sxs-lookup"><span data-stu-id="3151d-183">Generating the sequence numbers has lost all the useful information about where the `if/else` branches and loops were present in the original code.</span></span> <span data-ttu-id="3151d-184">Dies führt zu einem **doppelt so langen diff** wie zuvor.</span><span class="sxs-lookup"><span data-stu-id="3151d-184">This results in a diff **twice as long** as before.</span></span>

<span data-ttu-id="3151d-185">Dies ist ein triviales Beispiel.</span><span class="sxs-lookup"><span data-stu-id="3151d-185">This is a trivial example.</span></span> <span data-ttu-id="3151d-186">In realistischeren Fällen mit komplexen und tief geschachtelten Strukturen und vor allem mit Schleifen sind die Leistungskosten in der Regel höher.</span><span class="sxs-lookup"><span data-stu-id="3151d-186">In more realistic cases with complex and deeply nested structures, and especially with loops, the performance cost is usually higher.</span></span> <span data-ttu-id="3151d-187">Anstatt sofort festzustellen, welche Schleifenblöcke oder Branches eingefügt oder entfernt wurden, muss der diff-Algorithmus eine tiefe Rekursion der Renderstrukturen durchführen.</span><span class="sxs-lookup"><span data-stu-id="3151d-187">Instead of immediately identifying which loop blocks or branches have been inserted or removed, the diff algorithm has to recurse deeply into the render trees.</span></span> <span data-ttu-id="3151d-188">Dies führt in der Regel dazu, dass längere Bearbeitungsskripts erstellt werden müssen, da der diff-Algorithmus falsch informiert ist, wie sich die alten und neuen Strukturen aufeinander beziehen.</span><span class="sxs-lookup"><span data-stu-id="3151d-188">This usually results in having to build longer edit scripts because the diff algorithm is misinformed about how the old and new structures relate to each other.</span></span>

### <a name="guidance-and-conclusions"></a><span data-ttu-id="3151d-189">Anleitungen und Schlussfolgerungen</span><span class="sxs-lookup"><span data-stu-id="3151d-189">Guidance and conclusions</span></span>

* <span data-ttu-id="3151d-190">Die App-Leistung leidet, wenn Sequenznummern dynamisch generiert werden.</span><span class="sxs-lookup"><span data-stu-id="3151d-190">App performance suffers if sequence numbers are generated dynamically.</span></span>
* <span data-ttu-id="3151d-191">Das Framework kann zur Laufzeit automatisch keine eigenen Sequenznummern erstellen, da die erforderlichen Informationen nicht vorhanden sind, es sei denn, sie werden zur Kompilierzeit aufgezeichnet.</span><span class="sxs-lookup"><span data-stu-id="3151d-191">The framework can't create its own sequence numbers automatically at runtime because the necessary information doesn't exist unless it's captured at compile time.</span></span>
* <span data-ttu-id="3151d-192">Schreiben Sie keine langen Blöcke manuell implementierter <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder>-Logik.</span><span class="sxs-lookup"><span data-stu-id="3151d-192">Don't write long blocks of manually-implemented <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> logic.</span></span> <span data-ttu-id="3151d-193">Bevorzugen Sie `.razor`-Dateien, und ermöglichen Sie es dem Compiler, die Sequenznummern zu verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="3151d-193">Prefer `.razor` files and allow the compiler to deal with the sequence numbers.</span></span> <span data-ttu-id="3151d-194">Wenn Sie manuelle <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder>-Logik nicht vermeiden können, teilen Sie lange Codeblöcke in kleinere Teile auf, die in <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.OpenRegion%2A>/<xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.CloseRegion%2A>-Aufrufe eingebunden werden.</span><span class="sxs-lookup"><span data-stu-id="3151d-194">If you're unable to avoid manual <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> logic, split long blocks of code into smaller pieces wrapped in <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.OpenRegion%2A>/<xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.CloseRegion%2A> calls.</span></span> <span data-ttu-id="3151d-195">Jede Region verfügt über einen eigenen separaten Bereich von Sequenznummern, sodass Sie in jeder Region von 0 (Null; oder von jeder anderen beliebigen Zahl) aus starten können.</span><span class="sxs-lookup"><span data-stu-id="3151d-195">Each region has its own separate space of sequence numbers, so you can restart from zero (or any other arbitrary number) inside each region.</span></span>
* <span data-ttu-id="3151d-196">Wenn Sequenznummern hartcodiert sind, erfordert der diff-Algorithmus nur, dass die Sequenznummern den Wert erhöhen.</span><span class="sxs-lookup"><span data-stu-id="3151d-196">If sequence numbers are hardcoded, the diff algorithm only requires that sequence numbers increase in value.</span></span> <span data-ttu-id="3151d-197">Die Anfangswerte und Lücken sind irrelevant.</span><span class="sxs-lookup"><span data-stu-id="3151d-197">The initial value and gaps are irrelevant.</span></span> <span data-ttu-id="3151d-198">Eine legitime Option besteht darin, die Codezeilennummer als Sequenznummer zu verwenden oder von 0 (Null) aus zu starten und Werte in Einer- oder Hunderterschritten (oder einem beliebigen bevorzugten Intervall) zu erhöhen.</span><span class="sxs-lookup"><span data-stu-id="3151d-198">One legitimate option is to use the code line number as the sequence number, or start from zero and increase by ones or hundreds (or any preferred interval).</span></span> 
* <span data-ttu-id="3151d-199">Blazor verwendet Sequenznummern, während andere Benutzeroberflächenframeworks diese nicht für Verzeichnisvergleiche verwenden.</span><span class="sxs-lookup"><span data-stu-id="3151d-199">Blazor uses sequence numbers, while other tree-diffing UI frameworks don't use them.</span></span> <span data-ttu-id="3151d-200">Das Vergleichen ist weitaus schneller, wenn Sequenznummern verwendet werden. Außerdem hat Blazor den Vorteil eines Kompilierungsschritts, der Sequenznummern automatisch für Entwickler verarbeitet, die `.razor`-Dateien erstellen.</span><span class="sxs-lookup"><span data-stu-id="3151d-200">Diffing is far faster when sequence numbers are used, and Blazor has the advantage of a compile step that deals with sequence numbers automatically for developers authoring `.razor` files.</span></span>
