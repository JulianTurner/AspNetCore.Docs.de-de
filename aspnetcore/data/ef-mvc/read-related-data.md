---
title: 'Tutorial: Lesen verwandter Daten: ASP.NET Core MVC mit EF Core'
description: In diesem Tutorial lesen Sie verwandte Daten und zeigen sie an – d.h., die Daten, die Entity Framework in Navigationseigenschaften lädt.
author: rick-anderson
ms.author: riande
ms.date: 09/28/2019
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
uid: data/ef-mvc/read-related-data
ms.openlocfilehash: b1adca435b48db22a176d33a216c07d6647a6695
ms.sourcegitcommit: 97243663fd46c721660e77ef652fe2190a461f81
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/09/2021
ms.locfileid: "98058310"
---
# <a name="tutorial-read-related-data---aspnet-mvc-with-ef-core"></a>Tutorial: Lesen verwandter Daten: ASP.NET Core MVC mit EF Core

Im vorherigen Tutorial haben Sie das Schuldatenmodell abgeschlossen. In diesem Tutorial lesen Sie verwandte Daten und zeigen sie an – d.h., die Daten, die Entity Framework in Navigationseigenschaften lädt.

Die folgenden Abbildungen zeigen die Seiten, mit den Sie arbeiten werden.

![Indexseite „Kurse“](read-related-data/_static/courses-index.png)

![Indexseite „Dozenten“](read-related-data/_static/instructors-index.png)

In diesem Tutorial:

> [!div class="checklist"]
> * Erfahren Sie, wie verwandte Daten geladen werden.
> * Erstellen Sie eine Seite „Kurse“
> * Erstellen Sie eine Seite „Instructors“
> * Erfahren Sie mehr über das explizite Laden

## <a name="prerequisites"></a>Voraussetzungen

* [Erstellen eines komplexen Datenmodells](complex-data-model.md)

## <a name="learn-how-to-load-related-data"></a>Erfahren Sie, wie verwandte Daten geladen werden.

Es gibt mehrere Möglichkeiten, mit denen Software für objektrelationales Mapping (ORM), z.B. Entity Framework, verwandte Daten in die Navigationseigenschaften einer Entität laden kann:

* Eager Loading (vorzeitiges Laden). Wenn die Entität gelesen wird, werden ihre verwandten Daten mit ihr abgerufen. Dies führt normalerweise zu einer einzelnen Joinabfrage, die alle Daten abruft, die erforderlich sind. Sie geben Eager Loading in Entity Framework Core mit den `Include`- und `ThenInclude`-Methoden an.

  ![Beispiel für Eager Loading](read-related-data/_static/eager-loading.png)

  Sie können einige der Daten in separaten Abfragen abrufen und EF „korrigiert“ die Navigationseigenschaften.  D.h., dass EF automatisch die separat abgerufenen Entitäten in die Navigationseigenschaften der zuvor abgerufenen Entitäten hinzufügt. Für die Abfrage, die verwandte Daten abruft, können Sie die `Load`-Methode verwenden, anstelle einer Methode, die eine Liste oder ein Objekt wie `ToList` oder `Single` zurückgibt.

  ![Beispiel für separate Abfragen](read-related-data/_static/separate-queries.png)

* Explizites Laden. Wenn die Entität zuerst gelesen wird, werden verwandte Daten nicht abgerufen. Sie schreiben Code, der die verwandten Daten abruft, wenn erforderlich. So wie im Fall von Eager Loading mit separaten Abfragen führt explizites Laden zu mehreren Abfragen, die an die Datenbank gesendet werden. Der Unterschied liegt darin, dass beim expliziten Laden der Code die zu ladenden Navigationseigenschaften angibt. In Entity Framework Core 1.1 können Sie die `Load`-Methode verwenden, um das explizite Laden auszuführen. Zum Beispiel:

  ![Beispiel für explizites Laden](read-related-data/_static/explicit-loading.png)

* Lazy Loading (verzögertes Laden). Wenn die Entität zuerst gelesen wird, werden verwandte Daten nicht abgerufen. Wenn Sie jedoch zum ersten Mal versuchen, auf eine Navigationseigenschaft zuzugreifen, werden die für diese Navigationseigenschaft erforderlichen Daten automatisch abgerufen. Jedes Mal, wenn Sie zum ersten mal versuchen, Daten von einer Navigationseigenschaft abzurufen, wird eine Anfrage an die Datenbank gesendet. Entity Framework Core 1.0 unterstützt das Lazy Loading nicht.

### <a name="performance-considerations"></a>Überlegungen zur Leistung

Wenn Sie wissen, dass Sie für jede abgerufene Entität verwandte Daten benötigen, bietet Eager Loading häufig die beste Leistung, da eine einzelne Abfrage, die an die Datenbank gesendet wird, in der Regel effizienter ist als separate Abfragen für jede abgerufene Entität. Nehmen wir beispielsweise an, dass jede Abteilung zehn verwandte Kurse hat. Eager Loading aller verwandter Daten würde zu nur einer einzelnen (Join)-Abfrage und einem einzelnen Roundtrip zur Datenbank führen. Eine separate Abfrage für Kurse jeder Abteilung würde zu elf Roundtrips zur Datenbank führen. Die zusätzlichen Roundtrips zur Datenbank beeinträchtigen die Leistung besonders bei hoher Latenz.

Andererseits sind separate Abfragen in einigen Szenarios jedoch effizienter. Eager Loading aller verwandter Daten in einer Abfrage könnte eine sehr komplexe Verknüpfung generieren, die der SQL Server nicht effizient verarbeiten kann. Oder angenommen, Sie benötigen nur Zugriff auf Navigationseigenschaften einer Entität, um auf eine Teilmenge der Reihe von Entitäten, die Sie verarbeiten, zuzugreifen. In diesem Fall können separate Abfragen möglicherweise besser ausgeführt werden, da Eager Loading aller Elemente im Vorfeld mehr Daten als benötigt abrufen würde. Wenn die Leistung wichtig ist, empfiehlt es sich, die Leistung mit beiden Möglichkeiten zu testen, um die beste Wahl treffen zu können.

## <a name="create-a-courses-page"></a>Erstellen Sie eine Seite „Kurse“

Die Kursentität enthält eine Navigationseigenschaft, die die Abteilungsentität der Abteilung enthält, der der Kurs zugewiesen ist. Sie benötigen die Namenseigenschaft der Abteilungsentität in der `Course.Department`-Navigationseigenschaft, um den Namen der zugewiesenen Abteilung in einer Kursliste anzuzeigen.

Erstellen Sie einen Controller mit dem Namen CoursesController für den Kursentitätstyp. Verwenden Sie dieselben Optionen für den **MVC-Controller mit Ansichten unter Verwendung des Entity Framework**-Gerüstbauers, die Sie zuvor für den Studentencontroller verwendet haben, wie in der folgenden Abbildung gezeigt:

![Hinzufügen eines Kursecontrollers](read-related-data/_static/add-courses-controller.png)

Öffnen Sie *CoursesController.cs*, und untersuchen Sie die `Index`-Methode. Der automatische Gerüstbau hat mithilfe der `Include`-Methode ein Eager Loading für die `Department`-Navigationseigenschaft angegeben.

Ersetzen Sie die `Index`-Methode durch den folgenden Code, der einen geeigneteren Namen für `IQueryable` verwendet, der Kursentitäten (`courses` anstelle von `schoolContext`) zurückgibt:

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_RevisedIndexMethod)]

Öffnen Sie *Views/Courses/Index.cshtml*. Ersetzen Sie den Vorlagencode durch den folgenden Code. Die Änderungen werden hervorgehoben:

[!code-cshtml[](intro/samples/cu/Views/Courses/Index.cshtml?highlight=4,7,15-17,34-36,44)]

Sie haben die folgenden Änderungen am eingerüsteten Code vorgenommen:

* Die Überschrift wurde von „Index“ in „Kurse“ geändert.

* Die Spalte **Anzahl** wurde hinzugefügt. Sie zeigt den `CourseID`-Eigenschaftswert an. Primärschlüssel werden nicht standardmäßig eingerüstet, da sie normalerweise ohne Bedeutung für Endbenutzer sind. Allerdings ist in diesem Fall der Primärschlüssel sinnvoll, und Sie möchten ihn anzeigen.

* Die Spalte **Abteilung** wurde geändert, sodass sie jetzt den Namen der Abteilung anzeigt. Der Code zeigt die `Name`-Eigenschaft der Abteilungsentität an, die in die `Department`-Navigationseigenschaft geladen wird:

  ```html
  @Html.DisplayFor(modelItem => item.Department.Name)
  ```

Führen Sie die Anwendung aus, und wählen Sie die Registerkarte **Kurse** aus, um die Liste mit den Abteilungsnamen anzuzeigen.

![Indexseite „Kurse“](read-related-data/_static/courses-index.png)

## <a name="create-an-instructors-page"></a>Erstellen Sie eine Seite „Instructors“

In diesem Abschnitt müssen Sie einen Controller und eine Ansicht für die Instructor-Entität erstellen, um die Dozentenseite anzuzeigen:

![Indexseite „Dozenten“](read-related-data/_static/instructors-index.png)

Auf dieser Seite werden verwandte Daten auf folgende Weise gelesen und angezeigt:

* Die Liste der Dozenten zeigt verwandte Daten aus der OfficeAssignment-Entität. Die Instructor- und OfficeAssignment-Entitäten sind in einer 1:0..1-Beziehung. Sie werden Eager Loading für die OfficeAssignment-Entitäten verwenden. Wie zuvor erläutert, ist Eager Loading in der Regel effizienter, wenn Sie die verwandten Daten für alle abgerufenen Zeilen der primären Tabelle benötigen. In diesem Fall sollten Sie die Office-Anweisungen für alle angezeigten Dozenten anzeigen.

* Wenn der Benutzer einen Dozenten auswählt, werden verwandte Course-Entitäten angezeigt. Die Instructor- und Course-Entitäten stehen in einer m:n-Beziehung zueinander. Sie werden Eager Loading für die Kursentitäten und ihre zugehörigen Abteilungsentitäten verwenden. In diesem Fall können separate Abfrage effizienter sein, da nur Kurse für den ausgewählten Dozenten benötigt werden. Dieses Beispiel zeigt jedoch, wie Eager Loading für Navigationseigenschaften in Entitäten in den Navigationseigenschaften verwendet wird.

* Wenn der Benutzer einen Kurs auswählt, werden verwandte Daten aus der Registrierungsentität angezeigt. Die Kurs- und Registrierungsentitäten sind in einer 1:n-Beziehung. Sie werden separate Abfragen für Registrierungsentitäten und ihre verwandten Studentenentitäten verwenden.

### <a name="create-a-view-model-for-the-instructor-index-view"></a>Erstellen eines Ansichtsmodells für die Indexansicht „Dozenten“

Die Dozentenseite zeigt Daten aus drei verschiedenen Tabellen. Aus diesem Grund erstellen Sie ein Ansichtsmodell, das drei Eigenschaften enthält. Jede enthält Daten für eine der Tabellen.

Erstellen Sie im Ordner *SchoolViewModels* *InstructorIndexData.cs*, und ersetzen Sie den bestehenden Code durch den folgenden Code:

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/InstructorIndexData.cs)]

### <a name="create-the-instructor-controller-and-views"></a>Erstellen der Dozentencontroller und -ansichten

Erstellen Sie einen Dozentencontroller mit EF-Lese-/Schreibaktionen, wie in der folgenden Abbildung gezeigt:

![Hinzufügen von Dozentencontrollern](read-related-data/_static/add-instructors-controller.png)

Öffnen Sie *InstructorsController.cs*. Fügen Sie eine Using-Anweisung für den Namespace ViewModels hinzu:

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_Using)]

Ersetzen Sie die Indexmethode durch den folgenden Code, um Eager Loading verwandter Daten durchzuführen und ihn in das Ansichtsmodell einzufügen.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_EagerLoading)]

Die Methode akzeptiert optionale Routendaten (`id`) und einen Abfragezeichenfolgenparameter (`courseID`), die die ID-Werte des ausgewählten Dozenten und Kurses bereitstellen. Die Parameter werden durch die **Auswählen**-Links auf der Seite bereitgestellt.

Der Code erstellt zuerst eine Instanz des Ansichtsmodells und fügt die Dozentenliste ein. Der Code gibt Eager Loading für die `Instructor.OfficeAssignment`- und `Instructor.CourseAssignments`-Navigationseigenschaften an. Innerhalb der `CourseAssignments`-Eigenschaft wird die `Course`-Eigenschaft geladen, und innerhalb dieser werden die `Enrollments`- und `Department`-Eigenschaften geladen, und innerhalb jeder `Enrollment`-Entität wird die `Student`-Eigenschaft geladen.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_ThenInclude)]

Da die Ansicht immer die OfficeAssignment-Entität erfordert, ist es effizienter, sie in derselben Abfrage abzurufen. Kursentitäten sind erforderlich, wenn auf der Webseite ein Dozent ausgewählt ist. Somit ist eine einzelne Abfrage nur besser als mehrere Abfragen, wenn die Seite häufiger mit einem ausgewählten Kurs als ohne angezeigt wird.

Der Code wiederholt `CourseAssignments` und `Course`, da Sie zwei Eigenschaften aus `Course` benötigen. Die erste Zeichenfolge der `ThenInclude`-Abrufe erhält `CourseAssignment.Course`, `Course.Enrollments` und `Enrollment.Student`.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_ThenInclude&highlight=3-6)]

An diesem Punkt im Code wäre eine andere `ThenInclude` für Navigationseigenschaften von `Student`, die Sie nicht benötigen. Aber ein Aufruf von `Include` beginnt mit `Instructor`-Eigenschaften neu. Daher müssen Sie den Vorgang erneut durchlaufen und `Course.Department` anstelle von `Course.Enrollments` angeben.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_ThenInclude&highlight=7-9)]

Der folgende Code wird ausgeführt, wenn ein Dozent ausgewählt wurde. Der ausgewählte Dozent wird aus der Liste der Dozenten im Ansichtsmodell abgerufen. Die `Courses`-Eigenschaft des Ansichtsmodells wird dann mit den Kursentitäten aus der `CourseAssignments`-Navigationseigenschaft dieses Dozenten geladen.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?range=56-62)]

Die `Where`-Methode gibt eine Sammlung zurück. Aber in diesem Fall resultieren die an diese Methode übergebenen Kriterien nur in einer einzigen zurückgegebenen Instructor-Entität. Die `Single`-Methode konvertiert die Sammlung in eine einzelne Instructor-Entität, die Ihnen Zugriff auf die `CourseAssignments`-Eigenschaft dieser Entität gibt. Die `CourseAssignments`-Eigenschaft enthält `CourseAssignment`-Entitäten, aus der Sie nur die verwandten `Course`-Entitäten benötigen.

Verwenden Sie die `Single`-Methode für eine Sammlung, wenn Sie wissen, dass die Sammlung nur über ein Element verfügt. Die Single-Methode löst eine Ausnahme aus, wenn die Sammlung, an die übergeben wurde, leer ist, oder wenn mehr als ein Element vorhanden ist. Eine Alternative ist `SingleOrDefault`, womit ein Standardwert (in diesem Fall null) zurückgegeben wird, wenn die Sammlung leer ist. In diesem Fall würde jedoch trotzdem eine Ausnahme ausgelöst werden (da versucht wird, eine `Courses`-Eigenschaft auf einem Null-Verweis zu finden). Die Ausnahmemeldung würde die Ursache des Problems weniger deutlich angeben. Wenn Sie die `Single`-Methode aufrufen, können Sie auch in die Where-Bedingung übergehen, anstatt die `Where`-Methode separat aufzurufen:

```csharp
.Single(i => i.ID == id.Value)
```

anstelle von:

```csharp
.Where(i => i.ID == id.Value).Single()
```

Wenn ein Kurs ausgewählt wurde, wird der ausgewählte Kurs aus der Kursliste im Ansichtsmodell abgerufen. Die `Enrollments`-Eigenschaft des Ansichtsmodells wird dann mit den Registrierungsentitäten aus der `Enrollments`-Navigationseigenschaft dieses Kurses geladen.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?range=64-69)]

### <a name="modify-the-instructor-index-view"></a>Ändern der Dozentenindexansicht

Ersetzen Sie in *Views/Instructors/Index.cshtml* den Vorlagencode durch den folgenden Code. Die Änderungen werden hervorgehoben.

::: moniker range=">= aspnetcore-2.2"

[!code-cshtml[](intro/samples/5cu-snap/Views/Instructors/Index.cshtml?range=1-62&highlight=1,3-7,15-19,24,26-31,41-52,54)]

::: moniker-end

::: moniker range="<= aspnetcore-2.1"

[!code-cshtml[](intro/samples/cu/Views/Instructors/Index1.cshtml?range=1-62&highlight=1,3-7,15-19,24,26-31,41-52,54)]

::: moniker-end

Sie haben die folgenden Änderungen am bestehenden Code vorgenommen:

* Die Modellklasse wurde zu `InstructorIndexData` geändert.

* Der Seitenname wurde von **Index** in **Dozenten** geändert.

* Es wurde eine **Office**-Spalte hinzugefügt, die `item.OfficeAssignment.Location` nur anzeigt, wenn `item.OfficeAssignment` nicht gleich null ist. (Da dies eine 1:0..1-Beziehung ist, gibt es möglicherweise keine verwandte OfficeAssignment-Entität.)

  ```cshtml
  @if (item.OfficeAssignment != null)
  {
      @item.OfficeAssignment.Location
  }
  ```

* Es wurde eine **Kurse**-Spalte hinzugefügt, die die Kurse eines jeden Dozenten anzeigt. Weitere Informationen finden Sie im Abschnitt [Explicit line transition](xref:mvc/views/razor#explicit-line-transition) (Expliziter Zeilenübergang) des Razor-Syntaxartikels.

* Es wurde Code hinzugefügt, der dem `tr`-Element des ausgewählten Dozenten bedingt eine Bootstrap-CSS-Klasse hinzufügt. Diese Klasse legt eine Hintergrundfarbe für die ausgewählte Zeile fest.

* Es wurde ein neuer Link mit der Bezeichnung **Auswählen** unmittelbar vor den anderen Links in jeder Zeile hinzugefügt. Dies führt dazu, dass die ID des ausgewählten Dozenten an die `Index`-Methode gesendet wird.

  ```cshtml
  <a asp-action="Index" asp-route-id="@item.ID">Select</a> |
  ```

Führen Sie die Anwendung aus. Klicken Sie auf die Registerkarte **Dozenten**. Die Seite zeigt die Eigenschaft für den Standort verwandter OfficeAssignment-Entitäten und eine leere Zelle an, wenn keine verwandte OfficeAssignment-Entität vorhanden ist.

![Indexseite „Dozenten“, nichts ausgewählt](read-related-data/_static/instructors-index-no-selection.png)

Fügen Sie in der Datei *Views/Instructors/Index.cshtml* nach dem Schließen des Tabellenelements (am Ende der Datei) den folgenden Code hinzu. Dieser Code zeigt eine Liste der Kurse an, die im Zusammenhang mit einem Dozenten stehen, wenn ein Dozent ausgewählt wird.

[!code-cshtml[](intro/samples/cu/Views/Instructors/Index1.cshtml?range=66-101)]

Dieser Code liest die `Courses`-Eigenschaft des Ansichtsmodells, um eine Kursliste anzuzeigen. Er bietet außerdem einen **Auswählen**-Link, der die ID des ausgewählten Kurses an die `Index`-Aktionsmethode sendet.

Aktualisieren Sie die Seite. Wählen Sie einen Dozenten aus. Jetzt sehen Sie ein Raster, das die dem Dozenten zugewiesenen Kurse anzeigt. Sie sehen auch den Namen der zugewiesenen Abteilung für jeden Kurs.

![Indexseite „Dozenten“, Dozent ausgewählt](read-related-data/_static/instructors-index-instructor-selected.png)

Fügen Sie den folgenden Code hinzu, nachdem Sie den Codeblock hinzugefügt haben. Dies zeigt eine Liste der Studenten an, die im Kurs registriert sind, wenn dieser Kurs ausgewählt ist.

[!code-cshtml[](intro/samples/cu/Views/Instructors/Index1.cshtml?range=103-125)]

Dieser Code liest die Registrierungseigenschaft des Ansichtsmodells, um eine Liste der in diesem Kurs registrierten Studenten anzuzeigen.

Aktualisieren Sie die Seite erneut. Wählen Sie einen Dozenten aus. Wählen Sie dann einen Kurs aus, um die Liste der registrierten Studenten und deren Noten einzusehen.

![Indexseite „Dozenten“, Dozent und Kurs ausgewählt](read-related-data/_static/instructors-index.png)

## <a name="about-explicit-loading"></a>Informationen zum expliziten Laden

Wenn Sie die Liste der Dozenten aus *InstructorsController.cs* abrufen, haben Sie Eager Loading für die `CourseAssignments`-Navigationseigenschaft angegeben.

Angenommen, Sie haben erwartet, dass Benutzer nur selten Registrierungen für einen ausgewählten Dozenten und Kurs angezeigt haben möchten. In diesem Fall möchten Sie die Registrierungsdaten möglicherweise nur laden, wenn diese angefordert werden. Ersetzen Sie die `Index`-Methode durch folgenden Code, um ein Beispiel für Eager Loading zu sehen. Dieser Code entfernt das Eager Loading für Registrierungen und lädt diese Eigenschaft explizit. Die Codeänderungen werden hervorgehoben.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_ExplicitLoading&highlight=23-29)]

Der neue Code löscht die *ThenInclude*-Methodenaufrufe für Registrierungsdaten aus dem Code, der Instructor-Entitäten abruft. Außerdem wird `AsNoTracking` gelöscht.  Wenn ein Dozent und Kurs ausgewählt werden, ruft der hervorgehobene Code Registrierungsentitäten für den ausgewählten Kurs und Studentenentitäten für jede Registrierung ab.

Führen Sie die Anwendung aus, navigieren Sie zur Dozentenindexseite, und Sie werden keinen Unterschied in der Anzeige auf der Seite bemerken, obwohl Sie die Abrufart für Daten geändert haben.

## <a name="get-the-code"></a>Abrufen des Codes

[Download or view the completed app (Herunterladen oder anzeigen der vollständigen App).](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial:

> [!div class="checklist"]
> * Haben Sie gelernt, wie verwandte Daten geladen werden
> * Wurde eine Seite „Kurse“ erstellt
> * Wurde eine Seite „Instructors“ erstellt
> * Haben Sie mehr über das explizite Laden erfahren

Fahren Sie mit dem nächsten Tutorial fort, um mehr über das Aktualisieren zugehöriger Daten zu erfahren.

> [!div class="nextstepaction"]
> [Aktualisieren relevanter Daten](update-related-data.md)
