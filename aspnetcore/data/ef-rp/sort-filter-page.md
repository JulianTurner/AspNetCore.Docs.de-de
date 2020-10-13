---
title: 'Teil 3: Razor Pages mit EF Core in ASP.NET Core – Sortieren, Filtern und Paging'
author: rick-anderson
description: Dies ist Teil 3 der Tutorialreihe zu Razor Pages und dem Entity Framework.
ms.author: riande
ms.custom: mvc
ms.date: 07/22/2019
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
uid: data/ef-rp/sort-filter-page
ms.openlocfilehash: e01704cb10c88f3e9442e74034f5e5d39787f300
ms.sourcegitcommit: e519d95d17443abafba8f712ac168347b15c8b57
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/02/2020
ms.locfileid: "91653892"
---
# <a name="part-3-no-locrazor-pages-with-ef-core-in-aspnet-core---sort-filter-paging"></a>Teil 3: Razor Pages mit EF Core in ASP.NET Core – Sortieren, Filtern und Paging

Von [Tom Dykstra](https://github.com/tdykstra), [Rick Anderson](https://twitter.com/RickAndMSFT) und [Jon P. Smith](https://twitter.com/thereformedprog)

[!INCLUDE [about the series](~/includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

In diesem Tutorial werden Funktionen zum Sortieren und Filtern sowie für Paging zur Student-Seiten hinzugefügt.

Die folgende Abbildung zeigt eine fertige Seite. Die Spaltenüberschriften sind Links, die zum Sortieren der Spalte angeklickt werden können. Wiederholtes Klicken auf eine Spaltenüberschrift schaltet zwischen aufsteigender und absteigender Sortierreihenfolge um.

![Indexseite „Studenten“](sort-filter-page/_static/paging30.png)

## <a name="add-sorting"></a>Hinzufügen von Sortierung

Ersetzen Sie den Code in *Pages/Students/Index.cshtml.cs* durch den folgenden Code, um Sortierung hinzuzufügen.

[!code-csharp[Main](intro/samples/cu30snapshots/3-sorting/Pages/Students/Index1.cshtml.cs?name=snippet_All)]

Der vorangehende Code:

* Er erfordert das Hinzufügen von `using System;`.
* Fügt Eigenschaften hinzu, die die Sortierparameter enthalten.
* Ändert den Namen der `Student`-Eigenschaft in `Students`.
* Ersetzt den Code in der `OnGetAsync`-Methode.

Die `OnGetAsync`-Methode empfängt einen `sortOrder`-Parameter aus der Abfragezeichenfolge in der URL. Die URL und die Abfragezeichenfolge werden vom [Anchor-Taghilfsprogramm](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) generiert.

Der `sortOrder`-Parameter ist entweder `Name` oder `Date`. Dem Parameter `sortOrder` folgt optional `_desc` zur Angabe einer absteigenden Reihenfolge. Standardmäßig wird eine aufsteigende Sortierreihenfolge verwendet.

Wenn die Indexseite über den Link **Studenten** angefordert wird, gibt es keine Abfragezeichenfolge. Die Studenten werden in aufsteigender Reihenfolge nach Nachnamen angezeigt. Die aufsteigende Reihenfolge nach Nachnamen ist `default` in der `switch`-Anweisung. Wenn der Benutzer auf einen Link in einer Spaltenüberschrift klickt, wird der entsprechende `sortOrder`-Wert im Abfragezeichenfolgenwert bereitgestellt.

`NameSort` und `DateSort` werden auf der Razor-Seite verwendet, um die Hyperlinks in den Spaltenüberschriften mit den entsprechenden Abfragezeichenfolgenwerten zu konfigurieren:

[!code-csharp[Main](intro/samples/cu30snapshots/3-sorting/Pages/Students/Index1.cshtml.cs?name=snippet_Ternary)]

Der Code verwendet den bedingten C#-Operator [?:](/dotnet/csharp/language-reference/operators/conditional-operator). Der Operator `?:` ist ein ternärer Operator (er nimmt drei Operanden an). Die erste Zeile gibt an, dass wenn `sortOrder` NULL oder leer ist, `NameSort` auf `name_desc` festgelegt wird. Wenn `sortOrder`***nicht*** NULL oder leer ist, wird `NameSort` auf eine leere Zeichenfolge festgelegt.

Durch diese beiden Anweisungen können auf der Seite die Hyperlinks in den Spaltenüberschriften wie folgt festgelegt werden:

| Aktuelle Sortierreihenfolge   | Hyperlink „Nachname“ | Hyperlink „Datum“ |
|:--------------------:|:-------------------:|:--------------:|
| Nachname (aufsteigend)  | descending          | ascending      |
| Nachname (absteigend) | ascending           | ascending      |
| Datum (aufsteigend)       | ascending           | descending     |
| Datum (absteigend)      | ascending           | ascending      |

Die Methode gibt über LINQ to Entities die Spalte an, nach der sortiert werden soll. Der Code initialisiert das Objekt `IQueryable<Student>` vor der Switch-Anweisung und ändert es in die Switch-Anweisung:

[!code-csharp[Main](intro/samples/cu30snapshots/3-sorting/Pages/Students/Index1.cshtml.cs?name=snippet_IQueryable)]

Wenn ein Objekt vom Typ `IQueryable` erstellt oder geändert wird, wird keine Abfrage an die Datenbank gesendet. Die Abfrage wird erst dann ausgeführt, wenn das Objekt `IQueryable` in eine Sammlung konvertiert wurde. `IQueryable` werden in eine Sammlung konvertiert, indem eine Methode wie z.B. `ToListAsync` aufgerufen wird. Aus diesem Grund führt der `IQueryable`-Code zu einer einzelnen Abfrage, die bis zu folgender Anweisung nicht ausgeführt wird:

[!code-csharp[Main](intro/samples/cu30snapshots/3-sorting/Pages/Students/Index1.cshtml.cs?name=snippet_SortOnlyRtn)]

`OnGetAsync` könnte mit einer Vielzahl von sortierbaren Spalten ausführlich werden. Informationen zu einer alternativen Möglichkeit zum Programmieren dieser Funktionalität finden Sie unter [Verwenden von dynamischem LINQ zum Vereinfachen von Code](xref:data/ef-mvc/advanced#dynamic-linq) in der MVC-Version dieser Tutorialreihe.

### <a name="add-column-heading-hyperlinks-to-the-student-index-page"></a>Hinzufügen von Hyperlinks auf Spaltenüberschriften zur Studentenindexseite

Ersetzen Sie den Code in *Students/Index.cshtml* durch folgenden Code. Die Änderungen werden hervorgehoben.

[!code-cshtml[Main](intro/samples/cu30snapshots/3-sorting/Pages/Students/Index1.cshtml?highlight=5,8,17-19,22,25-27,33)]

Der vorangehende Code:

* Fügt Hyperlinks zu den Spaltenüberschriften `LastName` und `EnrollmentDate` hinzu.
* Verwendet die Informationen in `NameSort` und `DateSort` zum Einrichten von Hyperlinks mit den Werten der aktuellen Sortierreihenfolge.
* Ändert die Seitenüberschrift aus „Index“ in „Students“.
* Ändert `Model.Student` in `Model.Students`.

So überprüfen Sie die Funktionsfähigkeit der Sortierung:

* Führen Sie die App aus, und klicken Sie auf die Registerkarte **Studenten**.
* Klicken Sie auf die Spaltenüberschriften.

## <a name="add-filtering"></a>Hinzufügen von Filterung

So fügen Sie Filter zur Indexseite „Studenten“ hinzu:

* Ein Textfeld und die Schaltfläche „Senden“ werden zur Razor Page hinzugefügt. Im Textfeld wird eine Suchzeichenfolge für den Vor- oder Nachnamen bereitgestellt.
* Das Seitenmodell wird aktualisiert, damit der Wert im Textfeld verwendet wird.

### <a name="update-the-ongetasync-method"></a>Aktualisieren der OnGetAsync-Methode

Ersetzen Sie den Code in *Students/Index.cshtml.cs* durch den folgenden Code, um Filterung hinzuzufügen:

[!code-csharp[Main](intro/samples/cu30snapshots/3-sorting/Pages/Students/Index2.cshtml.cs?name=snippet_All&highlight=17,22,26-30)]

Der vorangehende Code:

* Fügt der `OnGetAsync`-Methode den `searchString`-Parameter hinzu und speichert den Parameterwert in der `CurrentFilter`-Eigenschaft. Der Suchzeichenfolgenwert wird aus einem Textfeld empfangen, das im nächsten Abschnitt hinzugefügt wird.
* Fügt der LINQ-Anweisung eine `Where`-Klausel hinzu. In der `Where`-Klausel werden nur die Studenten ausgewählt, deren Vor- oder Nachname die Suchzeichenfolge enthält. Die LINQ-Anweisung wird nur dann ausgeführt, wenn ein Wert vorhanden ist, nach dem gesucht werden soll.

### <a name="iqueryable-vs-ienumerable"></a>IQueryable im Vergleich zu IEnumerable

Der Code ruft die Methode <xref:System.Linq.Queryable.Where%2A> in einem `IQueryable`-Objekt auf, und der Filter wird auf dem Server verarbeitet. In einigen Szenarios ruft die App möglicherweise die Methode `Where` als Erweiterungsmethode für eine Auflistung im Speicher auf. Angenommen, `_context.Students` wird beispielsweise von EF Core `DbSet` in eine Repositorymethode geändert, die eine `IEnumerable`-Sammlung zurückgibt. Das Ergebnis wäre normalerweise identisch, aber in einigen Fällen kann es unterschiedlich ausfallen.

Bei der .NET Framework-Implementierung von `Contains` wird beispielsweise standardmäßig ein Vergleich unter Beachtung der Groß-/Kleinschreibung vorgenommen. In SQL Server wird die Unterscheidung von Groß-/Kleinschreibung bei `Contains` durch die Einstellung für die Sortierung der SQL Server-Instanz bestimmt. Bei SQL Server wird die Groß-/Kleinschreibung standardmäßig nicht beachtet. Für SQLite wird standardmäßig zwischen Groß-/Kleinschreibung unterschieden. `ToUpper` könnte aufgerufen werden, wenn die Groß-/Kleinschreibung bei der Durchführung des Tests explizit nicht beachtet werden soll:

```csharp
Where(s => s.LastName.ToUpper().Contains(searchString.ToUpper())`
```

Der vorstehende Code würde sicherstellen, dass der Filter nicht zwischen Groß-/Kleinschreibung unterscheidet, auch wenn die `Where`-Methode für `IEnumerable` aufgerufen oder für SQLite ausgeführt wird.

Wenn `Contains` in einer `IEnumerable`-Sammlung verwendet wird, wird die .NET Core-Implementierung verwendet. Wenn `Contains` in einem `IQueryable`-Objekt aufgerufen wird, wird die Datenbankimplementierung verwendet.

Der Aufruf von `Contains` für `IQueryable` ist in der Regel aus Leistungsgründen vorzuziehen. Mit `IQueryable` wird die Filterung vom Datenbankserver ausgeführt. Wenn zuerst ein `IEnumerable` erstellt wird, müssen alle Zeilen vom Datenbankserver zurückgegeben werden.

Beim Aufrufen von `ToUpper` kommt es zu Leistungseinbußen. Der `ToUpper`-Code in der WHERE-Klausel der TSQL SELECT-Anweisung eine Funktion hinzu. Durch die hinzugefügte Funktion wird verhindert, dass der Optimierer einen Index verwendet. Da in SQL die Groß-/Kleinschreibung beachtet wird, sollte `ToUpper` möglichst nicht aufgerufen werden, wenn dies nicht notwendig ist.

Weitere Informationen finden Sie unter [Verwenden von Abfragen ohne Berücksichtigung der Groß-/Kleinschreibung mit dem SQLite-Anbieter](https://github.com/aspnet/EntityFrameworkCore/issues/11414).

### <a name="update-the-no-locrazor-page"></a>Aktualisieren der Razor-Seite

Ersetzen Sie den Code in *Pages/Students/Index.cshtml*, um eine Schaltfläche **Search** (Suchen) hinzuzufügen.

[!code-cshtml[Main](intro/samples/cu30snapshots/3-sorting/Pages/Students/Index2.cshtml?highlight=14-23)]

Der vorangehende Code verwendet das `<form>`-[Taghilfsprogramm](xref:mvc/views/tag-helpers/intro), um das Suchtextfeld und die Schaltfläche hinzuzufügen. Das `<form>`-Taghilfsprogramm sendet Formulardaten standardmäßig mit einer POST-Anforderung. Mit der POST-Anforderung werden die Parameter im HTTP-Nachrichtentext und nicht in der URL übergeben. Bei Verwendung einer HTTP GET-Anforderung werden die Formulardaten als Abfragezeichenfolgen in der URL übergeben. Durch die Übergabe der Daten mit Abfragezeichenfolgen können Benutzer die URL als Lesezeichen speichern. In den [W3C-Richtlinien](https://www.w3.org/2001/tag/doc/whenToUseGet.html) wird die Verwendung einer GET-Anforderung empfohlen, wenn die Aktion nicht zu einem Update führt.

Testen der App:

* Klicken Sie auf die Registerkarte **Studenten**, und geben Sie eine Suchzeichenfolge ein. Wenn Sie SQLite verwenden, wird für den Filter nur denn nicht zwischen Groß-/Kleinschreibung unterschieden, wenn Sie den zuvor gezeigten optionalen `ToUpper`-Code implementiert haben.

* Klicken Sie auf **Suchen**.

Beachten Sie, dass die URL die Suchzeichenfolge enthält. Zum Beispiel:

```browser-address-bar
https://localhost:5001/Students?SearchString=an
```

Wenn die Seite als Lesezeichen gespeichert wird, enthält das Lesezeichen die URL der Seite und die `SearchString`-Abfragezeichenfolge. Durch `method="get"` im `form`-Tag wurde die Abfragezeichenfolge generiert.

Aktuell geht der Filterwert aus dem Feld **Suchen** verloren, wenn ein Link zur Sortierung der Spaltenüberschrift ausgewählt wird. Das Problem des verlorenen Filterwerts wird im nächsten Abschnitt behoben.

## <a name="add-paging"></a>Hinzufügen von Paging

In diesem Abschnitt wird eine `PaginatedList`-Klasse zur Unterstützung von Paging erstellt. Die `PaginatedList`-Klasse verwendet `Skip`- und `Take`-Anweisungen zum Filtern von Daten auf dem Server, statt alle Zeilen der Tabelle abzurufen. Die folgende Abbildung zeigt die Pagingschaltflächen.

![Indexseite „Studenten“ mit Paginglinks](sort-filter-page/_static/paging30.png)

### <a name="create-the-paginatedlist-class"></a>Erstellen der PaginatedList-Klasse

Erstellen Sie im Projektordner `PaginatedList.cs` mit folgendem Code:

[!code-csharp[Main](intro/samples/cu30/PaginatedList.cs)]

Die Methode `CreateAsync` im vorangehenden Code übernimmt die Seitengröße und die Seitenzahl und wendet die entsprechenden `Skip`- und `Take`-Anweisungen auf das Objekt `IQueryable` an. Wenn `ToListAsync` im Objekt `IQueryable` aufgerufen wird, wird eine Liste zurückgegeben, die nur die angeforderte Seite enthält. Die Eigenschaften `HasPreviousPage` und `HasNextPage` dienen zum Aktivieren oder Deaktivieren der Schaltflächen **Zurück** und **Weiter** für das Paging.

Die Methode `CreateAsync` dient zum Erstellen des Objekts `PaginatedList<T>`. Ein Konstruktor kann das Objekt `PaginatedList<T>` nicht erstellen. Konstruktoren können keinen asynchronen Code ausführen.

### <a name="add-paging-to-the-pagemodel-class"></a>Hinzufügen von Paging zur PageModel-Klasse

Ersetzen Sie den Code in *Students/Index.cshtml.cs*, um Paging hinzuzufügen.

[!code-csharp[Main](intro/samples/cu30/Pages/Students/Index.cshtml.cs?name=snippet_All&highlight=15-20,23-30,57-59)]

Der vorangehende Code:

* Ändert den Typ der `Students`-Eigenschaft aus `IList<Student>` in `PaginatedList<Student>`.
* Fügt den Seitenindex, die aktuelle `sortOrder` und den `currentFilter` zur `OnGetAsync`-Methodensignatur hinzu.
* Er speichert die Sortierreihenfolge in der `CurrentSort`-Eigenschaft.
* Setzt den Seitenindex auf 1 zurück, wenn eine neue Suchzeichenfolge vorhanden ist.
* Verwendet die `PaginatedList`-Klasse zum Abrufen von Student-Entitäten.
* Er legt `pageSize` auf 3 fest. Eine echte App verwendet [Configuration](xref:fundamentals/configuration/index)(Konfiguration), um den Wert für die Seitengröße festzulegen.

Alle Parameter, die `OnGetAsync` empfängt, sind NULL, wenn:

* Die Seite über den Link **Studenten** aufgerufen wird.
* Der Benutzer auf keinen Link für das Paging oder die Sortierung geklickt hat.

Wenn auf einen Paging-Link geklickt wird, enthält die Seitenindexvariable die anzuzeigende Seitenzahl.

Die `CurrentSort`-Eigenschaft stellt die Razor-Seite mit der aktuellen Sortierreihenfolge bereit. Die aktuelle Sortierreihenfolge muss in den Paging-Links enthalten sein, damit die Sortierreihenfolge während des Pagings beibehalten wird.

Die `CurrentFilter`-Eigenschaft stellt die Razor-Seite mit der aktuellen Filterzeichenfolge bereit. Der `CurrentFilter`-Wert:

* Muss in den Paging-Links enthalten sein, damit die Filtereinstellungen während des Pagings beibehalten werden.
* Muss im Textfeld wiederhergestellt werden, wenn die Seite erneut angezeigt wird.

Wenn die Suchzeichenfolge während des Pagings geändert wird, wird die Seite auf 1 zurückgesetzt. Die Seite muss auf 1 zurückgesetzt werden, da der neue Filter andere Daten anzeigen kann. Folgendes gilt, wenn ein Suchwert eingegeben und auf **Senden** geklickt wird:

  * Die Suchzeichenfolge wird geändert.
  * Der Parameter `searchString` ist nicht NULL.

  Die Methode `PaginatedList.CreateAsync` konvertiert die Studentenabfrage in eine einzelne Studentenseite in einem Sammlungstyp, der das Paging unterstützt. Diese einzelne Studentenseite wird an die Razor Page übergeben.

  Die zwei Fragezeichen nach `pageIndex` im `PaginatedList.CreateAsync`-Aufruf stellen den [NULL-Sammeloperator](/dotnet/csharp/language-reference/operators/null-conditional-operator) dar. Der NULL-Sammeloperator definiert einen Standardwert für einen auf NULL festlegbaren Typ. Der Ausdruck `pageIndex ?? 1` gibt den Wert `pageIndex` zurück, wenn er über einen Wert verfügt, andernfalls wird 1 zurückgegeben.

### <a name="add-paging-links-to-the-no-locrazor-page"></a>Hinzufügen von Paging-Links zur Razor-Seite

Ersetzen Sie den Code in *Students/Index.cshtml* durch den folgenden Code. Die Änderungen werden hervorgehoben:

[!code-cshtml[Main](intro/samples/cu30/Pages/Students/Index.cshtml?highlight=29-32,38-41,69-87)]

In den Links der Spaltenüberschriften wird die Abfragezeichenfolge verwendet, um die aktuelle Suchzeichenfolge an die Methode `OnGetAsync` zu übergeben:

[!code-cshtml[Main](intro/samples/cu30/Pages/Students/Index.cshtml?range=29-32)]

Die Pagingschaltflächen werden durch Taghilfsprogramme angezeigt:

[!code-cshtml[Main](intro/samples/cu30/Pages/Students/Index.cshtml?range=73-87)]

Führen Sie die App aus, und navigieren Sie zur Studentenseite.

* Klicken Sie in verschiedenen Sortierreihenfolgen auf die Paging-Links, um sicherzustellen, dass das Paging funktioniert.
* Geben Sie eine Suchzeichenfolge ein und versuchen Sie, das Paging durchzuführen, um sicherzustellen, dass das Paging bei Sortier- und Filtervorgängen ordnungsgemäß funktioniert.

![Indexseite „Studenten“ mit Paginglinks](sort-filter-page/_static/paging30.png)

## <a name="add-grouping"></a>Hinzufügen von Gruppierung

In diesem Abschnitt wird eine Seite „About“ (Info) erstellt, auf der angezeigt wird, wie viele Studenten sich für jedes Registrierungsdatum registriert haben. Bei dem Update wird eine Gruppierung verwendet, und es umfasst die folgenden Schritte:

* Erstellen Sie ein Ansichtsmodells für die auf der Seite **About** (Info) verwendeten Daten.
* Das Aktualisieren der Info-Seite, um das Ansichtsmodell zu verwenden.

### <a name="create-the-view-model"></a>Erstellen des Ansichtsmodells

Erstellen Sie einen Ordner *Models/SchoolViewModels*.

Erstellen Sie *SchoolViewModels/EnrollmentDateGroup.cs* mit dem folgenden Code:

[!code-csharp[Main](intro/samples/cu30/Models/SchoolViewModels/EnrollmentDateGroup.cs)]

### <a name="create-the-no-locrazor-page"></a>Erstellen der Razor-Seite

Fügen Sie eine Datei *Pages/About.cshtml* mit dem folgenden Code hinzu:

[!code-cshtml[Main](intro/samples/cu30/Pages/About.cshtml)]

### <a name="create-the-page-model"></a>Erstellen des Seitenmodells

Aktualisieren Sie die Datei *Pages/About.cshtml.cs* mit folgendem Code:

[!code-csharp[Main](intro/samples/cu30/Pages/About.cshtml.cs)]

Die LINQ-Anweisung gruppiert die Studentenentitäten nach Anmeldedatum, berechnet die Anzahl der Entitäten in jeder Gruppe und speichert die Ergebnisse in einer Sammlung von `EnrollmentDateGroup`-Ansichtsmodellobjekten.

Führen Sie die App aus, und navigieren Sie zur Seite „Info“. Die Anzahl der Studenten für die jeweiligen Anmeldedatumswerte wird in einer Tabelle angezeigt.

![Seite „Info“](sort-filter-page/_static/about30.png)

## <a name="next-steps"></a>Nächste Schritte

Im nächsten Tutorial verwendet die App Migrationen zum Aktualisieren des Datenmodells.

> [!div class="step-by-step"]
> [Vorheriges Tutorial](xref:data/ef-rp/crud)
> [Nächstes Tutorial](xref:data/ef-rp/migrations)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

In diesem Tutorial werden die Funktionen zum Sortieren, Filtern, Gruppieren und Paging hinzugefügt.

Die folgende Abbildung zeigt eine fertige Seite. Die Spaltenüberschriften sind Links, die zum Sortieren der Spalte angeklickt werden können. Durch wiederholtes Klicken auf eine Spaltenüberschrift wird zwischen aufsteigender und absteigender Sortierreihenfolge gewechselt.

![Indexseite „Studenten“](sort-filter-page/_static/paging.png)

Wenn nicht zu lösende Probleme auftreten, laden Sie die [fertige App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) herunter.

## <a name="add-sorting-to-the-index-page"></a>Hinzufügen einer Sortierung zur Indexseite

Gehen Sie wie folgt vor, um Zeichenfolgen zu `PageModel` von *Students/Index.cshtml.cs* hinzuzufügen, damit die Sortierungsparameter darin enthalten sind:

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet1&highlight=10-13)]

Aktualisieren Sie `OnGetAsync` von *Students/Index.cshtml.cs* mit folgendem Code:

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortOnly)]

Der vorangehende Code erhält den Parameter `sortOrder` aus der Abfragezeichenfolge in der URL. Die URL (einschließlich der Abfragezeichenfolge) wird vom [Anchor-Taghilfsprogramm](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper
) generiert.

Der Parameter `sortOrder` ist entweder „Name“ oder „Datum“. Dem Parameter `sortOrder` folgt optional „_desc“ zur Angabe einer absteigenden Reihenfolge. Standardmäßig wird eine aufsteigende Sortierreihenfolge verwendet.

Wenn die Indexseite über den Link **Studenten** angefordert wird, gibt es keine Abfragezeichenfolge. Die Studenten werden in aufsteigender Reihenfolge nach Nachnamen angezeigt. Die aufsteigende Reihenfolge nach Nachnamen (FallThrough-Fall) ist in der `switch`-Anweisung die Standardeinstellung. Wenn der Benutzer auf einen Link in einer Spaltenüberschrift klickt, wird der entsprechende `sortOrder`-Wert im Abfragezeichenfolgenwert bereitgestellt.

`NameSort` und `DateSort` werden auf der Razor-Seite verwendet, um die Hyperlinks in den Spaltenüberschriften mit den entsprechenden Abfragezeichenfolgenwerten zu konfigurieren:

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortOnly&highlight=3-4)]

Der folgende Code enthält den bedingten [C#-Operator „?:“](/dotnet/csharp/language-reference/operators/conditional-operator):

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_Ternary)]

Die erste Zeile gibt an, dass wenn `sortOrder` NULL oder leer ist, `NameSort` auf „name_desc“ festgelegt wird. Wenn `sortOrder`**nicht** NULL oder leer ist, wird `NameSort` auf eine leere Zeichenfolge festgelegt.

`?: operator` ist auch als ternärer Operator bekannt.

Durch diese beiden Anweisungen können auf der Seite die Hyperlinks in den Spaltenüberschriften wie folgt festgelegt werden:

| Aktuelle Sortierreihenfolge | Hyperlink „Nachname“ | Hyperlink „Datum“ |
|:--------------------:|:-------------------:|:--------------:|
| Nachname (aufsteigend) | descending        | ascending      |
| Nachname (absteigend) | ascending           | ascending      |
| Datum (aufsteigend)       | ascending           | descending     |
| Datum (absteigend)      | ascending           | ascending      |

Die Methode gibt über LINQ to Entities die Spalte an, nach der sortiert werden soll. Der Code initialisiert das Objekt `IQueryable<Student>` vor der Switch-Anweisung und ändert es in die Switch-Anweisung:

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortOnly&highlight=6-999)]

 Wenn ein Objekt vom Typ `IQueryable` erstellt oder geändert wird, wird keine Abfrage an die Datenbank gesendet. Die Abfrage wird erst dann ausgeführt, wenn das Objekt `IQueryable` in eine Sammlung konvertiert wurde. `IQueryable` werden in eine Sammlung konvertiert, indem eine Methode wie z.B. `ToListAsync` aufgerufen wird. Aus diesem Grund führt der `IQueryable`-Code zu einer einzelnen Abfrage, die bis zu folgender Anweisung nicht ausgeführt wird:

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortOnlyRtn)]

`OnGetAsync` könnte mit einer Vielzahl von sortierbaren Spalten ausführlich werden.

### <a name="add-column-heading-hyperlinks-to-the-student-index-page"></a>Hinzufügen von Hyperlinks auf Spaltenüberschriften zur Studentenindexseite

Ersetzen Sie den Code in *Students/Index.cshtml* durch folgenden hervorgehobenen Code:

[!code-cshtml[](intro/samples/cu21/Pages/Students/Index2.cshtml?highlight=17-19,25-27)]

Der vorangehende Code:

* Fügt Hyperlinks zu den Spaltenüberschriften `LastName` und `EnrollmentDate` hinzu.
* Verwendet die Informationen in `NameSort` und `DateSort` zum Einrichten von Hyperlinks mit den Werten der aktuellen Sortierreihenfolge.

So überprüfen Sie die Funktionsfähigkeit der Sortierung:

* Führen Sie die App aus, und klicken Sie auf die Registerkarte **Studenten**.
* Klicken Sie auf **Nachname**.
* Klicken Sie auf **Anmeldedatum**.

So erhalten Sie ein besseres Verständnis des Codes:

* Legen Sie in *Students/Index.cshtml.cs* einen Breakpoint auf `switch (sortOrder)` fest.
* Fügen Sie für `NameSort` und `DateSort` ein Überwachungselement hinzu.
* Legen Sie in *Students/Index.cshtml* einen Breakpoint auf `@Html.DisplayNameFor(model => model.Student[0].LastName)` fest.

Führen Sie den Debugger schrittweise aus.

## <a name="add-a-search-box-to-the-students-index-page"></a>Hinzufügen eines Suchfelds zur Studentenindexseite

So fügen Sie Filter zur Indexseite „Studenten“ hinzu:

* Ein Textfeld und die Schaltfläche „Senden“ werden zur Razor Page hinzugefügt. Im Textfeld wird eine Suchzeichenfolge für den Vor- oder Nachnamen bereitgestellt.
* Das Seitenmodell wird aktualisiert, damit der Wert im Textfeld verwendet wird.

### <a name="add-filtering-functionality-to-the-index-method"></a>Hinzufügen der Filterfunktion zur Indexmethode

Aktualisieren Sie `OnGetAsync` von *Students/Index.cshtml.cs* mit folgendem Code:

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortFilter&highlight=1,5,9-13)]

Der vorangehende Code:

* Fügt den Parameter `searchString` zur Methode `OnGetAsync` hinzu. Der Suchzeichenfolgenwert wird aus einem Textfeld empfangen, das im nächsten Abschnitt hinzugefügt wird.
* Hat eine `Where`-Klausel zur LINQ-Anweisung hinzugefügt. In der `Where`-Klausel werden nur die Studenten ausgewählt, deren Vor- oder Nachname die Suchzeichenfolge enthält. Die LINQ-Anweisung wird nur dann ausgeführt, wenn ein Wert vorhanden ist, nach dem gesucht werden soll.

Hinweis: Der vorangehende Code ruft die Methode `Where` in einem `IQueryable`-Objekt auf, und der Filter wird auf dem Server verarbeitet. In einigen Szenarios ruft die App möglicherweise die Methode `Where` als Erweiterungsmethode für eine Auflistung im Speicher auf. Angenommen, `_context.Students` wird beispielsweise von EF Core `DbSet` in eine Repositorymethode geändert, die eine `IEnumerable`-Sammlung zurückgibt. Das Ergebnis wäre normalerweise identisch, aber in einigen Fällen kann es unterschiedlich ausfallen.

Bei der .NET Framework-Implementierung von `Contains` wird beispielsweise standardmäßig ein Vergleich unter Beachtung der Groß-/Kleinschreibung vorgenommen. In SQL Server wird die Unterscheidung von Groß-/Kleinschreibung bei `Contains` durch die Einstellung für die Sortierung der SQL Server-Instanz bestimmt. Bei SQL Server wird die Groß-/Kleinschreibung standardmäßig nicht beachtet. `ToUpper` könnte aufgerufen werden, wenn die Groß-/Kleinschreibung bei der Durchführung des Tests explizit nicht beachtet werden soll:

`Where(s => s.LastName.ToUpper().Contains(searchString.ToUpper())`

Der vorangehende Code würde sicherstellen, dass die Groß-/Kleinschreibung bei Ergebnissen nicht beachtet wird, wenn der Code so geändert wird, dass er `IEnumerable` verwendet. Wenn `Contains` in einer `IEnumerable`-Sammlung verwendet wird, wird die .NET Core-Implementierung verwendet. Wenn `Contains` in einem `IQueryable`-Objekt aufgerufen wird, wird die Datenbankimplementierung verwendet. Die Rückgabe eines Objekts vom Typ `IEnumerable` aus einem Repository kann signifikante Leistungseinbußen mit sich bringen:

1. Alle Zeilen werden vom Datenbankserver zurückgegeben.
1. Der Filter wird auf alle zurückgegebenen Zeilen in der Anwendung angewendet.

Beim Aufrufen von `ToUpper` kommt es zu Leistungseinbußen. Der `ToUpper`-Code in der WHERE-Klausel der TSQL SELECT-Anweisung eine Funktion hinzu. Durch die hinzugefügte Funktion wird verhindert, dass der Optimierer einen Index verwendet. Da in SQL die Groß-/Kleinschreibung beachtet wird, sollte `ToUpper` möglichst nicht aufgerufen werden, wenn dies nicht notwendig ist.

### <a name="add-a-search-box-to-the-student-index-page"></a>Hinzufügen eines Suchfelds zur Studentenindexseite

Fügen Sie folgenden hervorgehobenen Code zu *Pages/Students/Index.cshtml* hinzu, um die Schaltfläche **Suchen** und sortiertes Chrom zu erstellen.

[!code-cshtml[](intro/samples/cu21/Pages/Students/Index3.cshtml?highlight=14-23&range=1-25)]

Der vorangehende Code verwendet das `<form>`-[Taghilfsprogramm](xref:mvc/views/tag-helpers/intro), um das Suchtextfeld und die Schaltfläche hinzuzufügen. Das `<form>`-Taghilfsprogramm sendet Formulardaten standardmäßig mit einer POST-Anforderung. Mit der POST-Anforderung werden die Parameter im HTTP-Nachrichtentext und nicht in der URL übergeben. Bei Verwendung einer HTTP GET-Anforderung werden die Formulardaten als Abfragezeichenfolgen in der URL übergeben. Durch die Übergabe der Daten mit Abfragezeichenfolgen können Benutzer die URL als Lesezeichen speichern. In den [W3C-Richtlinien](https://www.w3.org/2001/tag/doc/whenToUseGet.html) wird die Verwendung einer GET-Anforderung empfohlen, wenn die Aktion nicht zu einem Update führt.

Testen der App:

* Klicken Sie auf die Registerkarte **Studenten**, und geben Sie eine Suchzeichenfolge ein.
* Klicken Sie auf **Suchen**.

Beachten Sie, dass die URL die Suchzeichenfolge enthält.

```html
http://localhost:5000/Students?SearchString=an
```

Wenn die Seite als Lesezeichen gespeichert wird, enthält das Lesezeichen die URL der Seite und die `SearchString`-Abfragezeichenfolge. Durch `method="get"` im `form`-Tag wurde die Abfragezeichenfolge generiert.

Aktuell geht der Filterwert aus dem Feld **Suchen** verloren, wenn ein Link zur Sortierung der Spaltenüberschrift ausgewählt wird. Das Problem des verlorenen Filterwerts wird im nächsten Abschnitt behoben.

## <a name="add-paging-functionality-to-the-students-index-page"></a>Hinzufügen von Pagingfunktionen der Studentenindexseite

In diesem Abschnitt wird eine `PaginatedList`-Klasse zur Unterstützung von Paging erstellt. Die `PaginatedList`-Klasse verwendet `Skip`- und `Take`-Anweisungen zum Filtern von Daten auf dem Server, statt alle Zeilen der Tabelle abzurufen. Die folgende Abbildung zeigt die Pagingschaltflächen.

![Indexseite „Studenten“ mit Paginglinks](sort-filter-page/_static/paging.png)

Erstellen Sie im Projektordner `PaginatedList.cs` mit folgendem Code:

[!code-csharp[](intro/samples/cu21/PaginatedList.cs)]

Die Methode `CreateAsync` im vorangehenden Code übernimmt die Seitengröße und die Seitenzahl und wendet die entsprechenden `Skip`- und `Take`-Anweisungen auf das Objekt `IQueryable` an. Wenn `ToListAsync` im Objekt `IQueryable` aufgerufen wird, wird eine Liste zurückgegeben, die nur die angeforderte Seite enthält. Die Eigenschaften `HasPreviousPage` und `HasNextPage` dienen zum Aktivieren oder Deaktivieren der Schaltflächen **Zurück** und **Weiter** für das Paging.

Die Methode `CreateAsync` dient zum Erstellen des Objekts `PaginatedList<T>`. Ein Konstruktor kann das Objekt `PaginatedList<T>` nicht erstellen. Konstruktoren können keinen asynchronen Code ausführen.

## <a name="add-paging-functionality-to-the-index-method"></a>Fügen Sie Pagingfunktionen zur Indexmethode hinzu

Aktualisieren Sie in *Students/Index.cshtml.cs* den Typ von `Student` von `IList<Student>` in `PaginatedList<Student>`:

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortFilterPageType)]

Aktualisieren Sie `OnGetAsync` von *Students/Index.cshtml.cs* mit folgendem Code:

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortFilterPage&highlight=1-4,7-14,41-999)]

Der vorangehende Code fügt den Seitenindex, die aktuelle `sortOrder`-Eigenschaft und die `currentFilter`-Eigenschaft zur Methodensignatur hinzu.

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortFilterPage2)]

Alle Parameter sind NULL, wenn:

* Die Seite über den Link **Studenten** aufgerufen wird.
* Der Benutzer auf keinen Link für das Paging oder die Sortierung geklickt hat.

Wenn auf einen Paging-Link geklickt wird, enthält die Seitenindexvariable die anzuzeigende Seitenzahl.

`CurrentSort` stellt die Razor Page mit der aktuellen Sortierreihenfolge bereit. Die aktuelle Sortierreihenfolge muss in den Paging-Links enthalten sein, damit die Sortierreihenfolge während des Pagings beibehalten wird.

`CurrentFilter` stellt die Razor Page mit der aktuellen Filterzeichenfolge bereit. Der `CurrentFilter`-Wert:

* Muss in den Paging-Links enthalten sein, damit die Filtereinstellungen während des Pagings beibehalten werden.
* Muss im Textfeld wiederhergestellt werden, wenn die Seite erneut angezeigt wird.

Wenn die Suchzeichenfolge während des Pagings geändert wird, wird die Seite auf 1 zurückgesetzt. Die Seite muss auf 1 zurückgesetzt werden, da der neue Filter andere Daten anzeigen kann. Folgendes gilt, wenn ein Suchwert eingegeben und auf **Senden** geklickt wird:

* Die Suchzeichenfolge wird geändert.
* Der Parameter `searchString` ist nicht NULL.

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortFilterPage3)]

Die Methode `PaginatedList.CreateAsync` konvertiert die Studentenabfrage in eine einzelne Studentenseite in einem Sammlungstyp, der das Paging unterstützt. Diese einzelne Studentenseite wird an die Razor Page übergeben.

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortFilterPage4)]

Die zwei Fragezeichen in `PaginatedList.CreateAsync` stellen den [NULL-Sammeloperator](/dotnet/csharp/language-reference/operators/null-conditional-operator) dar. Der NULL-Sammeloperator definiert einen Standardwert für einen auf NULL festlegbaren Typ. Der Ausdruck `(pageIndex ?? 1)` bedeutet, dass der Wert von `pageIndex` zurückgegeben wird, wenn dieser einen Wert aufweist. Wenn der `pageIndex` keinen Wert aufweist, wird 1 zurückgegeben.

## <a name="add-paging-links-to-the-student-no-locrazor-page"></a>Hinzufügen von Paging-Links zur Razor Page für Studenten

Aktualisieren Sie das Markup in *Students/Index.cshtml*. Die Änderungen werden hervorgehoben:

[!code-cshtml[](intro/samples/cu21/Pages/Students/Index.cshtml?highlight=28-31,37-40,68-999)]

In den Links der Spaltenüberschriften wird die Abfragezeichenfolge verwendet, um die aktuelle Suchzeichenfolge an die Methode `OnGetAsync` zu übergeben, damit der Benutzer Filterergebnisse sortieren kann:

[!code-cshtml[](intro/samples/cu21/Pages/Students/Index.cshtml?range=28-31)]

Die Pagingschaltflächen werden durch Taghilfsprogramme angezeigt:

[!code-cshtml[](intro/samples/cu21/Pages/Students/Index.cshtml?range=72-)]

Führen Sie die App aus, und navigieren Sie zur Studentenseite.

* Klicken Sie in verschiedenen Sortierreihenfolgen auf die Paging-Links, um sicherzustellen, dass das Paging funktioniert.
* Geben Sie eine Suchzeichenfolge ein und versuchen Sie, das Paging durchzuführen, um sicherzustellen, dass das Paging bei Sortier- und Filtervorgängen ordnungsgemäß funktioniert.

![Indexseite „Studenten“ mit Paginglinks](sort-filter-page/_static/paging.png)

So erhalten Sie ein besseres Verständnis des Codes:

* Legen Sie in *Students/Index.cshtml.cs* einen Breakpoint auf `switch (sortOrder)` fest.
* Fügen Sie für `NameSort`, `DateSort`, `CurrentSort` und `Model.Student.PageIndex` ein Überwachungselement hinzu.
* Legen Sie in *Students/Index.cshtml* einen Breakpoint auf `@Html.DisplayNameFor(model => model.Student[0].LastName)` fest.

Führen Sie den Debugger schrittweise aus.

## <a name="update-the-about-page-to-show-student-statistics"></a>Aktualisieren der Info-Seite zum Anzeigen von Studentenstatistiken

In diesem Schritt wird *Pages/About.cshtml* aktualisiert, um anzuzeigen, wie viele Studenten sich am jeweiligen Anmeldedatum angemeldet haben. Bei dem Update wird eine Gruppierung verwendet, und es umfasst die folgenden Schritte:

* Das Erstellen eines Ansichtsmodells für die auf der **Info**-Seite verwendeten Daten.
* Das Aktualisieren der Info-Seite, um das Ansichtsmodell zu verwenden.

### <a name="create-the-view-model"></a>Erstellen des Ansichtsmodells

Erstellen Sie im Ordner *Models* (Modelle) den Ordner *SchoolViewModels*.

Fügen Sie im Ordner *SchoolViewModels* die Datei *EnrollmentDateGroup.cs* mit folgendem Code hinzu:

[!code-csharp[](intro/samples/cu21/Models/SchoolViewModels/EnrollmentDateGroup.cs)]

### <a name="update-the-about-page-model"></a>Aktualisieren des Info-Seitenmodells

Die Webvorlagen in ASP.NET Core 2.2 enthalten nicht die Seite „Info“. Erstellen Sie bei Verwenden von ASP.NET Core 2.2 die „Informationen zu Razor Page“.

Aktualisieren Sie die Datei *Pages/About.cshtml.cs* mit folgendem Code:

[!code-csharp[](intro/samples/cu21/Pages/About.cshtml.cs)]

Die LINQ-Anweisung gruppiert die Studentenentitäten nach Anmeldedatum, berechnet die Anzahl der Entitäten in jeder Gruppe und speichert die Ergebnisse in einer Sammlung von `EnrollmentDateGroup`-Ansichtsmodellobjekten.

### <a name="modify-the-about-no-locrazor-page"></a>Ändern der Seite „Informationen zu Razor Page“

Ersetzen Sie den Code in der Datei *Pages/About.cshtml* durch den folgenden Code:

[!code-cshtml[](intro/samples/cu21/Pages/About.cshtml)]

Führen Sie die App aus, und navigieren Sie zur Seite „Info“. Die Anzahl der Studenten für die jeweiligen Anmeldedatumswerte wird in einer Tabelle angezeigt.

Wenn nicht zu lösende Probleme auftreten, laden Sie die [abgeschlossene Anwendung für diese Phase](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/StageSnapShots/cu-part3-sorting) herunter.

![Seite „Info“](sort-filter-page/_static/about.png)

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Debuggen von ASP.NET Core 2.x-Quellcode](https://github.com/dotnet/AspNetCore.Docs/issues/4155)
* [Dieses Tutorial auf YouTube](https://www.youtube.com/watch?v=MDs7PFpoMqI)

Im nächsten Tutorial verwendet die App Migrationen zum Aktualisieren des Datenmodells.

> [!div class="step-by-step"]
> [Zurück](xref:data/ef-rp/crud)
> [Weiter](xref:data/ef-rp/migrations)

::: moniker-end

