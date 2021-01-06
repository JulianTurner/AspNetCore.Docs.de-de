---
title: 'Tutorial: Hinzufügen von Sortieren, Filtern und Paging: ASP.NET MVC mit EF Core'
description: In diesem Tutorial fügen Sie die Funktionen zum Sortieren, Filtern und Paging zur Studentenindexseite hinzu. Sie werden auch eine Seite erstellen, auf der einfache Gruppierungsvorgänge ausgeführt werden.
author: rick-anderson
ms.author: riande
ms.date: 03/27/2019
ms.topic: tutorial
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
uid: data/ef-mvc/sort-filter-page
ms.openlocfilehash: 8e425d413471912c763c4892a90e9d12039efec4
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/04/2021
ms.locfileid: "93053981"
---
# <a name="tutorial-add-sorting-filtering-and-paging---aspnet-mvc-with-ef-core"></a>Tutorial: Hinzufügen von Sortieren, Filtern und Paging: ASP.NET MVC mit EF Core

Im vorherigen Tutorial haben Sie eine Reihe von Webseiten für grundlegende CRUD-Vorgänge für Studentenentitäten implementiert. In diesem Tutorial fügen Sie die Funktionen zum Sortieren, Filtern und Paging zur Studentenindexseite hinzu. Sie werden auch eine Seite erstellen, auf der einfache Gruppierungsvorgänge ausgeführt werden.

Die folgende Abbildung zeigt, wie die Seite am Ende aussehen wird. Die Spaltenüberschriften sind Links, auf die der Benutzer klicken kann, um die Spalte zu sortieren. Wiederholtes Klicken auf eine Spaltenüberschrift schaltet zwischen aufsteigender und absteigender Sortierreihenfolge um.

![Indexseite „Studenten“](sort-filter-page/_static/paging.png)

In diesem Tutorial:

> [!div class="checklist"]
> * Hinzufügen von Spaltensortierungslinks
> * Hinzufügen eines Suchfelds
> * Hinzufügen von Paging zum „Students“-Index
> * Hinzufügen von Paging zur „Index“-Methode
> * Hinzufügen von Paginglinks
> * Erstellen einer Infoseite

## <a name="prerequisites"></a>Voraussetzungen

* [Implementieren von CRUD-Funktionen](crud.md)

## <a name="add-column-sort-links"></a>Hinzufügen von Spaltensortierungslinks

Ändern Sie die `Index`-Methode des Studentencontrollers, und fügen Sie Code zur Studentenindexansicht hinzu, um der Indexseite die Sortierfunktion hinzuzufügen.

### <a name="add-sorting-functionality-to-the-index-method"></a>Hinzufügen der Sortierfunktion zur Indexmethode

Ersetzen Sie in *StudentsController.cs* die `Index`-Methode durch den folgenden Code:

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_SortOnly)]

Dieser Code empfängt einen `sortOrder`-Parameter aus der Abfragezeichenfolge in der URL. Der Wert der Abfragezeichenfolge wird von ASP.NET Core MVC als Parameter an die Aktionsmethode übergeben. Der Parameter ist eine Zeichenfolge, entweder „Name“ oder „Date“, optional gefolgt von einem Unterstrich und der Zeichenfolge „desc“, die die absteigende Reihenfolge angibt. Standardmäßig wird eine aufsteigende Sortierreihenfolge verwendet.

Bei der ersten Anforderung der Indexseite gibt es keine Abfragezeichenfolge. Die Studenten werden nach Nachnamen in aufsteigender Reihenfolge angezeigt. Dies ist durch den Fall-Through-Fall in der `switch`-Anweisung standardmäßig festgelegt. Wenn der Benutzer auf den Link einer Spaltenüberschrift klickt, wird der entsprechende `sortOrder`-Wert in der Abfragezeichenfolge bereitgestellt.

Die beiden `ViewData`-Elemente (NameSortParm und DateSortParm) werden von der Ansicht verwendet, um die Links der Spaltenüberschriften mit den entsprechenden Abfragezeichenfolgenwerten zu konfigurieren.

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_SortOnly&highlight=3-4)]

Hierbei handelt es sich um ternäre Anweisungen. Die erste gibt an, dass wenn der `sortOrder`-Parameter gleich 0 (null) oder leer ist, „NameSortParm“ auf „name_desc“ festgelegt werden soll. Andernfalls soll er auf eine leere Zeichenfolge festgelegt werden. Diese beiden Anweisungen ermöglichen der Ansicht das Festlegen der Links für Spaltenüberschriften wie folgt:

|  Aktuelle Sortierreihenfolge  | Hyperlink „Nachname“ | Hyperlink „Datum“ |
|:--------------------:|:-------------------:|:--------------:|
| Nachname (aufsteigend)  | descending          | ascending      |
| Nachname (absteigend) | ascending           | ascending      |
| Datum (aufsteigend)       | ascending           | descending     |
| Datum (absteigend)      | ascending           | ascending      |

Die Methode gibt über LINQ to Entities die Spalte an, nach der sortiert werden soll. Der Code erstellt vor der Switch-Anweisung eine `IQueryable`-Variable, ändert sie in der Switch-Anweisung und ruft die `ToListAsync`-Methode nach der `switch`-Anweisung auf. Es wir keine Abfrage an die Datenbank gesendet, wenn Sie die `IQueryable`-Variablen erstellen und ändern. Die Abfrage wird nicht ausgeführt, bis Sie das `IQueryable`-Objekt in eine Sammlung konvertieren, indem Sie eine Methode aufrufen, z.B. die `ToListAsync`-Methode. Aus diesem Grund führt dieser Code zu einer einzelnen Abfrage, die bis zur `return View`-Anweisung nicht ausgeführt wird.

Dieser Code könnte mit einer großen Anzahl von Spalten ausführlich werden. [Das letzte Tutorial dieser Reihe](advanced.md#dynamic-linq) zeigt, wie Sie Code schreiben, mit dem Sie den Namen der `OrderBy`-Spalte an eine Zeichenfolgenvariablen übergeben können.

### <a name="add-column-heading-hyperlinks-to-the-student-index-view"></a>Hinzufügen von Spaltenüberschriftenlinks zur Studentenindexansicht

Ersetzen Sie den Code in *Views/Students/Index.cshtml* durch den folgenden Code, um Spaltenüberschriftenlinks hinzuzufügen. Die geänderten Zeilen werden hervorgehoben.

[!code-cshtml[](intro/samples/cu/Views/Students/Index2.cshtml?highlight=16,22)]

Dieser Code verwendet die Informationen in den `ViewData`-Eigenschaften zum Einrichten von Links mit den entsprechenden Abfragezeichenfolgenwerten.

Führen Sie die Anwendung aus, wählen Sie die Registerkarte **Students** (Studenten) aus, klicken Sie auf die Spaltenüberschriften **Last Name** (Nachname) und **Enrollment Date** (Anmeldedatum), um diese Sortierung zu überprüfen.

![Indexseite „Studenten“ in Reihenfolge der Namen](sort-filter-page/_static/name-order.png)

## <a name="add-a-search-box"></a>Hinzufügen eines Suchfelds

Wenn Sie eine Filterfunktion zur Studentenindexseite hinzufügen möchten, dann fügen Sie ein Textfeld und die Schaltfläche „Senden“ zur Ansicht hinzu, und führen Sie die entsprechenden Änderungen in der `Index`-Methode aus. Sie können eine Zeichenfolge in das Textfeld für Vor- und Nachnamen eingeben, um eine Suche zu starten.

### <a name="add-filtering-functionality-to-the-index-method"></a>Hinzufügen der Filterfunktion zur Indexmethode

Ersetzen Sie in *StudentsController.cs* die `Index`-Methode durch den folgenden Code (die Änderungen sind hervorgehoben).

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_SortFilter&highlight=1,5,9-13)]

Sie haben einen `searchString`-Parameter zur `Index`-Methode hinzugefügt. Der Zeichenfolgenwert für die Suche wird aus einem Textfeld empfangen, das Sie zur Indexansicht hinzufügen. Sie haben ebenfalls eine Where-Klausel zur LINQ-Anweisung hinzugefügt, die nur Studenten auswählt, deren Vor- oder Nachnamen die zu suchende Zeichenfolge enthält. Die Anweisung, die die Where-Klausel hinzufügt, wird nur ausgeführt, wenn nach einem Wert gesucht wird.

> [!NOTE]
> Wenn Sie die `Where`-Methode auf einem `IQueryable`-Objekt aufrufen, wird der Filter auf dem Server verarbeitet. In einigen Szenarios rufen Sie möglicherweise die `Where`-Methode als Erweiterungsmethode für eine speicherinterne Sammlung auf. (Angenommen, Sie ändern den Verweis auf `_context.Students`. Dann wird nicht mehr auf ein `DbSet`-EF, sondern auf eine Repository-Methode verwiesen, die eine `IEnumerable`-Sammlung zurückgibt.) Das Ergebnis wäre normalerweise identisch, aber in einigen Fällen kann es unterschiedlich ausfallen.
>
>Die .NET Framework-Implementierung der `Contains`-Methode führt beispielsweise standardmäßig einen Vergleich unter Beachtung der Groß-/Kleinschreibung durch. Aber in SQL Server wird dies durch die Sortierungseinstellung der SQL Server-Instanz bestimmt. Diese Einstellung berücksichtigt die Groß-/Kleinschreibung standardmäßig nicht. Sie könnten die `ToUpper`-Methode aufrufen, wenn die Groß-/Kleinschreibung bei der Durchführung des Tests explizit nicht beachtet werden soll:  *Where(s => s.LastName.ToUpper().Contains(searchString.ToUpper())* . Das würde sicherstellen, dass die Ergebnisse gleich bleiben, wenn Sie den Code später ändern, um ein Repository zu verwenden, das eine `IEnumerable`-Sammlung anstelle eines `IQueryable`-Objekts zurückgibt. (Beim Aufrufen der `Contains`-Methode einer `IEnumerable`-Sammlung erhalten Sie die .NET Framework-Implementierung. Wenn Sie sie auf einem `IQueryable`-Objekt aufrufen, erhalten Sie die Implementierung des Datenanbieters.) Es gibt jedoch eine Leistungseinbuße für diese Lösung. Der `ToUpper`-Code würde eine Funktion in die WHERE-Klausel der TSQL SELECT-Anweisung setzen. Die würde verhindern, dass der Optimierer einen Index verwendet. Da SQL die Groß-/Kleinschreibung hauptsächlich nicht berücksichtigt, wird empfohlen, den `ToUpper`-Code zu vermeiden, bis die Migration zu einem Datenspeicher erfolgt ist, der die Groß-/Kleinschreibung beachtet.

### <a name="add-a-search-box-to-the-student-index-view"></a>Hinzufügen eines Suchfelds zur Studentenindexansicht

Fügen Sie in *Views/Student/Index.cshtml* den hervorgehobenen Code unmittelbar vor dem Tag „Tabelle öffnen“ hinzu, um eine Beschriftung, ein Textfeld und eine **Suche**-Schaltfläche zu erstellen.

[!code-cshtml[](intro/samples/cu/Views/Students/Index3.cshtml?range=9-23&highlight=5-13)]

Dieser Code verwendet das [Taghilfsprogramm](xref:mvc/views/tag-helpers/intro) `<form>`, um das Suchtextfeld und die Schaltfläche hinzuzufügen. Das Taghilfsprogramm `<form>` sendet standardmäßig Formulardaten mit einem POST, was bedeutet, dass Parameter als Abfragezeichenfolgen im Hauptteil der HTTP-Nachricht und nicht in der URL übergeben werden. Bei der Angabe von HTTP GET werden die Formulardaten als Abfragezeichenfolgen an die URL übergeben. Dadurch können Benutzer ein Lesezeichen für die URL erstellen. Die W3C-Richtlinien empfehlen die Verwendung eines GET-Vorgangs, wenn die Aktion nicht zu einem Update führt.

Führen Sie die Anwendung aus, wählen Sie die Registerkarte **Studenten**, geben Sie eine Suchzeichenfolge ein, und klicken Sie auf „Suchen“, um die Funktionsweise des Filters zu überprüfen.

![Indexseite Studenten mit Filtern](sort-filter-page/_static/filtering.png)

Beachten Sie, dass die URL die Suchzeichenfolge enthält.

```html
http://localhost:5813/Students?SearchString=an
```

Wenn Sie diese Seite kennzeichnen, erhalten Sie die gefilterte Liste bei der Verwendung von Lesezeichen. Wird `method="get"` zum `form`-Tag hinzugefügt, wird die Abfragezeichenfolge generiert.

Wenn Sie in dieser Phase auf einen Sortierlink in einer Spaltenüberschrift klicken, verlieren Sie den Filterwert, den Sie im Feld neben **Suchen** eingegeben haben. Dies soll im nächsten Abschnitt behoben werden.

## <a name="add-paging-to-students-index"></a>Hinzufügen von Paging zum „Students“-Index

Um die Pagingfunktionen zur Studentenindexseite hinzuzufügen, erstellen Sie eine `PaginatedList`-Klasse, die `Skip`- und `Take`-Anweisungen zum Filtern von Daten auf dem Server verwendet, anstatt immer alle Zeilen der Tabelle abzurufen. Dann nehmen Sie zusätzliche Änderungen der `Index`-Methode vor, und fügen Pagingschaltflächen zur `Index`-Ansicht hinzu. Die folgende Abbildung zeigt die Pagingschaltflächen.

![Indexseite „Studenten“ mit Paginglinks](sort-filter-page/_static/paging.png)

Erstellen Sie `PaginatedList.cs` im Projektordner. Ersetzen Sie den Vorlagencode dann durch den folgenden Code.

[!code-csharp[](intro/samples/cu/PaginatedList.cs)]

Die `CreateAsync`-Methode in diesem Code akzeptiert die Seitengröße und die Seitenzahl und wendet die entsprechenden `Skip`- und `Take`-Anweisungen auf `IQueryable` an. Wenn `ToListAsync` auf `IQueryable` aufgerufen wird, wird eine Liste zurückgegeben, die nur die angeforderte Seite enthält. Die Eigenschaften `HasPreviousPage` und `HasNextPage` dienen zum Aktivieren oder Deaktivieren der Pagingschaltflächen **Zurück** und **Weiter**.

Ein `CreateAsync`-Methode wird anstelle eines Konstruktors verwendet, um das `PaginatedList<T>`-Objekt zu erstellen, da die Konstruktoren keinen asynchronen Code ausführen können.

## <a name="add-paging-to-index-method"></a>Hinzufügen von Paging zur „Index“-Methode

Ersetzen Sie in *StudentsController.cs* die `Index`-Methode mit dem folgenden Code.

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_SortFilterPage&highlight=1-5,7,11-18,45-46)]

Dieser Code fügt ein Parameter für die Seitenanzahl, die aktuelle Sortierreihenfolge und den aktuellen Filter zur Methodensignatur hinzu.

```csharp
public async Task<IActionResult> Index(
    string sortOrder,
    string currentFilter,
    string searchString,
    int? pageNumber)
```

Wenn die Seite zum ersten Mal angezeigt wird oder wenn der Benutzer nicht auf einen Paging- oder Sortierlink geklickt hat, werden alle Parameter gleich 0 (null) sein.  Wenn auf ein Paginglink geklickt wird, enthält die Seitenvariable die anzuzeigende Seitenzahl.

Das `ViewData`-Element „CurrentSort“ stellt die aktuelle Sortierreihenfolge für die Ansicht bereit. Diese Sortierreihenfolge muss in den Paginglinks enthalten sein, damit sie beim Pagingvorgang identisch bleibt.

Das `ViewData`-Element „CurrentFilter“ stellt der Ansicht die aktuelle Filterzeichenfolge zur Verfügung. Dieser Wert muss in den Paginglinks enthalten sein, damit die Filtereinstellungen während des Pagingvorgangs beibehalten werden, und er muss im Textfeld wiederhergestellt werden, wenn die Seite erneut angezeigt wird.

Wenn die Suchzeichenfolge während des Pagingvorgangs geändert wird, muss die Seite auf 1 zurückgesetzt werden, da der neue Filter andere Daten anzeigen kann. Die Suchzeichenfolge wird geändert, wenn ein Wert in das Textfeld eingegeben und auf die Schaltfläche „Senden“ geklickt wird. In diesem Fall ist der `searchString`-Parameter nicht gleich 0 (null).

```csharp
if (searchString != null)
{
    pageNumber = 1;
}
else
{
    searchString = currentFilter;
}
```

Am Ende der `Index`-Methode konvertiert die `PaginatedList.CreateAsync`-Methode die Abfrage der Studentendaten in eine einzelne Seite in einem Sammlungstyp, der Pagingvorgänge unterstützt. Diese einzelnen Seite mit Studentendaten wird dann an die Ansicht übergeben.

```csharp
return View(await PaginatedList<Student>.CreateAsync(students.AsNoTracking(), pageNumber ?? 1, pageSize));
```

Die `PaginatedList.CreateAsync`-Methode nimmt eine Seitenanzahl. Die zwei Fragezeichen stellen den Nullzusammensetzungsoperator dar. Der Nullzusammensetzungsoperator definiert einen Standardwert für einen Nullable-Typ. Der `(pageNumber ?? 1)`-Ausdruck bedeutet, dass `pageNumber` zurückgegeben wird, wenn dies über einen Wert verfügt, oder 1, wenn `pageNumber` gleich 0 (null) ist.

## <a name="add-paging-links"></a>Hinzufügen von Paginglinks

Ersetzen Sie in *Views/Students/Index.cshtml* den vorhandenen Code durch den folgenden Code. Die Änderungen werden hervorgehoben.

[!code-cshtml[](intro/samples/cu/Views/Students/Index.cshtml?highlight=1,27,30,33,61-79)]

Die `@model`-Anweisung am oberen Rand der Seite gibt an, dass die Ansicht nun ein `PaginatedList<T>`-Objekt anstelle eines `List<T>`-Objekts aufruft.

Die Spaltenüberschriftenlinks verwenden die Abfragezeichenfolge, um die aktuelle Suchzeichenfolge an den Controller zu übergeben, damit Benutzer Filterergebnisse sortieren können:

```html
<a asp-action="Index" asp-route-sortOrder="@ViewData["DateSortParm"]" asp-route-currentFilter ="@ViewData["CurrentFilter"]">Enrollment Date</a>
```

Die Pagingschaltflächen werden durch Taghilfsprogramme angezeigt:

```html
<a asp-action="Index"
   asp-route-sortOrder="@ViewData["CurrentSort"]"
   asp-route-pageNumber="@(Model.PageIndex - 1)"
   asp-route-currentFilter="@ViewData["CurrentFilter"]"
   class="btn btn-default @prevDisabled">
   Previous
</a>
```

Führen Sie die Anwendung aus. Wechseln Sie zur Studentenseite.

![Indexseite „Studenten“ mit Paginglinks](sort-filter-page/_static/paging.png)

Klicken Sie auf die Paginglinks in verschiedenen Sortierreihenfolgen, um sicherzustellen, dass die Paging funktioniert. Geben Sie dann eine Suchzeichenfolge ein. Probieren Sie Paging erneut aus, um sicherzustellen, dass sie auch mit Sortier- und Filtervorgängen ordnungsgemäß funktioniert.

## <a name="create-an-about-page"></a>Erstellen einer Infoseite

Auf der **Infoseite** der Contoso University wird angezeigt, wie viele Studenten sich an welchem Datum angemeldet haben. Das erfordert Gruppieren und einfache Berechnungen dieser Gruppen. Um dies zu erreichen, ist Folgendes erforderlich:

* Erstellen Sie eine Ansichtsmodellklasse für die Daten, die Sie an die Ansicht übergeben müssen.
* Erstellen Sie die About-Methode im Home-Controller.
* Erstellen Sie die Ansicht „Info“.

### <a name="create-the-view-model"></a>Erstellen des Ansichtsmodells

Erstellen Sie im Ordner *Models* (Modelle) den Ordner *SchoolViewModels*.

Fügen Sie im neuen Ordner die Klassendatei *EnrollmentDateGroup.cs* hinzu. Ersetzen Sie den Vorlagencode durch den folgenden Code:

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/EnrollmentDateGroup.cs)]

### <a name="modify-the-home-controller"></a>Ändern des Home-Controllers

Fügen Sie in *HomeController.cs* am Anfang der Datei die folgenden Anweisungen hinzu:

[!code-csharp[](intro/samples/cu/Controllers/HomeController.cs?name=snippet_Usings1)]

Fügen Sie eine Klassenvariable für den Datenbankkontext hinzu, unmittelbar nachdem Sie die geschweifte Klammer für die Klasse geöffnet haben. Rufen Sie eine Instanz des Kontexts von ASP.NET Core DI auf:

[!code-csharp[](intro/samples/cu/Controllers/HomeController.cs?name=snippet_AddContext&highlight=3,5,7)]

Fügen Sie eine `About`-Methode mit dem folgenden Code hinzu:

[!code-csharp[](intro/samples/cu/Controllers/HomeController.cs?name=snippet_UseDbSet)]

Die LINQ-Anweisung gruppiert die Studentenentitäten nach Anmeldedatum, berechnet die Anzahl der Entitäten in jeder Gruppe und speichert die Ergebnisse in einer Sammlung von `EnrollmentDateGroup`-Ansichtsmodellobjekten.

### <a name="create-the-about-view"></a>Erstellen der Ansicht „Info“

Fügen Sie eine *Views/Home/About.cshtml*-Datei mit dem folgenden Code hinzu:

[!code-cshtml[](intro/samples/cu/Views/Home/About.cshtml)]

Führen Sie die Anwendung aus, und wechseln Sie zur Infoseite. Die Anzahl der Studenten für die jeweiligen Anmeldedatumswerte wird in einer Tabelle angezeigt.

## <a name="get-the-code"></a>Abrufen des Codes

[Download or view the completed app (Herunterladen oder anzeigen der vollständigen App).](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial:

> [!div class="checklist"]
> * Spaltensortierungslinks wurden hinzugefügt
> * Ein Suchfeld wurde hinzugefügt
> * Paging wurde dem „Students“-Index hinzugefügt
> * Paging wurde der „Index“-Methode hinzugefügt
> * Paginglinks wurden hinzugefügt
> * Infoseite wurde erstellt

Fahren Sie mit dem nächsten Tutorial fort, um zu erfahren, wie Sie mithilfe von Migrationen Datenmodelländerungen verarbeiten.

> [!div class="nextstepaction"]
> [Weiter: Tutorial: Verwenden des Migrationsfeatures: ASP.NET MVC mit EF Core](migrations.md)
