---
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
ms.openlocfilehash: c152524e0acd3803bd3b8078f667cce01180e25d
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551328"
---
Während für eine Blazor Server-App ein Prerendering durchgeführt wird, sind bestimmte Aktionen (z. B. Aufrufe in JavaScript) nicht möglich, da noch keine Verbindung mit dem Browser hergestellt wurde. Komponenten müssen wahrscheinlich unterschiedlich rendern, wenn dafür ein Prerendering durchgeführt wurde.

Wenn Sie JavaScript-Interop-Aufrufe bis nach Herstellung der Verbindung zum Browser verzögern möchten, können Sie das [Lebenszyklusereignis der OnAfterRenderAsync-Komponente](xref:blazor/components/lifecycle#after-component-render) verwenden. Dieses Ereignis wird nur aufgerufen, nachdem die App vollständig gerendert und die Clientverbindung hergestellt wurde.

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

Stellen Sie für den obigen Beispielcode eine `setElementText` JavaScript-Funktion im `<head>`-Element von `wwwroot/index.html` (Blazor WebAssembly) oder `Pages/_Host.cshtml` (Blazor Server) bereit. Die Funktion wird mit <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> aufgerufen und gibt keinen Wert zurück:

```html
<script>
  window.setElementText = (element, text) => element.innerText = text;
</script>
```

> [!WARNING]
> Im vorangehenden Beispiel wird das Dokumentobjektmodell (DOM) direkt zu Demonstrationszwecken geändert. Das direkte Ändern des DOM mit JavaScript wird in den meisten Szenarios nicht empfohlen, da JavaScript die Änderungsnachverfolgung von Blazor beeinträchtigen kann.

Die folgende Komponente veranschaulicht, wie JavaScript-Interop als Teil der Initialisierungslogik einer Komponente auf eine Weise verwendet werden kann, die mit dem Prerendering kompatibel ist. Die Komponente zeigt, dass es möglich ist, in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> ein Renderingupdate zu initiieren. Der Entwickler muss in diesem Szenario vermeiden, eine Endlosschleife zu erstellen.

Wenn <xref:Microsoft.JSInterop.JSRuntime.InvokeAsync%2A?displayProperty=nameWithType> aufgerufen wird, wird `ElementRef` nur in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> und nicht in einer früheren Lebenszyklusmethode verwendet, da es kein JavaScript-Element gibt, bis die Komponente gerendert wird.

[StateHasChanged](xref:blazor/components/lifecycle#state-changes) wird aufgerufen, um die Komponente mit dem neuen Zustand, der vom JavaScript Interop-Aufruf abgerufen wurde, erneut zu rendern. Weitere Informationen erhalten Sie unter <xref:blazor/components/rendering>. Der Code erstellt keine Endlosschleife, da `StateHasChanged` nur aufgerufen wird, wenn `infoFromJs` `null` ist.

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

Stellen Sie für den obigen Beispielcode eine `setElementText` JavaScript-Funktion im `<head>`-Element von `wwwroot/index.html` (Blazor WebAssembly) oder `Pages/_Host.cshtml` (Blazor Server) bereit. Die Funktion wird mit <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType> aufgerufen und gibt einen Wert zurück:

```html
<script>
  window.setElementText = (element, text) => {
    element.innerText = text;
    return text;
  };
</script>
```

> [!WARNING]
> Im vorangehenden Beispiel wird das Dokumentobjektmodell (DOM) direkt zu Demonstrationszwecken geändert. Das direkte Ändern des DOM mit JavaScript wird in den meisten Szenarios nicht empfohlen, da JavaScript die Änderungsnachverfolgung von Blazor beeinträchtigen kann.
