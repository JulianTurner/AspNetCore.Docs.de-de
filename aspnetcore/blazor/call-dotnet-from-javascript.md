---
title: 'Aufrufen von .NET-Methoden von JavaScript-Funktionen in ASP.NET Core :::no-loc(Blazor):::'
author: guardrex
description: In diesem Artikel erfahren Sie, wie Sie .NET-Methoden von JavaScript-Funktionen in :::no-loc(Blazor):::-Apps aufrufen.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc, devx-track-js
ms.date: 08/12/2020
no-loc:
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: blazor/call-dotnet-from-javascript
ms.openlocfilehash: 4a7c06ed985c290eb4b3ffca6d5ed74c6bc4e031
ms.sourcegitcommit: 2e3a967331b2c69f585dd61e9ad5c09763615b44
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92690291"
---
# <a name="call-net-methods-from-javascript-functions-in-aspnet-core-no-locblazor"></a><span data-ttu-id="81bbb-103">Aufrufen von .NET-Methoden von JavaScript-Funktionen in ASP.NET Core :::no-loc(Blazor):::</span><span class="sxs-lookup"><span data-stu-id="81bbb-103">Call .NET methods from JavaScript functions in ASP.NET Core :::no-loc(Blazor):::</span></span>

<span data-ttu-id="81bbb-104">Von [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), [Shashikant Rudrawadi](http://wisne.co) und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="81bbb-104">By [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), [Shashikant Rudrawadi](http://wisne.co), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="81bbb-105">Eine :::no-loc(Blazor):::-App kann JavaScript-Funktionen über .NET-Methoden und .NET-Methoden über JavaScript-Funktionen aufrufen.</span><span class="sxs-lookup"><span data-stu-id="81bbb-105">A :::no-loc(Blazor)::: app can invoke JavaScript functions from .NET methods and .NET methods from JavaScript functions.</span></span> <span data-ttu-id="81bbb-106">Diese Szenarios werden als *JavaScript-Interoperabilität* ( *JS Interop* ) bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="81bbb-106">These scenarios are called *JavaScript interoperability* ( *JS interop* ).</span></span>

<span data-ttu-id="81bbb-107">In diesem Artikel wird das Aufrufen von .NET-Methoden über JavaScript behandelt.</span><span class="sxs-lookup"><span data-stu-id="81bbb-107">This article covers invoking .NET methods from JavaScript.</span></span> <span data-ttu-id="81bbb-108">Informationen zum Aufrufen von JavaScript-Funktionen über .NET finden Sie unter <xref:blazor/call-javascript-from-dotnet>.</span><span class="sxs-lookup"><span data-stu-id="81bbb-108">For information on how to call JavaScript functions from .NET, see <xref:blazor/call-javascript-from-dotnet>.</span></span>

<span data-ttu-id="81bbb-109">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="81bbb-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="static-net-method-call"></a><span data-ttu-id="81bbb-110">Statischer .NET-Methodenaufruf</span><span class="sxs-lookup"><span data-stu-id="81bbb-110">Static .NET method call</span></span>

<span data-ttu-id="81bbb-111">Um eine statische .NET-Methode über JavaScript aufzurufen, verwenden Sie die Funktionen `DotNet.invokeMethod` oder `DotNet.invokeMethodAsync`.</span><span class="sxs-lookup"><span data-stu-id="81bbb-111">To invoke a static .NET method from JavaScript, use the `DotNet.invokeMethod` or `DotNet.invokeMethodAsync` functions.</span></span> <span data-ttu-id="81bbb-112">Übergeben Sie den Bezeichner der statischen Methode, die Sie aufrufen möchten, den Namen der Assembly, die die Funktion enthält, und alle Argumente.</span><span class="sxs-lookup"><span data-stu-id="81bbb-112">Pass in the identifier of the static method you wish to call, the name of the assembly containing the function, and any arguments.</span></span> <span data-ttu-id="81bbb-113">Die asynchrone Version wird bevorzugt, um :::no-loc(Blazor Server):::-Szenarien zu unterstützen.</span><span class="sxs-lookup"><span data-stu-id="81bbb-113">The asynchronous version is preferred to support :::no-loc(Blazor Server)::: scenarios.</span></span> <span data-ttu-id="81bbb-114">Die .NET-Methode muss öffentlich und statisch sein sowie das Attribut [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) aufweisen.</span><span class="sxs-lookup"><span data-stu-id="81bbb-114">The .NET method must be public, static, and have the [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) attribute.</span></span> <span data-ttu-id="81bbb-115">Das Aufrufen offener generischer Methoden wird derzeit nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="81bbb-115">Calling open generic methods isn't currently supported.</span></span>

<span data-ttu-id="81bbb-116">Die Beispiel-App enthält eine C#-Methode zur Rückgabe eines `int`-Arrays.</span><span class="sxs-lookup"><span data-stu-id="81bbb-116">The sample app includes a C# method to return an `int` array.</span></span> <span data-ttu-id="81bbb-117">Das [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute)-Attribut wird auf die Methode angewendet.</span><span class="sxs-lookup"><span data-stu-id="81bbb-117">The [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) attribute is applied to the method.</span></span>

<span data-ttu-id="81bbb-118">`Pages/JsInterop.razor`:</span><span class="sxs-lookup"><span data-stu-id="81bbb-118">`Pages/JsInterop.razor`:</span></span>

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

<span data-ttu-id="81bbb-119">Das dem Client zugestellte JavaScript ruft die C# .NET-Methode auf.</span><span class="sxs-lookup"><span data-stu-id="81bbb-119">JavaScript served to the client invokes the C# .NET method.</span></span>

<span data-ttu-id="81bbb-120">`wwwroot/exampleJsInterop.js`:</span><span class="sxs-lookup"><span data-stu-id="81bbb-120">`wwwroot/exampleJsInterop.js`:</span></span>

[!code-javascript[](./common/samples/3.x/:::no-loc(Blazor):::WebAssemblySample/wwwroot/exampleJsInterop.js?highlight=8-14)]

<span data-ttu-id="81bbb-121">Überprüfen Sie bei Auswahl der Schaltfläche **`Trigger .NET static method ReturnArrayAsync`** die Konsolenausgabe in den Web Developer Tools des Browsers.</span><span class="sxs-lookup"><span data-stu-id="81bbb-121">When the **`Trigger .NET static method ReturnArrayAsync`** button is selected, examine the console output in the browser's web developer tools.</span></span>

<span data-ttu-id="81bbb-122">Die Konsolenausgabe lautet:</span><span class="sxs-lookup"><span data-stu-id="81bbb-122">The console output is:</span></span>

```console
Array(4) [ 1, 2, 3, 4 ]
```

<span data-ttu-id="81bbb-123">Der vierte Arraywert wird in das von `ReturnArrayAsync` zurückgegebene Array (`data.push(4);`) gepusht.</span><span class="sxs-lookup"><span data-stu-id="81bbb-123">The fourth array value is pushed to the array (`data.push(4);`) returned by `ReturnArrayAsync`.</span></span>

<span data-ttu-id="81bbb-124">Standardmäßig ist der Methodenbezeichner der Methodenname, aber Sie können mit dem Attributkonstruktor [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) einen anderen Bezeichner angeben:</span><span class="sxs-lookup"><span data-stu-id="81bbb-124">By default, the method identifier is the method name, but you can specify a different identifier using the [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) attribute constructor:</span></span>

```csharp
@code {
    [JSInvokable("DifferentMethodName")]
    public static Task<int[]> ReturnArrayAsync()
    {
        return Task.FromResult(new int[] { 1, 2, 3 });
    }
}
```

<span data-ttu-id="81bbb-125">In der clientseitigen JavaScript-Datei:</span><span class="sxs-lookup"><span data-stu-id="81bbb-125">In the client-side JavaScript file:</span></span>

```javascript
returnArrayAsyncJs: function () {
  DotNet.invokeMethodAsync('{APP ASSEMBLY}', 'DifferentMethodName')
    .then(data => {
      data.push(4);
      console.log(data);
    });
}
```

<span data-ttu-id="81bbb-126">Der Platzhalter `{APP ASSEMBLY}` ist der App-Assemblyname der App (z. B. `:::no-loc(Blazor):::Sample`).</span><span class="sxs-lookup"><span data-stu-id="81bbb-126">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `:::no-loc(Blazor):::Sample`).</span></span>

## <a name="instance-method-call"></a><span data-ttu-id="81bbb-127">Aufruf der Instanzmethode</span><span class="sxs-lookup"><span data-stu-id="81bbb-127">Instance method call</span></span>

<span data-ttu-id="81bbb-128">Sie können auch .NET-Instanzmethoden von JavaScript aus aufrufen.</span><span class="sxs-lookup"><span data-stu-id="81bbb-128">You can also call .NET instance methods from JavaScript.</span></span> <span data-ttu-id="81bbb-129">So rufen Sie .NET-Instanzmethoden von JavaScript aus auf</span><span class="sxs-lookup"><span data-stu-id="81bbb-129">To invoke a .NET instance method from JavaScript:</span></span>

* <span data-ttu-id="81bbb-130">Übergeben Sie die .NET-Instanz durch Verweis auf JavaScript:</span><span class="sxs-lookup"><span data-stu-id="81bbb-130">Pass the .NET instance by reference to JavaScript:</span></span>
  * <span data-ttu-id="81bbb-131">Führen Sie einen statischen Aufruf an <xref:Microsoft.JSInterop.DotNetObjectReference.Create%2A?displayProperty=nameWithType> durch.</span><span class="sxs-lookup"><span data-stu-id="81bbb-131">Make a static call to <xref:Microsoft.JSInterop.DotNetObjectReference.Create%2A?displayProperty=nameWithType>.</span></span>
  * <span data-ttu-id="81bbb-132">Umschließen Sie die Instanz mit einer <xref:Microsoft.JSInterop.DotNetObjectReference>-Instanz, und rufen Sie <xref:Microsoft.JSInterop.DotNetObjectReference.Create%2A> für die <xref:Microsoft.JSInterop.DotNetObjectReference>-Instanz auf.</span><span class="sxs-lookup"><span data-stu-id="81bbb-132">Wrap the instance in a <xref:Microsoft.JSInterop.DotNetObjectReference> instance and call <xref:Microsoft.JSInterop.DotNetObjectReference.Create%2A> on the <xref:Microsoft.JSInterop.DotNetObjectReference> instance.</span></span> <span data-ttu-id="81bbb-133">Entsorgen Sie <xref:Microsoft.JSInterop.DotNetObjectReference>-Objekte (ein Beispiel folgt später in diesem Abschnitt).</span><span class="sxs-lookup"><span data-stu-id="81bbb-133">Dispose of <xref:Microsoft.JSInterop.DotNetObjectReference> objects (an example appears later in this section).</span></span>
* <span data-ttu-id="81bbb-134">Rufen Sie .NET-Instanzmethoden für die Instanz mit den Funktionen `invokeMethod` oder `invokeMethodAsync` auf.</span><span class="sxs-lookup"><span data-stu-id="81bbb-134">Invoke .NET instance methods on the instance using the `invokeMethod` or `invokeMethodAsync` functions.</span></span> <span data-ttu-id="81bbb-135">Die .NET-Instanz kann auch als Argument übergeben werden, wenn andere .NET-Methoden von JavaScript aus aufgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="81bbb-135">The .NET instance can also be passed as an argument when invoking other .NET methods from JavaScript.</span></span>

> [!NOTE]
> <span data-ttu-id="81bbb-136">Die Beispiel-App protokolliert Nachrichten an die clientseitige Konsole.</span><span class="sxs-lookup"><span data-stu-id="81bbb-136">The sample app logs messages to the client-side console.</span></span> <span data-ttu-id="81bbb-137">Für die folgenden Beispiele, die durch die Beispiel-App veranschaulicht werden, untersuchen Sie die Konsolenausgabe des Browsers in den Entwicklertools des Browsers.</span><span class="sxs-lookup"><span data-stu-id="81bbb-137">For the following examples demonstrated by the sample app, examine the browser's console output in the browser's developer tools.</span></span>

<span data-ttu-id="81bbb-138">Bei Auswahl der Schaltfläche **`Trigger .NET instance method HelloHelper.SayHello`** wird `ExampleJsInterop.CallHelloHelperSayHello` aufgerufen und der Name `:::no-loc(Blazor):::` an die Methode übergeben.</span><span class="sxs-lookup"><span data-stu-id="81bbb-138">When the **`Trigger .NET instance method HelloHelper.SayHello`** button is selected, `ExampleJsInterop.CallHelloHelperSayHello` is called and passes a name, `:::no-loc(Blazor):::`, to the method.</span></span>

<span data-ttu-id="81bbb-139">`Pages/JsInterop.razor`:</span><span class="sxs-lookup"><span data-stu-id="81bbb-139">`Pages/JsInterop.razor`:</span></span>

```razor
<button type="button" class="btn btn-primary" @onclick="TriggerNetInstanceMethod">
    Trigger .NET instance method HelloHelper.SayHello
</button>

@code {
    public async Task TriggerNetInstanceMethod()
    {
        var exampleJsInterop = new ExampleJsInterop(JSRuntime);
        await exampleJsInterop.CallHelloHelperSayHello(":::no-loc(Blazor):::");
    }
}
```

<span data-ttu-id="81bbb-140">`CallHelloHelperSayHello` ruft die JavaScript-Funktion `sayHello` mit einer neuen Instanz von `HelloHelper` auf.</span><span class="sxs-lookup"><span data-stu-id="81bbb-140">`CallHelloHelperSayHello` invokes the JavaScript function `sayHello` with a new instance of `HelloHelper`.</span></span>

<span data-ttu-id="81bbb-141">`JsInteropClasses/ExampleJsInterop.cs`:</span><span class="sxs-lookup"><span data-stu-id="81bbb-141">`JsInteropClasses/ExampleJsInterop.cs`:</span></span>

[!code-csharp[](./common/samples/3.x/:::no-loc(Blazor):::WebAssemblySample/JsInteropClasses/ExampleJsInterop.cs?name=snippet1&highlight=11-18)]

<span data-ttu-id="81bbb-142">`wwwroot/exampleJsInterop.js`:</span><span class="sxs-lookup"><span data-stu-id="81bbb-142">`wwwroot/exampleJsInterop.js`:</span></span>

[!code-javascript[](./common/samples/3.x/:::no-loc(Blazor):::WebAssemblySample/wwwroot/exampleJsInterop.js?highlight=15-18)]

<span data-ttu-id="81bbb-143">Der Name wird an den Konstruktor von `HelloHelper` übergeben, der die Eigenschaft `HelloHelper.Name` festlegt.</span><span class="sxs-lookup"><span data-stu-id="81bbb-143">The name is passed to `HelloHelper`'s constructor, which sets the `HelloHelper.Name` property.</span></span> <span data-ttu-id="81bbb-144">Wenn die JavaScript-Funktion `sayHello` ausgeführt wird, gibt `HelloHelper.SayHello` die Nachricht `Hello, {Name}!` zurück, die von der JavaScript-Funktion in die Konsole geschrieben wird.</span><span class="sxs-lookup"><span data-stu-id="81bbb-144">When the JavaScript function `sayHello` is executed, `HelloHelper.SayHello` returns the `Hello, {Name}!` message, which is written to the console by the JavaScript function.</span></span>

<span data-ttu-id="81bbb-145">`JsInteropClasses/HelloHelper.cs`:</span><span class="sxs-lookup"><span data-stu-id="81bbb-145">`JsInteropClasses/HelloHelper.cs`:</span></span>

[!code-csharp[](./common/samples/3.x/:::no-loc(Blazor):::WebAssemblySample/JsInteropClasses/HelloHelper.cs?name=snippet1&highlight=5,10-11)]

<span data-ttu-id="81bbb-146">Konsolenausgabe in den Webentwicklertools des Browsers:</span><span class="sxs-lookup"><span data-stu-id="81bbb-146">Console output in the browser's web developer tools:</span></span>

```console
Hello, :::no-loc(Blazor):::!
```

<span data-ttu-id="81bbb-147">Verwenden Sie einen der folgenden Ansätze, um einen Arbeitsspeicherverlust zu vermeiden und die Garbage Collection für eine Komponente zu ermöglichen, die ein <xref:Microsoft.JSInterop.DotNetObjectReference> erstellt:</span><span class="sxs-lookup"><span data-stu-id="81bbb-147">To avoid a memory leak and allow garbage collection on a component that creates a <xref:Microsoft.JSInterop.DotNetObjectReference>, adopt one of the following approaches:</span></span>

* <span data-ttu-id="81bbb-148">Löschen Sie das Objekt in der Klasse, die die <xref:Microsoft.JSInterop.DotNetObjectReference>-Instanz erstellt hat:</span><span class="sxs-lookup"><span data-stu-id="81bbb-148">Dispose of the object in the class that created the <xref:Microsoft.JSInterop.DotNetObjectReference> instance:</span></span>

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

  <span data-ttu-id="81bbb-149">Das vorhergehende Muster, das in der Klasse `ExampleJsInterop` gezeigt wird, kann auch in einer Komponente implementiert werden:</span><span class="sxs-lookup"><span data-stu-id="81bbb-149">The preceding pattern shown in the `ExampleJsInterop` class can also be implemented in a component:</span></span>

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
          objRef = DotNetObjectReference.Create(new HelloHelper(":::no-loc(Blazor):::"));

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
  
  <span data-ttu-id="81bbb-150">Der Platzhalter `{APP ASSEMBLY}` ist der App-Assemblyname der App (z. B. `:::no-loc(Blazor):::Sample`).</span><span class="sxs-lookup"><span data-stu-id="81bbb-150">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `:::no-loc(Blazor):::Sample`).</span></span>

* <span data-ttu-id="81bbb-151">Wenn die Komponente oder Klasse die <xref:Microsoft.JSInterop.DotNetObjectReference>-Instanz nicht löscht, löschen Sie das Objekt im Client, indem Sie `.dispose()` aufrufen:</span><span class="sxs-lookup"><span data-stu-id="81bbb-151">When the component or class doesn't dispose of the <xref:Microsoft.JSInterop.DotNetObjectReference>, dispose of the object on the client by calling `.dispose()`:</span></span>

  ```javascript
  window.myFunction = (dotnetHelper) => {
    dotnetHelper.invokeMethodAsync('{APP ASSEMBLY}', 'MyMethod');
    dotnetHelper.dispose();
  }
  ```

## <a name="component-instance-method-call"></a><span data-ttu-id="81bbb-152">Aufrufen der Komponenteninstanzmethode</span><span class="sxs-lookup"><span data-stu-id="81bbb-152">Component instance method call</span></span>

<span data-ttu-id="81bbb-153">So rufen Sie die .NET-Methoden einer Komponente auf:</span><span class="sxs-lookup"><span data-stu-id="81bbb-153">To invoke a component's .NET methods:</span></span>

* <span data-ttu-id="81bbb-154">Verwenden Sie die `invokeMethod`- oder `invokeMethodAsync`-Funktion, um einen statischen Methodenaufruf an die Komponente auszuführen.</span><span class="sxs-lookup"><span data-stu-id="81bbb-154">Use the `invokeMethod` or `invokeMethodAsync` function to make a static method call to the component.</span></span>
* <span data-ttu-id="81bbb-155">Die statische Methode der Komponente umschließt den Aufruf der Instanzmethode als aufgerufene <xref:System.Action>.</span><span class="sxs-lookup"><span data-stu-id="81bbb-155">The component's static method wraps the call to its instance method as an invoked <xref:System.Action>.</span></span>

> [!NOTE]
> <span data-ttu-id="81bbb-156">Verwenden Sie für :::no-loc(Blazor Server):::-Apps, bei denen mehrere Benutzer gleichzeitig dieselbe Komponente verwenden können, eine Hilfsklasse zum Aufrufen von Instanzmethoden.</span><span class="sxs-lookup"><span data-stu-id="81bbb-156">For :::no-loc(Blazor Server)::: apps, where several users might be concurrently using the same component, use a helper class to invoke instance methods.</span></span>
>
> <span data-ttu-id="81bbb-157">Weitere Informationen finden Sie im Abschnitt [Komponenteninstanzmethode-Hilfsklasse](#component-instance-method-helper-class).</span><span class="sxs-lookup"><span data-stu-id="81bbb-157">For more information, see the [Component instance method helper class](#component-instance-method-helper-class) section.</span></span>

<span data-ttu-id="81bbb-158">In der clientseitigen JavaScript-Datei:</span><span class="sxs-lookup"><span data-stu-id="81bbb-158">In the client-side JavaScript:</span></span>

```javascript
function updateMessageCallerJS() {
  DotNet.invokeMethodAsync('{APP ASSEMBLY}', 'UpdateMessageCaller');
}
```

<span data-ttu-id="81bbb-159">Der Platzhalter `{APP ASSEMBLY}` ist der App-Assemblyname der App (z. B. `:::no-loc(Blazor):::Sample`).</span><span class="sxs-lookup"><span data-stu-id="81bbb-159">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `:::no-loc(Blazor):::Sample`).</span></span>

<span data-ttu-id="81bbb-160">`Pages/JSInteropComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="81bbb-160">`Pages/JSInteropComponent.razor`:</span></span>

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

<span data-ttu-id="81bbb-161">So übergeben Sie Argumente an die Instanzmethode:</span><span class="sxs-lookup"><span data-stu-id="81bbb-161">To pass arguments to the instance method:</span></span>

* <span data-ttu-id="81bbb-162">Fügen Sie dem JS-Methodenaufruf Parameter hinzu.</span><span class="sxs-lookup"><span data-stu-id="81bbb-162">Add parameters to the JS method invocation.</span></span> <span data-ttu-id="81bbb-163">Im folgenden Beispiel wird ein Name an die Methode übergeben.</span><span class="sxs-lookup"><span data-stu-id="81bbb-163">In the following example, a name is passed to the method.</span></span> <span data-ttu-id="81bbb-164">Der Liste können nach Bedarf weitere Parameter hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="81bbb-164">Additional parameters can be added to the list as needed.</span></span>

  ```javascript
  function updateMessageCallerJS(name) {
    DotNet.invokeMethodAsync('{APP ASSEMBLY}', 'UpdateMessageCaller', name);
  }
  ```
  
  <span data-ttu-id="81bbb-165">Der Platzhalter `{APP ASSEMBLY}` ist der App-Assemblyname der App (z. B. `:::no-loc(Blazor):::Sample`).</span><span class="sxs-lookup"><span data-stu-id="81bbb-165">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `:::no-loc(Blazor):::Sample`).</span></span>

* <span data-ttu-id="81bbb-166">Geben Sie die richtigen Typen für die <xref:System.Action> für die Parameter an.</span><span class="sxs-lookup"><span data-stu-id="81bbb-166">Provide the correct types to the <xref:System.Action> for the parameters.</span></span> <span data-ttu-id="81bbb-167">Geben Sie die Parameterliste für die C#-Methoden an.</span><span class="sxs-lookup"><span data-stu-id="81bbb-167">Provide the parameter list to the C# methods.</span></span> <span data-ttu-id="81bbb-168">Rufen Sie die <xref:System.Action> (`UpdateMessage`) mit den Parametern (`action.Invoke(name)`) auf.</span><span class="sxs-lookup"><span data-stu-id="81bbb-168">Invoke the <xref:System.Action> (`UpdateMessage`) with the parameters (`action.Invoke(name)`).</span></span>

  <span data-ttu-id="81bbb-169">`Pages/JSInteropComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="81bbb-169">`Pages/JSInteropComponent.razor`:</span></span>

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

  <span data-ttu-id="81bbb-170">Gibt `message` aus, wenn die Schaltfläche **Call JS Method** (JS-Methode aufrufen) ausgewählt wird:</span><span class="sxs-lookup"><span data-stu-id="81bbb-170">Output `message` when the **Call JS Method** button is selected:</span></span>

  ```
  Sarah Jane, UpdateMessage Called!
  ```

## <a name="component-instance-method-helper-class"></a><span data-ttu-id="81bbb-171">Komponenteninstanzmethode-Hilfsklasse</span><span class="sxs-lookup"><span data-stu-id="81bbb-171">Component instance method helper class</span></span>

<span data-ttu-id="81bbb-172">Die-Hilfsklasse wird verwendet, um eine Instanzmethode als <xref:System.Action>aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="81bbb-172">The helper class is used to invoke an instance method as an <xref:System.Action>.</span></span> <span data-ttu-id="81bbb-173">Hilfsklassen sind in folgenden Fällen nützlich:</span><span class="sxs-lookup"><span data-stu-id="81bbb-173">Helper classes are useful when:</span></span>

* <span data-ttu-id="81bbb-174">Mehrere Komponenten desselben Typs werden auf derselben Seite gerendert.</span><span class="sxs-lookup"><span data-stu-id="81bbb-174">Several components of the same type are rendered on the same page.</span></span>
* <span data-ttu-id="81bbb-175">Es wird eine :::no-loc(Blazor Server):::-App verwendet, in der mehrere Benutzer eine Komponente gleichzeitig verwenden können.</span><span class="sxs-lookup"><span data-stu-id="81bbb-175">A :::no-loc(Blazor Server)::: app is used, where multiple users might be using a component concurrently.</span></span>

<span data-ttu-id="81bbb-176">Im folgenden Beispiel:</span><span class="sxs-lookup"><span data-stu-id="81bbb-176">In the following example:</span></span>

* <span data-ttu-id="81bbb-177">Die `JSInteropExample`-Komponente enthält mehrere `ListItem`-Komponenten.</span><span class="sxs-lookup"><span data-stu-id="81bbb-177">The `JSInteropExample` component contains several `ListItem` components.</span></span>
* <span data-ttu-id="81bbb-178">Alle `ListItem`-Komponenten bestehen aus einer Nachricht und einer Schaltfläche.</span><span class="sxs-lookup"><span data-stu-id="81bbb-178">Each `ListItem` component is composed of a message and a button.</span></span>
* <span data-ttu-id="81bbb-179">Wenn auf die Schaltfläche einer `ListItem`-Komponente geklickt wird, ändert die `UpdateMessage`-Methode des `ListItem`-Elements den Text des Listenelements und blendet die Schaltfläche aus.</span><span class="sxs-lookup"><span data-stu-id="81bbb-179">When a `ListItem` component button is selected, that `ListItem`'s `UpdateMessage` method changes the list item text and hides the button.</span></span>

<span data-ttu-id="81bbb-180">`MessageUpdateInvokeHelper.cs`:</span><span class="sxs-lookup"><span data-stu-id="81bbb-180">`MessageUpdateInvokeHelper.cs`:</span></span>

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

<span data-ttu-id="81bbb-181">Der Platzhalter `{APP ASSEMBLY}` ist der App-Assemblyname der App (z. B. `:::no-loc(Blazor):::Sample`).</span><span class="sxs-lookup"><span data-stu-id="81bbb-181">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `:::no-loc(Blazor):::Sample`).</span></span>

<span data-ttu-id="81bbb-182">In der clientseitigen JavaScript-Datei:</span><span class="sxs-lookup"><span data-stu-id="81bbb-182">In the client-side JavaScript:</span></span>

```javascript
window.updateMessageCallerJS = (dotnetHelper) => {
    dotnetHelper.invokeMethodAsync('{APP ASSEMBLY}', 'UpdateMessageCaller');
    dotnetHelper.dispose();
}
```

<span data-ttu-id="81bbb-183">Der Platzhalter `{APP ASSEMBLY}` ist der App-Assemblyname der App (z. B. `:::no-loc(Blazor):::Sample`).</span><span class="sxs-lookup"><span data-stu-id="81bbb-183">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `:::no-loc(Blazor):::Sample`).</span></span>

<span data-ttu-id="81bbb-184">`Shared/ListItem.razor`:</span><span class="sxs-lookup"><span data-stu-id="81bbb-184">`Shared/ListItem.razor`:</span></span>

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

<span data-ttu-id="81bbb-185">`Pages/JSInteropExample.razor`:</span><span class="sxs-lookup"><span data-stu-id="81bbb-185">`Pages/JSInteropExample.razor`:</span></span>

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

## <a name="avoid-circular-object-references"></a><span data-ttu-id="81bbb-186">Vermeiden von Objektzirkelbezügen</span><span class="sxs-lookup"><span data-stu-id="81bbb-186">Avoid circular object references</span></span>

<span data-ttu-id="81bbb-187">Objekte, die Zirkelbezüge enthalten, können auf dem Client für folgende Vorgänge nicht serialisiert werden:</span><span class="sxs-lookup"><span data-stu-id="81bbb-187">Objects that contain circular references can't be serialized on the client for either:</span></span>

* <span data-ttu-id="81bbb-188">.NET-Methodenaufrufe.</span><span class="sxs-lookup"><span data-stu-id="81bbb-188">.NET method calls.</span></span>
* <span data-ttu-id="81bbb-189">JavaScript-Methodenaufrufe von C#, wenn der Rückgabetyp Zirkelbezüge enthält.</span><span class="sxs-lookup"><span data-stu-id="81bbb-189">JavaScript method calls from C# when the return type has circular references.</span></span>

<span data-ttu-id="81bbb-190">Weitere Informationen finden Sie unter den folgenden Problemen:</span><span class="sxs-lookup"><span data-stu-id="81bbb-190">For more information, see the following issues:</span></span>

* [<span data-ttu-id="81bbb-191">Zirkelbezüge werden nicht unterstützt, die Zweite (dotnet/aspnetcore #20525)</span><span class="sxs-lookup"><span data-stu-id="81bbb-191">Circular references are not supported, take two (dotnet/aspnetcore #20525)</span></span>](https://github.com/dotnet/aspnetcore/issues/20525)
* [<span data-ttu-id="81bbb-192">Vorschlag: Hinzufügen eines Mechanismus zur Verarbeitung von Zirkelbezügen bei der Serialisierung (dotnet/runtime #30820)</span><span class="sxs-lookup"><span data-stu-id="81bbb-192">Proposal: Add mechanism to handle circular references when serializing (dotnet/runtime #30820)</span></span>](https://github.com/dotnet/runtime/issues/30820)

## <a name="additional-resources"></a><span data-ttu-id="81bbb-193">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="81bbb-193">Additional resources</span></span>

* <xref:blazor/call-javascript-from-dotnet>
* [<span data-ttu-id="81bbb-194">`InteropComponent.razor`-Beispiel (dotnet/AspNetCore-GitHub-Repository, Releasebranch 3.1)</span><span class="sxs-lookup"><span data-stu-id="81bbb-194">`InteropComponent.razor` example (dotnet/AspNetCore GitHub repository, 3.1 release branch)</span></span>](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
* [<span data-ttu-id="81bbb-195">Ausführen umfangreicher Datenübertragungen in :::no-loc(Blazor Server):::-Apps</span><span class="sxs-lookup"><span data-stu-id="81bbb-195">Perform large data transfers in :::no-loc(Blazor Server)::: apps</span></span>](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)
