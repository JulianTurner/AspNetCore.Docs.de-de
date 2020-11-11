---
title: 'Aufrufen von JavaScript-Funktionen über .NET-Methoden in ASP.NET Core :::no-loc(Blazor):::'
author: guardrex
description: In diesem Artikel erfahren Sie, wie Sie JavaScript-Funktionen über .NET-Methoden in :::no-loc(Blazor):::-Apps aufrufen.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc, devx-track-js
ms.date: 10/20/2020
no-loc:
- ':::no-loc(appsettings.json):::'
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
uid: blazor/call-javascript-from-dotnet
ms.openlocfilehash: 17d6087b884775a8bfcb41fe23296f508467e924
ms.sourcegitcommit: d64bf0cbe763beda22a7728c7f10d07fc5e19262
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2020
ms.locfileid: "93234451"
---
# <a name="call-javascript-functions-from-net-methods-in-aspnet-core-no-locblazor"></a><span data-ttu-id="3d1be-103">Aufrufen von JavaScript-Funktionen über .NET-Methoden in ASP.NET Core :::no-loc(Blazor):::</span><span class="sxs-lookup"><span data-stu-id="3d1be-103">Call JavaScript functions from .NET methods in ASP.NET Core :::no-loc(Blazor):::</span></span>

<span data-ttu-id="3d1be-104">Von [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27) und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="3d1be-104">By [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="3d1be-105">Eine :::no-loc(Blazor):::-App kann JavaScript-Funktionen über .NET-Methoden und .NET-Methoden über JavaScript-Funktionen aufrufen.</span><span class="sxs-lookup"><span data-stu-id="3d1be-105">A :::no-loc(Blazor)::: app can invoke JavaScript functions from .NET methods and .NET methods from JavaScript functions.</span></span> <span data-ttu-id="3d1be-106">Diese Szenarios werden als *JavaScript-Interoperabilität* ( *JS Interop* ) bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="3d1be-106">These scenarios are called *JavaScript interoperability* ( *JS interop* ).</span></span>

<span data-ttu-id="3d1be-107">In diesem Artikel wird das Aufrufen von JavaScript-Funktionen über .NET behandelt.</span><span class="sxs-lookup"><span data-stu-id="3d1be-107">This article covers invoking JavaScript functions from .NET.</span></span> <span data-ttu-id="3d1be-108">Weitere Informationen zum Aufrufen von .NET-Methoden über JavaScript finden Sie unter <xref:blazor/call-dotnet-from-javascript>.</span><span class="sxs-lookup"><span data-stu-id="3d1be-108">For information on how to call .NET methods from JavaScript, see <xref:blazor/call-dotnet-from-javascript>.</span></span>

<span data-ttu-id="3d1be-109">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="3d1be-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="3d1be-110">Verwenden Sie die <xref:Microsoft.JSInterop.IJSRuntime>-Abstraktion, um JavaScript über .NET aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="3d1be-110">To call into JavaScript from .NET, use the <xref:Microsoft.JSInterop.IJSRuntime> abstraction.</span></span> <span data-ttu-id="3d1be-111">Fügen Sie die <xref:Microsoft.JSInterop.IJSRuntime>-Abstraktion in Ihre Komponente ein, um JS Interop-Aufrufe auszugeben.</span><span class="sxs-lookup"><span data-stu-id="3d1be-111">To issue JS interop calls, inject the <xref:Microsoft.JSInterop.IJSRuntime> abstraction in your component.</span></span> <span data-ttu-id="3d1be-112"><xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> akzeptiert einen Bezeichner für die JavaScript-Funktion, die Sie aufrufen möchten, sowie eine beliebige Anzahl JSON-serialisierbarer Argumente.</span><span class="sxs-lookup"><span data-stu-id="3d1be-112"><xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> takes an identifier for the JavaScript function that you wish to invoke along with any number of JSON-serializable arguments.</span></span> <span data-ttu-id="3d1be-113">Der Funktionsbezeichner ist relativ zum globalen Bereich (`window`).</span><span class="sxs-lookup"><span data-stu-id="3d1be-113">The function identifier is relative to the global scope (`window`).</span></span> <span data-ttu-id="3d1be-114">Wenn Sie `window.someScope.someFunction` aufrufen möchten, lautet der Bezeichner `someScope.someFunction`.</span><span class="sxs-lookup"><span data-stu-id="3d1be-114">If you wish to call `window.someScope.someFunction`, the identifier is `someScope.someFunction`.</span></span> <span data-ttu-id="3d1be-115">Die Funktion muss nicht registriert werden, bevor sie aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="3d1be-115">There's no need to register the function before it's called.</span></span> <span data-ttu-id="3d1be-116">Der Rückgabetyp `T` muss ebenfalls JSON-serialisierbar sein.</span><span class="sxs-lookup"><span data-stu-id="3d1be-116">The return type `T` must also be JSON serializable.</span></span> <span data-ttu-id="3d1be-117">`T` sollte mit dem .NET-Typ übereinstimmen, der dem zurückgegebenen JSON-Typ am ehesten entspricht.</span><span class="sxs-lookup"><span data-stu-id="3d1be-117">`T` should match the .NET type that best maps to the JSON type returned.</span></span>

<span data-ttu-id="3d1be-118">JavaScript-Funktionen, die eine [Zusage](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) zurückgeben, werden mit <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="3d1be-118">JavaScript functions that return a [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) are called with <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A>.</span></span> <span data-ttu-id="3d1be-119">`InvokeAsync` entpackt die Zusage und gibt den Wert zurück, der von der Zusage erwartet wird.</span><span class="sxs-lookup"><span data-stu-id="3d1be-119">`InvokeAsync` unwraps the Promise and returns the value awaited by the Promise.</span></span>

<span data-ttu-id="3d1be-120">Bei :::no-loc(Blazor Server):::-Apps mit aktiviertem Prerendering ist das Aufrufen von JavaScript beim ersten Prerendering nicht möglich.</span><span class="sxs-lookup"><span data-stu-id="3d1be-120">For :::no-loc(Blazor Server)::: apps with prerendering enabled, calling into JavaScript isn't possible during the initial prerendering.</span></span> <span data-ttu-id="3d1be-121">JavaScript-Interoperabilitätsaufrufe müssen verzögert werden, bis die Verbindung mit dem Browser hergestellt ist.</span><span class="sxs-lookup"><span data-stu-id="3d1be-121">JavaScript interop calls must be deferred until after the connection with the browser is established.</span></span> <span data-ttu-id="3d1be-122">Weitere Informationen finden Sie im Abschnitt [Erkennen, wenn für eine :::no-loc(Blazor Server):::-App ein Prerendering durchgeführt wird](#detect-when-a-blazor-server-app-is-prerendering).</span><span class="sxs-lookup"><span data-stu-id="3d1be-122">For more information, see the [Detect when a :::no-loc(Blazor Server)::: app is prerendering](#detect-when-a-blazor-server-app-is-prerendering) section.</span></span>

<span data-ttu-id="3d1be-123">Das folgende Beispiel basiert auf [`TextDecoder`](https://developer.mozilla.org/docs/Web/API/TextDecoder), einem auf JavaScript basierenden Decoder.</span><span class="sxs-lookup"><span data-stu-id="3d1be-123">The following example is based on [`TextDecoder`](https://developer.mozilla.org/docs/Web/API/TextDecoder), a JavaScript-based decoder.</span></span> <span data-ttu-id="3d1be-124">Im Beispiel wird veranschaulicht, wie eine JavaScript-Funktion aus einer C#-Methode aufgerufen wird, die eine Anforderung aus Entwicklercode in eine vorhandene JavaScript-API auslagert.</span><span class="sxs-lookup"><span data-stu-id="3d1be-124">The example demonstrates how to invoke a JavaScript function from a C# method that offloads a requirement from developer code to an existing JavaScript API.</span></span> <span data-ttu-id="3d1be-125">Die JavaScript-Funktion akzeptiert ein Bytearray von einer C#-Methode, decodiert das Array und gibt den Text zum Anzeigen an die Komponente zurück.</span><span class="sxs-lookup"><span data-stu-id="3d1be-125">The JavaScript function accepts a byte array from a C# method, decodes the array, and returns the text to the component for display.</span></span>

<span data-ttu-id="3d1be-126">Geben Sie im `<head>`-Element von `wwwroot/index.html` (:::no-loc(Blazor WebAssembly):::) oder `Pages/_Host.cshtml` (:::no-loc(Blazor Server):::) eine JavaScript-Funktion an, die `TextDecoder` verwendet, um ein übermitteltes Array zu decodieren und den decodierten Wert zurückzugeben:</span><span class="sxs-lookup"><span data-stu-id="3d1be-126">Inside the `<head>` element of `wwwroot/index.html` (:::no-loc(Blazor WebAssembly):::) or `Pages/_Host.cshtml` (:::no-loc(Blazor Server):::), provide a JavaScript function that uses `TextDecoder` to decode a passed array and return the decoded value:</span></span>

[!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-convertarray.html)]

<span data-ttu-id="3d1be-127">JavaScript-Code, z. B. der Code im vorherigen Beispiel, kann auch über eine JavaScript-Datei (`.js`) mit einem Verweis auf die Skriptdatei geladen werden:</span><span class="sxs-lookup"><span data-stu-id="3d1be-127">JavaScript code, such as the code shown in the preceding example, can also be loaded from a JavaScript file (`.js`) with a reference to the script file:</span></span>

```html
<script src="exampleJsInterop.js"></script>
```

<span data-ttu-id="3d1be-128">Die folgende Komponente führt folgende Aktionen aus:</span><span class="sxs-lookup"><span data-stu-id="3d1be-128">The following component:</span></span>

* <span data-ttu-id="3d1be-129">Sie ruft die JavaScript-Funktion `convertArray` mit `JS` auf, wenn eine Komponentenschaltfläche ( **`Convert Array`** ) ausgewählt wird.</span><span class="sxs-lookup"><span data-stu-id="3d1be-129">Invokes the `convertArray` JavaScript function using `JS` when a component button ( **`Convert Array`** ) is selected.</span></span>
* <span data-ttu-id="3d1be-130">Nachdem die JavaScript-Funktion aufgerufen wurde, wird das übergebene Array in eine Zeichenfolge konvertiert.</span><span class="sxs-lookup"><span data-stu-id="3d1be-130">After the JavaScript function is called, the passed array is converted into a string.</span></span> <span data-ttu-id="3d1be-131">Die Zeichenfolge wird zur Anzeige an die Komponente zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="3d1be-131">The string is returned to the component for display.</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/call-js-example.razor?highlight=2,34-35)]

## <a name="ijsruntime"></a><span data-ttu-id="3d1be-132">IJSRuntime</span><span class="sxs-lookup"><span data-stu-id="3d1be-132">IJSRuntime</span></span>

<span data-ttu-id="3d1be-133">Verwenden Sie einen der folgenden Ansätze, um die <xref:Microsoft.JSInterop.IJSRuntime>-Abstraktion zu verwenden:</span><span class="sxs-lookup"><span data-stu-id="3d1be-133">To use the <xref:Microsoft.JSInterop.IJSRuntime> abstraction, adopt any of the following approaches:</span></span>

* <span data-ttu-id="3d1be-134">Fügen Sie die <xref:Microsoft.JSInterop.IJSRuntime>-Abstraktion in die :::no-loc(Razor):::-Komponente (`.razor`) ein:</span><span class="sxs-lookup"><span data-stu-id="3d1be-134">Inject the <xref:Microsoft.JSInterop.IJSRuntime> abstraction into the :::no-loc(Razor)::: component (`.razor`):</span></span>

  [!code-razor[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction.razor?highlight=1)]

  <span data-ttu-id="3d1be-135">Stellen Sie innerhalb des `<head>`-Elements von `wwwroot/index.html` (:::no-loc(Blazor WebAssembly):::) oder `Pages/_Host.cshtml` (:::no-loc(Blazor Server):::) eine `handleTickerChanged`-JavaScript-Funktion bereit.</span><span class="sxs-lookup"><span data-stu-id="3d1be-135">Inside the `<head>` element of `wwwroot/index.html` (:::no-loc(Blazor WebAssembly):::) or `Pages/_Host.cshtml` (:::no-loc(Blazor Server):::), provide a `handleTickerChanged` JavaScript function.</span></span> <span data-ttu-id="3d1be-136">Die Funktion wird mit <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> aufgerufen und gibt keinen Wert zurück:</span><span class="sxs-lookup"><span data-stu-id="3d1be-136">The function is called with <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> and doesn't return a value:</span></span>

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged1.html)]

* <span data-ttu-id="3d1be-137">Fügen Sie die <xref:Microsoft.JSInterop.IJSRuntime>-Abstraktion in eine Klasse (`.cs`) ein:</span><span class="sxs-lookup"><span data-stu-id="3d1be-137">Inject the <xref:Microsoft.JSInterop.IJSRuntime> abstraction into a class (`.cs`):</span></span>

  [!code-csharp[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction-class.cs?highlight=5)]

  <span data-ttu-id="3d1be-138">Stellen Sie innerhalb des `<head>`-Elements von `wwwroot/index.html` (:::no-loc(Blazor WebAssembly):::) oder `Pages/_Host.cshtml` (:::no-loc(Blazor Server):::) eine `handleTickerChanged`-JavaScript-Funktion bereit.</span><span class="sxs-lookup"><span data-stu-id="3d1be-138">Inside the `<head>` element of `wwwroot/index.html` (:::no-loc(Blazor WebAssembly):::) or `Pages/_Host.cshtml` (:::no-loc(Blazor Server):::), provide a `handleTickerChanged` JavaScript function.</span></span> <span data-ttu-id="3d1be-139">Die Funktion wird mit `JS.InvokeAsync` aufgerufen und gibt einen Wert zurück:</span><span class="sxs-lookup"><span data-stu-id="3d1be-139">The function is called with `JS.InvokeAsync` and returns a value:</span></span>

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged2.html)]

* <span data-ttu-id="3d1be-140">Verwenden Sie das `[Inject]`-Attribut für die dynamische Inhaltsgenerierung mit [BuildRenderTree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic):</span><span class="sxs-lookup"><span data-stu-id="3d1be-140">For dynamic content generation with [BuildRenderTree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic), use the `[Inject]` attribute:</span></span>

  ```razor
  [Inject]
  IJSRuntime JS { get; set; }
  ```

<span data-ttu-id="3d1be-141">In der clientseitigen Beispiel-App zu diesem Artikel stehen der App zwei JavaScript-Funktionen zur Verfügung, die mit dem DOM (Dokumentobjektmodell) interagieren, um Benutzereingaben zu empfangen und eine Begrüßungsnachricht anzuzeigen:</span><span class="sxs-lookup"><span data-stu-id="3d1be-141">In the client-side sample app that accompanies this topic, two JavaScript functions are available to the app that interact with the DOM to receive user input and display a welcome message:</span></span>

* <span data-ttu-id="3d1be-142">`showPrompt`: Generiert eine Eingabeaufforderung zum Akzeptieren der Benutzereingaben (dem Namen des Benutzers) und gibt den Namen an den Aufrufer zurück.</span><span class="sxs-lookup"><span data-stu-id="3d1be-142">`showPrompt`: Produces a prompt to accept user input (the user's name) and returns the name to the caller.</span></span>
* <span data-ttu-id="3d1be-143">`displayWelcome`: Weist einem DOM-Objekt eine Begrüßungsnachricht vom Aufrufer mit einer `id` mit dem Wert `welcome` zu.</span><span class="sxs-lookup"><span data-stu-id="3d1be-143">`displayWelcome`: Assigns a welcome message from the caller to a DOM object with an `id` of `welcome`.</span></span>

<span data-ttu-id="3d1be-144">`wwwroot/exampleJsInterop.js`:</span><span class="sxs-lookup"><span data-stu-id="3d1be-144">`wwwroot/exampleJsInterop.js`:</span></span>

[!code-javascript[](./common/samples/3.x/:::no-loc(Blazor):::WebAssemblySample/wwwroot/exampleJsInterop.js?highlight=2-7)]

<span data-ttu-id="3d1be-145">Platzieren Sie das `<script>`-Tag, das auf die JavaScript-Datei verweist, in der `wwwroot/index.html`-Datei (:::no-loc(Blazor WebAssembly):::) oder der `Pages/_Host.cshtml`-Datei (:::no-loc(Blazor Server):::).</span><span class="sxs-lookup"><span data-stu-id="3d1be-145">Place the `<script>` tag that references the JavaScript file in the `wwwroot/index.html` file (:::no-loc(Blazor WebAssembly):::) or `Pages/_Host.cshtml` file (:::no-loc(Blazor Server):::).</span></span>

<span data-ttu-id="3d1be-146">`wwwroot/index.html` (:::no-loc(Blazor WebAssembly):::):</span><span class="sxs-lookup"><span data-stu-id="3d1be-146">`wwwroot/index.html` (:::no-loc(Blazor WebAssembly):::):</span></span>

[!code-html[](./common/samples/3.x/:::no-loc(Blazor):::WebAssemblySample/wwwroot/index.html?highlight=22)]

<span data-ttu-id="3d1be-147">`Pages/_Host.cshtml` (:::no-loc(Blazor Server):::):</span><span class="sxs-lookup"><span data-stu-id="3d1be-147">`Pages/_Host.cshtml` (:::no-loc(Blazor Server):::):</span></span>

[!code-cshtml[](./common/samples/3.x/:::no-loc(Blazor):::ServerSample/Pages/_Host.cshtml?highlight=35)]

<span data-ttu-id="3d1be-148">Platzieren Sie kein `<script>`-Tag in einer Komponentendatei, weil das `<script>`-Tag nicht dynamisch aktualisiert werden kann.</span><span class="sxs-lookup"><span data-stu-id="3d1be-148">Don't place a `<script>` tag in a component file because the `<script>` tag can't be updated dynamically.</span></span>

<span data-ttu-id="3d1be-149">Die Interoperabilität von .NET-Methoden mit den JavaScript-Funktionen in der Datei `exampleJsInterop.js` erfolgt, indem <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType> aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="3d1be-149">.NET methods interop with the JavaScript functions in the `exampleJsInterop.js` file by calling <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType>.</span></span>

<span data-ttu-id="3d1be-150">Die <xref:Microsoft.JSInterop.IJSRuntime>-Abstraktion ist asynchron, um :::no-loc(Blazor Server):::-Szenarios zu ermöglichen.</span><span class="sxs-lookup"><span data-stu-id="3d1be-150">The <xref:Microsoft.JSInterop.IJSRuntime> abstraction is asynchronous to allow for :::no-loc(Blazor Server)::: scenarios.</span></span> <span data-ttu-id="3d1be-151">Wenn es sich bei der App um eine :::no-loc(Blazor WebAssembly):::-App handelt, sollten Sie die JavaScript-Funktion synchron aufrufen, eine Typumwandlung nach unten in <xref:Microsoft.JSInterop.IJSInProcessRuntime> durchführen und stattdessen <xref:Microsoft.JSInterop.IJSInProcessRuntime.Invoke%2A> aufrufen.</span><span class="sxs-lookup"><span data-stu-id="3d1be-151">If the app is a :::no-loc(Blazor WebAssembly)::: app and you want to invoke a JavaScript function synchronously, downcast to <xref:Microsoft.JSInterop.IJSInProcessRuntime> and call <xref:Microsoft.JSInterop.IJSInProcessRuntime.Invoke%2A> instead.</span></span> <span data-ttu-id="3d1be-152">Es wird empfohlen, für die meisten JS Interop-Bibliotheken asynchrone APIs zu verwenden, um sicherzustellen, dass die Bibliotheken in allen Szenarios verfügbar sind.</span><span class="sxs-lookup"><span data-stu-id="3d1be-152">We recommend that most JS interop libraries use the async APIs to ensure that the libraries are available in all scenarios.</span></span>

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="3d1be-153">Informationen zum Aktivieren der JavaScript-Isolation in [JavaScript-Standardmodulen](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules) finden Sie im Abschnitt [:::no-loc(Blazor):::-JavaScript-Isolation und Objektverweise](#blazor-javascript-isolation-and-object-references).</span><span class="sxs-lookup"><span data-stu-id="3d1be-153">To enable JavaScript isolation in standard [JavaScript modules](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules), see the [:::no-loc(Blazor)::: JavaScript isolation and object references](#blazor-javascript-isolation-and-object-references) section.</span></span>

::: moniker-end

<span data-ttu-id="3d1be-154">Die Beispiel-App enthält eine Komponente zur Veranschaulichung der JavaScript-Interoperabilität.</span><span class="sxs-lookup"><span data-stu-id="3d1be-154">The sample app includes a component to demonstrate JS interop.</span></span> <span data-ttu-id="3d1be-155">Die Komponente:</span><span class="sxs-lookup"><span data-stu-id="3d1be-155">The component:</span></span>

* <span data-ttu-id="3d1be-156">empfängt Benutzereingaben über eine JavaScript-Eingabeaufforderung.</span><span class="sxs-lookup"><span data-stu-id="3d1be-156">Receives user input via a JavaScript prompt.</span></span>
* <span data-ttu-id="3d1be-157">gibt den Text zur Verarbeitung an die Komponente zurück.</span><span class="sxs-lookup"><span data-stu-id="3d1be-157">Returns the text to the component for processing.</span></span>
* <span data-ttu-id="3d1be-158">ruft eine zweite JavaScript-Funktion auf, die mit dem DOM interagiert, um eine Begrüßungsnachricht anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="3d1be-158">Calls a second JavaScript function that interacts with the DOM to display a welcome message.</span></span>

<span data-ttu-id="3d1be-159">`Pages/JsInterop.razor`:</span><span class="sxs-lookup"><span data-stu-id="3d1be-159">`Pages/JsInterop.razor`:</span></span>

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
                $"Hello {name}! Welcome to :::no-loc(Blazor):::!");
    }
}
```

<span data-ttu-id="3d1be-160">Der Platzhalter `{APP ASSEMBLY}` ist der App-Assemblyname der App (z. B. `:::no-loc(Blazor):::Sample`).</span><span class="sxs-lookup"><span data-stu-id="3d1be-160">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `:::no-loc(Blazor):::Sample`).</span></span>

1. <span data-ttu-id="3d1be-161">Wenn `TriggerJsPrompt` ausgeführt wird, indem die Schaltfläche **`Trigger JavaScript Prompt`** der Komponente ausgewählt wird, wird die `showPrompt`-Funktion von JavaScript aufgerufen, die in der `wwwroot/exampleJsInterop.js`-Datei bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="3d1be-161">When `TriggerJsPrompt` is executed by selecting the component's **`Trigger JavaScript Prompt`** button, the JavaScript `showPrompt` function provided in the `wwwroot/exampleJsInterop.js` file is called.</span></span>
1. <span data-ttu-id="3d1be-162">Die `showPrompt`-Funktion akzeptiert Benutzereingaben (den Namen des Benutzers), die HTML-codiert und an die Komponente zurückgegeben werden.</span><span class="sxs-lookup"><span data-stu-id="3d1be-162">The `showPrompt` function accepts user input (the user's name), which is HTML-encoded and returned to the component.</span></span> <span data-ttu-id="3d1be-163">Die Komponente speichert den Namen des Benutzers in der lokalen Variable `name`.</span><span class="sxs-lookup"><span data-stu-id="3d1be-163">The component stores the user's name in a local variable, `name`.</span></span>
1. <span data-ttu-id="3d1be-164">Die in `name` gespeicherte Zeichenfolge wird in eine Begrüßungsnachricht eingebunden, die an die JavaScript-Funktion `displayWelcome` übergeben wird, die die Begrüßungsnachricht in ein Überschriftentag rendert.</span><span class="sxs-lookup"><span data-stu-id="3d1be-164">The string stored in `name` is incorporated into a welcome message, which is passed to a JavaScript function, `displayWelcome`, which renders the welcome message into a heading tag.</span></span>

## <a name="call-a-void-javascript-function"></a><span data-ttu-id="3d1be-165">Aufrufen der JavaScript-Funktion „void“</span><span class="sxs-lookup"><span data-stu-id="3d1be-165">Call a void JavaScript function</span></span>

<span data-ttu-id="3d1be-166">Verwenden Sie <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> für Folgendes:</span><span class="sxs-lookup"><span data-stu-id="3d1be-166">Use <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> for the following:</span></span>

* <span data-ttu-id="3d1be-167">JavaScript-Funktionen, die [void(0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) oder [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined) zurückgeben</span><span class="sxs-lookup"><span data-stu-id="3d1be-167">JavaScript functions that return [void(0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) or [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined).</span></span>
* <span data-ttu-id="3d1be-168">Wenn .NET nicht erforderlich ist, um das Ergebnis eines JavaScript-Aufrufs zu lesen</span><span class="sxs-lookup"><span data-stu-id="3d1be-168">If .NET isn't required to read the result of a JavaScript call.</span></span>

## <a name="detect-when-a-no-locblazor-server-app-is-prerendering"></a><span data-ttu-id="3d1be-169">Erkennen, wenn für eine :::no-loc(Blazor Server):::-App ein Prerendering durchgeführt wird</span><span class="sxs-lookup"><span data-stu-id="3d1be-169">Detect when a :::no-loc(Blazor Server)::: app is prerendering</span></span>
 
[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="capture-references-to-elements"></a><span data-ttu-id="3d1be-170">Erfassen von Verweisen auf Elemente</span><span class="sxs-lookup"><span data-stu-id="3d1be-170">Capture references to elements</span></span>

<span data-ttu-id="3d1be-171">Einige JS Interop-Szenarios erfordern Verweise auf HTML-Elemente.</span><span class="sxs-lookup"><span data-stu-id="3d1be-171">Some JS interop scenarios require references to HTML elements.</span></span> <span data-ttu-id="3d1be-172">Beispielsweise erfordert eine Benutzeroberflächenbibliothek möglicherweise einen Elementverweis für die Initialisierung, oder Sie müssen befehlsähnliche APIs für ein Element aufrufen, z. B. `focus` oder `play`.</span><span class="sxs-lookup"><span data-stu-id="3d1be-172">For example, a UI library may require an element reference for initialization, or you might need to call command-like APIs on an element, such as `focus` or `play`.</span></span>

<span data-ttu-id="3d1be-173">Erfassen Sie mithilfe des folgenden Ansatzes Verweise auf HTML-Elemente in einer Komponente:</span><span class="sxs-lookup"><span data-stu-id="3d1be-173">Capture references to HTML elements in a component using the following approach:</span></span>

* <span data-ttu-id="3d1be-174">Fügen Sie ein `@ref`-Attribut zum HTML-Element hinzu.</span><span class="sxs-lookup"><span data-stu-id="3d1be-174">Add an `@ref` attribute to the HTML element.</span></span>
* <span data-ttu-id="3d1be-175">Definieren Sie ein Feld vom Typ <xref:Microsoft.AspNetCore.Components.ElementReference>, dessen Name mit dem Wert des `@ref`-Attributs übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="3d1be-175">Define a field of type <xref:Microsoft.AspNetCore.Components.ElementReference> whose name matches the value of the `@ref` attribute.</span></span>

<span data-ttu-id="3d1be-176">Im folgenden Beispiel wird das Erfassen eines Verweises auf das `username` `<input>`-Element veranschaulicht:</span><span class="sxs-lookup"><span data-stu-id="3d1be-176">The following example shows capturing a reference to the `username` `<input>` element:</span></span>

```razor
<input @ref="username" ... />

@code {
    ElementReference username;
}
```

> [!WARNING]
> <span data-ttu-id="3d1be-177">Verwenden Sie Elementverweise nur, um die Inhalte eines leeren Elements zu ändern, das nicht mit :::no-loc(Blazor)::: interagiert.</span><span class="sxs-lookup"><span data-stu-id="3d1be-177">Only use an element reference to mutate the contents of an empty element that doesn't interact with :::no-loc(Blazor):::.</span></span> <span data-ttu-id="3d1be-178">Dieses Szenario ist nützlich, wenn eine Drittanbieter-API Inhalt für das Element bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="3d1be-178">This scenario is useful when a third-party API supplies content to the element.</span></span> <span data-ttu-id="3d1be-179">Da :::no-loc(Blazor)::: nicht mit dem Element interagiert, kann kein Konflikt zwischen der Darstellung von :::no-loc(Blazor)::: des Elements und dem DOM auftreten.</span><span class="sxs-lookup"><span data-stu-id="3d1be-179">Because :::no-loc(Blazor)::: doesn't interact with the element, there's no possibility of a conflict between :::no-loc(Blazor):::'s representation of the element and the DOM.</span></span>
>
> <span data-ttu-id="3d1be-180">Im folgenden Beispiel ist es *gefährlich* , die Inhalte der unsortierten Liste (`ul`) zu ändern, weil :::no-loc(Blazor)::: mit dem DOM interagiert, um die Listenelemente (`<li>`) dieses Elements aufzufüllen:</span><span class="sxs-lookup"><span data-stu-id="3d1be-180">In the following example, it's *dangerous* to mutate the contents of the unordered list (`ul`) because :::no-loc(Blazor)::: interacts with the DOM to populate this element's list items (`<li>`):</span></span>
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
> <span data-ttu-id="3d1be-181">Wenn die Inhalte des Elements `MyList` durch JS Interop geändert werden und :::no-loc(Blazor)::: versucht, diffs-Algorithmen auf das Element anzuwenden, entsprechen diese nicht dem DOM.</span><span class="sxs-lookup"><span data-stu-id="3d1be-181">If JS interop mutates the contents of element `MyList` and :::no-loc(Blazor)::: attempts to apply diffs to the element, the diffs won't match the DOM.</span></span>

<span data-ttu-id="3d1be-182">Eine <xref:Microsoft.AspNetCore.Components.ElementReference> wird über JS Interop an JavaScript-Code übergeben.</span><span class="sxs-lookup"><span data-stu-id="3d1be-182">An <xref:Microsoft.AspNetCore.Components.ElementReference> is passed through to JavaScript code via JS interop.</span></span> <span data-ttu-id="3d1be-183">Der JavaScript-Code empfängt eine `HTMLElement`-Instanz, die er mit normalen DOM-APIs verwenden kann.</span><span class="sxs-lookup"><span data-stu-id="3d1be-183">The JavaScript code receives an `HTMLElement` instance, which it can use with normal DOM APIs.</span></span> <span data-ttu-id="3d1be-184">Im folgenden Code wird beispielsweise eine .NET-Erweiterungsmethode definiert, die das Senden eines Mausklicks an ein Element ermöglicht:</span><span class="sxs-lookup"><span data-stu-id="3d1be-184">For example, the following code defines a .NET extension method that enables sending a mouse click to an element:</span></span>

<span data-ttu-id="3d1be-185">`exampleJsInterop.js`:</span><span class="sxs-lookup"><span data-stu-id="3d1be-185">`exampleJsInterop.js`:</span></span>

```javascript
window.interopFunctions = {
  clickElement : function (element) {
    element.click();
  }
}
```

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="3d1be-186">Verwenden Sie [`FocusAsync`](xref:blazor/components/event-handling#focus-an-element) im C# Code, um ein Element zu fokussieren, das in das :::no-loc(Blazor):::-Framework integriert ist und mit Elementverweisen arbeitet.</span><span class="sxs-lookup"><span data-stu-id="3d1be-186">Use [`FocusAsync`](xref:blazor/components/event-handling#focus-an-element) in C# code to focus an element, which is built-into the :::no-loc(Blazor)::: framework and works with element references.</span></span>

::: moniker-end

<span data-ttu-id="3d1be-187">Verwenden Sie <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType>, um eine JavaScript-Funktion aufzurufen, die keinen Wert zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="3d1be-187">To call a JavaScript function that doesn't return a value, use <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="3d1be-188">Mit dem folgenden Code wird ein clientseitiges `Click`-Ereignis ausgelöst, indem die vorherige JavaScript-Funktion mit dem erfassten <xref:Microsoft.AspNetCore.Components.ElementReference> aufgerufen wird:</span><span class="sxs-lookup"><span data-stu-id="3d1be-188">The following code triggers a client-side `Click` event by calling the preceding JavaScript function with the captured <xref:Microsoft.AspNetCore.Components.ElementReference>:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component1.razor?highlight=14-15)]

<span data-ttu-id="3d1be-189">Erstellen Sie eine statische Erweiterungsmethode, die die <xref:Microsoft.JSInterop.IJSRuntime>-Instanz empfängt, um eine Erweiterungsmethode zu verwenden:</span><span class="sxs-lookup"><span data-stu-id="3d1be-189">To use an extension method, create a static extension method that receives the <xref:Microsoft.JSInterop.IJSRuntime> instance:</span></span>

```csharp
public static async Task TriggerClickEvent(this ElementReference elementRef, 
    IJSRuntime js)
{
    await js.InvokeVoidAsync("interopFunctions.clickElement", elementRef);
}
```

<span data-ttu-id="3d1be-190">Die `clickElement`-Methode wird direkt für das Objekt aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="3d1be-190">The `clickElement` method is called directly on the object.</span></span> <span data-ttu-id="3d1be-191">Im folgenden Beispiel wird davon ausgegangen, dass die `TriggerClickEvent`-Methode vom `JsInteropClasses`-Namespace verfügbar ist:</span><span class="sxs-lookup"><span data-stu-id="3d1be-191">The following example assumes that the `TriggerClickEvent` method is available from the `JsInteropClasses` namespace:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component2.razor?highlight=15)]

> [!IMPORTANT]
> <span data-ttu-id="3d1be-192">Die `exampleButton`-Variable wird erst aufgefüllt, nachdem die Komponente gerendert wurde.</span><span class="sxs-lookup"><span data-stu-id="3d1be-192">The `exampleButton` variable is only populated after the component is rendered.</span></span> <span data-ttu-id="3d1be-193">Wenn ein nicht aufgefüllter <xref:Microsoft.AspNetCore.Components.ElementReference>-Wert an JavaScript-Code übergeben wird, empfängt der JavaScript-Code den Wert `null`.</span><span class="sxs-lookup"><span data-stu-id="3d1be-193">If an unpopulated <xref:Microsoft.AspNetCore.Components.ElementReference> is passed to JavaScript code, the JavaScript code receives a value of `null`.</span></span> <span data-ttu-id="3d1be-194">Sie können Elementverweise bearbeiten, nachdem die Komponente das Rendering abgeschlossen hat, indem Sie die [Lebenszyklusmethoden `OnAfterRenderAsync` oder `OnAfterRender`](xref:blazor/components/lifecycle#after-component-render) verwenden.</span><span class="sxs-lookup"><span data-stu-id="3d1be-194">To manipulate element references after the component has finished rendering use the [`OnAfterRenderAsync` or `OnAfterRender` component lifecycle methods](xref:blazor/components/lifecycle#after-component-render).</span></span>

<span data-ttu-id="3d1be-195">Verwenden Sie <xref:System.Threading.Tasks.ValueTask%601>, wenn Sie mit generischen Typen arbeiten und einen Wert zurückgeben:</span><span class="sxs-lookup"><span data-stu-id="3d1be-195">When working with generic types and returning a value, use <xref:System.Threading.Tasks.ValueTask%601>:</span></span>

```csharp
public static ValueTask<T> GenericMethod<T>(this ElementReference elementRef, 
    IJSRuntime js)
{
    return js.InvokeAsync<T>("exampleJsFunctions.doSomethingGeneric", elementRef);
}
```

<span data-ttu-id="3d1be-196">`GenericMethod` wird direkt für das Objekt mit einem Typ aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="3d1be-196">`GenericMethod` is called directly on the object with a type.</span></span> <span data-ttu-id="3d1be-197">Im folgenden Beispiel wird davon ausgegangen, dass die `GenericMethod`-Methode vom `JsInteropClasses`-Namespace verfügbar ist:</span><span class="sxs-lookup"><span data-stu-id="3d1be-197">The following example assumes that the `GenericMethod` is available from the `JsInteropClasses` namespace:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component3.razor?highlight=17)]

## <a name="reference-elements-across-components"></a><span data-ttu-id="3d1be-198">Komponentenübergreifende Verweiselemente</span><span class="sxs-lookup"><span data-stu-id="3d1be-198">Reference elements across components</span></span>

<span data-ttu-id="3d1be-199">Ein <xref:Microsoft.AspNetCore.Components.ElementReference> kann aus folgenden Gründen nicht zwischen Komponenten übermittelt werden:</span><span class="sxs-lookup"><span data-stu-id="3d1be-199">An <xref:Microsoft.AspNetCore.Components.ElementReference> can't be passed between components because:</span></span>

* <span data-ttu-id="3d1be-200">Das Vorhandensein der Instanz ist nur nach dem Rendern der Komponente garantiert, das während oder nach der Ausführung der <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A>/<xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>-Methode einer Komponente erfolgt.</span><span class="sxs-lookup"><span data-stu-id="3d1be-200">The instance is only guaranteed to exist after the component is rendered, which is during or after a component's <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A>/<xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> method executes.</span></span>
* <span data-ttu-id="3d1be-201">Ein <xref:Microsoft.AspNetCore.Components.ElementReference> ist ein [`struct`](/csharp/language-reference/builtin-types/struct), der nicht als [Komponentenparameter](xref:blazor/components/index#component-parameters) übergeben werden kann.</span><span class="sxs-lookup"><span data-stu-id="3d1be-201">An <xref:Microsoft.AspNetCore.Components.ElementReference> is a [`struct`](/csharp/language-reference/builtin-types/struct), which can't be passed as a [component parameter](xref:blazor/components/index#component-parameters).</span></span>

<span data-ttu-id="3d1be-202">Damit eine übergeordnete Komponente einen Elementverweis anderen Komponenten zur Verfügung stellen kann, kann die übergeordnete Komponente:</span><span class="sxs-lookup"><span data-stu-id="3d1be-202">For a parent component to make an element reference available to other components, the parent component can:</span></span>

* <span data-ttu-id="3d1be-203">zulassen, dass untergeordnete Komponenten Rückrufe registrieren.</span><span class="sxs-lookup"><span data-stu-id="3d1be-203">Allow child components to register callbacks.</span></span>
* <span data-ttu-id="3d1be-204">die registrierten Rückrufe während des <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A>-Ereignisses mit dem übergebenen Elementverweis aufrufen.</span><span class="sxs-lookup"><span data-stu-id="3d1be-204">Invoke the registered callbacks during the <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> event with the passed element reference.</span></span> <span data-ttu-id="3d1be-205">Dieser Ansatz ermöglicht untergeordneten Komponenten indirekt die Interaktion mit dem Elementverweis des übergeordneten Elements.</span><span class="sxs-lookup"><span data-stu-id="3d1be-205">Indirectly, this approach allows child components to interact with the parent's element reference.</span></span>

<span data-ttu-id="3d1be-206">Dieser Ansatz wird anhand des folgenden :::no-loc(Blazor WebAssembly):::-Beispiels veranschaulicht.</span><span class="sxs-lookup"><span data-stu-id="3d1be-206">The following :::no-loc(Blazor WebAssembly)::: example illustrates the approach.</span></span>

<span data-ttu-id="3d1be-207">Im `<head>` von `wwwroot/index.html`:</span><span class="sxs-lookup"><span data-stu-id="3d1be-207">In the `<head>` of `wwwroot/index.html`:</span></span>

```html
<style>
    .red { color: red }
</style>
```

<span data-ttu-id="3d1be-208">Im `<body>` von `wwwroot/index.html`:</span><span class="sxs-lookup"><span data-stu-id="3d1be-208">In the `<body>` of `wwwroot/index.html`:</span></span>

```html
<script>
    function setElementClass(element, className) {
        /** @type {HTMLElement} **/
        var myElement = element;
        myElement.classList.add(className);
    }
</script>
```

<span data-ttu-id="3d1be-209">`Pages/Index.razor` (übergeordnete Komponente):</span><span class="sxs-lookup"><span data-stu-id="3d1be-209">`Pages/Index.razor` (parent component):</span></span>

```razor
@page "/"

<h1 @ref="title">Hello, world!</h1>

Welcome to your new app.

<SurveyPrompt Parent="this" Title="How is :::no-loc(Blazor)::: working for you?" />
```

<span data-ttu-id="3d1be-210">`Pages/Index.razor.cs`:</span><span class="sxs-lookup"><span data-stu-id="3d1be-210">`Pages/Index.razor.cs`:</span></span>

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

<span data-ttu-id="3d1be-211">Der Platzhalter `{APP ASSEMBLY}` ist der App-Assemblyname der App (z. B. `:::no-loc(Blazor):::Sample`).</span><span class="sxs-lookup"><span data-stu-id="3d1be-211">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `:::no-loc(Blazor):::Sample`).</span></span>

<span data-ttu-id="3d1be-212">`Shared/SurveyPrompt.razor` (untergeordnete Komponente):</span><span class="sxs-lookup"><span data-stu-id="3d1be-212">`Shared/SurveyPrompt.razor` (child component):</span></span>

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

<span data-ttu-id="3d1be-213">`Shared/SurveyPrompt.razor.cs`:</span><span class="sxs-lookup"><span data-stu-id="3d1be-213">`Shared/SurveyPrompt.razor.cs`:</span></span>

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

<span data-ttu-id="3d1be-214">Der Platzhalter `{APP ASSEMBLY}` ist der App-Assemblyname der App (z. B. `:::no-loc(Blazor):::Sample`).</span><span class="sxs-lookup"><span data-stu-id="3d1be-214">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `:::no-loc(Blazor):::Sample`).</span></span>

## <a name="harden-js-interop-calls"></a><span data-ttu-id="3d1be-215">Festschreiben von JS Interop-Aufrufen</span><span class="sxs-lookup"><span data-stu-id="3d1be-215">Harden JS interop calls</span></span>

<span data-ttu-id="3d1be-216">JS Interop kann aufgrund von Netzwerkfehlern fehlschlagen und sollte als unzuverlässig behandelt werden.</span><span class="sxs-lookup"><span data-stu-id="3d1be-216">JS interop may fail due to networking errors and should be treated as unreliable.</span></span> <span data-ttu-id="3d1be-217">Standardmäßig weisen :::no-loc(Blazor Server):::-Apps einen Timeout für JS Interop-Aufrufe an den Server nach einer Minute auf.</span><span class="sxs-lookup"><span data-stu-id="3d1be-217">By default, a :::no-loc(Blazor Server)::: app times out JS interop calls on the server after one minute.</span></span> <span data-ttu-id="3d1be-218">Wenn eine App ein engeres Timeout tolerieren kann, legen Sie das Timeout mit einem der folgenden Ansätze fest:</span><span class="sxs-lookup"><span data-stu-id="3d1be-218">If an app can tolerate a more aggressive timeout, set the timeout using one of the following approaches:</span></span>

* <span data-ttu-id="3d1be-219">Legen Sie das Timeout global in `Startup.ConfigureServices` fest:</span><span class="sxs-lookup"><span data-stu-id="3d1be-219">Globally in `Startup.ConfigureServices`, specify the timeout:</span></span>

  ```csharp
  services.AddServerSide:::no-loc(Blazor):::(
      options => options.JSInteropDefaultCallTimeout = TimeSpan.FromSeconds({SECONDS}));
  ```

* <span data-ttu-id="3d1be-220">Pro Aufruf im Komponentencode kann ein einzelner Aufruf das Timeout festlegen:</span><span class="sxs-lookup"><span data-stu-id="3d1be-220">Per-invocation in component code, a single call can specify the timeout:</span></span>

  ```csharp
  var result = await JS.InvokeAsync<string>("MyJSOperation", 
      TimeSpan.FromSeconds({SECONDS}), new[] { "Arg1" });
  ```

<span data-ttu-id="3d1be-221">Weitere Informationen zur Ressourcenauslastung finden Sie unter <xref:blazor/security/server/threat-mitigation>.</span><span class="sxs-lookup"><span data-stu-id="3d1be-221">For more information on resource exhaustion, see <xref:blazor/security/server/threat-mitigation>.</span></span>

[!INCLUDE[](~/includes/blazor-share-interop-code.md)]

## <a name="avoid-circular-object-references"></a><span data-ttu-id="3d1be-222">Vermeiden von Objektzirkelbezügen</span><span class="sxs-lookup"><span data-stu-id="3d1be-222">Avoid circular object references</span></span>

<span data-ttu-id="3d1be-223">Objekte, die Zirkelbezüge enthalten, können auf dem Client für folgende Vorgänge nicht serialisiert werden:</span><span class="sxs-lookup"><span data-stu-id="3d1be-223">Objects that contain circular references can't be serialized on the client for either:</span></span>

* <span data-ttu-id="3d1be-224">.NET-Methodenaufrufe.</span><span class="sxs-lookup"><span data-stu-id="3d1be-224">.NET method calls.</span></span>
* <span data-ttu-id="3d1be-225">JavaScript-Methodenaufrufe von C#, wenn der Rückgabetyp Zirkelbezüge enthält.</span><span class="sxs-lookup"><span data-stu-id="3d1be-225">JavaScript method calls from C# when the return type has circular references.</span></span>

<span data-ttu-id="3d1be-226">Weitere Informationen finden Sie unter den folgenden Problemen:</span><span class="sxs-lookup"><span data-stu-id="3d1be-226">For more information, see the following issues:</span></span>

* [<span data-ttu-id="3d1be-227">Zirkelbezüge werden nicht unterstützt, die Zweite (dotnet/aspnetcore #20525)</span><span class="sxs-lookup"><span data-stu-id="3d1be-227">Circular references are not supported, take two (dotnet/aspnetcore #20525)</span></span>](https://github.com/dotnet/aspnetcore/issues/20525)
* [<span data-ttu-id="3d1be-228">Vorschlag: Hinzufügen eines Mechanismus zur Verarbeitung von Zirkelbezügen bei der Serialisierung (dotnet/runtime #30820)</span><span class="sxs-lookup"><span data-stu-id="3d1be-228">Proposal: Add mechanism to handle circular references when serializing (dotnet/runtime #30820)</span></span>](https://github.com/dotnet/runtime/issues/30820)

::: moniker range=">= aspnetcore-5.0"

## <a name="no-locblazor-javascript-isolation-and-object-references"></a><span data-ttu-id="3d1be-229">:::no-loc(Blazor):::-JavaScript-Isolation und Objektverweise</span><span class="sxs-lookup"><span data-stu-id="3d1be-229">:::no-loc(Blazor)::: JavaScript isolation and object references</span></span>

<span data-ttu-id="3d1be-230">:::no-loc(Blazor)::: aktiviert JavaScript-Isolierung in [JavaScript-Standardmodulen](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules).</span><span class="sxs-lookup"><span data-stu-id="3d1be-230">:::no-loc(Blazor)::: enables JavaScript isolation in standard [JavaScript modules](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules).</span></span> <span data-ttu-id="3d1be-231">JavaScript-Isolierung bietet die folgenden Vorteile:</span><span class="sxs-lookup"><span data-stu-id="3d1be-231">JavaScript isolation provides the following benefits:</span></span>

* <span data-ttu-id="3d1be-232">Importiertes JavaScript verschmutzt nicht mehr den globalen Namespace.</span><span class="sxs-lookup"><span data-stu-id="3d1be-232">Imported JavaScript no longer pollutes the global namespace.</span></span>
* <span data-ttu-id="3d1be-233">Consumer einer Bibliothek und Komponenten müssen nicht das zugehörige JavaScript importieren.</span><span class="sxs-lookup"><span data-stu-id="3d1be-233">Consumers of a library and components aren't required to import the related JavaScript.</span></span>

<span data-ttu-id="3d1be-234">Das folgende JavaScript-Modul exportiert z. B. eine JavaScript-Funktion, um eine Browsereingabeaufforderung anzuzeigen:</span><span class="sxs-lookup"><span data-stu-id="3d1be-234">For example, the following JavaScript module exports a JavaScript function for showing a browser prompt:</span></span>

```javascript
export function showPrompt(message) {
  return prompt(message, 'Type anything here');
}
```

<span data-ttu-id="3d1be-235">Fügen Sie das oben gezeigte JavaScript-Modul einer .NET-Bibliothek als statische Webressource (`wwwroot/exampleJsInterop.js`) hinzu, und importieren Sie das Modul dann mithilfe des <xref:Microsoft.JSInterop.IJSRuntime>-Diensts in den .NET-Code.</span><span class="sxs-lookup"><span data-stu-id="3d1be-235">Add the preceding JavaScript module to a .NET library as a static web asset (`wwwroot/exampleJsInterop.js`) and then import the module into the .NET code using the <xref:Microsoft.JSInterop.IJSRuntime> service.</span></span> <span data-ttu-id="3d1be-236">Der Dienst wird im folgenden Beispiel als `js` (nicht gezeigt) eingefügt:</span><span class="sxs-lookup"><span data-stu-id="3d1be-236">The service is injected as `js` (not shown) for the following example:</span></span>

```csharp
var module = await js.InvokeAsync<IJSObjectReference>(
    "import", "./_content/MyComponents/exampleJsInterop.js");
```

<span data-ttu-id="3d1be-237">Der Bezeichner `import` im Beispiel oben ist ein spezieller Bezeichner, der insbesondere zum Importieren eines JavaScript-Moduls verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="3d1be-237">The `import` identifier in the preceding example is a special identifier used specifically for importing a JavaScript module.</span></span> <span data-ttu-id="3d1be-238">Geben Sie das Modul mithilfe seines stabilen statischen Webressourcenpfads an: `_content/{LIBRARY NAME}/{PATH UNDER WWWROOT}`.</span><span class="sxs-lookup"><span data-stu-id="3d1be-238">Specify the module using its stable static web asset path: `_content/{LIBRARY NAME}/{PATH UNDER WWWROOT}`.</span></span> <span data-ttu-id="3d1be-239">Der Platzhalter `{LIBRARY NAME}` ist der Name der Bibliothek.</span><span class="sxs-lookup"><span data-stu-id="3d1be-239">The placeholder `{LIBRARY NAME}` is the library name.</span></span> <span data-ttu-id="3d1be-240">Der Platzhalter `{PATH UNDER WWWROOT}` ist der Pfad zum Skript unter `wwwroot`.</span><span class="sxs-lookup"><span data-stu-id="3d1be-240">The placeholder `{PATH UNDER WWWROOT}` is the path to the script under `wwwroot`.</span></span>

<span data-ttu-id="3d1be-241"><xref:Microsoft.JSInterop.IJSRuntime> importiert das Modul als `IJSObjectReference`-Element, das einen Verweis auf ein JavaScript-Objekt aus .NET-Code darstellt.</span><span class="sxs-lookup"><span data-stu-id="3d1be-241"><xref:Microsoft.JSInterop.IJSRuntime> imports the module as a `IJSObjectReference`, which represents a reference to a JavaScript object from .NET code.</span></span> <span data-ttu-id="3d1be-242">Verwenden Sie `IJSObjectReference`, um exportierte JavaScript-Funktionen aus dem Modul aufzurufen:</span><span class="sxs-lookup"><span data-stu-id="3d1be-242">Use the `IJSObjectReference` to invoke exported JavaScript functions from the module:</span></span>

```csharp
public async ValueTask<string> Prompt(string message)
{
    return await module.InvokeAsync<string>("showPrompt", message);
}
```

<span data-ttu-id="3d1be-243">`IJSInProcessObjectReference` steht für einen Verweis auf ein JavaScript-Objekt, dessen Funktionen synchron aufgerufen werden können.</span><span class="sxs-lookup"><span data-stu-id="3d1be-243">`IJSInProcessObjectReference` represents a reference to a JavaScript object whose functions can be invoked synchronously.</span></span>

<span data-ttu-id="3d1be-244">`IJSUnmarshalledObjectReference` steht für einen Verweis auf ein JavaScript-Objekt, dessen Funktionen aufgerufen werden können, ohne dass .NET-Daten serialisiert werden müssen.</span><span class="sxs-lookup"><span data-stu-id="3d1be-244">`IJSUnmarshalledObjectReference` represents a reference to an JavaScript object whose functions can be invoked without the overhead of serializing .NET data.</span></span> <span data-ttu-id="3d1be-245">Die Verwendung in :::no-loc(Blazor WebAssembly)::: bietet sich an, wenn die Leistung entscheidend ist:</span><span class="sxs-lookup"><span data-stu-id="3d1be-245">This can be used in :::no-loc(Blazor WebAssembly)::: when performance is crucial:</span></span>

```javascript
window.unmarshalledInstance = {
  helloWorld: function (personNamePointer) {
    const personName = :::no-loc(Blazor):::.platform.readStringField(value, 0);
    return `Hello ${personName}`;
  }
};
```

```csharp
var unmarshalledRuntime = (IJSUnmarshalledRuntime)js;
var jsUnmarshalledReference = unmarshalledRuntime
    .InvokeUnmarshalled<IJSUnmarshalledObjectReference>("unmarshalledInstance");

string helloWorldString = jsUnmarshalledReference.InvokeUnmarshalled<string, string>(
    "helloWorld");
```

<span data-ttu-id="3d1be-246">Im vorangehenden Beispiel wird der Dienst <xref:Microsoft.JSInterop.IJSRuntime> in die Klasse injiziert und `js` zugewiesen (nicht gezeigt).</span><span class="sxs-lookup"><span data-stu-id="3d1be-246">In the preceding example, the <xref:Microsoft.JSInterop.IJSRuntime> service is injected into the class and assigned to `js` (not shown).</span></span>

## <a name="use-of-javascript-libraries-that-render-ui-dom-elements"></a><span data-ttu-id="3d1be-247">Verwendung von JavaScript-Bibliotheken zum Rendern von Benutzeroberflächenelementen (DOM-Elemente)</span><span class="sxs-lookup"><span data-stu-id="3d1be-247">Use of JavaScript libraries that render UI (DOM elements)</span></span>

<span data-ttu-id="3d1be-248">Manchmal möchten Sie vielleicht JavaScript-Bibliotheken verwenden, die sichtbare Benutzeroberflächenelemente innerhalb des Browser-DOM erstellen.</span><span class="sxs-lookup"><span data-stu-id="3d1be-248">Sometimes you may wish to use JavaScript libraries that produce visible user interface elements within the browser DOM.</span></span> <span data-ttu-id="3d1be-249">Auf den ersten Blick scheint dies schwierig zu sein, da das Vergleichssystem von :::no-loc(Blazor)::: darauf beruht, die Kontrolle über die Struktur von DOM-Elementen zu haben. Außerdem treten Fehler auf, wenn externer Code die DOM-Struktur bearbeitet, was den Mechanismus für die Diff-Anwendung unmöglich macht.</span><span class="sxs-lookup"><span data-stu-id="3d1be-249">At first glance, this might seem difficult because :::no-loc(Blazor):::'s diffing system relies on having control over the tree of DOM elements and runs into errors if some external code mutates the DOM tree and invalidates its mechanism for applying diffs.</span></span> <span data-ttu-id="3d1be-250">Dabei handelt es sich um keine :::no-loc(Blazor):::-spezifische Einschränkung.</span><span class="sxs-lookup"><span data-stu-id="3d1be-250">This isn't a :::no-loc(Blazor):::-specific limitation.</span></span> <span data-ttu-id="3d1be-251">Die gleiche Herausforderung stellt sich bei jedem anderen Diff-basierten Benutzeroberflächenframework.</span><span class="sxs-lookup"><span data-stu-id="3d1be-251">The same challenge occurs with any diff-based UI framework.</span></span>

<span data-ttu-id="3d1be-252">Glücklicherweise ist es einfach, extern erstellte Benutzeroberflächenelemente innerhalb der Oberfläche einer :::no-loc(Blazor):::-Komponente zuverlässig einzubetten.</span><span class="sxs-lookup"><span data-stu-id="3d1be-252">Fortunately, it's straightforward to embed externally-generated UI within a :::no-loc(Blazor)::: component UI reliably.</span></span> <span data-ttu-id="3d1be-253">Die hierfür empfohlene Methode besteht darin, den Code der Komponente (`.razor`-Datei) ein leeres Element erzeugen zu lassen.</span><span class="sxs-lookup"><span data-stu-id="3d1be-253">The recommended technique is to have the component's code (`.razor` file) produce an empty element.</span></span> <span data-ttu-id="3d1be-254">Im Vergleichssystem von :::no-loc(Blazor)::: ist das Element immer leer, der Renderer durchläuft das Element also nicht rekursiv, sondern berücksichtigt die Inhalte gar nicht.</span><span class="sxs-lookup"><span data-stu-id="3d1be-254">As far as :::no-loc(Blazor):::'s diffing system is concerned, the element is always empty, so the renderer does not recurse into the element and instead leaves its contents alone.</span></span> <span data-ttu-id="3d1be-255">Dies führt dazu, dass das Auffüllen des Elements mit zufälligem extern verwalteten Inhalt sicher ist.</span><span class="sxs-lookup"><span data-stu-id="3d1be-255">This makes it safe to populate the element with arbitrary externally-managed content.</span></span>

<span data-ttu-id="3d1be-256">Das folgende Beispiel veranschaulicht das Konzept.</span><span class="sxs-lookup"><span data-stu-id="3d1be-256">The following example demonstrates the concept.</span></span> <span data-ttu-id="3d1be-257">Wenn innerhalb der `if`-Anweisung `firstRender` `true` ist, kann `myElement` genutzt werden.</span><span class="sxs-lookup"><span data-stu-id="3d1be-257">Within the `if` statement when `firstRender` is `true`, do something with `myElement`.</span></span> <span data-ttu-id="3d1be-258">Beispielsweise wird für die Auffüllung eine externe JavaScript-Bibliothek aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="3d1be-258">For example, call an external JavaScript library to populate it.</span></span> <span data-ttu-id="3d1be-259">:::no-loc(Blazor)::: berücksichtigt die Inhalte des Elements solange nicht, bis die Komponente selbst entfernt wird.</span><span class="sxs-lookup"><span data-stu-id="3d1be-259">:::no-loc(Blazor)::: leaves the element's contents alone until this component itself is removed.</span></span> <span data-ttu-id="3d1be-260">Wenn die Komponente entfernt wird, wird die gesamte DOM-Unterstruktur der Komponente ebenfalls entfernt.</span><span class="sxs-lookup"><span data-stu-id="3d1be-260">When the component is removed, the component's entire DOM subtree is also removed.</span></span>

```razor
<h1>Hello! This is a :::no-loc(Blazor)::: component rendered at @DateTime.Now</h1>

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

<span data-ttu-id="3d1be-261">Als detailliertes Beispiel können Sie sich die folgende Komponente ansehen, die eine interaktive Karte mithilfe der [Open-Source-Mapbox-APIs](https://www.mapbox.com/) rendert:</span><span class="sxs-lookup"><span data-stu-id="3d1be-261">As a more detailed example, consider the following component that renders an interactive map using the [open-source Mapbox APIs](https://www.mapbox.com/):</span></span>

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

<span data-ttu-id="3d1be-262">Das entsprechende JavaScript-Modul, das unter `wwwroot/mapComponent.js` platziert werden sollte, sieht folgendermaßen aus:</span><span class="sxs-lookup"><span data-stu-id="3d1be-262">The corresponding JavaScript module, which should be placed at `wwwroot/mapComponent.js`, is as follows:</span></span>

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

<span data-ttu-id="3d1be-263">Ersetzen Sie im vorherigen Beispiel die Zeichenfolge `{ACCESS TOKEN}` durch ein gültiges Zugriffstoken, das Sie von https://account.mapbox.com abrufen können.</span><span class="sxs-lookup"><span data-stu-id="3d1be-263">In the preceding example, replace the string `{ACCESS TOKEN}` with a valid access token that you can get from https://account.mapbox.com.</span></span>

<span data-ttu-id="3d1be-264">Für eine korrekte Formatierung fügen Sie der HTML-Hostseite den folgenden Stylesheet-Tag hinzu (`index.html` oder `_Host.cshtml`):</span><span class="sxs-lookup"><span data-stu-id="3d1be-264">To produce correct styling, add the following stylesheet tag to the host HTML page (`index.html` or `_Host.cshtml`):</span></span>

```html
<link rel="stylesheet" href="https://api.mapbox.com/mapbox-gl-js/v1.12.0/mapbox-gl.css" />
```

<span data-ttu-id="3d1be-265">Das vorangehende Beispiel erzeugt eine interaktive Kartenbenutzeroberfläche, auf der der Benutzer folgende Möglichkeiten hat:</span><span class="sxs-lookup"><span data-stu-id="3d1be-265">The preceding example produces an interactive map UI, in which the user:</span></span>

* <span data-ttu-id="3d1be-266">Ziehen der Karte zum Scrollen oder Zoomen</span><span class="sxs-lookup"><span data-stu-id="3d1be-266">Can drag to scroll or zoom.</span></span>
* <span data-ttu-id="3d1be-267">Klicken auf Schaltflächen, um zu vordefinierten Standorten zu springen</span><span class="sxs-lookup"><span data-stu-id="3d1be-267">Click buttons to jump to predefined locations.</span></span>

![Mapbox-Straßenkarte von Tokio in Japan, mit Schaltflächen, über die Bristol im Vereinigten Königreich und Tokio in Japan ausgewählt werden können](https://user-images.githubusercontent.com/1101362/94939821-92ef6700-04ca-11eb-858e-fff6df0053ae.png)

<span data-ttu-id="3d1be-269">Die wichtigsten Punkte, die verstanden werden müssen, sind die folgenden:</span><span class="sxs-lookup"><span data-stu-id="3d1be-269">The key points to understand are:</span></span>

 * <span data-ttu-id="3d1be-270">Für :::no-loc(Blazor)::: gilt, dass `<div>` mit `@ref="mapElement"` leer bleibt.</span><span class="sxs-lookup"><span data-stu-id="3d1be-270">The `<div>` with `@ref="mapElement"` is left empty as far as :::no-loc(Blazor)::: is concerned.</span></span> <span data-ttu-id="3d1be-271">Deshalb kann die Auffüllung durch `mapbox-gl.js` sicher erfolgen, und die Inhalte können im Laufe der Zeit bearbeitet werden.</span><span class="sxs-lookup"><span data-stu-id="3d1be-271">It's therefore safe for `mapbox-gl.js` to populate it and modify its contents over time.</span></span> <span data-ttu-id="3d1be-272">Sie können dieses Vorgehen für alle JavaScript-Bibliotheken verwenden, die Benutzeroberflächen rendern.</span><span class="sxs-lookup"><span data-stu-id="3d1be-272">You can use this technique with any JavaScript library that renders UI.</span></span> <span data-ttu-id="3d1be-273">Sie könnten sogar Komponenten des JavaScript-SPA-Frameworks eines Drittanbieters innerhalb der :::no-loc(Blazor):::-Komponenten einbetten, solange nicht aktiv versucht wird, andere Teile der Seite zu ändern.</span><span class="sxs-lookup"><span data-stu-id="3d1be-273">You could even embed components from a third-party JavaScript SPA framework inside :::no-loc(Blazor)::: components, as long as they don't try to reach out and modify other parts of the page.</span></span> <span data-ttu-id="3d1be-274">Es ist *nicht* sicher, wenn externer JavaScript-Code Elemente bearbeitet, die von :::no-loc(Blazor)::: nicht als leer klassifiziert werden.</span><span class="sxs-lookup"><span data-stu-id="3d1be-274">It is *not* safe for external JavaScript code to modify elements that :::no-loc(Blazor)::: does not regard as empty.</span></span>
 * <span data-ttu-id="3d1be-275">Bei Verwendung dieses Ansatzes sollten Sie die Regeln im Hinterkopf behalten, wie :::no-loc(Blazor)::: DOM-Elemente beibehält oder löscht.</span><span class="sxs-lookup"><span data-stu-id="3d1be-275">When using this approach, bear in mind the rules about how :::no-loc(Blazor)::: retains or destroys DOM elements.</span></span> <span data-ttu-id="3d1be-276">Im vorherigen Beispiel kann die Komponente Klickereignisse auf Schaltflächen sicher verarbeiten und die vorhandene Karteninstanz aktualisieren, da DOM-Elemente standardmäßig beibehalten werden, wo dies möglich ist.</span><span class="sxs-lookup"><span data-stu-id="3d1be-276">In the preceding example, the component safely handles button click events and updates the existing map instance because DOM elements are retained where possible by default.</span></span> <span data-ttu-id="3d1be-277">Wenn Sie eine Liste von Kartenelementen innerhalb einer `@foreach`-Schleife rendern, sollten Sie `@key` verwenden, um für die Beibehaltung der Komponenteninstanzen zu sorgen.</span><span class="sxs-lookup"><span data-stu-id="3d1be-277">If you were rendering a list of map elements from inside a `@foreach` loop, you want to use `@key` to ensure the preservation of component instances.</span></span> <span data-ttu-id="3d1be-278">Andernfalls könnten Änderungen der Listendaten dazu führen, dass Komponenteninstanzen den Status vorheriger Instanzen auf nicht gewünschte Weise beibehalten.</span><span class="sxs-lookup"><span data-stu-id="3d1be-278">Otherwise, changes in the list data could cause component instances to retain the state of previous instances in an undesirable manner.</span></span> <span data-ttu-id="3d1be-279">Weitere Informationen finden Sie unter [Verwenden von @key zur Beibehaltung von Elementen und Komponenten](xref:blazor/components/index#use-key-to-control-the-preservation-of-elements-and-components).</span><span class="sxs-lookup"><span data-stu-id="3d1be-279">For more information, see [using @key to preserve elements and components](xref:blazor/components/index#use-key-to-control-the-preservation-of-elements-and-components).</span></span>

<span data-ttu-id="3d1be-280">Außerdem zeigt das vorherige Beispiel, wie es möglich ist, JavaScript-Logik und -Abhängigkeiten innerhalb eines ES6-Moduls zu kapseln und dynamisch mithilfe des `import`-Bezeichners zu laden.</span><span class="sxs-lookup"><span data-stu-id="3d1be-280">Additionally, the preceding example shows how it's possible to encapsulate JavaScript logic and dependencies within an ES6 module and load it dynamically using the `import` identifier.</span></span> <span data-ttu-id="3d1be-281">Weitere Informationen finden Sie unter [JavaScript-Isolierung in Blazor und Objektverweise](#blazor-javascript-isolation-and-object-references).</span><span class="sxs-lookup"><span data-stu-id="3d1be-281">For more information, see [JavaScript isolation and object references](#blazor-javascript-isolation-and-object-references).</span></span>

::: moniker-end

## <a name="size-limits-on-js-interop-calls"></a><span data-ttu-id="3d1be-282">Größenbeschränkungen bei JS-Interop-Aufrufen</span><span class="sxs-lookup"><span data-stu-id="3d1be-282">Size limits on JS interop calls</span></span>

<span data-ttu-id="3d1be-283">In :::no-loc(Blazor WebAssembly)::: gibt das Framework keine Beschränkungen hinsichtlich der Größe von Ein- und Ausgaben von JS-Interop-Aufrufen vor.</span><span class="sxs-lookup"><span data-stu-id="3d1be-283">In :::no-loc(Blazor WebAssembly):::, the framework doesn't impose limits on the size of inputs and outputs of JS interop calls.</span></span>

<span data-ttu-id="3d1be-284">In :::no-loc(Blazor Server)::: wird das Ergebnis eines JS-Interop-Aufrufs durch die maximale Payloadgröße beschränkt, die von :::no-loc(SignalR)::: (<xref:Microsoft.AspNetCore.:::no-loc(SignalR):::.HubOptions.MaximumReceiveMessageSize>) erzwungen wird. Der Standardwert ist 32 KB.</span><span class="sxs-lookup"><span data-stu-id="3d1be-284">In :::no-loc(Blazor Server):::, the result of a JS interop call is limited by the maximum payload size enforced by :::no-loc(SignalR)::: (<xref:Microsoft.AspNetCore.:::no-loc(SignalR):::.HubOptions.MaximumReceiveMessageSize>), which defaults to 32 KB.</span></span> <span data-ttu-id="3d1be-285">Anwendungen, die versuchen, mit einer Payload über <xref:Microsoft.AspNetCore.:::no-loc(SignalR):::.HubOptions.MaximumReceiveMessageSize> auf einen JS-Interop-Befehl zu reagieren, lösen einen Fehler aus.</span><span class="sxs-lookup"><span data-stu-id="3d1be-285">Applications that attempt to respond to a JS interop call with a payload larger than <xref:Microsoft.AspNetCore.:::no-loc(SignalR):::.HubOptions.MaximumReceiveMessageSize> throw an error.</span></span> <span data-ttu-id="3d1be-286">Eine höherer Grenzwert kann durch Ändern von <xref:Microsoft.AspNetCore.:::no-loc(SignalR):::.HubOptions.MaximumReceiveMessageSize> konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="3d1be-286">A larger limit can be configured by modifying <xref:Microsoft.AspNetCore.:::no-loc(SignalR):::.HubOptions.MaximumReceiveMessageSize>.</span></span> <span data-ttu-id="3d1be-287">Im folgenden Beispiel wird die maximale Größe eingehender Nachrichten auf 64 KB (64 × 1.024 × 1.024) festgelegt:</span><span class="sxs-lookup"><span data-stu-id="3d1be-287">The following example sets the maximum receive message size to 64 KB (64 \* 1024 \* 1024):</span></span>

```csharp
services.AddServerSide:::no-loc(Blazor):::()
   .AddHubOptions(options => options.MaximumReceiveMessageSize = 64 * 1024 * 1024);
```

<span data-ttu-id="3d1be-288">Wenn Sie den Grenzwert von :::no-loc(SignalR)::: erhöhen, verbrauchen Sie auch mehr Serverressourcen und setzen den Server einem erhöhten Risiko durch böswillige Benutzer aus.</span><span class="sxs-lookup"><span data-stu-id="3d1be-288">Increasing the :::no-loc(SignalR)::: limit comes at the cost of requiring the use of more server resources, and it exposes the server to increased risks from a malicious user.</span></span> <span data-ttu-id="3d1be-289">Darüber hinaus kann das Lesen sehr großer Inhalte in den Arbeitsspeicher als Zeichenfolgen oder Bytearrays zu Zuordnungen führen, die vom Garbage Collector nur schlecht verarbeitet werden können. Dies kann zu zusätzlichen Leistungseinbußen führen.</span><span class="sxs-lookup"><span data-stu-id="3d1be-289">Additionally, reading a large amount of content in to memory as strings or byte arrays can also result in allocations that work poorly with the garbage collector, resulting in additional performance penalties.</span></span> <span data-ttu-id="3d1be-290">Eine Möglichkeit zum Lesen großer Payloads besteht darin, den Inhalt in kleineren Blöcken zu senden und die Nutzdaten als <xref:System.IO.Stream> zu verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="3d1be-290">One option for reading large payloads is to consider sending the content in smaller chunks and processing the payload as a <xref:System.IO.Stream>.</span></span> <span data-ttu-id="3d1be-291">Diesen Ansatz können Sie anwenden, wenn große JSON-Payloads gelesen werden oder die Daten in JavaScript als unformatierte Bytes verfügbar sind.</span><span class="sxs-lookup"><span data-stu-id="3d1be-291">This can be used when reading large JSON payloads or if data is available in JavaScript as raw bytes.</span></span> <span data-ttu-id="3d1be-292">Ein Beispiel für das Senden großer binärer Payloads in :::no-loc(Blazor Server):::, bei dem ähnliche Techniken wie die `InputFile`-Komponente verwendet werden, finden Sie unter der [Beispiel-App BinarySubmit](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/BinarySubmit).</span><span class="sxs-lookup"><span data-stu-id="3d1be-292">For an example that demonstrates sending large binary payloads in :::no-loc(Blazor Server)::: that uses techniques similar to the `InputFile` component, see the [Binary Submit sample app](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/BinarySubmit).</span></span>

<span data-ttu-id="3d1be-293">Beachten Sie die folgenden Anleitungen, wenn Sie Code zum Übertragen großer Datenmengen zwischen JavaScript und :::no-loc(Blazor)::: entwickeln:</span><span class="sxs-lookup"><span data-stu-id="3d1be-293">Consider the following guidance when developing code that transfers a large amount of data between JavaScript and :::no-loc(Blazor)::::</span></span>

* <span data-ttu-id="3d1be-294">Segmentieren Sie die Daten in kleinere Teile, und senden Sie die Datensegmente sequenziell, bis alle Daten vom Server empfangen wurden.</span><span class="sxs-lookup"><span data-stu-id="3d1be-294">Slice the data into smaller pieces, and send the data segments sequentially until all of the data is received by the server.</span></span>
* <span data-ttu-id="3d1be-295">Ordnen Sie in JavaScript- und C#-Code keine großen Objekte zu.</span><span class="sxs-lookup"><span data-stu-id="3d1be-295">Don't allocate large objects in JavaScript and C# code.</span></span>
* <span data-ttu-id="3d1be-296">Blockieren Sie den hauptsächlichen Benutzeroberflächenthread nicht für lange Zeiträume, wenn Sie Daten senden oder empfangen.</span><span class="sxs-lookup"><span data-stu-id="3d1be-296">Don't block the main UI thread for long periods when sending or receiving data.</span></span>
* <span data-ttu-id="3d1be-297">Geben Sie belegten Arbeitsspeicher frei, wenn der Prozess abgeschlossen oder abgebrochen wird.</span><span class="sxs-lookup"><span data-stu-id="3d1be-297">Free any memory consumed when the process is completed or cancelled.</span></span>
* <span data-ttu-id="3d1be-298">Erzwingen Sie die folgenden zusätzlichen Anforderungen aus Sicherheitsgründen:</span><span class="sxs-lookup"><span data-stu-id="3d1be-298">Enforce the following additional requirements for security purposes:</span></span>
  * <span data-ttu-id="3d1be-299">Deklarieren Sie die maximale Datei- oder Datengröße, die übermittelt werden kann.</span><span class="sxs-lookup"><span data-stu-id="3d1be-299">Declare the maximum file or data size that can be passed.</span></span>
  * <span data-ttu-id="3d1be-300">Deklarieren Sie die minimale Uploadrate vom Client an den Server.</span><span class="sxs-lookup"><span data-stu-id="3d1be-300">Declare the minimum upload rate from the client to the server.</span></span>
* <span data-ttu-id="3d1be-301">Nachdem die Daten vom Server empfangen wurden, ist mit den Daten Folgendes möglich:</span><span class="sxs-lookup"><span data-stu-id="3d1be-301">After the data is received by the server, the data can be:</span></span>
  * <span data-ttu-id="3d1be-302">Sie können temporär in einem Speicherpuffer gespeichert werden, bis alle Segmente gesammelt wurden.</span><span class="sxs-lookup"><span data-stu-id="3d1be-302">Temporarily stored in a memory buffer until all of the segments are collected.</span></span>
  * <span data-ttu-id="3d1be-303">Sie können sofort verarbeitet werden.</span><span class="sxs-lookup"><span data-stu-id="3d1be-303">Consumed immediately.</span></span> <span data-ttu-id="3d1be-304">Beispielsweise können die Daten sofort in einer Datenbank gespeichert oder auf den Datenträger geschrieben werden, wenn die einzelnen Segmente empfangen werden.</span><span class="sxs-lookup"><span data-stu-id="3d1be-304">For example, the data can be stored immediately in a database or written to disk as each segment is received.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3d1be-305">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="3d1be-305">Additional resources</span></span>

* <xref:blazor/call-dotnet-from-javascript>
* [<span data-ttu-id="3d1be-306">InteropComponent.razor-Beispiel (dotnet/AspNetCore-GitHub-Repository, Releasebranch 3.1)</span><span class="sxs-lookup"><span data-stu-id="3d1be-306">InteropComponent.razor example (dotnet/AspNetCore GitHub repository, 3.1 release branch)</span></span>](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
