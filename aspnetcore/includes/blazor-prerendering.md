<span data-ttu-id="15352-101">Während für eine Blazor Server-App ein Prerendering erfolgt, sind bestimmte Aktionen (z. B. Aufrufe in JavaScript) nicht möglich, da noch keine Verbindung mit dem Browser hergestellt wurde.</span><span class="sxs-lookup"><span data-stu-id="15352-101">While a Blazor Server app is prerendering, certain actions, such as calling into JavaScript, aren't possible because a connection with the browser hasn't been established.</span></span> <span data-ttu-id="15352-102">Komponenten müssen wahrscheinlich unterschiedlich rendern, wenn dafür ein Prerendering durchgeführt wurde.</span><span class="sxs-lookup"><span data-stu-id="15352-102">Components may need to render differently when prerendered.</span></span>

<span data-ttu-id="15352-103">Wenn Sie JavaScript-Interop-Aufrufe bis nach Herstellung der Verbindung zum Browser verzögern möchten, können Sie das [Lebenszyklusereignis der OnAfterRenderAsync-Komponente](xref:blazor/components/lifecycle#after-component-render) verwenden.</span><span class="sxs-lookup"><span data-stu-id="15352-103">To delay JavaScript interop calls until after the connection with the browser is established, you can use the [OnAfterRenderAsync component lifecycle event](xref:blazor/components/lifecycle#after-component-render).</span></span> <span data-ttu-id="15352-104">Dieses Ereignis wird nur aufgerufen, nachdem die App vollständig gerendert und die Clientverbindung hergestellt wurde.</span><span class="sxs-lookup"><span data-stu-id="15352-104">This event is only called after the app is fully rendered and the client connection is established.</span></span>

```cshtml
@using Microsoft.JSInterop
@inject IJSRuntime JSRuntime

<div @ref="divElement">Text during render</div>

@code {
    private ElementReference divElement;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            await JSRuntime.InvokeVoidAsync(
                "setElementText", divElement, "Text after render");
        }
    }
}
```

<span data-ttu-id="15352-105">Stellen Sie für den obigen Beispielcode eine `setElementText`-JavaScript-Funktion im `<head>`-Element von `wwwroot/index.html` (Blazor WebAssembly) oder `Pages/_Host.cshtml` (Blazor Server) bereit.</span><span class="sxs-lookup"><span data-stu-id="15352-105">For the preceding example code, provide a `setElementText` JavaScript function inside the `<head>` element of `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server).</span></span> <span data-ttu-id="15352-106">Die Funktion wird mit <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> aufgerufen und gibt keinen Wert zurück:</span><span class="sxs-lookup"><span data-stu-id="15352-106">The function is called with <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> and doesn't return a value:</span></span>

```html
<script>
  window.setElementText = (element, text) => element.innerText = text;
</script>
```

> [!WARNING]
> <span data-ttu-id="15352-107">Im vorangehenden Beispiel wird das Dokumentobjektmodell (DOM) direkt zu Demonstrationszwecken geändert.</span><span class="sxs-lookup"><span data-stu-id="15352-107">The preceding example modifies the Document Object Model (DOM) directly for demonstration purposes only.</span></span> <span data-ttu-id="15352-108">Das direkte Ändern des DOM mit JavaScript wird in den meisten Szenarios nicht empfohlen, da JavaScript die Änderungsnachverfolgung von Blazor beeinträchtigen kann.</span><span class="sxs-lookup"><span data-stu-id="15352-108">Directly modifying the DOM with JavaScript isn't recommended in most scenarios because JavaScript can interfere with Blazor's change tracking.</span></span>

<span data-ttu-id="15352-109">Die folgende Komponente veranschaulicht, wie JavaScript-Interop als Teil der Initialisierungslogik einer Komponente auf eine Weise verwendet werden kann, die mit dem Prerendering kompatibel ist.</span><span class="sxs-lookup"><span data-stu-id="15352-109">The following component demonstrates how to use JavaScript interop as part of a component's initialization logic in a way that's compatible with prerendering.</span></span> <span data-ttu-id="15352-110">Die Komponente zeigt, dass es möglich ist, in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> ein Renderingupdate zu initiieren.</span><span class="sxs-lookup"><span data-stu-id="15352-110">The component shows that it's possible to trigger a rendering update from inside <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>.</span></span> <span data-ttu-id="15352-111">Der Entwickler muss in diesem Szenario vermeiden, eine Endlosschleife zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="15352-111">The developer must avoid creating an infinite loop in this scenario.</span></span>

<span data-ttu-id="15352-112">Wenn <xref:Microsoft.JSInterop.JSRuntime.InvokeAsync%2A?displayProperty=nameWithType> aufgerufen wird, wird `ElementRef` nur in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> und nicht in einer früheren Lebenszyklusmethode verwendet, da es kein JavaScript-Element gibt, bis die Komponente gerendert wird.</span><span class="sxs-lookup"><span data-stu-id="15352-112">Where <xref:Microsoft.JSInterop.JSRuntime.InvokeAsync%2A?displayProperty=nameWithType> is called, `ElementRef` is only used in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> and not in any earlier lifecycle method because there's no JavaScript element until after the component is rendered.</span></span>

<span data-ttu-id="15352-113">[StateHasChanged](xref:blazor/components/lifecycle#state-changes) wird aufgerufen, um die Komponente mit dem neuen Zustand, der vom JavaScript-Interop-Aufruf abgerufen wurde, erneut zu überarbeiten.</span><span class="sxs-lookup"><span data-stu-id="15352-113">[StateHasChanged](xref:blazor/components/lifecycle#state-changes) is called to rerender the component with the new state obtained from the JavaScript interop call.</span></span> <span data-ttu-id="15352-114">Der Code erstellt keine Endlosschleife, da `StateHasChanged` nur aufgerufen wird, wenn `infoFromJs` `null` ist.</span><span class="sxs-lookup"><span data-stu-id="15352-114">The code doesn't create an infinite loop because `StateHasChanged` is only called when `infoFromJs` is `null`.</span></span>

```cshtml
@page "/prerendered-interop"
@using Microsoft.AspNetCore.Components
@using Microsoft.JSInterop
@inject IJSRuntime JSRuntime

<p>
    Get value via JS interop call:
    <strong id="val-get-by-interop">@(infoFromJs ?? "No value yet")</strong>
</p>

Set value via JS interop call:
<div id="val-set-by-interop" @ref="divElement"></div>

@code {
    private string infoFromJs;
    private ElementReference divElement;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender && infoFromJs == null)
        {
            infoFromJs = await JSRuntime.InvokeAsync<string>(
                "setElementText", divElement, "Hello from interop call!");

            StateHasChanged();
        }
    }
}
```

<span data-ttu-id="15352-115">Stellen Sie für den obigen Beispielcode eine `setElementText`-JavaScript-Funktion im `<head>`-Element von `wwwroot/index.html` (Blazor WebAssembly) oder `Pages/_Host.cshtml` (Blazor Server) bereit.</span><span class="sxs-lookup"><span data-stu-id="15352-115">For the preceding example code, provide a `setElementText` JavaScript function inside the `<head>` element of `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server).</span></span> <span data-ttu-id="15352-116">Die Funktion wird mit <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType> aufgerufen und gibt einen Wert zurück:</span><span class="sxs-lookup"><span data-stu-id="15352-116">The function is called with<xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType> and returns a value:</span></span>

```html
<script>
  window.setElementText = (element, text) => {
    element.innerText = text;
    return text;
  };
</script>
```

> [!WARNING]
> <span data-ttu-id="15352-117">Im vorangehenden Beispiel wird das Dokumentobjektmodell (DOM) direkt zu Demonstrationszwecken geändert.</span><span class="sxs-lookup"><span data-stu-id="15352-117">The preceding example modifies the Document Object Model (DOM) directly for demonstration purposes only.</span></span> <span data-ttu-id="15352-118">Das direkte Ändern des DOM mit JavaScript wird in den meisten Szenarios nicht empfohlen, da JavaScript die Änderungsnachverfolgung von Blazor beeinträchtigen kann.</span><span class="sxs-lookup"><span data-stu-id="15352-118">Directly modifying the DOM with JavaScript isn't recommended in most scenarios because JavaScript can interfere with Blazor's change tracking.</span></span>
