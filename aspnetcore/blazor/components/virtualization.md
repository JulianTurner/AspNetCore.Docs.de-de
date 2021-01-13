---
title: ASP.NET Core Blazor-Komponentenvirtualisierung
author: guardrex
description: Erfahren Sie, Komponentenvirtualisierung in ASP.NET Core Blazor-Apps verwendet wird.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/02/2020
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
uid: blazor/components/virtualization
ms.openlocfilehash: 920a23aee0d0555e93c829142700709d5881afd2
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/04/2021
ms.locfileid: "97753092"
---
# <a name="aspnet-core-no-locblazor-component-virtualization"></a><span data-ttu-id="b6706-103">ASP.NET Core Blazor-Komponentenvirtualisierung</span><span class="sxs-lookup"><span data-stu-id="b6706-103">ASP.NET Core Blazor component virtualization</span></span>

<span data-ttu-id="b6706-104">Von [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="b6706-104">By [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="b6706-105">Verbessern Sie die wahrgenommene Leistung von Komponentenrendering mithilfe der integrierten Virtualisierungsunterstützung des Blazor-Frameworks.</span><span class="sxs-lookup"><span data-stu-id="b6706-105">Improve the perceived performance of component rendering using the Blazor framework's built-in virtualization support.</span></span> <span data-ttu-id="b6706-106">Virtualisierung ist eine Technik zum Einschränken des Benutzeroberflächenrenderings auf die aktuell sichtbaren Elemente.</span><span class="sxs-lookup"><span data-stu-id="b6706-106">Virtualization is a technique for limiting UI rendering to just the parts that are currently visible.</span></span> <span data-ttu-id="b6706-107">Die Virtualisierung ist beispielsweise hilfreich, wenn die App eine lange Liste von Elementen rendern und nur eine Teilmenge der Elemente zu einem bestimmten Zeitpunkt sichtbar sein muss.</span><span class="sxs-lookup"><span data-stu-id="b6706-107">For example, virtualization is helpful when the app must render a long list of items and only a subset of items is required to be visible at any given time.</span></span> <span data-ttu-id="b6706-108">Blazor stellt die `Virtualize`-Komponente bereit, die verwendet werden kann, um den Komponenten einer App Virtualisierung hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="b6706-108">Blazor provides the `Virtualize` component that can be used to add virtualization to an app's components.</span></span>

<span data-ttu-id="b6706-109">Ohne Virtualisierung kann eine typische Liste eine C#-[`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in)-Schleife verwenden, um jedes Element in der Liste zu rendern:</span><span class="sxs-lookup"><span data-stu-id="b6706-109">Without virtualization, a typical list might use a C# [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) loop to render each item in the list:</span></span>

```razor
@foreach (var employee in employees)
{
    <p>
        @employee.FirstName @employee.LastName has the 
        job title of @employee.JobTitle.
    </p>
}
```

<span data-ttu-id="b6706-110">Wenn die Liste Tausende von Elementen enthält, kann das Rendern der Liste viel Zeit in Anspruch nehmen.</span><span class="sxs-lookup"><span data-stu-id="b6706-110">If the list contains thousands of items, then rendering the list may take a long time.</span></span> <span data-ttu-id="b6706-111">Der Benutzer kann ggf. eine merkliche Verzögerung der Benutzeroberfläche wahrnehmen.</span><span class="sxs-lookup"><span data-stu-id="b6706-111">The user may experience a noticeable UI lag.</span></span>

<span data-ttu-id="b6706-112">Anstatt jedes Element in der Liste gleichzeitig zu rendern, ersetzen Sie die [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in)-Schleife durch die `Virtualize`-Komponente, und geben Sie eine feste Elementquelle mit `Items` an.</span><span class="sxs-lookup"><span data-stu-id="b6706-112">Instead of rendering each item in the list all at one time, replace the [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) loop with the `Virtualize` component and specify a fixed item source with `Items`.</span></span> <span data-ttu-id="b6706-113">Nur die aktuell sichtbaren Elemente werden gerendert:</span><span class="sxs-lookup"><span data-stu-id="b6706-113">Only the items that are currently visible are rendered:</span></span>

```razor
<Virtualize Context="employee" Items="@employees">
    <p>
        @employee.FirstName @employee.LastName has the 
        job title of @employee.JobTitle.
    </p>
</Virtualize>
```

<span data-ttu-id="b6706-114">Wenn Sie mit `Context` keinen Kontext für die Komponente angeben, verwenden Sie den `context`-Wert (`@context.{PROPERTY}`) in der Elementinhaltsvorlage:</span><span class="sxs-lookup"><span data-stu-id="b6706-114">If not specifying a context to the component with `Context`, use the `context` value (`@context.{PROPERTY}`) in the item content template:</span></span>

```razor
<Virtualize Items="@employees">
    <p>
        @context.FirstName @context.LastName has the 
        job title of @context.JobTitle.
    </p>
</Virtualize>
```

<span data-ttu-id="b6706-115">Die `Virtualize`-Komponente berechnet, wie viele Elemente basierend auf der Höhe des Containers und der Größe der gerenderten Elemente gerendert werden.</span><span class="sxs-lookup"><span data-stu-id="b6706-115">The `Virtualize` component calculates how many items to render based on the height of the container and the size of the rendered items.</span></span>

<span data-ttu-id="b6706-116">Der Elementinhalt für die `Virtualize`-Komponente kann Folgendes umfassen:</span><span class="sxs-lookup"><span data-stu-id="b6706-116">The item content for the `Virtualize` component can include:</span></span>

* <span data-ttu-id="b6706-117">Einfachen HTML- und Razor-Code, wie im vorherigen Beispiel gezeigt</span><span class="sxs-lookup"><span data-stu-id="b6706-117">Plain HTML and Razor code, as the preceding example shows.</span></span>
* <span data-ttu-id="b6706-118">Eine oder mehrere Razor-Komponenten</span><span class="sxs-lookup"><span data-stu-id="b6706-118">One or more Razor components.</span></span>
* <span data-ttu-id="b6706-119">Eine Mischung aus HTML/Razor- und Razor-Komponenten.</span><span class="sxs-lookup"><span data-stu-id="b6706-119">A mix of HTML/Razor and Razor components.</span></span>

## <a name="item-provider-delegate"></a><span data-ttu-id="b6706-120">Elementanbieterdelegat</span><span class="sxs-lookup"><span data-stu-id="b6706-120">Item provider delegate</span></span>

<span data-ttu-id="b6706-121">Wenn Sie nicht alle Elemente in den Arbeitsspeicher laden möchten, können Sie eine Elementanbieter-Delegatmethode für den `ItemsProvider`-Parameter der Komponente festlegen, die die angeforderten Elemente bei Bedarf asynchron abruft:</span><span class="sxs-lookup"><span data-stu-id="b6706-121">If you don't want to load all of the items into memory, you can specify an items provider delegate method to the component's `ItemsProvider` parameter that asynchronously retrieves the requested items on demand:</span></span>

```razor
<Virtualize Context="employee" ItemsProvider="@LoadEmployees">
    <p>
        @employee.FirstName @employee.LastName has the 
        job title of @employee.JobTitle.
    </p>
</Virtualize>
```

<span data-ttu-id="b6706-122">Der Elementanbieter empfängt eine `ItemsProviderRequest`, die die erforderliche Anzahl von Elementen angibt, beginnend an einem bestimmten Startindex.</span><span class="sxs-lookup"><span data-stu-id="b6706-122">The items provider receives an `ItemsProviderRequest`, which specifies the required number of items starting at a specific start index.</span></span> <span data-ttu-id="b6706-123">Der Elementanbieter ruft dann die angeforderten Elemente aus einer Datenbank oder einem anderen Dienst ab und gibt Sie als `ItemsProviderResult<TItem>` zusammen mit der Gesamtzahl der Elemente zurück.</span><span class="sxs-lookup"><span data-stu-id="b6706-123">The items provider then retrieves the requested items from a database or other service and returns them as an `ItemsProviderResult<TItem>` along with a count of the total items.</span></span> <span data-ttu-id="b6706-124">Der Elementanbieter kann auswählen, ob die Elemente mit jeder Anforderung abgerufen oder zwischengespeichert werden, sodass Sie sofort verfügbar sind.</span><span class="sxs-lookup"><span data-stu-id="b6706-124">The items provider can choose to retrieve the items with each request or cache them so that they're readily available.</span></span>

<span data-ttu-id="b6706-125">Eine `Virtualize`-Komponente kann nur **eine Elementquelle** aus ihren Parametern akzeptieren. Versuchen Sie daher nicht, gleichzeitig einen Anbieter eines Elements zu verwenden und `Items`eine Sammlung zuzuweisen.</span><span class="sxs-lookup"><span data-stu-id="b6706-125">A `Virtualize` component can only accept **one item source** from its parameters, so don't attempt to simultaneously use an items provider and assign a collection to `Items`.</span></span> <span data-ttu-id="b6706-126">Wenn beide zugewiesen sind, wird eine <xref:System.InvalidOperationException> ausgelöst, wenn die Parameter der Komponente zur Laufzeit festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="b6706-126">If both are assigned, an <xref:System.InvalidOperationException> is thrown when the component's parameters are set at runtime.</span></span>

<span data-ttu-id="b6706-127">Im folgenden Beispiel werden Mitarbeiter aus einem `EmployeeService` geladen:</span><span class="sxs-lookup"><span data-stu-id="b6706-127">The following example loads employees from an `EmployeeService`:</span></span>

```csharp
private async ValueTask<ItemsProviderResult<Employee>> LoadEmployees(
    ItemsProviderRequest request)
{
    var numEmployees = Math.Min(request.Count, totalEmployees - request.StartIndex);
    var employees = await EmployeesService.GetEmployeesAsync(request.StartIndex, 
        numEmployees, request.CancellationToken);

    return new ItemsProviderResult<Employee>(employees, totalEmployees);
}
```

## <a name="placeholder"></a><span data-ttu-id="b6706-128">Platzhalter</span><span class="sxs-lookup"><span data-stu-id="b6706-128">Placeholder</span></span>

<span data-ttu-id="b6706-129">Da das Anfordern von Elementen aus einer Remotedatenquelle einige Zeit in Anspruch nehmen kann, haben Sie die Möglichkeit, einen Platzhalter (`<Placeholder>...</Placeholder>`) zu rendern, bis die Elementdaten verfügbar sind:</span><span class="sxs-lookup"><span data-stu-id="b6706-129">Because requesting items from a remote data source might take some time, you have the option to render a placeholder (`<Placeholder>...</Placeholder>`) until the item data is available:</span></span>

```razor
<Virtualize Context="employee" ItemsProvider="@LoadEmployees">
    <ItemContent>
        <p>
            @employee.FirstName @employee.LastName has the 
            job title of @employee.JobTitle.
        </p>
    </ItemContent>
    <Placeholder>
        <p>
            Loading&hellip;
        </p>
    </Placeholder>
</Virtualize>
```

## <a name="item-size"></a><span data-ttu-id="b6706-130">Elementgröße</span><span class="sxs-lookup"><span data-stu-id="b6706-130">Item size</span></span>

<span data-ttu-id="b6706-131">Die Größe jedes Elements in Pixeln kann mit `ItemSize` festgelegt werden (Standardwert: 50px):</span><span class="sxs-lookup"><span data-stu-id="b6706-131">The size of each item in pixels can be set with `ItemSize` (default: 50px):</span></span>

```razor
<Virtualize Context="employee" Items="@employees" ItemSize="25">
    ...
</Virtualize>
```

## <a name="overscan-count"></a><span data-ttu-id="b6706-132">Overscananzahl</span><span class="sxs-lookup"><span data-stu-id="b6706-132">Overscan count</span></span>

<span data-ttu-id="b6706-133">`OverscanCount` bestimmt, wie viele zusätzliche Elemente vor und nach dem sichtbaren Bereich gerendert werden.</span><span class="sxs-lookup"><span data-stu-id="b6706-133">`OverscanCount` determines how many additional items are rendered before and after the visible region.</span></span> <span data-ttu-id="b6706-134">Diese Einstellung trägt dazu bei, die Häufigkeit des Renderns beim Scrollen zu verringern.</span><span class="sxs-lookup"><span data-stu-id="b6706-134">This setting helps to reduce the frequency of rendering during scrolling.</span></span> <span data-ttu-id="b6706-135">Höhere Werte führen jedoch dazu, dass mehr Elemente auf der Seite gerendert werden (Standard: 3):</span><span class="sxs-lookup"><span data-stu-id="b6706-135">However, higher values result in more elements rendered in the page (default: 3):</span></span>

```razor
<Virtualize Context="employee" Items="@employees" OverscanCount="4">
    ...
</Virtualize>
```

## <a name="state-changes"></a><span data-ttu-id="b6706-136">Statusänderungen</span><span class="sxs-lookup"><span data-stu-id="b6706-136">State changes</span></span>

<span data-ttu-id="b6706-137">Wenn Sie Änderungen an Elementen vornehmen, die von der `Virtualize`-Komponente gerendert werden, rufen Sie <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> auf, um die erneute Auswertung und das erneute Rendern der Komponente zu erzwingen.</span><span class="sxs-lookup"><span data-stu-id="b6706-137">When making changes to items rendered by the `Virtualize` component, call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> to force re-evaluation and rerendering of the component.</span></span>
