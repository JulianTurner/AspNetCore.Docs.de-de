---
title: Aufrufen von JavaScript-Funktionen über .NET-Methoden in ASP.NET Core Blazor
author: guardrex
description: In diesem Artikel erfahren Sie, wie Sie JavaScript-Funktionen über .NET-Methoden in Blazor-Apps aufrufen.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc, devx-track-js
ms.date: 11/25/2020
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
uid: blazor/call-javascript-from-dotnet
ms.openlocfilehash: 2502f43f4eaf245996827f704462ec340bbb8e07
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/16/2021
ms.locfileid: "98252538"
---
# <a name="call-javascript-functions-from-net-methods-in-aspnet-core-no-locblazor"></a>Aufrufen von JavaScript-Funktionen über .NET-Methoden in ASP.NET Core Blazor

Von [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), [Pranav Krishnamoorthy](https://github.com/pranavkm) und [Luke Latham](https://github.com/guardrex)

Eine Blazor-App kann JavaScript-Funktionen über .NET-Methoden und .NET-Methoden über JavaScript-Funktionen aufrufen. Diese Szenarios werden als *JavaScript-Interoperabilität* (*JS Interop*) bezeichnet.

In diesem Artikel wird das Aufrufen von JavaScript-Funktionen über .NET behandelt. Weitere Informationen zum Aufrufen von .NET-Methoden über JavaScript finden Sie unter <xref:blazor/call-dotnet-from-javascript>.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

Verwenden Sie die <xref:Microsoft.JSInterop.IJSRuntime>-Abstraktion, um JavaScript über .NET aufzurufen. Fügen Sie die <xref:Microsoft.JSInterop.IJSRuntime>-Abstraktion in Ihre Komponente ein, um JS Interop-Aufrufe auszugeben. <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> akzeptiert einen Bezeichner für die JavaScript-Funktion, die Sie aufrufen möchten, sowie eine beliebige Anzahl JSON-serialisierbarer Argumente. Der Funktionsbezeichner ist relativ zum globalen Bereich (`window`). Wenn Sie `window.someScope.someFunction` aufrufen möchten, lautet der Bezeichner `someScope.someFunction`. Die Funktion muss nicht registriert werden, bevor sie aufgerufen wird. Der Rückgabetyp `T` muss ebenfalls JSON-serialisierbar sein. `T` sollte mit dem .NET-Typ übereinstimmen, der dem zurückgegebenen JSON-Typ am ehesten entspricht.

JavaScript-Funktionen, die eine [Zusage](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) zurückgeben, werden mit <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> aufgerufen. `InvokeAsync` entpackt die Zusage und gibt den Wert zurück, der von der Zusage erwartet wird.

Bei Blazor Server-Apps mit aktiviertem Prerendering ist das Aufrufen von JavaScript beim ersten Prerendering nicht möglich. JavaScript-Interoperabilitätsaufrufe müssen verzögert werden, bis die Verbindung mit dem Browser hergestellt ist. Weitere Informationen finden Sie im Abschnitt [Erkennen, wenn für eine Blazor Server-App ein Prerendering durchgeführt wird](#detect-when-a-blazor-server-app-is-prerendering).

Das folgende Beispiel basiert auf [`TextDecoder`](https://developer.mozilla.org/docs/Web/API/TextDecoder), einem auf JavaScript basierenden Decoder. Im Beispiel wird veranschaulicht, wie eine JavaScript-Funktion aus einer C#-Methode aufgerufen wird, die eine Anforderung aus Entwicklercode in eine vorhandene JavaScript-API auslagert. Die JavaScript-Funktion akzeptiert ein Bytearray von einer C#-Methode, decodiert das Array und gibt den Text zum Anzeigen an die Komponente zurück.

Geben Sie im `<head>`-Element von `wwwroot/index.html` (Blazor WebAssembly) oder `Pages/_Host.cshtml` (Blazor Server) eine JavaScript-Funktion an, die `TextDecoder` verwendet, um ein übermitteltes Array zu decodieren und den decodierten Wert zurückzugeben:

[!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-convertarray.html)]

JavaScript-Code, z. B. der Code im vorherigen Beispiel, kann auch über eine JavaScript-Datei (`.js`) mit einem Verweis auf die Skriptdatei geladen werden:

```html
<script src="exampleJsInterop.js"></script>
```

Die folgende Komponente führt folgende Aktionen aus:

* Sie ruft die JavaScript-Funktion `convertArray` mit `JS` auf, wenn eine Komponentenschaltfläche ( **`Convert Array`** ) ausgewählt wird.
* Nachdem die JavaScript-Funktion aufgerufen wurde, wird das übergebene Array in eine Zeichenfolge konvertiert. Die Zeichenfolge wird zur Anzeige an die Komponente zurückgegeben.

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/call-js-example.razor?highlight=2,34-35)]

## <a name="ijsruntime"></a>IJSRuntime

Verwenden Sie einen der folgenden Ansätze, um die <xref:Microsoft.JSInterop.IJSRuntime>-Abstraktion zu verwenden:

* Fügen Sie die <xref:Microsoft.JSInterop.IJSRuntime>-Abstraktion in die Razor-Komponente (`.razor`) ein:

  [!code-razor[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction.razor?highlight=1)]

  Stellen Sie innerhalb des `<head>`-Elements von `wwwroot/index.html` (Blazor WebAssembly) oder `Pages/_Host.cshtml` (Blazor Server) eine `handleTickerChanged`-JavaScript-Funktion bereit. Die Funktion wird mit <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> aufgerufen und gibt keinen Wert zurück:

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged1.html)]

* Fügen Sie die <xref:Microsoft.JSInterop.IJSRuntime>-Abstraktion in eine Klasse (`.cs`) ein:

  [!code-csharp[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction-class.cs?highlight=5)]

  Stellen Sie innerhalb des `<head>`-Elements von `wwwroot/index.html` (Blazor WebAssembly) oder `Pages/_Host.cshtml` (Blazor Server) eine `handleTickerChanged`-JavaScript-Funktion bereit. Die Funktion wird mit `JS.InvokeAsync` aufgerufen und gibt einen Wert zurück:

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged2.html)]

* Verwenden Sie das `[Inject]`-Attribut für die dynamische Inhaltsgenerierung mit [BuildRenderTree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic):

  ```razor
  [Inject]
  IJSRuntime JS { get; set; }
  ```

In der clientseitigen Beispiel-App zu diesem Artikel stehen der App zwei JavaScript-Funktionen zur Verfügung, die mit dem DOM (Dokumentobjektmodell) interagieren, um Benutzereingaben zu empfangen und eine Begrüßungsnachricht anzuzeigen:

* `showPrompt`: Generiert eine Eingabeaufforderung zum Akzeptieren der Benutzereingaben (dem Namen des Benutzers) und gibt den Namen an den Aufrufer zurück.
* `displayWelcome`: Weist einem DOM-Objekt eine Begrüßungsnachricht vom Aufrufer mit einer `id` mit dem Wert `welcome` zu.

`wwwroot/exampleJsInterop.js`:

[!code-javascript[](./common/samples/5.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=2-7)]

Platzieren Sie das `<script>`-Tag, das auf die JavaScript-Datei verweist, in der `wwwroot/index.html`-Datei (Blazor WebAssembly) oder der `Pages/_Host.cshtml`-Datei (Blazor Server).

`wwwroot/index.html` (Blazor WebAssembly):

[!code-html[](./common/samples/5.x/BlazorWebAssemblySample/wwwroot/index.html?highlight=22)]

`Pages/_Host.cshtml` (Blazor Server):

[!code-cshtml[](./common/samples/5.x/BlazorServerSample/Pages/_Host.cshtml?highlight=34)]

Platzieren Sie kein `<script>`-Tag in einer Komponentendatei, weil das `<script>`-Tag nicht dynamisch aktualisiert werden kann.

Die Interoperabilität von .NET-Methoden mit den JavaScript-Funktionen in der Datei `exampleJsInterop.js` erfolgt, indem <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType> aufgerufen wird.

Die <xref:Microsoft.JSInterop.IJSRuntime>-Abstraktion ist asynchron, um Blazor Server-Szenarios zu ermöglichen. Wenn es sich bei der App um eine Blazor WebAssembly-App handelt, sollten Sie die JavaScript-Funktion synchron aufrufen, eine Typumwandlung nach unten in <xref:Microsoft.JSInterop.IJSInProcessRuntime> durchführen und stattdessen <xref:Microsoft.JSInterop.IJSInProcessRuntime.Invoke%2A> aufrufen. Es wird empfohlen, für die meisten JS Interop-Bibliotheken asynchrone APIs zu verwenden, um sicherzustellen, dass die Bibliotheken in allen Szenarios verfügbar sind.

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> Informationen zum Aktivieren der JavaScript-Isolation in [JavaScript-Standardmodulen](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules) finden Sie im Abschnitt [Blazor-JavaScript-Isolation und Objektverweise](#blazor-javascript-isolation-and-object-references).

::: moniker-end

Die Beispiel-App enthält eine Komponente zur Veranschaulichung der JavaScript-Interoperabilität. Die Komponente:

* empfängt Benutzereingaben über eine JavaScript-Eingabeaufforderung.
* gibt den Text zur Verarbeitung an die Komponente zurück.
* ruft eine zweite JavaScript-Funktion auf, die mit dem DOM interagiert, um eine Begrüßungsnachricht anzuzeigen.

`Pages/JsInterop.razor`:

```razor
@page "/JSInterop"
@using {APP ASSEMBLY}.JsInteropClasses
@inject IJSRuntime JS

<h1>JavaScript Interop</h1>

<h2>Invoke JavaScript functions from .NET methods</h2>

<button type="button" class="btn btn-primary" @onclick="TriggerJsPrompt">
    Trigger JavaScript Prompt
</button>

<h3 id="welcome" style="color:green;font-style:italic"></h3>

@code {
    public async Task TriggerJsPrompt()
    {
        var name = await JS.InvokeAsync<string>(
                "exampleJsFunctions.showPrompt",
                "What's your name?");

        await JS.InvokeVoidAsync(
                "exampleJsFunctions.displayWelcome",
                $"Hello {name}! Welcome to Blazor!");
    }
}
```

Der Platzhalter `{APP ASSEMBLY}` ist der App-Assemblyname der App (z. B. `BlazorSample`).

1. Wenn `TriggerJsPrompt` ausgeführt wird, indem die Schaltfläche **`Trigger JavaScript Prompt`** der Komponente ausgewählt wird, wird die `showPrompt`-Funktion von JavaScript aufgerufen, die in der `wwwroot/exampleJsInterop.js`-Datei bereitgestellt wird.
1. Die `showPrompt`-Funktion akzeptiert Benutzereingaben (den Namen des Benutzers), die HTML-codiert und an die Komponente zurückgegeben werden. Die Komponente speichert den Namen des Benutzers in der lokalen Variable `name`.
1. Die in `name` gespeicherte Zeichenfolge wird in eine Begrüßungsnachricht eingebunden, die an die JavaScript-Funktion `displayWelcome` übergeben wird, die die Begrüßungsnachricht in ein Überschriftentag rendert.

## <a name="call-a-void-javascript-function"></a>Aufrufen der JavaScript-Funktion „void“

Verwenden Sie <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> für Folgendes:

* JavaScript-Funktionen, die [void(0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) oder [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined) zurückgeben
* Wenn .NET nicht erforderlich ist, um das Ergebnis eines JavaScript-Aufrufs zu lesen

## <a name="detect-when-a-no-locblazor-server-app-is-prerendering"></a>Erkennen, wenn für eine Blazor Server-App ein Prerendering durchgeführt wird
 
[!INCLUDE[](~/blazor/includes/prerendering.md)]

## <a name="capture-references-to-elements"></a>Erfassen von Verweisen auf Elemente

Einige JS Interop-Szenarios erfordern Verweise auf HTML-Elemente. Beispielsweise erfordert eine Benutzeroberflächenbibliothek möglicherweise einen Elementverweis für die Initialisierung, oder Sie müssen befehlsähnliche APIs für ein Element aufrufen, z. B. `focus` oder `play`.

Erfassen Sie mithilfe des folgenden Ansatzes Verweise auf HTML-Elemente in einer Komponente:

* Fügen Sie ein `@ref`-Attribut zum HTML-Element hinzu.
* Definieren Sie ein Feld vom Typ <xref:Microsoft.AspNetCore.Components.ElementReference>, dessen Name mit dem Wert des `@ref`-Attributs übereinstimmt.

Im folgenden Beispiel wird das Erfassen eines Verweises auf das `username` `<input>`-Element veranschaulicht:

```razor
<input @ref="username" ... />

@code {
    ElementReference username;
}
```

> [!WARNING]
> Verwenden Sie Elementverweise nur, um die Inhalte eines leeren Elements zu ändern, das nicht mit Blazor interagiert. Dieses Szenario ist nützlich, wenn eine Drittanbieter-API Inhalt für das Element bereitstellt. Da Blazor nicht mit dem Element interagiert, kann kein Konflikt zwischen der Darstellung von Blazor des Elements und dem DOM auftreten.
>
> Im folgenden Beispiel ist es *gefährlich*, die Inhalte der unsortierten Liste (`ul`) zu ändern, weil Blazor mit dem DOM interagiert, um die Listenelemente (`<li>`) dieses Elements aufzufüllen:
>
> ```razor
> <ul ref="MyList">
>     @foreach (var item in Todos)
>     {
>         <li>@item.Text</li>
>     }
> </ul>
> ```
>
> Wenn die Inhalte des Elements `MyList` durch JS Interop geändert werden und Blazor versucht, diffs-Algorithmen auf das Element anzuwenden, entsprechen diese nicht dem DOM.

Eine <xref:Microsoft.AspNetCore.Components.ElementReference> wird über JS Interop an JavaScript-Code übergeben. Der JavaScript-Code empfängt eine `HTMLElement`-Instanz, die er mit normalen DOM-APIs verwenden kann. Im folgenden Code wird beispielsweise eine .NET-Erweiterungsmethode definiert, die das Senden eines Mausklicks an ein Element ermöglicht:

`exampleJsInterop.js`:

```javascript
window.interopFunctions = {
  clickElement : function (element) {
    element.click();
  }
}
```

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> Verwenden Sie [`FocusAsync`](xref:blazor/components/event-handling#focus-an-element) im C# Code, um ein Element zu fokussieren, das in das Blazor-Framework integriert ist und mit Elementverweisen arbeitet.

::: moniker-end

Verwenden Sie <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType>, um eine JavaScript-Funktion aufzurufen, die keinen Wert zurückgibt. Mit dem folgenden Code wird ein clientseitiges `Click`-Ereignis ausgelöst, indem die vorherige JavaScript-Funktion mit dem erfassten <xref:Microsoft.AspNetCore.Components.ElementReference> aufgerufen wird:

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component1.razor?highlight=14-15)]

Erstellen Sie eine statische Erweiterungsmethode, die die <xref:Microsoft.JSInterop.IJSRuntime>-Instanz empfängt, um eine Erweiterungsmethode zu verwenden:

```csharp
public static async Task TriggerClickEvent(this ElementReference elementRef, 
    IJSRuntime js)
{
    await js.InvokeVoidAsync("interopFunctions.clickElement", elementRef);
}
```

Die `clickElement`-Methode wird direkt für das Objekt aufgerufen. Im folgenden Beispiel wird davon ausgegangen, dass die `TriggerClickEvent`-Methode vom `JsInteropClasses`-Namespace verfügbar ist:

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component2.razor?highlight=15)]

> [!IMPORTANT]
> Die `exampleButton`-Variable wird erst aufgefüllt, nachdem die Komponente gerendert wurde. Wenn ein nicht aufgefüllter <xref:Microsoft.AspNetCore.Components.ElementReference>-Wert an JavaScript-Code übergeben wird, empfängt der JavaScript-Code den Wert `null`. Sie können Elementverweise bearbeiten, nachdem die Komponente das Rendering abgeschlossen hat, indem Sie die [Lebenszyklusmethoden `OnAfterRenderAsync` oder `OnAfterRender`](xref:blazor/components/lifecycle#after-component-render) verwenden.

Verwenden Sie <xref:System.Threading.Tasks.ValueTask%601>, wenn Sie mit generischen Typen arbeiten und einen Wert zurückgeben:

```csharp
public static ValueTask<T> GenericMethod<T>(this ElementReference elementRef, 
    IJSRuntime js)
{
    return js.InvokeAsync<T>("exampleJsFunctions.doSomethingGeneric", elementRef);
}
```

`GenericMethod` wird direkt für das Objekt mit einem Typ aufgerufen. Im folgenden Beispiel wird davon ausgegangen, dass die `GenericMethod`-Methode vom `JsInteropClasses`-Namespace verfügbar ist:

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component3.razor?highlight=17)]

## <a name="reference-elements-across-components"></a>Komponentenübergreifende Verweiselemente

Ein <xref:Microsoft.AspNetCore.Components.ElementReference> kann aus folgenden Gründen nicht zwischen Komponenten übermittelt werden:

* Das Vorhandensein der Instanz ist nur nach dem Rendern der Komponente garantiert, das während oder nach der Ausführung der <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A>/<xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>-Methode einer Komponente erfolgt.
* Ein <xref:Microsoft.AspNetCore.Components.ElementReference> ist ein [`struct`](/csharp/language-reference/builtin-types/struct), der nicht als [Komponentenparameter](xref:blazor/components/index#component-parameters) übergeben werden kann.

Damit eine übergeordnete Komponente einen Elementverweis anderen Komponenten zur Verfügung stellen kann, kann die übergeordnete Komponente:

* zulassen, dass untergeordnete Komponenten Rückrufe registrieren.
* die registrierten Rückrufe während des <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A>-Ereignisses mit dem übergebenen Elementverweis aufrufen. Dieser Ansatz ermöglicht untergeordneten Komponenten indirekt die Interaktion mit dem Elementverweis des übergeordneten Elements.

Dieser Ansatz wird anhand des folgenden Blazor WebAssembly-Beispiels veranschaulicht.

Im `<head>` von `wwwroot/index.html`:

```html
<style>
    .red { color: red }
</style>
```

Im `<body>` von `wwwroot/index.html`:

```html
<script>
    function setElementClass(element, className) {
        /** @type {HTMLElement} **/
        var myElement = element;
        myElement.classList.add(className);
    }
</script>
```

`Pages/Index.razor` (übergeordnete Komponente):

```razor
@page "/"

<h1 @ref="title">Hello, world!</h1>

Welcome to your new app.

<SurveyPrompt Parent="this" Title="How is Blazor working for you?" />
```

`Pages/Index.razor.cs`:

```csharp
using System;
using System.Collections.Generic;
using Microsoft.AspNetCore.Components;

namespace {APP ASSEMBLY}.Pages
{
    public partial class Index : 
        ComponentBase, IObservable<ElementReference>, IDisposable
    {
        private bool disposing;
        private IList<IObserver<ElementReference>> subscriptions = 
            new List<IObserver<ElementReference>>();
        private ElementReference title;

        protected override void OnAfterRender(bool firstRender)
        {
            base.OnAfterRender(firstRender);

            foreach (var subscription in subscriptions)
            {
                try
                {
                    subscription.OnNext(title);
                }
                catch (Exception)
                {
                    throw;
                }
            }
        }

        public void Dispose()
        {
            disposing = true;

            foreach (var subscription in subscriptions)
            {
                try
                {
                    subscription.OnCompleted();
                }
                catch (Exception)
                {
                }
            }

            subscriptions.Clear();
        }

        public IDisposable Subscribe(IObserver<ElementReference> observer)
        {
            if (disposing)
            {
                throw new InvalidOperationException("Parent being disposed");
            }

            subscriptions.Add(observer);

            return new Subscription(observer, this);
        }

        private class Subscription : IDisposable
        {
            public Subscription(IObserver<ElementReference> observer, Index self)
            {
                Observer = observer;
                Self = self;
            }

            public IObserver<ElementReference> Observer { get; }
            public Index Self { get; }

            public void Dispose()
            {
                Self.subscriptions.Remove(Observer);
            }
        }
    }
}
```

Der Platzhalter `{APP ASSEMBLY}` ist der App-Assemblyname der App (z. B. `BlazorSample`).

`Shared/SurveyPrompt.razor` (untergeordnete Komponente):

```razor
@inject IJSRuntime JS

<div class="alert alert-secondary mt-4" role="alert">
    <span class="oi oi-pencil mr-2" aria-hidden="true"></span>
    <strong>@Title</strong>

    <span class="text-nowrap">
        Please take our
        <a target="_blank" class="font-weight-bold" 
            href="https://go.microsoft.com/fwlink/?linkid=2109206">brief survey</a>
    </span>
    and tell us what you think.
</div>

@code {
    [Parameter]
    public string Title { get; set; }
}
```

`Shared/SurveyPrompt.razor.cs`:

```csharp
using System;
using Microsoft.AspNetCore.Components;

namespace {APP ASSEMBLY}.Shared
{
    public partial class SurveyPrompt : 
        ComponentBase, IObserver<ElementReference>, IDisposable
    {
        private IDisposable subscription = null;

        [Parameter]
        public IObservable<ElementReference> Parent { get; set; }

        protected override void OnParametersSet()
        {
            base.OnParametersSet();

            if (subscription != null)
            {
                subscription.Dispose();
            }

            subscription = Parent.Subscribe(this);
        }

        public void OnCompleted()
        {
            subscription = null;
        }

        public void OnError(Exception error)
        {
            subscription = null;
        }

        public void OnNext(ElementReference value)
        {
            JS.InvokeAsync<object>(
                "setElementClass", new object[] { value, "red" });
        }

        public void Dispose()
        {
            subscription?.Dispose();
        }
    }
}
```

Der Platzhalter `{APP ASSEMBLY}` ist der App-Assemblyname der App (z. B. `BlazorSample`).

## <a name="harden-js-interop-calls"></a>Festschreiben von JS Interop-Aufrufen

JS Interop kann aufgrund von Netzwerkfehlern fehlschlagen und sollte als unzuverlässig behandelt werden. Standardmäßig weisen Blazor Server-Apps einen Timeout für JS Interop-Aufrufe an den Server nach einer Minute auf. Wenn eine App ein engeres Timeout tolerieren kann, legen Sie das Timeout mit einem der folgenden Ansätze fest:

* Legen Sie das Timeout global in `Startup.ConfigureServices` fest:

  ```csharp
  services.AddServerSideBlazor(
      options => options.JSInteropDefaultCallTimeout = TimeSpan.FromSeconds({SECONDS}));
  ```

* Pro Aufruf im Komponentencode kann ein einzelner Aufruf das Timeout festlegen:

  ```csharp
  var result = await JS.InvokeAsync<string>("MyJSOperation", 
      TimeSpan.FromSeconds({SECONDS}), new[] { "Arg1" });
  ```

Weitere Informationen zur Ressourcenauslastung finden Sie unter <xref:blazor/security/server/threat-mitigation>.

[!INCLUDE[](~/blazor/includes/share-interop-code.md)]

## <a name="avoid-circular-object-references"></a>Vermeiden von Objektzirkelbezügen

Objekte, die Zirkelbezüge enthalten, können auf dem Client für folgende Vorgänge nicht serialisiert werden:

* .NET-Methodenaufrufe.
* JavaScript-Methodenaufrufe von C#, wenn der Rückgabetyp Zirkelbezüge enthält.

Weitere Informationen finden Sie unter [Circular references are not supported, take two (dotnet/aspnetcore #20525)](https://github.com/dotnet/aspnetcore/issues/20525) (Zirkelbezüge werden nicht unterstützt, die Zweite [dotnet/aspnetcore #20525]).

::: moniker range=">= aspnetcore-5.0"

## <a name="no-locblazor-javascript-isolation-and-object-references"></a>Blazor-JavaScript-Isolation und Objektverweise

Blazor aktiviert JavaScript-Isolierung in [JavaScript-Standardmodulen](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules). JavaScript-Isolierung bietet die folgenden Vorteile:

* Importiertes JavaScript verschmutzt nicht mehr den globalen Namespace.
* Consumer einer Bibliothek und Komponenten müssen nicht das zugehörige JavaScript importieren.

Das folgende JavaScript-Modul exportiert z. B. eine JavaScript-Funktion, um eine Browsereingabeaufforderung anzuzeigen:

```javascript
export function showPrompt(message) {
  return prompt(message, 'Type anything here');
}
```

Fügen Sie das oben gezeigte JavaScript-Modul einer .NET-Bibliothek als statische Webressource (`wwwroot/exampleJsInterop.js`) hinzu, und importieren Sie das Modul dann mithilfe des <xref:Microsoft.JSInterop.IJSRuntime>-Diensts in den .NET-Code. Der Dienst wird im folgenden Beispiel als `js` (nicht gezeigt) eingefügt:

```csharp
var module = await js.InvokeAsync<IJSObjectReference>(
    "import", "./_content/MyComponents/exampleJsInterop.js");
```

Der Bezeichner `import` im Beispiel oben ist ein spezieller Bezeichner, der insbesondere zum Importieren eines JavaScript-Moduls verwendet wird. Geben Sie das Modul mithilfe seines stabilen statischen Webressourcenpfads an: `./_content/{LIBRARY NAME}/{PATH UNDER WWWROOT}`. Das Pfadsegment für das aktuelle Verzeichnis (`./`) ist erforderlich, damit der korrekte Pfad zu statischen Objekten in der JavaScript-Datei erstellt werden kann. Der Platzhalter `{LIBRARY NAME}` ist der Name der Bibliothek. Der Platzhalter `{PATH UNDER WWWROOT}` ist der Pfad zum Skript unter `wwwroot`.

<xref:Microsoft.JSInterop.IJSRuntime> importiert das Modul als `IJSObjectReference`-Element, das einen Verweis auf ein JavaScript-Objekt aus .NET-Code darstellt. Verwenden Sie `IJSObjectReference`, um exportierte JavaScript-Funktionen aus dem Modul aufzurufen:

```csharp
public async ValueTask<string> Prompt(string message)
{
    return await module.InvokeAsync<string>("showPrompt", message);
}
```

`IJSInProcessObjectReference` steht für einen Verweis auf ein JavaScript-Objekt, dessen Funktionen synchron aufgerufen werden können.

## <a name="use-of-javascript-libraries-that-render-ui-dom-elements"></a>Verwendung von JavaScript-Bibliotheken zum Rendern von Benutzeroberflächenelementen (DOM-Elemente)

Manchmal möchten Sie vielleicht JavaScript-Bibliotheken verwenden, die sichtbare Benutzeroberflächenelemente innerhalb des Browser-DOM erstellen. Auf den ersten Blick scheint dies schwierig zu sein, da das Vergleichssystem von Blazor darauf beruht, die Kontrolle über die Struktur von DOM-Elementen zu haben. Außerdem treten Fehler auf, wenn externer Code die DOM-Struktur bearbeitet, was den Mechanismus für die Diff-Anwendung unmöglich macht. Dabei handelt es sich um keine Blazor-spezifische Einschränkung. Die gleiche Herausforderung stellt sich bei jedem anderen Diff-basierten Benutzeroberflächenframework.

Glücklicherweise ist es einfach, extern erstellte Benutzeroberflächenelemente innerhalb der Oberfläche einer Blazor-Komponente zuverlässig einzubetten. Die hierfür empfohlene Methode besteht darin, den Code der Komponente (`.razor`-Datei) ein leeres Element erzeugen zu lassen. Im Vergleichssystem von Blazor ist das Element immer leer, der Renderer durchläuft das Element also nicht rekursiv, sondern berücksichtigt die Inhalte gar nicht. Dies führt dazu, dass das Auffüllen des Elements mit zufälligem extern verwalteten Inhalt sicher ist.

Das folgende Beispiel veranschaulicht das Konzept. Wenn innerhalb der `if`-Anweisung `firstRender` `true` ist, kann `myElement` genutzt werden. Beispielsweise wird für die Auffüllung eine externe JavaScript-Bibliothek aufgerufen. Blazor berücksichtigt die Inhalte des Elements solange nicht, bis die Komponente selbst entfernt wird. Wenn die Komponente entfernt wird, wird die gesamte DOM-Unterstruktur der Komponente ebenfalls entfernt.

```razor
<h1>Hello! This is a Blazor component rendered at @DateTime.Now</h1>

<div @ref="myElement"></div>

@code {
    HtmlElement myElement;
    
    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            ...
        }
    }
}
```

Als detailliertes Beispiel können Sie sich die folgende Komponente ansehen, die eine interaktive Karte mithilfe der [Open-Source-Mapbox-APIs](https://www.mapbox.com/) rendert:

```razor
@inject IJSRuntime JS
@implements IAsyncDisposable

<div @ref="mapElement" style='width: 400px; height: 300px;'></div>

<button @onclick="() => ShowAsync(51.454514, -2.587910)">Show Bristol, UK</button>
<button @onclick="() => ShowAsync(35.6762, 139.6503)">Show Tokyo, Japan</button>

@code
{
    ElementReference mapElement;
    IJSObjectReference mapModule;
    IJSObjectReference mapInstance;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            mapModule = await JS.InvokeAsync<IJSObjectReference>(
                "import", "./mapComponent.js");
            mapInstance = await mapModule.InvokeAsync<IJSObjectReference>(
                "addMapToElement", mapElement);
        }
    }

    Task ShowAsync(double latitude, double longitude)
        => mapModule.InvokeVoidAsync("setMapCenter", mapInstance, latitude, 
            longitude).AsTask();

    private async ValueTask IAsyncDisposable.DisposeAsync()
    {
        await mapInstance.DisposeAsync();
        await mapModule.DisposeAsync();
    }
}
```

Das entsprechende JavaScript-Modul, das unter `wwwroot/mapComponent.js` platziert werden sollte, sieht folgendermaßen aus:

```javascript
import 'https://api.mapbox.com/mapbox-gl-js/v1.12.0/mapbox-gl.js';

// TO MAKE THE MAP APPEAR YOU MUST ADD YOUR ACCESS TOKEN FROM 
// https://account.mapbox.com
mapboxgl.accessToken = '{ACCESS TOKEN}';

export function addMapToElement(element) {
  return new mapboxgl.Map({
    container: element,
    style: 'mapbox://styles/mapbox/streets-v11',
    center: [-74.5, 40],
    zoom: 9
  });
}

export function setMapCenter(map, latitude, longitude) {
  map.setCenter([longitude, latitude]);
}
```

Ersetzen Sie im vorherigen Beispiel die Zeichenfolge `{ACCESS TOKEN}` durch ein gültiges Zugriffstoken, das Sie von https://account.mapbox.com abrufen können.

Für eine korrekte Formatierung fügen Sie der HTML-Hostseite den folgenden Stylesheet-Tag hinzu (`index.html` oder `_Host.cshtml`):

```html
<link rel="stylesheet" href="https://api.mapbox.com/mapbox-gl-js/v1.12.0/mapbox-gl.css" />
```

Das vorangehende Beispiel erzeugt eine interaktive Kartenbenutzeroberfläche, auf der der Benutzer folgende Möglichkeiten hat:

* Ziehen der Karte zum Scrollen oder Zoomen
* Klicken auf Schaltflächen, um zu vordefinierten Standorten zu springen

![Mapbox-Straßenkarte von Tokio in Japan, mit Schaltflächen, über die Bristol im Vereinigten Königreich und Tokio in Japan ausgewählt werden können](https://user-images.githubusercontent.com/1101362/94939821-92ef6700-04ca-11eb-858e-fff6df0053ae.png)

Die wichtigsten Punkte, die verstanden werden müssen, sind die folgenden:

 * Für Blazor gilt, dass `<div>` mit `@ref="mapElement"` leer bleibt. Deshalb kann die Auffüllung durch `mapbox-gl.js` sicher erfolgen, und die Inhalte können im Laufe der Zeit bearbeitet werden. Sie können dieses Vorgehen für alle JavaScript-Bibliotheken verwenden, die Benutzeroberflächen rendern. Sie könnten sogar Komponenten des JavaScript-SPA-Frameworks eines Drittanbieters innerhalb der Blazor-Komponenten einbetten, solange nicht aktiv versucht wird, andere Teile der Seite zu ändern. Es ist *nicht* sicher, wenn externer JavaScript-Code Elemente bearbeitet, die von Blazor nicht als leer klassifiziert werden.
 * Bei Verwendung dieses Ansatzes sollten Sie die Regeln im Hinterkopf behalten, wie Blazor DOM-Elemente beibehält oder löscht. Im vorherigen Beispiel kann die Komponente Klickereignisse auf Schaltflächen sicher verarbeiten und die vorhandene Karteninstanz aktualisieren, da DOM-Elemente standardmäßig beibehalten werden, wo dies möglich ist. Wenn Sie eine Liste von Kartenelementen innerhalb einer `@foreach`-Schleife rendern, sollten Sie `@key` verwenden, um für die Beibehaltung der Komponenteninstanzen zu sorgen. Andernfalls könnten Änderungen der Listendaten dazu führen, dass Komponenteninstanzen den Status vorheriger Instanzen auf nicht gewünschte Weise beibehalten. Weitere Informationen finden Sie unter [Verwenden von @key zur Beibehaltung von Elementen und Komponenten](xref:blazor/components/index#use-key-to-control-the-preservation-of-elements-and-components).

Außerdem zeigt das vorherige Beispiel, wie es möglich ist, JavaScript-Logik und -Abhängigkeiten innerhalb eines ES6-Moduls zu kapseln und dynamisch mithilfe des `import`-Bezeichners zu laden. Weitere Informationen finden Sie unter [JavaScript-Isolierung in Blazor und Objektverweise](#blazor-javascript-isolation-and-object-references).

::: moniker-end

## <a name="size-limits-on-js-interop-calls"></a>Größenbeschränkungen bei JS-Interop-Aufrufen

In Blazor WebAssembly gibt das Framework keine Beschränkungen hinsichtlich der Größe von JS Interop-Eingaben und -Ausgaben vor.

In Blazor Server werden JS Interop-Aufrufe hinsichtlich ihrer Größe durch den Maximalwert eingehender SignalR-Nachrichten beschränkt, die für Hubmethoden zulässig sind. Dies wird von <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize?displayProperty=nameWithType> erzwungen. Der Standardwert beträgt 32 KB. SignalR-Nachrichten von JS an .NET, die größer als <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize> sind, führen zu einem Fehler. Das Framework beinhaltet keine Beschränkungen hinsichtlich der Größe einer SignalR-Nachricht vom Hub an einen Client. Weitere Informationen finden Sie unter <xref:blazor/call-dotnet-from-javascript#size-limits-on-js-interop-calls>.
  
## <a name="js-modules"></a>JS-Module

Für die JS-Isolation funktioniert JS-Interop mit der Standardunterstützung des Browsers für [ECMAScript-Module (ESM)](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules) ([ECMAScript-Spezifikation](https://tc39.es/ecma262/#sec-modules)).

## <a name="unmarshalled-js-interop"></a>Nicht gemarshallt JS-Interop-Aufrufe

Bei Blazor WebAssembly-Komponenten kann es zu Leistungseinbußen kommen, wenn .NET-Objekte für JS-Interop serialisiert werden und eine der folgenden Bedingungen zutrifft:

* Eine große Menge von .NET-Objekten wird schnell serialisiert. Beispiel: JS-Interop-Aufrufe werden auf Grundlage des Bewegens eines Eingabegeräts durchgeführt, z. B. beim Drehen eines Mausrads.
* Für JS-Interop müssen große .NET-Objekte oder viele .NET-Objekte serialisiert werden. Beispiel: Für JS-Interop-Aufrufe müssen Dutzende von Dateien serialisiert werden.

<xref:Microsoft.JSInterop.IJSUnmarshalledObjectReference> steht für einen Verweis auf ein JavaScript-Objekt, dessen Funktionen aufgerufen werden können, ohne dass .NET-Daten serialisiert werden müssen.

Im folgenden Beispiel:

* Eine [Struktur](/dotnet/csharp/language-reference/builtin-types/struct), die eine Zeichenfolge und einen Integerwert enthält, wird unserialisiert an JavaScript übergegeben.
* JavaScript-Funktionen verarbeiten die Daten und geben entweder einen booleschen Wert oder eine Zeichenfolge an den Aufrufer zurück.
* Eine JavaScript-Zeichenfolge ist nicht direkt in ein .NET-`string`-Objekt konvertierbar. Mit der `unmarshalledFunctionReturnString`-Funktion wird `BINDING.js_string_to_mono_string` aufgerufen, um die Konvertierung einer JavaScript-Zeichenfolge zu verwalten.

> [!NOTE]
> Die folgenden Beispiele sind für dieses Szenario keine typischen Anwendungsfälle, da die [Struktur](/dotnet/csharp/language-reference/builtin-types/struct), die an JavaScript übergeben wird, nicht zu einer schlechten Komponentenleistung führt. Im Beispiel wird ein kleines Objekt verwendet, um nur die Konzepte für das Übergeben von unserialisierten .NET-Daten zu veranschaulichen.

Der Inhalt eines `<script>`-Blocks in `wwwroot/index.html` oder einer externen JavaScript-Datei, auf die durch `wwwroot/index.html` verwiesen wird:

```javascript
window.returnJSObjectReference = () => {
    return {
        unmarshalledFunctionReturnBoolean: function (fields) {
            const name = Blazor.platform.readStringField(fields, 0);
            const year = Blazor.platform.readInt32Field(fields, 8);

            return name === "Brigadier Alistair Gordon Lethbridge-Stewart" &&
                year === 1968;
        },
        unmarshalledFunctionReturnString: function (fields) {
            const name = Blazor.platform.readStringField(fields, 0);
            const year = Blazor.platform.readInt32Field(fields, 8);

            return BINDING.js_string_to_mono_string(`Hello, ${name} (${year})!`);
        }
    };
}
```

> [!WARNING]
> Der Name, das Verhalten und das Vorhandensein der `js_string_to_mono_string`-Funktion kann sich in einer zukünftigen Version von .NET ändern. Beispiel:
>
> * Die Funktion wird wahrscheinlich umbenannt.
> * Die Funktion selbst kann zugunsten automatischer Konvertierung von Zeichenfolgen durch das Framework entfernt werden.

`Pages/UnmarshalledJSInterop.razor` (URL: `/unmarshalled-js-interop`):

```razor
@page "/unmarshalled-js-interop"
@using System.Runtime.InteropServices
@using Microsoft.JSInterop
@inject IJSRuntime JS

<h1>Unmarshalled JS interop</h1>

@if (callResultForBoolean)
{
    <p>JS interop was successful!</p>
}

@if (!string.IsNullOrEmpty(callResultForString))
{
    <p>@callResultForString</p>
}

<p>
    <button @onclick="CallJSUnmarshalledForBoolean">
        Call Unmarshalled JS & Return Boolean
    </button>
    <button @onclick="CallJSUnmarshalledForString">
        Call Unmarshalled JS & Return String
    </button>
</p>

<p>
    <a href="https://www.doctorwho.tv">Doctor Who</a>
    is a registered trademark of the <a href="https://www.bbc.com/">BBC</a>.
</p>

@code {
    private bool callResultForBoolean;
    private string callResultForString;

    private void CallJSUnmarshalledForBoolean()
    {
        var unmarshalledRuntime = (IJSUnmarshalledRuntime)JS;

        var jsUnmarshalledReference = unmarshalledRuntime
            .InvokeUnmarshalled<IJSUnmarshalledObjectReference>(
                "returnJSObjectReference");

        callResultForBoolean = 
            jsUnmarshalledReference.InvokeUnmarshalled<InteropStruct, bool>(
                "unmarshalledFunctionReturnBoolean", GetStruct());
    }

    private void CallJSUnmarshalledForString()
    {
        var unmarshalledRuntime = (IJSUnmarshalledRuntime)JS;

        var jsUnmarshalledReference = unmarshalledRuntime
            .InvokeUnmarshalled<IJSUnmarshalledObjectReference>(
                "returnJSObjectReference");

        callResultForString = 
            jsUnmarshalledReference.InvokeUnmarshalled<InteropStruct, string>(
                "unmarshalledFunctionReturnString", GetStruct());
    }

    private InteropStruct GetStruct()
    {
        return new InteropStruct
        {
            Name = "Brigadier Alistair Gordon Lethbridge-Stewart",
            Year = 1968,
        };
    }

    [StructLayout(LayoutKind.Explicit)]
    public struct InteropStruct
    {
        [FieldOffset(0)]
        public string Name;

        [FieldOffset(8)]
        public int Year;
    }
}
```

Wenn eine `IJSUnmarshalledObjectReference`-Instanz nicht im C#-Code verworfen wird, kann sie in JavaScript verworfen werden. Die folgende `dispose`-Funktion verwirft die Objektreferenz, wenn sie aus JavaScript aufgerufen wird:

```javascript
window.exampleJSObjectReferenceNotDisposedInCSharp = () => {
    return {
        dispose: function () {
            DotNet.disposeJSObjectReference(this);
        },

        ...
    };
}
```

Arraytypen können mithilfe von `js_typed_array_to_array` aus JavaScript-Objekten in .NET-Objekte konvertiert werden, aber das JavaScript-Array muss ein typisiertes Array sein. Arrays aus JavaScript können in C#-Code als .NET-Objektarray (`object[]`) gelesen werden.

Andere Datentypen, z. B. Zeichenfolgenarrays, können konvertiert werden, erfordern jedoch das Erstellen eines neuen Mono-Arrayobjekts (`mono_obj_array_new`) und das Festlegen seines Werts (`mono_obj_array_set`).

> [!WARNING]
> JavaScript-Funktionen, die vom Blazor-Framework bereitgestellt werden (z. B. `js_typed_array_to_array`, `mono_obj_array_new` und `mono_obj_array_set`), unterliegen Namensänderungen, Verhaltensänderungen oder der Entfernung in zukünftigen Versionen von .NET.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:blazor/call-dotnet-from-javascript>
* [InteropComponent.razor-Beispiel (dotnet/AspNetCore-GitHub-Repository, Releasebranch 3.1)](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
