---
title: Aufrufen von .NET-Methoden von JavaScript-Funktionen in ASP.NET Core Blazor
author: guardrex
description: In diesem Artikel erfahren Sie, wie Sie .NET-Methoden von JavaScript-Funktionen in Blazor-Apps aufrufen.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc, devx-track-js
ms.date: 08/12/2020
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
uid: blazor/call-dotnet-from-javascript
ms.openlocfilehash: e602f29e6932280f4625ade64201ff232e02150d
ms.sourcegitcommit: 610936e4d3507f7f3d467ed7859ab9354ec158ba
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/25/2021
ms.locfileid: "98751633"
---
# <a name="call-net-methods-from-javascript-functions-in-aspnet-core-blazor"></a><span data-ttu-id="842f7-103">Aufrufen von .NET-Methoden von JavaScript-Funktionen in ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="842f7-103">Call .NET methods from JavaScript functions in ASP.NET Core Blazor</span></span>

<span data-ttu-id="842f7-104">Von [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), [Shashikant Rudrawadi](http://wisne.co) und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="842f7-104">By [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), [Shashikant Rudrawadi](http://wisne.co), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="842f7-105">Eine Blazor-App kann JavaScript-Funktionen über .NET-Methoden und .NET-Methoden über JavaScript-Funktionen aufrufen.</span><span class="sxs-lookup"><span data-stu-id="842f7-105">A Blazor app can invoke JavaScript functions from .NET methods and .NET methods from JavaScript functions.</span></span> <span data-ttu-id="842f7-106">Diese Szenarios werden als *JavaScript-Interoperabilität* (*JS Interop*) bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="842f7-106">These scenarios are called *JavaScript interoperability* (*JS interop*).</span></span>

<span data-ttu-id="842f7-107">In diesem Artikel wird das Aufrufen von .NET-Methoden über JavaScript behandelt.</span><span class="sxs-lookup"><span data-stu-id="842f7-107">This article covers invoking .NET methods from JavaScript.</span></span> <span data-ttu-id="842f7-108">Informationen zum Aufrufen von JavaScript-Funktionen über .NET finden Sie unter <xref:blazor/call-javascript-from-dotnet>.</span><span class="sxs-lookup"><span data-stu-id="842f7-108">For information on how to call JavaScript functions from .NET, see <xref:blazor/call-javascript-from-dotnet>.</span></span>

<span data-ttu-id="842f7-109">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="842f7-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

> [!NOTE]
> <span data-ttu-id="842f7-110">Fügen Sie JS-Dateien (`<script>`-Tags) vor dem schließenden `</body>`-Tag in die Datei `wwwroot/index.html` (Blazor WebAssembly) oder die Datei `Pages/_Host.cshtml` (Blazor Server) ein.</span><span class="sxs-lookup"><span data-stu-id="842f7-110">Add JS files (`<script>` tags) before the closing `</body>` tag in the `wwwroot/index.html` file (Blazor WebAssembly) or `Pages/_Host.cshtml` file (Blazor Server).</span></span> <span data-ttu-id="842f7-111">Stellen Sie sicher, dass JS-Dateien mit JS-Interopmethoden vor JS-Dateien des Blazor-Frameworks eingeschlossen werden.</span><span class="sxs-lookup"><span data-stu-id="842f7-111">Ensure that JS files with JS interop methods are included before Blazor framework JS files.</span></span>

## <a name="static-net-method-call"></a><span data-ttu-id="842f7-112">Statischer .NET-Methodenaufruf</span><span class="sxs-lookup"><span data-stu-id="842f7-112">Static .NET method call</span></span>

<span data-ttu-id="842f7-113">Um eine statische .NET-Methode über JavaScript aufzurufen, verwenden Sie die Funktionen `DotNet.invokeMethod` oder `DotNet.invokeMethodAsync`.</span><span class="sxs-lookup"><span data-stu-id="842f7-113">To invoke a static .NET method from JavaScript, use the `DotNet.invokeMethod` or `DotNet.invokeMethodAsync` functions.</span></span> <span data-ttu-id="842f7-114">Übergeben Sie den Bezeichner der statischen Methode, die Sie aufrufen möchten, den Namen der Assembly, die die Funktion enthält, und alle Argumente.</span><span class="sxs-lookup"><span data-stu-id="842f7-114">Pass in the identifier of the static method you wish to call, the name of the assembly containing the function, and any arguments.</span></span> <span data-ttu-id="842f7-115">Die asynchrone Version wird bevorzugt, um Blazor Server-Szenarien zu unterstützen.</span><span class="sxs-lookup"><span data-stu-id="842f7-115">The asynchronous version is preferred to support Blazor Server scenarios.</span></span> <span data-ttu-id="842f7-116">Die .NET-Methode muss öffentlich und statisch sein sowie das Attribut [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) aufweisen.</span><span class="sxs-lookup"><span data-stu-id="842f7-116">The .NET method must be public, static, and have the [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) attribute.</span></span> <span data-ttu-id="842f7-117">Das Aufrufen offener generischer Methoden wird derzeit nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="842f7-117">Calling open generic methods isn't currently supported.</span></span>

<span data-ttu-id="842f7-118">Die Beispiel-App enthält eine C#-Methode zur Rückgabe eines `int`-Arrays.</span><span class="sxs-lookup"><span data-stu-id="842f7-118">The sample app includes a C# method to return an `int` array.</span></span> <span data-ttu-id="842f7-119">Das [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute)-Attribut wird auf die Methode angewendet.</span><span class="sxs-lookup"><span data-stu-id="842f7-119">The [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) attribute is applied to the method.</span></span>

<span data-ttu-id="842f7-120">`Pages/JsInterop.razor`:</span><span class="sxs-lookup"><span data-stu-id="842f7-120">`Pages/JsInterop.razor`:</span></span>

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

<span data-ttu-id="842f7-121">Das dem Client zugestellte JavaScript ruft die C# .NET-Methode auf.</span><span class="sxs-lookup"><span data-stu-id="842f7-121">JavaScript served to the client invokes the C# .NET method.</span></span>

<span data-ttu-id="842f7-122">`wwwroot/exampleJsInterop.js`:</span><span class="sxs-lookup"><span data-stu-id="842f7-122">`wwwroot/exampleJsInterop.js`:</span></span>

[!code-javascript[](./common/samples/5.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=8-14)]

<span data-ttu-id="842f7-123">Überprüfen Sie bei Auswahl der Schaltfläche **`Trigger .NET static method ReturnArrayAsync`** die Konsolenausgabe in den Web Developer Tools des Browsers.</span><span class="sxs-lookup"><span data-stu-id="842f7-123">When the **`Trigger .NET static method ReturnArrayAsync`** button is selected, examine the console output in the browser's web developer tools.</span></span>

<span data-ttu-id="842f7-124">Die Konsolenausgabe lautet:</span><span class="sxs-lookup"><span data-stu-id="842f7-124">The console output is:</span></span>

```console
Array(4) [ 1, 2, 3, 4 ]
```

<span data-ttu-id="842f7-125">Der vierte Arraywert wird in das von `ReturnArrayAsync` zurückgegebene Array (`data.push(4);`) gepusht.</span><span class="sxs-lookup"><span data-stu-id="842f7-125">The fourth array value is pushed to the array (`data.push(4);`) returned by `ReturnArrayAsync`.</span></span>

<span data-ttu-id="842f7-126">Standardmäßig ist der Methodenbezeichner der Methodenname, aber Sie können mit dem Attributkonstruktor [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) einen anderen Bezeichner angeben:</span><span class="sxs-lookup"><span data-stu-id="842f7-126">By default, the method identifier is the method name, but you can specify a different identifier using the [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) attribute constructor:</span></span>

```csharp
@code {
    [JSInvokable("DifferentMethodName")]
    public static Task<int[]> ReturnArrayAsync()
    {
        return Task.FromResult(new int[] { 1, 2, 3 });
    }
}
```

<span data-ttu-id="842f7-127">In der clientseitigen JavaScript-Datei:</span><span class="sxs-lookup"><span data-stu-id="842f7-127">In the client-side JavaScript file:</span></span>

```javascript
returnArrayAsyncJs: function () {
  DotNet.invokeMethodAsync('{APP ASSEMBLY}', 'DifferentMethodName')
    .then(data => {
      data.push(4);
      console.log(data);
    });
}
```

<span data-ttu-id="842f7-128">Der Platzhalter `{APP ASSEMBLY}` ist der App-Assemblyname der App (z. B. `BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="842f7-128">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

## <a name="instance-method-call"></a><span data-ttu-id="842f7-129">Aufruf der Instanzmethode</span><span class="sxs-lookup"><span data-stu-id="842f7-129">Instance method call</span></span>

<span data-ttu-id="842f7-130">Sie können auch .NET-Instanzmethoden von JavaScript aus aufrufen.</span><span class="sxs-lookup"><span data-stu-id="842f7-130">You can also call .NET instance methods from JavaScript.</span></span> <span data-ttu-id="842f7-131">So rufen Sie .NET-Instanzmethoden von JavaScript aus auf</span><span class="sxs-lookup"><span data-stu-id="842f7-131">To invoke a .NET instance method from JavaScript:</span></span>

* <span data-ttu-id="842f7-132">Übergeben Sie die .NET-Instanz durch Verweis auf JavaScript:</span><span class="sxs-lookup"><span data-stu-id="842f7-132">Pass the .NET instance by reference to JavaScript:</span></span>
  * <span data-ttu-id="842f7-133">Führen Sie einen statischen Aufruf an <xref:Microsoft.JSInterop.DotNetObjectReference.Create%2A?displayProperty=nameWithType> durch.</span><span class="sxs-lookup"><span data-stu-id="842f7-133">Make a static call to <xref:Microsoft.JSInterop.DotNetObjectReference.Create%2A?displayProperty=nameWithType>.</span></span>
  * <span data-ttu-id="842f7-134">Umschließen Sie die Instanz mit einer <xref:Microsoft.JSInterop.DotNetObjectReference>-Instanz, und rufen Sie <xref:Microsoft.JSInterop.DotNetObjectReference.Create%2A> für die <xref:Microsoft.JSInterop.DotNetObjectReference>-Instanz auf.</span><span class="sxs-lookup"><span data-stu-id="842f7-134">Wrap the instance in a <xref:Microsoft.JSInterop.DotNetObjectReference> instance and call <xref:Microsoft.JSInterop.DotNetObjectReference.Create%2A> on the <xref:Microsoft.JSInterop.DotNetObjectReference> instance.</span></span> <span data-ttu-id="842f7-135">Entsorgen Sie <xref:Microsoft.JSInterop.DotNetObjectReference>-Objekte (ein Beispiel folgt später in diesem Abschnitt).</span><span class="sxs-lookup"><span data-stu-id="842f7-135">Dispose of <xref:Microsoft.JSInterop.DotNetObjectReference> objects (an example appears later in this section).</span></span>
* <span data-ttu-id="842f7-136">Rufen Sie .NET-Instanzmethoden für die Instanz mit den Funktionen `invokeMethod` oder `invokeMethodAsync` auf.</span><span class="sxs-lookup"><span data-stu-id="842f7-136">Invoke .NET instance methods on the instance using the `invokeMethod` or `invokeMethodAsync` functions.</span></span> <span data-ttu-id="842f7-137">Die .NET-Instanz kann auch als Argument übergeben werden, wenn andere .NET-Methoden von JavaScript aus aufgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="842f7-137">The .NET instance can also be passed as an argument when invoking other .NET methods from JavaScript.</span></span>

> [!NOTE]
> <span data-ttu-id="842f7-138">Die Beispiel-App protokolliert Nachrichten an die clientseitige Konsole.</span><span class="sxs-lookup"><span data-stu-id="842f7-138">The sample app logs messages to the client-side console.</span></span> <span data-ttu-id="842f7-139">Für die folgenden Beispiele, die durch die Beispiel-App veranschaulicht werden, untersuchen Sie die Konsolenausgabe des Browsers in den Entwicklertools des Browsers.</span><span class="sxs-lookup"><span data-stu-id="842f7-139">For the following examples demonstrated by the sample app, examine the browser's console output in the browser's developer tools.</span></span>

<span data-ttu-id="842f7-140">Bei Auswahl der Schaltfläche **`Trigger .NET instance method HelloHelper.SayHello`** wird `ExampleJsInterop.CallHelloHelperSayHello` aufgerufen und der Name `Blazor` an die Methode übergeben.</span><span class="sxs-lookup"><span data-stu-id="842f7-140">When the **`Trigger .NET instance method HelloHelper.SayHello`** button is selected, `ExampleJsInterop.CallHelloHelperSayHello` is called and passes a name, `Blazor`, to the method.</span></span>

<span data-ttu-id="842f7-141">`Pages/JsInterop.razor`:</span><span class="sxs-lookup"><span data-stu-id="842f7-141">`Pages/JsInterop.razor`:</span></span>

```razor
<button type="button" class="btn btn-primary" @onclick="TriggerNetInstanceMethod">
    Trigger .NET instance method HelloHelper.SayHello
</button>

@code {
    public async Task TriggerNetInstanceMethod()
    {
        var exampleJsInterop = new ExampleJsInterop(JS);
        await exampleJsInterop.CallHelloHelperSayHello("Blazor");
    }
}
```

<span data-ttu-id="842f7-142">`CallHelloHelperSayHello` ruft die JavaScript-Funktion `sayHello` mit einer neuen Instanz von `HelloHelper` auf.</span><span class="sxs-lookup"><span data-stu-id="842f7-142">`CallHelloHelperSayHello` invokes the JavaScript function `sayHello` with a new instance of `HelloHelper`.</span></span>

<span data-ttu-id="842f7-143">`JsInteropClasses/ExampleJsInterop.cs`:</span><span class="sxs-lookup"><span data-stu-id="842f7-143">`JsInteropClasses/ExampleJsInterop.cs`:</span></span>

[!code-csharp[](./common/samples/5.x/BlazorWebAssemblySample/JsInteropClasses/ExampleJsInterop.cs?name=snippet1&highlight=11-18)]

<span data-ttu-id="842f7-144">`wwwroot/exampleJsInterop.js`:</span><span class="sxs-lookup"><span data-stu-id="842f7-144">`wwwroot/exampleJsInterop.js`:</span></span>

[!code-javascript[](./common/samples/5.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=15-18)]

<span data-ttu-id="842f7-145">Der Name wird an den Konstruktor von `HelloHelper` übergeben, der die Eigenschaft `HelloHelper.Name` festlegt.</span><span class="sxs-lookup"><span data-stu-id="842f7-145">The name is passed to `HelloHelper`'s constructor, which sets the `HelloHelper.Name` property.</span></span> <span data-ttu-id="842f7-146">Wenn die JavaScript-Funktion `sayHello` ausgeführt wird, gibt `HelloHelper.SayHello` die Nachricht `Hello, {Name}!` zurück, die von der JavaScript-Funktion in die Konsole geschrieben wird.</span><span class="sxs-lookup"><span data-stu-id="842f7-146">When the JavaScript function `sayHello` is executed, `HelloHelper.SayHello` returns the `Hello, {Name}!` message, which is written to the console by the JavaScript function.</span></span>

<span data-ttu-id="842f7-147">`JsInteropClasses/HelloHelper.cs`:</span><span class="sxs-lookup"><span data-stu-id="842f7-147">`JsInteropClasses/HelloHelper.cs`:</span></span>

[!code-csharp[](./common/samples/5.x/BlazorWebAssemblySample/JsInteropClasses/HelloHelper.cs?name=snippet1&highlight=5,10-11)]

<span data-ttu-id="842f7-148">Konsolenausgabe in den Webentwicklertools des Browsers:</span><span class="sxs-lookup"><span data-stu-id="842f7-148">Console output in the browser's web developer tools:</span></span>

```console
Hello, Blazor!
```

<span data-ttu-id="842f7-149">Verwenden Sie einen der folgenden Ansätze, um einen Arbeitsspeicherverlust zu vermeiden und die Garbage Collection für eine Komponente zu ermöglichen, die ein <xref:Microsoft.JSInterop.DotNetObjectReference> erstellt:</span><span class="sxs-lookup"><span data-stu-id="842f7-149">To avoid a memory leak and allow garbage collection on a component that creates a <xref:Microsoft.JSInterop.DotNetObjectReference>, adopt one of the following approaches:</span></span>

* <span data-ttu-id="842f7-150">Löschen Sie das Objekt in der Klasse, die die <xref:Microsoft.JSInterop.DotNetObjectReference>-Instanz erstellt hat:</span><span class="sxs-lookup"><span data-stu-id="842f7-150">Dispose of the object in the class that created the <xref:Microsoft.JSInterop.DotNetObjectReference> instance:</span></span>

  ```csharp
  public class ExampleJsInterop : IDisposable
  {
      private readonly IJSRuntime js;
      private DotNetObjectReference<HelloHelper> objRef;

      public ExampleJsInterop(IJSRuntime js)
      {
          this.js = js;
      }

      public ValueTask<string> CallHelloHelperSayHello(string name)
      {
          objRef = DotNetObjectReference.Create(new HelloHelper(name));

          return js.InvokeAsync<string>(
              "exampleJsFunctions.sayHello",
              objRef);
      }

      public void Dispose()
      {
          objRef?.Dispose();
      }
  }
  ```

  <span data-ttu-id="842f7-151">Das vorhergehende Muster, das in der Klasse `ExampleJsInterop` gezeigt wird, kann auch in einer Komponente implementiert werden:</span><span class="sxs-lookup"><span data-stu-id="842f7-151">The preceding pattern shown in the `ExampleJsInterop` class can also be implemented in a component:</span></span>

  ```razor
  @page "/JSInteropComponent"
  @using {APP ASSEMBLY}.JsInteropClasses
  @implements IDisposable
  @inject IJSRuntime JS

  <h1>JavaScript Interop</h1>

  <button type="button" class="btn btn-primary" @onclick="TriggerNetInstanceMethod">
      Trigger .NET instance method HelloHelper.SayHello
  </button>

  @code {
      private DotNetObjectReference<HelloHelper> objRef;

      public async Task TriggerNetInstanceMethod()
      {
          objRef = DotNetObjectReference.Create(new HelloHelper("Blazor"));

          await JS.InvokeAsync<string>(
              "exampleJsFunctions.sayHello",
              objRef);
      }

      public void Dispose()
      {
          objRef?.Dispose();
      }
  }
  ```
  
  <span data-ttu-id="842f7-152">Der Platzhalter `{APP ASSEMBLY}` ist der App-Assemblyname der App (z. B. `BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="842f7-152">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

* <span data-ttu-id="842f7-153">Wenn die Komponente oder Klasse die <xref:Microsoft.JSInterop.DotNetObjectReference>-Instanz nicht löscht, löschen Sie das Objekt im Client, indem Sie `.dispose()` aufrufen:</span><span class="sxs-lookup"><span data-stu-id="842f7-153">When the component or class doesn't dispose of the <xref:Microsoft.JSInterop.DotNetObjectReference>, dispose of the object on the client by calling `.dispose()`:</span></span>

  ```javascript
  window.myFunction = (dotnetHelper) => {
    dotnetHelper.invokeMethodAsync('{APP ASSEMBLY}', 'MyMethod');
    dotnetHelper.dispose();
  }
  ```

## <a name="component-instance-method-call"></a><span data-ttu-id="842f7-154">Aufrufen der Komponenteninstanzmethode</span><span class="sxs-lookup"><span data-stu-id="842f7-154">Component instance method call</span></span>

<span data-ttu-id="842f7-155">So rufen Sie die .NET-Methoden einer Komponente auf:</span><span class="sxs-lookup"><span data-stu-id="842f7-155">To invoke a component's .NET methods:</span></span>

* <span data-ttu-id="842f7-156">Verwenden Sie die `invokeMethod`- oder `invokeMethodAsync`-Funktion, um einen statischen Methodenaufruf an die Komponente auszuführen.</span><span class="sxs-lookup"><span data-stu-id="842f7-156">Use the `invokeMethod` or `invokeMethodAsync` function to make a static method call to the component.</span></span>
* <span data-ttu-id="842f7-157">Die statische Methode der Komponente umschließt den Aufruf der Instanzmethode als aufgerufene <xref:System.Action>.</span><span class="sxs-lookup"><span data-stu-id="842f7-157">The component's static method wraps the call to its instance method as an invoked <xref:System.Action>.</span></span>

> [!NOTE]
> <span data-ttu-id="842f7-158">Verwenden Sie für Blazor Server-Apps, bei denen mehrere Benutzer gleichzeitig dieselbe Komponente verwenden können, eine Hilfsklasse zum Aufrufen von Instanzmethoden.</span><span class="sxs-lookup"><span data-stu-id="842f7-158">For Blazor Server apps, where several users might be concurrently using the same component, use a helper class to invoke instance methods.</span></span>
>
> <span data-ttu-id="842f7-159">Weitere Informationen finden Sie im Abschnitt [Komponenteninstanzmethode-Hilfsklasse](#component-instance-method-helper-class).</span><span class="sxs-lookup"><span data-stu-id="842f7-159">For more information, see the [Component instance method helper class](#component-instance-method-helper-class) section.</span></span>

<span data-ttu-id="842f7-160">In der clientseitigen JavaScript-Datei:</span><span class="sxs-lookup"><span data-stu-id="842f7-160">In the client-side JavaScript:</span></span>

```javascript
function updateMessageCallerJS() {
  DotNet.invokeMethodAsync('{APP ASSEMBLY}', 'UpdateMessageCaller');
}
```

<span data-ttu-id="842f7-161">Der Platzhalter `{APP ASSEMBLY}` ist der App-Assemblyname der App (z. B. `BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="842f7-161">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

<span data-ttu-id="842f7-162">`Pages/JSInteropComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="842f7-162">`Pages/JSInteropComponent.razor`:</span></span>

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

<span data-ttu-id="842f7-163">So übergeben Sie Argumente an die Instanzmethode:</span><span class="sxs-lookup"><span data-stu-id="842f7-163">To pass arguments to the instance method:</span></span>

* <span data-ttu-id="842f7-164">Fügen Sie dem JS-Methodenaufruf Parameter hinzu.</span><span class="sxs-lookup"><span data-stu-id="842f7-164">Add parameters to the JS method invocation.</span></span> <span data-ttu-id="842f7-165">Im folgenden Beispiel wird ein Name an die Methode übergeben.</span><span class="sxs-lookup"><span data-stu-id="842f7-165">In the following example, a name is passed to the method.</span></span> <span data-ttu-id="842f7-166">Der Liste können nach Bedarf weitere Parameter hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="842f7-166">Additional parameters can be added to the list as needed.</span></span>

  ```javascript
  function updateMessageCallerJS(name) {
    DotNet.invokeMethodAsync('{APP ASSEMBLY}', 'UpdateMessageCaller', name);
  }
  ```
  
  <span data-ttu-id="842f7-167">Der Platzhalter `{APP ASSEMBLY}` ist der App-Assemblyname der App (z. B. `BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="842f7-167">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

* <span data-ttu-id="842f7-168">Geben Sie die richtigen Typen für die <xref:System.Action> für die Parameter an.</span><span class="sxs-lookup"><span data-stu-id="842f7-168">Provide the correct types to the <xref:System.Action> for the parameters.</span></span> <span data-ttu-id="842f7-169">Geben Sie die Parameterliste für die C#-Methoden an.</span><span class="sxs-lookup"><span data-stu-id="842f7-169">Provide the parameter list to the C# methods.</span></span> <span data-ttu-id="842f7-170">Rufen Sie die <xref:System.Action> (`UpdateMessage`) mit den Parametern (`action.Invoke(name)`) auf.</span><span class="sxs-lookup"><span data-stu-id="842f7-170">Invoke the <xref:System.Action> (`UpdateMessage`) with the parameters (`action.Invoke(name)`).</span></span>

  <span data-ttu-id="842f7-171">`Pages/JSInteropComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="842f7-171">`Pages/JSInteropComponent.razor`:</span></span>

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

  <span data-ttu-id="842f7-172">Gibt `message` aus, wenn die Schaltfläche **Call JS Method** (JS-Methode aufrufen) ausgewählt wird:</span><span class="sxs-lookup"><span data-stu-id="842f7-172">Output `message` when the **Call JS Method** button is selected:</span></span>

  ```
  Sarah Jane, UpdateMessage Called!
  ```

## <a name="component-instance-method-helper-class"></a><span data-ttu-id="842f7-173">Komponenteninstanzmethode-Hilfsklasse</span><span class="sxs-lookup"><span data-stu-id="842f7-173">Component instance method helper class</span></span>

<span data-ttu-id="842f7-174">Die-Hilfsklasse wird verwendet, um eine Instanzmethode als <xref:System.Action>aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="842f7-174">The helper class is used to invoke an instance method as an <xref:System.Action>.</span></span> <span data-ttu-id="842f7-175">Hilfsklassen sind in folgenden Fällen nützlich:</span><span class="sxs-lookup"><span data-stu-id="842f7-175">Helper classes are useful when:</span></span>

* <span data-ttu-id="842f7-176">Mehrere Komponenten desselben Typs werden auf derselben Seite gerendert.</span><span class="sxs-lookup"><span data-stu-id="842f7-176">Several components of the same type are rendered on the same page.</span></span>
* <span data-ttu-id="842f7-177">Es wird eine Blazor Server-App verwendet, in der mehrere Benutzer eine Komponente gleichzeitig verwenden können.</span><span class="sxs-lookup"><span data-stu-id="842f7-177">A Blazor Server app is used, where multiple users might be using a component concurrently.</span></span>

<span data-ttu-id="842f7-178">Im folgenden Beispiel:</span><span class="sxs-lookup"><span data-stu-id="842f7-178">In the following example:</span></span>

* <span data-ttu-id="842f7-179">Die `JSInteropExample`-Komponente enthält mehrere `ListItem`-Komponenten.</span><span class="sxs-lookup"><span data-stu-id="842f7-179">The `JSInteropExample` component contains several `ListItem` components.</span></span>
* <span data-ttu-id="842f7-180">Alle `ListItem`-Komponenten bestehen aus einer Nachricht und einer Schaltfläche.</span><span class="sxs-lookup"><span data-stu-id="842f7-180">Each `ListItem` component is composed of a message and a button.</span></span>
* <span data-ttu-id="842f7-181">Wenn auf die Schaltfläche einer `ListItem`-Komponente geklickt wird, ändert die `UpdateMessage`-Methode des `ListItem`-Elements den Text des Listenelements und blendet die Schaltfläche aus.</span><span class="sxs-lookup"><span data-stu-id="842f7-181">When a `ListItem` component button is selected, that `ListItem`'s `UpdateMessage` method changes the list item text and hides the button.</span></span>

<span data-ttu-id="842f7-182">`MessageUpdateInvokeHelper.cs`:</span><span class="sxs-lookup"><span data-stu-id="842f7-182">`MessageUpdateInvokeHelper.cs`:</span></span>

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

<span data-ttu-id="842f7-183">Der Platzhalter `{APP ASSEMBLY}` ist der App-Assemblyname der App (z. B. `BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="842f7-183">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

<span data-ttu-id="842f7-184">In der clientseitigen JavaScript-Datei:</span><span class="sxs-lookup"><span data-stu-id="842f7-184">In the client-side JavaScript:</span></span>

```javascript
window.updateMessageCallerJS = (dotnetHelper) => {
    dotnetHelper.invokeMethodAsync('{APP ASSEMBLY}', 'UpdateMessageCaller');
    dotnetHelper.dispose();
}
```

<span data-ttu-id="842f7-185">Der Platzhalter `{APP ASSEMBLY}` ist der App-Assemblyname der App (z. B. `BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="842f7-185">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

<span data-ttu-id="842f7-186">`Shared/ListItem.razor`:</span><span class="sxs-lookup"><span data-stu-id="842f7-186">`Shared/ListItem.razor`:</span></span>

```razor
@inject IJSRuntime JS

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
        await JS.InvokeVoidAsync("updateMessageCallerJS",
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

<span data-ttu-id="842f7-187">`Pages/JSInteropExample.razor`:</span><span class="sxs-lookup"><span data-stu-id="842f7-187">`Pages/JSInteropExample.razor`:</span></span>

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

[!INCLUDE[](~/blazor/includes/share-interop-code.md)]

## <a name="avoid-circular-object-references"></a><span data-ttu-id="842f7-188">Vermeiden von Objektzirkelbezügen</span><span class="sxs-lookup"><span data-stu-id="842f7-188">Avoid circular object references</span></span>

<span data-ttu-id="842f7-189">Objekte, die Zirkelbezüge enthalten, können auf dem Client für folgende Vorgänge nicht serialisiert werden:</span><span class="sxs-lookup"><span data-stu-id="842f7-189">Objects that contain circular references can't be serialized on the client for either:</span></span>

* <span data-ttu-id="842f7-190">.NET-Methodenaufrufe.</span><span class="sxs-lookup"><span data-stu-id="842f7-190">.NET method calls.</span></span>
* <span data-ttu-id="842f7-191">JavaScript-Methodenaufrufe von C#, wenn der Rückgabetyp Zirkelbezüge enthält.</span><span class="sxs-lookup"><span data-stu-id="842f7-191">JavaScript method calls from C# when the return type has circular references.</span></span>

<span data-ttu-id="842f7-192">Weitere Informationen finden Sie unter den folgenden Problemen:</span><span class="sxs-lookup"><span data-stu-id="842f7-192">For more information, see the following issues:</span></span>

* [<span data-ttu-id="842f7-193">Zirkelbezüge werden nicht unterstützt, die Zweite (dotnet/aspnetcore #20525)</span><span class="sxs-lookup"><span data-stu-id="842f7-193">Circular references are not supported, take two (dotnet/aspnetcore #20525)</span></span>](https://github.com/dotnet/aspnetcore/issues/20525)
* [<span data-ttu-id="842f7-194">Vorschlag: Hinzufügen eines Mechanismus zur Verarbeitung von Zirkelbezügen bei der Serialisierung (dotnet/runtime #30820)</span><span class="sxs-lookup"><span data-stu-id="842f7-194">Proposal: Add mechanism to handle circular references when serializing (dotnet/runtime #30820)</span></span>](https://github.com/dotnet/runtime/issues/30820)

## <a name="size-limits-on-js-interop-calls"></a><span data-ttu-id="842f7-195">Größenbeschränkungen bei JS-Interop-Aufrufen</span><span class="sxs-lookup"><span data-stu-id="842f7-195">Size limits on JS interop calls</span></span>

<span data-ttu-id="842f7-196">In Blazor WebAssembly gibt das Framework keine Beschränkungen hinsichtlich der Größe von JS Interop-Eingaben und -Ausgaben vor.</span><span class="sxs-lookup"><span data-stu-id="842f7-196">In Blazor WebAssembly, the framework doesn't impose a limit on the size of JS interop inputs and outputs.</span></span>

<span data-ttu-id="842f7-197">In Blazor Server werden JS Interop-Aufrufe hinsichtlich ihrer Größe durch den Maximalwert eingehender SignalR-Nachrichten beschränkt, die für Hubmethoden zulässig sind. Dies wird von <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize?displayProperty=nameWithType> erzwungen. Der Standardwert beträgt 32 KB.</span><span class="sxs-lookup"><span data-stu-id="842f7-197">In Blazor Server, JS interop calls are limited in size by the maximum incoming SignalR message size permitted for hub methods, which is enforced by <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize?displayProperty=nameWithType> (default: 32 KB).</span></span> <span data-ttu-id="842f7-198">SignalR-Nachrichten von JS an .NET, die größer als <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize> sind, führen zu einem Fehler.</span><span class="sxs-lookup"><span data-stu-id="842f7-198">JS to .NET SignalR messages larger than <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize> throw an error.</span></span> <span data-ttu-id="842f7-199">Das Framework beinhaltet keine Beschränkungen hinsichtlich der Größe einer SignalR-Nachricht vom Hub an einen Client.</span><span class="sxs-lookup"><span data-stu-id="842f7-199">The framework doesn't impose a limit on the size of a SignalR message from the hub to a client.</span></span>

<span data-ttu-id="842f7-200">Wenn die SignalR-Protokollierung nicht auf [Debuggen](xref:Microsoft.Extensions.Logging.LogLevel) oder [Überwachung](xref:Microsoft.Extensions.Logging.LogLevel) festgelegt ist, wird ein Fehler zur Nachrichtengröße nur in der Konsole für Entwicklertools des Browsers angezeigt:</span><span class="sxs-lookup"><span data-stu-id="842f7-200">When SignalR logging isn't set to [Debug](xref:Microsoft.Extensions.Logging.LogLevel) or [Trace](xref:Microsoft.Extensions.Logging.LogLevel), a message size error only appears in the browser's developer tools console:</span></span>

> <span data-ttu-id="842f7-201">Fehler: Connection disconnected with error „Error: Server returned an error on close: Connection closed with an error.“ (Die Verbindung wurde durch den folgenden Fehler getrennt: „Der Server hat beim Schließen einen Fehler zurückgegeben: Die Verbindung wurde durch einen Fehler beendet.“)</span><span class="sxs-lookup"><span data-stu-id="842f7-201">Error: Connection disconnected with error 'Error: Server returned an error on close: Connection closed with an error.'.</span></span>

<span data-ttu-id="842f7-202">Wenn die [serverseitige Protokollierung in SignalR](xref:signalr/diagnostics#server-side-logging) auf [Debuggen](xref:Microsoft.Extensions.Logging.LogLevel) oder [Überwachung](xref:Microsoft.Extensions.Logging.LogLevel) festgelegt ist, tritt bei der serverseitigen Protokollierung eine <xref:System.IO.InvalidDataException>-Ausnahme für einen Fehler in Bezug auf die Nachrichtengröße auf.</span><span class="sxs-lookup"><span data-stu-id="842f7-202">When [SignalR server-side logging](xref:signalr/diagnostics#server-side-logging) is set to [Debug](xref:Microsoft.Extensions.Logging.LogLevel) or [Trace](xref:Microsoft.Extensions.Logging.LogLevel), server-side logging surfaces an <xref:System.IO.InvalidDataException> for a message size error.</span></span>

<span data-ttu-id="842f7-203">`appsettings.Development.json`:</span><span class="sxs-lookup"><span data-stu-id="842f7-203">`appsettings.Development.json`:</span></span>

```json
{
  "DetailedErrors": true,
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Warning",
      "Microsoft.Hosting.Lifetime": "Information",
      "Microsoft.AspNetCore.SignalR": "Debug"
    }
  }
}
```

> <span data-ttu-id="842f7-204">System.IO.InvalidDataException: The maximum message size of 32768B was exceeded.</span><span class="sxs-lookup"><span data-stu-id="842f7-204">System.IO.InvalidDataException: The maximum message size of 32768B was exceeded.</span></span> <span data-ttu-id="842f7-205">The message size can be configured in AddHubOptions. (System.IO.InvalidDataException: Die maximale Nachrichtengröße von 32.768 Byte wurde überschritten. Die Nachrichtengröße kann unter AddHubOptions konfiguriert werden.)</span><span class="sxs-lookup"><span data-stu-id="842f7-205">The message size can be configured in AddHubOptions.</span></span>

<span data-ttu-id="842f7-206">Erhöhen Sie den Grenzwert, indem Sie <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize> in `Startup.ConfigureServices` festlegen.</span><span class="sxs-lookup"><span data-stu-id="842f7-206">Increase the limit by setting <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="842f7-207">Im folgenden Beispiel wird die maximale Größe eingehender Nachrichten auf 64 KB (64 × 1.024) festgelegt:</span><span class="sxs-lookup"><span data-stu-id="842f7-207">The following example sets the maximum receive message size to 64 KB (64 \* 1024):</span></span>

```csharp
services.AddServerSideBlazor()
   .AddHubOptions(options => options.MaximumReceiveMessageSize = 64 * 1024);
```

<span data-ttu-id="842f7-208">Wenn Sie den Grenzwert für die Größe eingehender Nachrichten von SignalR erhöhen, verbrauchen Sie auch mehr Serverressourcen und setzen den Server einem erhöhten Risiko durch böswillige Benutzer aus.</span><span class="sxs-lookup"><span data-stu-id="842f7-208">Increasing the SignalR incoming message size limit comes at the cost of requiring more server resources, and it exposes the server to increased risks from a malicious user.</span></span> <span data-ttu-id="842f7-209">Darüber hinaus kann das Lesen sehr großer Inhalte in den Arbeitsspeicher als Zeichenfolgen oder Bytearrays zu Zuordnungen führen, die vom Garbage Collector nur schlecht verarbeitet werden können. Dies kann zu zusätzlichen Leistungseinbußen führen.</span><span class="sxs-lookup"><span data-stu-id="842f7-209">Additionally, reading a large amount of content in to memory as strings or byte arrays can also result in allocations that work poorly with the garbage collector, resulting in additional performance penalties.</span></span>

<span data-ttu-id="842f7-210">Eine Möglichkeit zum Lesen großer Mengen an Nutzdaten besteht darin, den Inhalt in kleineren Blöcken zu senden und die Nutzdaten als <xref:System.IO.Stream> zu verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="842f7-210">One option for reading large payloads is to send the content in smaller chunks and process the payload as a <xref:System.IO.Stream>.</span></span> <span data-ttu-id="842f7-211">Diesen Ansatz können Sie anwenden, wenn große JSON-Payloads gelesen werden oder die Daten in JavaScript als unformatierte Bytes verfügbar sind.</span><span class="sxs-lookup"><span data-stu-id="842f7-211">This can be used when reading large JSON payloads or if data is available in JavaScript as raw bytes.</span></span> <span data-ttu-id="842f7-212">Ein Beispiel für das Senden großer binärer Payloads in Blazor Server, bei dem ähnliche Techniken wie die `InputFile`-Komponente verwendet werden, finden Sie unter der [Beispiel-App BinarySubmit](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/BinarySubmit).</span><span class="sxs-lookup"><span data-stu-id="842f7-212">For an example that demonstrates sending large binary payloads in Blazor Server that uses techniques similar to the `InputFile` component, see the [Binary Submit sample app](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/BinarySubmit).</span></span>

<span data-ttu-id="842f7-213">Beachten Sie die folgenden Anleitungen, wenn Sie Code zum Übertragen großer Datenmengen zwischen JavaScript und Blazor entwickeln:</span><span class="sxs-lookup"><span data-stu-id="842f7-213">Consider the following guidance when developing code that transfers a large amount of data between JavaScript and Blazor:</span></span>

* <span data-ttu-id="842f7-214">Segmentieren Sie die Daten in kleinere Teile, und senden Sie die Datensegmente sequenziell, bis alle Daten vom Server empfangen wurden.</span><span class="sxs-lookup"><span data-stu-id="842f7-214">Slice the data into smaller pieces, and send the data segments sequentially until all of the data is received by the server.</span></span>
* <span data-ttu-id="842f7-215">Ordnen Sie in JavaScript- und C#-Code keine großen Objekte zu.</span><span class="sxs-lookup"><span data-stu-id="842f7-215">Don't allocate large objects in JavaScript and C# code.</span></span>
* <span data-ttu-id="842f7-216">Blockieren Sie den hauptsächlichen Benutzeroberflächenthread nicht für lange Zeiträume, wenn Sie Daten senden oder empfangen.</span><span class="sxs-lookup"><span data-stu-id="842f7-216">Don't block the main UI thread for long periods when sending or receiving data.</span></span>
* <span data-ttu-id="842f7-217">Geben Sie belegten Arbeitsspeicher frei, wenn der Prozess abgeschlossen oder abgebrochen wird.</span><span class="sxs-lookup"><span data-stu-id="842f7-217">Free any memory consumed when the process is completed or cancelled.</span></span>
* <span data-ttu-id="842f7-218">Erzwingen Sie die folgenden zusätzlichen Anforderungen aus Sicherheitsgründen:</span><span class="sxs-lookup"><span data-stu-id="842f7-218">Enforce the following additional requirements for security purposes:</span></span>
  * <span data-ttu-id="842f7-219">Deklarieren Sie die maximale Datei- oder Datengröße, die übermittelt werden kann.</span><span class="sxs-lookup"><span data-stu-id="842f7-219">Declare the maximum file or data size that can be passed.</span></span>
  * <span data-ttu-id="842f7-220">Deklarieren Sie die minimale Uploadrate vom Client an den Server.</span><span class="sxs-lookup"><span data-stu-id="842f7-220">Declare the minimum upload rate from the client to the server.</span></span>
* <span data-ttu-id="842f7-221">Nachdem die Daten vom Server empfangen wurden, ist mit den Daten Folgendes möglich:</span><span class="sxs-lookup"><span data-stu-id="842f7-221">After the data is received by the server, the data can be:</span></span>
  * <span data-ttu-id="842f7-222">Sie können temporär in einem Speicherpuffer gespeichert werden, bis alle Segmente gesammelt wurden.</span><span class="sxs-lookup"><span data-stu-id="842f7-222">Temporarily stored in a memory buffer until all of the segments are collected.</span></span>
  * <span data-ttu-id="842f7-223">Sie können sofort verarbeitet werden.</span><span class="sxs-lookup"><span data-stu-id="842f7-223">Consumed immediately.</span></span> <span data-ttu-id="842f7-224">Beispielsweise können die Daten sofort in einer Datenbank gespeichert oder auf den Datenträger geschrieben werden, wenn die einzelnen Segmente empfangen werden.</span><span class="sxs-lookup"><span data-stu-id="842f7-224">For example, the data can be stored immediately in a database or written to disk as each segment is received.</span></span>

## <a name="js-modules"></a><span data-ttu-id="842f7-225">JS-Module</span><span class="sxs-lookup"><span data-stu-id="842f7-225">JS modules</span></span>

<span data-ttu-id="842f7-226">Für die JS-Isolation funktioniert JS-Interop mit der Standardunterstützung des Browsers für [ECMAScript-Module (ESM)](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules) ([ECMAScript-Spezifikation](https://tc39.es/ecma262/#sec-modules)).</span><span class="sxs-lookup"><span data-stu-id="842f7-226">For JS isolation, JS interop works with the browser's default support for [EcmaScript modules (ESM)](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules) ([ECMAScript specification](https://tc39.es/ecma262/#sec-modules)).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="842f7-227">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="842f7-227">Additional resources</span></span>

* <xref:blazor/call-javascript-from-dotnet>
* [<span data-ttu-id="842f7-228">`InteropComponent.razor`-Beispiel (dotnet/AspNetCore-GitHub-Repository, Releasebranch 3.1)</span><span class="sxs-lookup"><span data-stu-id="842f7-228">`InteropComponent.razor` example (dotnet/AspNetCore GitHub repository, 3.1 release branch)</span></span>](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
