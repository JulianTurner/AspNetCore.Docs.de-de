---
title: 'Kaskadierende Werte und Parameter in ASP.NET Core Blazor'
author: guardrex
description: Erfahren Sie, wie Sie Daten aus einer Vorgängerkomponente in Nachfolgerkomponenten übertragen.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/06/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: blazor/components/cascading-values-and-parameters
ms.openlocfilehash: dec71f881dc815a26d62da6df92118926b1dcf06
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93056425"
---
# <a name="aspnet-core-no-locblazor-cascading-values-and-parameters"></a><span data-ttu-id="77104-103">Kaskadierende Werte und Parameter in ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="77104-103">ASP.NET Core Blazor cascading values and parameters</span></span>

<span data-ttu-id="77104-104">Von [Luke Latham](https://github.com/guardrex) und [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="77104-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="77104-105">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="77104-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="77104-106">In einigen Szenarios ist es unpraktisch, Daten mithilfe von [Komponentenparametern](xref:blazor/components/index#component-parameters) von einer Vorgängerkomponente an eine Nachfolgerkomponente zu übertragen. Das gilt insbesondere, wenn es mehrere Komponentenebenen gibt.</span><span class="sxs-lookup"><span data-stu-id="77104-106">In some scenarios, it's inconvenient to flow data from an ancestor component to a descendent component using [component parameters](xref:blazor/components/index#component-parameters), especially when there are several component layers.</span></span> <span data-ttu-id="77104-107">Kaskadierende Werte und Parameter lösen dieses Problem, indem es Vorgängerkomponenten einfach ermöglicht wird, für alle Nachfolgerkomponenten einen Wert bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="77104-107">Cascading values and parameters solve this problem by providing a convenient way for an ancestor component to provide a value to all of its descendent components.</span></span> <span data-ttu-id="77104-108">Kaskadierende Werte und Parameter bieten auch einen Ansatz für die Koordination von Komponenten.</span><span class="sxs-lookup"><span data-stu-id="77104-108">Cascading values and parameters also provide an approach for components to coordinate.</span></span>

### <a name="theme-example"></a><span data-ttu-id="77104-109">Designbeispiel</span><span class="sxs-lookup"><span data-stu-id="77104-109">Theme example</span></span>

<span data-ttu-id="77104-110">Im folgenden Beispiel aus der Beispiel-App gibt die `ThemeInfo`-Klasse die Designinformationen an, die in der Komponentenhierarchie nach unten weitergegeben werden, sodass alle Schaltflächen innerhalb eines bestimmten Teils der App denselben Stil aufweisen.</span><span class="sxs-lookup"><span data-stu-id="77104-110">In the following example from the sample app, the `ThemeInfo` class specifies the theme information to flow down the component hierarchy so that all of the buttons within a given part of the app share the same style.</span></span>

<span data-ttu-id="77104-111">`UIThemeClasses/ThemeInfo.cs`:</span><span class="sxs-lookup"><span data-stu-id="77104-111">`UIThemeClasses/ThemeInfo.cs`:</span></span>

```csharp
public class ThemeInfo
{
    public string ButtonClass { get; set; }
}
```

<span data-ttu-id="77104-112">Eine Vorgängerkomponente kann einen kaskadierenden Wert mithilfe der CascadingValue-Komponente bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="77104-112">An ancestor component can provide a cascading value using the Cascading Value component.</span></span> <span data-ttu-id="77104-113">Die <xref:Microsoft.AspNetCore.Components.CascadingValue%601>-Komponente umschließt eine Teilstruktur der Komponentenhierarchie und stellt einen einzelnen Wert für alle Komponenten in dieser Unterstruktur bereit.</span><span class="sxs-lookup"><span data-stu-id="77104-113">The <xref:Microsoft.AspNetCore.Components.CascadingValue%601> component wraps a subtree of the component hierarchy and supplies a single value to all components within that subtree.</span></span>

<span data-ttu-id="77104-114">Beispielsweise gibt die Beispiel-App Designinformationen (`ThemeInfo`) in einem der Layouts der App als kaskadierenden Parameter für alle Komponenten an, die den Layouttext der `@Body`-Eigenschaft bilden.</span><span class="sxs-lookup"><span data-stu-id="77104-114">For example, the sample app specifies theme information (`ThemeInfo`) in one of the app's layouts as a cascading parameter for all components that make up the layout body of the `@Body` property.</span></span> <span data-ttu-id="77104-115">`ButtonClass` wird in der Layoutkomponente der Wert `btn-success` zugewiesen.</span><span class="sxs-lookup"><span data-stu-id="77104-115">`ButtonClass` is assigned a value of `btn-success` in the layout component.</span></span> <span data-ttu-id="77104-116">Jede Nachfolgerkomponente kann diese Eigenschaft über das kaskadierende `ThemeInfo`-Objekt nutzen.</span><span class="sxs-lookup"><span data-stu-id="77104-116">Any descendent component can consume this property through the `ThemeInfo` cascading object.</span></span>

<span data-ttu-id="77104-117">`CascadingValuesParametersLayout`-Komponente:</span><span class="sxs-lookup"><span data-stu-id="77104-117">`CascadingValuesParametersLayout` component:</span></span>

```razor
@inherits LayoutComponentBase
@using BlazorSample.UIThemeClasses

<div class="container-fluid">
    <div class="row">
        <div class="col-sm-3">
            <NavMenu />
        </div>
        <div class="col-sm-9">
            <CascadingValue Value="theme">
                <div class="content px-4">
                    @Body
                </div>
            </CascadingValue>
        </div>
    </div>
</div>

@code {
    private ThemeInfo theme = new ThemeInfo { ButtonClass = "btn-success" };
}
```

<span data-ttu-id="77104-118">Komponenten deklarieren kaskadierende Parameter mithilfe des [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute)-Attributs, um kaskadierende Werte zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="77104-118">To make use of cascading values, components declare cascading parameters using the [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) attribute.</span></span> <span data-ttu-id="77104-119">Kaskadierende Werte werden nach Typ an kaskadierende Parameter gebunden.</span><span class="sxs-lookup"><span data-stu-id="77104-119">Cascading values are bound to cascading parameters by type.</span></span>

<span data-ttu-id="77104-120">In der Beispiel-App bindet die `CascadingValuesParametersTheme`-Komponente den kaskadierenden `ThemeInfo`-Wert an einen kaskadierenden Parameter.</span><span class="sxs-lookup"><span data-stu-id="77104-120">In the sample app, the `CascadingValuesParametersTheme` component binds the `ThemeInfo` cascading value to a cascading parameter.</span></span> <span data-ttu-id="77104-121">Der Parameter wird verwendet, um die CSS-Klasse für eine der Schaltflächen festzulegen, die von der Komponente angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="77104-121">The parameter is used to set the CSS class for one of the buttons displayed by the component.</span></span>

<span data-ttu-id="77104-122">`CascadingValuesParametersTheme`-Komponente:</span><span class="sxs-lookup"><span data-stu-id="77104-122">`CascadingValuesParametersTheme` component:</span></span>

```razor
@page "/cascadingvaluesparameterstheme"
@layout CascadingValuesParametersLayout
@using BlazorSample.UIThemeClasses

<h1>Cascading Values & Parameters</h1>

<p>Current count: @currentCount</p>

<p>
    <button class="btn" @onclick="IncrementCount">
        Increment Counter (Unthemed)
    </button>
</p>

<p>
    <button class="btn @ThemeInfo.ButtonClass" @onclick="IncrementCount">
        Increment Counter (Themed)
    </button>
</p>

@code {
    private int currentCount = 0;

    [CascadingParameter]
    protected ThemeInfo ThemeInfo { get; set; }

    private void IncrementCount()
    {
        currentCount++;
    }
}
```

<span data-ttu-id="77104-123">Sie können mehrere Werte desselben Typs innerhalb derselben Unterstruktur kaskadieren, indem Sie jeder <xref:Microsoft.AspNetCore.Components.CascadingValue%601>-Komponente und dem entsprechenden [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute)-Attribut eine eindeutige <xref:Microsoft.AspNetCore.Components.CascadingValue%601.Name%2A>-Zeichenfolge bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="77104-123">To cascade multiple values of the same type within the same subtree, provide a unique <xref:Microsoft.AspNetCore.Components.CascadingValue%601.Name%2A> string to each <xref:Microsoft.AspNetCore.Components.CascadingValue%601> component and its corresponding [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) attribute.</span></span> <span data-ttu-id="77104-124">Im folgenden Beispiel kaskadieren zwei <xref:Microsoft.AspNetCore.Components.CascadingValue%601>-Komponenten verschiedene Instanzen von `MyCascadingType` nach Namen:</span><span class="sxs-lookup"><span data-stu-id="77104-124">In the following example, two <xref:Microsoft.AspNetCore.Components.CascadingValue%601> components cascade different instances of `MyCascadingType` by name:</span></span>

```razor
<CascadingValue Value="@parentCascadeParameter1" Name="CascadeParam1">
    <CascadingValue Value="@ParentCascadeParameter2" Name="CascadeParam2">
        ...
    </CascadingValue>
</CascadingValue>

@code {
    private MyCascadingType parentCascadeParameter1;

    [Parameter]
    public MyCascadingType ParentCascadeParameter2 { get; set; }

    ...
}
```

<span data-ttu-id="77104-125">In einer Nachfolgerkomponente erhalten die kaskadierenden Parameter ihre Werte nach Namen von den entsprechenden kaskadierenden Werten in der Vorgängerkomponente:</span><span class="sxs-lookup"><span data-stu-id="77104-125">In a descendant component, the cascaded parameters receive their values from the corresponding cascaded values in the ancestor component by name:</span></span>

```razor
...

@code {
    [CascadingParameter(Name = "CascadeParam1")]
    protected MyCascadingType ChildCascadeParameter1 { get; set; }
    
    [CascadingParameter(Name = "CascadeParam2")]
    protected MyCascadingType ChildCascadeParameter2 { get; set; }
}
```

### <a name="tabset-example"></a><span data-ttu-id="77104-126">TabSet-Beispiel</span><span class="sxs-lookup"><span data-stu-id="77104-126">TabSet example</span></span>

<span data-ttu-id="77104-127">Kaskadierende Parameter ermöglichen auch die Zusammenarbeit von Komponenten in der Komponentenhierarchie.</span><span class="sxs-lookup"><span data-stu-id="77104-127">Cascading parameters also enable components to collaborate across the component hierarchy.</span></span> <span data-ttu-id="77104-128">Sehen Sie sich z. B. das folgende `TabSet`-Beispiel in der Beispiel-App an.</span><span class="sxs-lookup"><span data-stu-id="77104-128">For example, consider the following `TabSet` example in the sample app.</span></span>

<span data-ttu-id="77104-129">Die Beispiel-App enthält eine `ITab`-Schnittstelle, die Registerkarten implementieren:</span><span class="sxs-lookup"><span data-stu-id="77104-129">The sample app has an `ITab` interface that tabs implement:</span></span>

[!code-csharp[](../common/samples/3.x/BlazorWebAssemblySample/UIInterfaces/ITab.cs)]

<span data-ttu-id="77104-130">Die `CascadingValuesParametersTabSet`-Komponente verwendet die `TabSet`-Komponente, die mehrere `Tab`-Komponenten enthält:</span><span class="sxs-lookup"><span data-stu-id="77104-130">The `CascadingValuesParametersTabSet` component uses the `TabSet` component, which contains several `Tab` components:</span></span>

```razor
@page "/CascadingValuesParametersTabSet"

<TabSet>
    <Tab Title="First tab">
        <h4>Greetings from the first tab!</h4>

        <label>
            <input type="checkbox" @bind="showThirdTab" />
            Toggle third tab
        </label>
    </Tab>
    <Tab Title="Second tab">
        <h4>The second tab says Hello World!</h4>
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

<span data-ttu-id="77104-131">Die untergeordneten `Tab`-Komponenten werden nicht explizit als Parameter an `TabSet`übermittelt.</span><span class="sxs-lookup"><span data-stu-id="77104-131">The child `Tab` components aren't explicitly passed as parameters to the `TabSet`.</span></span> <span data-ttu-id="77104-132">Stattdessen sind die untergeordneten `Tab`-Komponenten Teil des untergeordneten Inhalts von `TabSet`.</span><span class="sxs-lookup"><span data-stu-id="77104-132">Instead, the child `Tab` components are part of the child content of the `TabSet`.</span></span> <span data-ttu-id="77104-133">Allerdings muss `TabSet` weiterhin über jede `Tab`-Komponente informiert werden, damit die Header und die aktive Registerkarte gerendert werden können. Damit diese Koordination ohne zusätzlichen Code möglich ist, kann die `TabSet`-Komponente *sich selbst als kaskadierenden Wert angeben* , der dann von der `Tab`-Nachfolgerkomponente abgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="77104-133">However, the `TabSet` still needs to know about each `Tab` component so that it can render the headers and the active tab. To enable this coordination without requiring additional code, the `TabSet` component *can provide itself as a cascading value* that is then picked up by the descendent `Tab` components.</span></span>

<span data-ttu-id="77104-134">`TabSet`-Komponente:</span><span class="sxs-lookup"><span data-stu-id="77104-134">`TabSet` component:</span></span>

[!code-razor[](../common/samples/3.x/BlazorWebAssemblySample/Components/TabSet.razor)]

<span data-ttu-id="77104-135">Die `Tab`-Nachfolgerkomponenten erfassen die enthaltende `TabSet`-Komponente als kaskadierenden Parameter. Das bedeutet, dass die `Tab`-Komponenten sich zu `TabSet` hinzufügen und koordinieren, welche Registerkarte aktiv ist.</span><span class="sxs-lookup"><span data-stu-id="77104-135">The descendent `Tab` components capture the containing `TabSet` as a cascading parameter, so the `Tab` components add themselves to the `TabSet` and coordinate on which tab is active.</span></span>

<span data-ttu-id="77104-136">`Tab`-Komponente:</span><span class="sxs-lookup"><span data-stu-id="77104-136">`Tab` component:</span></span>

[!code-razor[](../common/samples/3.x/BlazorWebAssemblySample/Components/Tab.razor)]
