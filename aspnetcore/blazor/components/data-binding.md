---
title: ASP.NET Core :::no-loc(Blazor):::-Datenbindung
author: guardrex
description: In diesem Artikel lernen Sie Datenbindungsfeatures für Komponenten und DOM-Elemente in :::no-loc(Blazor):::-Apps kennen.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/22/2020
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
uid: blazor/components/data-binding
ms.openlocfilehash: 004a15bf63c34144049a45f9d5fca8852fa36a3f
ms.sourcegitcommit: fbd5427293d9ecccc388bd5fd305c2eb8ada7281
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94463820"
---
# <a name="aspnet-core-no-locblazor-data-binding"></a><span data-ttu-id="98367-103">ASP.NET Core :::no-loc(Blazor):::-Datenbindung</span><span class="sxs-lookup"><span data-stu-id="98367-103">ASP.NET Core :::no-loc(Blazor)::: data binding</span></span>

<span data-ttu-id="98367-104">Von [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27) und [Steve Sanderson](https://github.com/SteveSandersonMS)</span><span class="sxs-lookup"><span data-stu-id="98367-104">By [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), and [Steve Sanderson](https://github.com/SteveSandersonMS)</span></span>

<span data-ttu-id="98367-105">:::no-loc(Razor):::-Komponenten stellen Features zur Datenbindung über ein HTML-Elementattribut namens [`@bind`](xref:mvc/views/razor#bind) bereit, mit einem Feld, einer Eigenschaft oder einem :::no-loc(Razor):::-Ausdruckswert.</span><span class="sxs-lookup"><span data-stu-id="98367-105">:::no-loc(Razor)::: components provide data binding features via an HTML element attribute named [`@bind`](xref:mvc/views/razor#bind) with a field, property, or :::no-loc(Razor)::: expression value.</span></span>

<span data-ttu-id="98367-106">Im folgenden Beispiel wird ein `<input>`-Element an das `currentValue`-Feld und ein `<input>`-Element an die `CurrentValue`-Eigenschaft gebunden:</span><span class="sxs-lookup"><span data-stu-id="98367-106">The following example binds an `<input>` element to the `currentValue` field and an `<input>` element to the `CurrentValue` property:</span></span>

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

<span data-ttu-id="98367-107">Wenn eines der Elemente den Fokus verliert, wird das gebundene Feld oder die Eigenschaft aktualisiert.</span><span class="sxs-lookup"><span data-stu-id="98367-107">When one of the elements loses focus, its bound field or property is updated.</span></span>

<span data-ttu-id="98367-108">Das Textfeld wird in der Benutzeroberfläche nur dann aktualisiert, wenn die Komponente gerendert wird, nicht als Reaktion auf die Änderung des Werts des Felds oder der Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="98367-108">The text box is updated in the UI only when the component is rendered, not in response to changing the field's or property's value.</span></span> <span data-ttu-id="98367-109">Da sich Komponenten nach der Ausführung von Ereignishandlercode selbst rendern, werden Feld- und Eigenschaftsaktualisierungen *in der Regel* unmittelbar nach dem Auslösen eines Ereignishandlers in der Benutzeroberfläche widergespiegelt.</span><span class="sxs-lookup"><span data-stu-id="98367-109">Since components render themselves after event handler code executes, field and property updates are *usually* reflected in the UI immediately after an event handler is triggered.</span></span>

<span data-ttu-id="98367-110">Die Verwendung von [`@bind`](xref:mvc/views/razor#bind) mit der `CurrentValue`-Eigenschaft (`<input @bind="CurrentValue" />`) entspricht im Wesentlichen dem Folgenden:</span><span class="sxs-lookup"><span data-stu-id="98367-110">Using [`@bind`](xref:mvc/views/razor#bind) with the `CurrentValue` property (`<input @bind="CurrentValue" />`) is essentially equivalent to the following:</span></span>

```razor
<input value="@CurrentValue"
    @onchange="@((ChangeEventArgs __e) => CurrentValue = 
        __e.Value.ToString())" />

@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="98367-111">Wenn die Komponente gerendert wird, stammt der `value` des Eingabeelements aus der `CurrentValue`-Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="98367-111">When the component is rendered, the `value` of the input element comes from the `CurrentValue` property.</span></span> <span data-ttu-id="98367-112">Wenn der Benutzer in das Textfeld eingibt und den Elementfokus ändert, wird das Ereignis `onchange` ausgelöst und die Eigenschaft `CurrentValue` auf den geänderten Wert festgelegt.</span><span class="sxs-lookup"><span data-stu-id="98367-112">When the user types in the text box and changes element focus, the `onchange` event is fired and the `CurrentValue` property is set to the changed value.</span></span> <span data-ttu-id="98367-113">In Wirklichkeit ist die Codegenerierung komplexer, weil [`@bind`](xref:mvc/views/razor#bind) Fälle behandelt, in denen Typkonvertierungen durchgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="98367-113">In reality, the code generation is more complex than that because [`@bind`](xref:mvc/views/razor#bind) handles cases where type conversions are performed.</span></span> <span data-ttu-id="98367-114">Im Prinzip assoziiert [`@bind`](xref:mvc/views/razor#bind) den aktuellen Wert eines Ausdrucks mit einem `value`-Attribut und behandelt Änderungen mit dem registrierten Handler.</span><span class="sxs-lookup"><span data-stu-id="98367-114">In principle, [`@bind`](xref:mvc/views/razor#bind) associates the current value of an expression with a `value` attribute and handles changes using the registered handler.</span></span>

<span data-ttu-id="98367-115">Binden Sie eine Eigenschaft oder ein Feld an andere Ereignisse, indem Sie auch ein `@bind:event`-Attribut mit einem `event`-Parameter einbeziehen.</span><span class="sxs-lookup"><span data-stu-id="98367-115">Bind a property or field on other events by also including an `@bind:event` attribute with an `event` parameter.</span></span> <span data-ttu-id="98367-116">Das folgende Beispiel bindet die Eigenschaft `CurrentValue` an das Ereignis `oninput`:</span><span class="sxs-lookup"><span data-stu-id="98367-116">The following example binds the `CurrentValue` property on the `oninput` event:</span></span>

```razor
<input @bind="CurrentValue" @bind:event="oninput" />

@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="98367-117">Im Gegensatz zu `onchange`, das ausgelöst wird, wenn das Element den Fokus verliert, wird `oninput` ausgelöst, wenn sich der Wert des Textfelds ändert.</span><span class="sxs-lookup"><span data-stu-id="98367-117">Unlike `onchange`, which fires when the element loses focus, `oninput` fires when the value of the text box changes.</span></span>

<!-- Hold location for resolution of https://github.com/dotnet/AspNetCore.Docs/issues/19721 -->

<span data-ttu-id="98367-118">Bei der Attributbindung wird die Groß- und Kleinschreibung berücksichtigt.</span><span class="sxs-lookup"><span data-stu-id="98367-118">Attribute binding is case sensitive:</span></span>

* <span data-ttu-id="98367-119">`@bind` ist gültig.</span><span class="sxs-lookup"><span data-stu-id="98367-119">`@bind` is valid.</span></span>
* <span data-ttu-id="98367-120">`@Bind` und `@BIND` sind ungültig.</span><span class="sxs-lookup"><span data-stu-id="98367-120">`@Bind` and `@BIND` are invalid.</span></span>

## <a name="unparsable-values"></a><span data-ttu-id="98367-121">Nicht analysierbare Werte</span><span class="sxs-lookup"><span data-stu-id="98367-121">Unparsable values</span></span>

<span data-ttu-id="98367-122">Wenn ein Benutzer einem Element mit Datenbindung einen nicht analysierbaren Wert zur Verfügung stellt, wird der nicht analysierbare Wert automatisch auf seinen vorherigen Wert zurückgesetzt, wenn das Bindungsereignis ausgelöst wird.</span><span class="sxs-lookup"><span data-stu-id="98367-122">When a user provides an unparsable value to a databound element, the unparsable value is automatically reverted to its previous value when the bind event is triggered.</span></span>

<span data-ttu-id="98367-123">Betrachten Sie das folgende Szenario:</span><span class="sxs-lookup"><span data-stu-id="98367-123">Consider the following scenario:</span></span>

* <span data-ttu-id="98367-124">Ein `<input>`-Element ist an einen `int`-Typ mit einem Anfangswert von `123` gebunden:</span><span class="sxs-lookup"><span data-stu-id="98367-124">An `<input>` element is bound to an `int` type with an initial value of `123`:</span></span>

  ```razor
  <input @bind="inputValue" />

  @code {
      private int inputValue = 123;
  }
  ```

* <span data-ttu-id="98367-125">Der Benutzer aktualisiert den Wert des Elements auf der Seite zu `123.45` und ändert den Elementfokus.</span><span class="sxs-lookup"><span data-stu-id="98367-125">The user updates the value of the element to `123.45` in the page and changes the element focus.</span></span>

<span data-ttu-id="98367-126">Im vorangehenden Szenario wird der Wert des Elements auf `123` zurückgesetzt.</span><span class="sxs-lookup"><span data-stu-id="98367-126">In the preceding scenario, the element's value is reverted to `123`.</span></span> <span data-ttu-id="98367-127">Wenn der Wert `123.45` zugunsten des ursprünglichen Werts von `123` abgelehnt wird, versteht der Benutzer, dass sein Wert nicht akzeptiert wurde.</span><span class="sxs-lookup"><span data-stu-id="98367-127">When the value `123.45` is rejected in favor of the original value of `123`, the user understands that their value wasn't accepted.</span></span>

<span data-ttu-id="98367-128">Standardmäßig gilt die Bindung für das `onchange`-Ereignis des Elements (`@bind="{PROPERTY OR FIELD}"`).</span><span class="sxs-lookup"><span data-stu-id="98367-128">By default, binding applies to the element's `onchange` event (`@bind="{PROPERTY OR FIELD}"`).</span></span> <span data-ttu-id="98367-129">Verwenden Sie `@bind="{PROPERTY OR FIELD}" @bind:event={EVENT}`, um die Bindung bei einem anderen Ereignis auszulösen.</span><span class="sxs-lookup"><span data-stu-id="98367-129">Use `@bind="{PROPERTY OR FIELD}" @bind:event={EVENT}` to trigger binding on a different event.</span></span> <span data-ttu-id="98367-130">Für das `oninput`-Ereignis (`@bind:event="oninput"`) erfolgt die Umkehrung nach jedem Tastendruck, der einen nicht analysierbaren Wert einführt.</span><span class="sxs-lookup"><span data-stu-id="98367-130">For the `oninput` event (`@bind:event="oninput"`), the reversion occurs after any keystroke that introduces an unparsable value.</span></span> <span data-ttu-id="98367-131">Wenn das `oninput`-Ereignis mit einem `int`-gebundenen Typ angestrebt wird, wird ein Benutzer daran gehindert, ein `.`-Zeichen einzugeben.</span><span class="sxs-lookup"><span data-stu-id="98367-131">When targeting the `oninput` event with an `int`-bound type, a user is prevented from typing a `.` character.</span></span> <span data-ttu-id="98367-132">Ein `.`-Zeichen wird sofort entfernt, sodass der Benutzer sofort die Rückmeldung erhält, dass nur ganze Zahlen zulässig sind.</span><span class="sxs-lookup"><span data-stu-id="98367-132">A `.` character is immediately removed, so the user receives immediate feedback that only whole numbers are permitted.</span></span> <span data-ttu-id="98367-133">Es gibt Szenarien, in denen die Umkehrung des Werts auf das `oninput` Ereignis nicht ideal ist, z. B. wenn dem Benutzer erlaubt werden soll, einen nicht analysierbaren `<input>`-Wert zu löschen.</span><span class="sxs-lookup"><span data-stu-id="98367-133">There are scenarios where reverting the value on the `oninput` event isn't ideal, such as when the user should be allowed to clear an unparsable `<input>` value.</span></span> <span data-ttu-id="98367-134">Zu den Alternativen gehören:</span><span class="sxs-lookup"><span data-stu-id="98367-134">Alternatives include:</span></span>

* <span data-ttu-id="98367-135">Verwenden Sie nicht das Ereignis `oninput`.</span><span class="sxs-lookup"><span data-stu-id="98367-135">Don't use the `oninput` event.</span></span> <span data-ttu-id="98367-136">Verwenden Sie das Standardereignis `onchange` (geben Sie nur `@bind="{PROPERTY OR FIELD}"` an), bei dem ein ungültiger Wert erst dann rückgängig gemacht wird, wenn das Element den Fokus verliert.</span><span class="sxs-lookup"><span data-stu-id="98367-136">Use the default `onchange` event (only specify `@bind="{PROPERTY OR FIELD}"`), where an invalid value isn't reverted until the element loses focus.</span></span>
* <span data-ttu-id="98367-137">Binden Sie an einen Nullable-Typ, z. B. `int?` oder `string`, und stellen Sie benutzerdefinierte Logik zur Behandlung ungültiger Einträge bereit.</span><span class="sxs-lookup"><span data-stu-id="98367-137">Bind to a nullable type, such as `int?` or `string` and provide custom logic to handle invalid entries.</span></span>
* <span data-ttu-id="98367-138">Verwenden Sie eine [Formularüberprüfungskomponente](xref:blazor/forms-validation) wie <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> oder <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601>.</span><span class="sxs-lookup"><span data-stu-id="98367-138">Use a [form validation component](xref:blazor/forms-validation), such as <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> or <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601>.</span></span> <span data-ttu-id="98367-139">Formularüberprüfungskomponenten verfügen über die integrierte Unterstützung der Verwaltung ungültiger Eingaben.</span><span class="sxs-lookup"><span data-stu-id="98367-139">Form validation components have built-in support to manage invalid inputs.</span></span> <span data-ttu-id="98367-140">Weitere Informationen finden Sie unter <xref:blazor/forms-validation>.</span><span class="sxs-lookup"><span data-stu-id="98367-140">For more information, see <xref:blazor/forms-validation>.</span></span> <span data-ttu-id="98367-141">Formularüberprüfungskomponenten:</span><span class="sxs-lookup"><span data-stu-id="98367-141">Form validation components:</span></span>
  * <span data-ttu-id="98367-142">Erlauben Sie dem Benutzer, ungültige Eingaben zu machen und Überprüfungsfehler für das zugehörige <xref:Microsoft.AspNetCore.Components.Forms.EditContext> zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="98367-142">Permit the user to provide invalid input and receive validation errors on the associated <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span>
  * <span data-ttu-id="98367-143">Zeigen Sie Überprüfungsfehler auf der Benutzeroberfläche an, ohne den Benutzer bei der Eingabe zusätzlicher WebForm-Daten zu beeinträchtigen.</span><span class="sxs-lookup"><span data-stu-id="98367-143">Display validation errors in the UI without interfering with the user entering additional webform data.</span></span>

## <a name="format-strings"></a><span data-ttu-id="98367-144">Formatzeichenfolgen</span><span class="sxs-lookup"><span data-stu-id="98367-144">Format strings</span></span>

<span data-ttu-id="98367-145">Die Datenbindung funktioniert mit <xref:System.DateTime>-Formatzeichenfolgen, die `@bind:format` verwenden.</span><span class="sxs-lookup"><span data-stu-id="98367-145">Data binding works with <xref:System.DateTime> format strings using `@bind:format`.</span></span> <span data-ttu-id="98367-146">Andere Formatausdrücke wie Währungs- oder Zahlenformate sind zu diesem Zeitpunkt nicht verfügbar.</span><span class="sxs-lookup"><span data-stu-id="98367-146">Other format expressions, such as currency or number formats, aren't available at this time.</span></span>

```razor
<input @bind="startDate" @bind:format="yyyy-MM-dd" />

@code {
    private DateTime startDate = new DateTime(2020, 1, 1);
}
```

<span data-ttu-id="98367-147">Im vorhergehenden Code ist der Feldtyp des `<input>`-Elements (`type`) standardmäßig auf `text` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="98367-147">In the preceding code, the `<input>` element's field type (`type`) defaults to `text`.</span></span> <span data-ttu-id="98367-148">`@bind:format` wird für die Bindung der folgenden .NET-Typen unterstützt:</span><span class="sxs-lookup"><span data-stu-id="98367-148">`@bind:format` is supported for binding the following .NET types:</span></span>

* <xref:System.DateTime?displayProperty=fullName>
* <span data-ttu-id="98367-149"><xref:System.DateTime?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="98367-149"><xref:System.DateTime?displayProperty=fullName>?</span></span>
* <xref:System.DateTimeOffset?displayProperty=fullName>
* <span data-ttu-id="98367-150"><xref:System.DateTimeOffset?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="98367-150"><xref:System.DateTimeOffset?displayProperty=fullName>?</span></span>

<span data-ttu-id="98367-151">Das `@bind:format`-Attribut gibt das Datumsformat an, das auf den `value` des `<input>`-Elements angewendet werden soll.</span><span class="sxs-lookup"><span data-stu-id="98367-151">The `@bind:format` attribute specifies the date format to apply to the `value` of the `<input>` element.</span></span> <span data-ttu-id="98367-152">Das Format wird auch verwendet, um den Wert beim Eintreten eines `onchange`-Ereignisses zu analysieren.</span><span class="sxs-lookup"><span data-stu-id="98367-152">The format is also used to parse the value when an `onchange` event occurs.</span></span>

<span data-ttu-id="98367-153">Die Angabe eines Formats für den Feldtyp `date` wird nicht empfohlen, da :::no-loc(Blazor)::: eine integrierte Unterstützung für die Formatierung von Daten bietet.</span><span class="sxs-lookup"><span data-stu-id="98367-153">Specifying a format for the `date` field type isn't recommended because :::no-loc(Blazor)::: has built-in support to format dates.</span></span> <span data-ttu-id="98367-154">Verwenden Sie trotz der Empfehlung nur dann das Datumsformat `yyyy-MM-dd` für die Bindung, um ordnungsgemäß zu funktionieren, wenn ein Format mit dem Feldtyp `date` bereitgestellt wird:</span><span class="sxs-lookup"><span data-stu-id="98367-154">In spite of the recommendation, only use the `yyyy-MM-dd` date format for binding to function correctly if a format is supplied with the `date` field type:</span></span>

```razor
<input type="date" @bind="startDate" @bind:format="yyyy-MM-dd">
```

## <a name="binding-with-component-parameters"></a><span data-ttu-id="98367-155">Binden mit Komponentenparametern</span><span class="sxs-lookup"><span data-stu-id="98367-155">Binding with component parameters</span></span>

<span data-ttu-id="98367-156">Ein häufiges Szenario besteht darin, eine Eigenschaft in einer untergeordneten Komponente an eine Eigenschaft in ihrem übergeordneten Element zu binden.</span><span class="sxs-lookup"><span data-stu-id="98367-156">A common scenario is binding a property in a child component to a property in its parent.</span></span> <span data-ttu-id="98367-157">Dieses Szenario wird als *verkettete Bindung* bezeichnet, da mehrere Ebenen der Bindung gleichzeitig auftreten.</span><span class="sxs-lookup"><span data-stu-id="98367-157">This scenario is called a *chained bind* because multiple levels of binding occur simultaneously.</span></span>

<span data-ttu-id="98367-158">Komponentenparameter erlauben das Binden von Eigenschaften und Feldern einer übergeordneten Komponente mit `@bind-{PROPERTY OR FIELD}`-Syntax.</span><span class="sxs-lookup"><span data-stu-id="98367-158">Component parameters permit binding properties and fields of a parent component with `@bind-{PROPERTY OR FIELD}` syntax.</span></span>

<span data-ttu-id="98367-159">Verkettete Bindungen können nicht mit der [`@bind`](xref:mvc/views/razor#bind)-Syntax in der untergeordneten Komponente implementiert werden.</span><span class="sxs-lookup"><span data-stu-id="98367-159">Chained binds can't be implemented with [`@bind`](xref:mvc/views/razor#bind) syntax in the child component.</span></span> <span data-ttu-id="98367-160">Separat müssen ein Ereignishandler und ein Wert angegeben werden, um die Aktualisierung der Eigenschaft in der übergeordneten Komponente zu unterstützen.</span><span class="sxs-lookup"><span data-stu-id="98367-160">An event handler and value must be specified separately to support updating the property in the parent from the child component.</span></span>

<span data-ttu-id="98367-161">Die übergeordnete Komponente nutzt weiterhin die [`@bind`](xref:mvc/views/razor#bind)-Syntax, um die Datenbindung mit der untergeordneten Komponente einzurichten.</span><span class="sxs-lookup"><span data-stu-id="98367-161">The parent component still leverages the [`@bind`](xref:mvc/views/razor#bind) syntax to set up the data-binding with the child component.</span></span>

<span data-ttu-id="98367-162">Die folgende `Child`-Komponente (`Shared/Child.razor`) verfügt über einen `Year`-Komponentenparameter und `YearChanged`-Rückruf:</span><span class="sxs-lookup"><span data-stu-id="98367-162">The following `Child` component (`Shared/Child.razor`) has a `Year` component parameter and `YearChanged` callback:</span></span>

```razor
<div class="card bg-light mt-3" style="width:18rem ">
    <div class="card-body">
        <h3 class="card-title">Child Component</h3>
        <p class="card-text">Child <code>Year</code>: @Year</p>
    </div>
</div>

<button @onclick="UpdateYearFromChild">Update Year from Child</button>

@code {
    private Random r = new Random();

    [Parameter]
    public int Year { get; set; }

    [Parameter]
    public EventCallback<int> YearChanged { get; set; }

    private async Task UpdateYearFromChild()
    {
        await YearChanged.InvokeAsync(r.Next(1950, 2021));
    }
}
```

<span data-ttu-id="98367-163">Der Rückruf (<xref:Microsoft.AspNetCore.Components.EventCallback%601>) muss den Namen des Komponentenparameters aufweisen, gefolgt vom Suffix „`Changed`“ (`{PARAMETER NAME}Changed`).</span><span class="sxs-lookup"><span data-stu-id="98367-163">The callback (<xref:Microsoft.AspNetCore.Components.EventCallback%601>) must be named as the component parameter name followed by the "`Changed`" suffix (`{PARAMETER NAME}Changed`).</span></span> <span data-ttu-id="98367-164">Im vorherigen Beispiel trägt der Rückruf den Namen `YearChanged`.</span><span class="sxs-lookup"><span data-stu-id="98367-164">In the preceding example, the callback is named `YearChanged`.</span></span> <span data-ttu-id="98367-165"><xref:Microsoft.AspNetCore.Components.EventCallback.InvokeAsync%2A?displayProperty=nameWithType> ruft den Delegaten, der der Bindung zugeordnet ist, mit dem bereitgestellten Argument auf und sendet eine Ereignisbenachrichtigung über die geänderte Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="98367-165"><xref:Microsoft.AspNetCore.Components.EventCallback.InvokeAsync%2A?displayProperty=nameWithType> invokes the delegate associated with the binding with the provided argument and dispatches an event notification for the changed property.</span></span>

<span data-ttu-id="98367-166">In der folgenden `Parent`-Komponente (`Parent.razor`) ist das Feld `year` an den `Year`-Parameter der untergeordneten Komponente gebunden:</span><span class="sxs-lookup"><span data-stu-id="98367-166">In the following `Parent` component (`Parent.razor`), the `year` field is bound to the `Year` parameter of the child component:</span></span>

```razor
@page "/Parent"

<h1>Parent Component</h1>

<p>Parent <code>year</code>: @year</p>

<button @onclick="UpdateYear">Update Parent <code>year</code></button>

<Child @bind-Year="year" />

@code {
    private Random r = new Random();
    private int year = 1979;

    private void UpdateYear()
    {
        year = r.Next(1950, 2021);
    }
}
```

<span data-ttu-id="98367-167">Der Parameter `Year` ist bindbar, da er ein Begleitereignis `YearChanged` aufweist, das dem Typ des Parameters `Year` entspricht.</span><span class="sxs-lookup"><span data-stu-id="98367-167">The `Year` parameter is bindable because it has a companion `YearChanged` event that matches the type of the `Year` parameter.</span></span>

<span data-ttu-id="98367-168">Gemäß der Konvention kann eine Eigenschaft an einen entsprechenden Ereignishandler gebunden werden, indem ein `@bind-{PROPERTY}:event`-Attribut einbezogen wird, das dem Handler zugewiesen ist.</span><span class="sxs-lookup"><span data-stu-id="98367-168">By convention, a property can be bound to a corresponding event handler by including an `@bind-{PROPERTY}:event` attribute assigned to the handler.</span></span> <span data-ttu-id="98367-169">`<Child @bind-Year="year" />` ist identisch mit dem folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="98367-169">`<Child @bind-Year="year" />` is equivalent to writing:</span></span>

```razor
<Child @bind-Year="year" @bind-Year:event="YearChanged" />
```

<span data-ttu-id="98367-170">In einem komplexeren Beispiel aus der Praxis wird die `PasswordField`-Komponente (`PasswordField.razor`) für Folgendes verwendet:</span><span class="sxs-lookup"><span data-stu-id="98367-170">In a more sophisticated and real-world example, the following `PasswordField` component (`PasswordField.razor`):</span></span>

* <span data-ttu-id="98367-171">Legt den Wert eines `<input>`-Elements auf ein `password`-Feld fest.</span><span class="sxs-lookup"><span data-stu-id="98367-171">Sets an `<input>` element's value to a `password` field.</span></span>
* <span data-ttu-id="98367-172">Macht Änderungen einer `Password`-Eigenschaft einer übergeordneten Komponente mit einem [`EventCallback`](xref:blazor/components/event-handling#eventcallback) verfügbar, der den aktuellen Wert des `password`-Felds des untergeordneten Elements als Argument übergibt.</span><span class="sxs-lookup"><span data-stu-id="98367-172">Exposes changes of a `Password` property to a parent component with an [`EventCallback`](xref:blazor/components/event-handling#eventcallback) that passes in the current value of the child's `password` field as its argument.</span></span>
* <span data-ttu-id="98367-173">Verwendet das `onclick`-Ereignis zum Auslösen der `ToggleShowPassword`-Methode.</span><span class="sxs-lookup"><span data-stu-id="98367-173">Uses the `onclick` event to trigger the `ToggleShowPassword` method.</span></span> <span data-ttu-id="98367-174">Weitere Informationen finden Sie unter <xref:blazor/components/event-handling>.</span><span class="sxs-lookup"><span data-stu-id="98367-174">For more information, see <xref:blazor/components/event-handling>.</span></span>

```razor
<h1>Provide your password</h1>

Password:

<input @oninput="OnPasswordChanged" 
       required 
       type="@(showPassword ? "text" : "password")" 
       value="@password" />

<button class="btn btn-primary" @onclick="ToggleShowPassword">
    Show password
</button>

@code {
    private bool showPassword;
    private string password;

    [Parameter]
    public string Password { get; set; }

    [Parameter]
    public EventCallback<string> PasswordChanged { get; set; }

    private async Task OnPasswordChanged(ChangeEventArgs e)
    {
        password = e.Value.ToString();

        await PasswordChanged.InvokeAsync(password);
    }

    private void ToggleShowPassword()
    {
        showPassword = !showPassword;
    }
}
```

<span data-ttu-id="98367-175">Die `PasswordField`-Komponente wird in einer anderen Komponente verwendet:</span><span class="sxs-lookup"><span data-stu-id="98367-175">The `PasswordField` component is used in another component:</span></span>

```razor
@page "/Parent"

<h1>Parent Component</h1>

<PasswordField @bind-Password="password" />

@code {
    private string password;
}
```

<span data-ttu-id="98367-176">Führen Sie Prüfungen durch, oder fangen Sie Fehler in der Methode ab, die den Delegat der Bindung aufruft.</span><span class="sxs-lookup"><span data-stu-id="98367-176">Perform checks or trap errors in the method that invokes the binding's delegate.</span></span> <span data-ttu-id="98367-177">Das folgende Beispiel gibt dem Benutzer ein sofortiges Feedback, wenn ein Leerzeichen im Wert des Kennworts verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="98367-177">The following example provides immediate feedback to the user if a space is used in the password's value:</span></span>

```razor
<h1>Child Component</h1>

Password: 

<input @oninput="OnPasswordChanged" 
       required 
       type="@(showPassword ? "text" : "password")" 
       value="@password" />

<button class="btn btn-primary" @onclick="ToggleShowPassword">
    Show password
</button>

<span class="text-danger">@validationMessage</span>

@code {
    private bool showPassword;
    private string password;
    private string validationMessage;

    [Parameter]
    public string Password { get; set; }

    [Parameter]
    public EventCallback<string> PasswordChanged { get; set; }

    private Task OnPasswordChanged(ChangeEventArgs e)
    {
        password = e.Value.ToString();

        if (password.Contains(' '))
        {
            validationMessage = "Spaces not allowed!";

            return Task.CompletedTask;
        }
        else
        {
            validationMessage = string.Empty;

            return PasswordChanged.InvokeAsync(password);
        }
    }

    private void ToggleShowPassword()
    {
        showPassword = !showPassword;
    }
}
```

<span data-ttu-id="98367-178">Weitere Informationen zu <xref:Microsoft.AspNetCore.Components.EventCallback%601> finden Sie unter <xref:blazor/components/event-handling#eventcallback>.</span><span class="sxs-lookup"><span data-stu-id="98367-178">For more information on <xref:Microsoft.AspNetCore.Components.EventCallback%601>, see <xref:blazor/components/event-handling#eventcallback>.</span></span>

## <a name="bind-across-more-than-two-components"></a><span data-ttu-id="98367-179">Binden über mehr als zwei Komponenten</span><span class="sxs-lookup"><span data-stu-id="98367-179">Bind across more than two components</span></span>

<span data-ttu-id="98367-180">Sie können eine Bindung über eine beliebige Anzahl geschachtelter Komponenten erstellen, Sie müssen aber den unidirektionalen Datenfluss berücksichtigen:</span><span class="sxs-lookup"><span data-stu-id="98367-180">You can bind through any number of nested components, but you must respect the one-way flow of data:</span></span>

* <span data-ttu-id="98367-181">Änderungsbenachrichtigungen *durchlaufen die Hierarchie von unten nach oben*.</span><span class="sxs-lookup"><span data-stu-id="98367-181">Change notifications *flow up the hierarchy*.</span></span>
* <span data-ttu-id="98367-182">Neue Parameterwerte *durchlaufen die Hierarchie von oben nach unten*.</span><span class="sxs-lookup"><span data-stu-id="98367-182">New parameter values *flow down the hierarchy*.</span></span>

<span data-ttu-id="98367-183">Ein gängiges und empfohlenes Verfahren besteht darin, nur die zugrunde liegenden Daten in der übergeordneten Komponente zu speichern, damit Sie immer genau wissen, welcher Zustand aktualisiert werden muss.</span><span class="sxs-lookup"><span data-stu-id="98367-183">A common and recommended approach is to only store the underlying data in the parent component to avoid any confusion about what state must be updated.</span></span>

<span data-ttu-id="98367-184">Die folgenden Komponenten veranschaulichen die oben beschriebenen Konzepte:</span><span class="sxs-lookup"><span data-stu-id="98367-184">The following components demonstrate the preceding concepts:</span></span>

<span data-ttu-id="98367-185">`ParentComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="98367-185">`ParentComponent.razor`:</span></span>

```razor
<h1>Parent Component</h1>

<p>Parent Message: <b>@parentMessage</b></p>

<p>
    <button @onclick="ChangeValue">Change from Parent</button>
</p>

<ChildComponent @bind-ChildMessage="parentMessage" />

@code {
    private string parentMessage = "Initial value set in Parent";

    private void ChangeValue()
    {
        parentMessage = $"Set in Parent {DateTime.Now}";
    }
}
```

<span data-ttu-id="98367-186">`ChildComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="98367-186">`ChildComponent.razor`:</span></span>

```razor
<div class="border rounded m-1 p-1">
    <h2>Child Component</h2>

    <p>Child Message: <b>@ChildMessage</b></p>

    <p>
        <button @onclick="ChangeValue">Change from Child</button>
    </p>

    <GrandchildComponent @bind-GrandchildMessage="BoundValue" />
</div>

@code {
    [Parameter]
    public string ChildMessage { get; set; }

    [Parameter]
    public EventCallback<string> ChildMessageChanged { get; set; }

    private string BoundValue
    {
        get => ChildMessage;
        set => ChildMessageChanged.InvokeAsync(value);
    }

    private async Task ChangeValue()
    {
        await ChildMessageChanged.InvokeAsync(
            $"Set in Child {DateTime.Now}");
    }
}
```

<span data-ttu-id="98367-187">`GrandchildComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="98367-187">`GrandchildComponent.razor`:</span></span>

```razor
<div class="border rounded m-1 p-1">
    <h3>Grandchild Component</h3>

    <p>Grandchild Message: <b>@GrandchildMessage</b></p>

    <p>
        <button @onclick="ChangeValue">Change from Grandchild</button>
    </p>
</div>

@code {
    [Parameter]
    public string GrandchildMessage { get; set; }

    [Parameter]
    public EventCallback<string> GrandchildMessageChanged { get; set; }

    private async Task ChangeValue()
    {
        await GrandchildMessageChanged.InvokeAsync(
            $"Set in Grandchild {DateTime.Now}");
    }
}
```

<span data-ttu-id="98367-188">Einen alternativen Ansatz, der sich für die gemeinsame Nutzung von Daten im Arbeitsspeicher über Komponenten hinweg eignet, die nicht unbedingt geschachtelt sind, finden Sie unter <xref:blazor/state-management#in-memory-state-container-service>.</span><span class="sxs-lookup"><span data-stu-id="98367-188">For an alternative approach suited to sharing data in-memory across components that aren't necessarily nested, see <xref:blazor/state-management#in-memory-state-container-service>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="98367-189">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="98367-189">Additional resources</span></span>

* [<span data-ttu-id="98367-190">Binden an Optionsfelder in einem Formular</span><span class="sxs-lookup"><span data-stu-id="98367-190">Binding to radio buttons in a form</span></span>](xref:blazor/forms-validation#radio-buttons)
* [<span data-ttu-id="98367-191">Binden von `<select>`-Elementoptionen an `null`-Werte von C#-Objekten in einem Formular</span><span class="sxs-lookup"><span data-stu-id="98367-191">Binding `<select>` element options to C# object `null` values in a form</span></span>](xref:blazor/forms-validation#binding-select-element-options-to-c-object-null-values)
