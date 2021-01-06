---
title: ASP.NET Core-Komponentenvorlagen in Blazor
author: guardrex
description: Hier erfahren Sie, wie Komponentenvorlagen eine oder mehrere Benutzeroberflächenvorlagen als Parameter akzeptieren können, die dann wiederum als Teil der Renderlogik der Komponente verwendet werden können.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/18/2020
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
uid: blazor/components/templated-components
ms.openlocfilehash: f818a0b7f1ba6d4dd6affeeba785c5e288568dd8
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/04/2021
ms.locfileid: "94507953"
---
# <a name="aspnet-core-no-locblazor-templated-components"></a>ASP.NET Core-Komponentenvorlagen in Blazor

Von [Luke Latham](https://github.com/guardrex) und [Daniel Roth](https://github.com/danroth27)

Bei Komponentenvorlagen geht es um Komponenten, die eine oder mehrere Benutzeroberflächenvorlagen als Parameter akzeptieren, die dann wiederum als Teil der Renderlogik der Komponente verwendet werden können. Mit Komponentenvorlagen können Sie Komponenten höherer Ebenen erstellen, die einfacher wiederverwendet werden können als reguläre Komponenten. Nachstehend sind einige Beispiele angegeben:

* Eine Tabellenkomponente, mit der ein Benutzer Vorlagen für die Überschrift, die Zeilen und die Fußzeile einer Tabelle angeben kann
* Eine Listenkomponente, mit der ein Benutzer eine Vorlage für Renderelemente in einer Liste angeben kann

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

## <a name="template-parameters"></a>Vorlagenparameter

Eine Komponentenvorlage wird definiert, indem mindestens ein Komponentenparameter des Typs <xref:Microsoft.AspNetCore.Components.RenderFragment> oder <xref:Microsoft.AspNetCore.Components.RenderFragment%601> angegeben wird. Ein Renderfragment stellt dabei ein Segment der Benutzeroberfläche dar, das gerendert werden soll. <xref:Microsoft.AspNetCore.Components.RenderFragment%601> akzeptiert einen Typparameter, der angegeben werden kann, wenn das Renderfragment aufgerufen wird.

`TableTemplate`-Komponente (`TableTemplate.razor`):

[!code-razor[](../common/samples/5.x/BlazorWebAssemblySample/Components/TableTemplate.razor)]

Bei der Verwendung einer Komponentenvorlage werden die Vorlagenparameter mithilfe eines untergeordneten Elements angegeben, das mit den Namen der Parameter übereinstimmt (`TableHeader` und `RowTemplate` im folgenden Beispiel):

```razor
<TableTemplate Items="pets">
    <TableHeader>
        <th>ID</th>
        <th>Name</th>
    </TableHeader>
    <RowTemplate>
        <td>@context.PetId</td>
        <td>@context.Name</td>
    </RowTemplate>
</TableTemplate>

@code {
    private List<Pet> pets = new List<Pet>
    {
        new Pet { PetId = 2, Name = "Mr. Bigglesworth" },
        new Pet { PetId = 4, Name = "Salem Saberhagen" },
        new Pet { PetId = 7, Name = "K-9" }
    };

    private class Pet
    {
        public int PetId { get; set; }
        public string Name { get; set; }
    }
}
```

> [!NOTE]
> Generische Typeinschränkungen werden in einem zukünftigen Release unterstützt. Weitere Informationen finden Sie unter [Zulassen generischer Typeinschränkungen (dotnet/aspnetcore #8433)](https://github.com/dotnet/aspnetcore/issues/8433).

## <a name="template-context-parameters"></a>Vorlagenkontextparameter

Komponentenargument des Typs <xref:Microsoft.AspNetCore.Components.RenderFragment%601>, die als Elemente übergeben werden, verfügen über einen impliziten Parameter namens `context` (beispielsweise `@context.PetId` im vorherigen Codebeispiel). Sie können den Parameternamen jedoch mithilfe des `Context`-Attributs für das untergeordnete Element ändern. Im folgenden Beispiel gibt das `Context`-Attribut des `RowTemplate`-Elements den `pet`-Parameter an:

```razor
<TableTemplate Items="pets">
    <TableHeader>
        <th>ID</th>
        <th>Name</th>
    </TableHeader>
    <RowTemplate Context="pet">
        <td>@pet.PetId</td>
        <td>@pet.Name</td>
    </RowTemplate>
</TableTemplate>

@code {
    ...
}
```

Alternativ können Sie das `Context`-Attribut im Komponentenelement angeben. Das angegebene `Context`-Attribut gilt für alle angegebenen Vorlagenparameter. Dies ist hilfreich, wenn Sie den Inhaltsparametername für einen impliziten untergeordneten Inhalt angeben möchten, ohne das untergeordnete Elemente umschließen zu müssen. Im folgenden Beispiel wird das `Context`-Attribut auf dem `TableTemplate`-Element angezeigt und gilt für alle Vorlagenparameter:

```razor
<TableTemplate Items="pets" Context="pet">
    <TableHeader>
        <th>ID</th>
        <th>Name</th>
    </TableHeader>
    <RowTemplate>
        <td>@pet.PetId</td>
        <td>@pet.Name</td>
    </RowTemplate>
</TableTemplate>

@code {
    ...
}
```

## <a name="generic-typed-components"></a>Komponenten mit generischem Typ

Komponentenvorlagen haben oft einen generischen Typ. Eine generische `ListViewTemplate`-Komponente (`ListViewTemplate.razor`) kann beispielsweise verwendet werden, um `IEnumerable<T>`-Werte zu rendern. Verwenden Sie die [`@typeparam`](xref:mvc/views/razor#typeparam)-Direktive, um Typenparameter anzugeben, um eine generische Komponente zu definieren:

[!code-razor[](../common/samples/5.x/BlazorWebAssemblySample/Components/ListViewTemplate.razor)]

Bei der Verwendung einer Komponente mit generischem Typ, wird der Typparameter wenn möglich abgeleitet:

```razor
<ListViewTemplate Items="pets">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>

@code {
    private List<Pet> pets = new List<Pet>
    {
        new Pet { Name = "Mr. Bigglesworth" },
        new Pet { Name = "Salem Saberhagen" },
        new Pet { Name = "K-9" }
    };

    private class Pet
    {
        public string Name { get; set; }
    }
}
```

Andernfalls muss der Typparameter explizit mithilfe eines Attributs angegeben werden, der mit dem Namen des Typparameters übereinstimmt. Im folgenden Beispiel wird der Typ von `TItem="Pet"` angegeben:

```razor
<ListViewTemplate Items="pets" TItem="Pet">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>

@code {
    ...
}
```
