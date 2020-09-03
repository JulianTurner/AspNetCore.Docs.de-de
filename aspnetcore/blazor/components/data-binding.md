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
ms.openlocfilehash: d88cad10314872271250cd43212a64698f485381
ms.sourcegitcommit: 8ed9a413bdc2d665ad11add8828898d726ccb106
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/01/2020
ms.locfileid: "89280399"
---
# <a name="aspnet-core-no-locblazor-data-binding"></a>ASP.NET Core Blazor-Datenbindung

Von [Luke Latham](https://github.com/guardrex) und [Daniel Roth](https://github.com/danroth27)

Razor-Komponenten stellen Features zur Datenbindung über ein HTML-Elementattribut namens [`@bind`](xref:mvc/views/razor#bind) bereit, mit einem Feld, einer Eigenschaft oder einem Razor-Ausdruckswert.

Im folgenden Beispiel wird ein `<input>`-Element an das `currentValue`-Feld und ein `<input>`-Element an die `CurrentValue`-Eigenschaft gebunden:

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

Wenn eines der Elemente den Fokus verliert, wird das gebundene Feld oder die Eigenschaft aktualisiert.

Das Textfeld wird in der Benutzeroberfläche nur dann aktualisiert, wenn die Komponente gerendert wird, nicht als Reaktion auf die Änderung des Werts des Felds oder der Eigenschaft. Da sich Komponenten nach der Ausführung von Ereignishandlercode selbst rendern, werden Feld- und Eigenschaftsaktualisierungen *in der Regel* unmittelbar nach dem Auslösen eines Ereignishandlers in der Benutzeroberfläche widergespiegelt.

Die Verwendung von [`@bind`](xref:mvc/views/razor#bind) mit der `CurrentValue`-Eigenschaft (`<input @bind="CurrentValue" />`) entspricht im Wesentlichen dem Folgenden:

```razor
<input value="@CurrentValue"
    @onchange="@((ChangeEventArgs __e) => CurrentValue = 
        __e.Value.ToString())" />

@code {
    private string CurrentValue { get; set; }
}
```

Wenn die Komponente gerendert wird, stammt der `value` des Eingabeelements aus der `CurrentValue`-Eigenschaft. Wenn der Benutzer in das Textfeld eingibt und den Elementfokus ändert, wird das Ereignis `onchange` ausgelöst und die Eigenschaft `CurrentValue` auf den geänderten Wert festgelegt. In Wirklichkeit ist die Codegenerierung komplexer, weil [`@bind`](xref:mvc/views/razor#bind) Fälle behandelt, in denen Typkonvertierungen durchgeführt werden. Im Prinzip assoziiert [`@bind`](xref:mvc/views/razor#bind) den aktuellen Wert eines Ausdrucks mit einem `value`-Attribut und behandelt Änderungen mit dem registrierten Handler.

Binden Sie eine Eigenschaft oder ein Feld an andere Ereignisse, indem Sie auch ein `@bind:event`-Attribut mit einem `event`-Parameter einbeziehen. Das folgende Beispiel bindet die Eigenschaft `CurrentValue` an das Ereignis `oninput`:

```razor
<input @bind="CurrentValue" @bind:event="oninput" />

@code {
    private string CurrentValue { get; set; }
}
```

Im Gegensatz zu `onchange`, das ausgelöst wird, wenn das Element den Fokus verliert, wird `oninput` ausgelöst, wenn sich der Wert des Textfelds ändert.

<!-- Hold location for resolution of https://github.com/dotnet/AspNetCore.Docs/issues/19721 -->

Bei der Attributbindung wird die Groß- und Kleinschreibung berücksichtigt.

* `@bind` ist gültig.
* `@Bind` und `@BIND` sind ungültig.

## <a name="unparsable-values"></a>Nicht analysierbare Werte

Wenn ein Benutzer einem Element mit Datenbindung einen nicht analysierbaren Wert zur Verfügung stellt, wird der nicht analysierbare Wert automatisch auf seinen vorherigen Wert zurückgesetzt, wenn das Bindungsereignis ausgelöst wird.

Betrachten Sie das folgende Szenario:

* Ein `<input>`-Element ist an einen `int`-Typ mit einem Anfangswert von `123` gebunden:

  ```razor
  <input @bind="inputValue" />

  @code {
      private int inputValue = 123;
  }
  ```

* Der Benutzer aktualisiert den Wert des Elements auf der Seite zu `123.45` und ändert den Elementfokus.

Im vorangehenden Szenario wird der Wert des Elements auf `123` zurückgesetzt. Wenn der Wert `123.45` zugunsten des ursprünglichen Werts von `123` abgelehnt wird, versteht der Benutzer, dass sein Wert nicht akzeptiert wurde.

Standardmäßig gilt die Bindung für das `onchange`-Ereignis des Elements (`@bind="{PROPERTY OR FIELD}"`). Verwenden Sie `@bind="{PROPERTY OR FIELD}" @bind:event={EVENT}`, um die Bindung bei einem anderen Ereignis auszulösen. Für das `oninput`-Ereignis (`@bind:event="oninput"`) erfolgt die Umkehrung nach jedem Tastendruck, der einen nicht analysierbaren Wert einführt. Wenn das `oninput`-Ereignis mit einem `int`-gebundenen Typ angestrebt wird, wird ein Benutzer daran gehindert, ein `.`-Zeichen einzugeben. Ein `.`-Zeichen wird sofort entfernt, sodass der Benutzer sofort die Rückmeldung erhält, dass nur ganze Zahlen zulässig sind. Es gibt Szenarien, in denen die Umkehrung des Werts auf das `oninput` Ereignis nicht ideal ist, z. B. wenn dem Benutzer erlaubt werden soll, einen nicht analysierbaren `<input>`-Wert zu löschen. Zu den Alternativen gehören:

* Verwenden Sie nicht das Ereignis `oninput`. Verwenden Sie das Standardereignis `onchange` (geben Sie nur `@bind="{PROPERTY OR FIELD}"` an), bei dem ein ungültiger Wert erst dann rückgängig gemacht wird, wenn das Element den Fokus verliert.
* Binden Sie an einen Nullable-Typ, z. B. `int?` oder `string`, und stellen Sie benutzerdefinierte Logik zur Behandlung ungültiger Einträge bereit.
* Verwenden Sie eine [Formularüberprüfungskomponente](xref:blazor/forms-validation) wie <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> oder <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601>. Formularüberprüfungskomponenten verfügen über die integrierte Unterstützung der Verwaltung ungültiger Eingaben. Weitere Informationen finden Sie unter <xref:blazor/forms-validation>. Formularüberprüfungskomponenten:
  * Erlauben Sie dem Benutzer, ungültige Eingaben zu machen und Überprüfungsfehler für das zugehörige <xref:Microsoft.AspNetCore.Components.Forms.EditContext> zu erhalten.
  * Zeigen Sie Überprüfungsfehler auf der Benutzeroberfläche an, ohne den Benutzer bei der Eingabe zusätzlicher WebForm-Daten zu beeinträchtigen.

## <a name="format-strings"></a>Formatzeichenfolgen

Die Datenbindung funktioniert mit <xref:System.DateTime>-Formatzeichenfolgen, die `@bind:format` verwenden. Andere Formatausdrücke wie Währungs- oder Zahlenformate sind zu diesem Zeitpunkt nicht verfügbar.

```razor
<input @bind="startDate" @bind:format="yyyy-MM-dd" />

@code {
    private DateTime startDate = new DateTime(2020, 1, 1);
}
```

Im vorhergehenden Code ist der Feldtyp des `<input>`-Elements (`type`) standardmäßig auf `text` festgelegt. `@bind:format` wird für die Bindung der folgenden .NET-Typen unterstützt:

* <xref:System.DateTime?displayProperty=fullName>
* <xref:System.DateTime?displayProperty=fullName>?
* <xref:System.DateTimeOffset?displayProperty=fullName>
* <xref:System.DateTimeOffset?displayProperty=fullName>?

Das `@bind:format`-Attribut gibt das Datumsformat an, das auf den `value` des `<input>`-Elements angewendet werden soll. Das Format wird auch verwendet, um den Wert beim Eintreten eines `onchange`-Ereignisses zu analysieren.

Die Angabe eines Formats für den Feldtyp `date` wird nicht empfohlen, da Blazor eine integrierte Unterstützung für die Formatierung von Daten bietet. Verwenden Sie trotz der Empfehlung nur dann das Datumsformat `yyyy-MM-dd` für die Bindung, um ordnungsgemäß zu funktionieren, wenn ein Format mit dem Feldtyp `date` bereitgestellt wird:

```razor
<input type="date" @bind="startDate" @bind:format="yyyy-MM-dd">
```

## <a name="parent-to-child-binding-with-component-parameters"></a>Bindung zwischen übergeordnetem und untergeordnetem Element mit Komponentenparametern

Komponentenparameter erlauben das Binden von Eigenschaften und Feldern einer übergeordneten Komponente mit `@bind-{PROPERTY OR FIELD}`-Syntax.

Die folgende `Child`-Komponente (`Child.razor`) verfügt über einen `Year`-Komponentenparameter und `YearChanged`-Rückruf:

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

Der Rückruf (<xref:Microsoft.AspNetCore.Components.EventCallback%601>) muss den Namen des Komponentenparameters aufweisen, gefolgt vom Suffix „`Changed`“ (`{PARAMETER NAME}Changed`). Im vorherigen Beispiel trägt der Rückruf den Namen `YearChanged`. Weitere Informationen zu <xref:Microsoft.AspNetCore.Components.EventCallback%601> finden Sie unter <xref:blazor/components/event-handling#eventcallback>.

In der folgenden `Parent`-Komponente (`Parent.razor`) ist das Feld `year` an den `Year`-Parameter der untergeordneten Komponente gebunden:

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

Der Parameter `Year` ist bindbar, da er ein Begleitereignis `YearChanged` aufweist, das dem Typ des Parameters `Year` entspricht.

Gemäß der Konvention kann eine Eigenschaft an einen entsprechenden Ereignishandler gebunden werden, indem ein `@bind-{PROPERTY}:event`-Attribut einbezogen wird, das dem Handler zugewiesen ist. `<Child @bind-Year="year" />` ist identisch mit dem folgenden Code:

```razor
<Child @bind-Year="year" @bind-Year:event="YearChanged" />
```

## <a name="child-to-parent-binding-with-chained-bind"></a>Bindung zwischen untergeordnetem und übergeordnetem Element mit verketteter Bindung

Ein häufiges Szenario ist die Verkettung eines datengebundenen Parameters mit einem Seitenelement in der Ausgabe der Komponente. Dieses Szenario wird als *verkettete Bindung* bezeichnet, da mehrere Ebenen der Bindung gleichzeitig auftreten.

Eine verkettete Bindung kann nicht mit der [`@bind`](xref:mvc/views/razor#bind)-Syntax in der untergeordneten Komponente implementiert werden. Der Ereignishandler und der Wert müssen separat angegeben werden. Eine übergeordnete Komponente kann jedoch die [`@bind`](xref:mvc/views/razor#bind)-Syntax mit dem Parameter der untergeordneten Komponente verwenden.

Die folgende `PasswordField`-Komponente (`PasswordField.razor`):

* Legt den Wert eines `<input>`-Elements auf eine `Password`-Eigenschaft fest.
* Macht Änderungen der `Password`-Eigenschaft einer übergeordneten Komponente mit einem [`EventCallback`](xref:blazor/components/event-handling#eventcallback) verfügbar.
* Verwendet das `onclick`-Ereignis zum Auslösen der `ToggleShowPassword`-Methode. Weitere Informationen finden Sie unter <xref:blazor/components/event-handling>.

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

Die `PasswordField`-Komponente wird in einer anderen Komponente verwendet:

```razor
@page "/Parent"

<h1>Parent Component</h1>

<PasswordField @bind-Password="password" />

@code {
    private string password;
}
```

So führen Sie Prüfungen durch oder fangen Fehler beim Kennwort im vorhergehenden Beispiel ab

* Erstellen Sie ein Unterstützungsfeld für `Password` (`password` im folgenden Beispielcode).
* Führen Sie die Prüfungen durch, oder fangen Sie Fehler im `Password`-Setter ab.

Das folgende Beispiel gibt dem Benutzer ein sofortiges Feedback, wenn ein Leerzeichen im Wert des Kennworts verwendet wird:

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

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Binden an Optionsfelder in einem Formular](xref:blazor/forms-validation#radio-buttons)
* [Binden von `<select>`-Elementoptionen an `null`-Werte von C#-Objekten in einem Formular](xref:blazor/forms-validation#binding-select-element-options-to-c-object-null-values)
