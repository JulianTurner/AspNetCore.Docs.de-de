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
# <a name="aspnet-core-blazor-cascading-values-and-parameters"></a><span data-ttu-id="4dead-103">Kaskadierende Werte und Parameter in ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="4dead-103">ASP.NET Core Blazor cascading values and parameters</span></span>

<span data-ttu-id="4dead-104">*Kaskadierende Werte und Parameter* bieten eine praktische Möglichkeit, Daten nach unten in einer Komponentenhierarchie zu übermitteln, dabei kann es sich um eine Vorgängerkomponente bis hin zu beliebigen untergeordneten Nachfolgerkomponenten handeln.</span><span class="sxs-lookup"><span data-stu-id="4dead-104">*Cascading values and parameters* provide a convienent way to flow data down a component hierarchy from an ancestor component to any number of decendent components.</span></span> <span data-ttu-id="4dead-105">Im Gegensatz zu [Komponentenparametern](xref:blazor/components/index#component-parameters) erfordern kaskadierende Werte und Parameter keine Attributzuweisung für jede Nachfolgerkomponente, in der die Daten genutzt werden.</span><span class="sxs-lookup"><span data-stu-id="4dead-105">Unlike [Component parameters](xref:blazor/components/index#component-parameters), cascading values and parameters don't require an attribute assignment for each descendent component where the data is consumed.</span></span> <span data-ttu-id="4dead-106">Mithilfe von kaskadierenden Werten und Parametern können Komponenten auch über eine Komponentenhierarchie miteinander abgestimmt werden.</span><span class="sxs-lookup"><span data-stu-id="4dead-106">Cascading values and parameters also allow components to coordinate with each other across a component hierarchy.</span></span>

## <a name="cascadingvalue-component"></a><span data-ttu-id="4dead-107">`CascadingValue`-Komponente</span><span class="sxs-lookup"><span data-stu-id="4dead-107">`CascadingValue` component</span></span>

<span data-ttu-id="4dead-108">Eine Vorgängerkomponente stellt einen kaskadierenden Wert mithilfe der [`CascadingValue`](xref:Microsoft.AspNetCore.Components.CascadingValue%601)-Komponente des Blazor-Frameworks bereit, die eine Unterstruktur einer Komponentenhierarchie umschließt und einen einzelnen Wert für alle Komponenten in deren Unterstruktur bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="4dead-108">An ancestor component provides a cascading value using the Blazor framework's [`CascadingValue`](xref:Microsoft.AspNetCore.Components.CascadingValue%601) component, which wraps a subtree of a component hierarchy and supplies a single value to all of the components within its subtree.</span></span>

<span data-ttu-id="4dead-109">Das folgende Beispiel veranschaulicht den Ablauf der Designinformationen nach unten in der Komponentenhierarchie einer Layoutkomponente, um eine CSS-Formatklasse für Schaltflächen in untergeordneten Komponenten bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="4dead-109">The following example demonstrates the flow of theme information down the component hierarchy of a layout component to provide a CSS style class to buttons in child components.</span></span>

<span data-ttu-id="4dead-110">Die folgende C#-Klasse `ThemeInfo` wird in einem Ordner namens `UIThemeClasses` platziert und gibt die Designinformation an.</span><span class="sxs-lookup"><span data-stu-id="4dead-110">The following `ThemeInfo` C# class is placed in a folder named `UIThemeClasses` and specifies the theme information.</span></span>

> [!NOTE]
> <span data-ttu-id="4dead-111">Für die Beispiele in diesem Abschnitt lautet der Namespace der App `BlazorSample`.</span><span class="sxs-lookup"><span data-stu-id="4dead-111">For the examples in this section, the app's namespace is `BlazorSample`.</span></span> <span data-ttu-id="4dead-112">Wenn Sie mit dem Code in Ihrer eigenen Beispiel-App experimentieren, ändern Sie den Namespace der App in den Namespace Ihrer eigenen Beispiel-App.</span><span class="sxs-lookup"><span data-stu-id="4dead-112">When experimenting with the code in your own sample app, change the app's namespace to your sample app's namespace.</span></span>

<span data-ttu-id="4dead-113">`UIThemeClasses/ThemeInfo.cs`:</span><span class="sxs-lookup"><span data-stu-id="4dead-113">`UIThemeClasses/ThemeInfo.cs`:</span></span>

```csharp
namespace BlazorSample.UIThemeClasses
{
    public class ThemeInfo
    {
        public string ButtonClass { get; set; }
    }
}
```

<span data-ttu-id="4dead-114">Die folgende [Layoutkomponente](xref:blazor/layouts) gibt die Designinformation (`ThemeInfo`) als kaskadierenden Wert für alle Komponenten an, aus denen der Layouttext der <xref:Microsoft.AspNetCore.Components.LayoutComponentBase.Body>-Eigenschaft besteht.</span><span class="sxs-lookup"><span data-stu-id="4dead-114">The following [layout component](xref:blazor/layouts) specifies theme information (`ThemeInfo`) as a cascading value for all components that make up the layout body of the <xref:Microsoft.AspNetCore.Components.LayoutComponentBase.Body> property.</span></span> <span data-ttu-id="4dead-115">`ButtonClass` wird ein Wert von [`btn-success`](https://getbootstrap.com/docs/5.0/components/buttons/) zugewiesen, bei dem es sich um ein Bootstrapschaltflächenformat handelt.</span><span class="sxs-lookup"><span data-stu-id="4dead-115">`ButtonClass` is assigned a value of [`btn-success`](https://getbootstrap.com/docs/5.0/components/buttons/), which is a Bootstrap button style.</span></span> <span data-ttu-id="4dead-116">Jede Nachfolgerkomponente in der Komponentenhierarchie kann die `ButtonClass`-Eigenschaft über den kaskadierenden `ThemeInfo`-Wert verwenden.</span><span class="sxs-lookup"><span data-stu-id="4dead-116">Any descendent component in the component hierarchy can use the `ButtonClass` property through the `ThemeInfo` cascading value.</span></span>

<span data-ttu-id="4dead-117">`Shared/MainLayout.razor`:</span><span class="sxs-lookup"><span data-stu-id="4dead-117">`Shared/MainLayout.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/MainLayout.razor?highlight=2,10-14,19)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/MainLayout.razor?highlight=2,9-13,17)]

::: moniker-end

## <a name="cascadingparameter-attribute"></a><span data-ttu-id="4dead-118">`[CascadingParameter]`-Attribut</span><span class="sxs-lookup"><span data-stu-id="4dead-118">`[CascadingParameter]` attribute</span></span>

<span data-ttu-id="4dead-119">Nachfolgerkomponenten deklarieren kaskadierende Parameter mithilfe des [`[CascadingParameter]`-Attributs](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute), um die kaskadierenden Werte zu nutzen.</span><span class="sxs-lookup"><span data-stu-id="4dead-119">To make use of cascading values, descendent components declare cascading parameters using the [`[CascadingParameter]` attribute](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute).</span></span> <span data-ttu-id="4dead-120">Kaskadierende Werte sind **nach Typ** an kaskadierende Parameter gebunden.</span><span class="sxs-lookup"><span data-stu-id="4dead-120">Cascading values are bound to cascading parameters **by type**.</span></span> <span data-ttu-id="4dead-121">Das Kaskadieren mehrerer Werte desselben Typs wird später in diesem Artikel im Abschnitt [Kaskadieren mehrerer Werte](#cascade-multiple-values) behandelt.</span><span class="sxs-lookup"><span data-stu-id="4dead-121">Cascading multiple values of the same type is covered in the [Cascade multiple values](#cascade-multiple-values) section later in this article.</span></span>

<span data-ttu-id="4dead-122">Die folgende Komponente bindet den kaskadierenden `ThemeInfo`-Wert an einen kaskadierenden Parameter, wobei optional der gleiche Name `ThemeInfo` verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="4dead-122">The following component binds the `ThemeInfo` cascading value to a cascading parameter, optionally using the same name of `ThemeInfo`.</span></span> <span data-ttu-id="4dead-123">Der Parameter wird verwendet, um die CSS-Klasse für die Schaltfläche **`Increment Counter (Themed)`** festzulegen.</span><span class="sxs-lookup"><span data-stu-id="4dead-123">The parameter is used to set the CSS class for the **`Increment Counter (Themed)`** button.</span></span>

<span data-ttu-id="4dead-124">`Pages/ThemedCounter.razor`:</span><span class="sxs-lookup"><span data-stu-id="4dead-124">`Pages/ThemedCounter.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/ThemedCounter.razor?highlight=2,15-17,23-24)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/ThemedCounter.razor?highlight=2,15-17,23-24)]

::: moniker-end

## <a name="cascade-multiple-values"></a><span data-ttu-id="4dead-125">Kaskadieren mehrerer Werte</span><span class="sxs-lookup"><span data-stu-id="4dead-125">Cascade multiple values</span></span>

<span data-ttu-id="4dead-126">Sie können mehrere Werte desselben Typs innerhalb derselben Unterstruktur kaskadieren, indem Sie jeder [`CascadingValue`](xref:Microsoft.AspNetCore.Components.CascadingValue%601)-Komponente und dem entsprechenden [`[CascadingParameter]`-Attribut](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) eine eindeutige <xref:Microsoft.AspNetCore.Components.CascadingValue%601.Name%2A>-Zeichenfolge bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="4dead-126">To cascade multiple values of the same type within the same subtree, provide a unique <xref:Microsoft.AspNetCore.Components.CascadingValue%601.Name%2A> string to each [`CascadingValue`](xref:Microsoft.AspNetCore.Components.CascadingValue%601) component and their corresponding [`[CascadingParameter]` attributes](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute).</span></span>

<span data-ttu-id="4dead-127">Im folgenden Beispiel kaskadieren zwei [`CascadingValue`](xref:Microsoft.AspNetCore.Components.CascadingValue%601)-Komponenten verschiedene Instanzen von `CascadingType` nach Namen:</span><span class="sxs-lookup"><span data-stu-id="4dead-127">In the following example, two [`CascadingValue`](xref:Microsoft.AspNetCore.Components.CascadingValue%601) components cascade different instances of `CascadingType`:</span></span>

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

<span data-ttu-id="4dead-128">In einer Nachfolgerkomponente erhalten die kaskadierenden Parameter ihre kaskadierenden Werte von der Vorgängerkomponente nach <xref:Microsoft.AspNetCore.Components.CascadingValue%601.Name%2A>:</span><span class="sxs-lookup"><span data-stu-id="4dead-128">In a descendant component, the cascaded parameters receive their cascaded values from the ancestor component by <xref:Microsoft.AspNetCore.Components.CascadingValue%601.Name%2A>:</span></span>

```razor
...

@code {
    [CascadingParameter(Name = "CascadeParam1")]
    protected CascadingType ChildCascadeParameter1 { get; set; }
    
    [CascadingParameter(Name = "CascadeParam2")]
    protected CascadingType ChildCascadeParameter2 { get; set; }
}
```

## <a name="pass-data-across-a-component-hierarchy"></a><span data-ttu-id="4dead-129">Übergeben von Daten über eine Komponentenhierarchie</span><span class="sxs-lookup"><span data-stu-id="4dead-129">Pass data across a component hierarchy</span></span>

<span data-ttu-id="4dead-130">Kaskadierende Parameter ermöglichen Komponenten auch, Daten über eine Komponentenhierarchie zu übergeben.</span><span class="sxs-lookup"><span data-stu-id="4dead-130">Cascading parameters also enable components to pass data across a component hierarchy.</span></span> <span data-ttu-id="4dead-131">Sehen Sie sich das folgende Beispiel auf der Benutzeroberflächen-Registerkarte aus, auf der eine auf der Registerkarte festgelegte Komponente eine Reihe einzelner Tabs beibehält.</span><span class="sxs-lookup"><span data-stu-id="4dead-131">Consider the following UI tab set example, where a tab set component maintains a series of individual tabs.</span></span>

> [!NOTE]
> <span data-ttu-id="4dead-132">Für die Beispiele in diesem Abschnitt lautet der Namespace der App `BlazorSample`.</span><span class="sxs-lookup"><span data-stu-id="4dead-132">For the examples in this section, the app's namespace is `BlazorSample`.</span></span> <span data-ttu-id="4dead-133">Wenn Sie mit dem Code in Ihrer eigenen Beispiel-App experimentieren, ändern Sie den Namespace in den Namespace Ihrer eigenen Beispiel-App.</span><span class="sxs-lookup"><span data-stu-id="4dead-133">When experimenting with the code in your own sample app, change the namespace to your sample app's namespace.</span></span>

<span data-ttu-id="4dead-134">Erstellen Sie eine `ITab`-Schnittstelle, die von Registerkarten in einem Ordner namens `UIInterfaces` implementiert wird.</span><span class="sxs-lookup"><span data-stu-id="4dead-134">Create an `ITab` interface that tabs implement in a folder named `UIInterfaces`.</span></span>

<span data-ttu-id="4dead-135">`UIInterfaces/ITab.cs`:</span><span class="sxs-lookup"><span data-stu-id="4dead-135">`UIInterfaces/ITab.cs`:</span></span>

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

<span data-ttu-id="4dead-136">Die folgende `TabSet`-Komponente verwaltet eine Reihe von Registerkarten.</span><span class="sxs-lookup"><span data-stu-id="4dead-136">The following `TabSet` component maintains a set of tabs.</span></span> <span data-ttu-id="4dead-137">Die `Tab`-Komponenten der Registerkarten, die später in diesem Abschnitt erstellt werden, stellen die Listenelemente (`<li>...</li>`) für die Liste (`<ul>...</ul>`) bereit.</span><span class="sxs-lookup"><span data-stu-id="4dead-137">The tab set's `Tab` components, which are created later in this section, supply the list items (`<li>...</li>`) for the list (`<ul>...</ul>`).</span></span>

<span data-ttu-id="4dead-138">Die untergeordneten `Tab`-Komponenten werden nicht explizit als Parameter an `TabSet`übermittelt.</span><span class="sxs-lookup"><span data-stu-id="4dead-138">Child `Tab` components aren't explicitly passed as parameters to the `TabSet`.</span></span> <span data-ttu-id="4dead-139">Stattdessen sind die untergeordneten `Tab`-Komponenten Teil des untergeordneten Inhalts von `TabSet`.</span><span class="sxs-lookup"><span data-stu-id="4dead-139">Instead, the child `Tab` components are part of the child content of the `TabSet`.</span></span> <span data-ttu-id="4dead-140">Allerdings muss `TabSet` weiterhin über jede `Tab`-Komponente informiert werden, damit die Header und die aktive Registerkarte gerendert werden können. Damit diese Koordination ohne zusätzlichen Code möglich ist, kann die `TabSet`-Komponente *sich selbst als kaskadierenden Wert angeben*, der dann von der `Tab`-Nachfolgerkomponente abgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="4dead-140">However, the `TabSet` still needs a reference each `Tab` component so that it can render the headers and the active tab. To enable this coordination without requiring additional code, the `TabSet` component *can provide itself as a cascading value* that is then picked up by the descendent `Tab` components.</span></span>

<span data-ttu-id="4dead-141">`Shared/TabSet.razor`:</span><span class="sxs-lookup"><span data-stu-id="4dead-141">`Shared/TabSet.razor`:</span></span>

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

<span data-ttu-id="4dead-142">Die `Tab`-Nachfolgerkomponenten erfassen das weiterführenden `TabSet` als kaskadierenden Parameter.</span><span class="sxs-lookup"><span data-stu-id="4dead-142">Descendent `Tab` components capture the containing `TabSet` as a cascading parameter.</span></span> <span data-ttu-id="4dead-143">Die `Tab`-Komponenten fügen sich selbst zu `TabSet` hinzu, und stimmen sich miteinander ab, um die aktive Registerkarte festzulegen.</span><span class="sxs-lookup"><span data-stu-id="4dead-143">The `Tab` components add themselves to the `TabSet` and coordinate to set the active tab.</span></span>

<span data-ttu-id="4dead-144">`Shared/Tab.razor`:</span><span class="sxs-lookup"><span data-stu-id="4dead-144">`Shared/Tab.razor`:</span></span>

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

<span data-ttu-id="4dead-145">Die folgende `ExampleTabSet`-Komponente verwendet die `TabSet`-Komponente, die drei `Tab`-Komponenten enthält.</span><span class="sxs-lookup"><span data-stu-id="4dead-145">The following `ExampleTabSet` component uses the `TabSet` component, which contains three `Tab` components.</span></span>

<span data-ttu-id="4dead-146">`Pages/ExampleTabSet.razor`:</span><span class="sxs-lookup"><span data-stu-id="4dead-146">`Pages/ExampleTabSet.razor`:</span></span>

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
