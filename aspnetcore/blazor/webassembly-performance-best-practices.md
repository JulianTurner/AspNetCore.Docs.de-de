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
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/04/2021
ms.locfileid: "95417382"
---
# <a name="aspnet-core-no-locblazor-webassembly-performance-best-practices"></a>Best Practices zur Blazor WebAssembly-Leistung in ASP.NET Core

Von [Pranav Krishnamoorthy](https://github.com/pranavkm) und [Steve Sanderson](https://github.com/SteveSandersonMS)

Blazor WebAssembly wurde sorgfältig entworfen und optimiert, um hohe Leistung in den realistischsten Anwendungsbenutzeroberflächen-Szenarien zu ermöglichen. Die besten Ergebnisse zu erzielen, hängt jedoch davon ab, dass die Entwickler die richtigen Muster und Features verwenden. Berücksichtigen Sie folgende Aspekte:

* **Laufzeitdurchsatz**: Der .NET-Code wird in einem Interpreter innerhalb der WebAssembly-Laufzeit ausgeführt, sodass der CPU-Durchsatz begrenzt ist. In anspruchsvollen Szenarien profitiert die App vom [Optimieren der Renderinggeschwindigkeit](#optimize-rendering-speed).
* **Startzeit**: Die App überträgt eine .NET-Laufzeit an den Browser. Daher ist es wichtig, Features zu verwenden, die die [Größe des Anwendungsdownloads minimieren](#minimize-app-download-size).

## <a name="optimize-rendering-speed"></a>Optimieren der Renderinggeschwindigkeit

Die folgenden Abschnitte enthalten Empfehlungen zum Minimieren der Renderingworkload und zur Verbesserung der Reaktionsfähigkeit der Benutzeroberfläche. Wenn Sie diese Ratschläge befolgen, können Sie mühelos *mindestens eine zehnfache Verbesserung* der Geschwindigkeit des Renderings der Benutzeroberfläche erreichen.

### <a name="avoid-unnecessary-rendering-of-component-subtrees"></a>Vermeiden von unnötigem Rendering der Komponentenunterstrukturen

Zur Laufzeit sind Komponenten als Hierarchie vorhanden. Eine Stammkomponente weist untergeordnete Komponenten auf. Die untergeordneten Elemente des Stamms verfügen wiederum über eigene untergeordnete Komponenten usw. Wenn ein Ereignis auftritt, z. B. ein Benutzer eine Schaltfläche auswählt, entscheidet Blazor folgendermaßen, welche Komponenten erneut gerendert werden:

 1. Das Ereignis selbst wird an den Ereignishandler der gerenderten Komponente gesendet. Nach Ausführung des Ereignishandlers wird diese Komponente erneut gerendert.
 1. Jedes Mal, wenn eine Komponente erneut gerendert wird, wird eine neue Kopie der Parameterwerte für jede ihrer untergeordneten Komponenten bereitgestellt.
 1. Beim Empfang eines neuen Satzes von Parameterwerten wählt jede Komponente aus, ob sie erneut gerendert werden muss. Standardmäßig werden Komponenten erneut gerendert, wenn die Parameterwerte geändert wurden (wenn sie z. B. veränderbare Objekte sind).

Die letzten beiden Schritte dieser Sequenz werden rekursiv die Komponentenhierarchie hinab fortgesetzt. In vielen Fällen wird die gesamte Unterstruktur erneut gerendert. Dies bedeutet, dass Ereignisse, die auf Komponenten auf hoher Ebene gerichtet sind, aufwändige erneute Renderingvorgänge verursachen können, da alles unterhalb dieses Punkts erneut gerendert werden muss.

Wenn Sie diesen Vorgang unterbrechen und die Renderingrekursion in einer bestimmten Unterstruktur unterbrechen möchten, können Sie Folgendes tun:

 * Stellen Sie sicher, dass alle Parameter für eine bestimmte Komponente primitive unveränderliche Typen sind (z. B. `string`, `int`, `bool`, `DateTime` und andere). Die integrierte Logik zum automatischen Erkennen von Änderungen überspringt das erneute Rendern, wenn keiner dieser Parameterwerte geändert wurde. Wenn Sie eine untergeordnete Komponente mit `<Customer CustomerId="@item.CustomerId" />` rendern, wobei `CustomerId` ein `int`-Wert ist, wird sie nur dann erneut gerendert, wenn `item.CustomerId` sich ändert.
 * Wenn Sie nicht primitive Parameterwerte wie benutzerdefinierte Modelltypen, Ereignisrückrufe oder <xref:Microsoft.AspNetCore.Components.RenderFragment>-Werte akzeptieren müssen, können Sie <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> überschreiben, um die Entscheidung über das Rendern zu steuern, was im Abschnitt [Verwendung von `ShouldRender`](#use-of-shouldrender) beschrieben wird.

Indem Sie die erneute Ausführung ganzer Unterstrukturen überspringen, können Sie möglicherweise den größten Teil des Renderingaufwands beim Auftreten eines Ereignisses vermeiden.

Sie können untergeordnete Komponenten speziell ausklammern, damit Sie das erneute Rendern dieses Teils der Benutzeroberfläche überspringen können. Dies ist eine gültige Methode zum Reduzieren des Aufwands für das Rendern einer übergeordneten Komponente.

#### <a name="use-of-shouldrender"></a>Verwendung von `ShouldRender`

Wenn eine nur auf die Benutzeroberfläche bezogene Komponente erstellt wird, die sich nach dem ursprünglichen Rendern (unabhängig von Parameterwerten) nicht mehr ändert, konfigurieren Sie <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> so, dass `false` zurückgegeben wird:

```razor
@code {
    protected override bool ShouldRender() => false;
}
```

Wenn die Komponente nur ein erneutes Rendern erfordert, wenn die Parameterwerte auf bestimmte Weise geändert werden, können Sie die erforderlichen Informationen mit privaten Feldern nachverfolgen, um Änderungen zu erkennen. Im folgenden Beispiel basiert `shouldRender` auf der Überprüfung auf beliebige Änderungen oder Mutationen, die ein erneutes Rendern auslösen sollten. `prevOutboundFlightId` und `prevInboundFlightId` verfolgen Informationen für das nächste mögliche Update nach:

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

Im vorhergehenden Code kann ein Ereignishandler auch `shouldRender` auf `true` festlegen, sodass die Komponente nach dem Ereignis erneut gerendert wird.

Bei den meisten Komponenten ist dieses Maß an manueller Steuerung nicht erforderlich. Sie sollten das Überspringen des Renderns von Unterstrukturen nur dann in Erwägung ziehen, wenn das Rendern dieser Unterstrukturen besonders aufwändig ist und in der Benutzeroberfläche Verzögerungen verursacht.

Weitere Informationen finden Sie unter <xref:blazor/components/lifecycle>.

::: moniker range=">= aspnetcore-5.0"

### <a name="virtualization"></a>Virtualisierung

Wenn große Teile der Benutzeroberfläche in einer Schleife gerendert werden, z. B. Listen oder Raster mit Tausenden von Einträgen, kann die schiere Menge an Renderingvorgängen zu einer Verzögerung beim Rendern der Benutzeroberfläche und somit zu einer negativen Benutzererfahrung führen. Da der Benutzer ohne Scrollen nur eine kleine Anzahl von Elementen gleichzeitig sehen kann, ist es offenbar Verschwendung, so viel Zeit in das Rendern von Elementen zu investieren, die derzeit nicht sichtbar sind.

Darum stellt Blazor eine integrierte [`<Virtualize>`-Komponente](xref:blazor/components/virtualization) bereit, die das Aussehen und Scrollverhalten einer beliebig großen Liste definiert, aber tatsächlich nur die Listenelemente rendert, die sich innerhalb des aktuellen Scrollviewports befinden. Dies bedeutet beispielsweise, dass nur der Aufwand zum Rendern von 20 Elementen erforderlich ist, die gleichzeitig sichtbar sind, wenn die App über eine Liste mit 100.000 Einträgen verfügt. Mit Verwendung der `<Virtualize>`-Komponente können Sie die Leistung der Benutzeroberfläche um Größenordnungen hochskalieren.

`<Virtualize>` kann in folgenden Fällen verwendet werden:

 * Rendern eines Satzes von Datenelementen in einer Schleife.
 * Die meisten Elemente sind beim Scrollen nicht sichtbar.
 * Die gerenderten Elemente haben exakt die gleiche Größe. Wenn der Benutzer zu einem beliebigen Punkt scrollt, kann die Komponente die anzuzeigenden sichtbaren Elemente berechnen.

Es folgt ein Beispiel für eine nicht virtualisierte Liste:

```razor
<div class="all-flights" style="height:500px;overflow-y:scroll">
    @foreach (var flight in allFlights)
    {
        <FlightSummary @key="flight.FlightId" Flight="@flight" />
    }
</div>
```

Wenn die `allFlights`-Sammlung 10.000 Elemente enthält, instanziiert und rendert sie 10.000 `<FlightSummary>`-Komponenteninstanzen. Im Vergleich dazu ein Beispiel für eine virtualisierte Liste:

```razor
<div class="all-flights" style="height:500px;overflow-y:scroll">
    <Virtualize Items="@allFlights" Context="flight">
        <FlightSummary @key="flight.FlightId" Flight="@flight" />
    </Virtualize>
</div>
```

Obwohl die resultierende Benutzeroberfläche für einen Benutzer gleich aussieht, instanziiert und rendert die Komponente im Hintergrund nur so viele `<FlightSummary>`-Instanzen, wie zum Ausfüllen des scrollbaren Bereichs benötigt werden. Der angezeigte Satz von `<FlightSummary>`-Instanzen wird neu berechnet und gerendert, wenn der Benutzer scrollt.

`<Virtualize>` hat noch weitere Vorteile. Wenn eine Komponente z. B. Daten von einer externen API anfordert, ermöglicht `<Virtualize>` der Komponente, nur das Segment von Datensätzen abzurufen, die dem aktuell sichtbaren Bereich entsprechen, anstatt alle Daten aus der Sammlung herunterzuladen.

Weitere Informationen finden Sie unter <xref:blazor/components/virtualization>.

::: moniker-end

### <a name="create-lightweight-optimized-components"></a>Erstellen von einfachen optimierten Komponenten

Die meisten Blazor-Komponenten erfordern keine aggressiven Optimierungsbemühungen. Dies liegt daran, dass die meisten Komponenten sich nicht häufig in der Benutzeroberfläche wiederholen und nicht oft neu gerendert werden. Beispielsweise werden `@page`-Komponenten und Komponenten, die Benutzeroberflächenelemente auf hoher Ebene darstellen (z. B. Dialoge oder Formulare) höchstwahrscheinlich nur einzeln und nur als Antwort auf eine Benutzeraktion erneut gerendert. Diese Komponenten verursachen keine großen Renderingworkloads, sodass Sie eine beliebige Kombination von Frameworkfeatures verwenden können, ohne sich besonders um die Renderleistung sorgen zu müssen.

Es gibt jedoch auch gängige Szenarien, in denen Sie Komponenten erstellen, die im großen Stil wiederholt werden müssen. Beispiel:

 * Umfangreiche geschachtelte Formen können aus Hunderten von einzelnen Eingaben, Bezeichnungen und anderen Elementen bestehen.
 * Raster verfügen möglicherweise über Tausende von Zellen.
 * Streudiagramme können Millionen von Datenpunkten enthalten.

Wenn jede Einheit als separate Komponenteninstanz modelliert wird, entstehen so viele, dass Probleme bei der Renderingleistung auftreten können. In diesem Abschnitt wird erläutert, wie Sie solche Komponenten vereinfachen, sodass die Benutzeroberfläche schnell und reaktionsfähig bleibt.

#### <a name="avoid-thousands-of-component-instances"></a>Vermeiden von Tausenden von Komponenteninstanzen

Jede Komponente ist eine separate Insel, die unabhängig von ihren übergeordneten und untergeordneten Elementen gerendert werden kann. Indem Sie auswählen, wie die Benutzeroberfläche in eine Hierarchie von Komponenten aufgeteilt werden soll, bestimmen Sie die Granularität des Renderings der Benutzeroberfläche. Dies kann für die Leistung entweder gut oder schlecht sein.

 * Wenn Sie die Benutzeroberfläche in mehr Komponenten aufteilen, können kleinere Teile der Benutzeroberfläche erneut gerendert werden, wenn Ereignisse auftreten. Wenn ein Benutzer z. B. auf eine Schaltfläche in einer Tabellenzeile klickt, können Sie vielleicht nur diese einzelne Zeile anstelle der gesamten Seite oder Tabelle erneut rendern lassen.
 * Jede zusätzliche Komponente benötigt jedoch zusätzlichen Arbeitsspeicher und CPU-Mehraufwand, um den unabhängigen Zustand und den Renderinglebenszyklus zu verwalten.

Beim Optimieren der Leistung von Blazor WebAssembly in .NET 5 haben wir einen Renderingmehraufwand von ca. 0,06 ms pro Komponenteninstanz gemessen. Dies basiert bei Ausführung auf einem typischen Laptop auf einer einfachen Komponente, die drei Parameter akzeptiert. Intern entsteht der Mehraufwand größtenteils durch das Abrufen des Status pro Komponente aus Wörterbüchern und Übergabe und Empfang von Parametern. Beim Hinzufügen 2.000 zusätzlicher Komponenteninstanzen ergibt sich durch Multiplikation, dass die Renderingzeit um 0,12 Sekunden verlängert und die Benutzeroberfläche den Benutzern langsam erscheinen würde.

Komponenten können einfacher gestaltet werden, sodass Sie mehr von Ihnen haben können, aber häufig besteht die leistungsfähigere Methode darin, nicht so viele Komponenten zu haben. In den folgenden Abschnitten werden zwei Ansätze beschrieben.

##### <a name="inline-child-components-into-their-parents"></a>Inline-Einfügung untergeordneter Komponenten in ihre übergeordneten Komponenten

Sehen Sie sich die folgende Komponente an, die eine Sequenz von untergeordneten Komponenten rendert:

```razor
<div class="chat">
    @foreach (var message in messages)
    {
        <ChatMessageDisplay Message="@message" />
    }
</div>
```

Für den vorhergehenden Beispielcode wird die `<ChatMessageDisplay>`-Komponente in einer Datei `ChatMessageDisplay.razor` definiert, die Folgendes enthält:

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

Das vorhergehende Beispiel funktioniert problemlos und mit guter Leistung, solange nicht Tausende von Nachrichten gleichzeitig angezeigt werden. Um Tausende von Nachrichten gleichzeitig anzuzeigen, sollten Sie die separate `ChatMessageDisplay`-Komponente *nicht* ausklammern. Nehmen Sie stattdessen eine direkte Inline-Einfügung des Renderings in das übergeordnete Element vor:

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

Dadurch wird der Renderingmehraufwand pro Komponente für so viele untergeordnete Komponenten vermieden zu dem Preis, sie nicht unabhängig voneinander einzeln erneut rendern zu können.

##### <a name="define-reusable-renderfragments-in-code"></a>Definieren wiederverwendbarer `RenderFragments` im Code

Sie können untergeordnete Komponenten ausschließlich als Möglichkeit zur Wiederverwendung von Renderinglogik ausklammern. Wenn dies der Fall ist, können Sie weiterhin die Renderinglogik wiederverwenden, ohne die tatsächlichen Komponenten zu deklarieren. Im `@code`-Block einer Komponente können Sie ein <xref:Microsoft.AspNetCore.Components.RenderFragment> definieren, das die Benutzeroberfläche ausgibt und von überall aus aufgerufen werden kann:

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

Wie im vorhergehenden Beispiel gezeigt, können Komponenten Markup aus Code in ihrem `@code`-Block und außerhalb davon ausgeben. Diese Vorgehensweise definiert einen <xref:Microsoft.AspNetCore.Components.RenderFragment>-Delegaten, den Sie in der normalen Renderingausgabe der Komponente optional an mehreren Stellen rendern können. Der Delegat muss einen Parameter mit dem Namen `__builder` vom Typ <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> akzeptieren, damit der Razor-Compiler Renderinganweisungen für ihn erzeugen kann.

Wenn dieser für mehrere Komponenten wiederverwendbar sein soll, sollten Sie ihn als `public static`-Member deklarieren:

```razor
public static RenderFragment SayHello = __builder =>
{
    <h1>Hello!</h1>
};
```

Dieser könnte nun von einer nicht verknüpften Komponente aus aufgerufen werden. Dieses Verfahren eignet sich für das Erstellen von Bibliotheken von wiederverwendbaren Markupausschnitten, die ohne Mehraufwand pro Komponente gerendert werden können.

<xref:Microsoft.AspNetCore.Components.RenderFragment>-Delegate können auch Parameter akzeptieren. So erstellen Sie das Äquivalent der `ChatMessageDisplay`-Komponente aus dem vorherigen Beispiel:

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

Dieser Ansatz bietet den Vorteil, dass Renderinglogik ohne Mehraufwand pro Komponente gerendert werden kann. Allerdings hat er weder den Vorteil, dass seine Unterstruktur der Benutzeroberfläche unabhängig aktualisiert werden kann, noch kann das Rendering dieser Unterstruktur der Benutzeroberfläche übersprungen werden, wenn ihr übergeordnetes Element gerendert wird, da es keine Komponentenbegrenzung gibt.

#### <a name="dont-receive-too-many-parameters"></a>Nicht zu viele Parameter empfangen

Wenn eine Komponente extrem häufig wiederholt wird, z. B. Hunderte oder Tausende Mal, sollten Sie bedenken, dass der Mehraufwand für das Übergeben und Empfangen der einzelnen Parameter kumuliert.

Es ist selten, dass zu viele Parameter die Leistung erheblich einschränken, aber es kann ein Faktor sein. Bei einer `<TableCell>`-Komponente, die 1.000 mal innerhalb eines Rasters gerendert wird, könnte jeder zusätzliche übergebene Parameter den gesamten Renderingaufwand um ungefähr 15 ms erhöhen. Wenn jede Zelle 10 Parameter akzeptiert, erfordert die Parameterübergabe ungefähr 150 ms pro Komponentenrendering, somit möglicherweise 150.000 ms (150 Sekunden) und führt von sich aus zu einer langsamen Benutzeroberfläche.

Um diese Auslastung zu reduzieren, könnten Sie mehrere Parameter über benutzerdefinierte Klassen bündeln. Beispielsweise könnte eine `<TableCell>`-Komponente Folgendes akzeptieren:

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

Im vorherigen Beispiel ist `Data` für jede Zelle anders, aber `Options` ist für alle gleich. Natürlich kann es eine Verbesserung sein, keine `<TableCell>`-Komponente zu haben und stattdessen ihre Logik inline in die übergeordnete Komponente einzufügen.

#### <a name="ensure-cascading-parameters-are-fixed"></a>Sicherstellen, dass kaskadierende Parameter korrigiert sind

Die `<CascadingValue>`-Komponente hat einen optionalen Parameter namens `IsFixed`.

 * Wenn der `IsFixed`-Wert `false` ist (Standard), richtet jeder Empfänger des kaskadierenden Werts ein Abonnement ein, um Änderungsbenachrichtigungen zu empfangen. In diesem Fall erfordert jeder `[CascadingParameter]` aufgrund der Abonnementnachverfolgung **erheblich mehr Aufwand** als ein regulärer `[Parameter]`.
 * Wenn der `IsFixed`-Wert `true` ist (z. B. `<CascadingValue Value="@someValue" IsFixed="true">`), dann empfangen Empfänger den Anfangswert, richten aber *kein* Abonnement für den Empfang von Updates ein. In diesem Fall ist jeder `[CascadingParameter]` einfach und **nicht aufwändiger** als ein regulärer `[Parameter]`.

Daher sollten Sie bei kaskadierenden Werten nach Möglichkeit `IsFixed="true"` verwenden. Dies ist möglich, wenn sich der angegebene Wert nicht im Laufe der Zeit ändert. In dem allgemeinen Muster, bei dem eine Komponente `this` als kaskadierenden Wert übergibt, sollten Sie `IsFixed="true"` verwenden:

```razor
<CascadingValue Value="this" IsFixed="true">
    <SomeOtherComponents>
</CascadingValue>
```

Dies ist ein großer Unterschied, wenn eine große Anzahl anderer Komponenten vorhanden ist, die den kaskadierenden Wert empfangen. Weitere Informationen finden Sie unter <xref:blazor/components/cascading-values-and-parameters>.

#### <a name="avoid-attribute-splatting-with-captureunmatchedvalues"></a>Vermeiden des Attribut-Splattings mit `CaptureUnmatchedValues`

Komponenten können mithilfe des <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues>-Flags auswählen, „nicht übereinstimmende“ Parameterwerte zu empfangen:

```razor
<div @attributes="OtherAttributes">...</div>

@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public IDictionary<string, object> OtherAttributes { get; set; }
}
```

Dieser Ansatz ermöglicht, beliebige zusätzliche Attribute an das Element zu übergeben. Er ist aber auch sehr aufwändig, da der Renderer Folgendes ausführen muss:

* Abgleichen aller angegebenen Parameter mit dem Satz bekannter Parameter, um ein Wörterbuch zu erstellen.
* Nachverfolgen, wie viele Kopien desselben Attributs sich gegenseitig überschreiben.

Verwenden Sie <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> für nicht leistungskritische Komponenten, z. B. solche, die nicht häufig wiederholt werden. Versuchen Sie jedoch bei Komponenten, die in großem Umfang gerendert werden, wie z. B. alle Elemente in einer umfangreichen Liste oder Zellen in einem Raster, das Attribut-Splatting zu vermeiden.

Weitere Informationen finden Sie unter <xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters>.

#### <a name="implement-setparametersasync-manually"></a>Manuelles Implementieren von `SetParametersAsync`

Einer der Hauptaspekte des Renderingmehraufwands pro Komponente ist das Schreiben eingehender Parameterwerte in die `[Parameter]`-Eigenschaften. Der Renderer muss hierfür die Reflektion verwenden. Obwohl dies etwas optimiert ist, erzwingt das Fehlen der JIT-Unterstützung für die WebAssembly-Laufzeit Grenzwerte.

In einigen Extremfällen möchten Sie möglicherweise die Reflektion vermeiden und ihre eigene Logik für die Parametereinstellung manuell implementieren. Dies kann unter folgenden Umständen anwendbar sein:

 * Sie verfügen über eine Komponente, die sehr häufig gerendert wird (z. B. gibt es Hunderte oder Tausende Kopien davon in der Benutzeroberfläche).
 * Sie akzeptiert viele Parameter.
 * Sie finden, dass der Mehraufwand für das Empfangen von Parametern die Reaktionsfähigkeit der Benutzeroberfläche merklich beeinträchtigt.

In diesen Fällen können Sie die virtuelle <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A>-Methode der Komponente überschreiben und ihre eigene komponentenspezifische Logik implementieren. Im folgenden Beispiel werden absichtlich Wörterbuch-Suchvorgänge vermieden:

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

Im vorhergehenden Code führt die Rückgabe der Basisklasse <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> die normalen Lebenszyklusmethoden aus, ohne dass Parameter erneut zugewiesen werden.

Wie Sie im vorhergehenden Code sehen können, ist das Überschreiben von <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> und das Bereitstellen benutzerdefinierter Logik kompliziert und aufwändig, daher wird dieser Ansatz im Allgemeinen nicht empfohlen. In Extremfällen kann die Renderingleistung um 20-25 % verbessert werden, aber Sie sollten diesen Ansatz nur in den zuvor aufgeführten Szenarien berücksichtigen.

### <a name="dont-trigger-events-too-rapidly"></a>Ereignisse nicht zu schnell auslösen

Einige Browserereignisse wie `onmousemove` und `onscroll` werden sehr häufig ausgelöst, Dutzende oder Hunderte Mal pro Sekunde. In den meisten Fällen muss die Aktualisierung der Benutzeroberfläche nicht so häufig ausgeführt werden. Wenn Sie dies versuchen, können Sie die Reaktionsfähigkeit der Benutzeroberfläche beeinträchtigen oder übermäßig viel CPU-Zeit verbrauchen.

Anstatt native `@onmousemove`- oder `@onscroll`-Ereignisse zu verwenden, sollten Sie vorzugsweise JS-Interop verwenden, um einen Rückruf zu registrieren, der seltener ausgelöst wird. Beispielsweise zeigt die folgende Komponente (`MyComponent.razor`) die Position der Maus an, aktualisiert jedoch nur höchstens einmal alle 500 ms:

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

Der entsprechende JavaScript-Code, der auf der Seite `index.html` platziert oder als ES6-Modul geladen werden kann, registriert den eigentlichen DOM-Ereignislistener. In diesem Beispiel verwendet der Ereignislistener die [`throttle`-Funktion von Lodash](https://lodash.com/docs/4.17.15#throttle), um die Rate der Aufrufe einzuschränken:

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

Diese Technik kann für Blazor Server sogar noch wichtiger sein, weil jeder Ereignisaufruf mit dem Senden einer Nachricht über das Netzwerk verbunden ist. Sie ist für Blazor WebAssembly nützlich, da sie den Renderingaufwand erheblich reduzieren kann.

## <a name="optimize-javascript-interop-speed"></a>Optimieren der JavaScript-Interop-Geschwindigkeit

Aufrufe zwischen .NET und JavaScript sind aus folgenden Gründen mit zusätzlichem Mehraufwand verbunden:

 * Standardmäßig sind Aufrufe asynchron.
 * Standardmäßig werden Parameter und Rückgabewerte JSON-serialisiert. Dies soll einen leicht verständlichen Konvertierungsmechanismus zwischen .NET- und JavaScript-Typen bieten.

Zusätzlich werden diese Aufrufe auf Blazor Server über das Netzwerk übergeben.

### <a name="avoid-excessively-fine-grained-calls"></a>Vermeiden übermäßig differenzierter Aufrufe

Da jeder Aufruf einen gewissen Mehraufwand erfordert, kann es hilfreich sein, die Anzahl der Aufrufe zu verringern. Beachten Sie den folgenden Code, der eine Sammlung von Elementen im `localStorage`-Speicher des Browsers speichert:

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

Im vorhergehenden Beispiel wird für jedes Element ein separater JS-Interop-Aufruf durchgeführt. Stattdessen reduziert der folgende Ansatz das JS-Interop auf einen einzelnen Aufruf:

```csharp
private async Task StoreAllInLocalStorage(IEnumerable<TodoItem> items)
{
    await JS.InvokeVoidAsync("storeAllInLocalStorage", items);
}
```

Die entsprechende JavaScript-Funktion wird wie folgt definiert:

```javascript
function storeAllInLocalStorage(items) {
  items.forEach(item => {
    localStorage.setItem(item.id, JSON.stringify(item));
  });
}
```

Für Blazor WebAssembly ist dies in der Regel nur wichtig, wenn Sie eine große Anzahl von JS-Interop-Aufrufen durchführen.

### <a name="consider-making-synchronous-calls"></a>Erwägen synchroner Aufrufe

JavaScript-Interop-Aufrufe sind standardmäßig asynchron, unabhängig davon, ob der aufgerufene Code synchron oder asynchron ist. Dadurch wird sichergestellt, dass die Komponenten mit Blazor WebAssembly und Blazor Server kompatibel sind. Auf Blazor Server müssen alle JavaScript-Interop-Aufrufe asynchron sein, da sie über eine Netzwerkverbindung gesendet werden.

Wenn Sie sicher sind, dass Ihre App nur auf Blazor WebAssembly ausgeführt wird, können Sie synchrone JavaScript-Interop-Aufrufe durchführen. Dies ist mit etwas weniger Mehraufwand verbunden als asynchrone Aufrufe und kann zu weniger Renderingzyklen führen, da es keinen Zwischenzustand gibt, während auf die Ergebnisse gewartet wird.

Um einen synchronen Aufruf von .NET an JavaScript durchführen zu können, wandeln Sie <xref:Microsoft.JSInterop.IJSRuntime> in <xref:Microsoft.JSInterop.IJSInProcessRuntime> um:

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

Wenn Sie mit `IJSObjectReference` arbeiten, können Sie einen synchronen Aufruf durch Umwandeln in `IJSInProcessObjectReference` durchführen.

::: moniker-end

Um einen synchronen Aufruf von JavaScript an .NET durchzuführen, verwenden Sie `DotNet.invokeMethod` anstelle von `DotNet.invokeMethodAsync`.

Synchrone Aufrufe funktionieren unter folgenden Voraussetzungen:

* Die App wird auf Blazor WebAssembly, nicht Blazor Server ausgeführt.
* Die aufgerufene Funktion gibt einen Wert synchron zurück (es handelt sich nicht um eine `async`-Methode, und es wird weder ein .NET-<xref:System.Threading.Tasks.Task> noch ein JavaScript-`Promise`) zurückgegeben.

Weitere Informationen finden Sie unter <xref:blazor/call-javascript-from-dotnet>.

::: moniker range=">= aspnetcore-5.0"
 
### <a name="consider-making-unmarshalled-calls"></a>Erwägen der Durchführung unmarshallter Aufrufe

Bei der Ausführung auf Blazor WebAssembly können unmarshallte Aufrufe von .NET an JavaScript durchgeführt werden. Dabei handelt es sich um synchrone Aufrufe, die keine JSON-Serialisierung von Argumenten oder Rückgabewerten ausführen. Alle Aspekte der Speicherverwaltung und Übersetzungen zwischen .NET- und JavaScript-Darstellungen werden dem Entwickler überlassen.

> [!WARNING]
> Während die Verwendung von `IJSUnmarshalledRuntime` den geringsten Aufwand der JS-Interop-Ansätze hat, gibt es für die JavaScript-APIs, die erforderlich sind, um mit diesen APIs zu interagieren, aktuell keine Dokumentation. Außerdem werden in diesem Bereich im Rahmen zukünftiger Releases Breaking Changes vorgenommen.

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

## <a name="minimize-app-download-size"></a>Minimieren der Größe des Anwendungsdownloads

::: moniker range=">= aspnetcore-5.0"

### <a name="intermediate-language-il-trimming"></a>IL-Kürzung (Intermediate Language, Zwischensprache)

[Das Kürzen nicht verwendeter Assemblys über eine Blazor WebAssembly-App](xref:blazor/host-and-deploy/configure-trimmer) reduziert die Größe der App, indem nicht genutzter Code in den Binärdateien der App entfernt wird. Der Trimmer wird standardmäßig beim Veröffentlichen einer Anwendung ausgeführt. Veröffentlichen Sie die App für die Bereitstellung mithilfe des Befehls [`dotnet publish`](/dotnet/core/tools/dotnet-publish) mit auf `Release` festgelegter Option [-c|--configuration](/dotnet/core/tools/dotnet-publish#options), um von der Kürzung zu profitieren:

::: moniker-end

::: moniker range="< aspnetcore-5.0"

### <a name="intermediate-language-il-linking"></a>Intermediate Language-Verknüpfung (IL)

[Das Konfigurieren eines Linkers für eine Blazor WebAssembly-App](xref:blazor/host-and-deploy/configure-linker) reduziert die Größe der App, indem nicht genutzter Code in den Binärdateien der App gekürzt wird. Standardmäßig ist der IL-Linker (Intermediate Language, Zwischensprache) nur aktiviert, wenn der Erstellvorgang in der `Release`-Konfiguration ausgeführt wird. Veröffentlichen Sie die App für die Bereitstellung mithilfe des Befehls [`dotnet publish`](/dotnet/core/tools/dotnet-publish) mit auf `Release` festgelegter Option [-c|--configuration](/dotnet/core/tools/dotnet-publish#options), um diesen Vorteil zu nutzen:

::: moniker-end

```dotnetcli
dotnet publish -c Release
```

### <a name="use-systemtextjson"></a>Verwenden von System.Text.Json

Die JS-Interop-Implementierung von Blazor basiert auf <xref:System.Text.Json>. Dabei handelt es sich um eine JSON-Hochleistungsserialisierungsbibliothek mit niedriger Arbeitsspeicherzuteilung. Das Verwenden von <xref:System.Text.Json> führt nicht zu einer zusätzlichen Nutzlastgröße für die App, wenn eine oder mehrere alternative JSON-Bibliotheken hinzufügt werden.

Eine Anleitung zur Migration finden Sie unter [Migration von `Newtonsoft.Json` zu `System.Text.Json`](/dotnet/standard/serialization/system-text-json-migrate-from-newtonsoft-how-to).

### <a name="lazy-load-assemblies"></a>Lazy Loading-Assemblys

Laden Sie Assemblys zur Laufzeit, wenn die Assemblys von einer Route benötigt werden. Weitere Informationen finden Sie unter <xref:blazor/webassembly-lazy-load-assemblies>.

### <a name="compression"></a>Komprimierung

Wenn eine Blazor WebAssembly-App veröffentlicht wird, wird die Ausgabe bei der Veröffentlichung statisch komprimiert, um die App-Größe zu verringern und den Aufwand für eine Laufzeitkomprimierung zu beseitigen. Blazor basiert darauf, dass der Server eine Inhaltsaushandlung ausführt und statisch komprimierte Dateien bereitstellt.

Nach der Bereitstellung einer App überprüfen Sie, ob die App komprimierte Dateien bereitstellt. Sehen Sie sich die Netzwerkregisterkarte der Entwicklertools eines Browsers an, und überprüfen Sie, ob die Dateien mit `Content-Encoding: br` oder `Content-Encoding: gz` bereitgestellt werden. Wenn der Host keine komprimierten Dateien bereitstellt, befolgen Sie die Anweisungen in <xref:blazor/host-and-deploy/webassembly#compression>.

### <a name="disable-unused-features"></a>Deaktivieren nicht genutzter Features

Die Blazor WebAssembly-Runtime schließt die folgenden .NET-Features ein, die deaktiviert werden können, wenn die App sie aufgrund einer kleineren Nutzlastgröße nicht benötigt:

* Eine Datendatei wird eingeschlossen, damit die Zeitzoneninformationen korrekt sind. Wenn die App dieses Feature nicht benötigt, ziehen Sie in Betracht, es zu deaktivieren, indem Sie die `BlazorEnableTimeZoneSupport`-MSBuild-Eigenschaft in der Projektdatei der App auf `false` festlegen:

  ```xml
  <PropertyGroup>
    <BlazorEnableTimeZoneSupport>false</BlazorEnableTimeZoneSupport>
  </PropertyGroup>
  ```

::: moniker range=">= aspnetcore-5.0"

* Blazor WebAssembly enthält standardmäßig Globalisierungsressourcen, die zum Anzeigen von Werten wie Datums- und Währungsangaben in der Kultur des Benutzers erforderlich sind. Wenn für die App keine Lokalisierung erforderlich ist, können Sie [die App so konfigurieren, dass die invariante Kultur unterstützt wird](xref:blazor/globalization-localization), die auf der `en-US`-Kultur basiert:

  ```xml
  <PropertyGroup>
    <InvariantGlobalization>true</InvariantGlobalization>
  </PropertyGroup>
  ```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* Die Sortierungsinformationen werden eingeschlossen, damit APIs wie <xref:System.StringComparison.InvariantCultureIgnoreCase?displayProperty=nameWithType> ordnungsgemäß funktionieren. Wenn Sie sicher sind, dass die App die Sortierungsinformationen nicht benötigt, ziehen Sie in Betracht, sie zu deaktivieren, indem Sie die `BlazorWebAssemblyPreserveCollationData`-MSBuild-Eigenschaft in der Projektdatei der App auf `false` festlegen:

  ```xml
  <PropertyGroup>
    <BlazorWebAssemblyPreserveCollationData>false</BlazorWebAssemblyPreserveCollationData>
  </PropertyGroup>
  ```

::: moniker-end
