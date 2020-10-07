---
title: ASP.NET Core Blazor-Komponentenvirtualisierung
author: guardrex
description: Erfahren Sie, Komponentenvirtualisierung in ASP.NET Core Blazor-Apps verwendet wird.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/22/2020
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
uid: blazor/components/virtualization
ms.openlocfilehash: 9c3e53bee7535b36bba3474ff50a881568bbd690
ms.sourcegitcommit: 74f4a4ddbe3c2f11e2e09d05d2a979784d89d3f5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/27/2020
ms.locfileid: "91393807"
---
# <a name="aspnet-core-no-locblazor-component-virtualization"></a><span data-ttu-id="17f64-103">ASP.NET Core Blazor-Komponentenvirtualisierung</span><span class="sxs-lookup"><span data-stu-id="17f64-103">ASP.NET Core Blazor component virtualization</span></span>

<span data-ttu-id="17f64-104">Von [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="17f64-104">By [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="17f64-105">Verbessern Sie die wahrgenommene Leistung von Komponentenrendering mithilfe der integrierten Virtualisierungsunterstützung des Blazor-Frameworks.</span><span class="sxs-lookup"><span data-stu-id="17f64-105">Improve the perceived performance of component rendering using the Blazor framework's built-in virtualization support.</span></span> <span data-ttu-id="17f64-106">Virtualisierung ist eine Technik zum Einschränken des Benutzeroberflächenrenderings auf die aktuell sichtbaren Elemente.</span><span class="sxs-lookup"><span data-stu-id="17f64-106">Virtualization is a technique for limiting UI rendering to just the parts that are currently visible.</span></span> <span data-ttu-id="17f64-107">Die Virtualisierung ist beispielsweise hilfreich, wenn die App eine lange Liste von Elementen rendern und nur eine Teilmenge der Elemente zu einem bestimmten Zeitpunkt sichtbar sein muss.</span><span class="sxs-lookup"><span data-stu-id="17f64-107">For example, virtualization is helpful when the app must render a long list of items and only a subset of items is required to be visible at any given time.</span></span> <span data-ttu-id="17f64-108">Blazor stellt die `Virtualize`-Komponente bereit, die verwendet werden kann, um den Komponenten einer App Virtualisierung hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="17f64-108">Blazor provides the `Virtualize` component that can be used to add virtualization to an app's components.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="17f64-109">Ohne Virtualisierung kann eine typische Liste eine C#-[`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in)-Schleife verwenden, um jedes Element in der Liste zu rendern:</span><span class="sxs-lookup"><span data-stu-id="17f64-109">Without virtualization, a typical list might use a C# [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) loop to render each item in the list:</span></span>

```razor
@foreach (var employee in employees)
{
    <p>
        @employee.FirstName @employee.LastName has the 
        job title of @employee.JobTitle.
    </p>
}
```

<span data-ttu-id="17f64-110">Wenn die Liste Tausende von Elementen enthält, kann das Rendern der Liste viel Zeit in Anspruch nehmen.</span><span class="sxs-lookup"><span data-stu-id="17f64-110">If the list contains thousands of items, then rendering the list may take a long time.</span></span> <span data-ttu-id="17f64-111">Der Benutzer kann ggf. eine merkliche Verzögerung der Benutzeroberfläche wahrnehmen.</span><span class="sxs-lookup"><span data-stu-id="17f64-111">The user may experience a noticeable UI lag.</span></span>

<span data-ttu-id="17f64-112">Anstatt jedes Element in der Liste gleichzeitig zu rendern, ersetzen Sie die [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in)-Schleife durch die `Virtualize`-Komponente, und geben Sie eine feste Elementquelle mit `Items` an.</span><span class="sxs-lookup"><span data-stu-id="17f64-112">Instead of rendering each item in the list all at one time, replace the [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) loop with the `Virtualize` component and specify a fixed item source with `Items`.</span></span> <span data-ttu-id="17f64-113">Nur die aktuell sichtbaren Elemente werden gerendert:</span><span class="sxs-lookup"><span data-stu-id="17f64-113">Only the items that are currently visible are rendered:</span></span>

```razor
<Virtualize Context="employee" Items="@employees">
    <p>
        @employee.FirstName @employee.LastName has the 
        job title of @employee.JobTitle.
    </p>
</Virtualize>
```

<span data-ttu-id="17f64-114">Wenn Sie mit `Context` keinen Kontext für die Komponente angeben, verwenden Sie den `context`-Wert (`@context.{PROPERTY}`) in der Elementinhaltsvorlage:</span><span class="sxs-lookup"><span data-stu-id="17f64-114">If not specifying a context to the component with `Context`, use the `context` value (`@context.{PROPERTY}`) in the item content template:</span></span>

```razor
<Virtualize Items="@employees">
    <p>
        @context.FirstName @context.LastName has the 
        job title of @context.JobTitle.
    </p>
</Virtualize>
```

<span data-ttu-id="17f64-115">Die `Virtualize`-Komponente berechnet, wie viele Elemente basierend auf der Höhe des Containers und der Größe der gerenderten Elemente gerendert werden.</span><span class="sxs-lookup"><span data-stu-id="17f64-115">The `Virtualize` component calculates how many items to render based on the height of the container and the size of the rendered items.</span></span>

<span data-ttu-id="17f64-116">Der Elementinhalt für die `Virtualize`-Komponente kann Folgendes umfassen:</span><span class="sxs-lookup"><span data-stu-id="17f64-116">The item content for the `Virtualize` component can include:</span></span>

* <span data-ttu-id="17f64-117">Einfachen HTML- und Razor-Code, wie im vorherigen Beispiel gezeigt</span><span class="sxs-lookup"><span data-stu-id="17f64-117">Plain HTML and Razor code, as the preceding example shows.</span></span>
* <span data-ttu-id="17f64-118">Eine oder mehrere Razor-Komponenten</span><span class="sxs-lookup"><span data-stu-id="17f64-118">One or more Razor components.</span></span>
* <span data-ttu-id="17f64-119">Eine Mischung aus HTML/Razor- und Razor-Komponenten.</span><span class="sxs-lookup"><span data-stu-id="17f64-119">A mix of HTML/Razor and Razor components.</span></span>

## <a name="item-provider-delegate"></a><span data-ttu-id="17f64-120">Elementanbieterdelegat</span><span class="sxs-lookup"><span data-stu-id="17f64-120">Item provider delegate</span></span>

<span data-ttu-id="17f64-121">Wenn Sie nicht alle Elemente in den Arbeitsspeicher laden möchten, können Sie eine Elementanbieter-Delegatmethode für den `ItemsProvider`-Parameter der Komponente festlegen, die die angeforderten Elemente bei Bedarf asynchron abruft:</span><span class="sxs-lookup"><span data-stu-id="17f64-121">If you don't want to load all of the items into memory, you can specify an items provider delegate method to the component's `ItemsProvider` parameter that asynchronously retrieves the requested items on demand:</span></span>

```razor
<Virtualize Context="employee" ItemsProvider="@LoadEmployees">
    <p>
        @employee.FirstName @employee.LastName has the 
        job title of @employee.JobTitle.
    </p>
</Virtualize>
```

<span data-ttu-id="17f64-122">Der Elementanbieter empfängt eine `ItemsProviderRequest`, die die erforderliche Anzahl von Elementen angibt, beginnend an einem bestimmten Startindex.</span><span class="sxs-lookup"><span data-stu-id="17f64-122">The items provider receives an `ItemsProviderRequest`, which specifies the required number of items starting at a specific start index.</span></span> <span data-ttu-id="17f64-123">Der Elementanbieter ruft dann die angeforderten Elemente aus einer Datenbank oder einem anderen Dienst ab und gibt Sie als `ItemsProviderResult<TItem>` zusammen mit der Gesamtzahl der Elemente zurück.</span><span class="sxs-lookup"><span data-stu-id="17f64-123">The items provider then retrieves the requested items from a database or other service and returns them as an `ItemsProviderResult<TItem>` along with a count of the total items.</span></span> <span data-ttu-id="17f64-124">Der Elementanbieter kann auswählen, ob die Elemente mit jeder Anforderung abgerufen oder zwischengespeichert werden, sodass Sie sofort verfügbar sind.</span><span class="sxs-lookup"><span data-stu-id="17f64-124">The items provider can choose to retrieve the items with each request or cache them so that they're readily available.</span></span> <span data-ttu-id="17f64-125">Versuchen Sie nicht, einen Elementanbieter zu verwenden, und weisen Sie `Items` für dieselbe `Virtualize`-Komponente eine Sammlung zu.</span><span class="sxs-lookup"><span data-stu-id="17f64-125">Don't attempt to use an items provider and assign a collection to `Items` for the same `Virtualize` component.</span></span>

<span data-ttu-id="17f64-126">Im folgenden Beispiel werden Mitarbeiter aus einem `EmployeeService` geladen:</span><span class="sxs-lookup"><span data-stu-id="17f64-126">The following example loads employees from an `EmployeeService`:</span></span>

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

## <a name="placeholder"></a><span data-ttu-id="17f64-127">Platzhalter</span><span class="sxs-lookup"><span data-stu-id="17f64-127">Placeholder</span></span>

<span data-ttu-id="17f64-128">Da das Anfordern von Elementen aus einer Remotedatenquelle einige Zeit in Anspruch nehmen kann, haben Sie die Möglichkeit, einen Platzhalter (`<Placeholder>...</Placeholder>`) zu rendern, bis die Elementdaten verfügbar sind:</span><span class="sxs-lookup"><span data-stu-id="17f64-128">Because requesting items from a remote data source might take some time, you have the option to render a placeholder (`<Placeholder>...</Placeholder>`) until the item data is available:</span></span>

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

## <a name="item-size"></a><span data-ttu-id="17f64-129">Elementgröße</span><span class="sxs-lookup"><span data-stu-id="17f64-129">Item size</span></span>

<span data-ttu-id="17f64-130">Die Größe jedes Elements in Pixeln kann mit `ItemSize` festgelegt werden (Standardwert: 50px):</span><span class="sxs-lookup"><span data-stu-id="17f64-130">The size of each item in pixels can be set with `ItemSize` (default: 50px):</span></span>

```razor
<Virtualize Context="employee" Items="@employees" ItemSize="25">
    ...
</Virtualize>
```

## <a name="overscan-count"></a><span data-ttu-id="17f64-131">Overscananzahl</span><span class="sxs-lookup"><span data-stu-id="17f64-131">Overscan count</span></span>

<span data-ttu-id="17f64-132">`OverscanCount` bestimmt, wie viele zusätzliche Elemente vor und nach dem sichtbaren Bereich gerendert werden.</span><span class="sxs-lookup"><span data-stu-id="17f64-132">`OverscanCount` determines how many additional items are rendered before and after the visible region.</span></span> <span data-ttu-id="17f64-133">Diese Einstellung trägt dazu bei, die Häufigkeit des Renderns beim Scrollen zu verringern.</span><span class="sxs-lookup"><span data-stu-id="17f64-133">This setting helps to reduce the frequency of rendering during scrolling.</span></span> <span data-ttu-id="17f64-134">Höhere Werte führen jedoch dazu, dass mehr Elemente auf der Seite gerendert werden (Standard: 3):</span><span class="sxs-lookup"><span data-stu-id="17f64-134">However, higher values result in more elements rendered in the page (default: 3):</span></span>

```razor
<Virtualize Context="employee" Items="@employees" OverscanCount="4">
    ...
</Virtualize>
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="17f64-135">Bei einem Raster oder einer Liste, die Hunderte Datensätze rendert, die Komponenten enthalten, ist die Auslastung des Prozessors für das Rendern beispielsweise höher.</span><span class="sxs-lookup"><span data-stu-id="17f64-135">For example, a grid or list that renders hundreds of rows containing components is processor intensive to render.</span></span> <span data-ttu-id="17f64-136">Ziehen Sie es in Betracht, ein Raster oder ein Listenlayout zu virtualisieren, sodass nur eine Teilmenge der Komponenten jeweils gleichzeitig gerendert wird.</span><span class="sxs-lookup"><span data-stu-id="17f64-136">Consider virtualizing a grid or list layout so that only a subset of the components is rendered at any given time.</span></span> <span data-ttu-id="17f64-137">Ein Beispiel für das Rendern einer Teilmenge von Komponenten finden Sie bei den folgenden Komponenten unter [`Virtualization`-Beispiel-App (aspnet/samples, GitHub-Repository)](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Virtualization):</span><span class="sxs-lookup"><span data-stu-id="17f64-137">For an example of component subset rendering, see the following components in the [`Virtualization` sample app (aspnet/samples GitHub repository)](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Virtualization):</span></span>

* <span data-ttu-id="17f64-138">`Virtualize`-Komponente ([`Shared/Virtualize.razor`](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Shared/Virtualize.cs)): Eine in C# geschriebene Komponente, die <xref:Microsoft.AspNetCore.Components.ComponentBase> implementiert, um mehrere Wetterdatensätze basierend auf dem Scrollen des Benutzers zu rendern</span><span class="sxs-lookup"><span data-stu-id="17f64-138">`Virtualize` component ([`Shared/Virtualize.razor`](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Shared/Virtualize.cs)): A component written in C# that implements <xref:Microsoft.AspNetCore.Components.ComponentBase> to render a set of weather data rows based on user scrolling.</span></span>
* <span data-ttu-id="17f64-139">`FetchData`-Komponente ([`Pages/FetchData.razor`](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Pages/FetchData.razor)): Verwendet die `Virtualize`-Komponente, um 25 Wetterdatensätze gleichzeitig anzuzeigen</span><span class="sxs-lookup"><span data-stu-id="17f64-139">`FetchData` component ([`Pages/FetchData.razor`](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Pages/FetchData.razor)): Uses the `Virtualize` component to display 25 rows of weather data at a time.</span></span>

::: moniker-end

## <a name="state-changes"></a><span data-ttu-id="17f64-140">Statusänderungen</span><span class="sxs-lookup"><span data-stu-id="17f64-140">State changes</span></span>

<span data-ttu-id="17f64-141">Wenn Sie Änderungen an Elementen vornehmen, die von der `Virtualize`-Komponente gerendert werden, rufen Sie <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> auf, um die erneute Auswertung und das erneute Rendern der Komponente zu erzwingen.</span><span class="sxs-lookup"><span data-stu-id="17f64-141">When making changes to items rendered by the `Virtualize` component, call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> to force re-evaluation and rerendering of the component.</span></span>
