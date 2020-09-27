---
title: ASP.NET Core Blazor-Komponentenvirtualisierung
author: guardrex
description: Erfahren Sie, Komponentenvirtualisierung in ASP.NET Core Blazor-Apps verwendet wird.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/21/2020
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
ms.openlocfilehash: 911eeeb445741aa1519e1464dd4a75e26f6f12ab
ms.sourcegitcommit: 62cc131969b2379f7a45c286a751e22d961dfbdb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/22/2020
ms.locfileid: "90847571"
---
# <a name="aspnet-core-no-locblazor-component-virtualization"></a>ASP.NET Core Blazor-Komponentenvirtualisierung

Von [Daniel Roth](https://github.com/danroth27)

Verbessern Sie die wahrgenommene Leistung von Komponentenrendering mithilfe der integrierten Virtualisierungsunterstützung des Blazor-Frameworks. Virtualisierung ist eine Technik zum Einschränken des Benutzeroberflächenrenderings auf die aktuell sichtbaren Elemente. Die Virtualisierung ist beispielsweise hilfreich, wenn die App eine lange Liste oder eine Tabelle mit vielen Zeilen rendern und nur eine Teilmenge der Elemente zu einem bestimmten Zeitpunkt sichtbar sein muss. Blazor stellt die `Virtualize`-Komponente bereit, die verwendet werden kann, um den Komponenten einer App Virtualisierung hinzuzufügen.

::: moniker range=">= aspnetcore-5.0"

Ohne Virtualisierung kann eine typische Liste oder tabellenbasierte Komponente eine C#-[`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in)-Schleife verwenden, um jedes Element in der Liste oder jede Zeile in der Tabelle zu rendern:

```razor
<table>
    @foreach (var employee in employees)
    {
        <tr>
            <td>@employee.FirstName</td>
            <td>@employee.LastName</td>
            <td>@employee.JobTitle</td>
        </tr>
    }
</table>
```

Wenn die Liste Tausende von Elementen enthält, kann das Rendern der Liste viel Zeit in Anspruch nehmen. Der Benutzer kann ggf. eine merkliche Verzögerung der Benutzeroberfläche wahrnehmen.

Anstatt jedes Element in der Liste gleichzeitig zu rendern, ersetzen Sie die [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in)-Schleife durch die `Virtualize`-Komponente, und geben Sie eine feste Elementquelle mit `Items` an. Nur die aktuell sichtbaren Elemente werden gerendert:

```razor
<table>
    <Virtualize Context="employee" Items="@employees">
        <tr>
            <td>@employee.FirstName</td>
            <td>@employee.LastName</td>
            <td>@employee.JobTitle</td>
        </tr>
    </Virtualize>
</table>
```

Wenn Sie mit `Context` keinen Kontext für die Komponente angeben, verwenden Sie den `context`-Wert (`@context.{PROPERTY}`) in der Elementinhaltsvorlage:

```razor
<table>
    <Virtualize Items="@employees">
        <tr>
            <td>@context.FirstName</td>
            <td>@context.LastName</td>
            <td>@context.JobTitle</td>
        </tr>
    </Virtualize>
</table>
```

Die `Virtualize`-Komponente berechnet, wie viele Elemente basierend auf der Höhe des Containers und der Größe der gerenderten Elemente gerendert werden.

## <a name="item-provider-delegate"></a>Elementanbieterdelegat

Wenn Sie nicht alle Elemente in den Arbeitsspeicher laden möchten, können Sie eine Elementanbieter-Delegatmethode für den `ItemsProvider`-Parameter der Komponente festlegen, die die angeforderten Elemente bei Bedarf asynchron abruft:

```razor
<table>
    <Virtualize Context="employee" ItemsProvider="@LoadEmployees">
         <tr>
            <td>@employee.FirstName</td>
            <td>@employee.LastName</td>
            <td>@employee.JobTitle</td>
        </tr>
    </Virtualize>
</table>
```

Der Elementanbieter empfängt eine `ItemsProviderRequest`, die die erforderliche Anzahl von Elementen angibt, beginnend an einem bestimmten Startindex. Der Elementanbieter ruft dann die angeforderten Elemente aus einer Datenbank oder einem anderen Dienst ab und gibt Sie als `ItemsProviderResult<TItem>` zusammen mit der Gesamtzahl der Elemente zurück. Der Elementanbieter kann auswählen, ob die Elemente mit jeder Anforderung abgerufen oder zwischengespeichert werden, sodass Sie sofort verfügbar sind. Versuchen Sie nicht, einen Elementanbieter zu verwenden, und weisen Sie `Items` für dieselbe `Virtualize`-Komponente eine Sammlung zu.

Im folgenden Beispiel werden Mitarbeiter aus einem `EmployeeService` geladen:

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

## <a name="placeholder"></a>Platzhalter

Da das Anfordern von Elementen aus einer Remotedatenquelle einige Zeit in Anspruch nehmen kann, haben Sie die Möglichkeit, einen Platzhalter (`<Placeholder>...</Placeholder>`) zu rendern, bis die Elementdaten verfügbar sind:

```razor
<table>
    <Virtualize Context="employee" ItemsProvider="@LoadEmployees">
        <ItemContent>
            <tr>
                <td>@employee.FirstName</td>
                <td>@employee.LastName</td>
                <td>@employee.JobTitle</td>
            </tr>
        </ItemContent>
        <Placeholder>
            <tr>
                <td>Loading...</td>
            </tr>
        </Placeholder>
    </Virtualize>
</table>
```

## <a name="item-size"></a>Elementgröße

Die Größe jedes Elements in Pixeln kann mit `ItemSize` festgelegt werden (Standardwert: 50px):

```razor
<table>
    <Virtualize Context="employee" Items="@employees" ItemSize="25">
        ...
    </Virtualize>
</table>
```

## <a name="overscan-count"></a>Overscananzahl

`OverscanCount` bestimmt, wie viele zusätzliche Elemente vor und nach dem sichtbaren Bereich gerendert werden. Diese Einstellung trägt dazu bei, die Häufigkeit des Renderns beim Scrollen zu verringern. Höhere Werte führen jedoch dazu, dass mehr Elemente auf der Seite gerendert werden (Standard: 3):

```razor
<table>
    <Virtualize Context="employee" Items="@employees" OverscanCount="4">
        ...
    </Virtualize>
</table>
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

Bei einem Raster oder einer Liste, die Hunderte Datensätze rendert, die Komponenten enthalten, ist die Auslastung des Prozessors für das Rendern beispielsweise höher. Ziehen Sie es in Betracht, ein Raster oder ein Listenlayout zu virtualisieren, sodass nur eine Teilmenge der Komponenten jeweils gleichzeitig gerendert wird. Ein Beispiel für das Rendern einer Teilmenge von Komponenten finden Sie bei den folgenden Komponenten unter [`Virtualization`-Beispiel-App (aspnet/samples, GitHub-Repository)](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Virtualization):

* `Virtualize`-Komponente ([`Shared/Virtualize.razor`](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Shared/Virtualize.cs)): Eine in C# geschriebene Komponente, die <xref:Microsoft.AspNetCore.Components.ComponentBase> implementiert, um mehrere Wetterdatensätze basierend auf dem Scrollen des Benutzers zu rendern
* `FetchData`-Komponente ([`Pages/FetchData.razor`](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Pages/FetchData.razor)): Verwendet die `Virtualize`-Komponente, um 25 Wetterdatensätze gleichzeitig anzuzeigen

::: moniker-end

## <a name="state-changes"></a>Statusänderungen

Wenn Sie Änderungen an Elementen vornehmen, die von der `Virtualize`-Komponente gerendert werden, rufen Sie <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> auf, um die erneute Auswertung und das erneute Rendern der Komponente zu erzwingen.
