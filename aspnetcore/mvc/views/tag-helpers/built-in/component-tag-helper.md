---
title: Komponententaghilfsprogramm in ASP.net Core
author: guardrex
ms.author: riande
description: Erfahren Sie, wie Sie das taghilfsprogramm ASP.net Core Component zum Rendering von Razor Komponenten in Seiten und Ansichten verwenden.
ms.custom: mvc
ms.date: 04/15/2020
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
uid: mvc/views/tag-helpers/builtin-th/component-tag-helper
ms.openlocfilehash: cddbca7f95e4d2143d4632aaa83133bc6210e251
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059155"
---
# <a name="component-tag-helper-in-aspnet-core"></a>Komponententaghilfsprogramm in ASP.net Core

Von [Daniel Roth](https://github.com/danroth27) und [Luke Latham](https://github.com/guardrex)

Verwenden Sie das [Komponententaghilfsprogramm](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper) zum Rendern einer Komponente einer Seite oder Ansicht.

## <a name="prerequisites"></a>Voraussetzungen

Befolgen Sie die Anweisungen im Abschnitt *Vorbereiten der APP für die Verwendung von Komponenten im Bereich "Seiten und Ansichten* " des <xref:blazor/components/integrate-components-into-razor-pages-and-mvc-apps#prepare-the-app> Artikels.

## <a name="component-tag-helper"></a>Komponententaghilfsprogramm

Das folgende komponententaghilfsprogramm rendert die `Counter` Komponente in einer Seite oder Ansicht:

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Pages

...

<component type="typeof(Counter)" render-mode="ServerPrerendered" />
```

Im vorangehenden Beispiel wird davon ausgegangen, dass `Counter` sich die Komponente im *Seiten* Ordner der APP befindet. Der Platzhalter `{APP ASSEMBLY}` ist der Assemblyname der App (z. B. `@using BlazorSample.Pages`).

Mit dem komponententaghilfsprogramm können auch Parameter an Komponenten übergeben werden. Sehen `ColorfulCheckbox` Sie sich die folgende Komponente an, mit der die Farbe und Größe der Kontrollkästchen Bezeichnung festgelegt wird:

```razor
<label style="font-size:@(Size)px;color:@Color">
    <input @bind="Value"
           id="survey" 
           name="blazor" 
           type="checkbox" />
    Enjoying Blazor?
</label>

@code {
    [Parameter]
    public bool Value { get; set; }

    [Parameter]
    public int Size { get; set; } = 8;

    [Parameter]
    public string Color { get; set; }

    protected override void OnInitialized()
    {
        Size += 10;
    }
}
```

Die `Size` `int` Komponenten Parameter () und `Color` ( `string` ) können vom komponententaghilfsprogramm festgelegt werden: [component parameters](xref:blazor/components/index#component-parameters)

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Shared

...

<component type="typeof(ColorfulCheckbox)" render-mode="ServerPrerendered" 
    param-Size="14" param-Color="@("blue")" />
```

Im vorangehenden Beispiel wird davon ausgegangen, dass `ColorfulCheckbox` sich die Komponente im frei *gegebenen* Ordner der APP befindet. Der Platzhalter `{APP ASSEMBLY}` ist der Assemblyname der App (z. B. `@using BlazorSample.Shared`).

Der folgende HTML-Code wird in der Seite oder Sicht gerendert:

```html
<label style="font-size:24px;color:blue">
    <input id="survey" name="blazor" type="checkbox">
    Enjoying Blazor?
</label>
```

Das übergeben einer Zeichenfolge in Anführungszeichen erfordert einen [expliziten Razor Ausdruck](xref:mvc/views/razor#explicit-razor-expressions), wie `param-Color` im vorherigen Beispiel gezeigt. Das Verarbeitungs Razor Verhalten für einen `string` Typwert gilt nicht für ein- `param-*` Attribut, da es sich bei dem Attribut um einen- `object` Typ handelt.

Der Parametertyp muss JSON-serialisierbar sein. Dies bedeutet in der Regel, dass der Typ einen Standardkonstruktor und festleg bare Eigenschaften aufweisen muss. Beispielsweise können Sie einen Wert für `Size` und `Color` im vorangehenden Beispiel angeben, da die Typen von `Size` und `Color` primitive Typen ( `int` und `string` ) sind, die vom JSON-Serialisierungsprogramm unterstützt werden.

Im folgenden Beispiel wird ein-Klassenobjekt an die-Komponente übermittelt:

*MyClass.cs* :

```csharp
public class MyClass
{
    public MyClass()
    {
    }

    public int MyInt { get; set; } = 999;
    public string MyString { get; set; } = "Initial value";
}
```

**Die Klasse muss über einen öffentlichen Parameter losen Konstruktor verfügen.**

*Shared/MyComponent. Razor* :

```razor
<h2>MyComponent</h2>

<p>Int: @MyObject.MyInt</p>
<p>String: @MyObject.MyString</p>

@code
{
    [Parameter]
    public MyClass MyObject { get; set; }
}
```

*Pages/mypage. cshtml* :

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}
@using {APP ASSEMBLY}.Shared

...

@{
    var myObject = new MyClass();
    myObject.MyInt = 7;
    myObject.MyString = "Set by MyPage";
}

<component type="typeof(MyComponent)" render-mode="ServerPrerendered" 
    param-MyObject="@myObject" />
```

Im vorangehenden Beispiel wird davon ausgegangen, dass `MyComponent` sich die Komponente im frei *gegebenen* Ordner der APP befindet. Der Platzhalter `{APP ASSEMBLY}` ist der AssemblyName der APP (z `@using BlazorSample` `@using BlazorSample.Shared` . b. und). `MyClass` befindet sich im-Namespace der app.

<xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> konfiguriert folgende Einstellungen für die Komponente:

* Ob die Komponente zuvor für die Seite gerendert wird
* Ob die Komponente als statische HTML auf der Seite gerendert wird oder ob sie die nötigen Informationen für das Bootstrapping einer Blazor-App über den Benutzer-Agent enthält.

| Rendermodus | Beschreibung |
| ----------- | ----------- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | Rendert die Komponente in statisches HTML und fügt einen Marker für eine Blazor Server-App hinzu. Wenn der Benutzer-Agent gestartet wird, wird der Marker zum Bootstrapping einer Blazor-App verwendet. |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | Rendert einen Marker für eine Blazor Server-App. Die Ausgabe der Komponente ist nicht enthalten. Wenn der Benutzer-Agent gestartet wird, wird der Marker zum Bootstrapping einer Blazor-App verwendet. |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | Rendert die Komponente in statischen HTML-Code. |

Während Seiten und Ansichten Komponenten verwenden können, ist das Gegenteil nicht der Fall. Komponenten können keine Ansichts-und Seiten spezifischen Funktionen verwenden, wie z. b. partielle Sichten und Abschnitte. Wenn Sie Logik aus einer partiellen Sicht in einer Komponente verwenden möchten, müssen Sie die partielle Sicht Logik in eine Komponente einbeziehen.

Das Rendern von Serverkomponenten über eine statische HTML-Seite wird nicht unterstützt.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper>
* <xref:mvc/views/tag-helpers/intro>
* <xref:blazor/components/index>
