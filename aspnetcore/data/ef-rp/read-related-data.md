---
title: 'Teil 6: Razor Pages mit EF Core in ASP.NET Core – Lesen relevanter Daten'
author: rick-anderson
description: Dies ist Teil 6 der Tutorialreihe zu Razor Pages und dem Entity Framework.
ms.author: riande
ms.custom: mvc
ms.date: 09/28/2019
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
uid: data/ef-rp/read-related-data
ms.openlocfilehash: dd00b9d7faf467857ec1e47f4cfb0296d84e5d3f
ms.sourcegitcommit: 62cc131969b2379f7a45c286a751e22d961dfbdb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/22/2020
ms.locfileid: "90847701"
---
# <a name="part-6-no-locrazor-pages-with-ef-core-in-aspnet-core---read-related-data"></a>Teil 6: Razor Pages mit EF Core in ASP.NET Core – Lesen relevanter Daten

Von [Tom Dykstra](https://github.com/tdykstra), [Jon P. Smith](https://twitter.com/thereformedprog) und [Rick Anderson](https://twitter.com/RickAndMSFT)

[!INCLUDE [about the series](../../includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

Dieses Tutorial zeigt, wie verwandte Daten gelesen und angezeigt werden. Verwandte Daten sind Daten, die von EF Core in die Navigationseigenschaften geladen werden.

Die folgenden Abbildungen zeigen die abgeschlossenen Seiten für dieses Tutorial:

![Indexseite „Kurse“](read-related-data/_static/courses-index30.png)

![Indexseite „Dozenten“](read-related-data/_static/instructors-index30.png)

## <a name="eager-explicit-and-lazy-loading"></a>Explizites Laden, Eager Loading und Lazy Loading

Es gibt mehrere Möglichkeiten, mit denen EF Core verwandte Daten in die Navigationseigenschaften einer Entität laden kann:

* [Eager Loading (vorzeitiges Laden)](/ef/core/querying/related-data#eager-loading). Beim Eager Loading lädt eine Abfrage für einen Entitätstyp auch verwandte Entitäten. Wenn eine Entität gelesen wird, werden ihre zugehörigen Daten abgerufen. Dies führt normalerweise zu einer einzelnen Joinabfrage, die alle Daten abruft, die erforderlich sind. EF Core wird mehrere Abfragen für einige Typen von Eager Loading ausgeben. Das Ausgeben mehrerer Abfragen kann effizienter sein als eine einzelne große Abfrage. Eager Loading wird mit den `Include`- und `ThenInclude`-Methoden angegeben.

  ![Beispiel für Eager Loading](read-related-data/_static/eager-loading.png)
 
  Eager Loading sendet mehrere Abfragen, wenn eine Sammlungsnavigation enthalten ist:

  * Eine Abfrage für die Hauptabfrage 
  * Eine Abfrage für jeden „Sammlungsrand“ in der Ladestruktur.

* Beispiel für separate Abfragen mit `Load`: Die Daten können in separaten Abfragen abgerufen werden. EF Core korrigiert die Navigationseigenschaften. „Korrigieren“ bedeutet, dass EF Core die Navigationseigenschaften automatisch mit Daten auffüllt. Separate Abfragen mit `Load` ähneln mehr dem expliziten Laden als dem Eager Loading.

  ![Beispiel für separate Abfragen](read-related-data/_static/separate-queries.png)

  **Hinweis**: EF Core korrigiert automatisch Navigationseigenschaften für alle anderen Entitäten, die zuvor in die Kontextinstanz geladen wurden. Auch wenn die Daten für eine Navigationseigenschaft *nicht* explizit eingeschlossen sind, kann die Eigenschaft immer noch aufgefüllt werden, wenn einige oder alle verwandten Entitäten zuvor geladen wurden.

* [Explizites Laden](/ef/core/querying/related-data#explicit-loading). Wenn die Entität zuerst gelesen wird, werden verwandte Daten nicht abgerufen. Es muss Code geschrieben werden, um die verwandten Daten bei Bedarf abzurufen. Explizites Laden mit separaten Abfragen führt zu mehreren Abfragen, die an die Datenbank gesendet werden. Mit explizitem Laden gibt der Code die zu ladenden Navigationseigenschaften an. Verwenden Sie für explizites Laden die `Load`-Methode. Zum Beispiel:

  ![Beispiel für explizites Laden](read-related-data/_static/explicit-loading.png)

* [Lazy Loading (verzögertes Laden)](/ef/core/querying/related-data#lazy-loading). Wenn die Entität zuerst gelesen wird, werden verwandte Daten nicht abgerufen. Wenn zum ersten Mal auf eine Navigationseigenschaft zugegriffen wird, werden die für diese Navigationseigenschaft erforderlichen Daten automatisch abgerufen. Wenn zum ersten Mal auf eine Navigationseigenschaft zugegriffen wird, wird jedes Mal eine Abfrage an die Datenbank gesendet. Lazy Loading kann die Leistung beeinträchtigen, z. B. wenn Entwickler N+1-Muster verwenden, ein übergeordnetes Element laden und über untergeordnete Elemente aufzählen.

## <a name="create-course-pages"></a>Erstellen von Course-Seiten

Die `Course`-Entität enthält eine Navigationseigenschaft, die die zugehörige `Department`-Entität enthält.

![Course.Department](read-related-data/_static/dep-crs.png)

So zeigen Sie den Namen des zugewiesenen Fachbereichs für einen Kurs an:

* Laden Sie die zugehörige `Department`-Entität in die `Course.Department`-Navigationseigenschaft.
* Rufen Sie den Namen aus der `Name`-Eigenschaft der `Department`-Entität ab.

<a name="scaffold"></a>

### <a name="scaffold-course-pages"></a>Gerüstbau von Course-Seiten

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Befolgen Sie die Anweisungen unter [Gerüstbau der Student-Seiten](xref:data/ef-rp/intro#scaffold-student-pages) mit den folgenden Ausnahmen:

  * Erstellen Sie einen Ordner *Pages/Courses*.
  * Verwenden Sie `Course` als Modellklasse.
  * Verwenden Sie die vorhandene Kontextklasse, anstatt eine neue Klasse zu erstellen.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Erstellen Sie einen Ordner *Pages/Courses*.

* Führen Sie den folgenden Befehl aus, um das Gerüst für Course-Seiten zu erstellen.

  **Unter Windows:**

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages\Courses --referenceScriptLibraries
  ```

  **Unter Linux oder macOS:**

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages/Courses --referenceScriptLibraries
  ```

---

* Öffnen Sie *Pages/Courses/Index.cshtml.cs*. Untersuchen Sie die `OnGetAsync`-Methode. Die Gerüstbauengine gibt Eager Loading für die `Department`-Navigationseigenschaft an. Die `Include`-Methode gibt Eager Loading an.

* Führen Sie die Anwendung aus, und klicken Sie auf den Link **Kurse**. Die Abteilungsspalte zeigt die `DepartmentID` an, die nicht hilfreich ist.

### <a name="display-the-department-name"></a>Anzeigen des Fachbereichnamens

Aktualisieren Sie „Pages/Courses/Index.cshtml.cs“ mit dem folgenden Code:

[!code-csharp[](intro/samples/cu30/Pages/Courses/Index.cshtml.cs?highlight=18,22,24)]

Der vorstehende Code ändert die `Course`-Eigenschaft in `Courses` und fügt `AsNoTracking` hinzu. `AsNoTracking` verbessert die Leistung, da die zurückgegebenen Entitäten nicht nachverfolgt werden. Die Entitäten müssen nicht nachverfolgt werden, da sie nicht im aktuellen Kontext aktualisiert werden.

Aktualisieren Sie *Pages/Courses/Index.cshtml* mit dem folgenden Code.

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Index.cshtml?highlight=5,8,16-18,20,23,26,32,35-37,45)]

Die folgenden Änderungen wurden am Codegerüst vorgenommen:

* Der `Course`-Eigenschaftenname wurde in `Courses` geändert.
* Die Spalte **Anzahl** wurde hinzugefügt. Sie zeigt den `CourseID`-Eigenschaftswert an. Primärschlüssel werden nicht standardmäßig eingerüstet, da sie normalerweise ohne Bedeutung für Endbenutzer sind. Allerdings hat der Primärschlüssel in diesem Fall jedoch Bedeutung.
* Die Spalte **Abteilung** wurde geändert, sodass sie jetzt den Namen der Abteilung anzeigt. Der Code zeigt die `Name`-Eigenschaft der `Department`-Entität an, die in die `Department`-Navigationseigenschaft geladen wird:

  ```html
  @Html.DisplayFor(modelItem => item.Department.Name)
  ```

Führen Sie die Anwendung aus. Wählen Sie die Registerkarte **Kurse** aus, um die Liste mit den Abteilungsnamen anzuzeigen.

![Indexseite „Kurse“](read-related-data/_static/courses-index30.png)

<a name="select"></a>

### <a name="loading-related-data-with-select"></a>Laden verwandter Daten mit „Select“

Die `OnGetAsync`-Methode lädt zugehörige Daten mit der `Include`-Methode. Die `Select`-Methode ist eine Alternative, die nur die zugehörigen benötigten Daten lädt. Für die einzelnen Elemente wie `Department.Name` wird ein INNER JOIN von SQL verwendet. Für Sammlungen wird ein anderer Zugriff auf die Datenbank verwendet, aber Gleiches gilt für den `Include`-Operator für Sammlungen.

Der folgende Code lädt verwandte Daten mit der `Select`-Methode:

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=6)]

Der voranstehende Code gibt keine Entitätstypen zurück, weshalb keine Nachverfolgung ausgeführt wird. Weitere Informationen zur EF-Nachverfolgung finden Sie unter [Nachverfolgungsabfragen im Vergleich zu Abfragen ohne Nachverfolgung](/ef/core/querying/tracking).

Die `CourseViewModel`:

[!code-csharp[](intro/samples/cu30snapshots/6-related/Models/SchoolViewModels/CourseViewModel.cs?name=snippet)]

Ein vollständiges Beispiel finden Sie unter [IndexSelect.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml) und [IndexSelect.cshtml.cs](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml.cs).

## <a name="create-instructor-pages"></a>Erstellen von Instructor-Seiten

In diesem Abschnitt erfolgt der Gerüstbau von Instructor-Seiten, und der Index-Seite des Dozenten werden zugehörige Courses und Enrollments hinzugefügt.

<a name="IP"></a>
![Indexseite „Dozenten“](read-related-data/_static/instructors-index30.png)

Auf dieser Seite werden verwandte Daten auf folgende Weise gelesen und angezeigt:

* Die Liste der Dozenten zeigt verwandte Daten aus der `OfficeAssignment`-Entität (Office in der vorherigen Abbildung). Die `Instructor`- und `OfficeAssignment`-Entitäten stehen in einer 1:0..1-Beziehung zueinander. Eager Loading wird für die `OfficeAssignment`-Entitäten verwendet. Eager Loading ist in der Regel effizienter, wenn die verwandten Daten angezeigt werden müssen. In diesem Fall werden die Office-Zuweisungen für die Dozenten angezeigt.
* Wenn der Benutzer einen Dozenten auswählt, werden zugehörige `Course`-Entitäten angezeigt. Die `Instructor`- und `Course`-Entitäten stehen in einer m:n-Beziehung zueinander. Für die `Course`-Entitäten und ihre verwandten `Department`-Entitäten wird das Eager Loading verwendet. In diesem Fall können separate Abfragen effizienter sein, da nur Kurse für den ausgewählten Dozenten benötigt werden. Dieses Beispiel zeigt, wie Eager Loading für Navigationseigenschaften in Entitäten in den Navigationseigenschaften verwendet wird.
* Wenn der Benutzer einen Kurs auswählt, werden zugehörige Daten aus der `Enrollments`-Entität angezeigt. In der vorherigen Abbildung sind der Name des Studenten und die Note angezeigt. Die `Course`- und `Enrollment`-Entitäten stehen in einer 1:n-Beziehung zueinander.

### <a name="create-a-view-model"></a>Erstellen von Anzeigemodellen

Die Dozentenseite zeigt Daten aus drei verschiedenen Tabellen. Ein Ansichtsmodell ist erforderlich, das drei Eigenschaften enthält, die die drei Tabellen darstellen.

Erstellen *SchoolViewModels/InstructorIndexData.cs* mit dem folgenden Code:

[!code-csharp[](intro/samples/cu30/Models/SchoolViewModels/InstructorIndexData.cs)]

### <a name="scaffold-instructor-pages"></a>Gerüstbau von Instructor-Seiten

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Befolgen Sie die Anweisungen unter [Gerüstbau der Student-Seiten](xref:data/ef-rp/intro#scaffold-student-pages) mit den folgenden Ausnahmen:

  * Erstellen Sie einen Ordner *Pages/Instructors*.
  * Verwenden Sie `Instructor` als Modellklasse.
  * Verwenden Sie die vorhandene Kontextklasse, anstatt eine neue Klasse zu erstellen.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Erstellen Sie einen Ordner *Pages/Instructors*.

* Führen Sie den folgenden Befehl aus, um das Gerüst für Instructor-Seiten zu erstellen.

  **Unter Windows:**

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages\Instructors --referenceScriptLibraries
  ```

  **Unter Linux oder macOS:**

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages/Instructors --referenceScriptLibraries
  ```

---

Führen Sie die App aus, und navigieren Sie zur Seite Instructors, um zu sehen, wie die Gerüstbauseite aussieht, bevor Sie sie aktualisieren.

Aktualisieren Sie *Pages/Instructors/Index.cshtml.cs* mit dem folgenden Code:

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_all&highlight=2,19-53)]

Die `OnGetAsync`-Methode akzeptiert optional Routendaten für die ID des ausgewählten Dozenten.

Untersuchen Sie die Abfrage in der Datei *Pages/Instructors/Index.cshtml.cs*:

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_EagerLoading)]

Der Code gibt Eager Loading für die folgenden Navigationseigenschaften an:

* `Instructor.OfficeAssignment`
* `Instructor.CourseAssignments`
  * `CourseAssignments.Course`
    * `Course.Department`
    * `Course.Enrollments`
      * `Enrollment.Student`

Beachten Sie die Wiederholung der `Include`- und `ThenInclude`-Methoden für `CourseAssignments` und `Course`. Diese Wiederholung ist erforderlich, um Eager Loading für zwei Navigationseigenschaften der `Course`-Entität anzugeben.

Der folgende Code wird ausgeführt, wenn ein Dozent ausgewählt wird (`id != null`).

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_SelectInstructor)]

Der ausgewählte Dozent wird aus der Liste der Dozenten im Ansichtsmodell abgerufen. Die `Courses`-Eigenschaft des Ansichtsmodells wird mit den `Course`-Entitäten aus der `CourseAssignments`-Navigationseigenschaft dieses Dozenten geladen.

Die `Where`-Methode gibt eine Sammlung zurück. In diesem Fall wählt der Filter jedoch eine einzelne Entität aus, sodass die `Single`-Methode aufgerufen wird, um die Sammlung in eine einzelne `Instructor`-Entität zu konvertieren. Die `Instructor`-Entität ermöglicht Zugriff auf die `CourseAssignments`-Eigenschaft. `CourseAssignments` ermöglicht Zugriff auf die verwandten `Course`-Entitäten.

![Dozent:Kurse m:N](complex-data-model/_static/courseassignment.png)

Die `Single`-Methode wird für eine Sammlung verwendet, wenn die Sammlung nur ein Element aufweist. Die `Single`-Methode löst eine Ausnahme aus, wenn die Sammlung leer ist oder mehr als ein Element vorhanden ist. Eine Alternative ist `SingleOrDefault`, womit ein Standardwert (in diesem Fall 0 (null)) zurückgegeben wird, wenn die Sammlung leer ist.

Wenn ein Kurs ausgewählt ist, füllt der folgende Code die `Enrollments`-Eigenschaft des Ansichtsmodells:

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_SelectCourse)]

### <a name="update-the-instructors-index-page"></a>Aktualisieren der Indexseite „Dozenten“

Aktualisieren Sie *Pages/Instructors/Index.cshtml* mit dem folgenden Code.

[!code-cshtml[](intro/samples/cu30/Pages/Instructors/Index.cshtml?highlight=1,5,8,16-21,25-32,43-57,67-102,104-126)]

Durch den vorangehenden Code werden folgende Änderungen vorgenommen:

* Aktualisiert die `page`-Anweisung von `@page` auf `@page "{id:int?}"`. `"{id:int?}"` ist eine Routenvorlage. Die Routenvorlage ändert ganzzahlige Abfragezeichenfolgen in der URL in Routendaten. Klicken Sie beispielsweise auf den Link **Auswählen** für einen Dozenten, wenn nur die `@page`-Anweisung eine URL wie die folgende erzeugt:

  `https://localhost:5001/Instructors?id=2`

  Wenn die Seitenanweisung `@page "{id:int?}"` ist, lautet die URL folgendermaßen:

  `https://localhost:5001/Instructors/2`

* Fügt eine **Office**-Spalte hinzu, die `item.OfficeAssignment.Location` nur anzeigt, wenn `item.OfficeAssignment` nicht NULL ist. Da dies eine 1:0..1-Beziehung ist, gibt es möglicherweise keine verwandte OfficeAssignment-Entität.

  ```html
  @if (item.OfficeAssignment != null)
  {
      @item.OfficeAssignment.Location
  }
  ```

* Fügt eine **Courses**-Spalte hinzu, die die Kurse eines jeden Dozenten anzeigt. Weitere Informationen zu dieser Razor-Syntax finden Sie unter [Expliziter Zeilenübergang](xref:mvc/views/razor#explicit-line-transition).

* Fügt Code hinzu, der `class="success"` dynamisch zum `tr`-Element des ausgewählten Dozenten und Kurses hinzufügt. Hiermit wird mit einer Bootstrapklasse eine Hintergrundfarbe für die ausgewählte Zeile hinzugefügt.

  ```html
  string selectedRow = "";
  if (item.CourseID == Model.CourseID)
  {
      selectedRow = "success";
  }
  <tr class="@selectedRow">
  ```

* Fügt einen neuen Link mit der Bezeichnung **Select** hinzu. Dieser Link sendet die ID des ausgewählten Dozenten an die `Index`-Methode und legt die Hintergrundfarbe fest.

  ```html
  <a asp-action="Index" asp-route-id="@item.ID">Select</a> |
  ```

* Fügt eine Tabelle mit Kursen für den ausgewählten Dozenten hinzu.

* Fügt eine Tabelle mit Studentenregistrierungen für den ausgewählten Kurs hinzu.

Führen Sie die Anwendung aus. Klicken Sie auf die Registerkarte **Dozenten**. Die Seite zeigt den `Location` (Office) aus der verwandten `OfficeAssignment`-Entität an. Wenn `OfficeAssignment` NULL ist, wird eine leere Tabellenzelle angezeigt.

Klicken Sie auf den Link **Select** für einen Dozenten. Der Zeilenstil ändert sich, und Kurse, die diesem Dozenten zugewiesen sind, werden angezeigt.

Wählen Sie einen Kurs aus, um die Liste der registrierten Studenten und deren Noten einzusehen.

![Indexseite „Dozenten“, Dozent und Kurs ausgewählt](read-related-data/_static/instructors-index30.png)

## <a name="using-single"></a>Verwenden von „Single“

Die `Single`-Methode kann die `Where`-Bedingung übergehen, anstatt die `Where`-Methode separat aufzurufen:

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/IndexSingle.cshtml.cs?name=snippet_single&highlight=21-22,30-31)]

Die Verwendung von `Single` mit einer Where-Bedingung ist eine Frage der persönlichen Präferenz. Sie bietet keine Vorteile gegenüber der Verwendung der `Where`-Methode.

## <a name="explicit-loading"></a>Explizites Laden

Der aktuelle Code gibt Eager Loading für `Enrollments` und `Students` an:

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_EagerLoading&highlight=6-9)]

Angenommen, Benutzer möchten Registrierungen für einen Kurs nur selten anzeigen lassen. In diesem Fall wäre es eine Optimierung, die Registrierungsdaten nur dann zu laden, wenn diese angefordert werden. In diesem Abschnitt wird die `OnGetAsync` aktualisiert, um das explizite Laden von `Enrollments` und `Students` zu verwenden.

Aktualisieren Sie *Pages/Instructors/Index.cshtml.cs* mit dem folgenden Code.

[!code-csharp[](intro/samples/cu30/Pages/Instructors/Index.cshtml.cs?highlight=31-35,52-56)]

Der vorangehende Code löscht die *ThenInclude*-Methodenaufrufe für Registrierung und Studentendaten. Wenn ein Kurs ausgewählt ist, ruft der explizit geladene Code Folgendes ab:

* Die `Enrollment`-Entitäten für den ausgewählten Kurs.
* Die `Student`-Entitäten für jede `Enrollment`.

Beachten Sie, dass der vorherige Code `.AsNoTracking()` auskommentiert. Navigationseigenschaften können nur für nachverfolgte Entitäten explizit geladen werden.

Testen Sie die App. Aus Benutzersicht verhält sich die Anwendung identisch mit der vorherigen Version.

## <a name="next-steps"></a>Nächste Schritte

Das nächste Tutorial zeigt, wie verwandte Daten aktualisiert werden.

>[!div class="step-by-step"]
>[Vorheriges Tutorial](xref:data/ef-rp/complex-data-model)
>[Nächstes Tutorial](xref:data/ef-rp/update-related-data)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

In diesem Tutorial werden verwandte Daten gelesen und angezeigt. Verwandte Daten sind Daten, die von EF Core in die Navigationseigenschaften geladen werden.

Wenn nicht zu lösende Probleme auftreten, laden Sie die [fertige App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) herunter, oder zeigen Sie diese an. [Anweisungen zum Download.](xref:index#how-to-download-a-sample)

Die folgenden Abbildungen zeigen die abgeschlossenen Seiten für dieses Tutorial:

![Indexseite „Kurse“](read-related-data/_static/courses-index.png)

![Indexseite „Dozenten“](read-related-data/_static/instructors-index.png)

## <a name="eager-explicit-and-lazy-loading-of-related-data"></a>Explizites, Eager und Lazy Loading verwandter Daten

Es gibt mehrere Möglichkeiten, mit denen EF Core verwandte Daten in die Navigationseigenschaften einer Entität laden kann:

* [Eager Loading (vorzeitiges Laden)](/ef/core/querying/related-data#eager-loading). Beim Eager Loading lädt eine Abfrage für einen Entitätstyp auch verwandte Entitäten. Wenn die Entität gelesen wird, werden ihre verwandten Daten abgerufen. Dies führt normalerweise zu einer einzelnen Joinabfrage, die alle Daten abruft, die erforderlich sind. EF Core wird mehrere Abfragen für einige Typen von Eager Loading ausgeben. Die Ausgabe mehrerer Abfragen kann effizienter sein, als dies bei einigen Abfragen in EF6 der Fall war. Dort war nur eine einzelne Abfrage vorhanden. Eager Loading wird mit den `Include`- und `ThenInclude`-Methoden angegeben.

  ![Beispiel für Eager Loading](read-related-data/_static/eager-loading.png)
 
  Eager Loading sendet mehrere Abfragen, wenn eine Sammlungsnavigation enthalten ist:

  * Eine Abfrage für die Hauptabfrage 
  * Eine Abfrage für jeden „Sammlungsrand“ in der Ladestruktur.

* Beispiel für separate Abfragen mit `Load`: Die Daten können in separaten Abfragen abgerufen werden. EF Core korrigiert die Navigationseigenschaften. „Korrigieren“ bedeutet, dass EF Core die Navigationseigenschaften automatisch füllt. Separate Abfragen mit `Load` ähneln mehr dem expliziten Laden als dem Eager Loading.

  ![Beispiel für separate Abfragen](read-related-data/_static/separate-queries.png)

  Hinweis: EF Core korrigiert automatisch Navigationseigenschaften für alle anderen Entitäten, die zuvor in die Kontextinstanz geladen wurden. Auch wenn die Daten für eine Navigationseigenschaft *nicht* explizit eingeschlossen sind, kann die Eigenschaft immer noch aufgefüllt werden, wenn einige oder alle verwandten Entitäten zuvor geladen wurden.

* [Explizites Laden](/ef/core/querying/related-data#explicit-loading). Wenn die Entität zuerst gelesen wird, werden verwandte Daten nicht abgerufen. Es muss Code geschrieben werden, um die verwandten Daten bei Bedarf abzurufen. Explizites Laden mit separaten Abfragen führt zu mehreren Abfragen, die an die Datenbank gesendet werden. Mit explizitem Laden gibt der Code die zu ladenden Navigationseigenschaften an. Verwenden Sie für explizites Laden die `Load`-Methode. Zum Beispiel:

  ![Beispiel für explizites Laden](read-related-data/_static/explicit-loading.png)

* [Lazy Loading (verzögertes Laden)](/ef/core/querying/related-data#lazy-loading). [Lazy Loading wurde in Version 2.1 zu EF Core hinzugefügt](/ef/core/querying/related-data#lazy-loading). Wenn die Entität zuerst gelesen wird, werden verwandte Daten nicht abgerufen. Wenn zum ersten Mal auf eine Navigationseigenschaft zugegriffen wird, werden die für diese Navigationseigenschaft erforderlichen Daten automatisch abgerufen. Wenn zum ersten Mal auf eine Navigationseigenschaft zugegriffen wird, wird jedes Mal eine Abfrage an die Datenbank geschickt.

* Der `Select`-Operator lädt nur die erforderlichen verwandten Daten.

## <a name="create-a-course-page-that-displays-department-name"></a>Erstellen einer Kursseite, die den Abteilungsnamen anzeigt

Die Course-Entität enthält eine Navigationseigenschaft, welche die `Department`-Entität enthält. Die `Department`-Entität enthält die Abteilung, der der Kurs zugewiesen ist.

So zeigen Sie den Namen der zugewiesenen Abteilung in einer Kursliste an:

* Rufen Sie `Name`-Eigenschaft aus der `Department`-Entität ab.
* Die `Department`-Entität stammt aus der `Course.Department`-Navigationseigenschaft.

![Course.Department](read-related-data/_static/dep-crs.png)

<a name="scaffold"></a>

### <a name="scaffold-the-course-model"></a>Erstellen des Gerüsts für das Kursmodell

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio) 

Führen Sie die Schritte unter [Erstellen des Gerüsts für das Studentenmodell](xref:data/ef-rp/intro#scaffold-the-student-model) aus, und verwenden Sie `Course` für die Modellklasse.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

 Führen Sie den folgenden Befehl aus:

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages\Courses --referenceScriptLibraries
  ```

---

Der vorherige Befehl erstellt ein Gerüst für das `Course`-Modell. Öffnen Sie das Projekt in Visual Studio.

Öffnen Sie *Pages/Courses/Index.cshtml.cs*. Untersuchen Sie die `OnGetAsync`-Methode. Die Gerüstbauengine gibt Eager Loading für die `Department`-Navigationseigenschaft an. Die `Include`-Methode gibt Eager Loading an.

Führen Sie die Anwendung aus, und klicken Sie auf den Link **Kurse**. Die Abteilungsspalte zeigt die `DepartmentID` an, die nicht hilfreich ist.

Aktualisieren Sie die `OnGetAsync`-Methode mit folgendem Code:

[!code-csharp[](intro/samples/cu/Pages/Courses/Index.cshtml.cs?name=snippet_RevisedIndexMethod)]

Der vorangehende Code fügt `AsNoTracking` hinzu. `AsNoTracking` verbessert die Leistung, da die zurückgegebenen Entitäten nicht nachverfolgt werden. Die Entitäten werden nicht nachverfolgt, da sie nicht im aktuellen Kontext aktualisiert werden.

Aktualisieren Sie *Pages/Courses/Index.cshtml* mit dem folgenden hervorgehobenen Markup:

[!code-cshtml[](intro/samples/cu/Pages/Courses/Index.cshtml?highlight=4,7,15-17,34-36,44)]

Die folgenden Änderungen wurden am Codegerüst vorgenommen:

* Die Überschrift wurde von „Index“ in „Kurse“ geändert.
* Die Spalte **Anzahl** wurde hinzugefügt. Sie zeigt den `CourseID`-Eigenschaftswert an. Primärschlüssel werden nicht standardmäßig eingerüstet, da sie normalerweise ohne Bedeutung für Endbenutzer sind. Allerdings hat der Primärschlüssel in diesem Fall jedoch Bedeutung.
* Die Spalte **Abteilung** wurde geändert, sodass sie jetzt den Namen der Abteilung anzeigt. Der Code zeigt die `Name`-Eigenschaft der `Department`-Entität an, die in die `Department`-Navigationseigenschaft geladen wird:

  ```html
  @Html.DisplayFor(modelItem => item.Department.Name)
  ```

Führen Sie die Anwendung aus. Wählen Sie die Registerkarte **Kurse** aus, um die Liste mit den Abteilungsnamen anzuzeigen.

![Indexseite „Kurse“](read-related-data/_static/courses-index.png)

<a name="select"></a>

### <a name="loading-related-data-with-select"></a>Laden verwandter Daten mit „Select“

Die `OnGetAsync`-Methode lädt verwandte Daten mit der `Include`-Methode:

[!code-csharp[](intro/samples/cu/Pages/Courses/Index.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=4)]

Der `Select`-Operator lädt nur die erforderlichen verwandten Daten. Für die einzelnen Elemente wie `Department.Name` wird ein INNER JOIN von SQL verwendet. Für Sammlungen wird ein anderer Zugriff auf die Datenbank verwendet, aber Gleiches gilt für den `Include`-Operator für Sammlungen.

Der folgende Code lädt verwandte Daten mit der `Select`-Methode:

[!code-csharp[](intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=4)]

Die `CourseViewModel`:

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/CourseViewModel.cs?name=snippet)]

Ein vollständiges Beispiel finden Sie unter [IndexSelect.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml) und [IndexSelect.cshtml.cs](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs).

## <a name="create-an-instructors-page-that-shows-courses-and-enrollments"></a>Erstellen einer Dozentenseite, die Kurse und Registrierungen anzeigt

In diesem Abschnitt wird die Dozentenseite erstellt.

<a name="IP"></a>
![Indexseite „Dozenten“](read-related-data/_static/instructors-index.png)

Auf dieser Seite werden verwandte Daten auf folgende Weise gelesen und angezeigt:

* Die Liste der Dozenten zeigt verwandte Daten aus der `OfficeAssignment`-Entität (Office in der vorherigen Abbildung). Die `Instructor`- und `OfficeAssignment`-Entitäten stehen in einer 1:0..1-Beziehung zueinander. Eager Loading wird für die `OfficeAssignment`-Entitäten verwendet. Eager Loading ist in der Regel effizienter, wenn die verwandten Daten angezeigt werden müssen. In diesem Fall werden die Office-Zuweisungen für die Dozenten angezeigt.
* Wenn der Benutzer einen Dozenten auswählt (Harui in der vorherigen Abbildung), werden verwandte `Course`-Entitäten angezeigt. Die `Instructor`- und `Course`-Entitäten stehen in einer m:n-Beziehung zueinander. Für die `Course`-Entitäten und ihre verwandten `Department`-Entitäten wird das Eager Loading verwendet. In diesem Fall können separate Abfragen effizienter sein, da nur Kurse für den ausgewählten Dozenten benötigt werden. Dieses Beispiel zeigt, wie Eager Loading für Navigationseigenschaften in Entitäten in den Navigationseigenschaften verwendet wird.
* Wenn der Benutzer einen Kurs auswählt (Chemie in der vorherigen Abbildung), werden verwandte Daten aus der `Enrollments`-Entität angezeigt. In der vorherigen Abbildung sind der Name des Studenten und die Note angezeigt. Die `Course`- und `Enrollment`-Entitäten stehen in einer 1:n-Beziehung zueinander.

### <a name="create-a-view-model-for-the-instructor-index-view"></a>Erstellen eines Ansichtsmodells für die Indexansicht „Dozenten“

Die Dozentenseite zeigt Daten aus drei verschiedenen Tabellen. Es wird ein Ansichtsmodell erstellt, das die drei Entitäten enthält, die die drei Tabellen darstellen.

Erstellen Sie im Ordner *SchoolViewModels* die Datei *InstructorIndexData.cs* mit dem folgenden Code:

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/InstructorIndexData.cs)]

### <a name="scaffold-the-instructor-model"></a>Gerüstbau für das Dozentenmodell

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio) 

Führen Sie die Schritte unter [Erstellen des Gerüsts für das Studentenmodell](xref:data/ef-rp/intro#scaffold-the-student-model) aus, und verwenden Sie `Instructor` für die Modellklasse.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

 Führen Sie den folgenden Befehl aus:

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages\Instructors --referenceScriptLibraries
  ```

---

Der vorherige Befehl erstellt ein Gerüst für das `Instructor`-Modell. 
Führen Sie die Anwendung aus, und navigieren Sie zur Dozentenseite.

Ersetzen Sie *Pages/Instructors/Index.cshtml.cs* durch den folgenden Code:

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index1.cshtml.cs?name=snippet_all&highlight=2,18-99)]

Die `OnGetAsync`-Methode akzeptiert optional Routendaten für die ID des ausgewählten Dozenten.

Untersuchen Sie die Abfrage in der Datei *Pages/Instructors/Index.cshtml.cs*:

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index1.cshtml.cs?name=snippet_ThenInclude)]

Die Abfrage enthält zwei Dinge:

* `OfficeAssignment`: In der [Dozentenansicht](#IP) angezeigt.
* `CourseAssignments`: Welche Kurse gegeben werden.

### <a name="update-the-instructors-index-page"></a>Aktualisieren der Indexseite „Dozenten“

Aktualisieren Sie *Pages/Instructors/Index.cshtml* mit folgendem Markup:

[!code-cshtml[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=1-65&highlight=1,5,8,16-21,25-32,43-57)]

Das oben stehende Markup führt die folgenden Änderungen durch:

* Aktualisiert die `page`-Anweisung von `@page` auf `@page "{id:int?}"`. `"{id:int?}"` ist eine Routenvorlage. Die Routenvorlage ändert ganzzahlige Abfragezeichenfolgen in der URL in Routendaten. Klicken Sie beispielsweise auf den Link **Auswählen** für einen Dozenten, wenn nur die `@page`-Anweisung eine URL wie die folgende erzeugt:

  `http://localhost:1234/Instructors?id=2`

  Wenn die Seitenanweisung `@page "{id:int?}"` ist, sieht die vorherige URL wie folgt aus:

  `http://localhost:1234/Instructors/2`

* Der Seitentitel lautet **Dozenten**.
* Es wurde eine **Office**-Spalte hinzugefügt, die `item.OfficeAssignment.Location` nur anzeigt, wenn `item.OfficeAssignment` nicht gleich 0 (null) ist. Da dies eine 1:0..1-Beziehung ist, gibt es möglicherweise keine verwandte OfficeAssignment-Entität.

  ```html
  @if (item.OfficeAssignment != null)
  {
      @item.OfficeAssignment.Location
  }
  ```

* Es wurde eine **Kurse**-Spalte hinzugefügt, die die Kurse eines jeden Dozenten anzeigt. Weitere Informationen zu dieser Razor-Syntax finden Sie unter [Expliziter Zeilenübergang](xref:mvc/views/razor#explicit-line-transition).

* Es wurde Code hinzugefügt, der `class="success"` dynamisch zum `tr`-Element des ausgewählten Dozenten hinzufügt. Hiermit wird eine Hintergrundfarbe mit einer Bootstrapklasse für die ausgewählte Zeile hinzugefügt.

  ```html
  string selectedRow = "";
  if (item.CourseID == Model.CourseID)
  {
      selectedRow = "success";
  }
  <tr class="@selectedRow">
  ```

* Es wurde ein neuer Link mit der Bezeichnung **Auswählen** hinzugefügt. Dieser Link sendet die ID des ausgewählten Dozenten an die `Index`-Methode und legt die Hintergrundfarbe fest.

  ```html
  <a asp-action="Index" asp-route-id="@item.ID">Select</a> |
  ```

Führen Sie die Anwendung aus. Klicken Sie auf die Registerkarte **Dozenten**. Die Seite zeigt den `Location` (Office) aus der verwandten `OfficeAssignment`-Entität an. Wenn OfficeAssignment gleich ist 0 (null), wird eine leere Tabellenzelle angezeigt.

Klicken Sie auf den Link **Auswählen**. Der Zeilenstil verändert sich.

### <a name="add-courses-taught-by-selected-instructor"></a>Hinzufügen von Kursen eines ausgewählten Dozenten

Aktualisieren Sie die `OnGetAsync`-Methode in *Pages/Instructors/Index.cshtml.cs* mit dem folgenden Code:

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_OnGetAsync&highlight=1,8,16-999)]

Fügen Sie `public int CourseID { get; set; }` hinzu.

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_1&highlight=12)]

Überprüfen Sie die aktualisierte Abfrage:

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_ThenInclude)]

Die vorhergehende Abfrage fügt die `Department`-Entitäten hinzu.

Der folgende Code wird ausgeführt, wenn ein Dozent ausgewählt wird (`id != null`). Der ausgewählte Dozent wird aus der Liste der Dozenten im Ansichtsmodell abgerufen. Die `Courses`-Eigenschaft des Ansichtsmodells wird mit den `Course`-Entitäten aus der `CourseAssignments`-Navigationseigenschaft dieses Dozenten geladen.

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_ID)]

Die `Where`-Methode gibt eine Sammlung zurück. In der vorherigen `Where`-Methode wird nur eine einzige `Instructor`-Entität zurückgegeben. Die `Single`-Methode konvertiert die Sammlung in eine einzelne `Instructor`-Entität. Die `Instructor`-Entität ermöglicht Zugriff auf die `CourseAssignments`-Eigenschaft. `CourseAssignments` ermöglicht Zugriff auf die verwandten `Course`-Entitäten.

![Dozent:Kurse m:N](complex-data-model/_static/courseassignment.png)

Die `Single`-Methode wird für eine Sammlung verwendet, wenn die Sammlung nur ein Element aufweist. Die `Single`-Methode löst eine Ausnahme aus, wenn die Sammlung leer ist oder mehr als ein Element vorhanden ist. Eine Alternative ist `SingleOrDefault`, womit ein Standardwert (in diesem Fall 0 (null)) zurückgegeben wird, wenn die Sammlung leer ist. Für eine leere Sammlung wird `SingleOrDefault` verwendet:

* Löst eine Ausnahme aus (auf der Suche nach einer `Courses`-Eigenschaft eines Nullverweises).
* Die Ausnahmemeldung würde die Ursache des Problems weniger deutlich angeben.

Wenn ein Kurs ausgewählt ist, füllt der folgende Code die `Enrollments`-Eigenschaft des Ansichtsmodells:

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_courseID)]

Fügen Sie das folgende Markup zum Ende der Razor-Seite *Pages/Instructors/Index.cshtml* hinzu:

[!code-cshtml[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=60-102&highlight=7-999)]

Das vorhergehende Markup zeigt eine Liste der Kurse eines bestimmten Dozenten an, wenn einer ausgewählt wird.

Testen Sie die App. Klicken Sie auf den Link **Auswählen** auf der Dozentenseite.

### <a name="show-student-data"></a>Anzeigen der Studentendaten

In diesem Abschnitt wird die Anwendung aktualisiert, um die Studentendaten für einen ausgewählten Kurs anzuzeigen.

Aktualisieren Sie die Abfrage in der `OnGetAsync`-Methode in *Pages/Instructors/Index.cshtml.cs* mit dem folgenden Code:

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index.cshtml.cs?name=snippet_ThenInclude&highlight=6-9)]

Aktualisieren Sie *Pages/Instructors/Index.cshtml*. Fügen Sie dem Dateiende das folgende Markup hinzu:

[!code-cshtml[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=103-)]

Das vorhergehende Markup zeigt eine Liste der Studenten, die im ausgewählten Kurs registriert sind.

Aktualisieren Sie die Seite. Wählen Sie einen Dozenten aus. Wählen Sie einen Kurs aus, um die Liste der registrierten Studenten und deren Noten einzusehen.

![Indexseite „Dozenten“, Dozent und Kurs ausgewählt](read-related-data/_static/instructors-index.png)

## <a name="using-single"></a>Verwenden von „Single“

Die `Single`-Methode kann die `Where`-Bedingung übergehen, anstatt die `Where`-Methode separat aufzurufen:

[!code-csharp[](intro/samples/cu/Pages/Instructors/IndexSingle.cshtml.cs?name=snippet_single&highlight=21-22,30-31)]

Der vorangehende `Single`-Ansatz bietet keine Vorteile gegenüber `Where`. Einige Entwickler bevorzugen einfach den `Single`-Ansatz.

## <a name="explicit-loading"></a>Explizites Laden

Der aktuelle Code gibt Eager Loading für `Enrollments` und `Students` an:

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index.cshtml.cs?name=snippet_ThenInclude&highlight=6-9)]

Angenommen, Benutzer möchten Registrierungen für einen Kurs nur selten anzeigen lassen. In diesem Fall wäre es eine Optimierung, die Registrierungsdaten nur dann zu laden, wenn diese angefordert werden. In diesem Abschnitt wird die `OnGetAsync` aktualisiert, um das explizite Laden von `Enrollments` und `Students` zu verwenden.

Aktualisieren Sie `OnGetAsync` mit dem folgenden Code:

[!code-csharp[](intro/samples/cu/Pages/Instructors/IndexXp.cshtml.cs?name=snippet_OnGetAsync&highlight=9-13,29-35)]

Der vorangehende Code löscht die *ThenInclude*-Methodenaufrufe für Registrierung und Studentendaten. Wenn ein Kurs ausgewählt ist, ruft der hervorgehobene Code Folgendes ab:

* Die `Enrollment`-Entitäten für den ausgewählten Kurs.
* Die `Student`-Entitäten für jede `Enrollment`.

Beachten Sie, dass der vorherige Code `.AsNoTracking()` auskommentiert. Navigationseigenschaften können nur für nachverfolgte Entitäten explizit geladen werden.

Testen Sie die App. Aus Benutzersicht verhält sich die Anwendung identisch mit der vorherigen Version.

Das nächste Tutorial zeigt, wie verwandte Daten aktualisiert werden.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Dieses Tutorial auf YouTube (Teil 1)](https://www.youtube.com/watch?v=PzKimUDmrvE)
* [Dieses Tutorial auf YouTube (Teil 2)](https://www.youtube.com/watch?v=xvDDrIHv5ko)

>[!div class="step-by-step"]
>[Zurück](xref:data/ef-rp/complex-data-model)
>[Weiter](xref:data/ef-rp/update-related-data)

::: moniker-end
