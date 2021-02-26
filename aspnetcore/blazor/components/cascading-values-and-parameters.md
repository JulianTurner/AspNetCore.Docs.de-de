---
title: Kaskadierende Werte und Parameter in ASP.NET Core Blazor
author: guardrex
description: Erfahren Sie, wie Sie Daten aus einer Vorgängerkomponente in Nachfolgerkomponenten übertragen.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/02/2021
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
uid: blazor/components/cascading-values-and-parameters
ms.openlocfilehash: 1fb9d75ca1613a7098840efd3ecb86ee90f4064c
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280238"
---
# <a name="aspnet-core-blazor-cascading-values-and-parameters"></a>Kaskadierende Werte und Parameter in ASP.NET Core Blazor

*Kaskadierende Werte und Parameter* bieten eine praktische Möglichkeit, Daten nach unten in einer Komponentenhierarchie zu übermitteln, dabei kann es sich um eine Vorgängerkomponente bis hin zu beliebigen untergeordneten Nachfolgerkomponenten handeln. Im Gegensatz zu [Komponentenparametern](xref:blazor/components/index#component-parameters) erfordern kaskadierende Werte und Parameter keine Attributzuweisung für jede Nachfolgerkomponente, in der die Daten genutzt werden. Mithilfe von kaskadierenden Werten und Parametern können Komponenten auch über eine Komponentenhierarchie miteinander abgestimmt werden.

## <a name="cascadingvalue-component"></a>`CascadingValue`-Komponente

Eine Vorgängerkomponente stellt einen kaskadierenden Wert mithilfe der [`CascadingValue`](xref:Microsoft.AspNetCore.Components.CascadingValue%601)-Komponente des Blazor-Frameworks bereit, die eine Unterstruktur einer Komponentenhierarchie umschließt und einen einzelnen Wert für alle Komponenten in deren Unterstruktur bereitstellt.

Das folgende Beispiel veranschaulicht den Ablauf der Designinformationen nach unten in der Komponentenhierarchie einer Layoutkomponente, um eine CSS-Formatklasse für Schaltflächen in untergeordneten Komponenten bereitzustellen.

Die folgende C#-Klasse `ThemeInfo` wird in einem Ordner namens `UIThemeClasses` platziert und gibt die Designinformation an.

> [!NOTE]
> Für die Beispiele in diesem Abschnitt lautet der Namespace der App `BlazorSample`. Wenn Sie mit dem Code in Ihrer eigenen Beispiel-App experimentieren, ändern Sie den Namespace der App in den Namespace Ihrer eigenen Beispiel-App.

`UIThemeClasses/ThemeInfo.cs`:

```csharp
namespace BlazorSample.UIThemeClasses
{
    public class ThemeInfo
    {
        public string ButtonClass { get; set; }
    }
}
```

Die folgende [Layoutkomponente](xref:blazor/layouts) gibt die Designinformation (`ThemeInfo`) als kaskadierenden Wert für alle Komponenten an, aus denen der Layouttext der <xref:Microsoft.AspNetCore.Components.LayoutComponentBase.Body>-Eigenschaft besteht. `ButtonClass` wird ein Wert von [`btn-success`](https://getbootstrap.com/docs/5.0/components/buttons/) zugewiesen, bei dem es sich um ein Bootstrapschaltflächenformat handelt. Jede Nachfolgerkomponente in der Komponentenhierarchie kann die `ButtonClass`-Eigenschaft über den kaskadierenden `ThemeInfo`-Wert verwenden.

`Shared/MainLayout.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/MainLayout.razor?highlight=2,10-14,19)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/MainLayout.razor?highlight=2,9-13,17)]

::: moniker-end

## <a name="cascadingparameter-attribute"></a>`[CascadingParameter]`-Attribut

Nachfolgerkomponenten deklarieren kaskadierende Parameter mithilfe des [`[CascadingParameter]`-Attributs](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute), um die kaskadierenden Werte zu nutzen. Kaskadierende Werte sind **nach Typ** an kaskadierende Parameter gebunden. Das Kaskadieren mehrerer Werte desselben Typs wird später in diesem Artikel im Abschnitt [Kaskadieren mehrerer Werte](#cascade-multiple-values) behandelt.

Die folgende Komponente bindet den kaskadierenden `ThemeInfo`-Wert an einen kaskadierenden Parameter, wobei optional der gleiche Name `ThemeInfo` verwendet wird. Der Parameter wird verwendet, um die CSS-Klasse für die Schaltfläche **`Increment Counter (Themed)`** festzulegen.

`Pages/ThemedCounter.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/ThemedCounter.razor?highlight=2,15-17,23-24)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/ThemedCounter.razor?highlight=2,15-17,23-24)]

::: moniker-end

## <a name="cascade-multiple-values"></a>Kaskadieren mehrerer Werte

Sie können mehrere Werte desselben Typs innerhalb derselben Unterstruktur kaskadieren, indem Sie jeder [`CascadingValue`](xref:Microsoft.AspNetCore.Components.CascadingValue%601)-Komponente und dem entsprechenden [`[CascadingParameter]`-Attribut](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) eine eindeutige <xref:Microsoft.AspNetCore.Components.CascadingValue%601.Name%2A>-Zeichenfolge bereitstellen.

Im folgenden Beispiel kaskadieren zwei [`CascadingValue`](xref:Microsoft.AspNetCore.Components.CascadingValue%601)-Komponenten verschiedene Instanzen von `CascadingType` nach Namen:

```razor
<CascadingValue Value="@parentCascadeParameter1" Name="CascadeParam1">
    <CascadingValue Value="@ParentCascadeParameter2" Name="CascadeParam2">
        ...
    </CascadingValue>
</CascadingValue>

@code {
    private CascadingType parentCascadeParameter1;

    [Parameter]
    public CascadingType ParentCascadeParameter2 { get; set; }

    ...
}
```

In einer Nachfolgerkomponente erhalten die kaskadierenden Parameter ihre kaskadierenden Werte von der Vorgängerkomponente nach <xref:Microsoft.AspNetCore.Components.CascadingValue%601.Name%2A>:

```razor
...

@code {
    [CascadingParameter(Name = "CascadeParam1")]
    protected CascadingType ChildCascadeParameter1 { get; set; }
    
    [CascadingParameter(Name = "CascadeParam2")]
    protected CascadingType ChildCascadeParameter2 { get; set; }
}
```

## <a name="pass-data-across-a-component-hierarchy"></a>Übergeben von Daten über eine Komponentenhierarchie

Kaskadierende Parameter ermöglichen Komponenten auch, Daten über eine Komponentenhierarchie zu übergeben. Sehen Sie sich das folgende Beispiel auf der Benutzeroberflächen-Registerkarte aus, auf der eine auf der Registerkarte festgelegte Komponente eine Reihe einzelner Tabs beibehält.

> [!NOTE]
> Für die Beispiele in diesem Abschnitt lautet der Namespace der App `BlazorSample`. Wenn Sie mit dem Code in Ihrer eigenen Beispiel-App experimentieren, ändern Sie den Namespace in den Namespace Ihrer eigenen Beispiel-App.

Erstellen Sie eine `ITab`-Schnittstelle, die von Registerkarten in einem Ordner namens `UIInterfaces` implementiert wird.

`UIInterfaces/ITab.cs`:

```csharp
using Microsoft.AspNetCore.Components;

namespace BlazorSample.UIInterfaces
{
    public interface ITab
    {
        RenderFragment ChildContent { get; }
    }
}
```

Die folgende `TabSet`-Komponente verwaltet eine Reihe von Registerkarten. Die `Tab`-Komponenten der Registerkarten, die später in diesem Abschnitt erstellt werden, stellen die Listenelemente (`<li>...</li>`) für die Liste (`<ul>...</ul>`) bereit.

Die untergeordneten `Tab`-Komponenten werden nicht explizit als Parameter an `TabSet`übermittelt. Stattdessen sind die untergeordneten `Tab`-Komponenten Teil des untergeordneten Inhalts von `TabSet`. Allerdings muss `TabSet` weiterhin über jede `Tab`-Komponente informiert werden, damit die Header und die aktive Registerkarte gerendert werden können. Damit diese Koordination ohne zusätzlichen Code möglich ist, kann die `TabSet`-Komponente *sich selbst als kaskadierenden Wert angeben*, der dann von der `Tab`-Nachfolgerkomponente abgerufen wird.

`Shared/TabSet.razor`:

```razor
@using BlazorSample.UIInterfaces

<!-- Display the tab headers -->

<CascadingValue Value=this>
    <ul class="nav nav-tabs">
        @ChildContent
    </ul>
</CascadingValue>

<!-- Display body for only the active tab -->

<div class="nav-tabs-body p-4">
    @ActiveTab?.ChildContent
</div>

@code {
    [Parameter]
    public RenderFragment ChildContent { get; set; }

    public ITab ActiveTab { get; private set; }

    public void AddTab(ITab tab)
    {
        if (ActiveTab == null)
        {
            SetActiveTab(tab);
        }
    }

    public void SetActiveTab(ITab tab)
    {
        if (ActiveTab != tab)
        {
            ActiveTab = tab;
            StateHasChanged();
        }
    }
}
```

Die `Tab`-Nachfolgerkomponenten erfassen das weiterführenden `TabSet` als kaskadierenden Parameter. Die `Tab`-Komponenten fügen sich selbst zu `TabSet` hinzu, und stimmen sich miteinander ab, um die aktive Registerkarte festzulegen.

`Shared/Tab.razor`:

```razor
@using BlazorSample.UIInterfaces
@implements ITab

<li>
    <a @onclick="ActivateTab" class="nav-link @TitleCssClass" role="button">
        @Title
    </a>
</li>

@code {
    [CascadingParameter]
    public TabSet ContainerTabSet { get; set; }

    [Parameter]
    public string Title { get; set; }

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    private string TitleCssClass => 
        ContainerTabSet.ActiveTab == this ? "active" : null;

    protected override void OnInitialized()
    {
        ContainerTabSet.AddTab(this);
    }

    private void ActivateTab()
    {
        ContainerTabSet.SetActiveTab(this);
    }
}
```

Die folgende `ExampleTabSet`-Komponente verwendet die `TabSet`-Komponente, die drei `Tab`-Komponenten enthält.

`Pages/ExampleTabSet.razor`:

```razor
@page "/example-tab-set"

<TabSet>
    <Tab Title="First tab">
        <h4>Greetings from the first tab!</h4>

        <label>
            <input type="checkbox" @bind="showThirdTab" />
            Toggle third tab
        </label>
    </Tab>

    <Tab Title="Second tab">
        <h4>Hello from the second tab!</h4>
    </Tab>

    @if (showThirdTab)
    {
        <Tab Title="Third tab">
            <h4>Welcome to the disappearing third tab!</h4>
            <p>Toggle this tab from the first tab.</p>
        </Tab>
    }
</TabSet>

@code {
    private bool showThirdTab;
}
```
