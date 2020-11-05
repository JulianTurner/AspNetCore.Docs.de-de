---
title: 'Tutorial: Aktualisieren verwandter Daten: ASP.NET Core MVC mit EF Core'
description: Mithilfe dieses Tutorials führen Sie Updates für verwandte Daten aus, indem Sie Felder mit Fremdschlüsseln sowie Navigationseigenschaften aktualisieren.
author: rick-anderson
ms.author: riande
ms.custom: mvc
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
uid: data/ef-mvc/update-related-data
ms.openlocfilehash: f41d0b1d7ba46ef4867e0d160dc61f21042ce403
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053942"
---
# <a name="tutorial-update-related-data---aspnet-mvc-with-ef-core"></a>Tutorial: Aktualisieren verwandter Daten: ASP.NET Core MVC mit EF Core

Mithilfe des letzten Tutorials haben Sie zugehörige Daten abgerufen. Mithilfe dieses Tutorials führen Sie hingegen Updates für verwandte Daten aus, indem Sie Felder mit Fremdschlüsseln sowie Navigationseigenschaften aktualisieren.

In den folgenden Abbildungen werden die Seiten dargestellt, mit denen Sie arbeiten werden.

![Bearbeitungsseite „Kurs“](update-related-data/_static/course-edit.png)

![Bearbeitungsseite „Dozent“](update-related-data/_static/instructor-edit-courses.png)

In diesem Tutorial:

> [!div class="checklist"]
> * Passen Sie die Seite „Kurse“ an
> * Fügen Sie die Seite „Bearbeiten“ für Dozenten hinzu
> * Fügen Sie der Seite „Bearbeiten“ Kurse hinzu
> * Aktualisieren Sie die Seite „Löschen“
> * Fügen Sie Bürostandort und Kurse der Seite „Erstellen“ hinzu

## <a name="prerequisites"></a>Voraussetzungen

* [Lesen relevanter Daten](read-related-data.md)

## <a name="customize-courses-pages"></a>Passen Sie die Seite „Kurse“ an

Wenn eine neue Kursentität erstellt wird, muss diese in Beziehung zu einer vorhandenen Abteilung stehen. Um dies zu vereinfachen, enthält der Gerüstcode Controllermethoden und Ansichten zum „Erstellen“ und „Bearbeiten“, die eine Dropdownliste enthalten, aus denen der Fachbereich ausgewählt werden kann. Die Dropdownliste legt die `Course.DepartmentID`-Fremdschlüsseleigenschaft fest. Mehr benötigt Entity Framework nicht, um die `Department`-Navigationseigenschaft mit der passenden Department-Entität zu laden. Verwenden Sie den Gerüstcode, aber nehmen Sie kleine Änderungen vor, um die Fehlerbehandlung hinzuzufügen und die Dropdownliste zu sortieren.

Löschen Sie aus der *CoursesController.cs* -Datei die vier Methoden zum „Erstellen“ und „Bearbeiten“, und ersetzen Sie diese durch den folgenden Code:

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_CreateGet)]

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_CreatePost)]

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_EditGet)]

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_EditPost)]

Erstellen Sie nach der HttpPost-Methode `Edit` eine neue Methode, die für die Dropdownliste Informationen zum Fachbereich lädt.

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_Departments)]

Die `PopulateDepartmentsDropDownList`-Methode ruft eine nach Namen sortierte Liste aller Abteilungen ab, erstellt eine `SelectList`-Auflistung für die Dropdownliste und übergibt diese Auflistung an die Ansicht in `ViewBag`. Die Methode akzeptiert den optionalen `selectedDepartment`-Parameter, über den der Code das Element angeben kann, das ausgewählt wird, wenn die Dropdownliste gerendert wird. Die Ansicht übergibt den Namen „DepartmentID“ an das Taghilfsprogramm `<select>`. Dann weiß das Hilfsprogramm, dass es nach einem `ViewBag`-Objekt für eine `SelectList` mit dem Namen „DepartmentID“ suchen soll.

Die HttpGet-Methode `Create` ruft die `PopulateDepartmentsDropDownList`-Methode auf, ohne das ausgewählte Element festzulegen, da der Fachbereich für einen neuen Code noch nicht festgelegt wurde:

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?highlight=3&name=snippet_CreateGet)]

Die HttpGet-Methode `Edit` legt anhand der ID des Fachbereichs, die bereits dem Kurs zugewiesen wurde, der bearbeitet wird, das ausgewählte Element fest:

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?highlight=15&name=snippet_EditGet)]

Die HttpPost-Methoden für `Create` und `Edit` enthalten außerdem Code, der das ausgewählte Element festlegt, wenn die Seite nach einem Fehler erneut angezeigt wird. Dadurch wird gewährleistet, dass der Fachbereich nicht geändert wird, wenn eine Seite erneut angezeigt wird, um die Fehlermeldung anzuzeigen.

### <a name="add-asnotracking-to-details-and-delete-methods"></a>Hinzufügen von „.AsNoTracking“ zu den Methoden „Details“ und „Delete“

Fügen Sie `AsNoTracking`-Aufrufe in die `Details` und zu den HttpGet-Methoden `Delete` hinzu, um die Leistung der Kursdetails und Seiten zum „Löschen“ zu optimieren.

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?highlight=10&name=snippet_Details)]

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?highlight=10&name=snippet_DeleteGet)]

### <a name="modify-the-course-views"></a>Ändern der Kursansichten

Fügen Sie in der *Views/Courses/Create.cshtml* -Datei die Option „Select Department“ (Fachbereich auswählen) zu der Dropdownliste **Department** (Fachbereich) hinzu, ändern Sie den Titel von **DepartmentID** in **Department** , und fügen Sie eine Validierungsmeldung hinzu.

[!code-cshtml[](intro/samples/cu/Views/Courses/Create.cshtml?highlight=2-6&range=29-34)]

Nehmen Sie in der Datei *Views/Courses/Edit.cshtml* dieselben Änderungen für das Feld „Department“ (Fachbereich) vor wie in der Datei *Create.cshtml*.

Fügen Sie in der *Views/Courses/Edit.cshtml* -Datei außerdem vor dem Feld **Titel** ein Feld für die Kursnummer hinzu. Da es sich bei der Kursnummer um einen Primärschlüssel handelt, wird dieser zwar angezeigt, kann aber nicht geändert werden.

[!code-cshtml[](intro/samples/cu/Views/Courses/Edit.cshtml?range=15-18)]

In der Ansicht „Bearbeiten“ ist bereits eine ausgeblendetes Feld (`<input type="hidden">`) für die Kursnummer vorhanden. Wenn Sie ein `<label>`-Taghilfsprogramm hinzufügen, wird trotzdem ein ausgeblendetes Feld benötigt, da dieses nicht dazu beiträgt, dass die Kursnummer in den bereitgestellten Daten vorhanden ist, wenn der Benutzer auf der Seite **Bearbeiten** auf **Speichern** klickt.

Fügen Sie oben in der *Views/Courses/Delete.cshtml* -Datei ein Feld für die Kursnummer hinzu, und ändern Sie „Department ID“ (Fachbereichs-ID) in „Department Name“ (Fachbereichsname).

[!code-cshtml[](intro/samples/cu/Views/Courses/Delete.cshtml?highlight=14-19,36)]

Nehmen Sie in der *Views/Courses/Details.cshtml* -Datei dieselben Änderungen wie in der *Delete.cshtml* -Datei vor.

### <a name="test-the-course-pages"></a>Testen der Kursseiten

Führen Sie die App aus, wählen Sie die Registerkarte **Courses** (Kurse) aus, klicken Sie auf **Neu erstellen** , und geben Sie die Daten für einen neuen Kurs ein:

![Seite „Kurs erstellen“](update-related-data/_static/course-create.png)

Klicken Sie auf **Erstellen**. Die Indexseite des Kurses wird mit dem Kurs angezeigt, der neu zu der Liste hinzugefügt wurde. Der Fachbereichsname in der Indexseitenliste wurde der Navigationseigenschaft entnommen und deutet darauf hin, dass die Beziehung ordnungsgemäß festgelegt wurde.

Klicken Sie auf der Indexseite eines Kurses auf **Bearbeiten**.

![Bearbeitungsseite „Kurs“](update-related-data/_static/course-edit.png)

Ändern Sie die Daten auf der Seite, und klicken Sie auf **Speichern**. Die Indexseite des Kurses wird mit den aktualisierten Kursdaten angezeigt.

## <a name="add-instructors-edit-page"></a>Fügen Sie die Seite „Bearbeiten“ für Dozenten hinzu

Bei der Bearbeitung eines Dozentendatensatzes sollten Sie auch die Bürozuweisung des Dozenten aktualisieren. Die Entität „Instructor“ verfügt über eine 1:0..1-Beziehung mit der Entität „OfficeAssignment“. Das bedeutet, dass Ihr Code den folgenden Situationen standhalten muss:

* Wenn der Benutzer die Bürozuweisung löscht, es für diese aber zuvor einen Wert gab, löschen Sie die Entität „OfficeAssignment“.

* Wenn der Benutzer eine Bürozuweisung eingibt, diese aber zuvor leer war, erstellen Sie eine neue OfficeAssignment-Entität.

* Wenn der Benutzer den Wert einer Bürozuweisung verändert, ändern Sie den Wert in eine bereits vorhandene OfficeAssignment-Entität.

### <a name="update-the-instructors-controller"></a>Aktualisieren des Controllers des Dozenten

Ändern Sie in der *InstructorsController.cs* -Datei den Code in der HttpGet-Methode `Edit`, sodass diese die `OfficeAssignment`-Eigenschaft der Instructor-Entität lädt und `AsNoTracking` aufruft:

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?highlight=8-11&name=snippet_EditGetOA)]

Ersetzen Sie die HttpPost-Methode `Edit` durch den folgenden Code, damit diese Updates für die Bürozuweisungen verarbeiten kann:

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_EditPostOA)]

Der Code führt Folgendes aus:

* Er ändert den Methodennamen auf `EditPost`, da die Signatur jetzt der Signatur der HttpGet-Methode `Edit` entspricht. Das `ActionName`-Attribut gibt an, dass die `/Edit/`-URL immer noch verwendet wird.

* Ruft für die Navigationseigenschaft `OfficeAssignment` die aktuelle Dozentenentität von der Datenbank über Eager Loading ab. Dies entspricht dem Vorgang in der HttpGet-Methode `Edit`.

* Führt ein Update für die abgerufene Dozentenentität mit Werten aus der Modellbindung aus. Mithilfe der `TryUpdateModel`-Überladung können Sie die Eigenschaften deklarieren, die Sie hinzufügen möchten. Dadurch wird, wie im [zweiten Tutorial](crud.md) beschrieben, vermieden, dass zu viele Angaben gemacht werden.

    <!-- Snippets don't play well with <ul> [!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?range=241-244)] -->

    ```csharp
    if (await TryUpdateModelAsync<Instructor>(
        instructorToUpdate,
        "",
        i => i.FirstMidName, i => i.LastName, i => i.HireDate, i => i.OfficeAssignment))
    ```

* Wenn kein Standort für das Büro angegeben wird, wird die Instructor.OfficeAssignment-Eigenschaft auf NULL festgelegt, damit die zugehörige Zeile aus der OfficeAssignment-Tabelle gelöscht wird.

    <!-- Snippets don't play well with <ul>  "intro/samples/cu/Controllers/InstructorsController.cs"} -->

    ```csharp
    if (String.IsNullOrWhiteSpace(instructorToUpdate.OfficeAssignment?.Location))
    {
        instructorToUpdate.OfficeAssignment = null;
    }
    ```

* Speichert die Änderungen in der Datenbank.

### <a name="update-the-instructor-edit-view"></a>Aktualisieren der Dozentenansicht „Bearbeiten“

Fügen Sie am Ende vor der Schaltfläche **Speichern** in der *Views/Instructors/Edit.cshtml* -Datei ein neues Feld hinzu, um den Standort des Büros zu bearbeiten:

[!code-cshtml[](intro/samples/cu/Views/Instructors/Edit.cshtml?range=30-34)]

Führen Sie die App aus, klicken Sie erst auf die Registerkarte **Dozenten** und dann für einen Dozenten auf **Bearbeiten**. Ändern Sie den **Standort des Büros** , und klicken Sie auf **Speichern**.

![Bearbeitungsseite „Dozent“](update-related-data/_static/instructor-edit-office.png)

## <a name="add-courses-to-edit-page"></a>Fügen Sie der Seite „Bearbeiten“ Kurse hinzu

Dozenten können eine beliebige Anzahl von Kursen unterrichten. Jetzt soll die Dozentenseite „Bearbeiten“ verbessert werden, indem es ermöglicht wird, Kurszuweisungen über eine Reihe von Kontrollkästchen zu verändern. Dies wird im folgenden Screenshot dargestellt:

![Dozentenseite „Bearbeiten“ mit Kursen](update-related-data/_static/instructor-edit-courses.png)

Zwischen den Entitäten „Course“ und „Instructor“ besteht eine m:n-Beziehung. Wenn Sie Beziehungen hinzufügen und entfernen möchten, können Sie Entitäten aus der verknüpften CourseAssignments-Entitätenmenge hinzufügen und entfernen.

Die Benutzeroberfläche, über die Sie ändern können, welchen Kursen ein Dozent zugewiesen ist, besteht aus einer Reihe von Kontrollkästchen. Für jeden Kurs in der Datenbank wird ein Kontrollkästchen angezeigt. Die Kontrollkästchen, denen der Dozent zu diesem Zeitpunkt zugewiesen ist, sind aktiviert. Der Benutzer kann Kontrollkästchen aktivieren oder deaktivieren, um Kurszuweisungen zu ändern. Bei einer deutlich höheren Anzahl von Kursen sollten Sie besser eine andere Methode verwenden, um Daten in der Ansicht darzustellen. Sie sollten aber auch in diesem Fall dieselbe Bearbeitungsmethode verwenden, um eine verknüpfte Entität zum Erstellen und Löschen von Beziehungen zu verwenden.

### <a name="update-the-instructors-controller"></a>Aktualisieren des Controllers des Dozenten

Verwenden Sie eine Ansichtsmodellklasse, um Daten für die Ansicht bereitzustellen, um eine Liste mit Kontrollkästchen zu erstellen.

Erstellen Sie im Ordner *SchoolViewModels* die Datei *AssignedCourseData.cs* , und ersetzen Sie den vorhandenen Code durch den folgenden Code:

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/AssignedCourseData.cs)]

Ersetzen Sie in der Datei *InstructorsController.cs* die HttpGet-Methode `Edit` durch den folgenden Code. Die Änderungen werden hervorgehoben.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?highlight=10,17,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36&name=snippet_EditGetCourses)]

Über den Code wird für die `Courses`-Navigationseigenschaft Eager Loading hinzugefügt und die neue `PopulateAssignedCourseData`-Methode aufgerufen, um über die Ansichtsmodellklasse `AssignedCourseData` Informationen für das Kontrollkästchenarray zur Verfügung zu stellen.

Der Code in der `PopulateAssignedCourseData`-Methode liest alle Course-Entitäten, um mithilfe der Ansichtsmodellklasse eine Liste von Kursen zu laden. Für jeden Kurs überprüft der Code, ob dieser in der `Courses`-Navigationseigenschaft des Dozenten vorhanden ist. Die Kurse, die dem Dozenten zugewiesen werden, werden in einer `HashSet`-Auflistung dargestellt, um die Suche effizienter zu machen, wenn Sie überprüfen, ob ein Kurs dem Dozenten zugewiesen ist. Die `Assigned`-Eigenschaft ist für Kurse, denen der Dozent zugewiesen ist, auf TRUE festgelegt. Die Ansicht verwendet diese Eigenschaft, um zu bestimmen, welche Kontrollkästchen als aktiviert angezeigt werden sollen. Zuletzt wird die Liste in `ViewData` an die Ansicht übergeben.

Fügen Sie als nächstes den Code hinzu, der ausgeführt wird, wenn der Benutzer auf **Speichern** klickt. Ersetzen Sie die `EditPost`-Methode durch den folgenden Code, und fügen Sie eine neue Methode hinzu, die ein Update für `Courses`-Navigationseigenschaft der Instructor-Entität ausführt.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?highlight=1,3,12,13,25,39-40&name=snippet_EditPostCourses)]

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_UpdateCourses&highlight=1-31)]

Die Methodensignatur unterscheidet sich jetzt von der HttpGet-Methode `Edit`, wodurch der Methodenname von `EditPost` auf `Edit` geändert wird.

Da es in der Ansicht keine Auflistung der Course-Entitäten gibt, kann durch die Modellbindung kein automatisches Update für die `CourseAssignments`-Navigationseigenschaft ausgeführt werden. Verwenden Sie dafür die neue `CourseAssignments`-Methode anstatt die Modellbindung zu verwenden, um ein Update für die `UpdateInstructorCourses`-Navigationseigenschaft auszuführen. Aus diesem Grund müssen Sie die `CourseAssignments`-Eigenschaft von der Modellbindung ausschließen. Dafür muss der Code, der `TryUpdateModel` aufruft, nicht verändert werden, weil Sie die Überladung verwenden, die eine explizite Genehmigung erfordert, und `CourseAssignments` nicht in der Liste der einzuschließenden Elemente enthalten ist.

Wenn keins der Kontrollkästchen aktiviert ist, initialisiert der Code in `UpdateInstructorCourses` die `CourseAssignments`-Navigationseigenschaft mit einer leeren Auflistung und gibt Folgendes zurück:

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_UpdateCourses&highlight=3-7)]

Der Code führt dann eine Schleife durch alle Kurse in der Datenbank aus und überprüft jeden Kurs im Hinblick auf die Kurse, die zu diesem Zeitpunkt dem Dozenten zugewiesen sind, und denen, die in der Ansicht aktiviert wurden. Die beiden letzten Auflistungen werden in `HashSet`-Objekten gespeichert, um Suchvorgänge effizienter zu gestalten.

Wenn das Kontrollkästchen für einen Kurs aktiviert ist, dieser Kurs jedoch nicht in der `Instructor.CourseAssignments`-Navigationseigenschaft vorhanden ist, wird dieser der Auflistung in der Navigationseigenschaft hinzugefügt.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?highlight=14-20&name=snippet_UpdateCourses)]

Wenn das Kontrollkästchen für einen Kurs aktiviert ist, dieser Kurs jedoch nicht in der `Instructor.CourseAssignments`-Navigationseigenschaft vorhanden ist, wird dieser aus der Auflistung in der Navigationseigenschaft gelöscht.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?highlight=21-29&name=snippet_UpdateCourses)]

### <a name="update-the-instructor-views"></a>Ausführen eines Updates für die Dozentenseiten

Fügen Sie in der *Views/Instructors/Edit.cshtml* -Datei ein **Kurse** -Feld mit einem Array mit Kontrollkästchen hinzu, indem Sie den folgenden Code direkt nach den `div`-Elementen für das **Büro** -Feld und vor dem `div`-Element für die Schaltfläche **Speichern** einfügen.

<a id="notepad"></a>
> [!NOTE]
> Wenn Sie den Code in Visual Studio einfügen, werden Zeilenumbrüche möglicherweise so geändert, dass der Code unterbrochen wird. Wenn der Code nach dem Einfügen anders aussieht, drücken Sie einmal STRG+Z, um die automatische Formatierung rückgängig zu machen. Damit werden die Zeilenumbrüche korrigiert, damit sie dem entsprechen, was Sie hier sehen. Der Einzug muss nicht perfekt sein, die Zeilen `@:</tr><tr>`, `@:<td>`, `@:</td>` und `@:</tr>` müssen jedoch, wie dargestellt, jeweils in einer einzelnen Zeile stehen. Ansonsten wird ein Laufzeitfehler ausgelöst. Drücken Sie, nachdem Sie den Block mit dem neuen Code ausgewählt haben, dreimal auf die TAB-Taste, um den neuen Code am vorhandenen Code auszurichten. Dieses Problem wurde in Visual Studio 2019 behoben.

[!code-cshtml[](intro/samples/cu/Views/Instructors/Edit.cshtml?range=35-61)]

Dieser Code erstellt eine HTML-Tabelle mit drei Spalten. Jede Spalte enthält ein Kontrollkästchen gefolgt von einem Titel, der aus der Kursnummer und dem Kurstitel besteht. Die Kontrollkästchen haben denselben Namen („selectedCourses“), was bei der Modellbindung deutlich macht, dass diese als Gruppe behandelt werden sollen. Das Wertattribut der einzelnen Kontrollkästchen ist auf den Wert von `CourseID` festgelegt. Wenn die Seite zurückgesendet wird, übergibt die Modellbindung ein Array an den Controller, das lediglich die `CourseID`-Werte der aktivierten Kontrollkästchen enthält.

Wenn die Kontrollkästchen zu Beginn gerendert werden, verfügen die Kästchen, die für Kurse stehen, die dem Dozenten zugewiesen sind, über aktivierte Attribute, die diese aktivieren (also als aktiviert anzeigen).

Führen Sie die App aus, klicken Sie erst auf die Registerkarte **Dozenten** und dann für einen Dozenten auf **Bearbeiten** , um die Seite **Bearbeiten** anzuzeigen.

![Dozentenseite „Edit“ (Bearbeiten) mit Kursen](update-related-data/_static/instructor-edit-courses.png)

Ändern Sie einige Kurszuweisungen, und klicken Sie auf „Speichern“. Die Änderungen werden auf der Indexseite angezeigt.

> [!NOTE]
> Der hier gewählte Ansatz für die Bearbeitung der Kursdaten von Dozenten wird empfohlen, wenn eine begrenzte Anzahl von Kursen verwendet wird. Bei umfangreicheren Auflistungen wären eine andere Benutzeroberfläche und eine andere Aktualisierungsmethode erforderlich.

## <a name="update-delete-page"></a>Aktualisieren Sie die Seite „Löschen“

Löschen Sie aus der Datei *InstructorsController.cs* die `DeleteConfirmed`-Methode, und fügen Sie stattdessen den folgenden Code ein.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?highlight=5-7,9-12&name=snippet_DeleteConfirmed)]

Durch diesen Code werden folgende Änderungen vorgenommen:

* Er verwendet Eager Loading für die Navigationseigenschaft `CourseAssignments`. Sie müssen diese Eigenschaft hinzufügen. Ansonsten weiß EF nicht, dass es zugehörige `CourseAssignment`-Entitäten gibt und löscht diese nicht. Wenn Sie vermeiden möchten, diese lesen zu müssen, können Sie in der Datenbank eine Löschweitergabe konfigurieren.

* Wenn der zu löschende Dozent als Administrator einer beliebigen Abteilung zugewiesen ist, wird die Dozentenzuweisung aus diesen Abteilungen entfernt.

## <a name="add-office-location-and-courses-to-create-page"></a>Fügen Sie Bürostandort und Kurse der Seite „Erstellen“ hinzu

Löschen Sie in der *InstructorsController.cs* -Datei die Methoden HttpGet und HttpPost-`Create`, und fügen Sie anschließend stattdessen den folgenden Code ein:

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_Create&highlight=3-5,12,14-22,29)]

Dieser Code ähnelt dem Code, der für die `Edit`-Methode verwendet wurde. Allerdings sind nicht von Beginn an Kurse ausgewählt. Die HttpGet-Methode `Create` ruft die `PopulateAssignedCourseData`-Methode nicht auf, weil möglicherweise Kurse ausgewählt sind, sondern um eine leere Auflistung für die `foreach`-Schleife in der Ansicht bereitzustellen. Andernfalls gibt der Ansichtscode eine NULL-Verweisausnahme zurück.

Die HttpPost-Methode `Create` fügt jeden der ausgewählten Kurse zu der `CourseAssignments`-Navigationseigenschaft hinzu, bevor sie nach Validierungsfehlern sucht und den neuen Dozenten zu der Datenbank hinzufügt. Kurse werden auch hinzugefügt, wenn es Modellfehler gibt, sodass alle zuvor ausgewählten Kurse automatisch wieder ausgewählt werden, wenn es zu Modellfehlern kommt (weil z.B. der Benutzer ein ungültiges Datum verschlüsselt hat) und die Seite mit Fehlermeldungen erneut dargestellt wird.

Beachten Sie, dass Sie die `CourseAssignments`-Navigationseigenschaft als leere Auflistung initialisieren müssen, wenn Sie dieser Kurse hinzufügen möchten:

```csharp
instructor.CourseAssignments = new List<CourseAssignment>();
```

Wenn Sie dies nicht im Controllercode durchführen möchten, können Sie dies auch im Dozentenmodell tun, indem Sie den Eigenschaftengetter ändern, um falls nötig automatisch die Auflistung zu erstellen. Dies wird im folgenden Code dargestellt:

```csharp
private ICollection<CourseAssignment> _courseAssignments;
public ICollection<CourseAssignment> CourseAssignments
{
    get
    {
        return _courseAssignments ?? (_courseAssignments = new List<CourseAssignment>());
    }
    set
    {
        _courseAssignments = value;
    }
}
```

Wenn Sie die `CourseAssignments`-Eigenschaft auf diese Weise ändern, können Sie den expliziten Code zum Initialisieren der Eigenschaft aus dem Controller entfernen.

Fügen die in der *Views/Instructor/Create.cshtml* -Datei ein Textfeld für den Bürostandort hinzu, und aktivieren Sie Felder für Kurse, bevor Sie auf die Schaltfläche „Übermitteln“ klicken. [Wenn Visual Studio den Code neu formatiert, wenn Sie diesen einfügen, beheben Sie die Formatierung](#notepad) auf dieselbe Weise wie auf der Seite „Bearbeiten“.

[!code-cshtml[](intro/samples/cu/Views/Instructors/Create.cshtml?range=29-61)]

Führen Sie einen Test durch, indem Sie die App ausführen und einen Dozenten erstellen.

## <a name="handling-transactions"></a>Verarbeiten von Transaktionen

Wie bereits im [CRUD-Tutorial](crud.md) erläutert, implementiert Entity Framework implizit Transaktionen. Informationen zu Szenarios, die Sie genauer kontrollieren müssen (z.B. wenn Sie Vorgänge einfügen möchten, die außerhalb von Entity Framework in einer Transaktion ausgeführt werden), finden Sie unter [Transaktionen](/ef/core/saving/transactions).

## <a name="get-the-code"></a>Abrufen des Codes

[Download or view the completed app (Herunterladen oder anzeigen der vollständigen App).](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial:

> [!div class="checklist"]
> * Haben Sie die Seite „Kurse“ angepasst
> * Haben Sie die Seite „Bearbeiten“ für Dozenten hinzugefügt
> * Haben Sie der Seite „Bearbeiten“ Kurse hinzugefügt
> * Haben Sie die Seite „Löschen“ aktualisiert
> * Haben Sie Bürostandort und Kurse der Seite „Erstellen“ hinzugefügt

Fahren Sie mit dem nächsten Tutorial fort, um zu erfahren, wie Sie Nebenläufigkeitskonflikte behandeln können.

> [!div class="nextstepaction"]
> [Verarbeiten von Nebenläufigkeitskonflikten](concurrency.md)
