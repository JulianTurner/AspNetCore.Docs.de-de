<span data-ttu-id="dddb0-101">Während eine serverseitige blazor-App vorab erstellt wird, sind bestimmte Aktionen wie das Aufrufen von JavaScript nicht möglich, da keine Verbindung mit dem Browser hergestellt wurde.</span><span class="sxs-lookup"><span data-stu-id="dddb0-101">While a Blazor server-side app is prerendering, certain actions, such as calling into JavaScript, aren't possible because a connection with the browser hasn't been established.</span></span> <span data-ttu-id="dddb0-102">Komponenten müssen bei der vorab Erstellung möglicherweise anders gerzugerdert werden.</span><span class="sxs-lookup"><span data-stu-id="dddb0-102">Components may need to render differently when prerendered.</span></span>

<span data-ttu-id="dddb0-103">Um JavaScript-Interop-Aufrufe zu verzögern, bis die Verbindung mit dem Browser hergestellt wurde, können `OnAfterRenderAsync` Sie das Komponenten Lebenszyklus-Ereignis verwenden.</span><span class="sxs-lookup"><span data-stu-id="dddb0-103">To delay JavaScript interop calls until after the connection with the browser is established, you can use the `OnAfterRenderAsync` component lifecycle event.</span></span> <span data-ttu-id="dddb0-104">Dieses Ereignis wird nur aufgerufen, nachdem die APP vollständig gerendert wurde und die Client Verbindung hergestellt wurde.</span><span class="sxs-lookup"><span data-stu-id="dddb0-104">This event is only called after the app is fully rendered and the client connection is established.</span></span>

```cshtml
@using Microsoft.JSInterop
@inject IJSRuntime JSRuntime

<input @ref="myInput" @ref:suppressField value="Value set during render" />

@code {
    private ElementReference myInput;

    protected override void OnAfterRender()
    {
        JSRuntime.InvokeAsync<object>(
            "setElementValue", myInput, "Value set after render");
    }
}
```

<span data-ttu-id="dddb0-105">Die folgende Komponente veranschaulicht, wie JavaScript-Interop als Teil der Initialisierungs Logik einer Komponente auf eine Weise verwendet werden kann, die mit der vorab Generierung kompatibel ist.</span><span class="sxs-lookup"><span data-stu-id="dddb0-105">The following component demonstrates how to use JavaScript interop as part of a component's initialization logic in a way that's compatible with prerendering.</span></span> <span data-ttu-id="dddb0-106">Die-Komponente zeigt, dass es möglich ist, ein renderingupdate von innerhalb `OnAfterRenderAsync`von zu initiieren.</span><span class="sxs-lookup"><span data-stu-id="dddb0-106">The component shows that it's possible to trigger a rendering update from inside `OnAfterRenderAsync`.</span></span> <span data-ttu-id="dddb0-107">Der Entwickler muss in diesem Szenario vermeiden, eine Endlosschleife zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="dddb0-107">The developer must avoid creating an infinite loop in this scenario.</span></span>

<span data-ttu-id="dddb0-108">Wenn `JSRuntime.InvokeAsync` aufgerufen wird, `ElementRef` wird nur in `OnAfterRenderAsync` und nicht in einer früheren Lebenszyklus Methode verwendet, da es kein JavaScript-Element gibt, bis die Komponente gerendert wird.</span><span class="sxs-lookup"><span data-stu-id="dddb0-108">Where `JSRuntime.InvokeAsync` is called, `ElementRef` is only used in `OnAfterRenderAsync` and not in any earlier lifecycle method because there's no JavaScript element until after the component is rendered.</span></span>

<span data-ttu-id="dddb0-109">`StateHasChanged`wird aufgerufen, um die Komponente mit dem neuen Zustand, der vom JavaScript-Interop-Aufruf abgerufen wurde, erneut zu überarbeiten.</span><span class="sxs-lookup"><span data-stu-id="dddb0-109">`StateHasChanged` is called to rerender the component with the new state obtained from the JavaScript interop call.</span></span> <span data-ttu-id="dddb0-110">Der Code erstellt keine Endlosschleife, da `StateHasChanged` nur aufgerufen wird, `infoFromJs` `null`wenn den Wert hat.</span><span class="sxs-lookup"><span data-stu-id="dddb0-110">The code doesn't create an infinite loop because `StateHasChanged` is only called when `infoFromJs` is `null`.</span></span>

```cshtml
@page "/prerendered-interop"
@using Microsoft.AspNetCore.Components
@using Microsoft.JSInterop
@inject IComponentContext ComponentContext
@inject IJSRuntime JSRuntime

<p>
    Get value via JS interop call:
    <strong id="val-get-by-interop">@(infoFromJs ?? "No value yet")</strong>
</p>

<p>
    Set value via JS interop call:
    <input id="val-set-by-interop" @ref="myElem" @ref:suppressField />
</p>

@code {
    private string infoFromJs;
    private ElementReference myElem;

    protected override async Task OnAfterRenderAsync()
    {
        // TEMPORARY: Currently we need this guard to avoid making the interop
        // call during prerendering. Soon this will be unnecessary because we
        // will change OnAfterRenderAsync so that it won't run during the
        // prerendering phase.
        if (!ComponentContext.IsConnected)
        {
            return;
        }

        if (infoFromJs == null)
        {
            infoFromJs = await JSRuntime.InvokeAsync<string>(
                "setElementValue", myElem, "Hello from interop call");

            StateHasChanged();
        }
    }
}
```

<span data-ttu-id="dddb0-111">Verwenden Sie die `IsConnected` -Eigenschaft für den `IComponentContext` Dienst, um unterschiedliche Inhalte bedingt zu erzeugen, je nachdem, ob die APP derzeit Inhalt vorab vorläuft.</span><span class="sxs-lookup"><span data-stu-id="dddb0-111">To conditionally render different content based on whether the app is currently prerendering content, use the `IsConnected` property on the `IComponentContext` service.</span></span> <span data-ttu-id="dddb0-112">Wenn serverseitig ausgeführt wird, `IsConnected` gibt nur `true` zurück, wenn eine aktive Verbindung mit dem Client vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="dddb0-112">When running server-side, `IsConnected` only returns `true` if there's an active connection to the client.</span></span> <span data-ttu-id="dddb0-113">Sie wird immer `true` zurückgegeben, wenn die Client seitige Ausführung ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="dddb0-113">It always returns `true` when running client-side.</span></span>

```cshtml
@page "/isconnected-example"
@using Microsoft.AspNetCore.Components.Services
@inject IComponentContext ComponentContext

<h1>IsConnected Example</h1>

<p>
    Current state:
    <strong id="connected-state">
        @(ComponentContext.IsConnected ? "connected" : "not connected")
    </strong>
</p>

<p>
    Clicks:
    <strong id="count">@count</strong>
    <button id="increment-count" @onclick="@(() => count++)">Click me</button>
</p>

@code {
    private int count;
}
```