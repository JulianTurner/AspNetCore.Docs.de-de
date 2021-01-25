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
ms.openlocfilehash: 1a4d4116b8a6d9266bbacbbdd8f20dc49b4e1db0
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253843"
---
# <a name="aspnet-core-no-locblazor-component-rendering"></a>Blazor-Komponentenrendering in ASP.NET Core

Von [Steve Sanderson](https://github.com/SteveSandersonMS)

Komponenten *müssen* gerendert werden, wenn sie erstmalig in der Komponentenhierarchie von ihrer übergeordneten Komponente hinzugefügt werden. Dies ist der einzige Fall, in dem eine Komponente kompromisslos gerendert werden muss.

Komponenten *können* jederzeit gerendert werden. Dies erfolgt gemäß ihrer eigenen Logik und Konventionen.

## <a name="conventions-for-componentbase"></a>Konventionen für `ComponentBase`

Razor-Komponenten (`.razor`) erben standardmäßig von der <xref:Microsoft.AspNetCore.Components.ComponentBase>-Basisklasse. Diese enthält Logik, die das erneute Rendern zu folgenden Zeitpunkten auslöst:

* Nach Anwenden aktualisierter Parameter einer übergeordneten Komponente
* Nach Anwenden eines aktualisierten Werts für einen kaskadierenden Parameter
* Nach Benachrichtigung zu einem Ereignis und Aufrufen eines der dazugehörigen Ereignishandler
* Nach einem Aufruf der dazugehörigen <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>-Methode

Von <xref:Microsoft.AspNetCore.Components.ComponentBase> geerbte Komponenten überspringen wiederholte Rendervorgänge aufgrund von Parameteraktualisierungen, wenn eine der folgenden Aussagen zutrifft:

* Alle Parameterwerte weisen einen bekannten unveränderlichen primitiven Typ auf (z. B. `int`, `string`, `DateTime`) und haben sich seit Festlegung der vorherigen Parameter nicht geändert.
* Die <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>-Methode der Komponente gibt `false` zurück.

Weitere Informationen zu <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> finden Sie unter <xref:blazor/webassembly-performance-best-practices#use-of-shouldrender>.

## <a name="control-the-rendering-flow"></a>Steuern des Renderingflows

In den meisten Fällen führen <xref:Microsoft.AspNetCore.Components.ComponentBase>-Konventionen zu erneuten Renderingvorgängen für die richtige Teilmenge der Komponenten, nachdem ein Ereignis auftritt. Entwickler müssen in der Regel keine manuelle Logik bereitstellen, damit das Framework die Information erhält, welche Komponenten erneut gerendert werden müssen und wann sie erneut gerendert werden müssen. Der Gesamteffekt der Konventionen des Frameworks besteht darin, dass die Komponente, für die ein Ereignis auftritt, sich selbst erneut rendert. Dies löst rekursiv ein erneutes Rendern der nachfolgenden Komponenten aus, deren Parameterwerte sich möglicherweise geändert haben.

Weitere Informationen zu den Auswirkungen auf die Leistung der Konventionen des Frameworks und dazu, wie Sie die Komponentenhierarchie einer App optimieren können, finden Sie unter <xref:blazor/webassembly-performance-best-practices#optimize-rendering-speed>.

## <a name="when-to-call-statehaschanged"></a>Gründe für einen Aufruf von `StateHasChanged`

Die <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A?displayProperty=nameWithType>-Methode ermöglicht es Ihnen, jederzeit ein Rendering auszulösen. Achten Sie jedoch darauf, <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> nicht unnötigerweise aufzurufen. Dies ist ein häufiger Fehler, da so unnötige Renderingkosten verursacht werden.

In folgenden Fällen sollte <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> *nicht* aufgerufen werden müssen:

* Routinemäßige Verarbeitung von Ereignissen (synchron und asynchron), da <xref:Microsoft.AspNetCore.Components.ComponentBase> ein Rendering für die meisten Routingereignishandler auslöst
* Implementieren typischer Lebenszykluslogik (synchron und asynchron), z. B. [`OnInitialized`](xref:blazor/components/lifecycle#component-initialization-methods) oder [`OnParametersSetAsync`](xref:blazor/components/lifecycle#after-parameters-are-set), da <xref:Microsoft.AspNetCore.Components.ComponentBase> ein Rendering für typische Lebenszyklusereignisse auslöst

In den in den folgenden Abschnitten beschriebenen Fällen ergibt es jedoch Sinn, <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> aufzurufen:

* [Ein asynchroner Handler beinhaltet mehrere asynchrone Phasen.](#an-asynchronous-handler-involves-multiple-asynchronous-phases)
* [Ein Aufruf von einer externen Quelle an das Blazor-Renderingsystem bzw. -Ereignisverarbeitungssystem wird empfangen.](#receiving-a-call-from-something-external-to-the-blazor-rendering-and-event-handling-system)
* [Eine Komponente soll außerhalb der untergeordneten Struktur gerendert werden, die von einem bestimmten Ereignis erneut gerendert wird.](#to-render-a-component-outside-the-subtree-thats-rerendered-by-a-particular-event)

### <a name="an-asynchronous-handler-involves-multiple-asynchronous-phases"></a>Ein asynchroner Handler beinhaltet mehrere asynchrone Phasen

Berücksichtigen Sie die folgende `Counter`-Komponente, die die Anzahl pro Klick viermal aktualisiert.

`Pages/Counter.razor`:

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

Aufgrund der Art, wie Aufgaben in .NET definiert werden, kann ein Empfänger von <xref:System.Threading.Tasks.Task> nur den endgültigen Abschluss beobachten, keine asynchronen Zwischenzustände. Deshalb kann <xref:Microsoft.AspNetCore.Components.ComponentBase> nur ein erneutes Rendering auslösen, wenn <xref:System.Threading.Tasks.Task> zuerst zurückgegeben wird und wenn <xref:System.Threading.Tasks.Task> endgültig abgeschlossen ist. Die Information, dass an anderen Zwischenpunkten ein erneutes Rendering durchgeführt werden soll, kann nicht gegeben werden. Wenn Sie an Zwischenpunkten ein erneutes Rendering durchführen möchten, verwenden Sie <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>.

### <a name="receiving-a-call-from-something-external-to-the-no-locblazor-rendering-and-event-handling-system"></a>Ein Aufruf von einer externen Quelle an das Blazor-Renderingsystem bzw. -Ereignisverarbeitungssystem wird empfangen

<xref:Microsoft.AspNetCore.Components.ComponentBase> kennt nur die eigenen Lebenszyklusmethoden und von Blazor ausgelösten Ereignisse. <xref:Microsoft.AspNetCore.Components.ComponentBase> hat keine Informationen zu anderen Ereignissen, die möglicherweise in Ihrem Code auftreten. Alle C#-Ereignisse, die von einem benutzerdefinierten Datenspeicher ausgelöst werden, sind beispielsweise für Blazor nicht bekannt. Damit solche Ereignisse ein erneutes Rendering auslösen, damit aktualisierte Werte auf der Benutzeroberfläche angezeigt werden, verwenden Sie <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>.

In einem anderen Anwendungsfall sollten Sie die folgende `Counter`-Komponente berücksichtigen, die <xref:System.Timers.Timer?displayProperty=fullName> verwendet, um die Anzahl in regelmäßigen Abständen zu aktualisieren, und <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> aufruft, um die Benutzeroberfläche zu aktualisieren.

`Pages/Counter.razor`:

```razor
@page "/counter"
@using System.Timers
@implements IDisposable

<p>Current count: @currentCount</p>

@code {
    private int currentCount = 0;
    private Timer timer = new Timer(1000);

    protected override void OnInitialized()
    {
        timer.Elapsed += (sender, eventArgs) => OnTimerCallback();
        timer.Start();
    }

    void OnTimerCallback()
    {
        _ = InvokeAsync(() =>
        {
            currentCount++;
            StateHasChanged();
        });
    }

    void IDisposable.Dispose() => timer.Dispose();
}
```

Im vorherigen Beispiel muss `OnTimerCallback` <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> aufrufen, da Blazor die Änderungen an `currentCount` im Rückruf nicht bekannt sind. `OnTimerCallback` wird außerhalb eines von Blazor verwalteten Renderingflows oder einer Ereignisbenachrichtigung ausgeführt.

Auf ähnliche Weise ist es erforderlich, die Logik in Blazor zu umschließen, da der Rückruf außerhalb des Synchronisierungskontexts von <xref:Microsoft.AspNetCore.Components.ComponentBase.InvokeAsync%2A?displayProperty=nameWithType> aufgerufen wird, damit sie in den Synchronisierungskontext des Renderers verschoben wird. <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> kann nur im Synchronisierungskontext des Renderers aufgerufen werden und gibt andernfalls eine Ausnahme zurück. Dies ist äquivalent zum Marshallen des Benutzeroberflächenthreads in anderen Benutzeroberflächenframeworks.

### <a name="to-render-a-component-outside-the-subtree-thats-rerendered-by-a-particular-event"></a>Eine Komponente soll außerhalb der untergeordneten Struktur gerendert werden, die von einem bestimmten Ereignis erneut gerendert wird

Möglicherweise ist es für Ihre Benutzeroberfläche erforderlich, ein Ereignis an eine Komponente zu senden, einen Status zu ändern und eine andere Komponente erneut zu rendern, die kein Nachfolger der Komponente ist, die das Ereignis empfängt.

Eine Möglichkeit, mit diesem Szenario umzugehen, ist die Verwendung einer Klasse für die *Statusverwaltung*, z. B. in Form eines DI-Diensts, die in verschiedene Komponenten injiziert wird. Wenn eine Komponente eine Methode für den Status-Manager aufruft, kann der Status-Manager ein C#-Ereignis auslösen, das dann von einer unabhängigen Komponente empfangen wird.

Da sich diese C#-Ereignisse außerhalb der Blazor-Renderingpipeline befinden, müssen Sie <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> für andere Komponenten aufrufen, die als Reaktion auf Ereignisse des Status-Managers gerendert werden sollen.

Dies ähnelt dem vorherigen Fall mit <xref:System.Timers.Timer?displayProperty=fullName> im [vorherigen Abschnitt](#receiving-a-call-from-something-external-to-the-blazor-rendering-and-event-handling-system). Da die Ausführungsaufrufliste in der Regel im Synchronisierungskontext des Renderers verbleibt, ist <xref:Microsoft.AspNetCore.Components.ComponentBase.InvokeAsync%2A> normalerweise nicht erforderlich. <xref:Microsoft.AspNetCore.Components.ComponentBase.InvokeAsync%2A> ist nur erforderlich, wenn sich die Logik außerhalb des Synchronisierungskontexts befindet, z. B. wenn <xref:System.Threading.Tasks.Task.ContinueWith%2A> für <xref:System.Threading.Tasks.Task> aufgerufen wird oder <xref:System.Threading.Tasks.Task> mit [`ConfigureAwait(false)`](xref:System.Threading.Tasks.Task.ConfigureAwait%2A) erwartet wird.
