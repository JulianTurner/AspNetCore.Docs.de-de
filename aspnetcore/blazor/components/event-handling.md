---
title: ASP.NET Core Blazor-Ereignisbehandlung
author: guardrex
description: Informieren Sie sich über die Features zur Ereignisbehandlung von Blazor, einschließlich Ereignisargumenttypen, Ereignisrückrufe und die Verwaltung von standardmäßigen Browserereignissen.
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
uid: blazor/components/event-handling
ms.openlocfilehash: 0d832d98ac9d1364b5db2bf65f31cbc5442db7f6
ms.sourcegitcommit: 74f4a4ddbe3c2f11e2e09d05d2a979784d89d3f5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/27/2020
ms.locfileid: "91393781"
---
# <a name="aspnet-core-no-locblazor-event-handling"></a><span data-ttu-id="24c06-103">ASP.NET Core Blazor-Ereignisbehandlung</span><span class="sxs-lookup"><span data-stu-id="24c06-103">ASP.NET Core Blazor event handling</span></span>

<span data-ttu-id="24c06-104">Von [Luke Latham](https://github.com/guardrex) und [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="24c06-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="24c06-105">Razor-Komponenten bieten Features zur Ereignisbehandlung.</span><span class="sxs-lookup"><span data-stu-id="24c06-105">Razor components provide event handling features.</span></span> <span data-ttu-id="24c06-106">Bei einem HTML-Elementattribut namens [`@on{EVENT}`](xref:mvc/views/razor#onevent) (z. B. `@onclick`) mit einem Wert vom Typ „Delegat“ behandelt eine Razor-Komponente den Wert des Attributs als einen Ereignishandler.</span><span class="sxs-lookup"><span data-stu-id="24c06-106">For an HTML element attribute named [`@on{EVENT}`](xref:mvc/views/razor#onevent) (for example, `@onclick`) with a delegate-typed value, a Razor component treats the attribute's value as an event handler.</span></span>

<span data-ttu-id="24c06-107">Der folgende Code ruft die `UpdateHeading`-Methode auf, wenn die Schaltfläche auf der Benutzeroberfläche ausgewählt wird:</span><span class="sxs-lookup"><span data-stu-id="24c06-107">The following code calls the `UpdateHeading` method when the button is selected in the UI:</span></span>

```razor
<button class="btn btn-primary" @onclick="UpdateHeading">
    Update heading
</button>

@code {
    private void UpdateHeading(MouseEventArgs e)
    {
        ...
    }
}
```

<span data-ttu-id="24c06-108">Der folgende Code ruft die `CheckChanged`-Methode auf, wenn das Kontrollkästchen auf der Benutzeroberfläche geändert wird:</span><span class="sxs-lookup"><span data-stu-id="24c06-108">The following code calls the `CheckChanged` method when the check box is changed in the UI:</span></span>

```razor
<input type="checkbox" class="form-check-input" @onchange="CheckChanged" />

@code {
    private void CheckChanged()
    {
        ...
    }
}
```

<span data-ttu-id="24c06-109">Ereignishandler können auch asynchron sein und ein <xref:System.Threading.Tasks.Task> zurückgeben.</span><span class="sxs-lookup"><span data-stu-id="24c06-109">Event handlers can also be asynchronous and return a <xref:System.Threading.Tasks.Task>.</span></span> <span data-ttu-id="24c06-110">Es ist nicht erforderlich, [StateHasChanged](xref:blazor/components/lifecycle#state-changes) manuell aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="24c06-110">There's no need to manually call [StateHasChanged](xref:blazor/components/lifecycle#state-changes).</span></span> <span data-ttu-id="24c06-111">Ausnahmen werden protokolliert, wenn sie auftreten.</span><span class="sxs-lookup"><span data-stu-id="24c06-111">Exceptions are logged when they occur.</span></span>

<span data-ttu-id="24c06-112">Im folgenden Beispiel wird `UpdateHeading` asynchron aufgerufen, wenn die Schaltfläche ausgewählt wird:</span><span class="sxs-lookup"><span data-stu-id="24c06-112">In the following example, `UpdateHeading` is called asynchronously when the button is selected:</span></span>

```razor
<button class="btn btn-primary" @onclick="UpdateHeading">
    Update heading
</button>

@code {
    private async Task UpdateHeading(MouseEventArgs e)
    {
        ...
    }
}
```

## <a name="event-argument-types"></a><span data-ttu-id="24c06-113">Ereignisargumenttypen</span><span class="sxs-lookup"><span data-stu-id="24c06-113">Event argument types</span></span>

<span data-ttu-id="24c06-114">Für einige Ereignisse sind Ereignisargumenttypen zulässig.</span><span class="sxs-lookup"><span data-stu-id="24c06-114">For some events, event argument types are permitted.</span></span> <span data-ttu-id="24c06-115">Die Angabe eines Ereignisparameters in einer Ereignismethodendefinition ist optional und nur erforderlich, wenn der Ereignistyp in der Methode verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="24c06-115">Specifying an event parameter in an event method definition is optional and only necessary if the event type is used in the method.</span></span> <span data-ttu-id="24c06-116">Im folgenden Beispiel wird das Ereignisargument `MouseEventArgs` in der Methode `ShowMessage` verwendet, um den Nachrichtentext festzulegen:</span><span class="sxs-lookup"><span data-stu-id="24c06-116">In the following example, the `MouseEventArgs` event argument is used in the `ShowMessage` method to set message text:</span></span>

```csharp
private void ShowMessage(MouseEventArgs e)
{
    messageText = $"The mouse is at coordinates: {e.ScreenX}:{e.ScreenY}";
}
```

<span data-ttu-id="24c06-117">Die unterstützten <xref:System.EventArgs> sind in der folgenden Tabelle aufgeführt.</span><span class="sxs-lookup"><span data-stu-id="24c06-117">Supported <xref:System.EventArgs> are shown in the following table.</span></span>

::: moniker range=">= aspnetcore-5.0"

| <span data-ttu-id="24c06-118">event</span><span class="sxs-lookup"><span data-stu-id="24c06-118">Event</span></span>            | <span data-ttu-id="24c06-119">Klasse</span><span class="sxs-lookup"><span data-stu-id="24c06-119">Class</span></span>  | <span data-ttu-id="24c06-120">DOM-Ereignisse und -Hinweise</span><span class="sxs-lookup"><span data-stu-id="24c06-120">DOM events and notes</span></span> |
| ---------------- | ------ | -------------------- |
| <span data-ttu-id="24c06-121">Zwischenablage</span><span class="sxs-lookup"><span data-stu-id="24c06-121">Clipboard</span></span>        | <xref:Microsoft.AspNetCore.Components.Web.ClipboardEventArgs> | <span data-ttu-id="24c06-122">`oncut`, `oncopy`, `onpaste`</span><span class="sxs-lookup"><span data-stu-id="24c06-122">`oncut`, `oncopy`, `onpaste`</span></span> |
| <span data-ttu-id="24c06-123">Ziehen</span><span class="sxs-lookup"><span data-stu-id="24c06-123">Drag</span></span>             | <xref:Microsoft.AspNetCore.Components.Web.DragEventArgs> | <span data-ttu-id="24c06-124">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span><span class="sxs-lookup"><span data-stu-id="24c06-124">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span></span><br><br><span data-ttu-id="24c06-125"><xref:Microsoft.AspNetCore.Components.Web.DataTransfer> und <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> speichern gezogene Elementdaten.</span><span class="sxs-lookup"><span data-stu-id="24c06-125"><xref:Microsoft.AspNetCore.Components.Web.DataTransfer> and <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> hold dragged item data.</span></span><br><br><span data-ttu-id="24c06-126">Implementiert Drag & Drop in Blazor-Apps mit [JS-Interop](xref:blazor/call-javascript-from-dotnet) und der [HTML-Drag & Drop-API](https://developer.mozilla.org/docs/Web/API/HTML_Drag_and_Drop_API).</span><span class="sxs-lookup"><span data-stu-id="24c06-126">Implement drag and drop in Blazor apps using [JS interop](xref:blazor/call-javascript-from-dotnet) with [HTML Drag and Drop API](https://developer.mozilla.org/docs/Web/API/HTML_Drag_and_Drop_API).</span></span> |
| <span data-ttu-id="24c06-127">Fehler</span><span class="sxs-lookup"><span data-stu-id="24c06-127">Error</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.ErrorEventArgs> | `onerror` |
| <span data-ttu-id="24c06-128">event</span><span class="sxs-lookup"><span data-stu-id="24c06-128">Event</span></span>            | <xref:System.EventArgs> | <span data-ttu-id="24c06-129">*Allgemein*</span><span class="sxs-lookup"><span data-stu-id="24c06-129">*General*</span></span><br><span data-ttu-id="24c06-130">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span><span class="sxs-lookup"><span data-stu-id="24c06-130">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span></span><br><br><span data-ttu-id="24c06-131">*Zwischenablage*</span><span class="sxs-lookup"><span data-stu-id="24c06-131">*Clipboard*</span></span><br><span data-ttu-id="24c06-132">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span><span class="sxs-lookup"><span data-stu-id="24c06-132">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span></span><br><br><span data-ttu-id="24c06-133">*Eingabe*</span><span class="sxs-lookup"><span data-stu-id="24c06-133">*Input*</span></span><br><span data-ttu-id="24c06-134">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span><span class="sxs-lookup"><span data-stu-id="24c06-134">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span></span><br><br><span data-ttu-id="24c06-135">*Medien*</span><span class="sxs-lookup"><span data-stu-id="24c06-135">*Media*</span></span><br><span data-ttu-id="24c06-136">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onended`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `ontoggle`, `onvolumechange`, `onwaiting`</span><span class="sxs-lookup"><span data-stu-id="24c06-136">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onended`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `ontoggle`, `onvolumechange`, `onwaiting`</span></span><br><br><span data-ttu-id="24c06-137"><xref:Microsoft.AspNetCore.Components.Web.EventHandlers> enthält Attribute zum Konfigurieren der Zuordnungen zwischen Ereignisnamen und Ereignisargumenttypen.</span><span class="sxs-lookup"><span data-stu-id="24c06-137"><xref:Microsoft.AspNetCore.Components.Web.EventHandlers> holds attributes to configure the mappings between event names and event argument types.</span></span> |
| <span data-ttu-id="24c06-138">Fokus</span><span class="sxs-lookup"><span data-stu-id="24c06-138">Focus</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.FocusEventArgs> | <span data-ttu-id="24c06-139">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span><span class="sxs-lookup"><span data-stu-id="24c06-139">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span></span><br><br><span data-ttu-id="24c06-140">Umfasst nicht die Unterstützung für `relatedTarget`.</span><span class="sxs-lookup"><span data-stu-id="24c06-140">Doesn't include support for `relatedTarget`.</span></span> |
| <span data-ttu-id="24c06-141">Eingabe</span><span class="sxs-lookup"><span data-stu-id="24c06-141">Input</span></span>            | <xref:Microsoft.AspNetCore.Components.ChangeEventArgs> | <span data-ttu-id="24c06-142">`onchange`, `oninput`</span><span class="sxs-lookup"><span data-stu-id="24c06-142">`onchange`, `oninput`</span></span> |
| <span data-ttu-id="24c06-143">Tastatur</span><span class="sxs-lookup"><span data-stu-id="24c06-143">Keyboard</span></span>         | <xref:Microsoft.AspNetCore.Components.Web.KeyboardEventArgs> | <span data-ttu-id="24c06-144">`onkeydown`, `onkeypress`, `onkeyup`</span><span class="sxs-lookup"><span data-stu-id="24c06-144">`onkeydown`, `onkeypress`, `onkeyup`</span></span> |
| <span data-ttu-id="24c06-145">Maus</span><span class="sxs-lookup"><span data-stu-id="24c06-145">Mouse</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs> | <span data-ttu-id="24c06-146">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span><span class="sxs-lookup"><span data-stu-id="24c06-146">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span></span> |
| <span data-ttu-id="24c06-147">Mauszeiger</span><span class="sxs-lookup"><span data-stu-id="24c06-147">Mouse pointer</span></span>    | <xref:Microsoft.AspNetCore.Components.Web.PointerEventArgs> | <span data-ttu-id="24c06-148">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span><span class="sxs-lookup"><span data-stu-id="24c06-148">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span></span> |
| <span data-ttu-id="24c06-149">Mausrad</span><span class="sxs-lookup"><span data-stu-id="24c06-149">Mouse wheel</span></span>      | <xref:Microsoft.AspNetCore.Components.Web.WheelEventArgs> | <span data-ttu-id="24c06-150">`onwheel`, `onmousewheel`</span><span class="sxs-lookup"><span data-stu-id="24c06-150">`onwheel`, `onmousewheel`</span></span> |
| <span data-ttu-id="24c06-151">Status</span><span class="sxs-lookup"><span data-stu-id="24c06-151">Progress</span></span>         | <xref:Microsoft.AspNetCore.Components.Web.ProgressEventArgs> | <span data-ttu-id="24c06-152">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span><span class="sxs-lookup"><span data-stu-id="24c06-152">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span></span> |
| <span data-ttu-id="24c06-153">Toucheingabe</span><span class="sxs-lookup"><span data-stu-id="24c06-153">Touch</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.TouchEventArgs> | <span data-ttu-id="24c06-154">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span><span class="sxs-lookup"><span data-stu-id="24c06-154">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span></span><br><br><span data-ttu-id="24c06-155"><xref:Microsoft.AspNetCore.Components.Web.TouchPoint> stellt einen einzelnen Kontaktpunkt auf einem Gerät mit Berührungseingabe dar.</span><span class="sxs-lookup"><span data-stu-id="24c06-155"><xref:Microsoft.AspNetCore.Components.Web.TouchPoint> represents a single contact point on a touch-sensitive device.</span></span> |

::: moniker-end

::: moniker range="< aspnetcore-5.0"

| <span data-ttu-id="24c06-156">event</span><span class="sxs-lookup"><span data-stu-id="24c06-156">Event</span></span>            | <span data-ttu-id="24c06-157">Klasse</span><span class="sxs-lookup"><span data-stu-id="24c06-157">Class</span></span> | <span data-ttu-id="24c06-158">DOM-Ereignisse und -Hinweise</span><span class="sxs-lookup"><span data-stu-id="24c06-158">DOM events and notes</span></span> |
| ---------------- | ----- | -------------------- |
| <span data-ttu-id="24c06-159">Zwischenablage</span><span class="sxs-lookup"><span data-stu-id="24c06-159">Clipboard</span></span>        | <xref:Microsoft.AspNetCore.Components.Web.ClipboardEventArgs> | <span data-ttu-id="24c06-160">`oncut`, `oncopy`, `onpaste`</span><span class="sxs-lookup"><span data-stu-id="24c06-160">`oncut`, `oncopy`, `onpaste`</span></span> |
| <span data-ttu-id="24c06-161">Ziehen</span><span class="sxs-lookup"><span data-stu-id="24c06-161">Drag</span></span>             | <xref:Microsoft.AspNetCore.Components.Web.DragEventArgs> | <span data-ttu-id="24c06-162">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span><span class="sxs-lookup"><span data-stu-id="24c06-162">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span></span><br><br><span data-ttu-id="24c06-163"><xref:Microsoft.AspNetCore.Components.Web.DataTransfer> und <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> speichern gezogene Elementdaten.</span><span class="sxs-lookup"><span data-stu-id="24c06-163"><xref:Microsoft.AspNetCore.Components.Web.DataTransfer> and <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> hold dragged item data.</span></span><br><br><span data-ttu-id="24c06-164">Implementiert Drag & Drop in Blazor-Apps mit [JS-Interop](xref:blazor/call-javascript-from-dotnet) und der [HTML-Drag & Drop-API](https://developer.mozilla.org/docs/Web/API/HTML_Drag_and_Drop_API).</span><span class="sxs-lookup"><span data-stu-id="24c06-164">Implement drag and drop in Blazor apps using [JS interop](xref:blazor/call-javascript-from-dotnet) with [HTML Drag and Drop API](https://developer.mozilla.org/docs/Web/API/HTML_Drag_and_Drop_API).</span></span> |
| <span data-ttu-id="24c06-165">Fehler</span><span class="sxs-lookup"><span data-stu-id="24c06-165">Error</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.ErrorEventArgs> | `onerror` |
| <span data-ttu-id="24c06-166">event</span><span class="sxs-lookup"><span data-stu-id="24c06-166">Event</span></span>            | <xref:System.EventArgs> | <span data-ttu-id="24c06-167">*Allgemein*</span><span class="sxs-lookup"><span data-stu-id="24c06-167">*General*</span></span><br><span data-ttu-id="24c06-168">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span><span class="sxs-lookup"><span data-stu-id="24c06-168">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span></span><br><br><span data-ttu-id="24c06-169">*Zwischenablage*</span><span class="sxs-lookup"><span data-stu-id="24c06-169">*Clipboard*</span></span><br><span data-ttu-id="24c06-170">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span><span class="sxs-lookup"><span data-stu-id="24c06-170">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span></span><br><br><span data-ttu-id="24c06-171">*Eingabe*</span><span class="sxs-lookup"><span data-stu-id="24c06-171">*Input*</span></span><br><span data-ttu-id="24c06-172">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span><span class="sxs-lookup"><span data-stu-id="24c06-172">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span></span><br><br><span data-ttu-id="24c06-173">*Medien*</span><span class="sxs-lookup"><span data-stu-id="24c06-173">*Media*</span></span><br><span data-ttu-id="24c06-174">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onended`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span><span class="sxs-lookup"><span data-stu-id="24c06-174">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onended`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span></span><br><br><span data-ttu-id="24c06-175"><xref:Microsoft.AspNetCore.Components.Web.EventHandlers> enthält Attribute zum Konfigurieren der Zuordnungen zwischen Ereignisnamen und Ereignisargumenttypen.</span><span class="sxs-lookup"><span data-stu-id="24c06-175"><xref:Microsoft.AspNetCore.Components.Web.EventHandlers> holds attributes to configure the mappings between event names and event argument types.</span></span> |
| <span data-ttu-id="24c06-176">Fokus</span><span class="sxs-lookup"><span data-stu-id="24c06-176">Focus</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.FocusEventArgs> | <span data-ttu-id="24c06-177">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span><span class="sxs-lookup"><span data-stu-id="24c06-177">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span></span><br><br><span data-ttu-id="24c06-178">Umfasst nicht die Unterstützung für `relatedTarget`.</span><span class="sxs-lookup"><span data-stu-id="24c06-178">Doesn't include support for `relatedTarget`.</span></span> |
| <span data-ttu-id="24c06-179">Eingabe</span><span class="sxs-lookup"><span data-stu-id="24c06-179">Input</span></span>            | <xref:Microsoft.AspNetCore.Components.ChangeEventArgs> | <span data-ttu-id="24c06-180">`onchange`, `oninput`</span><span class="sxs-lookup"><span data-stu-id="24c06-180">`onchange`, `oninput`</span></span> |
| <span data-ttu-id="24c06-181">Tastatur</span><span class="sxs-lookup"><span data-stu-id="24c06-181">Keyboard</span></span>         | <xref:Microsoft.AspNetCore.Components.Web.KeyboardEventArgs> | <span data-ttu-id="24c06-182">`onkeydown`, `onkeypress`, `onkeyup`</span><span class="sxs-lookup"><span data-stu-id="24c06-182">`onkeydown`, `onkeypress`, `onkeyup`</span></span> |
| <span data-ttu-id="24c06-183">Maus</span><span class="sxs-lookup"><span data-stu-id="24c06-183">Mouse</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs> | <span data-ttu-id="24c06-184">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span><span class="sxs-lookup"><span data-stu-id="24c06-184">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span></span> |
| <span data-ttu-id="24c06-185">Mauszeiger</span><span class="sxs-lookup"><span data-stu-id="24c06-185">Mouse pointer</span></span>    | <xref:Microsoft.AspNetCore.Components.Web.PointerEventArgs> | <span data-ttu-id="24c06-186">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span><span class="sxs-lookup"><span data-stu-id="24c06-186">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span></span> |
| <span data-ttu-id="24c06-187">Mausrad</span><span class="sxs-lookup"><span data-stu-id="24c06-187">Mouse wheel</span></span>      | <xref:Microsoft.AspNetCore.Components.Web.WheelEventArgs> | <span data-ttu-id="24c06-188">`onwheel`, `onmousewheel`</span><span class="sxs-lookup"><span data-stu-id="24c06-188">`onwheel`, `onmousewheel`</span></span> |
| <span data-ttu-id="24c06-189">Status</span><span class="sxs-lookup"><span data-stu-id="24c06-189">Progress</span></span>         | <xref:Microsoft.AspNetCore.Components.Web.ProgressEventArgs> | <span data-ttu-id="24c06-190">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span><span class="sxs-lookup"><span data-stu-id="24c06-190">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span></span> |
| <span data-ttu-id="24c06-191">Toucheingabe</span><span class="sxs-lookup"><span data-stu-id="24c06-191">Touch</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.TouchEventArgs> | <span data-ttu-id="24c06-192">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span><span class="sxs-lookup"><span data-stu-id="24c06-192">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span></span><br><br><span data-ttu-id="24c06-193"><xref:Microsoft.AspNetCore.Components.Web.TouchPoint> stellt einen einzelnen Kontaktpunkt auf einem Gerät mit Berührungseingabe dar.</span><span class="sxs-lookup"><span data-stu-id="24c06-193"><xref:Microsoft.AspNetCore.Components.Web.TouchPoint> represents a single contact point on a touch-sensitive device.</span></span> |

::: moniker-end

<span data-ttu-id="24c06-194">Weitere Informationen finden Sie in den folgenden Ressourcen:</span><span class="sxs-lookup"><span data-stu-id="24c06-194">For more information, see the following resources:</span></span>

* <span data-ttu-id="24c06-195">[`EventArgs`-Klassen in der ASP.NET Core Verweisquelle (dotnet/aspnetcore `master` branch)](https://github.com/dotnet/aspnetcore/tree/master/src/Components/Web/src/Web).</span><span class="sxs-lookup"><span data-stu-id="24c06-195">[`EventArgs` classes in the ASP.NET Core reference source (dotnet/aspnetcore `master` branch)](https://github.com/dotnet/aspnetcore/tree/master/src/Components/Web/src/Web).</span></span> <span data-ttu-id="24c06-196">Der `master`-Branch stellt die API dar, die sich für das *nächste* ASP.NET Core-Release in Entwicklung befindet.</span><span class="sxs-lookup"><span data-stu-id="24c06-196">The `master` branch represents API under development for the *next* ASP.NET Core release.</span></span> <span data-ttu-id="24c06-197">Wählen Sie für das aktuelle Release den entsprechenden GitHub-Repositorybranch aus (z. B. `release/3.1`).</span><span class="sxs-lookup"><span data-stu-id="24c06-197">For the current release, select the appropriate GitHub repository branch (for example, `release/3.1`).</span></span>
* <span data-ttu-id="24c06-198">[MDN-Webdokumentationen: GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers): Enthält Informationen dazu, welche HTML-Elemente die einzelnen DOM-Ereignisse unterstützen.</span><span class="sxs-lookup"><span data-stu-id="24c06-198">[MDN web docs: GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers): Includes information on which HTML elements support each DOM event.</span></span>

## <a name="lambda-expressions"></a><span data-ttu-id="24c06-199">Lambdaausdrücke</span><span class="sxs-lookup"><span data-stu-id="24c06-199">Lambda expressions</span></span>

<span data-ttu-id="24c06-200">[Lambdaausdrücke](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) können ebenfalls verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="24c06-200">[Lambda expressions](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) can also be used:</span></span>

```razor
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

<span data-ttu-id="24c06-201">Es ist häufig praktisch, über zusätzliche Werte zusammenzuschlagen, z. B. bei der Iteration über eine Reihe von Elementen.</span><span class="sxs-lookup"><span data-stu-id="24c06-201">It's often convenient to close over additional values, such as when iterating over a set of elements.</span></span> <span data-ttu-id="24c06-202">Das folgende Beispiel erstellt drei Schaltflächen, die jeweils `UpdateHeading` aufrufen, wobei ein Ereignisargument (<xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs>) und seine Schaltflächennummer (`buttonNumber`) übergeben werden, wenn sie auf der Benutzeroberfläche ausgewählt werden:</span><span class="sxs-lookup"><span data-stu-id="24c06-202">The following example creates three buttons, each of which calls `UpdateHeading` passing an event argument (<xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs>) and its button number (`buttonNumber`) when selected in the UI:</span></span>

```razor
<h2>@message</h2>

@for (var i = 1; i < 4; i++)
{
    var buttonNumber = i;

    <button class="btn btn-primary"
            @onclick="@(e => UpdateHeading(e, buttonNumber))">
        Button #@i
    </button>
}

@code {
    private string message = "Select a button to learn its position.";

    private void UpdateHeading(MouseEventArgs e, int buttonNumber)
    {
        message = $"You selected Button #{buttonNumber} at " +
            $"mouse position: {e.ClientX} X {e.ClientY}.";
    }
}
```

> [!NOTE]
> <span data-ttu-id="24c06-203">Verwenden Sie **keine** Schleifenvariable direkt in einem Lambdaausdruck wie `i` im vorangehenden `for`-Schleifenbeispiel.</span><span class="sxs-lookup"><span data-stu-id="24c06-203">Do **not** use a loop variable directly in a lambda expression, such as `i` in the preceding `for` loop example.</span></span> <span data-ttu-id="24c06-204">Ansonsten wird dieselbe Variable von allen Lambdaausdrücken verwendet, sodass der gleiche Wert in allen Lambdaausdrücken verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="24c06-204">Otherwise, the same variable is used by all lambda expressions, which results in use of the same value in all lambdas.</span></span> <span data-ttu-id="24c06-205">Erfassen Sie den Wert der Variable immer in einer lokalen Variable, und verwenden Sie diese anschließend.</span><span class="sxs-lookup"><span data-stu-id="24c06-205">Always capture the variable's value in a local variable and then use it.</span></span> <span data-ttu-id="24c06-206">Im vorangegangenen Beispiel wird die Schleifenvariable `i` `buttonNumber` zugewiesen.</span><span class="sxs-lookup"><span data-stu-id="24c06-206">In the preceding example, the loop variable `i` is assigned to `buttonNumber`.</span></span>

## <a name="eventcallback"></a><span data-ttu-id="24c06-207">EventCallback</span><span class="sxs-lookup"><span data-stu-id="24c06-207">EventCallback</span></span>

<span data-ttu-id="24c06-208">Ein häufiges Szenario mit geschachtelten Komponenten ist der Wunsch, die Methode einer übergeordneten Komponente auszuführen, wenn ein Ereignis einer untergeordneten Komponente eintritt.</span><span class="sxs-lookup"><span data-stu-id="24c06-208">A common scenario with nested components is the desire to run a parent component's method when a child component event occurs.</span></span> <span data-ttu-id="24c06-209">Das ein `onclick`-Ereignis in der untergeordneten Komponente auftritt, ist ein gängiger Anwendungsfall.</span><span class="sxs-lookup"><span data-stu-id="24c06-209">An `onclick` event occurring in the child component is a common use case.</span></span> <span data-ttu-id="24c06-210">Um Ereignisse komponentenübergreifend darzustellen, verwenden Sie ein <xref:Microsoft.AspNetCore.Components.EventCallback>.</span><span class="sxs-lookup"><span data-stu-id="24c06-210">To expose events across components, use an <xref:Microsoft.AspNetCore.Components.EventCallback>.</span></span> <span data-ttu-id="24c06-211">Eine übergeordnete Komponente kann dem <xref:Microsoft.AspNetCore.Components.EventCallback> einer untergeordneten Komponente eine Rückrufmethode zuweisen.</span><span class="sxs-lookup"><span data-stu-id="24c06-211">A parent component can assign a callback method to a child component's <xref:Microsoft.AspNetCore.Components.EventCallback>.</span></span>

<span data-ttu-id="24c06-212">Der `ChildComponent` in der Beispiel-App (`Components/ChildComponent.razor`) zeigt, wie der `onclick`-Handler einer Schaltfläche so eingerichtet ist, dass er einen <xref:Microsoft.AspNetCore.Components.EventCallback>-Delegaten von der `ParentComponent` des Beispiels empfängt.</span><span class="sxs-lookup"><span data-stu-id="24c06-212">The `ChildComponent` in the sample app (`Components/ChildComponent.razor`) demonstrates how a button's `onclick` handler is set up to receive an <xref:Microsoft.AspNetCore.Components.EventCallback> delegate from the sample's `ParentComponent`.</span></span> <span data-ttu-id="24c06-213">Der <xref:Microsoft.AspNetCore.Components.EventCallback> wird mit `MouseEventArgs` typisiert, was für ein `onclick`-Ereignis von einem Peripheriegerät geeignet ist:</span><span class="sxs-lookup"><span data-stu-id="24c06-213">The <xref:Microsoft.AspNetCore.Components.EventCallback> is typed with `MouseEventArgs`, which is appropriate for an `onclick` event from a peripheral device:</span></span>

[!code-razor[](../common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=5-7,17-18)]

<span data-ttu-id="24c06-214">Die `ParentComponent` legt die <xref:Microsoft.AspNetCore.Components.EventCallback%601> (`OnClickCallback`) des untergeordneten Elements auf seine `ShowMessage`-Methode fest.</span><span class="sxs-lookup"><span data-stu-id="24c06-214">The `ParentComponent` sets the child's <xref:Microsoft.AspNetCore.Components.EventCallback%601> (`OnClickCallback`) to its `ShowMessage` method.</span></span>

<span data-ttu-id="24c06-215">`Pages/ParentComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="24c06-215">`Pages/ParentComponent.razor`:</span></span>

```razor
@page "/ParentComponent"

<h1>Parent-child example</h1>

<ChildComponent Title="Panel Title from Parent"
                OnClickCallback="@ShowMessage">
    Content of the child component is supplied
    by the parent component.
</ChildComponent>

<p><b>@messageText</b></p>

@code {
    private string messageText;

    private void ShowMessage(MouseEventArgs e)
    {
        messageText = $"Blaze a new trail with Blazor! ({e.ScreenX}, {e.ScreenY})";
    }
}
```

<span data-ttu-id="24c06-216">Wenn die Schaltfläche in der `ChildComponent` ausgewählt ist:</span><span class="sxs-lookup"><span data-stu-id="24c06-216">When the button is selected in the `ChildComponent`:</span></span>

* <span data-ttu-id="24c06-217">Die `ShowMessage`-Methode von `ParentComponent` wird aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="24c06-217">The `ParentComponent`'s `ShowMessage` method is called.</span></span> <span data-ttu-id="24c06-218">`messageText` wird aktualisiert und in der `ParentComponent` angezeigt.</span><span class="sxs-lookup"><span data-stu-id="24c06-218">`messageText` is updated and displayed in the `ParentComponent`.</span></span>
* <span data-ttu-id="24c06-219">Ein Aufruf von [`StateHasChanged`](xref:blazor/components/lifecycle#state-changes) ist in der Methode des Rückrufs (`ShowMessage`) nicht erforderlich.</span><span class="sxs-lookup"><span data-stu-id="24c06-219">A call to [`StateHasChanged`](xref:blazor/components/lifecycle#state-changes) isn't required in the callback's method (`ShowMessage`).</span></span> <span data-ttu-id="24c06-220"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> wird automatisch aufgerufen, um die `ParentComponent` zu rendern, so wie Ereignisse untergeordneter Elemente das Rendern von Komponenten in Ereignishandlern auslösen, die innerhalb des untergeordneten Elements ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="24c06-220"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called automatically to rerender the `ParentComponent`, just as child events trigger component rerendering in event handlers that execute within the child.</span></span>

<span data-ttu-id="24c06-221"><xref:Microsoft.AspNetCore.Components.EventCallback> und <xref:Microsoft.AspNetCore.Components.EventCallback%601> gestatten asynchrone Delegate.</span><span class="sxs-lookup"><span data-stu-id="24c06-221"><xref:Microsoft.AspNetCore.Components.EventCallback> and <xref:Microsoft.AspNetCore.Components.EventCallback%601> permit asynchronous delegates.</span></span> <span data-ttu-id="24c06-222"><xref:Microsoft.AspNetCore.Components.EventCallback> ist schwach typisiert und erlaubt das Übergeben von Argumenten eines beliebigen Typs in `InvokeAsync(Object)`.</span><span class="sxs-lookup"><span data-stu-id="24c06-222"><xref:Microsoft.AspNetCore.Components.EventCallback> is weakly typed and allows passing any type argument in `InvokeAsync(Object)`.</span></span> <span data-ttu-id="24c06-223"><xref:Microsoft.AspNetCore.Components.EventCallback%601> ist stark typisiert und erfordert das Übergeben eines `T`-Arguments in `InvokeAsync(T)`, das `TValue` zugewiesen werden kann.</span><span class="sxs-lookup"><span data-stu-id="24c06-223"><xref:Microsoft.AspNetCore.Components.EventCallback%601> is strongly typed and requires passing a `T` argument in `InvokeAsync(T)` that's assignable to `TValue`.</span></span>

```razor
<ChildComponent 
    OnClickCallback="@(async () => { await Task.Yield(); messageText = "Blaze It!"; })" />
```

<span data-ttu-id="24c06-224">Rufen Sie ein <xref:Microsoft.AspNetCore.Components.EventCallback> oder <xref:Microsoft.AspNetCore.Components.EventCallback%601> mit <xref:Microsoft.AspNetCore.Components.EventCallback.InvokeAsync%2A> auf, und warten Sie auf das <xref:System.Threading.Tasks.Task>:</span><span class="sxs-lookup"><span data-stu-id="24c06-224">Invoke an <xref:Microsoft.AspNetCore.Components.EventCallback> or <xref:Microsoft.AspNetCore.Components.EventCallback%601> with <xref:Microsoft.AspNetCore.Components.EventCallback.InvokeAsync%2A> and await the <xref:System.Threading.Tasks.Task>:</span></span>

```csharp
await OnClickCallback.InvokeAsync(arg);
```

<span data-ttu-id="24c06-225">Verwenden Sie <xref:Microsoft.AspNetCore.Components.EventCallback> und <xref:Microsoft.AspNetCore.Components.EventCallback%601> für die Ereignisbehandlung und die Bindung von Komponentenparametern.</span><span class="sxs-lookup"><span data-stu-id="24c06-225">Use <xref:Microsoft.AspNetCore.Components.EventCallback> and <xref:Microsoft.AspNetCore.Components.EventCallback%601> for event handling and binding component parameters.</span></span>

<span data-ttu-id="24c06-226">Bevorzugen Sie das stark typisierte <xref:Microsoft.AspNetCore.Components.EventCallback%601> gegenüber dem <xref:Microsoft.AspNetCore.Components.EventCallback>.</span><span class="sxs-lookup"><span data-stu-id="24c06-226">Prefer the strongly typed <xref:Microsoft.AspNetCore.Components.EventCallback%601> over <xref:Microsoft.AspNetCore.Components.EventCallback>.</span></span> <span data-ttu-id="24c06-227"><xref:Microsoft.AspNetCore.Components.EventCallback%601> bietet den Benutzern der Komponente ein besseres Fehlerfeedback.</span><span class="sxs-lookup"><span data-stu-id="24c06-227"><xref:Microsoft.AspNetCore.Components.EventCallback%601> provides better error feedback to users of the component.</span></span> <span data-ttu-id="24c06-228">Ähnlich wie bei anderen UI-Ereignishandlern ist die Angabe des Ereignisparameters optional.</span><span class="sxs-lookup"><span data-stu-id="24c06-228">Similar to other UI event handlers, specifying the event parameter is optional.</span></span> <span data-ttu-id="24c06-229">Verwenden Sie <xref:Microsoft.AspNetCore.Components.EventCallback>, wenn kein Wert an den Rückruf übergeben wurde.</span><span class="sxs-lookup"><span data-stu-id="24c06-229">Use <xref:Microsoft.AspNetCore.Components.EventCallback> when there's no value passed to the callback.</span></span>

## <a name="prevent-default-actions"></a><span data-ttu-id="24c06-230">Verhindern von Standardaktionen</span><span class="sxs-lookup"><span data-stu-id="24c06-230">Prevent default actions</span></span>

<span data-ttu-id="24c06-231">Verwenden Sie das Direktivenattribut [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault), um die Standardaktion für ein Ereignis zu verhindern.</span><span class="sxs-lookup"><span data-stu-id="24c06-231">Use the [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) directive attribute to prevent the default action for an event.</span></span>

<span data-ttu-id="24c06-232">Wenn ein Schlüssel auf einem Eingabegerät ausgewählt wird und der Elementfokus auf einem Textfeld liegt, zeigt ein Browser normalerweise das Zeichen des Schlüssels in dem Textfeld an.</span><span class="sxs-lookup"><span data-stu-id="24c06-232">When a key is selected on an input device and the element focus is on a text box, a browser normally displays the key's character in the text box.</span></span> <span data-ttu-id="24c06-233">Im folgenden Beispiel wird das Standardverhalten durch die Angabe des Direktivenattributs `@onkeypress:preventDefault` verhindert.</span><span class="sxs-lookup"><span data-stu-id="24c06-233">In the following example, the default behavior is prevented by specifying the `@onkeypress:preventDefault` directive attribute.</span></span> <span data-ttu-id="24c06-234">Der Zähler wird inkrementiert und der Schlüssel **+** wird nicht im Wert des `<input>`-Elements erfasst:</span><span class="sxs-lookup"><span data-stu-id="24c06-234">The counter increments, and the **+** key isn't captured into the `<input>` element's value:</span></span>

```razor
<input value="@count" @onkeypress="KeyHandler" @onkeypress:preventDefault />

@code {
    private int count = 0;

    private void KeyHandler(KeyboardEventArgs e)
    {
        if (e.Key == "+")
        {
            count++;
        }
    }
}
```

<span data-ttu-id="24c06-235">Die Angabe des Attributs `@on{EVENT}:preventDefault` ohne Wert ist gleichbedeutend mit `@on{EVENT}:preventDefault="true"`.</span><span class="sxs-lookup"><span data-stu-id="24c06-235">Specifying the `@on{EVENT}:preventDefault` attribute without a value is equivalent to `@on{EVENT}:preventDefault="true"`.</span></span>

<span data-ttu-id="24c06-236">Der Wert des Attributs kann auch ein Ausdruck sein.</span><span class="sxs-lookup"><span data-stu-id="24c06-236">The value of the attribute can also be an expression.</span></span> <span data-ttu-id="24c06-237">Im folgenden Beispiel ist `shouldPreventDefault` ein `bool`-Feld, das entweder auf `true` oder `false` festgelegt ist:</span><span class="sxs-lookup"><span data-stu-id="24c06-237">In the following example, `shouldPreventDefault` is a `bool` field set to either `true` or `false`:</span></span>

```razor
<input @onkeypress:preventDefault="shouldPreventDefault" />
```

## <a name="stop-event-propagation"></a><span data-ttu-id="24c06-238">Beenden der Ereignisweitergabe</span><span class="sxs-lookup"><span data-stu-id="24c06-238">Stop event propagation</span></span>

<span data-ttu-id="24c06-239">Verwenden Sie das Direktivenattribut [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation), um die Ereignisweitergabe zu beenden.</span><span class="sxs-lookup"><span data-stu-id="24c06-239">Use the [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) directive attribute to stop event propagation.</span></span>

<span data-ttu-id="24c06-240">Im folgenden Beispiel verhindert das Aktivieren des Kontrollkästchens die Weitergabe von Klickereignissen des zweiten untergeordneten `<div>`-Elements an das übergeordnete `<div>`-Element:</span><span class="sxs-lookup"><span data-stu-id="24c06-240">In the following example, selecting the check box prevents click events from the second child `<div>` from propagating to the parent `<div>`:</span></span>

```razor
<label>
    <input @bind="stopPropagation" type="checkbox" />
    Stop Propagation
</label>

<div @onclick="OnSelectParentDiv">
    <h3>Parent div</h3>

    <div @onclick="OnSelectChildDiv">
        Child div that doesn't stop propagation when selected.
    </div>

    <div @onclick="OnSelectChildDiv" @onclick:stopPropagation="stopPropagation">
        Child div that stops propagation when selected.
    </div>
</div>

@code {
    private bool stopPropagation = false;

    private void OnSelectParentDiv() => 
        Console.WriteLine($"The parent div was selected. {DateTime.Now}");
    private void OnSelectChildDiv() => 
        Console.WriteLine($"A child div was selected. {DateTime.Now}");
}
```
