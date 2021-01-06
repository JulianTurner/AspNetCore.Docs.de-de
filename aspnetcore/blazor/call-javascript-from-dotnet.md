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
ms.openlocfilehash: 11312a34dc62dd3bace791819f62379bffbb1c49
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/04/2021
ms.locfileid: "97592834"
---
# <a name="call-javascript-functions-from-net-methods-in-aspnet-core-no-locblazor"></a><span data-ttu-id="0b1e0-103">Aufrufen von JavaScript-Funktionen über .NET-Methoden in ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="0b1e0-103">Call JavaScript functions from .NET methods in ASP.NET Core Blazor</span></span>

<span data-ttu-id="0b1e0-104">Von [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), [Pranav Krishnamoorthy](https://github.com/pranavkm) und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="0b1e0-104">By [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), [Pranav Krishnamoorthy](https://github.com/pranavkm), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="0b1e0-105">Eine Blazor-App kann JavaScript-Funktionen über .NET-Methoden und .NET-Methoden über JavaScript-Funktionen aufrufen.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-105">A Blazor app can invoke JavaScript functions from .NET methods and .NET methods from JavaScript functions.</span></span> <span data-ttu-id="0b1e0-106">Diese Szenarios werden als *JavaScript-Interoperabilität* (*JS Interop*) bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-106">These scenarios are called *JavaScript interoperability* (*JS interop*).</span></span>

<span data-ttu-id="0b1e0-107">In diesem Artikel wird das Aufrufen von JavaScript-Funktionen über .NET behandelt.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-107">This article covers invoking JavaScript functions from .NET.</span></span> <span data-ttu-id="0b1e0-108">Weitere Informationen zum Aufrufen von .NET-Methoden über JavaScript finden Sie unter <xref:blazor/call-dotnet-from-javascript>.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-108">For information on how to call .NET methods from JavaScript, see <xref:blazor/call-dotnet-from-javascript>.</span></span>

<span data-ttu-id="0b1e0-109">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="0b1e0-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="0b1e0-110">Verwenden Sie die <xref:Microsoft.JSInterop.IJSRuntime>-Abstraktion, um JavaScript über .NET aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-110">To call into JavaScript from .NET, use the <xref:Microsoft.JSInterop.IJSRuntime> abstraction.</span></span> <span data-ttu-id="0b1e0-111">Fügen Sie die <xref:Microsoft.JSInterop.IJSRuntime>-Abstraktion in Ihre Komponente ein, um JS Interop-Aufrufe auszugeben.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-111">To issue JS interop calls, inject the <xref:Microsoft.JSInterop.IJSRuntime> abstraction in your component.</span></span> <span data-ttu-id="0b1e0-112"><xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> akzeptiert einen Bezeichner für die JavaScript-Funktion, die Sie aufrufen möchten, sowie eine beliebige Anzahl JSON-serialisierbarer Argumente.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-112"><xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> takes an identifier for the JavaScript function that you wish to invoke along with any number of JSON-serializable arguments.</span></span> <span data-ttu-id="0b1e0-113">Der Funktionsbezeichner ist relativ zum globalen Bereich (`window`).</span><span class="sxs-lookup"><span data-stu-id="0b1e0-113">The function identifier is relative to the global scope (`window`).</span></span> <span data-ttu-id="0b1e0-114">Wenn Sie `window.someScope.someFunction` aufrufen möchten, lautet der Bezeichner `someScope.someFunction`.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-114">If you wish to call `window.someScope.someFunction`, the identifier is `someScope.someFunction`.</span></span> <span data-ttu-id="0b1e0-115">Die Funktion muss nicht registriert werden, bevor sie aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-115">There's no need to register the function before it's called.</span></span> <span data-ttu-id="0b1e0-116">Der Rückgabetyp `T` muss ebenfalls JSON-serialisierbar sein.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-116">The return type `T` must also be JSON serializable.</span></span> <span data-ttu-id="0b1e0-117">`T` sollte mit dem .NET-Typ übereinstimmen, der dem zurückgegebenen JSON-Typ am ehesten entspricht.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-117">`T` should match the .NET type that best maps to the JSON type returned.</span></span>

<span data-ttu-id="0b1e0-118">JavaScript-Funktionen, die eine [Zusage](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) zurückgeben, werden mit <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-118">JavaScript functions that return a [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) are called with <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A>.</span></span> <span data-ttu-id="0b1e0-119">`InvokeAsync` entpackt die Zusage und gibt den Wert zurück, der von der Zusage erwartet wird.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-119">`InvokeAsync` unwraps the Promise and returns the value awaited by the Promise.</span></span>

<span data-ttu-id="0b1e0-120">Bei Blazor Server-Apps mit aktiviertem Prerendering ist das Aufrufen von JavaScript beim ersten Prerendering nicht möglich.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-120">For Blazor Server apps with prerendering enabled, calling into JavaScript isn't possible during the initial prerendering.</span></span> <span data-ttu-id="0b1e0-121">JavaScript-Interoperabilitätsaufrufe müssen verzögert werden, bis die Verbindung mit dem Browser hergestellt ist.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-121">JavaScript interop calls must be deferred until after the connection with the browser is established.</span></span> <span data-ttu-id="0b1e0-122">Weitere Informationen finden Sie im Abschnitt [Erkennen, wenn für eine Blazor Server-App ein Prerendering durchgeführt wird](#detect-when-a-blazor-server-app-is-prerendering).</span><span class="sxs-lookup"><span data-stu-id="0b1e0-122">For more information, see the [Detect when a Blazor Server app is prerendering](#detect-when-a-blazor-server-app-is-prerendering) section.</span></span>

<span data-ttu-id="0b1e0-123">Das folgende Beispiel basiert auf [`TextDecoder`](https://developer.mozilla.org/docs/Web/API/TextDecoder), einem auf JavaScript basierenden Decoder.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-123">The following example is based on [`TextDecoder`](https://developer.mozilla.org/docs/Web/API/TextDecoder), a JavaScript-based decoder.</span></span> <span data-ttu-id="0b1e0-124">Im Beispiel wird veranschaulicht, wie eine JavaScript-Funktion aus einer C#-Methode aufgerufen wird, die eine Anforderung aus Entwicklercode in eine vorhandene JavaScript-API auslagert.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-124">The example demonstrates how to invoke a JavaScript function from a C# method that offloads a requirement from developer code to an existing JavaScript API.</span></span> <span data-ttu-id="0b1e0-125">Die JavaScript-Funktion akzeptiert ein Bytearray von einer C#-Methode, decodiert das Array und gibt den Text zum Anzeigen an die Komponente zurück.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-125">The JavaScript function accepts a byte array from a C# method, decodes the array, and returns the text to the component for display.</span></span>

<span data-ttu-id="0b1e0-126">Geben Sie im `<head>`-Element von `wwwroot/index.html` (Blazor WebAssembly) oder `Pages/_Host.cshtml` (Blazor Server) eine JavaScript-Funktion an, die `TextDecoder` verwendet, um ein übermitteltes Array zu decodieren und den decodierten Wert zurückzugeben:</span><span class="sxs-lookup"><span data-stu-id="0b1e0-126">Inside the `<head>` element of `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server), provide a JavaScript function that uses `TextDecoder` to decode a passed array and return the decoded value:</span></span>

[!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-convertarray.html)]

<span data-ttu-id="0b1e0-127">JavaScript-Code, z. B. der Code im vorherigen Beispiel, kann auch über eine JavaScript-Datei (`.js`) mit einem Verweis auf die Skriptdatei geladen werden:</span><span class="sxs-lookup"><span data-stu-id="0b1e0-127">JavaScript code, such as the code shown in the preceding example, can also be loaded from a JavaScript file (`.js`) with a reference to the script file:</span></span>

```html
<script src="exampleJsInterop.js"></script>
```

<span data-ttu-id="0b1e0-128">Die folgende Komponente führt folgende Aktionen aus:</span><span class="sxs-lookup"><span data-stu-id="0b1e0-128">The following component:</span></span>

* <span data-ttu-id="0b1e0-129">Sie ruft die JavaScript-Funktion `convertArray` mit `JS` auf, wenn eine Komponentenschaltfläche ( **`Convert Array`** ) ausgewählt wird.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-129">Invokes the `convertArray` JavaScript function using `JS` when a component button (**`Convert Array`**) is selected.</span></span>
* <span data-ttu-id="0b1e0-130">Nachdem die JavaScript-Funktion aufgerufen wurde, wird das übergebene Array in eine Zeichenfolge konvertiert.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-130">After the JavaScript function is called, the passed array is converted into a string.</span></span> <span data-ttu-id="0b1e0-131">Die Zeichenfolge wird zur Anzeige an die Komponente zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-131">The string is returned to the component for display.</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/call-js-example.razor?highlight=2,34-35)]

## <a name="ijsruntime"></a><span data-ttu-id="0b1e0-132">IJSRuntime</span><span class="sxs-lookup"><span data-stu-id="0b1e0-132">IJSRuntime</span></span>

<span data-ttu-id="0b1e0-133">Verwenden Sie einen der folgenden Ansätze, um die <xref:Microsoft.JSInterop.IJSRuntime>-Abstraktion zu verwenden:</span><span class="sxs-lookup"><span data-stu-id="0b1e0-133">To use the <xref:Microsoft.JSInterop.IJSRuntime> abstraction, adopt any of the following approaches:</span></span>

* <span data-ttu-id="0b1e0-134">Fügen Sie die <xref:Microsoft.JSInterop.IJSRuntime>-Abstraktion in die Razor-Komponente (`.razor`) ein:</span><span class="sxs-lookup"><span data-stu-id="0b1e0-134">Inject the <xref:Microsoft.JSInterop.IJSRuntime> abstraction into the Razor component (`.razor`):</span></span>

  [!code-razor[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction.razor?highlight=1)]

  <span data-ttu-id="0b1e0-135">Stellen Sie innerhalb des `<head>`-Elements von `wwwroot/index.html` (Blazor WebAssembly) oder `Pages/_Host.cshtml` (Blazor Server) eine `handleTickerChanged`-JavaScript-Funktion bereit.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-135">Inside the `<head>` element of `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server), provide a `handleTickerChanged` JavaScript function.</span></span> <span data-ttu-id="0b1e0-136">Die Funktion wird mit <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> aufgerufen und gibt keinen Wert zurück:</span><span class="sxs-lookup"><span data-stu-id="0b1e0-136">The function is called with <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> and doesn't return a value:</span></span>

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged1.html)]

* <span data-ttu-id="0b1e0-137">Fügen Sie die <xref:Microsoft.JSInterop.IJSRuntime>-Abstraktion in eine Klasse (`.cs`) ein:</span><span class="sxs-lookup"><span data-stu-id="0b1e0-137">Inject the <xref:Microsoft.JSInterop.IJSRuntime> abstraction into a class (`.cs`):</span></span>

  [!code-csharp[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction-class.cs?highlight=5)]

  <span data-ttu-id="0b1e0-138">Stellen Sie innerhalb des `<head>`-Elements von `wwwroot/index.html` (Blazor WebAssembly) oder `Pages/_Host.cshtml` (Blazor Server) eine `handleTickerChanged`-JavaScript-Funktion bereit.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-138">Inside the `<head>` element of `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server), provide a `handleTickerChanged` JavaScript function.</span></span> <span data-ttu-id="0b1e0-139">Die Funktion wird mit `JS.InvokeAsync` aufgerufen und gibt einen Wert zurück:</span><span class="sxs-lookup"><span data-stu-id="0b1e0-139">The function is called with `JS.InvokeAsync` and returns a value:</span></span>

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged2.html)]

* <span data-ttu-id="0b1e0-140">Verwenden Sie das `[Inject]`-Attribut für die dynamische Inhaltsgenerierung mit [BuildRenderTree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic):</span><span class="sxs-lookup"><span data-stu-id="0b1e0-140">For dynamic content generation with [BuildRenderTree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic), use the `[Inject]` attribute:</span></span>

  ```razor
  [Inject]
  IJSRuntime JS { get; set; }
  ```

<span data-ttu-id="0b1e0-141">In der clientseitigen Beispiel-App zu diesem Artikel stehen der App zwei JavaScript-Funktionen zur Verfügung, die mit dem DOM (Dokumentobjektmodell) interagieren, um Benutzereingaben zu empfangen und eine Begrüßungsnachricht anzuzeigen:</span><span class="sxs-lookup"><span data-stu-id="0b1e0-141">In the client-side sample app that accompanies this topic, two JavaScript functions are available to the app that interact with the DOM to receive user input and display a welcome message:</span></span>

* <span data-ttu-id="0b1e0-142">`showPrompt`: Generiert eine Eingabeaufforderung zum Akzeptieren der Benutzereingaben (dem Namen des Benutzers) und gibt den Namen an den Aufrufer zurück.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-142">`showPrompt`: Produces a prompt to accept user input (the user's name) and returns the name to the caller.</span></span>
* <span data-ttu-id="0b1e0-143">`displayWelcome`: Weist einem DOM-Objekt eine Begrüßungsnachricht vom Aufrufer mit einer `id` mit dem Wert `welcome` zu.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-143">`displayWelcome`: Assigns a welcome message from the caller to a DOM object with an `id` of `welcome`.</span></span>

<span data-ttu-id="0b1e0-144">`wwwroot/exampleJsInterop.js`:</span><span class="sxs-lookup"><span data-stu-id="0b1e0-144">`wwwroot/exampleJsInterop.js`:</span></span>

[!code-javascript[](./common/samples/5.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=2-7)]

<span data-ttu-id="0b1e0-145">Platzieren Sie das `<script>`-Tag, das auf die JavaScript-Datei verweist, in der `wwwroot/index.html`-Datei (Blazor WebAssembly) oder der `Pages/_Host.cshtml`-Datei (Blazor Server).</span><span class="sxs-lookup"><span data-stu-id="0b1e0-145">Place the `<script>` tag that references the JavaScript file in the `wwwroot/index.html` file (Blazor WebAssembly) or `Pages/_Host.cshtml` file (Blazor Server).</span></span>

<span data-ttu-id="0b1e0-146">`wwwroot/index.html` (Blazor WebAssembly):</span><span class="sxs-lookup"><span data-stu-id="0b1e0-146">`wwwroot/index.html` (Blazor WebAssembly):</span></span>

[!code-html[](./common/samples/5.x/BlazorWebAssemblySample/wwwroot/index.html?highlight=22)]

<span data-ttu-id="0b1e0-147">`Pages/_Host.cshtml` (Blazor Server):</span><span class="sxs-lookup"><span data-stu-id="0b1e0-147">`Pages/_Host.cshtml` (Blazor Server):</span></span>

[!code-cshtml[](./common/samples/5.x/BlazorServerSample/Pages/_Host.cshtml?highlight=34)]

<span data-ttu-id="0b1e0-148">Platzieren Sie kein `<script>`-Tag in einer Komponentendatei, weil das `<script>`-Tag nicht dynamisch aktualisiert werden kann.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-148">Don't place a `<script>` tag in a component file because the `<script>` tag can't be updated dynamically.</span></span>

<span data-ttu-id="0b1e0-149">Die Interoperabilität von .NET-Methoden mit den JavaScript-Funktionen in der Datei `exampleJsInterop.js` erfolgt, indem <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType> aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-149">.NET methods interop with the JavaScript functions in the `exampleJsInterop.js` file by calling <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType>.</span></span>

<span data-ttu-id="0b1e0-150">Die <xref:Microsoft.JSInterop.IJSRuntime>-Abstraktion ist asynchron, um Blazor Server-Szenarios zu ermöglichen.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-150">The <xref:Microsoft.JSInterop.IJSRuntime> abstraction is asynchronous to allow for Blazor Server scenarios.</span></span> <span data-ttu-id="0b1e0-151">Wenn es sich bei der App um eine Blazor WebAssembly-App handelt, sollten Sie die JavaScript-Funktion synchron aufrufen, eine Typumwandlung nach unten in <xref:Microsoft.JSInterop.IJSInProcessRuntime> durchführen und stattdessen <xref:Microsoft.JSInterop.IJSInProcessRuntime.Invoke%2A> aufrufen.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-151">If the app is a Blazor WebAssembly app and you want to invoke a JavaScript function synchronously, downcast to <xref:Microsoft.JSInterop.IJSInProcessRuntime> and call <xref:Microsoft.JSInterop.IJSInProcessRuntime.Invoke%2A> instead.</span></span> <span data-ttu-id="0b1e0-152">Es wird empfohlen, für die meisten JS Interop-Bibliotheken asynchrone APIs zu verwenden, um sicherzustellen, dass die Bibliotheken in allen Szenarios verfügbar sind.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-152">We recommend that most JS interop libraries use the async APIs to ensure that the libraries are available in all scenarios.</span></span>

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="0b1e0-153">Informationen zum Aktivieren der JavaScript-Isolation in [JavaScript-Standardmodulen](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules) finden Sie im Abschnitt [Blazor-JavaScript-Isolation und Objektverweise](#blazor-javascript-isolation-and-object-references).</span><span class="sxs-lookup"><span data-stu-id="0b1e0-153">To enable JavaScript isolation in standard [JavaScript modules](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules), see the [Blazor JavaScript isolation and object references](#blazor-javascript-isolation-and-object-references) section.</span></span>

::: moniker-end

<span data-ttu-id="0b1e0-154">Die Beispiel-App enthält eine Komponente zur Veranschaulichung der JavaScript-Interoperabilität.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-154">The sample app includes a component to demonstrate JS interop.</span></span> <span data-ttu-id="0b1e0-155">Die Komponente:</span><span class="sxs-lookup"><span data-stu-id="0b1e0-155">The component:</span></span>

* <span data-ttu-id="0b1e0-156">empfängt Benutzereingaben über eine JavaScript-Eingabeaufforderung.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-156">Receives user input via a JavaScript prompt.</span></span>
* <span data-ttu-id="0b1e0-157">gibt den Text zur Verarbeitung an die Komponente zurück.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-157">Returns the text to the component for processing.</span></span>
* <span data-ttu-id="0b1e0-158">ruft eine zweite JavaScript-Funktion auf, die mit dem DOM interagiert, um eine Begrüßungsnachricht anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-158">Calls a second JavaScript function that interacts with the DOM to display a welcome message.</span></span>

<span data-ttu-id="0b1e0-159">`Pages/JsInterop.razor`:</span><span class="sxs-lookup"><span data-stu-id="0b1e0-159">`Pages/JsInterop.razor`:</span></span>

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

<span data-ttu-id="0b1e0-160">Der Platzhalter `{APP ASSEMBLY}` ist der App-Assemblyname der App (z. B. `BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="0b1e0-160">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

1. <span data-ttu-id="0b1e0-161">Wenn `TriggerJsPrompt` ausgeführt wird, indem die Schaltfläche **`Trigger JavaScript Prompt`** der Komponente ausgewählt wird, wird die `showPrompt`-Funktion von JavaScript aufgerufen, die in der `wwwroot/exampleJsInterop.js`-Datei bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-161">When `TriggerJsPrompt` is executed by selecting the component's **`Trigger JavaScript Prompt`** button, the JavaScript `showPrompt` function provided in the `wwwroot/exampleJsInterop.js` file is called.</span></span>
1. <span data-ttu-id="0b1e0-162">Die `showPrompt`-Funktion akzeptiert Benutzereingaben (den Namen des Benutzers), die HTML-codiert und an die Komponente zurückgegeben werden.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-162">The `showPrompt` function accepts user input (the user's name), which is HTML-encoded and returned to the component.</span></span> <span data-ttu-id="0b1e0-163">Die Komponente speichert den Namen des Benutzers in der lokalen Variable `name`.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-163">The component stores the user's name in a local variable, `name`.</span></span>
1. <span data-ttu-id="0b1e0-164">Die in `name` gespeicherte Zeichenfolge wird in eine Begrüßungsnachricht eingebunden, die an die JavaScript-Funktion `displayWelcome` übergeben wird, die die Begrüßungsnachricht in ein Überschriftentag rendert.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-164">The string stored in `name` is incorporated into a welcome message, which is passed to a JavaScript function, `displayWelcome`, which renders the welcome message into a heading tag.</span></span>

## <a name="call-a-void-javascript-function"></a><span data-ttu-id="0b1e0-165">Aufrufen der JavaScript-Funktion „void“</span><span class="sxs-lookup"><span data-stu-id="0b1e0-165">Call a void JavaScript function</span></span>

<span data-ttu-id="0b1e0-166">Verwenden Sie <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> für Folgendes:</span><span class="sxs-lookup"><span data-stu-id="0b1e0-166">Use <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> for the following:</span></span>

* <span data-ttu-id="0b1e0-167">JavaScript-Funktionen, die [void(0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) oder [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined) zurückgeben</span><span class="sxs-lookup"><span data-stu-id="0b1e0-167">JavaScript functions that return [void(0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) or [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined).</span></span>
* <span data-ttu-id="0b1e0-168">Wenn .NET nicht erforderlich ist, um das Ergebnis eines JavaScript-Aufrufs zu lesen</span><span class="sxs-lookup"><span data-stu-id="0b1e0-168">If .NET isn't required to read the result of a JavaScript call.</span></span>

## <a name="detect-when-a-no-locblazor-server-app-is-prerendering"></a><span data-ttu-id="0b1e0-169">Erkennen, wenn für eine Blazor Server-App ein Prerendering durchgeführt wird</span><span class="sxs-lookup"><span data-stu-id="0b1e0-169">Detect when a Blazor Server app is prerendering</span></span>
 
[!INCLUDE[](~/blazor/includes/prerendering.md)]

## <a name="capture-references-to-elements"></a><span data-ttu-id="0b1e0-170">Erfassen von Verweisen auf Elemente</span><span class="sxs-lookup"><span data-stu-id="0b1e0-170">Capture references to elements</span></span>

<span data-ttu-id="0b1e0-171">Einige JS Interop-Szenarios erfordern Verweise auf HTML-Elemente.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-171">Some JS interop scenarios require references to HTML elements.</span></span> <span data-ttu-id="0b1e0-172">Beispielsweise erfordert eine Benutzeroberflächenbibliothek möglicherweise einen Elementverweis für die Initialisierung, oder Sie müssen befehlsähnliche APIs für ein Element aufrufen, z. B. `focus` oder `play`.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-172">For example, a UI library may require an element reference for initialization, or you might need to call command-like APIs on an element, such as `focus` or `play`.</span></span>

<span data-ttu-id="0b1e0-173">Erfassen Sie mithilfe des folgenden Ansatzes Verweise auf HTML-Elemente in einer Komponente:</span><span class="sxs-lookup"><span data-stu-id="0b1e0-173">Capture references to HTML elements in a component using the following approach:</span></span>

* <span data-ttu-id="0b1e0-174">Fügen Sie ein `@ref`-Attribut zum HTML-Element hinzu.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-174">Add an `@ref` attribute to the HTML element.</span></span>
* <span data-ttu-id="0b1e0-175">Definieren Sie ein Feld vom Typ <xref:Microsoft.AspNetCore.Components.ElementReference>, dessen Name mit dem Wert des `@ref`-Attributs übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-175">Define a field of type <xref:Microsoft.AspNetCore.Components.ElementReference> whose name matches the value of the `@ref` attribute.</span></span>

<span data-ttu-id="0b1e0-176">Im folgenden Beispiel wird das Erfassen eines Verweises auf das `username` `<input>`-Element veranschaulicht:</span><span class="sxs-lookup"><span data-stu-id="0b1e0-176">The following example shows capturing a reference to the `username` `<input>` element:</span></span>

```razor
<input @ref="username" ... />

@code {
    ElementReference username;
}
```

> [!WARNING]
> <span data-ttu-id="0b1e0-177">Verwenden Sie Elementverweise nur, um die Inhalte eines leeren Elements zu ändern, das nicht mit Blazor interagiert.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-177">Only use an element reference to mutate the contents of an empty element that doesn't interact with Blazor.</span></span> <span data-ttu-id="0b1e0-178">Dieses Szenario ist nützlich, wenn eine Drittanbieter-API Inhalt für das Element bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-178">This scenario is useful when a third-party API supplies content to the element.</span></span> <span data-ttu-id="0b1e0-179">Da Blazor nicht mit dem Element interagiert, kann kein Konflikt zwischen der Darstellung von Blazor des Elements und dem DOM auftreten.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-179">Because Blazor doesn't interact with the element, there's no possibility of a conflict between Blazor's representation of the element and the DOM.</span></span>
>
> <span data-ttu-id="0b1e0-180">Im folgenden Beispiel ist es *gefährlich*, die Inhalte der unsortierten Liste (`ul`) zu ändern, weil Blazor mit dem DOM interagiert, um die Listenelemente (`<li>`) dieses Elements aufzufüllen:</span><span class="sxs-lookup"><span data-stu-id="0b1e0-180">In the following example, it's *dangerous* to mutate the contents of the unordered list (`ul`) because Blazor interacts with the DOM to populate this element's list items (`<li>`):</span></span>
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
> <span data-ttu-id="0b1e0-181">Wenn die Inhalte des Elements `MyList` durch JS Interop geändert werden und Blazor versucht, diffs-Algorithmen auf das Element anzuwenden, entsprechen diese nicht dem DOM.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-181">If JS interop mutates the contents of element `MyList` and Blazor attempts to apply diffs to the element, the diffs won't match the DOM.</span></span>

<span data-ttu-id="0b1e0-182">Eine <xref:Microsoft.AspNetCore.Components.ElementReference> wird über JS Interop an JavaScript-Code übergeben.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-182">An <xref:Microsoft.AspNetCore.Components.ElementReference> is passed through to JavaScript code via JS interop.</span></span> <span data-ttu-id="0b1e0-183">Der JavaScript-Code empfängt eine `HTMLElement`-Instanz, die er mit normalen DOM-APIs verwenden kann.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-183">The JavaScript code receives an `HTMLElement` instance, which it can use with normal DOM APIs.</span></span> <span data-ttu-id="0b1e0-184">Im folgenden Code wird beispielsweise eine .NET-Erweiterungsmethode definiert, die das Senden eines Mausklicks an ein Element ermöglicht:</span><span class="sxs-lookup"><span data-stu-id="0b1e0-184">For example, the following code defines a .NET extension method that enables sending a mouse click to an element:</span></span>

<span data-ttu-id="0b1e0-185">`exampleJsInterop.js`:</span><span class="sxs-lookup"><span data-stu-id="0b1e0-185">`exampleJsInterop.js`:</span></span>

```javascript
window.interopFunctions = {
  clickElement : function (element) {
    element.click();
  }
}
```

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="0b1e0-186">Verwenden Sie [`FocusAsync`](xref:blazor/components/event-handling#focus-an-element) im C# Code, um ein Element zu fokussieren, das in das Blazor-Framework integriert ist und mit Elementverweisen arbeitet.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-186">Use [`FocusAsync`](xref:blazor/components/event-handling#focus-an-element) in C# code to focus an element, which is built-into the Blazor framework and works with element references.</span></span>

::: moniker-end

<span data-ttu-id="0b1e0-187">Verwenden Sie <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType>, um eine JavaScript-Funktion aufzurufen, die keinen Wert zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-187">To call a JavaScript function that doesn't return a value, use <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="0b1e0-188">Mit dem folgenden Code wird ein clientseitiges `Click`-Ereignis ausgelöst, indem die vorherige JavaScript-Funktion mit dem erfassten <xref:Microsoft.AspNetCore.Components.ElementReference> aufgerufen wird:</span><span class="sxs-lookup"><span data-stu-id="0b1e0-188">The following code triggers a client-side `Click` event by calling the preceding JavaScript function with the captured <xref:Microsoft.AspNetCore.Components.ElementReference>:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component1.razor?highlight=14-15)]

<span data-ttu-id="0b1e0-189">Erstellen Sie eine statische Erweiterungsmethode, die die <xref:Microsoft.JSInterop.IJSRuntime>-Instanz empfängt, um eine Erweiterungsmethode zu verwenden:</span><span class="sxs-lookup"><span data-stu-id="0b1e0-189">To use an extension method, create a static extension method that receives the <xref:Microsoft.JSInterop.IJSRuntime> instance:</span></span>

```csharp
public static async Task TriggerClickEvent(this ElementReference elementRef, 
    IJSRuntime js)
{
    await js.InvokeVoidAsync("interopFunctions.clickElement", elementRef);
}
```

<span data-ttu-id="0b1e0-190">Die `clickElement`-Methode wird direkt für das Objekt aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-190">The `clickElement` method is called directly on the object.</span></span> <span data-ttu-id="0b1e0-191">Im folgenden Beispiel wird davon ausgegangen, dass die `TriggerClickEvent`-Methode vom `JsInteropClasses`-Namespace verfügbar ist:</span><span class="sxs-lookup"><span data-stu-id="0b1e0-191">The following example assumes that the `TriggerClickEvent` method is available from the `JsInteropClasses` namespace:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component2.razor?highlight=15)]

> [!IMPORTANT]
> <span data-ttu-id="0b1e0-192">Die `exampleButton`-Variable wird erst aufgefüllt, nachdem die Komponente gerendert wurde.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-192">The `exampleButton` variable is only populated after the component is rendered.</span></span> <span data-ttu-id="0b1e0-193">Wenn ein nicht aufgefüllter <xref:Microsoft.AspNetCore.Components.ElementReference>-Wert an JavaScript-Code übergeben wird, empfängt der JavaScript-Code den Wert `null`.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-193">If an unpopulated <xref:Microsoft.AspNetCore.Components.ElementReference> is passed to JavaScript code, the JavaScript code receives a value of `null`.</span></span> <span data-ttu-id="0b1e0-194">Sie können Elementverweise bearbeiten, nachdem die Komponente das Rendering abgeschlossen hat, indem Sie die [Lebenszyklusmethoden `OnAfterRenderAsync` oder `OnAfterRender`](xref:blazor/components/lifecycle#after-component-render) verwenden.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-194">To manipulate element references after the component has finished rendering use the [`OnAfterRenderAsync` or `OnAfterRender` component lifecycle methods](xref:blazor/components/lifecycle#after-component-render).</span></span>

<span data-ttu-id="0b1e0-195">Verwenden Sie <xref:System.Threading.Tasks.ValueTask%601>, wenn Sie mit generischen Typen arbeiten und einen Wert zurückgeben:</span><span class="sxs-lookup"><span data-stu-id="0b1e0-195">When working with generic types and returning a value, use <xref:System.Threading.Tasks.ValueTask%601>:</span></span>

```csharp
public static ValueTask<T> GenericMethod<T>(this ElementReference elementRef, 
    IJSRuntime js)
{
    return js.InvokeAsync<T>("exampleJsFunctions.doSomethingGeneric", elementRef);
}
```

<span data-ttu-id="0b1e0-196">`GenericMethod` wird direkt für das Objekt mit einem Typ aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-196">`GenericMethod` is called directly on the object with a type.</span></span> <span data-ttu-id="0b1e0-197">Im folgenden Beispiel wird davon ausgegangen, dass die `GenericMethod`-Methode vom `JsInteropClasses`-Namespace verfügbar ist:</span><span class="sxs-lookup"><span data-stu-id="0b1e0-197">The following example assumes that the `GenericMethod` is available from the `JsInteropClasses` namespace:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component3.razor?highlight=17)]

## <a name="reference-elements-across-components"></a><span data-ttu-id="0b1e0-198">Komponentenübergreifende Verweiselemente</span><span class="sxs-lookup"><span data-stu-id="0b1e0-198">Reference elements across components</span></span>

<span data-ttu-id="0b1e0-199">Ein <xref:Microsoft.AspNetCore.Components.ElementReference> kann aus folgenden Gründen nicht zwischen Komponenten übermittelt werden:</span><span class="sxs-lookup"><span data-stu-id="0b1e0-199">An <xref:Microsoft.AspNetCore.Components.ElementReference> can't be passed between components because:</span></span>

* <span data-ttu-id="0b1e0-200">Das Vorhandensein der Instanz ist nur nach dem Rendern der Komponente garantiert, das während oder nach der Ausführung der <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A>/<xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>-Methode einer Komponente erfolgt.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-200">The instance is only guaranteed to exist after the component is rendered, which is during or after a component's <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A>/<xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> method executes.</span></span>
* <span data-ttu-id="0b1e0-201">Ein <xref:Microsoft.AspNetCore.Components.ElementReference> ist ein [`struct`](/csharp/language-reference/builtin-types/struct), der nicht als [Komponentenparameter](xref:blazor/components/index#component-parameters) übergeben werden kann.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-201">An <xref:Microsoft.AspNetCore.Components.ElementReference> is a [`struct`](/csharp/language-reference/builtin-types/struct), which can't be passed as a [component parameter](xref:blazor/components/index#component-parameters).</span></span>

<span data-ttu-id="0b1e0-202">Damit eine übergeordnete Komponente einen Elementverweis anderen Komponenten zur Verfügung stellen kann, kann die übergeordnete Komponente:</span><span class="sxs-lookup"><span data-stu-id="0b1e0-202">For a parent component to make an element reference available to other components, the parent component can:</span></span>

* <span data-ttu-id="0b1e0-203">zulassen, dass untergeordnete Komponenten Rückrufe registrieren.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-203">Allow child components to register callbacks.</span></span>
* <span data-ttu-id="0b1e0-204">die registrierten Rückrufe während des <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A>-Ereignisses mit dem übergebenen Elementverweis aufrufen.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-204">Invoke the registered callbacks during the <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> event with the passed element reference.</span></span> <span data-ttu-id="0b1e0-205">Dieser Ansatz ermöglicht untergeordneten Komponenten indirekt die Interaktion mit dem Elementverweis des übergeordneten Elements.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-205">Indirectly, this approach allows child components to interact with the parent's element reference.</span></span>

<span data-ttu-id="0b1e0-206">Dieser Ansatz wird anhand des folgenden Blazor WebAssembly-Beispiels veranschaulicht.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-206">The following Blazor WebAssembly example illustrates the approach.</span></span>

<span data-ttu-id="0b1e0-207">Im `<head>` von `wwwroot/index.html`:</span><span class="sxs-lookup"><span data-stu-id="0b1e0-207">In the `<head>` of `wwwroot/index.html`:</span></span>

```html
<style>
    .red { color: red }
</style>
```

<span data-ttu-id="0b1e0-208">Im `<body>` von `wwwroot/index.html`:</span><span class="sxs-lookup"><span data-stu-id="0b1e0-208">In the `<body>` of `wwwroot/index.html`:</span></span>

```html
<script>
    function setElementClass(element, className) {
        /** @type {HTMLElement} **/
        var myElement = element;
        myElement.classList.add(className);
    }
</script>
```

<span data-ttu-id="0b1e0-209">`Pages/Index.razor` (übergeordnete Komponente):</span><span class="sxs-lookup"><span data-stu-id="0b1e0-209">`Pages/Index.razor` (parent component):</span></span>

```razor
@page "/"

<h1 @ref="title">Hello, world!</h1>

Welcome to your new app.

<SurveyPrompt Parent="this" Title="How is Blazor working for you?" />
```

<span data-ttu-id="0b1e0-210">`Pages/Index.razor.cs`:</span><span class="sxs-lookup"><span data-stu-id="0b1e0-210">`Pages/Index.razor.cs`:</span></span>

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

<span data-ttu-id="0b1e0-211">Der Platzhalter `{APP ASSEMBLY}` ist der App-Assemblyname der App (z. B. `BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="0b1e0-211">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

<span data-ttu-id="0b1e0-212">`Shared/SurveyPrompt.razor` (untergeordnete Komponente):</span><span class="sxs-lookup"><span data-stu-id="0b1e0-212">`Shared/SurveyPrompt.razor` (child component):</span></span>

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

<span data-ttu-id="0b1e0-213">`Shared/SurveyPrompt.razor.cs`:</span><span class="sxs-lookup"><span data-stu-id="0b1e0-213">`Shared/SurveyPrompt.razor.cs`:</span></span>

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

<span data-ttu-id="0b1e0-214">Der Platzhalter `{APP ASSEMBLY}` ist der App-Assemblyname der App (z. B. `BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="0b1e0-214">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

## <a name="harden-js-interop-calls"></a><span data-ttu-id="0b1e0-215">Festschreiben von JS Interop-Aufrufen</span><span class="sxs-lookup"><span data-stu-id="0b1e0-215">Harden JS interop calls</span></span>

<span data-ttu-id="0b1e0-216">JS Interop kann aufgrund von Netzwerkfehlern fehlschlagen und sollte als unzuverlässig behandelt werden.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-216">JS interop may fail due to networking errors and should be treated as unreliable.</span></span> <span data-ttu-id="0b1e0-217">Standardmäßig weisen Blazor Server-Apps einen Timeout für JS Interop-Aufrufe an den Server nach einer Minute auf.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-217">By default, a Blazor Server app times out JS interop calls on the server after one minute.</span></span> <span data-ttu-id="0b1e0-218">Wenn eine App ein engeres Timeout tolerieren kann, legen Sie das Timeout mit einem der folgenden Ansätze fest:</span><span class="sxs-lookup"><span data-stu-id="0b1e0-218">If an app can tolerate a more aggressive timeout, set the timeout using one of the following approaches:</span></span>

* <span data-ttu-id="0b1e0-219">Legen Sie das Timeout global in `Startup.ConfigureServices` fest:</span><span class="sxs-lookup"><span data-stu-id="0b1e0-219">Globally in `Startup.ConfigureServices`, specify the timeout:</span></span>

  ```csharp
  services.AddServerSideBlazor(
      options => options.JSInteropDefaultCallTimeout = TimeSpan.FromSeconds({SECONDS}));
  ```

* <span data-ttu-id="0b1e0-220">Pro Aufruf im Komponentencode kann ein einzelner Aufruf das Timeout festlegen:</span><span class="sxs-lookup"><span data-stu-id="0b1e0-220">Per-invocation in component code, a single call can specify the timeout:</span></span>

  ```csharp
  var result = await JS.InvokeAsync<string>("MyJSOperation", 
      TimeSpan.FromSeconds({SECONDS}), new[] { "Arg1" });
  ```

<span data-ttu-id="0b1e0-221">Weitere Informationen zur Ressourcenauslastung finden Sie unter <xref:blazor/security/server/threat-mitigation>.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-221">For more information on resource exhaustion, see <xref:blazor/security/server/threat-mitigation>.</span></span>

[!INCLUDE[](~/blazor/includes/share-interop-code.md)]

## <a name="avoid-circular-object-references"></a><span data-ttu-id="0b1e0-222">Vermeiden von Objektzirkelbezügen</span><span class="sxs-lookup"><span data-stu-id="0b1e0-222">Avoid circular object references</span></span>

<span data-ttu-id="0b1e0-223">Objekte, die Zirkelbezüge enthalten, können auf dem Client für folgende Vorgänge nicht serialisiert werden:</span><span class="sxs-lookup"><span data-stu-id="0b1e0-223">Objects that contain circular references can't be serialized on the client for either:</span></span>

* <span data-ttu-id="0b1e0-224">.NET-Methodenaufrufe.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-224">.NET method calls.</span></span>
* <span data-ttu-id="0b1e0-225">JavaScript-Methodenaufrufe von C#, wenn der Rückgabetyp Zirkelbezüge enthält.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-225">JavaScript method calls from C# when the return type has circular references.</span></span>

<span data-ttu-id="0b1e0-226">Weitere Informationen finden Sie unter [Circular references are not supported, take two (dotnet/aspnetcore #20525)](https://github.com/dotnet/aspnetcore/issues/20525) (Zirkelbezüge werden nicht unterstützt, die Zweite [dotnet/aspnetcore #20525]).</span><span class="sxs-lookup"><span data-stu-id="0b1e0-226">For more information, see [Circular references are not supported, take two (dotnet/aspnetcore #20525)](https://github.com/dotnet/aspnetcore/issues/20525).</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="no-locblazor-javascript-isolation-and-object-references"></a><span data-ttu-id="0b1e0-227">Blazor-JavaScript-Isolation und Objektverweise</span><span class="sxs-lookup"><span data-stu-id="0b1e0-227">Blazor JavaScript isolation and object references</span></span>

<span data-ttu-id="0b1e0-228">Blazor aktiviert JavaScript-Isolierung in [JavaScript-Standardmodulen](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules).</span><span class="sxs-lookup"><span data-stu-id="0b1e0-228">Blazor enables JavaScript isolation in standard [JavaScript modules](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules).</span></span> <span data-ttu-id="0b1e0-229">JavaScript-Isolierung bietet die folgenden Vorteile:</span><span class="sxs-lookup"><span data-stu-id="0b1e0-229">JavaScript isolation provides the following benefits:</span></span>

* <span data-ttu-id="0b1e0-230">Importiertes JavaScript verschmutzt nicht mehr den globalen Namespace.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-230">Imported JavaScript no longer pollutes the global namespace.</span></span>
* <span data-ttu-id="0b1e0-231">Consumer einer Bibliothek und Komponenten müssen nicht das zugehörige JavaScript importieren.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-231">Consumers of a library and components aren't required to import the related JavaScript.</span></span>

<span data-ttu-id="0b1e0-232">Das folgende JavaScript-Modul exportiert z. B. eine JavaScript-Funktion, um eine Browsereingabeaufforderung anzuzeigen:</span><span class="sxs-lookup"><span data-stu-id="0b1e0-232">For example, the following JavaScript module exports a JavaScript function for showing a browser prompt:</span></span>

```javascript
export function showPrompt(message) {
  return prompt(message, 'Type anything here');
}
```

<span data-ttu-id="0b1e0-233">Fügen Sie das oben gezeigte JavaScript-Modul einer .NET-Bibliothek als statische Webressource (`wwwroot/exampleJsInterop.js`) hinzu, und importieren Sie das Modul dann mithilfe des <xref:Microsoft.JSInterop.IJSRuntime>-Diensts in den .NET-Code.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-233">Add the preceding JavaScript module to a .NET library as a static web asset (`wwwroot/exampleJsInterop.js`) and then import the module into the .NET code using the <xref:Microsoft.JSInterop.IJSRuntime> service.</span></span> <span data-ttu-id="0b1e0-234">Der Dienst wird im folgenden Beispiel als `js` (nicht gezeigt) eingefügt:</span><span class="sxs-lookup"><span data-stu-id="0b1e0-234">The service is injected as `js` (not shown) for the following example:</span></span>

```csharp
var module = await js.InvokeAsync<IJSObjectReference>(
    "import", "./_content/MyComponents/exampleJsInterop.js");
```

<span data-ttu-id="0b1e0-235">Der Bezeichner `import` im Beispiel oben ist ein spezieller Bezeichner, der insbesondere zum Importieren eines JavaScript-Moduls verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-235">The `import` identifier in the preceding example is a special identifier used specifically for importing a JavaScript module.</span></span> <span data-ttu-id="0b1e0-236">Geben Sie das Modul mithilfe seines stabilen statischen Webressourcenpfads an: `_content/{LIBRARY NAME}/{PATH UNDER WWWROOT}`.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-236">Specify the module using its stable static web asset path: `_content/{LIBRARY NAME}/{PATH UNDER WWWROOT}`.</span></span> <span data-ttu-id="0b1e0-237">Der Platzhalter `{LIBRARY NAME}` ist der Name der Bibliothek.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-237">The placeholder `{LIBRARY NAME}` is the library name.</span></span> <span data-ttu-id="0b1e0-238">Der Platzhalter `{PATH UNDER WWWROOT}` ist der Pfad zum Skript unter `wwwroot`.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-238">The placeholder `{PATH UNDER WWWROOT}` is the path to the script under `wwwroot`.</span></span>

<span data-ttu-id="0b1e0-239"><xref:Microsoft.JSInterop.IJSRuntime> importiert das Modul als `IJSObjectReference`-Element, das einen Verweis auf ein JavaScript-Objekt aus .NET-Code darstellt.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-239"><xref:Microsoft.JSInterop.IJSRuntime> imports the module as a `IJSObjectReference`, which represents a reference to a JavaScript object from .NET code.</span></span> <span data-ttu-id="0b1e0-240">Verwenden Sie `IJSObjectReference`, um exportierte JavaScript-Funktionen aus dem Modul aufzurufen:</span><span class="sxs-lookup"><span data-stu-id="0b1e0-240">Use the `IJSObjectReference` to invoke exported JavaScript functions from the module:</span></span>

```csharp
public async ValueTask<string> Prompt(string message)
{
    return await module.InvokeAsync<string>("showPrompt", message);
}
```

<span data-ttu-id="0b1e0-241">`IJSInProcessObjectReference` steht für einen Verweis auf ein JavaScript-Objekt, dessen Funktionen synchron aufgerufen werden können.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-241">`IJSInProcessObjectReference` represents a reference to a JavaScript object whose functions can be invoked synchronously.</span></span>

## <a name="use-of-javascript-libraries-that-render-ui-dom-elements"></a><span data-ttu-id="0b1e0-242">Verwendung von JavaScript-Bibliotheken zum Rendern von Benutzeroberflächenelementen (DOM-Elemente)</span><span class="sxs-lookup"><span data-stu-id="0b1e0-242">Use of JavaScript libraries that render UI (DOM elements)</span></span>

<span data-ttu-id="0b1e0-243">Manchmal möchten Sie vielleicht JavaScript-Bibliotheken verwenden, die sichtbare Benutzeroberflächenelemente innerhalb des Browser-DOM erstellen.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-243">Sometimes you may wish to use JavaScript libraries that produce visible user interface elements within the browser DOM.</span></span> <span data-ttu-id="0b1e0-244">Auf den ersten Blick scheint dies schwierig zu sein, da das Vergleichssystem von Blazor darauf beruht, die Kontrolle über die Struktur von DOM-Elementen zu haben. Außerdem treten Fehler auf, wenn externer Code die DOM-Struktur bearbeitet, was den Mechanismus für die Diff-Anwendung unmöglich macht.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-244">At first glance, this might seem difficult because Blazor's diffing system relies on having control over the tree of DOM elements and runs into errors if some external code mutates the DOM tree and invalidates its mechanism for applying diffs.</span></span> <span data-ttu-id="0b1e0-245">Dabei handelt es sich um keine Blazor-spezifische Einschränkung.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-245">This isn't a Blazor-specific limitation.</span></span> <span data-ttu-id="0b1e0-246">Die gleiche Herausforderung stellt sich bei jedem anderen Diff-basierten Benutzeroberflächenframework.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-246">The same challenge occurs with any diff-based UI framework.</span></span>

<span data-ttu-id="0b1e0-247">Glücklicherweise ist es einfach, extern erstellte Benutzeroberflächenelemente innerhalb der Oberfläche einer Blazor-Komponente zuverlässig einzubetten.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-247">Fortunately, it's straightforward to embed externally-generated UI within a Blazor component UI reliably.</span></span> <span data-ttu-id="0b1e0-248">Die hierfür empfohlene Methode besteht darin, den Code der Komponente (`.razor`-Datei) ein leeres Element erzeugen zu lassen.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-248">The recommended technique is to have the component's code (`.razor` file) produce an empty element.</span></span> <span data-ttu-id="0b1e0-249">Im Vergleichssystem von Blazor ist das Element immer leer, der Renderer durchläuft das Element also nicht rekursiv, sondern berücksichtigt die Inhalte gar nicht.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-249">As far as Blazor's diffing system is concerned, the element is always empty, so the renderer does not recurse into the element and instead leaves its contents alone.</span></span> <span data-ttu-id="0b1e0-250">Dies führt dazu, dass das Auffüllen des Elements mit zufälligem extern verwalteten Inhalt sicher ist.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-250">This makes it safe to populate the element with arbitrary externally-managed content.</span></span>

<span data-ttu-id="0b1e0-251">Das folgende Beispiel veranschaulicht das Konzept.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-251">The following example demonstrates the concept.</span></span> <span data-ttu-id="0b1e0-252">Wenn innerhalb der `if`-Anweisung `firstRender` `true` ist, kann `myElement` genutzt werden.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-252">Within the `if` statement when `firstRender` is `true`, do something with `myElement`.</span></span> <span data-ttu-id="0b1e0-253">Beispielsweise wird für die Auffüllung eine externe JavaScript-Bibliothek aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-253">For example, call an external JavaScript library to populate it.</span></span> <span data-ttu-id="0b1e0-254">Blazor berücksichtigt die Inhalte des Elements solange nicht, bis die Komponente selbst entfernt wird.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-254">Blazor leaves the element's contents alone until this component itself is removed.</span></span> <span data-ttu-id="0b1e0-255">Wenn die Komponente entfernt wird, wird die gesamte DOM-Unterstruktur der Komponente ebenfalls entfernt.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-255">When the component is removed, the component's entire DOM subtree is also removed.</span></span>

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

<span data-ttu-id="0b1e0-256">Als detailliertes Beispiel können Sie sich die folgende Komponente ansehen, die eine interaktive Karte mithilfe der [Open-Source-Mapbox-APIs](https://www.mapbox.com/) rendert:</span><span class="sxs-lookup"><span data-stu-id="0b1e0-256">As a more detailed example, consider the following component that renders an interactive map using the [open-source Mapbox APIs](https://www.mapbox.com/):</span></span>

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

<span data-ttu-id="0b1e0-257">Das entsprechende JavaScript-Modul, das unter `wwwroot/mapComponent.js` platziert werden sollte, sieht folgendermaßen aus:</span><span class="sxs-lookup"><span data-stu-id="0b1e0-257">The corresponding JavaScript module, which should be placed at `wwwroot/mapComponent.js`, is as follows:</span></span>

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

<span data-ttu-id="0b1e0-258">Ersetzen Sie im vorherigen Beispiel die Zeichenfolge `{ACCESS TOKEN}` durch ein gültiges Zugriffstoken, das Sie von https://account.mapbox.com abrufen können.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-258">In the preceding example, replace the string `{ACCESS TOKEN}` with a valid access token that you can get from https://account.mapbox.com.</span></span>

<span data-ttu-id="0b1e0-259">Für eine korrekte Formatierung fügen Sie der HTML-Hostseite den folgenden Stylesheet-Tag hinzu (`index.html` oder `_Host.cshtml`):</span><span class="sxs-lookup"><span data-stu-id="0b1e0-259">To produce correct styling, add the following stylesheet tag to the host HTML page (`index.html` or `_Host.cshtml`):</span></span>

```html
<link rel="stylesheet" href="https://api.mapbox.com/mapbox-gl-js/v1.12.0/mapbox-gl.css" />
```

<span data-ttu-id="0b1e0-260">Das vorangehende Beispiel erzeugt eine interaktive Kartenbenutzeroberfläche, auf der der Benutzer folgende Möglichkeiten hat:</span><span class="sxs-lookup"><span data-stu-id="0b1e0-260">The preceding example produces an interactive map UI, in which the user:</span></span>

* <span data-ttu-id="0b1e0-261">Ziehen der Karte zum Scrollen oder Zoomen</span><span class="sxs-lookup"><span data-stu-id="0b1e0-261">Can drag to scroll or zoom.</span></span>
* <span data-ttu-id="0b1e0-262">Klicken auf Schaltflächen, um zu vordefinierten Standorten zu springen</span><span class="sxs-lookup"><span data-stu-id="0b1e0-262">Click buttons to jump to predefined locations.</span></span>

![Mapbox-Straßenkarte von Tokio in Japan, mit Schaltflächen, über die Bristol im Vereinigten Königreich und Tokio in Japan ausgewählt werden können](https://user-images.githubusercontent.com/1101362/94939821-92ef6700-04ca-11eb-858e-fff6df0053ae.png)

<span data-ttu-id="0b1e0-264">Die wichtigsten Punkte, die verstanden werden müssen, sind die folgenden:</span><span class="sxs-lookup"><span data-stu-id="0b1e0-264">The key points to understand are:</span></span>

 * <span data-ttu-id="0b1e0-265">Für Blazor gilt, dass `<div>` mit `@ref="mapElement"` leer bleibt.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-265">The `<div>` with `@ref="mapElement"` is left empty as far as Blazor is concerned.</span></span> <span data-ttu-id="0b1e0-266">Deshalb kann die Auffüllung durch `mapbox-gl.js` sicher erfolgen, und die Inhalte können im Laufe der Zeit bearbeitet werden.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-266">It's therefore safe for `mapbox-gl.js` to populate it and modify its contents over time.</span></span> <span data-ttu-id="0b1e0-267">Sie können dieses Vorgehen für alle JavaScript-Bibliotheken verwenden, die Benutzeroberflächen rendern.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-267">You can use this technique with any JavaScript library that renders UI.</span></span> <span data-ttu-id="0b1e0-268">Sie könnten sogar Komponenten des JavaScript-SPA-Frameworks eines Drittanbieters innerhalb der Blazor-Komponenten einbetten, solange nicht aktiv versucht wird, andere Teile der Seite zu ändern.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-268">You could even embed components from a third-party JavaScript SPA framework inside Blazor components, as long as they don't try to reach out and modify other parts of the page.</span></span> <span data-ttu-id="0b1e0-269">Es ist *nicht* sicher, wenn externer JavaScript-Code Elemente bearbeitet, die von Blazor nicht als leer klassifiziert werden.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-269">It is *not* safe for external JavaScript code to modify elements that Blazor does not regard as empty.</span></span>
 * <span data-ttu-id="0b1e0-270">Bei Verwendung dieses Ansatzes sollten Sie die Regeln im Hinterkopf behalten, wie Blazor DOM-Elemente beibehält oder löscht.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-270">When using this approach, bear in mind the rules about how Blazor retains or destroys DOM elements.</span></span> <span data-ttu-id="0b1e0-271">Im vorherigen Beispiel kann die Komponente Klickereignisse auf Schaltflächen sicher verarbeiten und die vorhandene Karteninstanz aktualisieren, da DOM-Elemente standardmäßig beibehalten werden, wo dies möglich ist.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-271">In the preceding example, the component safely handles button click events and updates the existing map instance because DOM elements are retained where possible by default.</span></span> <span data-ttu-id="0b1e0-272">Wenn Sie eine Liste von Kartenelementen innerhalb einer `@foreach`-Schleife rendern, sollten Sie `@key` verwenden, um für die Beibehaltung der Komponenteninstanzen zu sorgen.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-272">If you were rendering a list of map elements from inside a `@foreach` loop, you want to use `@key` to ensure the preservation of component instances.</span></span> <span data-ttu-id="0b1e0-273">Andernfalls könnten Änderungen der Listendaten dazu führen, dass Komponenteninstanzen den Status vorheriger Instanzen auf nicht gewünschte Weise beibehalten.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-273">Otherwise, changes in the list data could cause component instances to retain the state of previous instances in an undesirable manner.</span></span> <span data-ttu-id="0b1e0-274">Weitere Informationen finden Sie unter [Verwenden von @key zur Beibehaltung von Elementen und Komponenten](xref:blazor/components/index#use-key-to-control-the-preservation-of-elements-and-components).</span><span class="sxs-lookup"><span data-stu-id="0b1e0-274">For more information, see [using @key to preserve elements and components](xref:blazor/components/index#use-key-to-control-the-preservation-of-elements-and-components).</span></span>

<span data-ttu-id="0b1e0-275">Außerdem zeigt das vorherige Beispiel, wie es möglich ist, JavaScript-Logik und -Abhängigkeiten innerhalb eines ES6-Moduls zu kapseln und dynamisch mithilfe des `import`-Bezeichners zu laden.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-275">Additionally, the preceding example shows how it's possible to encapsulate JavaScript logic and dependencies within an ES6 module and load it dynamically using the `import` identifier.</span></span> <span data-ttu-id="0b1e0-276">Weitere Informationen finden Sie unter [JavaScript-Isolierung in Blazor und Objektverweise](#blazor-javascript-isolation-and-object-references).</span><span class="sxs-lookup"><span data-stu-id="0b1e0-276">For more information, see [JavaScript isolation and object references](#blazor-javascript-isolation-and-object-references).</span></span>

::: moniker-end

## <a name="size-limits-on-js-interop-calls"></a><span data-ttu-id="0b1e0-277">Größenbeschränkungen bei JS-Interop-Aufrufen</span><span class="sxs-lookup"><span data-stu-id="0b1e0-277">Size limits on JS interop calls</span></span>

<span data-ttu-id="0b1e0-278">In Blazor WebAssembly gibt das Framework keine Beschränkungen hinsichtlich der Größe von Ein- und Ausgaben von JS-Interop-Aufrufen vor.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-278">In Blazor WebAssembly, the framework doesn't impose limits on the size of inputs and outputs of JS interop calls.</span></span>

<span data-ttu-id="0b1e0-279">In Blazor Server wird das Ergebnis eines JS-Interop-Aufrufs durch die maximale Payloadgröße beschränkt, die von SignalR (<xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize>) erzwungen wird. Der Standardwert ist 32 KB.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-279">In Blazor Server, the result of a JS interop call is limited by the maximum payload size enforced by SignalR (<xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize>), which defaults to 32 KB.</span></span> <span data-ttu-id="0b1e0-280">Anwendungen, die versuchen, mit einer Payload über <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize> auf einen JS-Interop-Befehl zu reagieren, lösen einen Fehler aus.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-280">Applications that attempt to respond to a JS interop call with a payload larger than <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize> throw an error.</span></span> <span data-ttu-id="0b1e0-281">Eine höherer Grenzwert kann durch Ändern von <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize> konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-281">A larger limit can be configured by modifying <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize>.</span></span> <span data-ttu-id="0b1e0-282">Im folgenden Beispiel wird die maximale Größe eingehender Nachrichten auf 64 KB (64 × 1.024 × 1.024) festgelegt:</span><span class="sxs-lookup"><span data-stu-id="0b1e0-282">The following example sets the maximum receive message size to 64 KB (64 \* 1024 \* 1024):</span></span>

```csharp
services.AddServerSideBlazor()
   .AddHubOptions(options => options.MaximumReceiveMessageSize = 64 * 1024 * 1024);
```

<span data-ttu-id="0b1e0-283">Wenn Sie den Grenzwert von SignalR erhöhen, verbrauchen Sie auch mehr Serverressourcen und setzen den Server einem erhöhten Risiko durch böswillige Benutzer aus.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-283">Increasing the SignalR limit comes at the cost of requiring the use of more server resources, and it exposes the server to increased risks from a malicious user.</span></span> <span data-ttu-id="0b1e0-284">Darüber hinaus kann das Lesen sehr großer Inhalte in den Arbeitsspeicher als Zeichenfolgen oder Bytearrays zu Zuordnungen führen, die vom Garbage Collector nur schlecht verarbeitet werden können. Dies kann zu zusätzlichen Leistungseinbußen führen.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-284">Additionally, reading a large amount of content in to memory as strings or byte arrays can also result in allocations that work poorly with the garbage collector, resulting in additional performance penalties.</span></span> <span data-ttu-id="0b1e0-285">Eine Möglichkeit zum Lesen großer Payloads besteht darin, den Inhalt in kleineren Blöcken zu senden und die Nutzdaten als <xref:System.IO.Stream> zu verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-285">One option for reading large payloads is to consider sending the content in smaller chunks and processing the payload as a <xref:System.IO.Stream>.</span></span> <span data-ttu-id="0b1e0-286">Diesen Ansatz können Sie anwenden, wenn große JSON-Payloads gelesen werden oder die Daten in JavaScript als unformatierte Bytes verfügbar sind.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-286">This can be used when reading large JSON payloads or if data is available in JavaScript as raw bytes.</span></span> <span data-ttu-id="0b1e0-287">Ein Beispiel für das Senden großer binärer Payloads in Blazor Server, bei dem ähnliche Techniken wie die `InputFile`-Komponente verwendet werden, finden Sie unter der [Beispiel-App BinarySubmit](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/BinarySubmit).</span><span class="sxs-lookup"><span data-stu-id="0b1e0-287">For an example that demonstrates sending large binary payloads in Blazor Server that uses techniques similar to the `InputFile` component, see the [Binary Submit sample app](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/BinarySubmit).</span></span>

<span data-ttu-id="0b1e0-288">Beachten Sie die folgenden Anleitungen, wenn Sie Code zum Übertragen großer Datenmengen zwischen JavaScript und Blazor entwickeln:</span><span class="sxs-lookup"><span data-stu-id="0b1e0-288">Consider the following guidance when developing code that transfers a large amount of data between JavaScript and Blazor:</span></span>

* <span data-ttu-id="0b1e0-289">Segmentieren Sie die Daten in kleinere Teile, und senden Sie die Datensegmente sequenziell, bis alle Daten vom Server empfangen wurden.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-289">Slice the data into smaller pieces, and send the data segments sequentially until all of the data is received by the server.</span></span>
* <span data-ttu-id="0b1e0-290">Ordnen Sie in JavaScript- und C#-Code keine großen Objekte zu.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-290">Don't allocate large objects in JavaScript and C# code.</span></span>
* <span data-ttu-id="0b1e0-291">Blockieren Sie den hauptsächlichen Benutzeroberflächenthread nicht für lange Zeiträume, wenn Sie Daten senden oder empfangen.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-291">Don't block the main UI thread for long periods when sending or receiving data.</span></span>
* <span data-ttu-id="0b1e0-292">Geben Sie belegten Arbeitsspeicher frei, wenn der Prozess abgeschlossen oder abgebrochen wird.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-292">Free any memory consumed when the process is completed or cancelled.</span></span>
* <span data-ttu-id="0b1e0-293">Erzwingen Sie die folgenden zusätzlichen Anforderungen aus Sicherheitsgründen:</span><span class="sxs-lookup"><span data-stu-id="0b1e0-293">Enforce the following additional requirements for security purposes:</span></span>
  * <span data-ttu-id="0b1e0-294">Deklarieren Sie die maximale Datei- oder Datengröße, die übermittelt werden kann.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-294">Declare the maximum file or data size that can be passed.</span></span>
  * <span data-ttu-id="0b1e0-295">Deklarieren Sie die minimale Uploadrate vom Client an den Server.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-295">Declare the minimum upload rate from the client to the server.</span></span>
* <span data-ttu-id="0b1e0-296">Nachdem die Daten vom Server empfangen wurden, ist mit den Daten Folgendes möglich:</span><span class="sxs-lookup"><span data-stu-id="0b1e0-296">After the data is received by the server, the data can be:</span></span>
  * <span data-ttu-id="0b1e0-297">Sie können temporär in einem Speicherpuffer gespeichert werden, bis alle Segmente gesammelt wurden.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-297">Temporarily stored in a memory buffer until all of the segments are collected.</span></span>
  * <span data-ttu-id="0b1e0-298">Sie können sofort verarbeitet werden.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-298">Consumed immediately.</span></span> <span data-ttu-id="0b1e0-299">Beispielsweise können die Daten sofort in einer Datenbank gespeichert oder auf den Datenträger geschrieben werden, wenn die einzelnen Segmente empfangen werden.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-299">For example, the data can be stored immediately in a database or written to disk as each segment is received.</span></span>
  
## <a name="js-modules"></a><span data-ttu-id="0b1e0-300">JS-Module</span><span class="sxs-lookup"><span data-stu-id="0b1e0-300">JS modules</span></span>

<span data-ttu-id="0b1e0-301">Für die JS-Isolation funktioniert JS-Interop mit der Standardunterstützung des Browsers für [ECMAScript-Module (ESM)](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules) ([ECMAScript-Spezifikation](https://tc39.es/ecma262/#sec-modules)).</span><span class="sxs-lookup"><span data-stu-id="0b1e0-301">For JS isolation, JS interop works with the browser's default support for [EcmaScript modules (ESM)](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules) ([ECMAScript specification](https://tc39.es/ecma262/#sec-modules)).</span></span>

## <a name="unmarshalled-js-interop"></a><span data-ttu-id="0b1e0-302">Nicht gemarshallt JS-Interop-Aufrufe</span><span class="sxs-lookup"><span data-stu-id="0b1e0-302">Unmarshalled JS interop</span></span>

<span data-ttu-id="0b1e0-303">Bei Blazor WebAssembly-Komponenten kann es zu Leistungseinbußen kommen, wenn .NET-Objekte für JS-Interop serialisiert werden und eine der folgenden Bedingungen zutrifft:</span><span class="sxs-lookup"><span data-stu-id="0b1e0-303">Blazor WebAssembly components may experience poor performance when .NET objects are serialized for JS interop and either of the following are true:</span></span>

* <span data-ttu-id="0b1e0-304">Eine große Menge von .NET-Objekten wird schnell serialisiert.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-304">A high volume of .NET objects are rapidly serialized.</span></span> <span data-ttu-id="0b1e0-305">Beispiel: JS-Interop-Aufrufe werden auf Grundlage des Bewegens eines Eingabegeräts durchgeführt, z. B. beim Drehen eines Mausrads.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-305">Example: JS interop calls are made on the basis of moving an input device, such as spinning a mouse wheel.</span></span>
* <span data-ttu-id="0b1e0-306">Für JS-Interop müssen große .NET-Objekte oder viele .NET-Objekte serialisiert werden.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-306">Large .NET objects or many .NET objects must be serialized for JS interop.</span></span> <span data-ttu-id="0b1e0-307">Beispiel: Für JS-Interop-Aufrufe müssen Dutzende von Dateien serialisiert werden.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-307">Example: JS interop calls require serializing dozens of files.</span></span>

<span data-ttu-id="0b1e0-308"><xref:Microsoft.JSInterop.IJSUnmarshalledObjectReference> steht für einen Verweis auf ein JavaScript-Objekt, dessen Funktionen aufgerufen werden können, ohne dass .NET-Daten serialisiert werden müssen.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-308"><xref:Microsoft.JSInterop.IJSUnmarshalledObjectReference> represents a reference to an JavaScript object whose functions can be invoked without the overhead of serializing .NET data.</span></span>

<span data-ttu-id="0b1e0-309">Im folgenden Beispiel:</span><span class="sxs-lookup"><span data-stu-id="0b1e0-309">In the following example:</span></span>

* <span data-ttu-id="0b1e0-310">Eine [Struktur](/dotnet/csharp/language-reference/builtin-types/struct), die eine Zeichenfolge und einen Integerwert enthält, wird unserialisiert an JavaScript übergegeben.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-310">A [struct](/dotnet/csharp/language-reference/builtin-types/struct) containing a string and an integer is passed unserialized to JavaScript.</span></span>
* <span data-ttu-id="0b1e0-311">JavaScript-Funktionen verarbeiten die Daten und geben entweder einen booleschen Wert oder eine Zeichenfolge an den Aufrufer zurück.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-311">JavaScript functions process the data and return either a boolean or string to the caller.</span></span>
* <span data-ttu-id="0b1e0-312">Eine JavaScript-Zeichenfolge ist nicht direkt in ein .NET-`string`-Objekt konvertierbar.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-312">A JavaScript string isn't directly convertible into a .NET `string` object.</span></span> <span data-ttu-id="0b1e0-313">Mit der `unmarshalledFunctionReturnString`-Funktion wird `BINDING.js_string_to_mono_string` aufgerufen, um die Konvertierung einer JavaScript-Zeichenfolge zu verwalten.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-313">The `unmarshalledFunctionReturnString` function calls `BINDING.js_string_to_mono_string` to manage the conversion of a Javascript string.</span></span>

> [!NOTE]
> <span data-ttu-id="0b1e0-314">Die folgenden Beispiele sind für dieses Szenario keine typischen Anwendungsfälle, da die [Struktur](/dotnet/csharp/language-reference/builtin-types/struct), die an JavaScript übergeben wird, nicht zu einer schlechten Komponentenleistung führt.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-314">The following examples aren't typical use cases for this scenario because the [struct](/dotnet/csharp/language-reference/builtin-types/struct) passed to JavaScript doesn't result in poor component performance.</span></span> <span data-ttu-id="0b1e0-315">Im Beispiel wird ein kleines Objekt verwendet, um nur die Konzepte für das Übergeben von unserialisierten .NET-Daten zu veranschaulichen.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-315">The example uses a small object merely to demonstrate the concepts for passing unserialized .NET data.</span></span>

<span data-ttu-id="0b1e0-316">Der Inhalt eines `<script>`-Blocks in `wwwroot/index.html` oder einer externen JavaScript-Datei, auf die durch `wwwroot/index.html` verwiesen wird:</span><span class="sxs-lookup"><span data-stu-id="0b1e0-316">Content of a `<script>` block in `wwwroot/index.html` or an external Javascript file referenced by `wwwroot/index.html`:</span></span>

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
> <span data-ttu-id="0b1e0-317">Der Name, das Verhalten und das Vorhandensein der `js_string_to_mono_string`-Funktion kann sich in einer zukünftigen Version von .NET ändern.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-317">The `js_string_to_mono_string` function name, behavior, and existence is subject to change in a future release of .NET.</span></span> <span data-ttu-id="0b1e0-318">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="0b1e0-318">For example:</span></span>
>
> * <span data-ttu-id="0b1e0-319">Die Funktion wird wahrscheinlich umbenannt.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-319">The function is likely to be renamed.</span></span>
> * <span data-ttu-id="0b1e0-320">Die Funktion selbst kann zugunsten automatischer Konvertierung von Zeichenfolgen durch das Framework entfernt werden.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-320">The function itself might be removed in favor of automatic conversion of strings by the framework.</span></span>

<span data-ttu-id="0b1e0-321">`Pages/UnmarshalledJSInterop.razor` (URL: `/unmarshalled-js-interop`):</span><span class="sxs-lookup"><span data-stu-id="0b1e0-321">`Pages/UnmarshalledJSInterop.razor` (URL: `/unmarshalled-js-interop`):</span></span>

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

<span data-ttu-id="0b1e0-322">Wenn eine `IJSUnmarshalledObjectReference`-Instanz nicht im C#-Code verworfen wird, kann sie in JavaScript verworfen werden.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-322">If an `IJSUnmarshalledObjectReference` instance isn't disposed in C# code, it can be disposed in JavaScript.</span></span> <span data-ttu-id="0b1e0-323">Die folgende `dispose`-Funktion verwirft die Objektreferenz, wenn sie aus JavaScript aufgerufen wird:</span><span class="sxs-lookup"><span data-stu-id="0b1e0-323">The following `dispose` function disposes the object reference when called from JavaScript:</span></span>

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

<span data-ttu-id="0b1e0-324">Arraytypen können mithilfe von `js_typed_array_to_array` aus JavaScript-Objekten in .NET-Objekte konvertiert werden, aber das JavaScript-Array muss ein typisiertes Array sein.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-324">Array types can be converted from JavaScript objects into .NET objects using `js_typed_array_to_array`, but the JavaScript array must be a typed array.</span></span> <span data-ttu-id="0b1e0-325">Arrays aus JavaScript können in C#-Code als .NET-Objektarray (`object[]`) gelesen werden.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-325">Arrays from JavaScript can be read in C# code as a .NET object array (`object[]`).</span></span>

<span data-ttu-id="0b1e0-326">Andere Datentypen, z. B. Zeichenfolgenarrays, können konvertiert werden, erfordern jedoch das Erstellen eines neuen Mono-Arrayobjekts (`mono_obj_array_new`) und das Festlegen seines Werts (`mono_obj_array_set`).</span><span class="sxs-lookup"><span data-stu-id="0b1e0-326">Other data types, such as string arrays, can be converted but require creating a new Mono array object (`mono_obj_array_new`) and setting its value (`mono_obj_array_set`).</span></span>

> [!WARNING]
> <span data-ttu-id="0b1e0-327">JavaScript-Funktionen, die vom Blazor-Framework bereitgestellt werden (z. B. `js_typed_array_to_array`, `mono_obj_array_new` und `mono_obj_array_set`), unterliegen Namensänderungen, Verhaltensänderungen oder der Entfernung in zukünftigen Versionen von .NET.</span><span class="sxs-lookup"><span data-stu-id="0b1e0-327">JavaScript functions provided by the Blazor framework, such as `js_typed_array_to_array`, `mono_obj_array_new`, and `mono_obj_array_set`, are subject to name changes, behavioral changes, or removal in future releases of .NET.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0b1e0-328">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="0b1e0-328">Additional resources</span></span>

* <xref:blazor/call-dotnet-from-javascript>
* [<span data-ttu-id="0b1e0-329">InteropComponent.razor-Beispiel (dotnet/AspNetCore-GitHub-Repository, Releasebranch 3.1)</span><span class="sxs-lookup"><span data-stu-id="0b1e0-329">InteropComponent.razor example (dotnet/AspNetCore GitHub repository, 3.1 release branch)</span></span>](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
