---
title: Aufrufen von .NET-Methoden von JavaScript-Funktionen in ASP.NET Core Blazor
author: guardrex
description: In diesem Artikel erfahren Sie, wie Sie .NET-Methoden von JavaScript-Funktionen in Blazor-Apps aufrufen.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc, devx-track-js
ms.date: 08/12/2020
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
uid: blazor/call-dotnet-from-javascript
ms.openlocfilehash: 4a7c06ed985c290eb4b3ffca6d5ed74c6bc4e031
ms.sourcegitcommit: 2e3a967331b2c69f585dd61e9ad5c09763615b44
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92690291"
---
# <a name="call-net-methods-from-javascript-functions-in-aspnet-core-no-locblazor"></a>Aufrufen von .NET-Methoden von JavaScript-Funktionen in ASP.NET Core Blazor

Von [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), [Shashikant Rudrawadi](http://wisne.co) und [Luke Latham](https://github.com/guardrex)

Eine Blazor-App kann JavaScript-Funktionen über .NET-Methoden und .NET-Methoden über JavaScript-Funktionen aufrufen. Diese Szenarios werden als *JavaScript-Interoperabilität* ( *JS Interop* ) bezeichnet.

In diesem Artikel wird das Aufrufen von .NET-Methoden über JavaScript behandelt. Informationen zum Aufrufen von JavaScript-Funktionen über .NET finden Sie unter <xref:blazor/call-javascript-from-dotnet>.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

## <a name="static-net-method-call"></a>Statischer .NET-Methodenaufruf

Um eine statische .NET-Methode über JavaScript aufzurufen, verwenden Sie die Funktionen `DotNet.invokeMethod` oder `DotNet.invokeMethodAsync`. Übergeben Sie den Bezeichner der statischen Methode, die Sie aufrufen möchten, den Namen der Assembly, die die Funktion enthält, und alle Argumente. Die asynchrone Version wird bevorzugt, um Blazor Server-Szenarien zu unterstützen. Die .NET-Methode muss öffentlich und statisch sein sowie das Attribut [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) aufweisen. Das Aufrufen offener generischer Methoden wird derzeit nicht unterstützt.

Die Beispiel-App enthält eine C#-Methode zur Rückgabe eines `int`-Arrays. Das [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute)-Attribut wird auf die Methode angewendet.

`Pages/JsInterop.razor`:

```razor
<button type="button" class="btn btn-primary"
        onclick="exampleJsFunctions.returnArrayAsyncJs()">
    Trigger .NET static method ReturnArrayAsync
</button>

@code {
    [JSInvokable]
    public static Task<int[]> ReturnArrayAsync()
    {
        return Task.FromResult(new int[] { 1, 2, 3 });
    }
}
```

Das dem Client zugestellte JavaScript ruft die C# .NET-Methode auf.

`wwwroot/exampleJsInterop.js`:

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=8-14)]

Überprüfen Sie bei Auswahl der Schaltfläche **`Trigger .NET static method ReturnArrayAsync`** die Konsolenausgabe in den Web Developer Tools des Browsers.

Die Konsolenausgabe lautet:

```console
Array(4) [ 1, 2, 3, 4 ]
```

Der vierte Arraywert wird in das von `ReturnArrayAsync` zurückgegebene Array (`data.push(4);`) gepusht.

Standardmäßig ist der Methodenbezeichner der Methodenname, aber Sie können mit dem Attributkonstruktor [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) einen anderen Bezeichner angeben:

```csharp
@code {
    [JSInvokable("DifferentMethodName")]
    public static Task<int[]> ReturnArrayAsync()
    {
        return Task.FromResult(new int[] { 1, 2, 3 });
    }
}
```

In der clientseitigen JavaScript-Datei:

```javascript
returnArrayAsyncJs: function () {
  DotNet.invokeMethodAsync('{APP ASSEMBLY}', 'DifferentMethodName')
    .then(data => {
      data.push(4);
      console.log(data);
    });
}
```

Der Platzhalter `{APP ASSEMBLY}` ist der App-Assemblyname der App (z. B. `BlazorSample`).

## <a name="instance-method-call"></a>Aufruf der Instanzmethode

Sie können auch .NET-Instanzmethoden von JavaScript aus aufrufen. So rufen Sie .NET-Instanzmethoden von JavaScript aus auf

* Übergeben Sie die .NET-Instanz durch Verweis auf JavaScript:
  * Führen Sie einen statischen Aufruf an <xref:Microsoft.JSInterop.DotNetObjectReference.Create%2A?displayProperty=nameWithType> durch.
  * Umschließen Sie die Instanz mit einer <xref:Microsoft.JSInterop.DotNetObjectReference>-Instanz, und rufen Sie <xref:Microsoft.JSInterop.DotNetObjectReference.Create%2A> für die <xref:Microsoft.JSInterop.DotNetObjectReference>-Instanz auf. Entsorgen Sie <xref:Microsoft.JSInterop.DotNetObjectReference>-Objekte (ein Beispiel folgt später in diesem Abschnitt).
* Rufen Sie .NET-Instanzmethoden für die Instanz mit den Funktionen `invokeMethod` oder `invokeMethodAsync` auf. Die .NET-Instanz kann auch als Argument übergeben werden, wenn andere .NET-Methoden von JavaScript aus aufgerufen werden.

> [!NOTE]
> Die Beispiel-App protokolliert Nachrichten an die clientseitige Konsole. Für die folgenden Beispiele, die durch die Beispiel-App veranschaulicht werden, untersuchen Sie die Konsolenausgabe des Browsers in den Entwicklertools des Browsers.

Bei Auswahl der Schaltfläche **`Trigger .NET instance method HelloHelper.SayHello`** wird `ExampleJsInterop.CallHelloHelperSayHello` aufgerufen und der Name `Blazor` an die Methode übergeben.

`Pages/JsInterop.razor`:

```razor
<button type="button" class="btn btn-primary" @onclick="TriggerNetInstanceMethod">
    Trigger .NET instance method HelloHelper.SayHello
</button>

@code {
    public async Task TriggerNetInstanceMethod()
    {
        var exampleJsInterop = new ExampleJsInterop(JSRuntime);
        await exampleJsInterop.CallHelloHelperSayHello("Blazor");
    }
}
```

`CallHelloHelperSayHello` ruft die JavaScript-Funktion `sayHello` mit einer neuen Instanz von `HelloHelper` auf.

`JsInteropClasses/ExampleJsInterop.cs`:

[!code-csharp[](./common/samples/3.x/BlazorWebAssemblySample/JsInteropClasses/ExampleJsInterop.cs?name=snippet1&highlight=11-18)]

`wwwroot/exampleJsInterop.js`:

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=15-18)]

Der Name wird an den Konstruktor von `HelloHelper` übergeben, der die Eigenschaft `HelloHelper.Name` festlegt. Wenn die JavaScript-Funktion `sayHello` ausgeführt wird, gibt `HelloHelper.SayHello` die Nachricht `Hello, {Name}!` zurück, die von der JavaScript-Funktion in die Konsole geschrieben wird.

`JsInteropClasses/HelloHelper.cs`:

[!code-csharp[](./common/samples/3.x/BlazorWebAssemblySample/JsInteropClasses/HelloHelper.cs?name=snippet1&highlight=5,10-11)]

Konsolenausgabe in den Webentwicklertools des Browsers:

```console
Hello, Blazor!
```

Verwenden Sie einen der folgenden Ansätze, um einen Arbeitsspeicherverlust zu vermeiden und die Garbage Collection für eine Komponente zu ermöglichen, die ein <xref:Microsoft.JSInterop.DotNetObjectReference> erstellt:

* Löschen Sie das Objekt in der Klasse, die die <xref:Microsoft.JSInterop.DotNetObjectReference>-Instanz erstellt hat:

  ```csharp
  public class ExampleJsInterop : IDisposable
  {
      private readonly IJSRuntime jsRuntime;
      private DotNetObjectReference<HelloHelper> objRef;

      public ExampleJsInterop(IJSRuntime jsRuntime)
      {
          this.jsRuntime = jsRuntime;
      }

      public ValueTask<string> CallHelloHelperSayHello(string name)
      {
          objRef = DotNetObjectReference.Create(new HelloHelper(name));

          return jsRuntime.InvokeAsync<string>(
              "exampleJsFunctions.sayHello",
              objRef);
      }

      public void Dispose()
      {
          objRef?.Dispose();
      }
  }
  ```

  Das vorhergehende Muster, das in der Klasse `ExampleJsInterop` gezeigt wird, kann auch in einer Komponente implementiert werden:

  ```razor
  @page "/JSInteropComponent"
  @using {APP ASSEMBLY}.JsInteropClasses
  @implements IDisposable
  @inject IJSRuntime JSRuntime

  <h1>JavaScript Interop</h1>

  <button type="button" class="btn btn-primary" @onclick="TriggerNetInstanceMethod">
      Trigger .NET instance method HelloHelper.SayHello
  </button>

  @code {
      private DotNetObjectReference<HelloHelper> objRef;

      public async Task TriggerNetInstanceMethod()
      {
          objRef = DotNetObjectReference.Create(new HelloHelper("Blazor"));

          await JSRuntime.InvokeAsync<string>(
              "exampleJsFunctions.sayHello",
              objRef);
      }

      public void Dispose()
      {
          objRef?.Dispose();
      }
  }
  ```
  
  Der Platzhalter `{APP ASSEMBLY}` ist der App-Assemblyname der App (z. B. `BlazorSample`).

* Wenn die Komponente oder Klasse die <xref:Microsoft.JSInterop.DotNetObjectReference>-Instanz nicht löscht, löschen Sie das Objekt im Client, indem Sie `.dispose()` aufrufen:

  ```javascript
  window.myFunction = (dotnetHelper) => {
    dotnetHelper.invokeMethodAsync('{APP ASSEMBLY}', 'MyMethod');
    dotnetHelper.dispose();
  }
  ```

## <a name="component-instance-method-call"></a>Aufrufen der Komponenteninstanzmethode

So rufen Sie die .NET-Methoden einer Komponente auf:

* Verwenden Sie die `invokeMethod`- oder `invokeMethodAsync`-Funktion, um einen statischen Methodenaufruf an die Komponente auszuführen.
* Die statische Methode der Komponente umschließt den Aufruf der Instanzmethode als aufgerufene <xref:System.Action>.

> [!NOTE]
> Verwenden Sie für Blazor Server-Apps, bei denen mehrere Benutzer gleichzeitig dieselbe Komponente verwenden können, eine Hilfsklasse zum Aufrufen von Instanzmethoden.
>
> Weitere Informationen finden Sie im Abschnitt [Komponenteninstanzmethode-Hilfsklasse](#component-instance-method-helper-class).

In der clientseitigen JavaScript-Datei:

```javascript
function updateMessageCallerJS() {
  DotNet.invokeMethodAsync('{APP ASSEMBLY}', 'UpdateMessageCaller');
}
```

Der Platzhalter `{APP ASSEMBLY}` ist der App-Assemblyname der App (z. B. `BlazorSample`).

`Pages/JSInteropComponent.razor`:

```razor
@page "/JSInteropComponent"

<p>
    Message: @message
</p>

<p>
    <button onclick="updateMessageCallerJS()">Call JS Method</button>
</p>

@code {
    private static Action action;
    private string message = "Select the button.";

    protected override void OnInitialized()
    {
        action = UpdateMessage;
    }

    private void UpdateMessage()
    {
        message = "UpdateMessage Called!";
        StateHasChanged();
    }

    [JSInvokable]
    public static void UpdateMessageCaller()
    {
        action.Invoke();
    }
}
```

So übergeben Sie Argumente an die Instanzmethode:

* Fügen Sie dem JS-Methodenaufruf Parameter hinzu. Im folgenden Beispiel wird ein Name an die Methode übergeben. Der Liste können nach Bedarf weitere Parameter hinzugefügt werden.

  ```javascript
  function updateMessageCallerJS(name) {
    DotNet.invokeMethodAsync('{APP ASSEMBLY}', 'UpdateMessageCaller', name);
  }
  ```
  
  Der Platzhalter `{APP ASSEMBLY}` ist der App-Assemblyname der App (z. B. `BlazorSample`).

* Geben Sie die richtigen Typen für die <xref:System.Action> für die Parameter an. Geben Sie die Parameterliste für die C#-Methoden an. Rufen Sie die <xref:System.Action> (`UpdateMessage`) mit den Parametern (`action.Invoke(name)`) auf.

  `Pages/JSInteropComponent.razor`:

  ```razor
  @page "/JSInteropComponent"

  <p>
      Message: @message
  </p>

  <p>
      <button onclick="updateMessageCallerJS('Sarah Jane')">
          Call JS Method
      </button>
  </p>

  @code {
      private static Action<string> action;
      private string message = "Select the button.";

      protected override void OnInitialized()
      {
          action = UpdateMessage;
      }

      private void UpdateMessage(string name)
      {
          message = $"{name}, UpdateMessage Called!";
          StateHasChanged();
      }

      [JSInvokable]
      public static void UpdateMessageCaller(string name)
      {
          action.Invoke(name);
      }
  }
  ```

  Gibt `message` aus, wenn die Schaltfläche **Call JS Method** (JS-Methode aufrufen) ausgewählt wird:

  ```
  Sarah Jane, UpdateMessage Called!
  ```

## <a name="component-instance-method-helper-class"></a>Komponenteninstanzmethode-Hilfsklasse

Die-Hilfsklasse wird verwendet, um eine Instanzmethode als <xref:System.Action>aufzurufen. Hilfsklassen sind in folgenden Fällen nützlich:

* Mehrere Komponenten desselben Typs werden auf derselben Seite gerendert.
* Es wird eine Blazor Server-App verwendet, in der mehrere Benutzer eine Komponente gleichzeitig verwenden können.

Im folgenden Beispiel:

* Die `JSInteropExample`-Komponente enthält mehrere `ListItem`-Komponenten.
* Alle `ListItem`-Komponenten bestehen aus einer Nachricht und einer Schaltfläche.
* Wenn auf die Schaltfläche einer `ListItem`-Komponente geklickt wird, ändert die `UpdateMessage`-Methode des `ListItem`-Elements den Text des Listenelements und blendet die Schaltfläche aus.

`MessageUpdateInvokeHelper.cs`:

```csharp
using System;
using Microsoft.JSInterop;

public class MessageUpdateInvokeHelper
{
    private Action action;

    public MessageUpdateInvokeHelper(Action action)
    {
        this.action = action;
    }

    [JSInvokable("{APP ASSEMBLY}")]
    public void UpdateMessageCaller()
    {
        action.Invoke();
    }
}
```

Der Platzhalter `{APP ASSEMBLY}` ist der App-Assemblyname der App (z. B. `BlazorSample`).

In der clientseitigen JavaScript-Datei:

```javascript
window.updateMessageCallerJS = (dotnetHelper) => {
    dotnetHelper.invokeMethodAsync('{APP ASSEMBLY}', 'UpdateMessageCaller');
    dotnetHelper.dispose();
}
```

Der Platzhalter `{APP ASSEMBLY}` ist der App-Assemblyname der App (z. B. `BlazorSample`).

`Shared/ListItem.razor`:

```razor
@inject IJSRuntime JsRuntime

<li>
    @message
    <button @onclick="InteropCall" style="display:@display">InteropCall</button>
</li>

@code {
    private string message = "Select one of these list item buttons.";
    private string display = "inline-block";
    private MessageUpdateInvokeHelper messageUpdateInvokeHelper;

    protected override void OnInitialized()
    {
        messageUpdateInvokeHelper = new MessageUpdateInvokeHelper(UpdateMessage);
    }

    protected async Task InteropCall()
    {
        await JsRuntime.InvokeVoidAsync("updateMessageCallerJS",
            DotNetObjectReference.Create(messageUpdateInvokeHelper));
    }

    private void UpdateMessage()
    {
        message = "UpdateMessage Called!";
        display = "none";
        StateHasChanged();
    }
}
```

`Pages/JSInteropExample.razor`:

```razor
@page "/JSInteropExample"

<h1>List of components</h1>

<ul>
    <ListItem />
    <ListItem />
    <ListItem />
    <ListItem />
</ul>
```

[!INCLUDE[Share interop code in a class library](~/includes/blazor-share-interop-code.md)]

## <a name="avoid-circular-object-references"></a>Vermeiden von Objektzirkelbezügen

Objekte, die Zirkelbezüge enthalten, können auf dem Client für folgende Vorgänge nicht serialisiert werden:

* .NET-Methodenaufrufe.
* JavaScript-Methodenaufrufe von C#, wenn der Rückgabetyp Zirkelbezüge enthält.

Weitere Informationen finden Sie unter den folgenden Problemen:

* [Zirkelbezüge werden nicht unterstützt, die Zweite (dotnet/aspnetcore #20525)](https://github.com/dotnet/aspnetcore/issues/20525)
* [Vorschlag: Hinzufügen eines Mechanismus zur Verarbeitung von Zirkelbezügen bei der Serialisierung (dotnet/runtime #30820)](https://github.com/dotnet/runtime/issues/30820)

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:blazor/call-javascript-from-dotnet>
* [`InteropComponent.razor`-Beispiel (dotnet/AspNetCore-GitHub-Repository, Releasebranch 3.1)](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
* [Ausführen umfangreicher Datenübertragungen in Blazor Server-Apps](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)
