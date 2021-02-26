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
ms.openlocfilehash: 03a49c827a1f70e6b721adf293857bb33475ed36
ms.sourcegitcommit: 04ad9cd26fcaa8bd11e261d3661f375f5f343cdc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/10/2021
ms.locfileid: "100107076"
---
# <a name="aspnet-core-blazor-lifecycle"></a>ASP.NET Core Blazor-Lebenszyklus

Das Blazor-Framework umfasst synchrone und asynchrone Lebenszyklusmethoden. Setzen Sie Lebenszyklusmethoden außer Kraft, um während der Komponenteninitialisierung und des Renderings zusätzliche Vorgänge mit Komponenten durchzuführen.

Auf den folgenden Abbildungen wird der Blazor-Lebenszyklus veranschaulicht. In den folgenden Abschnitten dieses Artikels werden die Lebenszyklusmethoden mit Beispielen definiert.

Ereignisse des Komponentenlebenszyklus:

1. Wenn die Komponente erstmalig ein Rendering für eine Anforderung ausführt, führen Sie folgende Schritte durch:
   * Erstellen Sie die Instanz der Komponente.
   * Führen Sie eine Eigenschaftsinjektion durch. Führen Sie [`SetParametersAsync`](#before-parameters-are-set) aus.
   * Rufen Sie [`OnInitialized{Async}`](#component-initialization-methods) auf. Wenn <xref:System.Threading.Tasks.Task> zurückgegeben wird, wird auf <xref:System.Threading.Tasks.Task> gewartet, und die Komponente wird gerendert. Wenn <xref:System.Threading.Tasks.Task> nicht zurückgegeben wird, wird die Komponente gerendert.
1. Rufen Sie [`OnParametersSet{Async}`](#after-parameters-are-set) auf, und rendern Sie die Komponente. Wenn <xref:System.Threading.Tasks.Task> aus `OnParametersSetAsync` zurückgegeben wird, wird auf <xref:System.Threading.Tasks.Task> gewartet. Anschließend wird die Komponente erneut gerendert.

![Lebenszyklusereignisse einer Razor-Komponente in Blazor](lifecycle/_static/lifecycle1.png)

Verarbeitung von DOM-Ereignissen (Document Object Model):

1. Der Ereignishandler wird ausgeführt.
1. Wenn <xref:System.Threading.Tasks.Task> zurückgegeben wird, wird auf <xref:System.Threading.Tasks.Task> gewartet, und dann wird die Komponente gerendert. Wenn <xref:System.Threading.Tasks.Task> nicht zurückgegeben wird, wird die Komponente gerendert.

![Verarbeitung von DOM-Ereignissen (Document Object Model)](lifecycle/_static/lifecycle2.png)

Der `Render`-Lebenszyklus:

1. Vermeiden Sie weitere Renderingvorgänge für die Komponente in den folgenden Fällen:
   * Nach dem ersten Rendering
   * Wenn [`ShouldRender`](#suppress-ui-refreshing) den Wert `false` aufweist
1. Erstellen Sie das Diff (Unterschied) der Renderstruktur, und rendern Sie die Komponente.
1. Warten Sie, bis das DOM aktualisiert wurde.
1. Rufen Sie [`OnAfterRender{Async}`](#after-component-render) auf.

![Lebenszyklus von Rendervorgängen](lifecycle/_static/lifecycle3.png)

Wenn [`StateHasChanged`](#state-changes) von Entwicklern aufgerufen wird, führt dies zu einem Rendervorgang. Weitere Informationen finden Sie unter <xref:blazor/components/rendering>.

## <a name="lifecycle-methods"></a>Lebenszyklusmethoden

### <a name="before-parameters-are-set"></a>Bevor die Parameter festgelegt werden

<xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> legt Parameter fest, die vom übergeordneten Element der Komponente in der Renderstruktur oder aus Routingparametern bereitgestellt werden. Durch Überschreiben dieser Methode kann Entwicklercode direkt mit den Parametern von <xref:Microsoft.AspNetCore.Components.ParameterView> interagieren.

Im folgenden Beispiel weist <xref:Microsoft.AspNetCore.Components.ParameterView.TryGetValue%2A?displayProperty=nameWithType> den Wert des `Param`-Parameters `value` zu, wenn die Analyse eines Routingparameters für `Param` erfolgreich ist. Wenn `value` nicht `null` lautet, wird der Wert von der Komponente angezeigt.

Obwohl bei der [Übereinstimmung von Routenparametern die Groß-/Kleinschreibung nicht beachtet wird](xref:blazor/fundamentals/routing#route-parameters), stimmt <xref:Microsoft.AspNetCore.Components.ParameterView.TryGetValue%2A> nur mit Parameternamen in der Routenvorlage überein, die die Groß-/Kleinschreibung beachten. Das folgende Beispiel ist erforderlich, um `/{Param?}` und nicht `/{param?}` zu verwenden, um den Wert zu erhalten. Wenn `/{param?}` in diesem Szenario verwendet wird, gibt <xref:Microsoft.AspNetCore.Components.ParameterView.TryGetValue%2A> `false` zurück und `message` ist auf keine der Zeichenfolgen festgelegt.

`Pages/SetParametersAsyncExample.razor`:

```razor
@page "/setparametersasync-example/{Param?}"

<h1>SetParametersAsync Example</h1>

<p>@message</p>

@code {
    private string message;

    [Parameter]
    public string Param { get; set; }

    public override async Task SetParametersAsync(ParameterView parameters)
    {
        if (parameters.TryGetValue<string>(nameof(Param), out var value))
        {
            if (value is null)
            {
                message = "The value of 'Param' is null.";
            }
            else
            {
                message = $"The value of 'Param' is {value}.";
            }
        }

        await base.SetParametersAsync(parameters);
    }
}
```

<xref:Microsoft.AspNetCore.Components.ParameterView> enthält die Parameterwerte für die Komponente für jeden Aufruf von <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A>.

Die Standardimplementierung von <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> legt den Wert der einzelnen Eigenschaften mit dem [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute)- oder [`[CascadingParameter]`-Attribut](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) fest, die einen entsprechenden Wert in <xref:Microsoft.AspNetCore.Components.ParameterView> aufweist. Parameter, die keinen entsprechenden Wert in <xref:Microsoft.AspNetCore.Components.ParameterView> haben, bleiben unverändert.

Wenn [`base.SetParametersAsync`](xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A) nicht aufgerufen wird, kann der benutzerdefinierte Code den eingehenden Parameterwert in jeder gewünschten Weise interpretieren. Beispielsweise ist es nicht erforderlich, die eingehenden Parameter den Eigenschaften der Klasse zuzuordnen.

Wenn Ereignishandler eingerichtet wurden, sollten Sie ihre Einbindung bei der Beseitigung aufheben. Weitere Informationen finden Sie im Abschnitt [Beseitigung von Komponenten mit `IDisposable`](#component-disposal-with-idisposable).

### <a name="component-initialization-methods"></a>Methoden zur Komponenteninitialisierung

<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> und <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> werden aufgerufen, wenn die Komponente initialisiert wird, nachdem sie ihre anfänglichen Parameter von ihrer übergeordneten Komponente in <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> erhalten hat. 

Verwenden Sie <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>, wenn die Komponente einen asynchronen Vorgang ausführt und nach Abschluss des Vorgangs aktualisiert werden soll.

Für einen synchronen Betrieb setzen Sie <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> außer Kraft:

```csharp
protected override void OnInitialized()
{
    ...
}
```

Überschreiben Sie <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>, und verwenden Sie den Operator [`await`](/dotnet/csharp/language-reference/operators/await) für den Vorgang, um einen asynchronen Vorgang durchzuführen:

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

Blazor Server-Apps, die [ihren Inhalt vorab rendern](xref:blazor/fundamentals/signalr#render-mode) rufen <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> *zweimal* auf:

* Einmal, wenn die Komponente anfänglich statisch als Teil der Seite gerendert wird.
* Ein zweites Mal, wenn der Browser eine Verbindung mit dem Server herstellt.

Informationen zum Verhindern, dass Entwicklercode in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> zweimal ausgeführt wird, finden Sie im Abschnitt [Zustandsbehaftete erneute Verbindung nach dem Prerendering](#stateful-reconnection-after-prerendering).

Während für eine Blazor Server-App ein Prerendering durchgeführt wird, sind bestimmte Aktionen (z. B. Aufrufe in JavaScript) nicht möglich, da noch keine Verbindung mit dem Browser hergestellt wurde. Komponenten müssen wahrscheinlich unterschiedlich rendern, wenn dafür ein Prerendering durchgeführt wurde. Weitere Informationen finden Sie im Abschnitt [Ermitteln des Prerenderings einer App](#detect-when-the-app-is-prerendering).

Wenn Ereignishandler eingerichtet wurden, sollten Sie ihre Einbindung bei der Beseitigung aufheben. Weitere Informationen finden Sie im Abschnitt [Beseitigung von Komponenten mit `IDisposable`](#component-disposal-with-idisposable).

### <a name="after-parameters-are-set"></a>Nachdem die Parameter festgelegt wurden

<xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> oder <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A> wird aufgerufen:

* Nachdem die Komponente in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> oder <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> initialisiert wurde.
* Wenn die übergeordnete Komponente neu gerendert wird und Folgendes bereitstellt:
  * Nur bekannte einfache, unveränderliche Typen, von denen sich mindestens ein Parameter geändert hat.
  * Beliebige Parameter mit komplexem Typ. Das Framework kann nicht wissen, ob die Werte eines Parameters mit komplexem Typ intern mutiert sind, also behandelt es den Parametersatz als geändert.

```csharp
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

> [!NOTE]
> Die asynchrone Arbeit bei der Anwendung von Parametern und Eigenschaftswerten muss während des <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A>-Lebenszyklusereignisses erfolgen.

```csharp
protected override void OnParametersSet()
{
    ...
}
```

Wenn Ereignishandler eingerichtet wurden, sollten Sie ihre Einbindung bei der Beseitigung aufheben. Weitere Informationen finden Sie im Abschnitt [Beseitigung von Komponenten mit `IDisposable`](#component-disposal-with-idisposable).

### <a name="after-component-render"></a>Nach dem Rendern der Komponente

<xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> und <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> werden aufgerufen, nachdem eine Komponente das Rendering beendet hat. Element- und Komponentenverweise werden an dieser Stelle aufgefüllt. In dieser Phase können Sie zusätzliche Initialisierungsschritte unter Verwendung des gerenderten Inhalts durchführen, z. B. die Aktivierung von JavaScript-Bibliotheken von Drittanbietern, die mit den gerenderten DOM-Elementen arbeiten.

Der Parameter `firstRender` für <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> und <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A>:

* Wird auf `true` festgelegt, wenn die Komponenteninstanz zum ersten Mal gerendert wird.
* Kann verwendet werden, um sicherzustellen, dass die Initialisierung nur einmal durchgeführt wird.

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
> Die asynchrone Arbeit unmittelbar nach dem Rendering muss während des <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>-Lebenszyklusereignisses erfolgen.
>
> Selbst wenn Sie ein <xref:System.Threading.Tasks.Task> von <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> zurückgeben, plant das Framework keinen weiteren Renderzyklus für Ihre Komponente ein, sobald diese Aufgabe abgeschlossen ist. Damit soll eine unendliche Renderschleife vermieden werden. Dies unterscheidet sich von den anderen Lebenszyklusmethoden, die einen weiteren Renderzyklus planen, sobald die zurückgegebene Aufgabe abgeschlossen ist.

```csharp
protected override void OnAfterRender(bool firstRender)
{
    if (firstRender)
    {
        ...
    }
}
```

<xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> und <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> *werden beim Vorabrendern auf dem Server nicht aufgerufen*. Die Methoden werden aufgerufen, wenn die Komponente interaktiv gerendert wird, nachdem das Vorabrendern abgeschlossen wurde. Beim Vorabrendern der App:

1. Die Komponente wird auf dem Server ausgeführt, um in der HTTP-Antwort statisches HTML-Markup zu generieren. Während dieser Phase werden <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> und <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> nicht aufgerufen.
1. Wenn `blazor.server.js` oder `blazor.webassembly.js` im Browser gestartet werden, wird die Komponente in einem interaktiven Renderingmodus neu gestartet. Nachdem eine Komponente neu gestartet wurde, **werden** <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> und <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> aufgerufen, da sich die App nicht mehr in der Phase des Vorabrenderns befindet.

Wenn Ereignishandler eingerichtet wurden, sollten Sie ihre Einbindung bei der Beseitigung aufheben. Weitere Informationen finden Sie im Abschnitt [Beseitigung von Komponenten mit `IDisposable`](#component-disposal-with-idisposable).

### <a name="suppress-ui-refreshing"></a>Unterdrücken der UI-Aktualisierung

Setzen Sie <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> außer Kraft, um die Aktualisierung der Benutzeroberfläche zu unterdrücken. Wenn die Implementierung `true` zurückgibt, wird die Benutzeroberfläche aktualisiert:

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

<xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> wird jedes Mal aufgerufen, wenn die Komponente gerendert wird.

Selbst wenn <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> außer Kraft gesetzt wird, wird die Komponente immer anfänglich gerendert.

Weitere Informationen finden Sie unter <xref:blazor/webassembly-performance-best-practices#avoid-unnecessary-rendering-of-component-subtrees>.

## <a name="state-changes"></a>Statusänderungen

<xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> benachrichtigt die Komponente, dass sich ihr Zustand geändert hat. Gegebenenfalls bewirkt der Aufruf von <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> das erneute Rendern der Komponente.

<xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> wird für <xref:Microsoft.AspNetCore.Components.EventCallback>-Methoden automatisch aufgerufen. Weitere Informationen finden Sie unter <xref:blazor/components/event-handling#eventcallback>.

Weitere Informationen finden Sie unter <xref:blazor/components/rendering>.

## <a name="handle-incomplete-async-actions-at-render"></a>Behandeln unvollständiger asynchroner Aktionen beim Rendern

Asynchrone Aktionen, die in Lebenszyklusereignissen ausgeführt werden, sind möglicherweise nicht abgeschlossen, bevor die Komponente gerendert wird. Objekte können während der Ausführung der Lebenszyklusmethode `null` oder unvollständig mit Daten gefüllt sein. Stellen Sie eine Renderinglogik bereit, um zu bestätigen, dass die Objekte initialisiert sind. Rendern Sie UI-Elemente für Platzhalter (z. B. eine Nachricht zum Ladevorgang), während Objekte `null` sind.

In der `FetchData`-Komponente der Blazor-Vorlagen wird <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> außer Kraft gesetzt, um Vorhersagedaten asynchron abzurufen (`forecasts`). Wenn `forecasts` gleich `null` ist, wird dem Benutzer eine Nachricht zum Ladevorgang angezeigt. Nachdem die von <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> zurückgegebene `Task` abgeschlossen ist, wird die Komponente mit dem aktualisierten Zustand neu gerendert.

`Pages/FetchData.razor` in der Blazor Server-Vorlage:

[!code-razor[](lifecycle/samples_snapshot/FetchData.razor?highlight=9,21,25)]

## <a name="handle-errors"></a>Behandeln von Fehlern

Informationen zur Behandlung von Fehlern während der Ausführung von Lebenszyklusmethoden finden Sie unter <xref:blazor/fundamentals/handle-errors#lifecycle-methods>.

## <a name="stateful-reconnection-after-prerendering"></a>Zustandsbehaftete erneute Verbindung nach dem Prerendering

Wenn in einer Blazor Server-App <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> gleich <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> ist, wird die Komponente zunächst statisch als Teil der Seite gerendert. Sobald der Browser eine Verbindung mit dem Server herstellt, wird die Komponente *wieder* gerendert, und die Komponente ist nun interaktiv. Wenn die [`OnInitialized{Async}`](#component-initialization-methods)-Lebenszyklusmethode zur Initialisierung der Komponente vorhanden ist, wird die Methode *zweimal* ausgeführt:

* Wenn die Komponente statisch vorab gerendert ist.
* Nachdem die Serververbindung hergestellt wurde.

Dies kann zu einer spürbaren Änderung der auf der Benutzeroberfläche angezeigten Daten führen, wenn die Komponente schließlich gerendert wird.

So vermeiden Sie das Szenario des doppelten Renderings in einer Blazor Server-App:

* Übergeben Sie einen Bezeichner, der zum Zwischenspeichern des Status während des Prerenderings und zum Abrufen des Zustands nach dem Neustart der App verwendet werden kann.
* Verwenden Sie den Bezeichner während des Prerenderings, um den Zustand der Komponenten zu speichern.
* Verwenden Sie den Bezeichner nach dem Prerendering, um den zwischengespeicherten Zustand abzurufen.

Der folgende Code veranschaulicht eine aktualisierte `WeatherForecastService` in einer vorlagenbasierten Blazor Server-App, die das doppelte Rendering vermeidet:

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

Weitere Informationen zum <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> finden Sie unter <xref:blazor/fundamentals/signalr#render-mode>.

## <a name="detect-when-the-app-is-prerendering"></a>Erkennen, wenn für die App ein Prerendering durchgeführt wird

[!INCLUDE[](~/blazor/includes/prerendering.md)]

## <a name="component-disposal-with-idisposable"></a>Entfernen von Komponenten mit `IDisposable`

Wenn eine Komponente <xref:System.IDisposable> implementiert, ruft das Framework die [Dispose-Methode](/dotnet/standard/garbage-collection/implementing-dispose) auf, wenn die Komponente aus der Benutzeroberfläche entfernt wird. Dabei können nicht verwaltete Ressourcen freigegeben werden. Das Entfernen kann jederzeit erfolgen, auch während der [Initialisierung von Komponenten](#component-initialization-methods). Die folgende Komponente implementiert <xref:System.IDisposable> mit der Razor-Anweisung [`@implements`](xref:mvc/views/razor#implements):

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

Wenn eine Beseitigung für ein Objekt erforderlich ist, kann ein Lambdaausdruck verwendet werden, um das Objekt zu verwerfen, wenn <xref:System.IDisposable.Dispose%2A?displayProperty=nameWithType> aufgerufen wird:

`Pages/CounterWithTimerDisposal.razor`:

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

Das vorherige Beispiel wird in <xref:blazor/components/rendering#receiving-a-call-from-something-external-to-the-blazor-rendering-and-event-handling-system> angezeigt.

Verwenden Sie bei asynchronen Aufgaben zur Beseitigung `DisposeAsync` anstelle von <xref:System.IDisposable.Dispose>.

```csharp
public async ValueTask DisposeAsync()
{
    ...
}
```

> [!NOTE]
> Der Aufruf von <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> in `Dispose` wird nicht unterstützt. <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> könnte im Rahmen des Beendens des Renderers aufgerufen werden, sodass die Anforderung von UI-Updates an diesem Punkt nicht unterstützt wird.

Kündigen Sie die .NET-Ereignisabonnements der Ereignishandler. Die folgenden [Blazor-Formularbeispiele](xref:blazor/forms-validation) veranschaulichen die Abbestellung eines Ereignishandlers in der `Dispose`-Methode:

* Ansatz mit einem privatem Feld und Lambdaausdruck

  [!code-razor[](lifecycle/samples_snapshot/event-handler-disposal-1.razor?highlight=23,28)]

* Ansatz mit einer privaten Methode

  [!code-razor[](lifecycle/samples_snapshot/event-handler-disposal-2.razor?highlight=16,26)]

Wenn [anonyme Funktionen](/dotnet/csharp/programming-guide/statements-expressions-operators/anonymous-functions), Methoden oder Ausdrücke verwendet werden, ist es nicht erforderlich, <xref:System.IDisposable> zu implementieren und Delegaten abzubestellen. Es kann jedoch problematisch sein, einen Delegaten abzubestellen, **wenn das Objekt, das das Ereignis offenlegt, die Lebensdauer der Komponente überschreitet, die den Delegaten registriert**. Wenn dies der Fall ist, führt dies zu einem Arbeitsspeicherverlust, da der registrierte Delegat das ursprüngliche Objekt aufrecht erhält. Verwenden Sie daher nur die folgenden Ansätze, wenn Sie wissen, dass der Ereignisdelegat schnell beseitigt wird. Wenn Sie sich bezüglich der Lebensdauer von Objekten, die eine Bereinigung erfordern, nicht sicher sind, abonnieren Sie eine Delegatmethode, und verwerfen Sie den Delegaten wie in den vorherigen Beispielen ordnungsgemäß.

* Anonymer Lambdamethodenansatz (explizite Bereinigung nicht erforderlich)

  ```csharp
  private void HandleFieldChanged(object sender, FieldChangedEventArgs e)
  {
      formInvalid = !editContext.Validate();
      StateHasChanged();
  }

  protected override void OnInitialized()
  {
      editContext = new EditContext(starship);
      editContext.OnFieldChanged += (s, e) => HandleFieldChanged((editContext)s, e);
  }
  ```

* Anonymer Lambdaausdrucksansatz (explizite Bereinigung nicht erforderlich)

  ```csharp
  private ValidationMessageStore messageStore;

  [CascadingParameter]
  private EditContext CurrentEditContext { get; set; }

  protected override void OnInitialized()
  {
      ...

      messageStore = new ValidationMessageStore(CurrentEditContext);

      CurrentEditContext.OnValidationRequested += (s, e) => messageStore.Clear();
      CurrentEditContext.OnFieldChanged += (s, e) => 
          messageStore.Clear(e.FieldIdentifier);
  }
  ```

  Das vollständige Beispiel mit dem oben stehenden Code mit anonymen Lambdaausdrücken erscheint in <xref:blazor/forms-validation#validator-components>.

Weitere Informationen finden Sie unter [Bereinigen von nicht verwalteten Ressourcen](/dotnet/standard/garbage-collection/unmanaged) und den Themen zum Implementieren der Methoden `Dispose` und `DisposeAsync`.

## <a name="cancelable-background-work"></a>Abbrechbare Hintergrundarbeit

Komponenten führen häufig Hintergrundaufgaben aus, die lange dauern, zum Beispiel die Durchführung von Netzwerkaufrufen (<xref:System.Net.Http.HttpClient>) und die Interaktion mit Datenbanken. Es ist wünschenswert, die Hintergrundarbeit zu unterbinden, um Systemressourcen in mehreren Situationen zu sparen. Beispielsweise werden asynchrone Hintergrundvorgänge nicht automatisch beendet, wenn ein Benutzer von einer Komponente wegnavigiert.

Andere Gründe, warum Arbeitselemente, die im Hintergrund ausgeführt werden, unterbrochen werden müssen, sind die folgenden:

* Eine ausgeführte Hintergrundaufgabe wurde mit fehlerhaften Eingabedaten oder Verarbeitungsparametern gestartet.
* Die aktuellen Arbeitselemente, die im Hintergrund ausgeführt werden, müssen durch neue Arbeitselemente ersetzt werden.
* Die Priorität der aktuell ausgeführten Aufgaben muss geändert werden.
* Die App muss herunterfahren werden, um sie erneut auf dem Server bereitzustellen.
* Serverressourcen werden eingeschränkt und erfordern die Neuplanung von Arbeitselementen, die im Hintergrund ausgeführt werden.

So implementieren Sie ein abbrechbares Hintergrundarbeitsmuster in einer Komponente:

* Verwenden Sie eine <xref:System.Threading.CancellationTokenSource>-Klasse und eine <xref:System.Threading.CancellationToken>-Struktur.
* Es ist gewünscht, dass der Abbruch bei der [Löschung der Komponente](#component-disposal-with-idisposable) und zu jedem Zeitpunkt durch manuellen Abbruch des Tokens durchgeführt wird. Rufen Sie dazu [`CancellationTokenSource.Cancel`](xref:System.Threading.CancellationTokenSource.Cancel%2A) auf, um zu signalisieren, dass die Hintergrundarbeit abgebrochen werden soll.
* Rufen Sie nach Rückgabe des asynchronen Aufrufs <xref:System.Threading.CancellationToken.ThrowIfCancellationRequested%2A> für das Token auf.

Im folgenden Beispiel:

* `await Task.Delay(5000, cts.Token);` stellt asynchrone Hintergrundaufgaben mit langer Ausführungszeit dar.
* `BackgroundResourceMethod` stellt eine Hintergrundmethode mit langer Ausführungszeit dar, die nicht gestartet werden sollte, wenn die `Resource` vor dem Aufruf der Methode verworfen wird.

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

## <a name="blazor-server-reconnection-events"></a>Blazor Server-Ereignisse zur Wiederherstellung von Verbindungen

Die in diesem Artikel behandelten Ereignisse zum Komponentenlebenszyklus werden getrennt von den [Blazor Server-Ereignishandlern zur Wiederherstellung von Verbindungen](xref:blazor/fundamentals/signalr#reflect-the-connection-state-in-the-ui) ausgeführt. Wenn bei einer Blazor Server-App die SignalR-Verbindung mit dem Client getrennt wird, werden nur Updates der Benutzeroberfläche unterbrochen. Updates der Benutzeroberfläche werden fortgesetzt, sobald die Verbindung wiederhergestellt wurde. Weitere Informationen zu Verbindungshandlerereignissen und zur Konfiguration finden Sie unter <xref:blazor/fundamentals/signalr>.
