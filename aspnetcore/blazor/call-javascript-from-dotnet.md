---
title: Aufrufen von JavaScript-Funktionen über .NET-Methoden in ASP.NET Core Blazor
author: guardrex
description: In diesem Artikel erfahren Sie, wie Sie JavaScript-Funktionen über .NET-Methoden in Blazor-Apps aufrufen.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/17/2020
no-loc:
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
ms.openlocfilehash: a62462e3a0a2366a8662573ada5d2e7589c14c0d
ms.sourcegitcommit: 24106b7ffffc9fff410a679863e28aeb2bbe5b7e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/17/2020
ms.locfileid: "90722474"
---
# <a name="call-javascript-functions-from-net-methods-in-aspnet-core-no-locblazor"></a>Aufrufen von JavaScript-Funktionen über .NET-Methoden in ASP.NET Core Blazor

Von [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27) und [Luke Latham](https://github.com/guardrex)

Eine Blazor-App kann JavaScript-Funktionen über .NET-Methoden und .NET-Methoden über JavaScript-Funktionen aufrufen. Diese Szenarios werden als *JavaScript-Interoperabilität* (*JS Interop*) bezeichnet.

In diesem Artikel wird das Aufrufen von JavaScript-Funktionen über .NET behandelt. Weitere Informationen zum Aufrufen von .NET-Methoden über JavaScript finden Sie unter <xref:blazor/call-dotnet-from-javascript>.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

Verwenden Sie die <xref:Microsoft.JSInterop.IJSRuntime>-Abstraktion, um JavaScript über .NET aufzurufen. Fügen Sie die <xref:Microsoft.JSInterop.IJSRuntime>-Abstraktion in Ihre Komponente ein, um JS Interop-Aufrufe auszugeben. <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> akzeptiert einen Bezeichner für die JavaScript-Funktion, die Sie aufrufen möchten, sowie eine beliebige Anzahl JSON-serialisierbarer Argumente. Der Funktionsbezeichner ist relativ zum globalen Bereich (`window`). Wenn Sie `window.someScope.someFunction` aufrufen möchten, lautet der Bezeichner `someScope.someFunction`. Die Funktion muss nicht registriert werden, bevor sie aufgerufen wird. Der Rückgabetyp `T` muss ebenfalls JSON-serialisierbar sein. `T` sollte mit dem .NET-Typ übereinstimmen, der dem zurückgegebenen JSON-Typ am ehesten entspricht.

Bei Blazor Server-Apps mit aktiviertem Prerendering ist das Aufrufen von JavaScript beim ersten Prerendering nicht möglich. JavaScript-Interoperabilitätsaufrufe müssen verzögert werden, bis die Verbindung mit dem Browser hergestellt ist. Weitere Informationen finden Sie im Abschnitt [Erkennen, wenn für eine Blazor Server-App ein Prerendering durchgeführt wird](#detect-when-a-blazor-server-app-is-prerendering).

Das folgende Beispiel basiert auf [`TextDecoder`](https://developer.mozilla.org/docs/Web/API/TextDecoder), einem auf JavaScript basierenden Decoder. Im Beispiel wird veranschaulicht, wie eine JavaScript-Funktion aus einer C#-Methode aufgerufen wird, die eine Anforderung aus Entwicklercode in eine vorhandene JavaScript-API auslagert. Die JavaScript-Funktion akzeptiert ein Bytearray von einer C#-Methode, decodiert das Array und gibt den Text zum Anzeigen an die Komponente zurück.

Geben Sie im `<head>`-Element von `wwwroot/index.html` (Blazor WebAssembly) oder `Pages/_Host.cshtml` (Blazor Server) eine JavaScript-Funktion an, die `TextDecoder` verwendet, um ein übermitteltes Array zu decodieren und den decodierten Wert zurückzugeben:

[!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-convertarray.html)]

JavaScript-Code, z. B. der Code im vorherigen Beispiel, kann auch über eine JavaScript-Datei (`.js`) mit einem Verweis auf die Skriptdatei geladen werden:

```html
<script src="exampleJsInterop.js"></script>
```

Die folgende Komponente führt folgende Aktionen aus:

* Sie ruft die JavaScript-Funktion `convertArray` mit `JSRuntime` auf, wenn eine Komponentenschaltfläche ( **`Convert Array`** ) ausgewählt wird.
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

  Stellen Sie innerhalb des `<head>`-Elements von `wwwroot/index.html` (Blazor WebAssembly) oder `Pages/_Host.cshtml` (Blazor Server) eine `handleTickerChanged`-JavaScript-Funktion bereit. Die Funktion wird mit `JSRuntime.InvokeAsync` aufgerufen und gibt einen Wert zurück:

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged2.html)]

* Verwenden Sie das `[Inject]`-Attribut für die dynamische Inhaltsgenerierung mit [BuildRenderTree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic):

  ```razor
  [Inject]
  IJSRuntime JSRuntime { get; set; }
  ```

In der clientseitigen Beispiel-App zu diesem Artikel stehen der App zwei JavaScript-Funktionen zur Verfügung, die mit dem DOM (Dokumentobjektmodell) interagieren, um Benutzereingaben zu empfangen und eine Begrüßungsnachricht anzuzeigen:

* `showPrompt`: Generiert eine Eingabeaufforderung zum Akzeptieren der Benutzereingaben (dem Namen des Benutzers) und gibt den Namen an den Aufrufer zurück.
* `displayWelcome`: Weist einem DOM-Objekt eine Begrüßungsnachricht vom Aufrufer mit einer `id` mit dem Wert `welcome` zu.

`wwwroot/exampleJsInterop.js`:

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=2-7)]

Platzieren Sie das `<script>`-Tag, das auf die JavaScript-Datei verweist, in der `wwwroot/index.html`-Datei (Blazor WebAssembly) oder der `Pages/_Host.cshtml`-Datei (Blazor Server).

`wwwroot/index.html` (Blazor WebAssembly):

[!code-html[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/index.html?highlight=22)]

`Pages/_Host.cshtml` (Blazor Server):

[!code-cshtml[](./common/samples/3.x/BlazorServerSample/Pages/_Host.cshtml?highlight=35)]

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
@inject IJSRuntime JSRuntime

<h1>JavaScript Interop</h1>

<h2>Invoke JavaScript functions from .NET methods</h2>

<button type="button" class="btn btn-primary" @onclick="TriggerJsPrompt">
    Trigger JavaScript Prompt
</button>

<h3 id="welcome" style="color:green;font-style:italic"></h3>

@code {
    public async Task TriggerJsPrompt()
    {
        var name = await JSRuntime.InvokeAsync<string>(
                "exampleJsFunctions.showPrompt",
                "What's your name?");

        await JSRuntime.InvokeVoidAsync(
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

JavaScript-Funktionen, die [void(0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) oder [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined) zurückgeben, werden mit <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> aufgerufen.

## <a name="detect-when-a-no-locblazor-server-app-is-prerendering"></a>Erkennen, wenn für eine Blazor Server-App ein Prerendering durchgeführt wird
 
[!INCLUDE[](~/includes/blazor-prerendering.md)]

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

Bei .NET-Code handelt es sich bei <xref:Microsoft.AspNetCore.Components.ElementReference> um einen nicht transparenten Handle. Das *Einzige*, was Sie mit <xref:Microsoft.AspNetCore.Components.ElementReference> tun können, ist, es per JS Interop an JavaScript-Code zu übergeben. Dadurch empfängt der JavaScript-seitige Code eine `HTMLElement`-Instanz, die er mit normalen DOM-APIs verwenden kann.

Im folgenden Code wird beispielsweise eine .NET-Erweiterungsmethode definiert, die das Festlegen des Fokus auf ein Element ermöglicht:

`exampleJsInterop.js`:

```javascript
window.exampleJsFunctions = {
  focusElement : function (element) {
    element.focus();
  }
}
```

Verwenden Sie <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType>, um eine JavaScript-Funktion aufzurufen, die keinen Wert zurückgibt. Mit dem folgenden Code wird der Fokus auf die Eingabe des Benutzernamens festgelegt, indem die vorherige JavaScript-Funktion mit dem erfassten <xref:Microsoft.AspNetCore.Components.ElementReference>-Wert aufgerufen wird:

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component1.razor?highlight=1,3,11-12)]

Erstellen Sie eine statische Erweiterungsmethode, die die <xref:Microsoft.JSInterop.IJSRuntime>-Instanz empfängt, um eine Erweiterungsmethode zu verwenden:

```csharp
public static async Task Focus(this ElementReference elementRef, IJSRuntime jsRuntime)
{
    await jsRuntime.InvokeVoidAsync(
        "exampleJsFunctions.focusElement", elementRef);
}
```

Die `Focus`-Methode wird direkt für das Objekt aufgerufen. Im folgenden Beispiel wird davon ausgegangen, dass die `Focus`-Methode vom `JsInteropClasses`-Namespace verfügbar ist:

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component2.razor?highlight=1-4,12)]

> [!IMPORTANT]
> Die `username`-Variable wird erst aufgefüllt, nachdem die Komponente gerendert wurde. Wenn ein nicht aufgefüllter <xref:Microsoft.AspNetCore.Components.ElementReference>-Wert an JavaScript-Code übergeben wird, empfängt der JavaScript-Code den Wert `null`. Um Elementverweise zu ändern, nachdem die Komponente gerendert wurde (um den anfänglichen Fokus für ein Element festzulegen), verwenden Sie die [`OnAfterRenderAsync`- oder `OnAfterRender`-Komponentenlebenszyklus-Methoden ](xref:blazor/components/lifecycle#after-component-render).

Verwenden Sie <xref:System.Threading.Tasks.ValueTask%601>, wenn Sie mit generischen Typen arbeiten und einen Wert zurückgeben:

```csharp
public static ValueTask<T> GenericMethod<T>(this ElementReference elementRef, 
    IJSRuntime jsRuntime)
{
    return jsRuntime.InvokeAsync<T>(
        "exampleJsFunctions.doSomethingGeneric", elementRef);
}
```

`GenericMethod` wird direkt für das Objekt mit einem Typ aufgerufen. Im folgenden Beispiel wird davon ausgegangen, dass die `GenericMethod`-Methode vom `JsInteropClasses`-Namespace verfügbar ist:

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component3.razor?highlight=17)]

## <a name="reference-elements-across-components"></a>Komponentenübergreifende Verweiselemente

<xref:Microsoft.AspNetCore.Components.ElementReference> ist nur in der <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A>-Methode einer Komponente garantiert gültig (und ein Elementverweis ist ein `struct`), daher kann ein Elementverweis nicht zwischen Komponenten übergeben werden.

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
  var result = await JSRuntime.InvokeAsync<string>("MyJSOperation", 
      TimeSpan.FromSeconds({SECONDS}), new[] { "Arg1" });
  ```

Weitere Informationen zur Ressourcenauslastung finden Sie unter <xref:blazor/security/server/threat-mitigation>.

[!INCLUDE[](~/includes/blazor-share-interop-code.md)]

## <a name="avoid-circular-object-references"></a>Vermeiden von Objektzirkelbezügen

Objekte, die Zirkelbezüge enthalten, können auf dem Client für folgende Vorgänge nicht serialisiert werden:

* .NET-Methodenaufrufe.
* JavaScript-Methodenaufrufe von C#, wenn der Rückgabetyp Zirkelbezüge enthält.

Weitere Informationen finden Sie unter den folgenden Problemen:

* [Zirkelbezüge werden nicht unterstützt, die Zweite (dotnet/aspnetcore #20525)](https://github.com/dotnet/aspnetcore/issues/20525)
* [Vorschlag: Hinzufügen eines Mechanismus zur Verarbeitung von Zirkelbezügen bei der Serialisierung (dotnet/runtime #30820)](https://github.com/dotnet/runtime/issues/30820)

::: moniker range=">= aspnetcore-5.0"

## <a name="no-locblazor-javascript-isolation-and-object-references"></a>Blazor-JavaScript-Isolation und Objektverweise

Blazor aktiviert die JavaScript-Isolation in [JavaScript-Standardmodulen](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules). JavaScript-Isolation bietet die folgenden Vorteile:

* Importiertes JavaScript verschmutzt nicht mehr den globalen Namespace.
* Consumer einer Bibliothek und Komponenten müssen nicht das zugehörige JavaScript importieren.

Das folgende JavaScript-Modul exportiert z. B. eine JavaScript-Funktion, um eine Browsereingabeaufforderung anzuzeigen:

```javascript
export function showPrompt(message) {
  return prompt(message, 'Type anything here');
}
```

Fügen Sie das oben gezeigte JavaScript-Modul einer .NET-Bibliothek als statische Webressource (`wwwroot/exampleJsInterop.js`) hinzu, und importieren Sie das Modul dann mithilfe des <xref:Microsoft.JSInterop.IJSRuntime>-Diensts in den .NET-Code. Der Dienst wird im folgenden Beispiel als `jsRuntime` (nicht gezeigt) eingefügt:

```csharp
var module = await jsRuntime.InvokeAsync<JSObjectReference>(
    "import", "./_content/MyComponents/exampleJsInterop.js");
```

Der Bezeichner `import` im Beispiel oben ist ein spezieller Bezeichner, der insbesondere zum Importieren eines JavaScript-Moduls verwendet wird. Geben Sie das Modul mithilfe seines stabilen statischen Webressourcenpfads an: `_content/{LIBRARY NAME}/{PATH UNDER WWWROOT}`. Der Platzhalter `{LIBRARY NAME}` ist der Name der Bibliothek. Der Platzhalter `{PATH UNDER WWWROOT}` ist der Pfad zum Skript unter `wwwroot`.

<xref:Microsoft.JSInterop.IJSRuntime> importiert das Modul als `JSObjectReference`-Element, das einen Verweis auf ein JavaScript-Objekt aus .NET-Code darstellt. Verwenden Sie `JSObjectReference`, um exportierte JavaScript-Funktionen aus dem Modul aufzurufen:

```csharp
public async ValueTask<string> Prompt(string message)
{
    return await module.InvokeAsync<string>("showPrompt", message);
}
```

::: moniker-end

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:blazor/call-dotnet-from-javascript>
* [InteropComponent.razor-Beispiel (dotnet/AspNetCore-GitHub-Repository, Releasebranch 3.1)](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
* [Ausführen umfangreicher Datenübertragungen in Blazor Server-Apps](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)
