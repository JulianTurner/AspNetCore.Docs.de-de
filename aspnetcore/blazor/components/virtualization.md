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
ms.openlocfilehash: afd2da19641b41871f06426934c39348daa54b1f
ms.sourcegitcommit: 2fea9bfe6127bbbdbb438406c82529b2bc331944
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/11/2021
ms.locfileid: "98065531"
---
# <a name="aspnet-core-no-locblazor-component-virtualization"></a>ASP.NET Core Blazor-Komponentenvirtualisierung

Von [Daniel Roth](https://github.com/danroth27)

Verbessern Sie die wahrgenommene Leistung von Komponentenrendering mithilfe der integrierten Virtualisierungsunterstützung des Blazor-Frameworks. Virtualisierung ist eine Technik zum Einschränken des Benutzeroberflächenrenderings auf die aktuell sichtbaren Elemente. Die Virtualisierung ist beispielsweise hilfreich, wenn die App eine lange Liste von Elementen rendern und nur eine Teilmenge der Elemente zu einem bestimmten Zeitpunkt sichtbar sein muss. Blazor stellt die [`Virtualize`-Komponente](xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601) bereit, die zum Hinzufügen der Virtualisierung zu den Komponenten einer App verwendet werden kann.

Die `Virtualize`-Komponente kann in folgenden Fällen verwendet werden:

* Rendern eines Satzes von Datenelementen in einer Schleife.
* Die meisten Elemente sind beim Scrollen nicht sichtbar.
* Die gerenderten Elemente haben exakt die gleiche Größe. Wenn der Benutzer zu einem beliebigen Punkt scrollt, kann die Komponente die anzuzeigenden sichtbaren Elemente berechnen.

Ohne Virtualisierung kann eine typische Liste eine C#-[`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in)-Schleife verwenden, um jedes Element in der Liste zu rendern:

```razor
<div style="height:500px;overflow-y:scroll">
    @foreach (var flight in allFlights)
    {
        <FlightSummary @key="flight.FlightId" Details="@flight.Summary" />
    }
</div>
```

Wenn die Liste Tausende von Elementen enthält, kann das Rendern der Liste viel Zeit in Anspruch nehmen. Der Benutzer kann ggf. eine merkliche Verzögerung der Benutzeroberfläche wahrnehmen.

Anstatt jedes Element in der Liste gleichzeitig zu rendern, ersetzen Sie die [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in)-Schleife durch die `Virtualize`-Komponente, und geben Sie eine feste Elementquelle mit <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Items%2A?displayProperty=nameWithType> an. Nur die aktuell sichtbaren Elemente werden gerendert:

```razor
<div style="height:500px;overflow-y:scroll">
    <Virtualize Items="@allFlights" Context="flight">
        <FlightSummary @key="flight.FlightId" Details="@flight.Summary" />
    </Virtualize>
</div>
```

Wenn Sie mit `Context` keinen Kontext für die Komponente angeben, verwenden Sie den `context`-Wert in der Elementinhaltsvorlage:

```razor
<div style="height:500px;overflow-y:scroll">
    <Virtualize Items="@allFlights">
        <FlightSummary @key="context.FlightId" Details="@context.Summary" />
    </Virtualize>
</div>
```

> [!NOTE]
> Der Prozess für die Zuordnung von Modellobjekten zu Elementen und Komponenten kann mit dem Anweisungsattribut [`@key`](xref:mvc/views/razor#key) gesteuert werden. `@key` bewirkt, dass der Vergleichsalgorithmus die Beibehaltung von Elementen oder Komponenten basierend auf dem Wert des Schlüssels garantiert.
>
> Weitere Informationen finden Sie in den folgenden Artikeln:
>
> * <xref:blazor/components/index#use-key-to-control-the-preservation-of-elements-and-components>
> * <xref:mvc/views/razor#key>

Die Komponente `Virtualize`:

* berechnet, wie viele Elemente basierend auf der Größe des Containers und der gerenderten Elemente gerendert werden sollen.
* führt neue Berechnungen durch, während der Benutzer scrollt, und rendert die Elemente noch mal.
* ruft nur das Segment der Datensätze einer externen API ab, das dem aktuell sichtbaren Bereich entspricht, anstatt alle Daten aus der Sammlung herunterzuladen.

Der Elementinhalt für die `Virtualize`-Komponente kann Folgendes umfassen:

* Einfachen HTML- und Razor-Code, wie im vorherigen Beispiel gezeigt
* Eine oder mehrere Razor-Komponenten
* Eine Mischung aus HTML/Razor- und Razor-Komponenten.

## <a name="item-provider-delegate"></a>Elementanbieterdelegat

Wenn Sie nicht alle Elemente in den Arbeitsspeicher laden möchten, können Sie eine Elementanbieter-Delegatmethode für den <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemsProvider%2A?displayProperty=nameWithType>-Parameter der Komponente festlegen, die die angeforderten Elemente bei Bedarf asynchron abruft. Im folgenden Beispiel stellt die Methode `LoadEmployees` die Elemente für die Komponente `Virtualize` bereit:

```razor
<Virtualize Context="employee" ItemsProvider="@LoadEmployees">
    <p>
        @employee.FirstName @employee.LastName has the 
        job title of @employee.JobTitle.
    </p>
</Virtualize>
```

Der Elementanbieter empfängt eine <xref:Microsoft.AspNetCore.Components.Web.Virtualization.ItemsProviderRequest>, die die erforderliche Anzahl von Elementen angibt, beginnend an einem bestimmten Startindex. Der Elementanbieter ruft dann die angeforderten Elemente aus einer Datenbank oder einem anderen Dienst ab und gibt Sie als <xref:Microsoft.AspNetCore.Components.Web.Virtualization.ItemsProviderResult%601> zusammen mit der Gesamtzahl der Elemente zurück. Der Elementanbieter kann auswählen, ob die Elemente mit jeder Anforderung abgerufen oder zwischengespeichert werden, sodass Sie sofort verfügbar sind.

Eine `Virtualize`-Komponente kann nur **eine Elementquelle** aus ihren Parametern akzeptieren. Versuchen Sie daher nicht, gleichzeitig einen Anbieter eines Elements zu verwenden und `Items`eine Sammlung zuzuweisen. Wenn beide zugewiesen sind, wird eine <xref:System.InvalidOperationException> ausgelöst, wenn die Parameter der Komponente zur Laufzeit festgelegt werden.

Im folgenden Beispiel lädt die Methode `LoadEmployees` die Mitarbeiter aus einem `EmployeeService`-Element (das nicht gezeigt wird):

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

<xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.RefreshDataAsync%2A?displayProperty=nameWithType> weist die Komponente an, Daten noch mal von <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemsProvider%2A> anzufordern. Dies ist nützlich, wenn sich externe Daten ändern. Bei Verwendung von <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Items%2A> muss dies nicht aufgerufen werden.

## <a name="placeholder"></a>Platzhalter

Da das Anfordern von Elementen von einer Remotedatenquelle etwas Zeit in Anspruch nehmen kann, verfügen Sie über die Option, einen Platzhalter mit Elementinhalt zu rendern:

* Verwenden Sie <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Placeholder%2A> (`<Placeholder>...</Placeholder>`), um Inhalt anzuzeigen, bis die Elementdaten verfügbar sind.
* Verwenden Sie <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemContent%2A?displayProperty=nameWithType> zum Festlegen der Elementvorlage für die Liste.

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

## <a name="item-size"></a>Elementgröße

Die Größe jedes Elements in Pixeln kann mit <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemSize%2A?displayProperty=nameWithType> festgelegt werden (Standardwert: 50):

```razor
<Virtualize Context="employee" Items="@employees" ItemSize="25">
    ...
</Virtualize>
```

## <a name="overscan-count"></a>Overscananzahl

<xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.OverscanCount%2A?displayProperty=nameWithType> bestimmt, wie viele zusätzliche Elemente vor und nach dem sichtbaren Bereich gerendert werden. Diese Einstellung trägt dazu bei, die Häufigkeit des Renderns beim Scrollen zu verringern. Höhere Werte führen jedoch dazu, dass mehr Elemente auf der Seite gerendert werden (Standard: 3):

```razor
<Virtualize Context="employee" Items="@employees" OverscanCount="4">
    ...
</Virtualize>
```

## <a name="state-changes"></a>Statusänderungen

Wenn Sie Änderungen an Elementen vornehmen, die von der `Virtualize`-Komponente gerendert werden, rufen Sie <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> auf, um die erneute Auswertung und das erneute Rendern der Komponente zu erzwingen.
