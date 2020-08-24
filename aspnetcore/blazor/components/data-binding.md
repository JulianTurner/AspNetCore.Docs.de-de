---
title: ASP.NET Core Blazor-Datenbindung
author: guardrex
description: In diesem Artikel lernen Sie Datenbindungsfeatures für Komponenten und DOM-Elemente in Blazor-Apps kennen.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/19/2020
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
uid: blazor/components/data-binding
ms.openlocfilehash: 3b41aedcbd0d2c22b20d8fa3a21b8af97d1fbb2c
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88628559"
---
# <a name="aspnet-core-no-locblazor-data-binding"></a><span data-ttu-id="b3a40-103">ASP.NET Core Blazor-Datenbindung</span><span class="sxs-lookup"><span data-stu-id="b3a40-103">ASP.NET Core Blazor data binding</span></span>

<span data-ttu-id="b3a40-104">Von [Luke Latham](https://github.com/guardrex) und [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="b3a40-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="b3a40-105">Razor-Komponenten stellen Features zur Datenbindung über ein HTML-Elementattribut namens [`@bind`](xref:mvc/views/razor#bind) bereit, mit einem Feld, einer Eigenschaft oder einem Razor-Ausdruckswert.</span><span class="sxs-lookup"><span data-stu-id="b3a40-105">Razor components provide data binding features via an HTML element attribute named [`@bind`](xref:mvc/views/razor#bind) with a field, property, or Razor expression value.</span></span>

<span data-ttu-id="b3a40-106">Im folgenden Beispiel wird ein `<input>`-Element an das `currentValue`-Feld und ein `<input>`-Element an die `CurrentValue`-Eigenschaft gebunden:</span><span class="sxs-lookup"><span data-stu-id="b3a40-106">The following example binds an `<input>` element to the `currentValue` field and an `<input>` element to the `CurrentValue` property:</span></span>

```razor
<p>
    <input @bind="currentValue" /> Current value: @currentValue
</p>

<p>
    <input @bind="CurrentValue" /> Current value: @CurrentValue
</p>

@code {
    private string currentValue;

    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="b3a40-107">Wenn eines der Elemente den Fokus verliert, wird das gebundene Feld oder die Eigenschaft aktualisiert.</span><span class="sxs-lookup"><span data-stu-id="b3a40-107">When one of the elements looses focus, its bound field or property is updated.</span></span>

<span data-ttu-id="b3a40-108">Das Textfeld wird in der Benutzeroberfläche nur dann aktualisiert, wenn die Komponente gerendert wird, nicht als Reaktion auf die Änderung des Werts des Felds oder der Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="b3a40-108">The text box is updated in the UI only when the component is rendered, not in response to changing the field's or property's value.</span></span> <span data-ttu-id="b3a40-109">Da sich Komponenten nach der Ausführung von Ereignishandlercode selbst rendern, werden Feld- und Eigenschaftsaktualisierungen *in der Regel* unmittelbar nach dem Auslösen eines Ereignishandlers in der Benutzeroberfläche widergespiegelt.</span><span class="sxs-lookup"><span data-stu-id="b3a40-109">Since components render themselves after event handler code executes, field and property updates are *usually* reflected in the UI immediately after an event handler is triggered.</span></span>

<span data-ttu-id="b3a40-110">Die Verwendung von [`@bind`](xref:mvc/views/razor#bind) mit der `CurrentValue`-Eigenschaft (`<input @bind="CurrentValue" />`) entspricht im Wesentlichen dem Folgenden:</span><span class="sxs-lookup"><span data-stu-id="b3a40-110">Using [`@bind`](xref:mvc/views/razor#bind) with the `CurrentValue` property (`<input @bind="CurrentValue" />`) is essentially equivalent to the following:</span></span>

```razor
<input value="@CurrentValue"
    @onchange="@((ChangeEventArgs __e) => CurrentValue = 
        __e.Value.ToString())" />

@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="b3a40-111">Wenn die Komponente gerendert wird, stammt der `value` des Eingabeelements aus der `CurrentValue`-Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="b3a40-111">When the component is rendered, the `value` of the input element comes from the `CurrentValue` property.</span></span> <span data-ttu-id="b3a40-112">Wenn der Benutzer in das Textfeld eingibt und den Elementfokus ändert, wird das Ereignis `onchange` ausgelöst und die Eigenschaft `CurrentValue` auf den geänderten Wert festgelegt.</span><span class="sxs-lookup"><span data-stu-id="b3a40-112">When the user types in the text box and changes element focus, the `onchange` event is fired and the `CurrentValue` property is set to the changed value.</span></span> <span data-ttu-id="b3a40-113">In Wirklichkeit ist die Codegenerierung komplexer, weil [`@bind`](xref:mvc/views/razor#bind) Fälle behandelt, in denen Typkonvertierungen durchgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="b3a40-113">In reality, the code generation is more complex than that because [`@bind`](xref:mvc/views/razor#bind) handles cases where type conversions are performed.</span></span> <span data-ttu-id="b3a40-114">Im Prinzip assoziiert [`@bind`](xref:mvc/views/razor#bind) den aktuellen Wert eines Ausdrucks mit einem `value`-Attribut und behandelt Änderungen mit dem registrierten Handler.</span><span class="sxs-lookup"><span data-stu-id="b3a40-114">In principle, [`@bind`](xref:mvc/views/razor#bind) associates the current value of an expression with a `value` attribute and handles changes using the registered handler.</span></span>

<span data-ttu-id="b3a40-115">Binden Sie eine Eigenschaft oder ein Feld an andere Ereignisse, indem Sie auch ein `@bind:event`-Attribut mit einem `event`-Parameter einbeziehen.</span><span class="sxs-lookup"><span data-stu-id="b3a40-115">Bind a property or field on other events by also including an `@bind:event` attribute with an `event` parameter.</span></span> <span data-ttu-id="b3a40-116">Das folgende Beispiel bindet die Eigenschaft `CurrentValue` an das Ereignis `oninput`:</span><span class="sxs-lookup"><span data-stu-id="b3a40-116">The following example binds the `CurrentValue` property on the `oninput` event:</span></span>

```razor
<input @bind="CurrentValue" @bind:event="oninput" />

@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="b3a40-117">Im Gegensatz zu `onchange`, das ausgelöst wird, wenn das Element den Fokus verliert, wird `oninput` ausgelöst, wenn sich der Wert des Textfelds ändert.</span><span class="sxs-lookup"><span data-stu-id="b3a40-117">Unlike `onchange`, which fires when the element loses focus, `oninput` fires when the value of the text box changes.</span></span>

<span data-ttu-id="b3a40-118">Verwenden Sie `@bind-{ATTRIBUTE}` mit der `@bind-{ATTRIBUTE}:event`-Syntax, um andere Elementattribute als `value` zu binden.</span><span class="sxs-lookup"><span data-stu-id="b3a40-118">Use `@bind-{ATTRIBUTE}` with `@bind-{ATTRIBUTE}:event` syntax to bind element attributes other than `value`.</span></span> <span data-ttu-id="b3a40-119">Im folgenden Beispiel:</span><span class="sxs-lookup"><span data-stu-id="b3a40-119">In the following example:</span></span>

* <span data-ttu-id="b3a40-120">Der Stil des Absatzes ist **rot**, wenn die Komponente geladen wird (`style="color:red"`).</span><span class="sxs-lookup"><span data-stu-id="b3a40-120">The paragraph's style is **red** when the component loads (`style="color:red"`).</span></span>
* <span data-ttu-id="b3a40-121">Der Benutzer ändert den Wert des Textfelds, um einen anderen CSS-Farbstil widerzuspiegeln, und ändert den Elementfokus der Seite.</span><span class="sxs-lookup"><span data-stu-id="b3a40-121">The user changes the value of the text box to reflect a different CSS color style and changes the page's element focus.</span></span> <span data-ttu-id="b3a40-122">Der Benutzer ändert z. B. den Wert des Textfelds in `color:blue` und drückt die <kbd>TAB-TASTE</kbd> auf der Tastatur.</span><span class="sxs-lookup"><span data-stu-id="b3a40-122">For example, the user changes the text box value to `color:blue` and presses the <kbd>Tab</kbd> key on the keyboard.</span></span>
* <span data-ttu-id="b3a40-123">Wenn sich der Elementfokus ändert:</span><span class="sxs-lookup"><span data-stu-id="b3a40-123">When the element focus changes:</span></span>
  * <span data-ttu-id="b3a40-124">Der Wert von `paragraphStyle` wird aus dem Wert des `<input>`-Elements zugewiesen.</span><span class="sxs-lookup"><span data-stu-id="b3a40-124">The value of `paragraphStyle` is assigned from the `<input>` element's value.</span></span>
  * <span data-ttu-id="b3a40-125">Der Absatzstil wird aktualisiert, um den neuen Stil in `paragraphStyle` widerzuspiegeln.</span><span class="sxs-lookup"><span data-stu-id="b3a40-125">The paragraph style is updated to reflect the new style in `paragraphStyle`.</span></span> <span data-ttu-id="b3a40-126">Wenn der Stil in `color:blue` aktualisiert wird, ändert sich die Textfarbe in **blau**.</span><span class="sxs-lookup"><span data-stu-id="b3a40-126">If the style is updated to `color:blue`, the text color changes to **blue**.</span></span>

```razor
<p>
    <input type="text" @bind="paragraphStyle" />
</p>

<p @bind-style="paragraphStyle" @bind-style:event="onchange">
    Blazorify the app!
</p>

@code {
    private string paragraphStyle = "color:red";
}
```

<span data-ttu-id="b3a40-127">Bei der Attributbindung wird die Groß- und Kleinschreibung berücksichtigt.</span><span class="sxs-lookup"><span data-stu-id="b3a40-127">Attribute binding is case sensitive:</span></span>

* <span data-ttu-id="b3a40-128">`@bind` ist gültig.</span><span class="sxs-lookup"><span data-stu-id="b3a40-128">`@bind` is valid.</span></span>
* <span data-ttu-id="b3a40-129">`@Bind` und `@BIND` sind ungültig.</span><span class="sxs-lookup"><span data-stu-id="b3a40-129">`@Bind` and `@BIND` are invalid.</span></span>

## <a name="unparsable-values"></a><span data-ttu-id="b3a40-130">Nicht analysierbare Werte</span><span class="sxs-lookup"><span data-stu-id="b3a40-130">Unparsable values</span></span>

<span data-ttu-id="b3a40-131">Wenn ein Benutzer einem Element mit Datenbindung einen nicht analysierbaren Wert zur Verfügung stellt, wird der nicht analysierbare Wert automatisch auf seinen vorherigen Wert zurückgesetzt, wenn das Bindungsereignis ausgelöst wird.</span><span class="sxs-lookup"><span data-stu-id="b3a40-131">When a user provides an unparsable value to a databound element, the unparsable value is automatically reverted to its previous value when the bind event is triggered.</span></span>

<span data-ttu-id="b3a40-132">Betrachten Sie das folgende Szenario:</span><span class="sxs-lookup"><span data-stu-id="b3a40-132">Consider the following scenario:</span></span>

* <span data-ttu-id="b3a40-133">Ein `<input>`-Element ist an einen `int`-Typ mit einem Anfangswert von `123` gebunden:</span><span class="sxs-lookup"><span data-stu-id="b3a40-133">An `<input>` element is bound to an `int` type with an initial value of `123`:</span></span>

  ```razor
  <input @bind="inputValue" />

  @code {
      private int inputValue = 123;
  }
  ```

* <span data-ttu-id="b3a40-134">Der Benutzer aktualisiert den Wert des Elements auf der Seite zu `123.45` und ändert den Elementfokus.</span><span class="sxs-lookup"><span data-stu-id="b3a40-134">The user updates the value of the element to `123.45` in the page and changes the element focus.</span></span>

<span data-ttu-id="b3a40-135">Im vorangehenden Szenario wird der Wert des Elements auf `123` zurückgesetzt.</span><span class="sxs-lookup"><span data-stu-id="b3a40-135">In the preceding scenario, the element's value is reverted to `123`.</span></span> <span data-ttu-id="b3a40-136">Wenn der Wert `123.45` zugunsten des ursprünglichen Werts von `123` abgelehnt wird, versteht der Benutzer, dass sein Wert nicht akzeptiert wurde.</span><span class="sxs-lookup"><span data-stu-id="b3a40-136">When the value `123.45` is rejected in favor of the original value of `123`, the user understands that their value wasn't accepted.</span></span>

<span data-ttu-id="b3a40-137">Standardmäßig gilt die Bindung für das `onchange`-Ereignis des Elements (`@bind="{PROPERTY OR FIELD}"`).</span><span class="sxs-lookup"><span data-stu-id="b3a40-137">By default, binding applies to the element's `onchange` event (`@bind="{PROPERTY OR FIELD}"`).</span></span> <span data-ttu-id="b3a40-138">Verwenden Sie `@bind="{PROPERTY OR FIELD}" @bind:event={EVENT}`, um die Bindung bei einem anderen Ereignis auszulösen.</span><span class="sxs-lookup"><span data-stu-id="b3a40-138">Use `@bind="{PROPERTY OR FIELD}" @bind:event={EVENT}` to trigger binding on a different event.</span></span> <span data-ttu-id="b3a40-139">Für das `oninput`-Ereignis (`@bind:event="oninput"`) erfolgt die Umkehrung nach jedem Tastendruck, der einen nicht analysierbaren Wert einführt.</span><span class="sxs-lookup"><span data-stu-id="b3a40-139">For the `oninput` event (`@bind:event="oninput"`), the reversion occurs after any keystroke that introduces an unparsable value.</span></span> <span data-ttu-id="b3a40-140">Wenn das `oninput`-Ereignis mit einem `int`-gebundenen Typ angestrebt wird, wird ein Benutzer daran gehindert, ein `.`-Zeichen einzugeben.</span><span class="sxs-lookup"><span data-stu-id="b3a40-140">When targeting the `oninput` event with an `int`-bound type, a user is prevented from typing a `.` character.</span></span> <span data-ttu-id="b3a40-141">Ein `.`-Zeichen wird sofort entfernt, sodass der Benutzer sofort die Rückmeldung erhält, dass nur ganze Zahlen zulässig sind.</span><span class="sxs-lookup"><span data-stu-id="b3a40-141">A `.` character is immediately removed, so the user receives immediate feedback that only whole numbers are permitted.</span></span> <span data-ttu-id="b3a40-142">Es gibt Szenarien, in denen die Umkehrung des Werts auf das `oninput` Ereignis nicht ideal ist, z. B. wenn dem Benutzer erlaubt werden soll, einen nicht analysierbaren `<input>`-Wert zu löschen.</span><span class="sxs-lookup"><span data-stu-id="b3a40-142">There are scenarios where reverting the value on the `oninput` event isn't ideal, such as when the user should be allowed to clear an unparsable `<input>` value.</span></span> <span data-ttu-id="b3a40-143">Zu den Alternativen gehören:</span><span class="sxs-lookup"><span data-stu-id="b3a40-143">Alternatives include:</span></span>

* <span data-ttu-id="b3a40-144">Verwenden Sie nicht das Ereignis `oninput`.</span><span class="sxs-lookup"><span data-stu-id="b3a40-144">Don't use the `oninput` event.</span></span> <span data-ttu-id="b3a40-145">Verwenden Sie das Standardereignis `onchange` (geben Sie nur `@bind="{PROPERTY OR FIELD}"` an), bei dem ein ungültiger Wert erst dann rückgängig gemacht wird, wenn das Element den Fokus verliert.</span><span class="sxs-lookup"><span data-stu-id="b3a40-145">Use the default `onchange` event (only specify `@bind="{PROPERTY OR FIELD}"`), where an invalid value isn't reverted until the element loses focus.</span></span>
* <span data-ttu-id="b3a40-146">Binden Sie an einen Nullable-Typ, z. B. `int?` oder `string`, und stellen Sie benutzerdefinierte Logik zur Behandlung ungültiger Einträge bereit.</span><span class="sxs-lookup"><span data-stu-id="b3a40-146">Bind to a nullable type, such as `int?` or `string` and provide custom logic to handle invalid entries.</span></span>
* <span data-ttu-id="b3a40-147">Verwenden Sie eine [Formularüberprüfungskomponente](xref:blazor/forms-validation) wie <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> oder <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601>.</span><span class="sxs-lookup"><span data-stu-id="b3a40-147">Use a [form validation component](xref:blazor/forms-validation), such as <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> or <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601>.</span></span> <span data-ttu-id="b3a40-148">Formularüberprüfungskomponenten verfügen über die integrierte Unterstützung der Verwaltung ungültiger Eingaben.</span><span class="sxs-lookup"><span data-stu-id="b3a40-148">Form validation components have built-in support to manage invalid inputs.</span></span> <span data-ttu-id="b3a40-149">Weitere Informationen finden Sie unter <xref:blazor/forms-validation>.</span><span class="sxs-lookup"><span data-stu-id="b3a40-149">For more information, see <xref:blazor/forms-validation>.</span></span> <span data-ttu-id="b3a40-150">Formularüberprüfungskomponenten:</span><span class="sxs-lookup"><span data-stu-id="b3a40-150">Form validation components:</span></span>
  * <span data-ttu-id="b3a40-151">Erlauben Sie dem Benutzer, ungültige Eingaben zu machen und Überprüfungsfehler für das zugehörige <xref:Microsoft.AspNetCore.Components.Forms.EditContext> zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="b3a40-151">Permit the user to provide invalid input and receive validation errors on the associated <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span>
  * <span data-ttu-id="b3a40-152">Zeigen Sie Überprüfungsfehler auf der Benutzeroberfläche an, ohne den Benutzer bei der Eingabe zusätzlicher WebForm-Daten zu beeinträchtigen.</span><span class="sxs-lookup"><span data-stu-id="b3a40-152">Display validation errors in the UI without interfering with the user entering additional webform data.</span></span>

## <a name="format-strings"></a><span data-ttu-id="b3a40-153">Formatzeichenfolgen</span><span class="sxs-lookup"><span data-stu-id="b3a40-153">Format strings</span></span>

<span data-ttu-id="b3a40-154">Die Datenbindung funktioniert mit <xref:System.DateTime>-Formatzeichenfolgen, die `@bind:format` verwenden.</span><span class="sxs-lookup"><span data-stu-id="b3a40-154">Data binding works with <xref:System.DateTime> format strings using `@bind:format`.</span></span> <span data-ttu-id="b3a40-155">Andere Formatausdrücke wie Währungs- oder Zahlenformate sind zu diesem Zeitpunkt nicht verfügbar.</span><span class="sxs-lookup"><span data-stu-id="b3a40-155">Other format expressions, such as currency or number formats, aren't available at this time.</span></span>

```razor
<input @bind="startDate" @bind:format="yyyy-MM-dd" />

@code {
    private DateTime startDate = new DateTime(2020, 1, 1);
}
```

<span data-ttu-id="b3a40-156">Im vorhergehenden Code ist der Feldtyp des `<input>`-Elements (`type`) standardmäßig auf `text` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="b3a40-156">In the preceding code, the `<input>` element's field type (`type`) defaults to `text`.</span></span> <span data-ttu-id="b3a40-157">`@bind:format` wird für die Bindung der folgenden .NET-Typen unterstützt:</span><span class="sxs-lookup"><span data-stu-id="b3a40-157">`@bind:format` is supported for binding the following .NET types:</span></span>

* <xref:System.DateTime?displayProperty=fullName>
* <span data-ttu-id="b3a40-158"><xref:System.DateTime?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="b3a40-158"><xref:System.DateTime?displayProperty=fullName>?</span></span>
* <xref:System.DateTimeOffset?displayProperty=fullName>
* <span data-ttu-id="b3a40-159"><xref:System.DateTimeOffset?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="b3a40-159"><xref:System.DateTimeOffset?displayProperty=fullName>?</span></span>

<span data-ttu-id="b3a40-160">Das `@bind:format`-Attribut gibt das Datumsformat an, das auf den `value` des `<input>`-Elements angewendet werden soll.</span><span class="sxs-lookup"><span data-stu-id="b3a40-160">The `@bind:format` attribute specifies the date format to apply to the `value` of the `<input>` element.</span></span> <span data-ttu-id="b3a40-161">Das Format wird auch verwendet, um den Wert beim Eintreten eines `onchange`-Ereignisses zu analysieren.</span><span class="sxs-lookup"><span data-stu-id="b3a40-161">The format is also used to parse the value when an `onchange` event occurs.</span></span>

<span data-ttu-id="b3a40-162">Die Angabe eines Formats für den Feldtyp `date` wird nicht empfohlen, da Blazor eine integrierte Unterstützung für die Formatierung von Daten bietet.</span><span class="sxs-lookup"><span data-stu-id="b3a40-162">Specifying a format for the `date` field type isn't recommended because Blazor has built-in support to format dates.</span></span> <span data-ttu-id="b3a40-163">Verwenden Sie trotz der Empfehlung nur dann das Datumsformat `yyyy-MM-dd` für die Bindung, um ordnungsgemäß zu funktionieren, wenn ein Format mit dem Feldtyp `date` bereitgestellt wird:</span><span class="sxs-lookup"><span data-stu-id="b3a40-163">In spite of the recommendation, only use the `yyyy-MM-dd` date format for binding to function correctly if a format is supplied with the `date` field type:</span></span>

```razor
<input type="date" @bind="startDate" @bind:format="yyyy-MM-dd">
```

## <a name="parent-to-child-binding-with-component-parameters"></a><span data-ttu-id="b3a40-164">Bindung zwischen übergeordnetem und untergeordnetem Element mit Komponentenparametern</span><span class="sxs-lookup"><span data-stu-id="b3a40-164">Parent-to-child binding with component parameters</span></span>

<span data-ttu-id="b3a40-165">Komponentenparameter erlauben das Binden von Eigenschaften und Feldern einer übergeordneten Komponente mit `@bind-{PROPERTY OR FIELD}`-Syntax.</span><span class="sxs-lookup"><span data-stu-id="b3a40-165">Component parameters permit binding properties and fields of a parent component with `@bind-{PROPERTY OR FIELD}` syntax.</span></span>

<span data-ttu-id="b3a40-166">Die folgende `Child`-Komponente (`Child.razor`) verfügt über einen `Year`-Komponentenparameter und `YearChanged`-Rückruf:</span><span class="sxs-lookup"><span data-stu-id="b3a40-166">The following `Child` component (`Child.razor`) has a `Year` component parameter and `YearChanged` callback:</span></span>

```razor
<div class="card bg-light mt-3" style="width:18rem ">
    <div class="card-body">
        <h3 class="card-title">Child Component</h3>
        <p class="card-text">Child <code>Year</code>: @Year</p>
        <p>
            <button @onclick="UpdateYear">
                Update Child <code>Year</code> and call 
                <code>YearChanged.InvokeAsync(Year)</code>
            </button>
        </p>
    </div>
</div>

@code {
    private Random r = new Random();

    [Parameter]
    public int Year { get; set; }

    [Parameter]
    public EventCallback<int> YearChanged { get; set; }

    private Task UpdateYear()
    {
        Year = r.Next(10050, 12021);

        return YearChanged.InvokeAsync(Year);
    }
}
```

<span data-ttu-id="b3a40-167">Der Rückruf (<xref:Microsoft.AspNetCore.Components.EventCallback%601>) muss den Namen des Komponentenparameters aufweisen, gefolgt vom Suffix „`Changed`“ (`{PARAMETER NAME}Changed`).</span><span class="sxs-lookup"><span data-stu-id="b3a40-167">The callback (<xref:Microsoft.AspNetCore.Components.EventCallback%601>) must be named as the component parameter name followed by the "`Changed`" suffix (`{PARAMETER NAME}Changed`).</span></span> <span data-ttu-id="b3a40-168">Im vorherigen Beispiel trägt der Rückruf den Namen `YearChanged`.</span><span class="sxs-lookup"><span data-stu-id="b3a40-168">In the preceding example, the callback is named `YearChanged`.</span></span> <span data-ttu-id="b3a40-169">Weitere Informationen zu <xref:Microsoft.AspNetCore.Components.EventCallback%601> finden Sie unter <xref:blazor/components/event-handling#eventcallback>.</span><span class="sxs-lookup"><span data-stu-id="b3a40-169">For more information on <xref:Microsoft.AspNetCore.Components.EventCallback%601>, see <xref:blazor/components/event-handling#eventcallback>.</span></span>

<span data-ttu-id="b3a40-170">In der folgenden `Parent`-Komponente (`Parent.razor`) ist das Feld `year` an den `Year`-Parameter der untergeordneten Komponente gebunden:</span><span class="sxs-lookup"><span data-stu-id="b3a40-170">In the following `Parent` component (`Parent.razor`), the `year` field is bound to the `Year` parameter of the child component:</span></span>

```razor
@page "/Parent"

<h1>Parent Component</h1>

<p>Parent <code>year</code>: @year</p>

<button @onclick="UpdateYear">Update Parent <code>year</code></button>

<Child @bind-Year="year" />

@code {
    private Random r = new Random();
    private int year = 1978;

    private void UpdateYear()
    {
        year = r.Next(1950, 2021);
    }
}
```

<span data-ttu-id="b3a40-171">Der Parameter `Year` ist bindbar, da er ein Begleitereignis `YearChanged` aufweist, das dem Typ des Parameters `Year` entspricht.</span><span class="sxs-lookup"><span data-stu-id="b3a40-171">The `Year` parameter is bindable because it has a companion `YearChanged` event that matches the type of the `Year` parameter.</span></span>

<span data-ttu-id="b3a40-172">Gemäß der Konvention kann eine Eigenschaft an einen entsprechenden Ereignishandler gebunden werden, indem ein `@bind-{PROPERTY}:event`-Attribut einbezogen wird, das dem Handler zugewiesen ist.</span><span class="sxs-lookup"><span data-stu-id="b3a40-172">By convention, a property can be bound to a corresponding event handler by including an `@bind-{PROPERTY}:event` attribute assigned to the handler.</span></span> <span data-ttu-id="b3a40-173">`<Child @bind-Year="year" />` ist identisch mit dem folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="b3a40-173">`<Child @bind-Year="year" />` is equivalent to writing:</span></span>

```razor
<Child @bind-Year="year" @bind-Year:event="YearChanged" />
```

## <a name="child-to-parent-binding-with-chained-bind"></a><span data-ttu-id="b3a40-174">Bindung zwischen untergeordnetem und übergeordnetem Element mit verketteter Bindung</span><span class="sxs-lookup"><span data-stu-id="b3a40-174">Child-to-parent binding with chained bind</span></span>

<span data-ttu-id="b3a40-175">Ein häufiges Szenario ist die Verkettung eines datengebundenen Parameters mit einem Seitenelement in der Ausgabe der Komponente.</span><span class="sxs-lookup"><span data-stu-id="b3a40-175">A common scenario is chaining a data-bound parameter to a page element in the component's output.</span></span> <span data-ttu-id="b3a40-176">Dieses Szenario wird als *verkettete Bindung* bezeichnet, da mehrere Ebenen der Bindung gleichzeitig auftreten.</span><span class="sxs-lookup"><span data-stu-id="b3a40-176">This scenario is called a *chained bind* because multiple levels of binding occur simultaneously.</span></span>

<span data-ttu-id="b3a40-177">Eine verkettete Bindung kann nicht mit der [`@bind`](xref:mvc/views/razor#bind)-Syntax in der untergeordneten Komponente implementiert werden.</span><span class="sxs-lookup"><span data-stu-id="b3a40-177">A chained bind can't be implemented with [`@bind`](xref:mvc/views/razor#bind) syntax in the child component.</span></span> <span data-ttu-id="b3a40-178">Der Ereignishandler und der Wert müssen separat angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="b3a40-178">The event handler and value must be specified separately.</span></span> <span data-ttu-id="b3a40-179">Eine übergeordnete Komponente kann jedoch die [`@bind`](xref:mvc/views/razor#bind)-Syntax mit dem Parameter der untergeordneten Komponente verwenden.</span><span class="sxs-lookup"><span data-stu-id="b3a40-179">A parent component, however, can use [`@bind`](xref:mvc/views/razor#bind) syntax with the child component's parameter.</span></span>

<span data-ttu-id="b3a40-180">Die folgende `PasswordField`-Komponente (`PasswordField.razor`):</span><span class="sxs-lookup"><span data-stu-id="b3a40-180">The following `PasswordField` component (`PasswordField.razor`):</span></span>

* <span data-ttu-id="b3a40-181">Legt den Wert eines `<input>`-Elements auf eine `Password`-Eigenschaft fest.</span><span class="sxs-lookup"><span data-stu-id="b3a40-181">Sets an `<input>` element's value to a `Password` property.</span></span>
* <span data-ttu-id="b3a40-182">Macht Änderungen der `Password`-Eigenschaft einer übergeordneten Komponente mit einem [`EventCallback`](xref:blazor/components/event-handling#eventcallback) verfügbar.</span><span class="sxs-lookup"><span data-stu-id="b3a40-182">Exposes changes of the `Password` property to a parent component with an [`EventCallback`](xref:blazor/components/event-handling#eventcallback).</span></span>
* <span data-ttu-id="b3a40-183">Verwendet das `onclick`-Ereignis zum Auslösen der `ToggleShowPassword`-Methode.</span><span class="sxs-lookup"><span data-stu-id="b3a40-183">Uses the `onclick` event to trigger the `ToggleShowPassword` method.</span></span> <span data-ttu-id="b3a40-184">Weitere Informationen finden Sie unter <xref:blazor/components/event-handling>.</span><span class="sxs-lookup"><span data-stu-id="b3a40-184">For more information, see <xref:blazor/components/event-handling>.</span></span>

```razor
<h1>Child Component</h1>

Password:

<input @oninput="OnPasswordChanged" 
       required 
       type="@(showPassword ? "text" : "password")" 
       value="@Password" />

<button class="btn btn-primary" @onclick="ToggleShowPassword">
    Show password
</button>

@code {
    private bool showPassword;

    [Parameter]
    public string Password { get; set; }

    [Parameter]
    public EventCallback<string> PasswordChanged { get; set; }

    private Task OnPasswordChanged(ChangeEventArgs e)
    {
        Password = e.Value.ToString();

        return PasswordChanged.InvokeAsync(Password);
    }

    private void ToggleShowPassword()
    {
        showPassword = !showPassword;
    }
}
```

<span data-ttu-id="b3a40-185">Die `PasswordField`-Komponente wird in einer anderen Komponente verwendet:</span><span class="sxs-lookup"><span data-stu-id="b3a40-185">The `PasswordField` component is used in another component:</span></span>

```razor
@page "/Parent"

<h1>Parent Component</h1>

<PasswordField @bind-Password="password" />

@code {
    private string password;
}
```

<span data-ttu-id="b3a40-186">So führen Sie Prüfungen durch oder fangen Fehler beim Kennwort im vorhergehenden Beispiel ab</span><span class="sxs-lookup"><span data-stu-id="b3a40-186">To perform checks or trap errors on the password in the preceding example:</span></span>

* <span data-ttu-id="b3a40-187">Erstellen Sie ein Unterstützungsfeld für `Password` (`password` im folgenden Beispielcode).</span><span class="sxs-lookup"><span data-stu-id="b3a40-187">Create a backing field for `Password` (`password` in the following example code).</span></span>
* <span data-ttu-id="b3a40-188">Führen Sie die Prüfungen durch, oder fangen Sie Fehler im `Password`-Setter ab.</span><span class="sxs-lookup"><span data-stu-id="b3a40-188">Perform the checks or trap errors in the `Password` setter.</span></span>

<span data-ttu-id="b3a40-189">Das folgende Beispiel gibt dem Benutzer ein sofortiges Feedback, wenn ein Leerzeichen im Wert des Kennworts verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="b3a40-189">The following example provides immediate feedback to the user if a space is used in the password's value:</span></span>

```razor
<h1>Child Component</h1>

Password: 

<input @oninput="OnPasswordChanged" 
       required 
       type="@(showPassword ? "text" : "password")" 
       value="@Password" />

<button class="btn btn-primary" @onclick="ToggleShowPassword">
    Show password
</button>

<span class="text-danger">@validationMessage</span>

@code {
    private bool showPassword;
    private string password;
    private string validationMessage;

    [Parameter]
    public string Password
    {
        get { return password ?? string.Empty; }
        set
        {
            if (password != value)
            {
                if (value.Contains(' '))
                {
                    validationMessage = "Spaces not allowed!";
                }
                else
                {
                    password = value;
                    validationMessage = string.Empty;
                }
            }
        }
    }

    [Parameter]
    public EventCallback<string> PasswordChanged { get; set; }

    private Task OnPasswordChanged(ChangeEventArgs e)
    {
        Password = e.Value.ToString();

        return PasswordChanged.InvokeAsync(Password);
    }

    private void ToggleShowPassword()
    {
        showPassword = !showPassword;
    }
}
```

## <a name="additional-resources"></a><span data-ttu-id="b3a40-190">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="b3a40-190">Additional resources</span></span>

* [<span data-ttu-id="b3a40-191">Binden an Optionsfelder in einem Formular</span><span class="sxs-lookup"><span data-stu-id="b3a40-191">Binding to radio buttons in a form</span></span>](xref:blazor/forms-validation#radio-buttons)
* [<span data-ttu-id="b3a40-192">Binden von `<select>`-Elementoptionen an `null`-Werte von C#-Objekten in einem Formular</span><span class="sxs-lookup"><span data-stu-id="b3a40-192">Binding `<select>` element options to C# object `null` values in a form</span></span>](xref:blazor/forms-validation#binding-select-element-options-to-c-object-null-values)
