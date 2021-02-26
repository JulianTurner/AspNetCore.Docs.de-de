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
ms.openlocfilehash: d9fc767a4b5160c616053b075ba92194bcffa275
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280017"
---
# <a name="aspnet-core-blazor-component-virtualization"></a><span data-ttu-id="dfe5c-103">ASP.NET Core Blazor-Komponentenvirtualisierung</span><span class="sxs-lookup"><span data-stu-id="dfe5c-103">ASP.NET Core Blazor component virtualization</span></span>

<span data-ttu-id="dfe5c-104">Verbessern Sie die wahrgenommene Leistung von Komponentenrendering mithilfe der integrierten Virtualisierungsunterstützung des Blazor-Frameworks.</span><span class="sxs-lookup"><span data-stu-id="dfe5c-104">Improve the perceived performance of component rendering using the Blazor framework's built-in virtualization support.</span></span> <span data-ttu-id="dfe5c-105">Virtualisierung ist eine Technik zum Einschränken des Benutzeroberflächenrenderings auf die aktuell sichtbaren Elemente.</span><span class="sxs-lookup"><span data-stu-id="dfe5c-105">Virtualization is a technique for limiting UI rendering to just the parts that are currently visible.</span></span> <span data-ttu-id="dfe5c-106">Die Virtualisierung ist beispielsweise hilfreich, wenn die App eine lange Liste von Elementen rendern und nur eine Teilmenge der Elemente zu einem bestimmten Zeitpunkt sichtbar sein muss.</span><span class="sxs-lookup"><span data-stu-id="dfe5c-106">For example, virtualization is helpful when the app must render a long list of items and only a subset of items is required to be visible at any given time.</span></span> <span data-ttu-id="dfe5c-107">Blazor stellt die [`Virtualize`-Komponente](xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601) bereit, die zum Hinzufügen der Virtualisierung zu den Komponenten einer App verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="dfe5c-107">Blazor provides the [`Virtualize` component](xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601) that can be used to add virtualization to an app's components.</span></span>

<span data-ttu-id="dfe5c-108">Die `Virtualize`-Komponente kann in folgenden Fällen verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="dfe5c-108">The `Virtualize` component can be used when:</span></span>

* <span data-ttu-id="dfe5c-109">Rendern eines Satzes von Datenelementen in einer Schleife.</span><span class="sxs-lookup"><span data-stu-id="dfe5c-109">Rendering a set of data items in a loop.</span></span>
* <span data-ttu-id="dfe5c-110">Die meisten Elemente sind beim Scrollen nicht sichtbar.</span><span class="sxs-lookup"><span data-stu-id="dfe5c-110">Most of the items aren't visible due to scrolling.</span></span>
* <span data-ttu-id="dfe5c-111">Die gerenderten Elemente haben exakt die gleiche Größe.</span><span class="sxs-lookup"><span data-stu-id="dfe5c-111">The rendered items are exactly the same size.</span></span> <span data-ttu-id="dfe5c-112">Wenn der Benutzer zu einem beliebigen Punkt scrollt, kann die Komponente die anzuzeigenden sichtbaren Elemente berechnen.</span><span class="sxs-lookup"><span data-stu-id="dfe5c-112">When the user scrolls to an arbitrary point, the component can calculate the visible items to show.</span></span>

<span data-ttu-id="dfe5c-113">Ohne Virtualisierung kann eine typische Liste eine C#-[`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in)-Schleife verwenden, um jedes Element in der Liste zu rendern:</span><span class="sxs-lookup"><span data-stu-id="dfe5c-113">Without virtualization, a typical list might use a C# [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) loop to render each item in the list:</span></span>

```razor
<div style="height:500px;overflow-y:scroll">
    @foreach (var flight in allFlights)
    {
        <FlightSummary @key="flight.FlightId" Details="@flight.Summary" />
    }
</div>
```

<span data-ttu-id="dfe5c-114">Wenn die Liste Tausende von Elementen enthält, kann das Rendern der Liste viel Zeit in Anspruch nehmen.</span><span class="sxs-lookup"><span data-stu-id="dfe5c-114">If the list contains thousands of items, then rendering the list may take a long time.</span></span> <span data-ttu-id="dfe5c-115">Der Benutzer kann ggf. eine merkliche Verzögerung der Benutzeroberfläche wahrnehmen.</span><span class="sxs-lookup"><span data-stu-id="dfe5c-115">The user may experience a noticeable UI lag.</span></span>

<span data-ttu-id="dfe5c-116">Anstatt jedes Element in der Liste gleichzeitig zu rendern, ersetzen Sie die [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in)-Schleife durch die `Virtualize`-Komponente, und geben Sie eine feste Elementquelle mit <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Items%2A?displayProperty=nameWithType> an.</span><span class="sxs-lookup"><span data-stu-id="dfe5c-116">Instead of rendering each item in the list all at one time, replace the [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) loop with the `Virtualize` component and specify a fixed item source with <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Items%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="dfe5c-117">Nur die aktuell sichtbaren Elemente werden gerendert:</span><span class="sxs-lookup"><span data-stu-id="dfe5c-117">Only the items that are currently visible are rendered:</span></span>

```razor
<div style="height:500px;overflow-y:scroll">
    <Virtualize Items="@allFlights" Context="flight">
        <FlightSummary @key="flight.FlightId" Details="@flight.Summary" />
    </Virtualize>
</div>
```

<span data-ttu-id="dfe5c-118">Wenn Sie mit `Context` keinen Kontext für die Komponente angeben, verwenden Sie den `context`-Wert in der Elementinhaltsvorlage:</span><span class="sxs-lookup"><span data-stu-id="dfe5c-118">If not specifying a context to the component with `Context`, use the `context` value in the item content template:</span></span>

```razor
<div style="height:500px;overflow-y:scroll">
    <Virtualize Items="@allFlights">
        <FlightSummary @key="context.FlightId" Details="@context.Summary" />
    </Virtualize>
</div>
```

> [!NOTE]
> <span data-ttu-id="dfe5c-119">Der Prozess für die Zuordnung von Modellobjekten zu Elementen und Komponenten kann mit dem Anweisungsattribut [`@key`](xref:mvc/views/razor#key) gesteuert werden.</span><span class="sxs-lookup"><span data-stu-id="dfe5c-119">The mapping process of model objects to elements and components can be controlled with the [`@key`](xref:mvc/views/razor#key) directive attribute.</span></span> <span data-ttu-id="dfe5c-120">`@key` bewirkt, dass der Vergleichsalgorithmus die Beibehaltung von Elementen oder Komponenten basierend auf dem Wert des Schlüssels garantiert.</span><span class="sxs-lookup"><span data-stu-id="dfe5c-120">`@key` causes the diffing algorithm to guarantee preservation of elements or components based on the key's value.</span></span>
>
> <span data-ttu-id="dfe5c-121">Weitere Informationen finden Sie in den folgenden Artikeln:</span><span class="sxs-lookup"><span data-stu-id="dfe5c-121">For more information, see the following articles:</span></span>
>
> * <xref:blazor/components/index#use-key-to-control-the-preservation-of-elements-and-components>
> * [<span data-ttu-id="dfe5c-122">Razor-Syntaxreferenz für ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="dfe5c-122">Razor syntax reference for ASP.NET Core</span></span>](xref:mvc/views/razor#key)

<span data-ttu-id="dfe5c-123">Die Komponente `Virtualize`:</span><span class="sxs-lookup"><span data-stu-id="dfe5c-123">The `Virtualize` component:</span></span>

* <span data-ttu-id="dfe5c-124">berechnet, wie viele Elemente basierend auf der Größe des Containers und der gerenderten Elemente gerendert werden sollen.</span><span class="sxs-lookup"><span data-stu-id="dfe5c-124">Calculates how many items to render based on the height of the container and the size of the rendered items.</span></span>
* <span data-ttu-id="dfe5c-125">führt neue Berechnungen durch, während der Benutzer scrollt, und rendert die Elemente noch mal.</span><span class="sxs-lookup"><span data-stu-id="dfe5c-125">Recalculates and rerenders items as the user scrolls.</span></span>
* <span data-ttu-id="dfe5c-126">ruft nur das Segment der Datensätze einer externen API ab, das dem aktuell sichtbaren Bereich entspricht, anstatt alle Daten aus der Sammlung herunterzuladen.</span><span class="sxs-lookup"><span data-stu-id="dfe5c-126">Only fetches the slice of records from an external API that correspond to the current visible region, instead of downloading all of the data from the collection.</span></span>

<span data-ttu-id="dfe5c-127">Der Elementinhalt für die `Virtualize`-Komponente kann Folgendes umfassen:</span><span class="sxs-lookup"><span data-stu-id="dfe5c-127">The item content for the `Virtualize` component can include:</span></span>

* <span data-ttu-id="dfe5c-128">Einfachen HTML- und Razor-Code, wie im vorherigen Beispiel gezeigt</span><span class="sxs-lookup"><span data-stu-id="dfe5c-128">Plain HTML and Razor code, as the preceding example shows.</span></span>
* <span data-ttu-id="dfe5c-129">Eine oder mehrere Razor-Komponenten</span><span class="sxs-lookup"><span data-stu-id="dfe5c-129">One or more Razor components.</span></span>
* <span data-ttu-id="dfe5c-130">Eine Mischung aus HTML/Razor- und Razor-Komponenten.</span><span class="sxs-lookup"><span data-stu-id="dfe5c-130">A mix of HTML/Razor and Razor components.</span></span>

## <a name="item-provider-delegate"></a><span data-ttu-id="dfe5c-131">Elementanbieterdelegat</span><span class="sxs-lookup"><span data-stu-id="dfe5c-131">Item provider delegate</span></span>

<span data-ttu-id="dfe5c-132">Wenn Sie nicht alle Elemente in den Arbeitsspeicher laden möchten, können Sie eine Elementanbieter-Delegatmethode für den <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemsProvider%2A?displayProperty=nameWithType>-Parameter der Komponente festlegen, die die angeforderten Elemente bei Bedarf asynchron abruft.</span><span class="sxs-lookup"><span data-stu-id="dfe5c-132">If you don't want to load all of the items into memory, you can specify an items provider delegate method to the component's <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemsProvider%2A?displayProperty=nameWithType> parameter that asynchronously retrieves the requested items on demand.</span></span> <span data-ttu-id="dfe5c-133">Im folgenden Beispiel stellt die Methode `LoadEmployees` die Elemente für die Komponente `Virtualize` bereit:</span><span class="sxs-lookup"><span data-stu-id="dfe5c-133">In the following example, the `LoadEmployees` method provides the items to the `Virtualize` component:</span></span>

```razor
<Virtualize Context="employee" ItemsProvider="@LoadEmployees">
    <p>
        @employee.FirstName @employee.LastName has the 
        job title of @employee.JobTitle.
    </p>
</Virtualize>
```

<span data-ttu-id="dfe5c-134">Der Elementanbieter empfängt eine <xref:Microsoft.AspNetCore.Components.Web.Virtualization.ItemsProviderRequest>, die die erforderliche Anzahl von Elementen angibt, beginnend an einem bestimmten Startindex.</span><span class="sxs-lookup"><span data-stu-id="dfe5c-134">The items provider receives an <xref:Microsoft.AspNetCore.Components.Web.Virtualization.ItemsProviderRequest>, which specifies the required number of items starting at a specific start index.</span></span> <span data-ttu-id="dfe5c-135">Der Elementanbieter ruft dann die angeforderten Elemente aus einer Datenbank oder einem anderen Dienst ab und gibt Sie als <xref:Microsoft.AspNetCore.Components.Web.Virtualization.ItemsProviderResult%601> zusammen mit der Gesamtzahl der Elemente zurück.</span><span class="sxs-lookup"><span data-stu-id="dfe5c-135">The items provider then retrieves the requested items from a database or other service and returns them as an <xref:Microsoft.AspNetCore.Components.Web.Virtualization.ItemsProviderResult%601> along with a count of the total items.</span></span> <span data-ttu-id="dfe5c-136">Der Elementanbieter kann auswählen, ob die Elemente mit jeder Anforderung abgerufen oder zwischengespeichert werden, sodass Sie sofort verfügbar sind.</span><span class="sxs-lookup"><span data-stu-id="dfe5c-136">The items provider can choose to retrieve the items with each request or cache them so that they're readily available.</span></span>

<span data-ttu-id="dfe5c-137">Eine `Virtualize`-Komponente kann nur **eine Elementquelle** aus ihren Parametern akzeptieren. Versuchen Sie daher nicht, gleichzeitig einen Anbieter eines Elements zu verwenden und `Items`eine Sammlung zuzuweisen.</span><span class="sxs-lookup"><span data-stu-id="dfe5c-137">A `Virtualize` component can only accept **one item source** from its parameters, so don't attempt to simultaneously use an items provider and assign a collection to `Items`.</span></span> <span data-ttu-id="dfe5c-138">Wenn beide zugewiesen sind, wird eine <xref:System.InvalidOperationException> ausgelöst, wenn die Parameter der Komponente zur Laufzeit festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="dfe5c-138">If both are assigned, an <xref:System.InvalidOperationException> is thrown when the component's parameters are set at runtime.</span></span>

<span data-ttu-id="dfe5c-139">Im folgenden Beispiel lädt die Methode `LoadEmployees` die Mitarbeiter aus einem `EmployeeService`-Element (das nicht gezeigt wird):</span><span class="sxs-lookup"><span data-stu-id="dfe5c-139">The following `LoadEmployees` method example loads employees from an `EmployeeService` (not shown):</span></span>

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

<span data-ttu-id="dfe5c-140"><xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.RefreshDataAsync%2A?displayProperty=nameWithType> weist die Komponente an, Daten noch mal von <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemsProvider%2A> anzufordern.</span><span class="sxs-lookup"><span data-stu-id="dfe5c-140"><xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.RefreshDataAsync%2A?displayProperty=nameWithType> instructs the component to rerequest data from its <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemsProvider%2A>.</span></span> <span data-ttu-id="dfe5c-141">Dies ist nützlich, wenn sich externe Daten ändern.</span><span class="sxs-lookup"><span data-stu-id="dfe5c-141">This is useful when external data changes.</span></span> <span data-ttu-id="dfe5c-142">Bei Verwendung von <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Items%2A> muss dies nicht aufgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="dfe5c-142">There's no need to call this when using <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Items%2A>.</span></span>

## <a name="placeholder"></a><span data-ttu-id="dfe5c-143">Platzhalter</span><span class="sxs-lookup"><span data-stu-id="dfe5c-143">Placeholder</span></span>

<span data-ttu-id="dfe5c-144">Da das Anfordern von Elementen von einer Remotedatenquelle etwas Zeit in Anspruch nehmen kann, verfügen Sie über die Option, einen Platzhalter mit Elementinhalt zu rendern:</span><span class="sxs-lookup"><span data-stu-id="dfe5c-144">Because requesting items from a remote data source might take some time, you have the option to render a placeholder with item content:</span></span>

* <span data-ttu-id="dfe5c-145">Verwenden Sie <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Placeholder%2A> (`<Placeholder>...</Placeholder>`), um Inhalt anzuzeigen, bis die Elementdaten verfügbar sind.</span><span class="sxs-lookup"><span data-stu-id="dfe5c-145">Use a <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Placeholder%2A> (`<Placeholder>...</Placeholder>`) to display content until the item data is available.</span></span>
* <span data-ttu-id="dfe5c-146">Verwenden Sie <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemContent%2A?displayProperty=nameWithType> zum Festlegen der Elementvorlage für die Liste.</span><span class="sxs-lookup"><span data-stu-id="dfe5c-146">Use <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemContent%2A?displayProperty=nameWithType> to set the item template for the list.</span></span>

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

## <a name="item-size"></a><span data-ttu-id="dfe5c-147">Elementgröße</span><span class="sxs-lookup"><span data-stu-id="dfe5c-147">Item size</span></span>

<span data-ttu-id="dfe5c-148">Die Höhe jedes Elements in Pixeln kann mit <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemSize%2A?displayProperty=nameWithType> festgelegt werden (Standardwert: 50):</span><span class="sxs-lookup"><span data-stu-id="dfe5c-148">The height of each item in pixels can be set with <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemSize%2A?displayProperty=nameWithType> (default: 50):</span></span>

```razor
<Virtualize Context="employee" Items="@employees" ItemSize="25">
    ...
</Virtualize>
```

<span data-ttu-id="dfe5c-149">Die Komponente `Virtualize` misst die tatsächliche Renderinggröße standardmäßig *nach* dem ersten Rendering.</span><span class="sxs-lookup"><span data-stu-id="dfe5c-149">By default, the `Virtualize` component measures the actual rendering size *after* the initial render occurs.</span></span> <span data-ttu-id="dfe5c-150">Verwenden Sie <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemSize%2A>, um die genaue Elementgröße im Voraus anzugeben, um die Leistung beim ersten Rendering zu verbessern und die richtige Scrollposition für das erneute Laden von Webseiten sicherzustellen.</span><span class="sxs-lookup"><span data-stu-id="dfe5c-150">Use <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemSize%2A> to provide an exact item size in advance to assist with accurate initial render performance and to ensure the correct scroll position for page reloads.</span></span> <span data-ttu-id="dfe5c-151">Wenn die Standardvorgehensweise <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemSize%2A> zur Folge hat, dass einige Elemente außerhalb der sichtbaren Ansicht gerendert werden, wird ein zweites Rendering ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="dfe5c-151">If the default <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemSize%2A> causes some items to render outside of the currently visible view, a second re-render is triggered.</span></span> <span data-ttu-id="dfe5c-152">Damit die Scrollposition des Browsers in einer virtualisierten Liste korrekt beibehalten wird, muss das erste Rendering korrekt sein.</span><span class="sxs-lookup"><span data-stu-id="dfe5c-152">To correctly maintain the browser's scroll position in a virtualized list, the initial render must be correct.</span></span> <span data-ttu-id="dfe5c-153">Andernfalls werden Benutzern ggf. falsche Elemente angezeigt.</span><span class="sxs-lookup"><span data-stu-id="dfe5c-153">If not, users might view the wrong items.</span></span> 

## <a name="overscan-count"></a><span data-ttu-id="dfe5c-154">Overscananzahl</span><span class="sxs-lookup"><span data-stu-id="dfe5c-154">Overscan count</span></span>

<span data-ttu-id="dfe5c-155"><xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.OverscanCount%2A?displayProperty=nameWithType> bestimmt, wie viele zusätzliche Elemente vor und nach dem sichtbaren Bereich gerendert werden.</span><span class="sxs-lookup"><span data-stu-id="dfe5c-155"><xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.OverscanCount%2A?displayProperty=nameWithType> determines how many additional items are rendered before and after the visible region.</span></span> <span data-ttu-id="dfe5c-156">Diese Einstellung trägt dazu bei, die Häufigkeit des Renderns beim Scrollen zu verringern.</span><span class="sxs-lookup"><span data-stu-id="dfe5c-156">This setting helps to reduce the frequency of rendering during scrolling.</span></span> <span data-ttu-id="dfe5c-157">Höhere Werte führen jedoch dazu, dass mehr Elemente auf der Seite gerendert werden (Standard: 3):</span><span class="sxs-lookup"><span data-stu-id="dfe5c-157">However, higher values result in more elements rendered in the page (default: 3):</span></span>

```razor
<Virtualize Context="employee" Items="@employees" OverscanCount="4">
    ...
</Virtualize>
```

## <a name="state-changes"></a><span data-ttu-id="dfe5c-158">Statusänderungen</span><span class="sxs-lookup"><span data-stu-id="dfe5c-158">State changes</span></span>

<span data-ttu-id="dfe5c-159">Wenn Sie Änderungen an Elementen vornehmen, die von der `Virtualize`-Komponente gerendert werden, rufen Sie <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> auf, um die erneute Auswertung und das erneute Rendern der Komponente zu erzwingen.</span><span class="sxs-lookup"><span data-stu-id="dfe5c-159">When making changes to items rendered by the `Virtualize` component, call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> to force re-evaluation and rerendering of the component.</span></span> <span data-ttu-id="dfe5c-160">Weitere Informationen finden Sie unter <xref:blazor/components/rendering>.</span><span class="sxs-lookup"><span data-stu-id="dfe5c-160">For more information, see <xref:blazor/components/rendering>.</span></span>
