---
title: Ansichtskomponenten in ASP.NET Core
author: rick-anderson
description: Erfahren Sie, wie Ansichtskomponenten in ASP.NET Core verwendet werden und wie sie Apps hinzugefügt werden.
ms.author: riande
ms.custom: mvc
ms.date: 12/18/2019
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
uid: mvc/views/view-components
ms.openlocfilehash: e0ff97b53d12fbf6c6a89e94704de1aee9d7f9e6
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060585"
---
# <a name="view-components-in-aspnet-core"></a>Ansichtskomponenten in ASP.NET Core

Von [Rick Anderson](https://twitter.com/RickAndMSFT)

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/view-components/sample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

## <a name="view-components"></a>Ansichtskomponenten

Ansichtskomponenten ähneln zwar den Teilansichten, sind aber wesentlich leistungsstärker. Ansichtskomponenten verwenden keine Modellbindungen und sind nur von den Daten abhängig, die bei ihrem Aufruf bereitgestellt werden. Dieser Artikel wurde mit Controllern und Ansichten geschrieben, aber Ansichts Komponenten funktionieren auch mit Razor Seiten.

Eine Ansichtskomponente:

* Rendert nur einen Block statt einer gesamten Antwort.
* Umfasst die gleiche Trennung von Belangen und Vorzüge der Testbarkeit, die auch zwischen einem Controller und einer Ansicht bestehen.
* Kann Parameter und Geschäftslogik aufweisen.
* Wird normalerweise von einer Layoutseite aus aufgerufen.

Ansichtskomponenten wurden für wiederverwendbare Renderinglogik entwickelt, die für eine Teilansicht zu komplex ist. Dazu gehören:

* Dynamische Navigationsmenüs
* Tag Cloud (dort, wo die Datenbank abgefragt wird)
* ein Anmeldebereich
* Einkaufswagen
* vor Kurzem veröffentlichte Artikel
* Inhalt in einer Seitenleiste auf einem klassischen Blog
* Ein Anmeldebereich, der auf jeder Seite gerendert wird und der die Links zum Abmelden bzw. Anmelden anzeigt, je nachdem, ob der Benutzer an- oder abgemeldet ist

Eine Ansichtskomponenten besteht aus zwei Teilen: der Klasse (normalerweise von [ViewComponent](/dotnet/api/microsoft.aspnetcore.mvc.viewcomponent) abgeleitet) und dem von dieser Klasse zurückgegebenen Ergebnis (normalerweise eine Ansicht). Eine Ansichtskomponente kann, ähnlich wie Controller, ein POCO sein. Die meisten Entwickler sollten jedoch von den Methoden und Eigenschaften, die von `ViewComponent` abgeleitet werden, Gebrauch machen.

Wenn Sie überlegen, ob Ansichts Komponenten die Spezifikationen einer APP erfüllen, erwägen Sie stattdessen die Verwendung von- Razor Komponenten. Razor Komponenten kombinieren auch Markup mit c#-Code, um wiederverwendbare UI-Einheiten zu schaffen Razor Komponenten sind für Entwickler Produktivität konzipiert, wenn Sie Client seitige Benutzeroberflächen Logik und Komposition bereitstellen. Weitere Informationen finden Sie unter <xref:blazor/components/index>.

## <a name="creating-a-view-component"></a>Erstellen einer Ansichtskomponente

In diesem Abschnitt werden die allgemeinen Anforderungen zum Erstellen einer Ansichtskomponente beschrieben. Im Folgenden wird jeder Schritt ausführlich betrachtet, und Sie erstellen im Zuge dessen eine Ansichtskomponente.

### <a name="the-view-component-class"></a>Die Ansichtskomponentenklasse

Eine Ansichtskomponentenklasse kann durch folgende Aktionen erstellt werden:

* durch die Ableitung von *ViewComponent*
* durch Ergänzen der Klasse mit dem Attribut `[ViewComponent]` oder durch das Ableiten von einer Klasse mit dem Attribut `[ViewComponent]`
* durch das Erstellen einer Klasse, deren Name mit dem Suffix *ViewComponent* endet

Ansichtskomponenten müssen, genauso wie Controller, öffentliche, unverschachtelt und nicht abstrakte Klassen sein. Der Ansichtskomponentenname ist der Klassenname ohne den Suffix „ViewComponent“. Er kann zudem explizit mit der Eigenschaft `ViewComponentAttribute.Name` angegeben werden.

Eine Ansichtskomponentenklasse:

* Unterstützt [Constructor Dependency Injection](../../fundamentals/dependency-injection.md) vollständig

* Ist nicht Bestandteil des Controllerlebenszyklus. Dies bedeutet, dass Sie keine [Filter](../controllers/filters.md) in Ansichtskomponenten verwenden können.

### <a name="view-component-methods"></a>Ansichtskomponentenmethoden

Eine Ansichtskomponente definiert ihre Logik in einer `InvokeAsync`-Methode, die ein `Task<IViewComponentResult>` zurückgibt, oder in einer synchronen `Invoke`-Methode, die ein `IViewComponentResult` zurückgibt. Parameter stammen direkt vom Aufruf der Ansichtskomponente und nicht von der Modellbindung. Eine Ansichtskomponente behandelt nie direkt eine Anfrage. Normalerweise initialisiert eine Ansichtskomponente ein Modell und übergibt dieses an eine Ansicht, indem sie die `View`-Methode aufruft. Zusammengefasst bedeutet dies für Komponentenmethoden Folgendes:

* Es wird eine `InvokeAsync`-Methode definiert, die ein `Task<IViewComponentResult>` zurückgibt, oder eine synchrone `Invoke`-Methode, die ein `IViewComponentResult` zurückgibt.
* Initialisiert in der Regel ein Modell und übergibt es an eine Ansicht, indem die-Methode aufgerufen wird `ViewComponent` `View` .
* Parameter stammen aus der aufrufenden Methode, nicht aus HTTP. Es gibt keine Modellbindung.
* Die Methoden sind nicht direkt als HTTP-Endpunkt erreichbar. Sie werden von Ihrem Code aufgerufen (üblicherweise in einer Ansicht). Eine Ansichtskomponente verarbeitet nie eine Anforderung.
* Methoden werden in der Signatur überladen, nicht in Details der aktuellen HTTP-Anforderung.

### <a name="view-search-path"></a>Anzeigen des Suchpfads

Die Runtime sucht in den folgenden Pfaden nach der Ansicht:

* /Views/{Controllername} /Components/{Ansichtskomponentenname}/{Ansichtsname}
* /Views/Shared/Components/{Ansichtskomponentenname}/{Ansichtsname}
* /Pages/Shared/Components/{Ansichtskomponentenname}/{Ansichtsname}

Der Suchpfad gilt für Projekte mit Controllern und Ansichten und Razor Seiten.

Der Standardansichtsname für die Ansichtskomponente ist *Default* . Dies bedeutet, dass Ihre Ansichtsdatei normalerweise *Default.cshtml* heißt. Sie können einen anderen Ansichtsnamen angeben, wenn Sie die Ansichtskomponentenergebnisse erstellen oder die `View`-Methode aufrufen.

Es wird empfohlen, dass Sie die Ansichtsdatei *Default.cshtml* nennen und den Pfad *View/Shared/Components/{Ansichtskomponentenname}/{Ansichtsname}* verwenden. Die Ansichtskomponente `PriorityList`, die in diesem Beispiel verwendet wird, verwendet *Views/Shared/Components/PriorityList/Default.cshtml* für die Ansichtskomponentenansicht.

### <a name="customize-the-view-search-path"></a>Anpassen des Ansichtssuchpfads

Um den Suchpfad für die Ansicht anzupassen, ändern Sie Razor die <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions.ViewLocationFormats> Sammlung. Um z. B. nach Ansichten im Pfad „/Components/{Name der Ansichtskomponente}/{Name der Ansicht}“ zu suchen, fügen Sie der Auflistung ein neues Element hinzu:

[!code-csharp[](view-components/samples_snapshot/2.x/Startup.cs?name=snippet_ViewLocationFormats&highlight=4)]

Im vorangehenden Code stellt der Platzhalter „{0}“ den Pfad „Components/{Name der Ansichtskomponente}/{Name der Ansicht}“ dar.

## <a name="invoking-a-view-component"></a>Aufrufen einer Ansichtskomponente

Rufen Sie Folgendes in der Ansicht auf, wenn Sie die Ansichtskomponente verwenden möchten:

```cshtml
@await Component.InvokeAsync("Name of view component", {Anonymous Type Containing Parameters})
```

Die Parameter werden an die `InvokeAsync`-Methode übergeben. Die Ansichtskomponente `PriorityList`, die im Artikel entwickelt wurde, wird von der Ansichtsdatei *Views/ToDo/Index.cshtml* aufgerufen. Im folgenden Code wird die `InvokeAsync`-Methode mit zwei Parametern aufgerufen:

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexFinal.cshtml?range=35)]

::: moniker range=">= aspnetcore-1.1"

## <a name="invoking-a-view-component-as-a-tag-helper"></a>Aufrufen einer Ansichtskomponente als Taghilfsprogramm

In ASP.NET Core 1.1 und höher können Sie eine Ansichtskomponente als [Taghilfsprogramm](xref:mvc/views/tag-helpers/intro) aufrufen.

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexTagHelper.cshtml?range=37-38)]

Namen von Klassen und Methodenparameter für Taghilfsprogramme, die in Pascal-Schreibweise angegeben sind, werden in [Kebab-Schreibweise](https://stackoverflow.com/questions/11273282/whats-the-name-for-dash-separated-case/12273101) übersetzt. Das Taghilfsprogramm zum Aufrufen einer Ansichtskomponente verwendet das `<vc></vc>`-Element. Die Ansichtskomponente wird wie folgt angegeben:

```cshtml
<vc:[view-component-name]
  parameter1="parameter1 value"
  parameter2="parameter2 value">
</vc:[view-component-name]>
```

Damit Sie eine Ansichtskomponente als Taghilfsprogramm verwenden können, registrieren Sie die Assembly, die die Ansichtskomponente enthält, mit der `@addTagHelper`-Anweisung. Wenn Ihre Ansichtskomponente eine Assembly mit dem Namen `MyWebApp` ist, fügen Sie die folgende Anweisung zu der Datei *_ViewImports.cshtml* hinzu:

```cshtml
@addTagHelper *, MyWebApp
```

Sie können eine Ansichtskomponente als Taghilfsprogramm für jede Datei registrieren, die auf die Ansichtskomponente verweist. Weitere Informationen zum Registrieren eines Taghilfsprogramms finden Sie unter [Managing Tag Helper Scope (Verwalten des Bereichs des Taghilfsprogramms)](xref:mvc/views/tag-helpers/intro#managing-tag-helper-scope).

Die `InvokeAsync`-Methode, die in diesem Tutorial verwendet wird:

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexFinal.cshtml?range=35)]

In Taghilfsprogramm-Markup:

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexTagHelper.cshtml?range=37-38)]

Im Beispiel oben stehenden Beispiel wird die Ansichtskomponente `PriorityList` zu `priority-list`. Die Parameter der Ansichtskomponente werden als Attribute in Kebab-Schreibweise übergeben.

::: moniker-end

### <a name="invoking-a-view-component-directly-from-a-controller"></a>Direkter Aufrufe einer Ansichtskomponente von einem Controller

Ansichtskomponenten werden normalerweise von einer Ansicht aus aufgerufen, aber Sie können sie auch direkt von einer Controllermethode aus aufrufen. Obwohl Ansichtskomponenten keine Endpunkte so wie Controller definieren, können Sie trotzdem eine Controlleraktion implementieren, die den Inhalt von `ViewComponentResult` zurückgibt.

In diesem Beispiel wird die Ansichtskomponente direkt von einem Controller aus aufgerufen:

[!code-csharp[](view-components/sample/ViewCompFinal/Controllers/ToDoController.cs?name=snippet_IndexVC)]

## <a name="walkthrough-creating-a-simple-view-component"></a>Exemplarische Vorgehensweise: Erstellen einer einfachen Ansichtskomponente

[Laden Sie den Startercode herunter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/view-components/sample), und erstellen und testen Sie diesen. Dabei handelt es sich um ein einfaches Projekt mit einem `ToDo`-Controller, in dem eine Liste von *ToDo* -Elementen angezeigt wird.

![Liste mit ToDo-Elementen](view-components/_static/2dos.png)

### <a name="add-a-viewcomponent-class"></a>Hinzufügen einer ViewComponent-Klasse

Erstellen Sie einen *ViewComponents* -Ordner, und fügen Sie die folgende `PriorityListViewComponent`-Klasse hinzu:

[!code-csharp[](view-components/sample/ViewCompFinal/ViewComponents/PriorityListViewComponent1.cs?name=snippet1)]

Bemerkungen zum Code:

* Ansichtskomponentenklassen können sich in **jedem** Ordner im Projekt befinden.
* Da der Klassenname „PriorityList **ViewComponent** mit dem Suffix **ViewComponent** endet, verwendet die Runtime die Zeichenfolge „PriorityList“, wenn Sie von einer Ansicht aus auf die Klassenkomponente verweist. Dies wird im Folgenden noch ausführlicher erklärt.
* Das `[ViewComponent]`-Attribut kann den Namen ändern, der zum Verweis auf eine Ansichtskomponente verwendet wird. Wir hätten die Klasse auch `XYZ` nennen und das `ViewComponent`-Attribut anwenden können:

  ```csharp
  [ViewComponent(Name = "PriorityList")]
     public class XYZ : ViewComponent
     ```

* Das oben stehende `[ViewComponent]`-Attribut teilt dem Ansichtskomponentenselektor mit, den Namen `PriorityList` zu verwenden, wenn er nach den Ansichten sucht, die mit der Komponente verknüpft sind. Zudem wird er informiert, die Zeichenfolge „“ zu verwenden, wenn er von einer Ansicht aus auf die Klassenkomponente verweist. Dies wird im Folgenden noch ausführlicher erklärt.
* Die Komponente verwendet [Dependency Injection](../../fundamentals/dependency-injection.md), um den Datenkontext verfügbar zu machen.
* `InvokeAsync` macht eine Methode verfügbar, die von einer Ansicht aus aufgerufen werden kann, und akzeptiert eine arbiträre Anzahl von Argumenten.
* Die `InvokeAsync`-Methode gibt mehrere `ToDo`-Elemente zurück, die die Bedingungen der Parameter `isDone` und `maxPriority` erfüllen.

### <a name="create-the-view-component-no-locrazor-view"></a>Erstellen der Ansichts Komponenten Razor Ansicht

* Erstellen Sie den Ordner *Views/Shared/Components* . Diese Ordner **muss** den Namen *Components* besitzen.

* Erstellen Sie den Ordner *Views/Shared/Components/PriorityList* . Der Ordnername muss mit dem Namen der Ansichtskomponentenklasse oder mit dem Namen der Klasse ohne Suffix (wenn wir uns an die Konvention gehalten und *ViewComponent* als Suffix im Klassennamen verwendet haben) übereinstimmen. Wenn Sie das Attribut `ViewComponent` verwenden, muss der Klassenname mit der Attributbezeichnung übereinstimmen.

* Erstellen Sie eine *Sichten/Shared/Components/prioritylist/default. cshtml* - Razor Ansicht:


  [!code-cshtml[](view-components/sample/ViewCompFinal/Views/Shared/Components/PriorityList/Default1.cshtml)]

   RazorIn der Ansicht wird eine Liste `TodoItem` von angezeigt und angezeigt. Wenn die `InvokeAsync`-Methode der Ansichtskomponente nicht den Namen der Ansicht übergibt (wie in unserem Beispiel), wird *Default* per Konvention für den Ansichtsnamen verwendet. Später in diesem Tutorial erfahren Sie, wie Sie den Namen der Ansicht übergeben. Fügen Sie eine Ansicht einem controllerspezifischen Ansichtsordner hinzu, um das Standardformat für einen spezifischen Controller zu überschreiben (z.B. *Views/ToDo/Components/PriorityList/Default.cshtml* ).

    Wenn die Ansichtskomponente controllerspezifisch ist, können Sie sie dem controllerspezifischen Ordner hinzufügen ( *Views/ToDo/Components/PriorityList/Default.cshtml* ).

* Fügen Sie ein `div`-Objekt, das einen Aufruf an eine Prioritätslistenkomponente enthält, am Ende der Datei *Views/ToDo/index.cshtml* hinzu:

    [!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexFirst.cshtml?range=34-38)]

Das Markup `@await Component.InvokeAsync` zeigt die Syntax für die aufrufenden Ansichtskomponenten an. Das erste Argument ist der Name der aufzurufenden Komponente. Darauffolgende Parameter werden an die Komponente übergeben. `InvokeAsync` kann eine arbiträre Anzahl von Argumenten annehmen.

Testen der App. In der folgenden Abbildung werden die ToDo-Liste und die Elemente mit Priorität angezeigt:

![ToDo-Liste und Prioritätselemente](view-components/_static/pi.png)

Sie können Sie Ansichtskomponente auch direkt vom Controller aus aufrufen:

[!code-csharp[](view-components/sample/ViewCompFinal/Controllers/ToDoController.cs?name=snippet_IndexVC)]

![Prioritätselemente der IndexVC-Aktion](view-components/_static/indexvc.png)

### <a name="specifying-a-view-name"></a>Angeben eines Ansichtsnamens

Eine komplexe Ansichtskomponente erfordert möglicherweise, dass unter bestimmten Umständen eine Ansicht angegeben wird, die nicht dem Standard entspricht. Der folgende Code zeigt, wie die Ansicht „PVC“ von der `InvokeAsync`-Methode aus angegeben wird. Aktualisieren Sie die `InvokeAsync`-Methode in der `PriorityListViewComponent`-Klasse.

[!code-csharp[](../../mvc/views/view-components/sample/ViewCompFinal/ViewComponents/PriorityListViewComponentFinal.cs?highlight=4,5,6,7,8,9&range=28-39)]

Kopieren Sie die Datei *Views/Shared/Components/PriorityList/Default.cshtml* in eine Ansicht mit dem Namen *Views/Shared/Components/PriorityList/PVC.cshtml* . Fügen Sie eine Überschrift hinzu, um anzugeben, dass die PVC-Ansicht verwendet wird.

[!code-cshtml[](../../mvc/views/view-components/sample/ViewCompFinal/Views/Shared/Components/PriorityList/PVC.cshtml?highlight=3)]

Aktualisieren Sie *Views/ToDo/Index.cshtml* :

<!-- Views/ToDo/Index.cshtml is never imported, so change to test tutorial -->

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexFinal.cshtml?range=35)]

Führen Sie die App aus und überprüfen Sie die PVC-Ansicht.

![Ansichtskomponente mit Priorität](view-components/_static/pvc.png)

Wenn die PVC-Ansicht nicht gerendert wird, stellen Sie sicher, dass Sie die Ansichtskomponente mit einer Priorität von 4 oder höher aufrufen.

### <a name="examine-the-view-path"></a>Untersuchen des Ansichtspfads

* Ändern Sie den Prioritätsparameter in drei oder weniger, damit die Prioritätsansicht nicht zurückgegeben wird.
* Benennen Sie *Views/ToDo/Components/PriorityList/Default.cshtml* vorübergehend in *1Default.cshtml* um.
* Wenn Sie die App testen, erhalten Sie die folgende Fehlermeldung:

   ```
   An unhandled exception occurred while processing the request.
   InvalidOperationException: The view 'Components/PriorityList/Default' wasn't found. The following locations were searched:
   /Views/ToDo/Components/PriorityList/Default.cshtml
   /Views/Shared/Components/PriorityList/Default.cshtml
   EnsureSuccessful
   ```

* Kopieren Sie *Views/ToDo/Components/PriorityList/1Default.cshtml* nach *Views/Shared/Components/PriorityList/Default.cshtml* .
* Fügen Sie der ToDo-Ansichtskomponentenansicht *Shared* (Freigegeben) Markup hinzu, um anzugeben, dass die Ansicht aus dem Ordner *Shared* stammt.
* Testen Sie die Komponentenansicht **Shared** (Freigegeben).

![ToDo-Ausgabe mit Komponentenansicht „Shared“](view-components/_static/shared.png)

### <a name="avoiding-hard-coded-strings"></a>Vermeiden hart codierter Zeichenfolgen

Wenn Sie Sicherheit zu Kompilierzeit haben möchten, können Sie den hart codierten Komponentennamen durch den Klassennamen ersetzen. Erstellen Sie die Ansichtskomponente ohne den Suffix „ViewComponent“:

[!code-csharp[](../../mvc/views/view-components/sample/ViewCompFinal/ViewComponents/PriorityList.cs?highlight=10&range=5-35)]

Fügen Sie `using` Razor der Ansichts Datei eine-Anweisung hinzu, und verwenden Sie den- `nameof` Operator:

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexNameof.cshtml?range=1-6,35-)]

## <a name="perform-synchronous-work"></a>Ausführen synchroner Verarbeitung

Das Framework verarbeitet den Aufruf einer synchronen `Invoke`-Methode, wenn Sie keine asynchrone Verarbeitung durchführen müssen. Die folgende Methode erstellt eine synchrone `Invoke`-Ansichtskomponente:

```csharp
public class PriorityList : ViewComponent
{
    public IViewComponentResult Invoke(int maxPriority, bool isDone)
    {
        var items = new List<string> { $"maxPriority: {maxPriority}", $"isDone: {isDone}" };
        return View(items);
    }
}
```

Die Datei der Ansichts Komponente Razor Listet die an die Methode übergebenen Zeichen folgen auf `Invoke` ( *views/Home/Components/prioritylist/default. cshtml* ):

```cshtml
@model List<string>

<h3>Priority Items</h3>
<ul>
    @foreach (var item in Model)
    {
        <li>@item</li>
    }
</ul>
```

::: moniker range=">= aspnetcore-1.1"

Die Ansichts Komponente wird in einer Razor Datei (z. b. *views/Home/Index. cshtml* ) mit einem der folgenden Ansätze aufgerufen:

* <xref:Microsoft.AspNetCore.Mvc.IViewComponentHelper>
* [Taghilfsprogramm](xref:mvc/views/tag-helpers/intro)

Rufen Sie `Component.InvokeAsync` auf, um das <xref:Microsoft.AspNetCore.Mvc.IViewComponentHelper>-Verfahren zu verwenden:

::: moniker-end

::: moniker range="< aspnetcore-1.1"

Die Ansichts Komponente wird in einer Razor Datei (z. b. *views/Home/Index. cshtml* ) mit aufgerufen <xref:Microsoft.AspNetCore.Mvc.IViewComponentHelper> .

Rufen Sie `Component.InvokeAsync` auf:

::: moniker-end

```cshtml
@await Component.InvokeAsync(nameof(PriorityList), new { maxPriority = 4, isDone = true })
```

::: moniker range=">= aspnetcore-1.1"

Um das Taghilfsprogramm zu verwenden, registrieren Sie die Assembly, die die Ansichtskomponente enthält, mit der Anweisung `@addTagHelper` (die Ansichtskomponente befindet sich in einer Assembly namens `MyWebApp`):

```cshtml
@addTagHelper *, MyWebApp
```

Verwenden Sie das taghilfsprogramm für die Ansichts Komponente in der Razor Markup Datei:

```cshtml
<vc:priority-list max-priority="999" is-done="false">
</vc:priority-list>
```

::: moniker-end

Die Methoden Signatur von `PriorityList.Invoke` ist synchron, Razor findet jedoch die-Methode `Component.InvokeAsync` in der Markup Datei und ruft Sie auf.

## <a name="all-view-component-parameters-are-required"></a>Alle Parameter einer Ansichtskomponente sind erforderlich

Jeder Parameter in einer Ansichtskomponente ist ein erforderliches Attribut. Weitere Informationen finden Sie im entsprechenden [GitHub-Issue](https://github.com/dotnet/AspNetCore/issues/5011). Wenn ein Parameter ausgelassen wird, geschieht Folgendes:

* Für die Signatur der `InvokeAsync`-Methode kann keine Übereinstimmung ermittelt werden. Die Methode wird daher nicht ausgeführt.
* Das Markup des ViewComponent-Elements wird nicht gerendert.
* Es werden keine Fehler ausgelöst.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Dependency Injection in Ansichten](xref:mvc/views/dependency-injection)
