---
title: ASP.NET Core Blazor-Ereignisbehandlung
author: guardrex
description: Informieren Sie sich über die Features zur Ereignisbehandlung von Blazor, einschließlich Ereignisargumenttypen, Ereignisrückrufe und die Verwaltung von standardmäßigen Browserereignissen.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/20/2020
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
uid: blazor/components/event-handling
ms.openlocfilehash: 56306d305a61d549380e847ab2754fa41130e14e
ms.sourcegitcommit: 1be547564381873fe9e84812df8d2088514c622a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2020
ms.locfileid: "94507810"
---
# <a name="aspnet-core-no-locblazor-event-handling"></a>ASP.NET Core Blazor-Ereignisbehandlung

Von [Luke Latham](https://github.com/guardrex) und [Daniel Roth](https://github.com/danroth27)

Razor-Komponenten bieten Features zur Ereignisbehandlung. Bei einem HTML-Elementattribut namens [`@on{EVENT}`](xref:mvc/views/razor#onevent) (z. B. `@onclick`) mit einem Wert vom Typ „Delegat“ behandelt eine Razor-Komponente den Wert des Attributs als einen Ereignishandler.

Der folgende Code ruft die `UpdateHeading`-Methode auf, wenn die Schaltfläche auf der Benutzeroberfläche ausgewählt wird:

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

Der folgende Code ruft die `CheckChanged`-Methode auf, wenn das Kontrollkästchen auf der Benutzeroberfläche geändert wird:

```razor
<input type="checkbox" class="form-check-input" @onchange="CheckChanged" />

@code {
    private void CheckChanged()
    {
        ...
    }
}
```

Ereignishandler können auch asynchron sein und ein <xref:System.Threading.Tasks.Task> zurückgeben. Es ist nicht erforderlich, [StateHasChanged](xref:blazor/components/lifecycle#state-changes) manuell aufzurufen. Ausnahmen werden protokolliert, wenn sie auftreten.

Im folgenden Beispiel wird `UpdateHeading` asynchron aufgerufen, wenn die Schaltfläche ausgewählt wird:

```razor
<button class="btn btn-primary" @onclick="UpdateHeading">
    Update heading
</button>

@code {
    private async Task UpdateHeading(MouseEventArgs e)
    {
        await ...
    }
}
```

## <a name="event-argument-types"></a>Ereignisargumenttypen

Für einige Ereignisse sind Ereignisargumenttypen zulässig. Die Angabe eines Ereignisparameters in einer Ereignismethodendefinition ist optional und nur erforderlich, wenn der Ereignistyp in der Methode verwendet wird. Im folgenden Beispiel wird das Ereignisargument `MouseEventArgs` in der Methode `ShowMessage` verwendet, um den Nachrichtentext festzulegen:

```csharp
private void ShowMessage(MouseEventArgs e)
{
    messageText = $"The mouse is at coordinates: {e.ScreenX}:{e.ScreenY}";
}
```

Die unterstützten <xref:System.EventArgs> sind in der folgenden Tabelle aufgeführt.

::: moniker range=">= aspnetcore-5.0"

| event            | Klasse  | DOM-Ereignisse und -Hinweise |
| ---------------- | ------ | -------------------- |
| Zwischenablage        | <xref:Microsoft.AspNetCore.Components.Web.ClipboardEventArgs> | `oncut`, `oncopy`, `onpaste` |
| Ziehen             | <xref:Microsoft.AspNetCore.Components.Web.DragEventArgs> | `ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`<br><br><xref:Microsoft.AspNetCore.Components.Web.DataTransfer> und <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> speichern gezogene Elementdaten.<br><br>Implementiert Drag & Drop in Blazor-Apps mit [JS-Interop](xref:blazor/call-javascript-from-dotnet) und der [HTML-Drag & Drop-API](https://developer.mozilla.org/docs/Web/API/HTML_Drag_and_Drop_API). |
| Fehler            | <xref:Microsoft.AspNetCore.Components.Web.ErrorEventArgs> | `onerror` |
| event            | <xref:System.EventArgs> | *Allgemein*<br>`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`<br><br>*Zwischenablage*<br>`onbeforecut`, `onbeforecopy`, `onbeforepaste`<br><br>*Eingabe*<br>`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`<br><br>*Medien*<br>`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onended`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `ontoggle`, `onvolumechange`, `onwaiting`<br><br><xref:Microsoft.AspNetCore.Components.Web.EventHandlers> enthält Attribute zum Konfigurieren der Zuordnungen zwischen Ereignisnamen und Ereignisargumenttypen. |
| Fokus            | <xref:Microsoft.AspNetCore.Components.Web.FocusEventArgs> | `onfocus`, `onblur`, `onfocusin`, `onfocusout`<br><br>Umfasst nicht die Unterstützung für `relatedTarget`. |
| Eingabe            | <xref:Microsoft.AspNetCore.Components.ChangeEventArgs> | `onchange`, `oninput` |
| Tastatur         | <xref:Microsoft.AspNetCore.Components.Web.KeyboardEventArgs> | `onkeydown`, `onkeypress`, `onkeyup` |
| Maus            | <xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs> | `onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout` |
| Mauszeiger    | <xref:Microsoft.AspNetCore.Components.Web.PointerEventArgs> | `onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture` |
| Mausrad      | <xref:Microsoft.AspNetCore.Components.Web.WheelEventArgs> | `onwheel`, `onmousewheel` |
| Status         | <xref:Microsoft.AspNetCore.Components.Web.ProgressEventArgs> | `onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout` |
| Toucheingabe            | <xref:Microsoft.AspNetCore.Components.Web.TouchEventArgs> | `ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`<br><br><xref:Microsoft.AspNetCore.Components.Web.TouchPoint> stellt einen einzelnen Kontaktpunkt auf einem Gerät mit Berührungseingabe dar. |

::: moniker-end

::: moniker range="< aspnetcore-5.0"

| event            | Klasse | DOM-Ereignisse und -Hinweise |
| ---------------- | ----- | -------------------- |
| Zwischenablage        | <xref:Microsoft.AspNetCore.Components.Web.ClipboardEventArgs> | `oncut`, `oncopy`, `onpaste` |
| Ziehen             | <xref:Microsoft.AspNetCore.Components.Web.DragEventArgs> | `ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`<br><br><xref:Microsoft.AspNetCore.Components.Web.DataTransfer> und <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> speichern gezogene Elementdaten.<br><br>Implementiert Drag & Drop in Blazor-Apps mit [JS-Interop](xref:blazor/call-javascript-from-dotnet) und der [HTML-Drag & Drop-API](https://developer.mozilla.org/docs/Web/API/HTML_Drag_and_Drop_API). |
| Fehler            | <xref:Microsoft.AspNetCore.Components.Web.ErrorEventArgs> | `onerror` |
| event            | <xref:System.EventArgs> | *Allgemein*<br>`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`<br><br>*Zwischenablage*<br>`onbeforecut`, `onbeforecopy`, `onbeforepaste`<br><br>*Eingabe*<br>`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`<br><br>*Medien*<br>`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onended`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`<br><br><xref:Microsoft.AspNetCore.Components.Web.EventHandlers> enthält Attribute zum Konfigurieren der Zuordnungen zwischen Ereignisnamen und Ereignisargumenttypen. |
| Fokus            | <xref:Microsoft.AspNetCore.Components.Web.FocusEventArgs> | `onfocus`, `onblur`, `onfocusin`, `onfocusout`<br><br>Umfasst nicht die Unterstützung für `relatedTarget`. |
| Eingabe            | <xref:Microsoft.AspNetCore.Components.ChangeEventArgs> | `onchange`, `oninput` |
| Tastatur         | <xref:Microsoft.AspNetCore.Components.Web.KeyboardEventArgs> | `onkeydown`, `onkeypress`, `onkeyup` |
| Maus            | <xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs> | `onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout` |
| Mauszeiger    | <xref:Microsoft.AspNetCore.Components.Web.PointerEventArgs> | `onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture` |
| Mausrad      | <xref:Microsoft.AspNetCore.Components.Web.WheelEventArgs> | `onwheel`, `onmousewheel` |
| Status         | <xref:Microsoft.AspNetCore.Components.Web.ProgressEventArgs> | `onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout` |
| Toucheingabe            | <xref:Microsoft.AspNetCore.Components.Web.TouchEventArgs> | `ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`<br><br><xref:Microsoft.AspNetCore.Components.Web.TouchPoint> stellt einen einzelnen Kontaktpunkt auf einem Gerät mit Berührungseingabe dar. |

::: moniker-end

Weitere Informationen finden Sie in den folgenden Ressourcen:

* [`EventArgs`-Klassen in der ASP.NET Core Verweisquelle (dotnet/aspnetcore `master` branch)](https://github.com/dotnet/aspnetcore/tree/master/src/Components/Web/src/Web). Der `master`-Branch stellt die API dar, die sich für das *nächste* ASP.NET Core-Release in Entwicklung befindet. Wählen Sie für das aktuelle Release den entsprechenden GitHub-Repositorybranch aus (z. B. `release/3.1`).
* [MDN-Webdokumentationen: GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers): Enthält Informationen dazu, welche HTML-Elemente die einzelnen DOM-Ereignisse unterstützen.

## <a name="lambda-expressions"></a>Lambdaausdrücke

[Lambdaausdrücke](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) können ebenfalls verwendet werden:

```razor
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

Es ist häufig praktisch, über zusätzliche Werte zusammenzuschlagen, z. B. bei der Iteration über eine Reihe von Elementen. Das folgende Beispiel erstellt drei Schaltflächen, die jeweils `UpdateHeading` aufrufen, wobei ein Ereignisargument (<xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs>) und seine Schaltflächennummer (`buttonNumber`) übergeben werden, wenn sie auf der Benutzeroberfläche ausgewählt werden:

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
> Verwenden Sie **keine** Schleifenvariable direkt in einem Lambdaausdruck wie `i` im vorangehenden `for`-Schleifenbeispiel. Ansonsten wird dieselbe Variable von allen Lambdaausdrücken verwendet, sodass der gleiche Wert in allen Lambdaausdrücken verwendet wird. Erfassen Sie den Wert der Variable immer in einer lokalen Variable, und verwenden Sie diese anschließend. Im vorangegangenen Beispiel wird die Schleifenvariable `i` `buttonNumber` zugewiesen.

## <a name="eventcallback"></a>EventCallback

Ein häufiges Szenario mit geschachtelten Komponenten ist der Wunsch, die Methode einer übergeordneten Komponente auszuführen, wenn ein Ereignis einer untergeordneten Komponente eintritt. Das ein `onclick`-Ereignis in der untergeordneten Komponente auftritt, ist ein gängiger Anwendungsfall. Um Ereignisse komponentenübergreifend darzustellen, verwenden Sie ein <xref:Microsoft.AspNetCore.Components.EventCallback>. Eine übergeordnete Komponente kann dem <xref:Microsoft.AspNetCore.Components.EventCallback> einer untergeordneten Komponente eine Rückrufmethode zuweisen.

Der `ChildComponent` in der Beispiel-App (`Components/ChildComponent.razor`) zeigt, wie der `onclick`-Handler einer Schaltfläche so eingerichtet ist, dass er einen <xref:Microsoft.AspNetCore.Components.EventCallback>-Delegaten von der `ParentComponent` des Beispiels empfängt. Der <xref:Microsoft.AspNetCore.Components.EventCallback> wird mit `MouseEventArgs` typisiert, was für ein `onclick`-Ereignis von einem Peripheriegerät geeignet ist:

[!code-razor[](../common/samples/5.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=5-7,17-18)]

Die `ParentComponent` legt die <xref:Microsoft.AspNetCore.Components.EventCallback%601> (`OnClickCallback`) des untergeordneten Elements auf seine `ShowMessage`-Methode fest.

`Pages/ParentComponent.razor`:

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

Wenn die Schaltfläche in der `ChildComponent` ausgewählt ist:

* Die `ShowMessage`-Methode von `ParentComponent` wird aufgerufen. `messageText` wird aktualisiert und in der `ParentComponent` angezeigt.
* Ein Aufruf von [`StateHasChanged`](xref:blazor/components/lifecycle#state-changes) ist in der Methode des Rückrufs (`ShowMessage`) nicht erforderlich. <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> wird automatisch aufgerufen, um die `ParentComponent` zu rendern, so wie Ereignisse untergeordneter Elemente das Rendern von Komponenten in Ereignishandlern auslösen, die innerhalb des untergeordneten Elements ausgeführt werden.

<xref:Microsoft.AspNetCore.Components.EventCallback> und <xref:Microsoft.AspNetCore.Components.EventCallback%601> gestatten asynchrone Delegate. <xref:Microsoft.AspNetCore.Components.EventCallback> ist schwach typisiert und erlaubt das Übergeben von Argumenten eines beliebigen Typs in `InvokeAsync(Object)`. <xref:Microsoft.AspNetCore.Components.EventCallback%601> ist stark typisiert und erfordert das Übergeben eines `T`-Arguments in `InvokeAsync(T)`, das `TValue` zugewiesen werden kann.

```razor
<ChildComponent 
    OnClickCallback="@(async () => { await Task.Yield(); messageText = "Blaze It!"; })" />
```

Rufen Sie ein <xref:Microsoft.AspNetCore.Components.EventCallback> oder <xref:Microsoft.AspNetCore.Components.EventCallback%601> mit <xref:Microsoft.AspNetCore.Components.EventCallback.InvokeAsync%2A> auf, und warten Sie auf das <xref:System.Threading.Tasks.Task>:

```csharp
await OnClickCallback.InvokeAsync(arg);
```

Verwenden Sie <xref:Microsoft.AspNetCore.Components.EventCallback> und <xref:Microsoft.AspNetCore.Components.EventCallback%601> für die Ereignisbehandlung und die Bindung von Komponentenparametern.

Bevorzugen Sie das stark typisierte <xref:Microsoft.AspNetCore.Components.EventCallback%601> gegenüber dem <xref:Microsoft.AspNetCore.Components.EventCallback>. <xref:Microsoft.AspNetCore.Components.EventCallback%601> bietet den Benutzern der Komponente ein besseres Fehlerfeedback. Ähnlich wie bei anderen UI-Ereignishandlern ist die Angabe des Ereignisparameters optional. Verwenden Sie <xref:Microsoft.AspNetCore.Components.EventCallback>, wenn kein Wert an den Rückruf übergeben wurde.

## <a name="prevent-default-actions"></a>Verhindern von Standardaktionen

Verwenden Sie das Direktivenattribut [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault), um die Standardaktion für ein Ereignis zu verhindern.

Wenn ein Schlüssel auf einem Eingabegerät ausgewählt wird und der Elementfokus auf einem Textfeld liegt, zeigt ein Browser normalerweise das Zeichen des Schlüssels in dem Textfeld an. Im folgenden Beispiel wird das Standardverhalten durch die Angabe des Direktivenattributs `@onkeypress:preventDefault` verhindert. Der Zähler wird inkrementiert und der Schlüssel **+** wird nicht im Wert des `<input>`-Elements erfasst:

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

Die Angabe des Attributs `@on{EVENT}:preventDefault` ohne Wert ist gleichbedeutend mit `@on{EVENT}:preventDefault="true"`.

Der Wert des Attributs kann auch ein Ausdruck sein. Im folgenden Beispiel ist `shouldPreventDefault` ein `bool`-Feld, das entweder auf `true` oder `false` festgelegt ist:

```razor
<input @onkeypress:preventDefault="shouldPreventDefault" />
```

## <a name="stop-event-propagation"></a>Beenden der Ereignisweitergabe

Verwenden Sie das Direktivenattribut [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation), um die Ereignisweitergabe zu beenden.

Im folgenden Beispiel verhindert das Aktivieren des Kontrollkästchens die Weitergabe von Klickereignissen des zweiten untergeordneten `<div>`-Elements an das übergeordnete `<div>`-Element:

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

::: moniker range=">= aspnetcore-5.0"

## <a name="focus-an-element"></a>Fokussieren eines Elements

Rufen Sie `FocusAsync` für einen [Elementverweis](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements) auf, um den Fokus auf ein Element im Code zu platzieren:

```razor
<input @ref="exampleInput" />

<button @onclick="ChangeFocus">Focus the Input Element</button>

@code {
    private ElementReference exampleInput;
    
    private async Task ChangeFocus()
    {
        await exampleInput.FocusAsync();
    }
}
```

::: moniker-end
