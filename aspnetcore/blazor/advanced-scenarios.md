---
title: Erweiterte ASP.NET Core-Blazor-Szenarios
author: guardrex
description: Informieren Sie sich über erweiterte Szenarios in Blazor einschließlich der Integration manueller RenderTreeBuilder-Logik in eine App.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/18/2020
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
uid: blazor/advanced-scenarios
ms.openlocfilehash: 95714b3c0d21d3b348a9a8a984e2a42e7708499e
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/04/2021
ms.locfileid: "93056555"
---
# <a name="aspnet-core-no-locblazor-advanced-scenarios"></a>Erweiterte ASP.NET Core-Blazor-Szenarios

Von [Luke Latham](https://github.com/guardrex) und [Daniel Roth](https://github.com/danroth27)

## <a name="no-locblazor-server-circuit-handler"></a>Blazor Server-Verbindungshandler

In Blazor Server kann mithilfe von Code ein *Verbindungshandler* definiert werden, mit dem Code für Zustandsänderungen einer Benutzerverbindung ausgeführt werden kann. Ein Verbindungshandler wird durch das Ableiten von `CircuitHandler` und Registrieren der Klasse im Dienstcontainer der App implementiert. Im folgenden Beispiel eines Verbindungshandlers werden geöffnete SignalR-Verbindungen nachverfolgt:

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

Verbindungshandler werden mithilfe von DI registriert. Bereichsbezogene Instanzen werden pro Verbindungsinstanz erstellt. Mithilfe von `TrackingCircuitHandler` aus dem vorherigen Beispiel wird ein Singletondienst erstellt, da der Zustand aller Verbindungen nachverfolgt werden muss:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...
    services.AddSingleton<CircuitHandler, TrackingCircuitHandler>();
}
```

Wenn die Methoden eines benutzerdefinierten Verbindungshandlers einen Ausnahmefehler auslösen, ist dieser Ausnahmefehler für die Blazor Server-Verbindung schwerwiegend. Umschließen Sie den Code in einer oder mehreren [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch)-Anweisungen mit Fehlerbehandlung und -protokollierung, um Ausnahmen im Code oder in aufgerufenen Methoden eines Handlers zu tolerieren.

Wenn eine Verbindung beendet wird, weil ein Benutzer die Verbindung getrennt hat und das Framework den Verbindungsstatus bereinigt, gibt das Framework den DI-Bereich der Verbindung frei. Wenn der Bereich freigegeben wird, werden alle Dienste im Verbindungsbereich verworfen, die <xref:System.IDisposable?displayProperty=fullName> implementieren. Wenn ein DI-Dienst während der Freigabe einen Ausnahmefehler auslöst, protokolliert das Framework die Ausnahme.

## <a name="manual-rendertreebuilder-logic"></a>Manuelle RenderTreeBuilder-Logik

<xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> stellt Methoden zum Bearbeiten von Komponenten und Elementen bereit. Dazu gehört auch das manuelle Erstellen von Komponenten in C#-Code.

> [!NOTE]
> Die Verwendung von <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> zum Erstellen von Komponenten ist ein erweitertes Szenario. Eine falsch formatierte Komponente (z. B. ein nicht geschlossenes Markuptag) kann zu undefiniertem Verhalten führen.

Beachten Sie die folgende `PetDetails`-Komponente, die manuell in eine andere Komponente integriert werden kann.

```razor
<h2>Pet Details Component</h2>

<p>@PetDetailsQuote</p>

@code
{
    [Parameter]
    public string PetDetailsQuote { get; set; }
}
```

Im folgenden Beispiel generiert die Schleife in der `CreateComponent`-Methode drei `PetDetails`-Komponenten. In <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder>-Methoden mit einer Sequenznummer sind die Sequenznummern Zeilennummern des Quellcodes. Der diff-Algorithmus von Blazor basiert auf den Sequenznummern, die unterschiedlichen Codezeilen und nicht unterschiedlichen Aufrufen entsprechen. Hartcodieren Sie die Argumente für Sequenznummern, wenn Sie eine Komponente mit <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder>-Methoden erstellen. **Die Verwendung einer Berechnung oder eines Leistungszählers zum Generieren der Sequenznummer kann zu schlechter Leistung führen.** Weitere Informationen finden Sie im Abschnitt [Auf Codezeilennummern und nicht auf die Ausführungsreihenfolge bezogene Sequenznummern](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order).

`BuiltContent`-Komponente:

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
> Die Typen in <xref:Microsoft.AspNetCore.Components.RenderTree> ermöglichen die Verarbeitung der *Ergebnisse* von Renderingvorgängen. Hierbei handelt es sich um interne Details der Blazor-Frameworkimplementierung. Diese Typen sollten als *instabil* betrachtet werden und in zukünftigen Versionen geändert werden.

### <a name="sequence-numbers-relate-to-code-line-numbers-and-not-execution-order"></a>Auf Codezeilennummern und nicht auf die Ausführungsreihenfolge bezogene Sequenznummern

Razor-Komponentendateien (`.razor`) werden stets kompiliert. Die Kompilierung stellt gegenüber der Interpretation von Code einen Vorteil dar, da der Kompilierungsschritt zum Einfügen von Informationen verwendet werden kann, die die App-Leistung zur Laufzeit erhöhen können.

Ein wichtiges Beispiel für diese Verbesserungen sind *Sequenznummern*. Sequenznummern geben der Laufzeit an, welche Ausgaben aus den unterschiedlichen und geordneten Codezeilen stammen. Diese Informationen werden von der Laufzeit verwendet, um effiziente und zeitlich lineare Strukturunterschiede zu generieren. Diese Methode ist viel schneller als es für einen allgemeinen diff-Strukturalgorithmus normalerweise möglich ist.

Betrachten Sie die Datei der folgenden Razor-Komponente (`.razor`):

```razor
@if (someFlag)
{
    <text>First</text>
}

Second
```

Der vorangehende Code wird in etwa wie folgt kompiliert:

```csharp
if (someFlag)
{
    builder.AddContent(0, "First");
}

builder.AddContent(1, "Second");
```

Wenn der Code zum ersten Mal ausgeführt wird, erhält der Generator Folgendes, wenn `someFlag` `true` ist:

| Sequenz | Typ      | Daten   |
| :------: | --------- | :----: |
| 0        | Textknoten | First  |
| 1        | Textknoten | Second |

Stellen Sie sich vor, dass `someFlag` `false` wird und das Markup wieder gerendert wird. Dieses Mal empfängt der Generator Folgendes:

| Sequenz | Typ       | Daten   |
| :------: | ---------- | :----: |
| 1        | Textknoten  | Second |

Wenn die Laufzeit einen diff-Algorithmus ausführt, wird angezeigt, dass das Element bei Sequenz `0` entfernt wurde. Daraufhin wird das folgende triviale *Bearbeitungsskript* generiert:

* Entfernen Sie den ersten Textknoten.

### <a name="the-problem-with-generating-sequence-numbers-programmatically"></a>Problem beim programmgesteuerten Generieren von Sequenznummern

Angenommen, Sie haben stattdessen die folgende Buildlogik für die Renderstruktur geschrieben:

```csharp
var seq = 0;

if (someFlag)
{
    builder.AddContent(seq++, "First");
}

builder.AddContent(seq++, "Second");
```

Die erste Ausgabe lautet nun wie folgt:

| Sequenz | Typ      | Daten   |
| :------: | --------- | :----: |
| 0        | Textknoten | First  |
| 1        | Textknoten | Second |

Dieses Ergebnis ist mit dem des vorherigen Falls identisch, sodass keine negativen Probleme aufgetreten sind. `someFlag` ist beim zweiten Rendering `false`, und die Ausgabe lautet wie folgt:

| Sequenz | Typ      | Daten   |
| :------: | --------- | ------ |
| 0        | Textknoten | Second |

Dieses Mal zeigt der diff-Algorithmus an, dass *zwei* Änderungen aufgetreten sind, und der Algorithmus generiert das folgende Bearbeitungsskript:

* Ändern Sie den Wert des ersten Textknotens in `Second`.
* Entfernen Sie den zweiten Textknoten.

Durch das Erzeugen der Sequenznummern sind alle nützlichen Informationen dazu verloren gegangen, wo die `if/else`-Branches und -Schleifen im ursprünglichen Code vorhanden waren. Dies führt zu einem **doppelt so langen diff** wie zuvor.

Dies ist ein triviales Beispiel. In realistischeren Fällen mit komplexen und tief geschachtelten Strukturen und vor allem mit Schleifen sind die Leistungskosten in der Regel höher. Anstatt sofort festzustellen, welche Schleifenblöcke oder Branches eingefügt oder entfernt wurden, muss der diff-Algorithmus eine tiefe Rekursion der Renderstrukturen durchführen. Dies führt in der Regel dazu, dass längere Bearbeitungsskripts erstellt werden müssen, da der diff-Algorithmus falsch informiert ist, wie sich die alten und neuen Strukturen aufeinander beziehen.

### <a name="guidance-and-conclusions"></a>Anleitungen und Schlussfolgerungen

* Die App-Leistung leidet, wenn Sequenznummern dynamisch generiert werden.
* Das Framework kann zur Laufzeit automatisch keine eigenen Sequenznummern erstellen, da die erforderlichen Informationen nicht vorhanden sind, es sei denn, sie werden zur Kompilierzeit aufgezeichnet.
* Schreiben Sie keine langen Blöcke manuell implementierter <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder>-Logik. Bevorzugen Sie `.razor`-Dateien, und ermöglichen Sie es dem Compiler, die Sequenznummern zu verarbeiten. Wenn Sie manuelle <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder>-Logik nicht vermeiden können, teilen Sie lange Codeblöcke in kleinere Teile auf, die in <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.OpenRegion%2A>/<xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.CloseRegion%2A>-Aufrufe eingebunden werden. Jede Region verfügt über einen eigenen separaten Bereich von Sequenznummern, sodass Sie in jeder Region von 0 (Null; oder von jeder anderen beliebigen Zahl) aus starten können.
* Wenn Sequenznummern hartcodiert sind, erfordert der diff-Algorithmus nur, dass die Sequenznummern den Wert erhöhen. Die Anfangswerte und Lücken sind irrelevant. Eine legitime Option besteht darin, die Codezeilennummer als Sequenznummer zu verwenden oder von 0 (Null) aus zu starten und Werte in Einer- oder Hunderterschritten (oder einem beliebigen bevorzugten Intervall) zu erhöhen. 
* Blazor verwendet Sequenznummern, während andere Benutzeroberflächenframeworks diese nicht für Verzeichnisvergleiche verwenden. Das Vergleichen ist weitaus schneller, wenn Sequenznummern verwendet werden. Außerdem hat Blazor den Vorteil eines Kompilierungsschritts, der Sequenznummern automatisch für Entwickler verarbeitet, die `.razor`-Dateien erstellen.
